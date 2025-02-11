### [Xorg](https://wiki.archlinux.org/title/Xorg)

Identify graphics card with `lspci -v | grep -A1 -e VGA -e 3D`.\
And install the corresponding driver (usually nvidia, probably already installed at the beginning).

## KDE

Install [Plasma](https://wiki.archlinux.org/title/KDE#Plasma) with `sudo pacman -S plasma` (the plasma package has a fair amount of bloat).\
Enable sddm to have the lockscreen after boot:

```console
sudo systemctl enable sddm
```

Also, enable NetworkManager to get the wifi: (Move this part to the main install?)

```console
sudo systemctl enable NetworkManager
```

### KDE tools

#### [Multimedia](https://archlinux.org/packages/extra/any/kde-multimedia-meta/)

```console
sudo pacman -S kmix
```

#### [Utils](https://archlinux.org/groups/x86_64/kde-utilities/)

```console
sudo pacman -S kate kcalc kdialog konsole
```

#### [Graphics](https://archlinux.org/packages/extra/any/kde-graphics-meta/)

```console
sudo pacman -S gwenview svgpart spectacle okular kolourpaint kdegraphics-thumbnailers
```

#### [System](https://archlinux.org/packages/extra/any/kde-system-meta/)

```console
sudo pacman -S dolphin
```

#### [SDK Meta](https://archlinux.org/packages/extra/any/kde-sdk-meta/)

```console
sudo pacman -S dolphin-plugins
```

#### [Network](https://archlinux.org/packages/extra/any/kde-network-meta/)

```console
sudo pacman -S ktorrent
```

Note: `kdeconnect` might be interesting for some.

#### Other

```console
sudo pacman -S ark krunner ffmpegthumbs
```

Ark is used to have zip / unzip functionalities within dolphin.

## Bluetooth

```console
pacman -S bluez bluez-utils pulseaudio-bluetooth
sudo systemctl enable bluetooth
```

reboot.

## Japanese keyboard layout

```console
sudo pacman -S fcitx5 fcitx5-mozc fcitx5-configtool fcitx5-qt fcitx5-gtk
```

The environment variables are in the `.zshrc`.

## I3

https://i3wm.org/docs/userguide.html
https://wiki.archlinux.org/title/I3
https://mudrii.medium.com/arch-linux-installation-on-hw-with-i3-windows-manager-part-2-x-window-system-and-i3-installation-86735e55a0a0
https://mudrii.medium.com/archlinux-tutorial-part-3-i3-configuration-and-operation-9cd6dc90e524

https://wiki.archlinux.org/title/Dmenu

#### Numlock on boot

https://wiki.archlinux.org/title/Activating_numlock_on_bootup#X.org

## Other tools

From the official repositories:

```console
sudo pacman -S vlc firefox remmina freerdp code gimp discord libreoffice-still
```

From the AUR:

```console
yay -S google-chrome anki via-bin
```

## Scrcpy

```console
sudo pacman -S android-tools
sudo pacman -S scrcpy
```

Enable USB (and optionnaly wifi) debugging on the phone, then connect it with USB and authorize the PC. Finally, check that the phone is detected with `adb devices`.\
Get the phone's IP address in `Settings > About phone.` and connect to it through wifi with:

```console
adb connect 192.168.0.236:5555
scrcpy
```
