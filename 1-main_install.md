# Main Install

### Early commands

`loadkeys jp106`\
Check if in efi mode: `ls /sys/firmware/efi/efivars` (should execute without error)\
`timedatectl set-ntp true`

## Partition the disks

### Create the partitions

List devices: `fdisk -l`\
`fdisk /dev/sda` (or whichever device is to be used)
1G for boot
4G swap
Rest to root partition

### Format the partitions:

```console
mkfs.vfat /dev/boot_partition -n BOOT
mkswap /dev/swap_partition -L SWAP
mkfs.ext4 /dev/root_partition -L ROOT
```

### Mount the file systems

```console
mount /dev/root_partition /mnt
mkdir /mnt/boot
mount /dev/boot_partition /mnt/boot
swapon /dev/swap_partition
```

### Select mirrors

```console
reflector --verbose --protocol https --latest 50 --sort rate --save /etc/pacman.d/mirrorlist
```

### Install essential packages

```console
pacstrap /mnt base base-devel linux-lts linux-lts-headers nvidia-dkms linux-firmware bash-completion neovim dhcpcd
```

## Configure the system

Fstab:

```console
genfstab -U /mnt >> /mnt/etc/fstab
```

Note: then edit the fstab to change the relatime to noatime.\
Chroot:

```console
arch-chroot /mnt
```

Remove the annoying beeping sound with `rmmod pcspkr`.

Time:

```console
ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
hwclock --systohc
```

Localization:\
Uncomment `en_US.UTF-8 UTF-8` in `/etc/locale.gen` and then run `locale-gen`

```console
echo "LANG=en_US.UTF-8" >> /etc/locale.conf
echo "KEYMAP=jp106" >> /etc/vconsole.conf
```

Network configuration (change myhostname to a proper name):

```console
echo "myhostname" >> /etc/hostname
echo "127.0.0.1	localhost" >> /etc/hosts
echo "::1		localhost" >> /etc/hosts
echo "127.0.1.1	myhostname.localdomain	myhostname" >> /etc/hosts
systemctl enable dhcpcd
```

Set root password: `passwd`

Install more packages:\
(Replace amd-ucode by intel-ucode depending on the cpu brand)

```console
pacman -S amd-ucode man-db pciutils wget git eza htop nvtop bat mlocate rsync ripgrep fd git-delta unzip sshfs zoxide fzf
```

(Use the `updatedb` command to create the locate database)\
**Note1**: htop could be replaced by [bottom](https://github.com/ClementTsang/bottom) (basically htop on steroid + [gping](https://github.com/orf/gping)).\
**Note2**: The default du command can be replaced by [dust](https://github.com/bootandy/dust)\
**Note3**: [bat-extra](https://github.com/eth-p/bat-extras) for things like batgrep and co. (needs to be git cloned and installed).

## Boot loader (grub):

[grub-mkconfig will automatically detect the microcode update and configure GRUB appropriately. After installing the microcode package, regenerate the GRUB config to activate loading the microcode update by running:
](https://wiki.archlinux.org/title/microcode)

```console
pacman -S grub efibootmgr
mkdir /boot/efi
mount /dev/sda1 /boot/efi
grub-install --target=x86_64-efi --bootloader-id=GRUB --efi-directory=/boot/efi
grub-mkconfig -o /boot/grub/grub.cfg
```

## Sudo

```console
pacman -S sudo
groupadd sudo
EDITOR=vim visudo
```

--> uncomment `sudo ALL=(ALL) ALL`

Add a sudo user:

```console
useradd -m hoel
usermod -aG sudo hoel
passwd hoel
```

## Others:

### SSH

#### Install and enable (especially for servers)

```console
pacman -S openssh
systemctl enable sshd.service
```

### Laptop

On laptops, to be able to easily control the power profile:

```bash
pacman -S power-profiles-daemon
```

### Fonts

```console
pacman -S adobe-source-han-sans-otc-fonts adobe-source-han-serif-otc-fonts adobe-source-code-pro-fonts ttf-roboto ttf-dejavu ttf-liberation ttf-jetbrains-mono-nerd
```

Japanese fonts:

```console
pacman -S noto-fonts-cjk noto-fonts-emoji noto-fonts otf-ipafont ttf-sazanami adobe-source-han-sans-jp-fonts adobe-source-han-serif-jp-fonts ttf-hanazono
```

### Pacman

In `/etc/pacman.conf`:

- Add a nice [colored output](https://wiki.archlinux.org/title/Color_output_in_console#pacman) by uncommenting `Color`.
- To see old and new versions of available packages, uncomment `VerbosePkgLists`.
- Allow parallel downloads by uncommenting `ParallelDownloads`.

#### [Cleaning the package cache](https://wiki.archlinux.org/title/Pacman#Cleaning_the_package_cache)

Install `pacman-contrib` to get paccache.

```console
pacman -S pacman-contrib
```

Set paccache to discard unused packages weekly:

```console
systemctl enable paccache.timer
systemctl start paccache.timer
```

### Yay

Must be done as a normal sudo user (you might want to reboot and check that everything works).

```console
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
cd ..
rm -r yay
```
