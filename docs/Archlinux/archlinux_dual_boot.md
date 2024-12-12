# Arch Linux Dual Boot with Windows 11

## Preparation on Windows 11

We will install Windows 11 first, then Arch Linux.

We need at least 512MB EFI partition in Arch Linux, so we will create a 1GB EFI system during Windows 11 Installation.

[Reference](https://blog.realogs.in/dual-booting-windows11-with-arch-linux/)

To create a 1GB EFI boot system:

1. remove all the partitions
2. `shift + f10` to open cmd

and then

```
diskpart
list disk
sel disk 1
create partition efi size=1024
format quick fs=fat32 label=System
exit
```

and then keep installing Windows 11.

## Create Live USB for Arch Linux

Now for Arch Linux.

First, we need to allocate some space for Arch Linux, so we go to disk management, and shrink the Windows 11 we installed, like 200GB.

and then create Live USB for Arch Linux, [balenaEtcher](https://www.balena.io/etcher)

When install Arch Linux, if you need wifi, type `iwctl`

```
iwctl
device list
station wlan0 scan
station wlan0 get-networks
station wlan0 connect IAN2
```

and then `ping google.com` to test

## Config Before Installation

!!! note
    make terminal font bigger

    ```bash
    cd /usr/share/kbd/consolefonts/
    setfont ter-d20b.psf.gz
    ```
    
    in `/etc/makepkg.conf`
    
    ```pkgconfig
    MAKEFLAGS="-j$(expr $(nproc) \+ 1)"
    ```
    
    in `/etc/pacman.conf`
    
    ```ini
    Color
    ParallelDownloads = 5
    ```
    
    ```pacmanconf
    [core]
    Server = http://archlinux.cs.nctu.edu.tw/$repo/os/$arch
    Include = /etc/pacman.d/mirrorlist
    
    [extra]
    Server = http://archlinux.cs.nctu.edu.tw/$repo/os/$arch
    Include = /etc/pacman.d/mirrorlist
    
    #[community-testing]
    #Include = /etc/pacman.d/mirrorlist
    
    [community]
    Server = http://archlinux.cs.nctu.edu.tw/$repo/os/$arch
    Include = /etc/pacman.d/mirrorlist
    ```
    
    ```bash
    pacman -Syy
    ```

## Partitioning

```bash
lsblk
```

```bash
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
nvme0n1     259:0    0 931.5G  0 disk
├─nvme0n1p1 259:1    0     1G  0 part /boot           # EFI System
├─nvme0n1p2 259:2    0    16M  0 part
├─nvme0n1p3 259:3    0 730.5G  0 part                 # Windows 11 Partition
├─nvme0n1p4 259:4    0     8G  0 part [SWAP]          # Linux Swap
└─nvme0n1p5 259:5    0   192G  0 part /               # Linux Partition
```

and use `cfdisk /dev/nvme0n1` to partition

```
mkswap /dev/nvme0n1p4
swapon /dev/nvme0n1p4
mkfs.ext4 /dev/nvme0n1p5
```

## Mount the file systems

```
mount /dev/nvme0n1p5 /mnt
```

```
mount --mkdir /dev/nvme0n1p1 /mnt/boot
```

```
pacstrap /mnt base linux linux-firmware vim
```

```
genfstab -U /mnt >> /mnt/etc/fstab
arch-chroot /mnt
```

## Set Time and Locale

```bash
ln -sf /usr/share/zoneinfo/Asia/Taipei /etc/localtime
```

```bash
vim /etc/locale.gen
```

```bash
# uncomment this line
en_US.UTF-8 UTF-8
```

```bash
locale-gen
echo LANG=en_US.UTF-8 > /etc/locale.conf
```

the name is the computer's name

```bash
echo myarch > /etc/hostname
```

```bash
vim /etc/hosts
```

```
127.0.0.1 localhost
::1 localhost
127.0.1.1 myarch
```

```bash
passwd
```


## Install Grub Boot Loader

```
pacman -S grub efibootmgr os-prober networkmanager wireless_tools wpa_supplicant iwd
```

```
systemctl enable NetworkManager
systemctl enable iwd
```



install grub:

```
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
```

in `/etc/default/grub`, uncomment (this can be done by archlinux_install script):

```
GRUB_DISABLE_OS_PROBER=false
```

Generate grub config:

```
grub-mkconfig -o /boot/grub/grub.cfg
```

!!! note
    After we reboot, we may see that there's no windows 11 option on grub menu, just log in Arch Linux, and type:

    ```
    sudo grub-mkconfig -o /boot/grub/grub.cfg
    ```

## Add User

the name is the user's name

```bash
pacman -S sudo
useradd -m ian
passwd ian
```

```bash
usermod -aG wheel,audio,video,storage ian
```

```bash
EDITOR=vim visudo
```

uncomment this line

```bash
## Uncomment to allow members of group wheel to execute any command
%wheel ALL=(ALL:ALL) ALL
```

## Finish Arch Linux Installation
```bash
exit
```

```bash
umount /mnt
```

or

```bash
umount -l /mnt
```

```bash
shutdown now
```

## After Installation

connect wifi with `nmtui` or `iwctl`

!!! note "After Installation"

    config terminal fonts
    
    ```bash
    sudo pacman -S terminus-font
    cd /usr/share/kbd/consolefonts/
    setfont ter-d20b.psf.gz
    ```
    
    in `/etc/vconsole.conf`
    
    ```
    FONT=ter-d20b.psf.gz
    ```
    
    in `/etc/makepkg.conf`
    
    ```ini
    MAKEFLAGS="-j$(expr $(nproc) \+ 1)"
    ```
    
    in `/etc/pacman.conf`
    
    ```pacmanconf
    Color
    ParallelDownloads = 5
    ```
    
    ```pacmanconf
    [core]
    Server = http://archlinux.cs.nctu.edu.tw/$repo/os/$arch
    Include = /etc/pacman.d/mirrorlist
    
    [extra]
    Server = http://archlinux.cs.nctu.edu.tw/$repo/os/$arch
    Include = /etc/pacman.d/mirrorlist
    
    #[community-testing]
    #Include = /etc/pacman.d/mirrorlist
    
    [community]
    Server = http://archlinux.cs.nctu.edu.tw/$repo/os/$arch
    Include = /etc/pacman.d/mirrorlist
    ```


## Install Yay AUR Helper

```bash
sudo pacman -Syy
```

copy github ip to `/etc/hosts`:

```bash
140.82.114.3 github.com
```

```bash
sudo pacman -S base-devel git
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

## Install User Packages

```bash
git clone https://github.com/zyrethor/archlinux_install
cd archlinux_install
./install.sh ## default install packages
./install.sh -s ## install system settings
./install.sh -a ## install additional packages
./install.sh -vm ## install vmware packages, after installation, reboot
```

If the installation takes too long, then lengthen sudo timeout, for example 60 min:

```bash
sudo EDITOR=vim visudo

Defaults env_reset, timestamp_timeout=60
```

