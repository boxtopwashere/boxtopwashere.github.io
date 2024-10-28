# System Administration Arch Linux Install
## Pre Install
#### Preparing Installation
* Download Arch Linux ISO and verify signature use sha256sums
* Create new VM using verified ISO with 20GB of HDD


## Base Installation
#### Boot and Installation Media 
* check boot mode
```sh
	cat /sys/firmware/efi/fw_platform_size 
	# if returns anything other than 64 adjust vm settings to boot from efi
```
* network
```sh
	ping google.com 
	# check if you can connect to the internet
```
* system clock
```sh
	timedatectl
```

#### Partitioning The Disk
```sh
lsblk # identify what disk are connected and which one you will install on
fdisk /dev/sda # disk we are installing to
g #for gpt table
n >> 1 >> [enter] >> +550M
n >> 2 >> [enter] >> 2G
n >> 3 >> [enter] >> [enter]
t >> 1 >> 1 # efi system
t >> 2 >> 19 # linux swap 
# partition 3 is set to linux filesystem by default
```

#### Formatting The Partion
```sh
* mkfs.fat  -F32  /dev/sda1 # efi file system
* mkswap /dev/sda2 # swap
* mkfs.ext4 /dev/sda3 # root file system
```

#### Mount File Systems
```sh mount /dev/sda3 /mnt
* mount --mkdir /dev/sda1 /mnt/boot
swapon /dev/sda2
```


## Installation & Configuration
#### Installing Packages
```sh
pacstrap -K /mnt base linux linux-firmware # install essential packages
pacman -Sy nano vim git curl firefox # additional packages
pacman -Sy openssh # install ssh
```

#### Generate fstab
```sh
genfstab -U /mnt >> /mnt/etc/fstab
```
#### Chroot
```sh
arch-chroot /mnt
```

#### Time
```sh
ln -sf /usr/share/zoneinfo/"Region"/"City" /etc/localtime
hwclock --systohc
```

#### Localization
```sh
nano /etc/locale.gen  
"uncomment 'en_US.UTF-8'"
locale-gen
```

#### Network Configuration
```sh
nano /etc/hostname 
"ARCHBOX"
pacman -S networkmanager
systemctl enable NetworkManager
systemctl start NetworkManager```

#### Root Password
```sh
passwd
```

#### Boot Loader
```sh
* pacman -Sy grub efibootmgr
* grub-install --target=x86_64-efi --efi-directory=mnt/boot --bootloader-id=GRUB
* grub-mkconfig -o /boot/grub/grub.cfg
```

## Reboot and Post Install
#### Rebooting
```sh
exit #exit out of chroot
umount -a
reboot
```

#### Setting Up Users
```sh
useradd -m TMAC
useradd -m codi
useradd -m justin
```

```sh
passwd TMAC
passwd codi
passwd justin
passwd -e codi
passwd -e justin
```

```sh
pacman -Sy sudo vi
visudo /etc/sudoers "uncomment '%wheel ALL=(ALL:ALL) ALL'"
usermod -aG wheel TMAC
usermod -aG wheel codi
usermod -aG wheel justin
```
#### Desktop Environment
```sh
pacman -Sy xorg plasma plasma-workspace kde-applications konsole
systemctl enable sddm 
systemctl start ssdm
```

#### Customizing Terminal
```sh
pacman -S zsh zsh-completions
zsh >> 0
chsh -s /usr/bin/zsh 
"'echo $SHELL' after reboot to confirm success"
```

```sh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" 
#download ohmyzsh for further options for customization
```

```sh
#only works for current user
nano ~/.zshrc #adjusting zsh settings
PROMPT='%F{2}%n%f@%F{94}%m%f:%F{222}%~%f  ' #change color and qualties 
#write to end of file and save
source ~/.zshrc 
#set source to our adjusted file
```

#### Aliases
```sh
nano ~/.zshrc
alias c='clear'
alias v=vim
alias svi="sudo vim"
source ~/.zshrc "update terminal"
```

#### Misc Customization
```sh
yt-dlp -F 'youtube link' #gives options to download yt video
yt-dlp -f 'chosen format id' 'youtube link' # downloads yt video in chosne format
"download smart video wallpaper from desktop and wallpaper settings"
"choose downloadded video and apply"
```