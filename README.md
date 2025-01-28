# Arch Linux installation

## Links
https://www.learnlinux.tv/how-to-install-arch-linux-a-comprehensive-step-by-step-guide/

iwctl

station wlan0 get-networks

iwctl --passphrase <passphrase> station <interface> connect NetworkName

## Create partitions:

fdisk

## Format the partition

### boot efi partition
mkfs.fat -F32 /dev/nvme0n1p2

### format ext4 partition
mkfs.ext4 /dev/nvme0n1p1 # boot
mkfs.ext4 /dev/nvme0n1p3 # /
mkfs.ext4 /dev/nvme0n1p4 # /home

### mount the partition and install the base system

mkdir -p /mnt/boot/efi
mkdir -p /mnt/home

mount /dev/nvme0n1p3 /mnt
mount /dev/nvme0n1p1 /mnt/boot
mount /dev/nvme0n1p4 /mnt/home


## Preparing the Arch installation environment

Now that we have our partitions mounted, let’s install the set of base packages for Arch:

pacstrap -i /mnt base
genfstab -U -p /mnt >> /mnt/etc/fstab

### Change the default working folder
arch-chroot /mnt

## User accounts

To protect the root account, we can set a password for it:

passwd

It would also be a good idea to create a user for yourself.

useradd -m -g users -G wheel hoangthanh28

Then, set a password for your account:

passwd hoangthanh28

## Install basic packages
pacman -S base-devel grub efibootmgr nvim networkmanager sudo linux linux-headers linux-firmware mesa sway swaylock swayidle swaybg git curl pipewire wireplumber nwg-look firefox greetd flameshot copyq sof-firmware xdg-desktop-portal xdg-desktop-portal-wlr xdg-desktop-portal-gtk grim ydotoold

mkinitcpio -p linux

nano /etc/locale.gen

Edit /etc/locale.gen and uncomment en_US.UTF-8:

locale-gen

### Set timezone

ln -sf /usr/share/zoneinfo/Asia/Ho_Chi_Minh /etc/localtime

### Mount efi

mount /dev/nvme0n1p2 /boot/efi

grub-install --target=x86_64-efi --bootloader-id=GRUB --boot-directory=/boot/efi --recheck

cp /usr/share/locale/en\@quot/LC_MESSAGES/grub.mo /boot/grub/locale/en.mo

grub-mkconfig -o /boot/grub/grub.cfg

### Modified the greetd service
nvim /etc/greetd/config.toml

systemctl enable greetd

systemctl enable NetworkManager

systemctl --user enable pipewire wireplumber