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
https://gist.github.com/pastleo/aa3a9524664864c505d637b771d079c9
