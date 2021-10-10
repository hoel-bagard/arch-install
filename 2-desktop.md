### [Xorg](https://wiki.archlinux.org/title/Xorg)
Identify graphics card with `lspci -v | grep -A1 -e VGA -e 3D`.\
And install the corresponding driver (usually nvidia, probably already installed at the beginning).

Then install [Plasma](https://wiki.archlinux.org/title/KDE#Plasma) with `sudo pacman -S plasma`.\
Enable sddm to have the lockscreen after boot:
```
systemctl enable sddm
```
Also, enable NetworkManager to get the wifi:   (Move this part to the main install?)
```
sudo systemctl enable NetworkManager
```


### KDE tools
#### [Multimedia](https://archlinux.org/packages/extra/any/kde-multimedia-meta/)
```
sudo pacman -S kmix 
```

#### [Utils](https://archlinux.org/groups/x86_64/kde-utilities/)
```
sudo pacman -S kate kcalc kdialog konsole
```

#### [Graphics](https://archlinux.org/packages/extra/any/kde-graphics-meta/)
```
sudo pacman -S gwenview svgpart spectacle okular kolourpaint kipi-plugins kdegraphics-thumbnailers 
```

#### [System](https://archlinux.org/packages/extra/any/kde-system-meta/)
```
sudo pacman -S dolphin 
```

#### [SDK Meta](https://archlinux.org/packages/extra/any/kde-sdk-meta/)
```
sudo pacman -S dolphin-plugins
```

#### [Network](https://archlinux.org/packages/extra/any/kde-network-meta/)
```
sudo pacman -S ktorrent 
```
Note: `kdeconnect ` might be interesting for some.


### Other tools
From the official repositories:
```
sudo pacman -S vlc firefox remmina code gimp discord libreoffice-still
```

From the AUR:
```
yay -S google-chrome anki via-bin
```
