# Gnome
## Install Arch Linux via SSH
```bash
passwd
systemctl start sshd.service
ip addr show
```
On the local machine
```bash
ssh root@ip.address.of.target
```
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
pacstrap /mnt base base-devel linux linux-firmware gvim man
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
gnome
---

vim /etc/hosts
---
127.0.0.1	localhost
::1		localhost
127.0.1.1	gnome.localdomain    gnome
---

passwd
pacman -Syu
pacman -S gnome gnome-software-packagekit-plugin gnome-flashback gnome-keyring gnome-tweaks gnome-applets xf86-video-fbdev xf86-video-vesa xf86-video-ati xf86-video-intel xf86-video-amdgpu xf86-video-nouveau xf86-input-synaptics xorg-server xorg-xinit network-manager-applet dnsmasq ttf-dejavu ttf-droid ttf-liberation wqy-zenhei sudo grub gst-libav ntfs-3g intel-ucode amd-ucode
grub-install --target=i386-pc /dev/sdX
grub-mkconfig -o /boot/grub/grub.cfg
vim /etc/sudoers
---
## Uncomment to allow members of group wheel to execute any command
%wheel ALL=(ALL) ALL
---

vim /etc/gdm/custom.conf
---
...
# Uncomment the line below to force the login screen to use Xorg
WaylandEnable=false
...
---

exit
umount -R /mnt
reboot
useradd -m -G users -s /bin/bash user
useradd -m -G users,wheel -s /bin/bash admin
passwd user
passwd admin
systemctl enable NetworkManager.service
systemctl start NetworkManager.service
systemctl start gdm.service
systemctl enable gdm.service
sudo pacman -S android-file-transfer android-tools android-udev chromium vlc libreoffice-still gimp git clipgrab firefox wget openshot evolution transmission-cli rsync postgresql inkscape gnome-sound-recorder
sudo dd if=/dev/zero of=/swapfile bs=1M count=4096 status=progress
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
sudo vim /etc/fstab
---
...
# swapfile
/swapfile    none    swap    defaults    0    0
---
```
## Solid state drive
```bash
sudo vim /etc/fstab
---
/dev/sdX1    /    ext4    defaults,discard    0    1
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
## Hide user from login list
```bash
sudo vim /var/lib/AccountsService/users/username
---
...
SystemAccount=true
---
```

## Xmonad
```bash
sudo pacman -S xmonad xmonad-contrib dmenu
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

## Printer
```bash
sudo pacman -S cups cups-pdf
sudo systemctl start org.cups.cupsd.service
sudo systemctl enable org.cups.cupsd.service
```
[Drivers](https://wiki.archlinux.org/index.php/CUPS/Printer-specific_problems)

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
sudo pacman -S steam
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
dd bs=4M if=path/to/archlinux.iso of=/dev/sdX status=progress oflag=sync
sudo fsck.ext4 /dev/sdX1
sudo ntfsfix /dev/sdX1
sudo blkid | grep sdX
makepkg -si
ssh-keygen -R <host>
cp -r -n Downloads Downloads1
sudo chown -R user:user /home/user
```

### Install windows.iso
```bash
cd Downloads
pacman -S p7zip
git clone https://aur.archlinux.org/windows2usb-git.git
git clone https://aur.archlinux.org/ms-sys.git
cd ms-sys
makepkg -si
cd ../windows2usb-git
makepkg -si
```
[How to use](https://github.com/ValdikSS/windows2usb#how-to-use)
