# Arch-Linux-Install-on-Lenovo-Thinkpad-T420s

Specs:

Device: Lenovo Thinkpad T420s
Processor: Intel(R) Core(TM) i7-2640M CPU @ 2.80GHz 2.80 GHz
Ram: 8.00GB
System type: 64-bit operating system, x64-based processor
GPU: Intel(R) HD Graphics 3000 and NVIDIA NVS 4200M
SSD: SAMSUNG SSD 128GB

1. ls /sys/firmware/efi/efivars

2. fdisk -l

3. fdisk /dev/sda 

4. Verify partitions using the fdisk -l command. We now have three partitions.

	- /dev/sda1 - /efi
	- /dev/sda2 - /swap
	- /dev/sda3 - /(root)
	
5. Create filesystem

	- mkfs.fat -F32 /dev/sda1
	- mkfs.ext4 /dev/sda3
	- mkswap /dev/sda2

6. Mount partitions

	- mount /dev/sda3 /mnt
	- mkdir /mnt/efi
	- mount /dev/sda1 /mnt/efi
	- swapon /dev/sda2

7. Select mirrors

	- cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.backup
	- reflector --verbose --latest 10 --sort rate --save /etc/pacman.d/mirrorlist
	
8. Install Arch Linux Base System

	- pacstrap /mnt/ base base-devel linux linux-firmware net-tools networkmanager nano sudo

9. Create fastab

	- genfstab -U /mnt >> /mnt/etc/fstab
	- cat /mnt/etc/fstab (veirfy)*

10. Arch Linux System Configuration
	
	- arch-chroot /mnt

	- nano /etc/locale.gen
		- uncomment: en.US.UTF-8 [UTF-8 for American-English]
	- locale-gen (verify)*
	- echo "LANG=en_US.UTF-8" > /etc/locale.conf
	- ln -sf /usr/share/zoneinfo/America/New_York /etc/localtime
	- hwclock --systohc --utc
	- echo "arch" > /etc/hostname
	- passwd (sets root password)
	- useradd -mg users -G power,wheel,storage -s /bin/bash angus
	- chage -d 0 angus

	- pacman -S sudo
	- pacman -S wpa_supplicant wireless_tools networkmanager network-manager-applet 
	- sudo systemctl enable NetworkManager.service
	- sudo systemctl enable wpa_supplicant.service
	- sudo systemctl start wpa_supplicant.service
	- sudo systemctl start NetworkManager.service
	- nano /etc/sudoers
	- Uncomment this line 
		- ## Uncomment to allow members of group wheel to execute any command
		- ## %wheel ALL=(ALL) ALL
		
	- pacman -S grub efibootmgr dosfstools os-prober mtools
	- mkdir /boot/EFI
	- mount /dev/sda1 /boot/EFI #Mount Fat32 EFI partition
	- grub-install --target=x86=64-efi --bootloader-id=grub_uefi --recheck
	- grub-mkconfig -o /boot/grub/grub.cfg
		
11. Install the X Windwos System on Arch Linux

	- sudo pacman -S xorg xorg-server
	- sudo pacman -S mate mate-extra
	- sudo pacman -S lightdm
	- sudo pacman -S lightdm-gtk-greeter
	- sudo systemctl enable lightdm 

