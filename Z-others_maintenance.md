## [Restore pacman packages to an earlier date](https://wiki.archlinux.org/title/Downgrading_packages)
In `/etc/pacman.conf`, to go back to 2021-05-15:

```
[core]
SigLevel = PackageRequired
Server=https://archive.archlinux.org/repos/2021/05/15/$repo/os/$arch

[extra]
SigLevel = PackageRequired
Server=https://archive.archlinux.org/repos/2014/03/30/$repo/os/$arch

[community]
SigLevel = PackageRequired
Server=https://archive.archlinux.org/repos/2014/03/30/$repo/os/$arch
```

And to go back to normal usage:
```
[core]
Include = /etc/pacman.d/mirrorlist

[extra]
Include = /etc/pacman.d/mirrorlist

[community]
Include = /etc/pacman.d/mirrorlist
```

## Office vpn
### OpenVPN
Install the packages:
```
sudo pacman -S openvpn networkmanager-openvpn
```

Get the client configuration file (pinned in the conversation with Sugihara-san) and copy it to `/etc/openvpn/client/xc_client.ovpn`.\
Get the username and password (look for #VPN in conversation with self) and put them in `/etc/openvpn/client/credentials.txt`. (username on first line, password on the second)\
You can then connect and disconnect to the office VPN using the commands `connect-vpn` and `disconnect-vpn` (defined in the zsh aliases).

### L2TP
To use with NetworkManager, install the [networkmanager-l2tp](https://archlinux.org/packages/community/x86_64/networkmanager-l2tp/) and [strongswan](https://archlinux.org/packages/community/x86_64/strongswan/) packages, then restart NetworkManager:
```
sudo pacman -S strongswan networkmanager-l2tp
systemctl restart NetworkManager
```

Then add the VPN connection with the following lines:\
(set the variables with your credentials)
```
CON_NAME=whatever_you_want_to_call_it
GATEWAY_HOST=xxx.xxx.xxx.xxx
PRE_SHARED_KEY=just_a_string
USERNAME=your_username
PASSWORD=random_string
nmcli c add con-name $CON_NAME type VPN vpn-type l2tp vpn.data "gateway=$GATEWAY_HOST, ipsec-enabled=yes, ipsec-psk=$PRE_SHARED_KEY, password-flags=0, user=$USERNAME" vpn.secrets password=$PASSWORD
```
You can delete the created connection with `nmcli c delete $CON_NAME` if something went wrong.

(Note for self: the XC credentials are in the conversation with myself on slack (it's pinned and tagged with "VPN"))
