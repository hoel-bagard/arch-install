# Archlinux IPA

## Server installation

On arch: set the `systemd.unified_cgroup_hierarchy=false` kernel parameter.

Cleanup just in case (if needed, might want to be careful if there is a pre-existing installation).

```
sudo rm -r /var/lib/ipa-data
docker stop ipa-container
docker rm ipa-container
sudo mkdir /var/lib/ipa-data
```

Create the server docker (change at least the password, and maybe the ips / addresses)

```
docker run --name ipa-container -it \
    --sysctl net.ipv6.conf.all.disable_ipv6=0 \
    -p 80:80 -p 135:135 -p 138:138 -p 139:139 -p 443:443 -p 445:445 -p 389:389 -p 636:636 -p 88:88 -p 464:464 -p 3268:3268 \
    -p 88:88/udp -p 138:138/udp -p 139:139/udp -p 389:389/udp -p 445:445/udp -p 464:464/udp -p 123:123/udp \
    --dns 192.168.2.59 \
    -e IPA_SERVER_IP=192.168.100.225 \
    --ip6 false -h censei.ipa.xc \
    -v /sys/fs/cgroup:/sys/fs/cgroup:ro \
    -v /var/lib/ipa-data:/data:Z \
    --tmpfs /run --tmpfs /tmp \
    -v /mnt:/mnt \
    freeipa/freeipa-server:fedora-34 ipa-server-install \
    -U \
    --realm=IPA.XC \
    --domain=ipa.xc \
    --ds-password=<password> \
    --admin-password=<password>
```

Ctrl+p+q to detach from the docker, then check if installation worked:\
TODO: Is it really needed to stop and restart ?

```
docker stop ipa-container
docker start ipa-container
```

Get back in the docker to check if it worked

```
docker exec -it ipa-container bash

# Verify that we can obtain a ticket for the admin user
kinit admin
klist
```

Usefull extra packages:\
(`iputils` for `ping` and `openssh-clients` for `scp`)

```
yum install iputils openssh-clients
```

### Make the ipa docker into a service

Create `/etc/systemd/system/freeipa.service` and put the following inside:

```
[Unit]
Description=Start FreeIPA user authentication server
StartLimitIntervalSec=0
Requires=docker.service
After=docker.service

[Service]
Type=simple
RemainAfterExit=yes
Restart=always
RestartSec=5
ExecStart=docker start ipa-container
ExecStop=docker stop ipa-container

[Install]
WantedBy=multi-user.target
```

Then `docker enable freeipa.service`

### [Enroll a client](https://wiki.archlinux.org/title/FreeIPA#Enroll_the_client)

Get back in the client (`docker exec -it ipa-container bash`), then:

```
kinit admin
ipa host-add --force --ip-address=192.168.166.31 client.example.com
ipa host-add-managedby --hosts=ipaserver.example.com client.example.com
ipa-getkeytab -s ipaserver.example.com -p host/client.example.com -k /tmp/client1.keytab
```

Install the keytab on the client:

```
scp /tmp/client1.keytab root@black.ipa.xc:/etc/krb5.keytab
```

## [Client](https://wiki.archlinux.org/title/FreeIPA#Manual_configuration_as_IPA_client)

To check the hostname, install the `inetutils` package, then `hostname --fqdn` should give the FQDN. It should look like `censei.xc` or `black.xc`.\
(The host name can be changed in `/etc/hostname` (just put `black` for example) and `/etc/hosts` (127.0.1.1 black.xc black) in case it was wrongly done during the installation)

### Client setup

`pacman -S sssd`

#### /etc/sssd/sssd.conf

Set its content to the following, only the `ipa_hostname` needs to be changed.

```
[sssd]
config_file_version = 2
services = nss, pam, sudo, ssh
domains = ipa.xc
debug_level = 5

[domain/IPA.XC]
debug_level = 5
cache_credentials = true
krb5_store_password_if_offline = true
id_provider = ipa
auth_provider = ipa
access_provider = ipa
ipa_domain= ipa.xc
ipa_server= censei.ipa.xc
ipa_hostname= black.ipa.xc
# override_shell = /usr/bin/zsh #feel free to comment this out if you dont need/want it
```

#### /etc/nsswitch.conf

Copy paste the following, no changes needed.

```
# Name Service Switch configuration file.
# See nsswitch.conf(5) for details.
# Begin /etc/nsswitch.conf

passwd: files sss
group: files sss
shadow: files sss
sudoers: files sss

publickey: files

hosts: files dns myhostname
networks: files

protocols: files
services: files
ethers: files
rpc: files

netgroup: files
# End /etc/nsswitch.conf
```

#### /etc/krb5.conf

Copy paste the following, no changes needed.

```
[libdefaults]
        default_realm = IPA.XC
        dns_lookup_realm = false
        dns_lookup_kdc = false
        rdns = false
        ticket_lifetime = 24h
        forwardable = yes
        #allow_weak_crypto = yes  # Only if absolutely necessary. Currently FreeIPA supports strong crypto.

[realms]
        IPA.XC = {
                admin_server = censei.ipa.xc:749
                kdc = censei.ipa.xc:88
                master_kdc = censei.ipa.xc:88
                default_admin = ipa.xc
        }

[domain_realm]
        ipa.xc = IPA.XC
        .ipa.xc = IPA.XC

[logging]
        default = FILE:/var/log/krb5libs.log
        kdc = FILE:/var/log/krb5kdc.log
        admin_server = FILE:/var/log/kadmin.log
```

#### /etc/nscd.conf

Look for those 2 lines

```
enable-cache		group		yes
enable-cache		passwd		yes
# enable-cache		netgroup	yes
```

And change them to:

```
enable-cache		group		no
enable-cache		passwd		no
# enable-cache		netgroup	no
```

Maybe do the same for netgroup according to the [wiki](https://wiki.archlinux.org/title/LDAP_authentication#SSSD_Configuration), but it does not seem to be necessary.

#### XC DNS

Usual XC DNS is `192.168.100.53`, but to use the IPA DNS it should be changed to `192.168.2.59`. This can be done temporarily (and with immediate effect) in `/etc/resolv.conf`. For a more permanent change, either make the file immutable with `chattr +i /etc/resolv.conf` or use [resolvconf](https://wiki.archlinux.org/title/Resolvconf).

#### Permissions

```
chmod 0600 /etc/sssd/sssd.conf
chown root:root /etc/sssd/sssd.conf
```

#### Checking

Before changing the client system, check that everything went well so far.

```
systemctl restart nscd
systemctl restart sssd
systemctl enable sssd
getent passwd testipa@ipa.xc
```

This should return something like (assuming a testipa account has been created):

```
testipa:*:497400005:497400005:Test IPA:/home/testipa:/bin/zsh
```

### PAM client

Maybe try to understand what [this blog](https://blog.waan.name/manually-setting-up-freeipa-client-on-archlinux/) is doing.

#### /etc/pam.d/system-auth

Make pam_sss.so optional at the top of the auth, account and password sections.

```
auth sufficient pam_sss.so
account sufficient pam_sss.so
password sufficient pam_sss.so
```

For the session section, make it optional like so:

```
session   required  pam_limits.so
session   required  pam_unix.so
session   optional  pam_sss.so
session   optional  pam_permit.so
```

#### /etc/pam.d/su and /etc/pam.d/su-l

```
#%PAM-1.0
auth            sufficient      pam_rootok.so
auth            sufficient      pam_sss.so
auth            required        pam_unix.so use_first_pass

account         sufficient      pam_sss.so
account         required        pam_unix.so

session         sufficient      pam_sss.so
session         required        pam_unix.so
```

#### /etc/pam.d/passwd

```
#%PAM-1.0
password        sufficient      pam_sss.so
password        required        pam_unix.so sha512 shadow nullok
```

#### /etc/pam.d/sudo

**Do NOT change**, despites what the arch wiki says. Just leave the defaults (see bellow), the pam_sss will the be provided through the proper includes.

```
auth            include         system-auth
account         include         system-auth
session         include         system-auth
```

#### /etc/ssh/sshd_config

Uncomment the two following lines:

```
AuthorizedKeysCommand /usr/bin/sss_ssh_authorizedkeys
AuthorizedKeysCommandUser nobody
```

(Might need to modify AuthorizedKeysCommand)

#### Create home folders at login

edit `/etc/pam.d/system-login` and add `session    required   pam_mkhomedir.so skel=/etc/skel umask=0022` to the session section above any "sufficient" items (I just placed it at the bottom since there was no sufficient item).

### Migrate old local users to ipa ones

On the IPA docker (or through the web UI), create the new users. For example:

```
ipa user-add tsuzuki --first=Takuya --last=Suzuki --password
```

Password can be the username, the user will need to change it upon first login.\
Delete the old local user with `userdel tsuzuki`. Then, get the UID of the new user, and on every server, change the ownership of their homefolder. For example `chown -R 497400008 /home/tsuzuki/`.

## Misc

### DNS

Instead of using 2 DNS (that have different entries), hard code the usual addresses in `/etc/hosts` like so:

```
# Usual names
192.168.100.225         censei.xc       censei
192.168.100.203         bitter.xc       bitter
192.168.100.43          black.xc        black
192.168.100.119         dgx.xc          dgx

# API names
192.168.100.225         censei.ipa.xc   censei
192.168.100.203         bitter.ipa.xc   bitter
192.168.100.43          black.ipa.xc    black
192.168.100.119         dgx.ipa.xc      dgx
```

And change the DNS to the IPA one in `/etc/resolv.conf`:

```
# Default XC DNS
# nameserver 192.168.100.53
# FreeIPA DNS
nameserver 192.168.2.59
```

Then use `chattr +i /etc/resolv.conf` to prevent the network manager from overwritting it.

### IPA admin commands:

`ipa user-show <user>` and `ipa user-status <user>` to get some infos on a user.\
`ipa pwpolicy-mod global_policy --minlife 0` to be able to change a password whenever.

#### Add users

To add a user:

```
ipa user-add ipa_test --first=Test --last=Ipa --password
```

To find one:

```
ipa user-find ipa_test
ipa user-find --all
```

Or just use the [web ui](https://censei.ipa.xc/ipa/ui/#/e/user/search)
