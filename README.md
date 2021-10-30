# Arch-Install
This is a Cheat Sheet for Arch Linux Installation



# Verify the boot mode
```bash
ls /sys/firmware/efi/efivars
```

# Update System Clock
```bash
timedatectl status
timedatectl set-ntp true
timedatectl set-timezone Europe/Athens
```

# Partitioning
```bash
lsblk
cgdisk /dev/the_drive_you_want
```
Boot 	    | Swap 										 | root
----------- |--------------------------------------------| ----
------------|-----------------**Size**-------------------|-----
1G 		   	| Follow the standards from IBM 			 | ***
------------|-----------------**GUID**-------------------|-----
ef00   		| 8200									 	 | 8300

## Format partiotions
* Format Boot Partition
```bash
mkfs.fat -F32 /dev/boot_partition
```
* Format Swap Partition
```bash
mkswap /dev/swap_partition
```
* Format Root Partition
```bash
mkfs.ext4 /dev/root_partition
```
## Mount Partitions
* Mount Root Partition
```bash
mount /dev/root_partition /mnt
```
* Turn on Swap Partition
```bash
swapon /dev/swap_partition
```
* Mount Boot Partition
```bash
mkdir -p /mnt/boot
mount /dev/boot_partition /mnt/boot
```

# Install essential packages
```bash
pacstrap /mnt base linux linux-firmware base-devel vi nano
```

* If you have a wi-fi connection install. **Follow the arch linux installation guide for more**
`iwctl` or `NetworkManager`

# Configure the system
* Verify you've done everything correct before configuring
```bash
genfstab -U /mnt
```
* Gnerate an [fstab](https://wiki.archlinux.org/title/Fstab "Fstab") file
```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

# Chroot
```bash
arch-chroot /mnt
```

## Time Zone
* Set the [time zone](https://wiki.archlinux.org/title/Time_zone "Time zone"):
```bash
ln -sf /usr/share/zoneinfo/_Region_/_City_ /etc/localtime
```
## Run [hwclock(8)](https://man.archlinux.org/man/hwclock.8) to generate `/etc/adjtime`:
```bash
hwclock --systohc
```
## locale-gen
* [Edit](https://wiki.archlinux.org/title/Textedit "Textedit") `/etc/locale.gen` and uncomment `en_US.UTF-8 UTF-8` and other needed [locales](https://wiki.archlinux.org/title/Locale "Locale").
```bash
vi /etc/locale-gen
```
* Generate the locales by running:
```bash
locale-gen
```
* Create a locale.conf file, and set the LANG variable accordingly:
```bash
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

# Network Settings
* Set hostname
```bash
echo "yourhostname" > /etc/hostname
```
* Initramfs

Creating a new _initramfs_ is usually not required, because [mkinitcpio](https://wiki.archlinux.org/title/Mkinitcpio "Mkinitcpio") was run on installation of the [kernel](https://wiki.archlinux.org/title/Kernel "Kernel") package with pacstrap. But it doesn't hurt at all to make sure that everything is installed correctly.
```bash
mkinitcpio -P
```

* Set root password
```bash
passwd
```

# Install Grub
```bash
pacman -Sy grub efibootmgr
```
* grub-install
```bash
grub-install --target=x86_64-efi --efi-directory=/your_mounted_boot_dir --bootloader-id=GRUB
```

* Create Grub Configuration
```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

# Reboot
```bash
exit
shutdown -r now
```
* Cross your fingers
* Say a prayer
* And wait

# Internet Connection
* Start the services systemd-networkd
```bash
systemctl start systemd-networkd
systemctl start systemd-resolved
```
* Check the name of your ethernet device
```bash
networkctl list
```
* Wired adapter using DHCP
```bash
vi /etc/systemd/network/20-wired.network
```
```
[Match]
Name=your_ethernet_device_name

[Network]
DHCP=yes
```
* Restart systemd-networkd
```bash
systemctl restart systemd-networkd
systemctl restart systemd-resolved
```
* Verify you have a connection
```bash
ip addr
ping archlinux.org
```

# Add User
* Add the user based with groups based on your preferences
```bash
useradd -g users -G wheel,storage,power -m your_user
```
* Change user's password
```bash
passwd your_user
```
* Add your user to the sudoers file [Optional]
```bash
visudo
```
Uncomment your preference

# Enable your Services
```bash
systemctl enable systemd-networkd
systemctl enable systemd-resolved
```


## Everything is set-up. Now you need to pick your Desktop Enviroment or your Window Manager


# Install Xorg Packages and Video Drivers
```bash
sudo pacman -S nvidia nvidia-utils nvidia-settings xorg-server xorg-apps xorg-xinit numlockx 
```
# Install Display Manager
```bash
sudo pacman -S lightdm lightdm-gtk-greeter
```
# Install additional fonts
```bash
sudo pacman -S noto-fonts ttf-ubuntu-font-family ttf-dejavu ttf-freefont \
ttf-liberation ttf-droid ttf-inconsolata ttf-roboto terminus-font ttf-font-awesome
```
# Install Sound Drivers and Tools
```bash
sudo pacman -S alsa-utils alsa-plugins alsa-lib pavucontrol
```
# Install i3
```bash
sudo pacman -S i3
```
# Install additional tools for i3 productivity
```bash
sudo pacman -S rxvt-unicode ranger rofi conky dmenu urxvt-perls \
perl-anyevent-i3 perl-json-xs alacritty
```

# Install a browser
```bash
sudo pacman -S firefox
```

# Install AUR
```bash
cd /opt
git clone https://aur.archlinux.org/yay-git.git
cd yay-git
makepkg -si
```

# Reboot

# Testing Sound and Configuration
```bash
aplay -l && \
lspci | grep -i audio && \
ls -l /dev/snd/
alsamixer -c 1
speaker-test -c 2
```
# X Window System Check
```bash
lspci -k | grep -A 2 -E "(VGA|3D)" && \
nvidia-smi && \
nvidia-smi -q -d TEMPERATURE && \
xrandr && \
xrandr --listproviders && \
xdpyinfo | grep dots
```

#  Edit ~/.Xresources
```
vi ~/.Xresources
!--------------------------
! ROFI Color theme
! -------------------------
rofi.color-enabled: true
!rofi.color-window: argb:ee273238, #273238, argb:3a1e2529
rofi.color-window:      #000, #000, #000
rofi.color-normal: argb:00273238, #c1c1c1, argb:3a273238, #394249, #ffffff
rofi.color-active: argb:00273238, #80cbc4, argb:3a273238, #394249, #80cbc4
rofi.color-urgent: argb:00273238, #ff1844, argb:3a273238, #394249, #ff1844
rofi.hide-scrollbar:    true

!---------------------------------
! Xft settings
! --------------------------------
!Xft.dpi:        110
Xft.dpi:        109
Xft.antialias:  true
Xft.rgba:       rgb
Xft.hinting:    true
Xft.hintstyle:  hintslight
Xft.autohint:   false
Xft.lcdfilter:  lcddefault

!---------------------------------
! URXVT Terminal config
! --------------------------------
URxvt.depth:                            32
URxvt*termName:                         screen-256color
URxvt*geometry:                         240x84
URxvt.loginShell:                       true
URxvt*scrollColor:                      #777777
URxvt.scrollStyle:                      rxvt
URxvt*scrollTtyKeypress:        true
URxvt*scrollTtyOutput:          false
URxvt*scrollWithBuffer:         true
URxvt*skipScroll:                       true
URxvt*scrollBar:                        false
URxvt*fading:                           30
URxvt*urgentOnBell:                     false
URxvt*visualBell:                       true
URxvt*mapAlert:                         true
URxvt*mouseWheelScrollPage:     true
URxvt.foreground:                       #eeeeee
URxvt.background:                       #000000
URxvt*colorUL:                          yellow
URxvt*underlineColor:           yellow
URxvt.saveLines:                        65535
URxvt.cursorBlink:                      false
URxvt.utf8:                             true
URxvt.locale:                           true

URxvt.letterSpace:              -1

URxvt.font:             xft:monospace:pixelsize=16:style=regular
URxvt.boldFont:         xft:monospace:pixelsize=14:style=bold

! Perl extensions
URxvt.perl-ext-common:     default,matcher
URxvt.matcher.button:      1
URxvt.urlLauncher:         chromium

URxvt.perl-ext-common:          ...,font-size
URxvt.keysym.C-Up:              perl:font-size:increase
URxvt.keysym.C-Down:            perl:font-size:decrease
URxvt.keysym.C-S-Up:            perl:font-size:incglobal
URxvt.keysym.C-S-Down:          perl:font-size:decglobal

URxvt.keysym.Home: \033[1~
URxvt.keysym.End: \033[4~
URxvt.keysym.KP_Home: \033[1~
URxvt.keysym.KP_End:  \033[4~

! Colors
URxvt*background: #000000
URxvt*foreground: #B2B2B2
! black
URxvt*color0:  #000000
URxvt*color8:  #686868
! red
URxvt*color1:  #B21818
URxvt*color9:  #FF5454
! green
URxvt*color2:  #18B218
URxvt*color10: #54FF54
! yellow
URxvt*color3:  #B26818
URxvt*color11: #FFFF54
! blue
URxvt*color4:  #1818B2
URxvt*color12: #5454FF
! purple
URxvt*color5:  #B218B2
URxvt*color13: #FF54FF
! cyan
URxvt*color6:  #18B2B2
URxvt*color14: #54FFFF
! white
URxvt*color7:  #B2B2B2
URxvt*color15: #FFFFFF
```

# Reload .Xresources for URXVT
```bash
xrdb ~/.Xresources
```

# Enable Start lightdm
```bash
sudo systemctl enable lightdm && \
sudo systemctl start lightdm
```

# Start X WIndow session
```bash
startx
```
