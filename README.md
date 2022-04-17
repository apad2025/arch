# Instructions for installing Arch Linux
## IMPORTANT!
This guide assumes you are using UEFI to boot Arch. If you are using a legacy or BIOS system, find a different guide.
## Disk Partitioning and formatting
Run
```sh
$ cfdisk /dev/yourdisk
```  
Locate the partition of the disk on which you want to install Arch and delete it unless it says free space  
Create a new 128M sized partition and select EFI System as its type  
Finally, create a new partition from the remainder of the free space  

Run  
```sh
$ mkfs.fat -F 32 /dev/yourEFI
$ mkfs.ext4 /dev/root_partition
```

## Mounting
Run
```sh
$ mount /dev/root_partition /mnt
$ mount --mkdir /dev/efi_system_partition /mnt/boot
```

## Package Installation
Run
```sh
$ pacstrap /mnt base base-devel linux linux-firmware vim
```

## Configuring Fstab
Run
```sh
$ genfstab -U /mnt >> /mnt/etc/fstab
```
Please note that we used the -U argument while generating the fstab. This is because the argument uses the UUID of the disk of the partition rather than /dev/sdx. The problem with using /dev/sdx is that it is not always a permanent label, while UUIDs do not change

>If your machine has more than one SATA, SCSI or IDE disk controller, the order in which their corresponding device nodes are added is arbitrary. This may result in device names like /dev/sda and /dev/sdb switching around on each boot, culminating in an unbootable system, kernel panic, or a block device disappearing. Persistent naming solves these issues.

-Sourced from The Arch Wiki

## Chrooting
Run
```sh
$ arch-chroot /mnt /bin/bash
```

## Bootloader
Run
```sh
$ pacman -S networkmanager grub
$ systemctl enable NetworkManager
$ grub-install /dev/yourdisk
```
If you get the error "grub-install: error: cannot find EFI directory." then you’re in for a treat. 

Mount the EFI partition with
```sh
$ mount /dev/yourpartition /boot/EFI/
```
then
```sh
$ pacman -S efibootmgr
```
finally
```sh
$ grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
```

## Configuring Grub
Run
```sh
$ vim /etc/default/grub
```
and uncomment GRUB_DISABLE_OS_PROBER=false
then run
```sh
$ grub-mkconfig -o /boot/grub/grub.cfg
```

## Setting Hostname and Password
Run
```sh
$ passwd
```
Then enter and confirm a password of your choice
then run
```sh
$ vim /etc/hostname
```
and set and save your hostname

## Configuring Locale
Run
```sh
$ vim /etc/locale.gen
```
Uncomment the languages you want, for this tutorial I will be uncommenting en_US.UTF-8 UTF-8 and en_US ISO-8859-1
now run
```sh
$ locale-gen
```
finally, run
```sh
$ vim /etc/locale.conf
```
and type LANG=en-US.UTF-8 or whatever UTF you previously uncommented

## Final steps
Run
```sh
$ umount -R /mnt
$ reboot
```

## Creating new user and sudo
Run
```sh
$ useradd -m username
$ passwd username
$ pacman -S sudo
$ EDITOR=vim visudo
```

In vim, type  
```sh
username ALL=(ALL:ALL) ALL
```
directly under the root ALL=(ALL:ALL) ALL line

## Installing KDE deksop
Run
```sh
$ pacman -S xorg plasma plasma-wayland-session kde-applications
```
then smash enter a bunch and wait a while
after that's done run
```sh
$ systemctl enable sddm.service
$ systemctl enable NetworkManager.service
$ reboot
```

## Installing Chrome (because we know you don't want to use Firefox)
Run
```sh
$ sudo pacman -S --needed base-devel git
$ git clone https://aur.archlinux.org/yay-git.git
$ cd yay-git/
$ makepkg -si
$ yay -S google-chrome
```
when you decide to update Chrome run
```sh
$ yay -Syu
```

## Disable mouse acceleration
Go to settings, input devices, mouse, and set acceleration profile to flat

# Finished
Congratulations, you have now successfully installed Arch Linux! Pat yourself on the back, you've just accomplished a rewarding but time consuming achievement.
