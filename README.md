# Установка Arch Linux с полнодисковым шифрованием

## Разметка
```
cfdisk
```
#### /dev/sda1 Раздел /boot 512M Type EFI System
#### /dev/sda2 Раздел / 100% Type Linux (default)

## Шифрование /
```
cryptsetup LuksFormat /dev/sda2
cryptsetup open /dev/sda2 root
```
## Создание файловой системы
```
mkfs.fat -F32 /dev/sda1
mkfs.ext4 /dev/mapper/root
```
## Монтирование разделов в /mnt
```
mount /dev/mapper/root /mnt
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot
```
## Установка
```
pacstrap /mnt base base-devel linux linux-firmware networkmanager vim fish
```
## Fstab
```
genfstab -U /mnt >> /mnt/etc/fstab
```
## Chroot
```
arch-chroot /mnt
```
## Добавить в HOOKS - encrypt
```
vim /etc/mkinitcpio.conf 
```
## Пересобрать ядро
```
mkinitcpio -p linux
```
## конфигурация systemd-boot
```
bootctl --path=/boot install
```
## loader.conf
```
cd /boot/loader
vim loader.conf
```
```
timeout 5
default arch
console-mode max
editor no
```
## arch.conf
```
vim /boot/loader/entries/arch.conf
```

```
title   Arch Linux
linux   /vmlinuz-linux
initrd  /initramfs-linux.img
options cryptdevice=UUID={uuid}:root root=/dev/mapper/root rw
```
## Passwd root
```
passwd root
```
## Создание пользователя
```
useradd -m -G wheel,audio -s /bin/fish {username}
```
## NetworkManager
```
systemctl enable NetworkManager
```
## Hostname
```
hostnamectl set-hostname {hostname}
```
## Exit,Reboot
```
exit
reboot
```
## sudo
```
usermod -aG sudo {username}
```
## Xorg/i3
```
sudo pacman -Sy xorg i3 lightdm lightdm-gtk-greeter
sudo systemctl enable lightdm
```

