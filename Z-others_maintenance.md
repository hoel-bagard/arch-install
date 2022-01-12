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

[This guide](https://gist.github.com/pastleo/aa3a9524664864c505d637b771d079c9) is slightly outdated, but still usefull (especially if using the GUI since I'm too lazy to redo the screenshots, the KDE Plasma bug has been solved though).
