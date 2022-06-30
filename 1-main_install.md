# Main Install
### Early commands
loadkeys jp106\
Check if in efi mode: `ls /sys/firmware/efi/efivars`  (should execute without error)\
timedatectl set-ntp true

## Partition the disks
### Create the partitions
List devices: `fdisk -l`\
`fdisk /dev/sda`   (or whichever device is to be used)
1G for boot
4G swap
Rest to root partition

### Format the partitions:
```
mkfs.vfat /dev/boot_partition -n BOOT
mkswap /dev/swap_partition -L SWAP
mkfs.ext4 /dev/root_partition -L ROOT
```

### Mount the file systems
```
mount /dev/root_partition /mnt
mkdir /mnt/boot
mount /dev/boot_partition /mnt/boot
swapon /dev/swap_partition
```

### Select mirrors
```
reflector --verbose --protocol https --latest 50 --sort rate --save /etc/pacman.d/mirrorlist
```
### Install essential packages
```
pacstrap /mnt base base-devel linux-lts linux-lts-headers nvidia-dkms linux-firmware bash-completion vim nano dhcpcd
```

## Configure the system
Fstab: 
```
genfstab -U /mnt >> /mnt/etc/fstab
```
Note: then edit the fstab to change the relatime to noatime.\
Chroot:
```
arch-chroot /mnt
```

Remove the annoying beeping sound with `rmmod pcspkr`.

Time:
```
ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
hwclock --systohc
```

Localization:\
Uncomment `en_US.UTF-8 UTF-8` in `/etc/locale.gen` and then run `locale-gen`
```
echo "LANG=en_US.UTF-8" >> /etc/locale.conf
echo "KEYMAP=jp106" >> /etc/vconsole.conf
```

Network configuration (change myhostname to a proper name):
```
echo "myhostname" >> /etc/hostname
echo "127.0.0.1	localhost" >> /etc/hosts
echo "::1		localhost" >> /etc/hosts
echo "127.0.1.1	myhostname.localdomain	myhostname" >> /etc/hosts
systemctl enable dhcpcd
```

Set root password: `passwd` 

Install more packages:\
(Replace amd-ucode by intel-ucode depending on the cpu brand)\
```
pacman -S amd-ucode man-db pciutils wget git exa htop nvtop bat mlocate rsync ripgrep git-delta unzip sshfs
```
(Use the `updatedb` command to create the locate database)\
**Note1**: htop could be replaced by [bottom](https://github.com/ClementTsang/bottom) (basically htop on steroid + [gping](https://github.com/orf/gping)).\
**Note2**: The default du command can be replaced by [dust](https://github.com/bootandy/dust)\
**Note3**: [bat-extra](https://github.com/eth-p/bat-extras) for things like batgrep and co. (needs to be git cloned and installed).

## Boot loader (grub):
[grub-mkconfig will automatically detect the microcode update and configure GRUB appropriately. After installing the microcode package, regenerate the GRUB config to activate loading the microcode update by running:
](https://wiki.archlinux.org/title/microcode)
```
pacman -S grub efibootmgr
mkdir /boot/efi
mount /dev/sda1 /boot/efi
grub-install --target=x86_64-efi --bootloader-id=GRUB --efi-directory=/boot/efi
grub-mkconfig -o /boot/grub/grub.cfg
```

## Sudo
```
pacman -S sudo
groupadd sudo
EDITOR=vim visudo
```
--> uncomment `sudo ALL=(ALL) ALL`

Add a sudo user:
```
useradd -m hoel
usermod -aG sudo hoel
passwd hoel
```


## Others:
### SSH
#### Install and enable (especially for servers)
```
pacman -S openssh
systemctl enable sshd.service
```

### Yay
Must be done as a normal sudo user (you might want to reboot and check that everything work).
```
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
cd ..
sudo rm -r yay
```

### Fonts
```
pacman -S noto-fonts adobe-source-han-sans-otc-fonts adobe-source-han-serif-otc-fonts adobe-source-code-pro-fonts ttf-roboto ttf-dejavu ttf-liberation noto-fonts-emoji
```

#### Pacman
Optional: add a nice [colored output](https://wiki.archlinux.org/title/Color_output_in_console#pacman) by uncommenting `Color` in `/etc/pacman.conf`.
