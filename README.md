# Gnome Installation cheatsheet

## Install Arch Linux via SSH

If wifi connection

```bash
iwctl
device list
station DEVICE scan
station DEVICE get-networks
station DEVICE connect SSID
exit
```

```bash
passwd
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
pacstrap -K /mnt base base-devel linux linux-firmware gvim man
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
127.0.0.1   localhost
::1         localhost
127.0.1.1   gnome.localdomain   gnome
```

```bash
passwd
pacman -Syu
```

## Install gnome-shell

### Initial install

```bash
pacman -S gnome-shell gdm archlinux-keyring alacritty openssh pavucontrol xf86-input-synaptics xorg-xinit network-manager-applet dnsmasq ttf-dejavu ttf-droid wqy-zenhei noto-fonts-emoji sudo grub gst-libav ntfs-3g gnome-control-center git gnome-keyring gnome-applets wget rsync
```

### Intel or Amd install

```bash
pacman -S intel-ucode
pacman -S amd-ucode
```

### Android install

```bash
pacman -S android-file-transfer android-tools android-udev
```

### Photo, video, pdf install

```bash
pacman -S eog evince gnome-sound-recorder vlc openshot gimp inkscape
```

### LibreOffice install

```bash
pacman -S libreoffice-still
```

### Torrent, Clipgrap install

```bash
pacman -S transmission-cli clipgrab
```

### PostgreSQL install

```bash
pacman -S postgresql
```

## Continue installation

### Choose video driver

```bash
pacman -S xf86-video-fbdev
pacman -S xf86-video-intel
pacman -S xf86-video-amdgpu
pacman -S xf86-video-ati
pacman -S xf86-video-vesa
pacman -S xf86-video-nouveau
```

```bash
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

```bash
vim /etc/gdm/custom.conf
```
```python
...
# Uncomment the line below to force the login screen to use Xorg
WaylandEnable=false
...
```

### Install navigation

1. Open Settings
2. Keyboard
3. Keyboard Shourtcuts - View and Customize Shourts
4. Navigation:
- Move window one workspace to the left **Shift+Super+H**
- Move window one workspace to the right **Shift+Super+L**
- Move window to last workspace **Shift+Super+.**
- Switch applications **Super+J**
- Switch to last workspace **Super+.**
- Switch to workspace 1 **Super+1**
- Switch to workspace 2 **Super+2**
- Switch to workspace 3 **Super+3**
- Switch to workspace 4 **Super+4**
- Switch to workspace on the left **Super+H**
- Switch to workspace on the right **Super+L**
5. Windows:
- Close window **Shift+Super+Q**
- Hide window **Alt+Super+H**
- Maximaze window **Ctrl+Super+K**
- Restore window **Ctrl+Super+J**
- View split on left **Ctrl+Super+H**
- View split on right **Ctrl+Super+L**
6. System:
- Lock screen **Shift+Ctrl+Super+L**
7. Sound and Media:
- Next track **Super+.**
- Play (or play/pause) **Super+/**
- Previous track **Super+,**
- Volume down **Alt+Super+J**
- Volume up **Alt+Super+K**

### Multitasking

1. Open Settings
2. Multitasking
3. Multi-Monitor
4. Workspaces on primary display only

## Cromium install
```bash
pacman -S chromium
```
Install extantions:

1. Json formatter
2. Vimium

Change search engine:

1. Settings
2. Search engine
3. Search engine used in the address bar. Learn more DuckDuckGo

## Users and services

```bash
useradd -m -G users -s /bin/bash user
useradd -m -G users,wheel,adbusers,video -s /bin/bash admin
passwd user
passwd admin
```

## Reboot remote computer

```bash
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

On the remote machine

```bash
systemctl start gdm.service
systemctl enable gdm.service
```

## Ohmyzsh
```bash
sudo pacman -S zsh zsh-completions
chsh -s /bin/zsh
sh -c "$(wget -O- https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
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
sudo pacman -S cups cups-pdf nss-mdns
sudo usermod -aG lpadmin admin
sudo systemctl start cups.service
sudo systemctl enable cups.service
```

[Drivers](https://wiki.archlinux.org/index.php/CUPS/Printer-specific_problems)

### Network connection to usb printer

```bash
sudo systemctl start avahi-daemon.service
sudo systemctl enable avahi-daemon.service
sudo vim /etc/nsswitch.conf
```

```python
hosts: mymachines mdns_minimal [NOTFOUND=return] resolve [!UNAVAIL=return] files myhostname dns
```

```bash
sudo systemctl restart cups.service
```

Open browser with address `localhost:631`


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

Syncthing all directories and files

```bash
rsync -r source/. destination
```

Format fat32

```bash
mkfs.fat -F 32 /dev/sdX1
```

Install iso file to device

```bash
dd bs=4M if=path/to/archlinux.iso of=/dev/sdX status=progress oflag=sync
```

Fix ext4 and ntfs devices

```bash
sudo fsck.ext4 /dev/sdX1
sudo ntfsfix /dev/sdX1
```

Show device UUID

```bash
sudo blkid | grep sdX
```

Make package from AUR

```bash
makepkg -si
```

Regenerate ssh-key for specified host

```bash
ssh-keygen -R <host>
```

Clear pacman cache

```bash
pacman -Scc
```

Update archlinux-keyring

```bash
pacman -Sy archlinux-keyring
```

Permissions for user folder

```bash
sudo chown -R user:user /home/user
```

Copy folder without overwriting an exists files

```bash
cp -r -n Downloads Downloads1
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
