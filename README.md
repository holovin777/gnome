# Gnome
![alt text](https://www.gnome.org/wp-content/uploads/2020/08/cropped-logo.png "Logo Gnome")
![alt text](https://archlinux.org/static/logos/archlinux-logo-light-scalable.1ae4cc2e2469.svg "Logo ArchLinux")
## Install Arch Linux via SSH
```bash
passwd
systemctl start sshd.service
ip addr show
```
On the local machine
```bash
ssh root@ip.address.of.target
ping archlinux.org
timedatectl set-ntp true
fdisk -l
fdisk /dev/sdX
```
```bash
o
n
+100G
n
w
```
```bash
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
```
```python
...
en_US.UTF-8 UTF-8
...
```
```bash
locale-gen
vim /etc/locale.conf
```
```python
LANG=en_US.UTF-8
```
```bash
vim /etc/hostname
```
```python
gnome
```
```bash
vim /etc/hosts
```
```python
127.0.0.1	localhost
::1		localhost
127.0.1.1	gnome.localdomain    gnome
```
```bash
passwd
pacman -Syu
```
## Install Gnome
| :exclamation: For only Qtile [pass](#continue-installation) this step |
|-----------------------------------------------------------------------|
```bash
pacman -S gnome gnome-software-packagekit-plugin gnome-flashback gnome-keyring gnome-tweaks gnome-applets
vim /etc/gdm/custom.conf
```
```python
...
# Uncomment the line below to force the login screen to use Xorg
WaylandEnable=false
...
```
## Continue installation
```bash
pacman -S xf86-input-synaptics xorg-server xorg-xinit network-manager-applet dnsmasq ttf-dejavu ttf-droid ttf-liberation wqy-zenhei sudo grub gst-libav ntfs-3g intel-ucode amd-ucode android-file-transfer android-tools android-udev chromium vlc libreoffice-still gimp git clipgrab firefox wget openshot evolution transmission-cli rsync postgresql inkscape gnome-sound-recorder
grub-install --target=i386-pc /dev/sdX
vim /etc/default/grub
```
```python
...
GRUB_DISABLE_OS_PROBER=false
...
```
```bash
grub-mkconfig -o /boot/grub/grub.cfg
vim /etc/sudoers
```
```python
## Uncomment to allow members of group wheel to execute any command
%wheel ALL=(ALL) ALL
```
## Choose video driver
```bash
pacman -S xf86-video-fbdev xf86-video-vesa xf86-video-ati xf86-video-intel xf86-video-amdgpu xf86-video-nouveau
```
## Users and services
```bash
useradd -m -G users -s /bin/bash user
useradd -m -G users,wheel,adbusers,video -s /bin/bash admin
passwd user
passwd admin
exit
umount -R /mnt
reboot
```
On the remote machine login with root
```bash
systemctl enable NetworkManager.service
systemctl start NetworkManager.service
systemctl start sshd.service
ip addr show
```
On the local machine
```bash
ssh admin@ip.address.of.target
sudo dd if=/dev/zero of=/swapfile bs=1M count=4096 status=progress
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
sudo vim /etc/fstab
```
```python
...
# swapfile
/swapfile    none    swap    defaults    0    0
...
```
## Solid state drive
```bash
sudo vim /etc/fstab
```
```python
/dev/sdX1    /    ext4    defaults,discard    0    1
```
## Start Gnome
| :exclamation: For only Qtile [pass](#qtile) this step |
|-----------------------------------------------------------------------|
On the remote machine
```bash
systemctl start gdm.service
systemctl enable gdm.service
```
## Qtile
| :exclamation: If your choose is Gnome, [pass](#hide-user-from-login-list) this step |
|-----------------------------------------------------------------------|
On the local machine
```bash
sudo pacman -S qtile
```
For default config
```bash
cp /usr/share/doc/qtile/default_config.py ~/.config/qtile/config.py
```
or you can install config file by [holovin777](https://github.com/holovin777/qtile)
```bash
cd .config
git clone https://github.com/holovin777/qtile.git
cd
```
### Xinitrc
```bash
cp /etc/X11/xinit/xinitrc ~/.xinitrc
vim ~/.xinitrc
```
```python
...
exec qtile start
```
```bash
startx
```
## Hide user from login list GDM
```bash
sudo vim /var/lib/AccountsService/users/username
```
```python
...
SystemAccount=true
...
```
## Hide applications from menu Gnome
Example gnome-boxes
Names for applications are located in directory /usr/share/applications
```bash
vim .local/share/applications/org.gnome.Boxes.desktop
```
```python
Hidden=true
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
```
```python
...
[multilib]
Include = /etc/pacman.d/mirrorlist
...
```
```bash
sudo pacman -Syu
sudo pacman -S steam
```

## Bluetooth
```bash
sudo systemctl start bluetooth.service
sudo systemctl enable bluetooth.service
```

## Convert .md to .pdf
```bash
mkdir grip
cd grip
python -m venv venv
source venv/bin/activate
pip install grip
grip ../Documents/README.md
```
Open localhost:6419 in Firefox, put `Ctrl+p` and save pdf file.

## Some commands
```bash
git remote set-url origin git@github.com:holovin777/gnome.git
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
pacman -Scc
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
