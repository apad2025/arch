# Instructions for installing Arch Linux
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
Please note that we used the -U argument while generating the fstab. This is because the argument uses the UUID of the disk and partition rather than /dev/sdx. The problem with using /dev/sdx is that it is not always a permanent label, while UUIDs do not change

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
