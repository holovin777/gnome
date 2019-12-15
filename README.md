# wos
```bash
ping archlinux.org
timedatectl set-ntp true
fdisk -l
fdisk /dev/sdX
```
## MBR with BIOS
```bash
---
o
n
+40G
n
w
---

mkfs.ext4 /dev/sdX1
mkfs.ext4 /dev/sdX2
mount /dev/sdX1 /mnt
mkdir /mnt/home
mount /dev/sdX2 /mnt/home
pacstrap /mnt base base-devel linux linux-firmware gvim
genfstab -U /mnt >> /mnt/etc/fstab
arch-chroot /mnt
ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
hwclock --systohc
vim /etc/locale.gen
---
...
en_US.UTF-8 UTF-8
...
---

locale-gen
vim /etc/locale.conf
---
LANG=en_US.UTF-8
---

vim /etc/hostname
---
myhostname
---

vim /etc/hosts
---
127.0.0.1	localhost
::1		localhost
127.0.1.1	myhostname.localdomain    myhostname
---

passwd
pacman -Syu
pacman -S gnome gnome-software-packagekit-plugin gnome-flashback gnome-keyring gnome-tweaks gnome-applets xf86-video-fbdev xf86-video-vesa xf86-video-ati xf86-video-intel xf86-video-amdgpu xf86-video-nouveau xf86-input-synaptics xorg-server xorg-xinit network-manager-applet dnsmasq ttf-dejavu ttf-droid xmonad xmonad-contrib dmenu sudo grub
pacman -S intel-ucode amd-ucode
grub-install --target=i386-pc /dev/sdX
grub-mkconfig -o /boot/grub/grub.cfg
exit
umount -R /mnt
reboot
useradd -m -G users -s /bin/bash user
useradd -m -G users,wheel -s /bin/bash admin
passwd user
passwd admin
systemctl enable NetworkManager.service
systemctl start NetworkManager.service
sudo vim /etc/sudoers
---
## Uncomment to allow members of group wheel to execute any command
%wheel ALL=(ALL) ALL
---

sudo vim /etc/gdm/custom.conf
---
...
# Uncomment the line below to force the login screen to use Xorg
WaylandEnable=false
...
---

systemctl start gdm.service
systemctl enable gdm.service
sudo pacman -S ntfs-3g android-file-transfer chromium vlc libreoffice-fresh gimp git clipgrab firefox wget openshot evolution rsync
sudo pacman -S android-tools android-udev
sudo fallocate -l 512M /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
sudo vim /etc/fstab
---
...
/swapfile none swap defaults 0 0
---
```
## Solid state drive
```bash
lsblk --discard
sudo vim /etc/fstab
---
/dev/sdX1  /           ext4  defaults,discard   0  1
---
```
## Nopassword user
```bash
sudo vim /etc/gdm/custom.conf
---
# Enable automatic login for user with a delay
[daemon]
...
TimedLoginEnable=true
TimedLogin=user
TimedLoginDelay=4

# Enable automatic login for user without delay
#[daemon]
...
#AutomaticLogin=username
#AutomaticLoginEnable=True
---

vim /etc/pam.d/gdm-password
---
...
auth sufficient pam_succeed_if.so user ingroup nopasswdlogin
---

groupadd nopasswdlogin
gpasswd -a user nopasswdlogin
```
## Xmonad
```bash
cp /etc/X11/xinit/xinitrc ~/.xinitrc
vim ~/.xinitrc
---
...
exec xmonad
---

mkdir .xmonad
vim .xmonad/xmonad.hs
---
import XMonad

main = xmonad def
    { terminal    = "gnome-terminal"
    , modMask     = mod4Mask
    , borderWidth = 1
    }
---

xmonad --recompile
```
## Steam
```bash
sudo vim /etc/pacman.conf
---
...
[multilib]
Include = /etc/pacman.d/mirrorlist
...
---

sudo pacman -Syu
sudo pacman -S steam ttf-liberation
```

## Bluetooth
```bash
sudo systemctl start bluetooth.service
sudo systemctl enable bluetooth.service
```

## Some commands
```bash
rsync -r source/. destination
mkfs.fat -F 32 /dev/sdX1
```
