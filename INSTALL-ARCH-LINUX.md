# Installing Arch linux with EFI

1. Internet connection with Wi-Fi or just connect an ethernet cable:
    - `ip link` (All the network interfaces of your pc will be displayed, among them there should be one called "wlan0".)

2. Activate the device to be able to use it:
    - `ip link set wlan0 up`

3. Then scan all available Wi-Fi networks:
    - `iwlist wlan0 scan`

4. Connect to a network with the WPA or WPA2 encryption protocol:
    - `wpa_passphrase <network name> <password> > /etc/wifi` 

5. Now we will have to connect to the Wi-Fi network with the configuration file that we have generated:
    - `wpa_supplicant -B -i wlan0 -D wext -c /etc/wifi`
    
6. Set an ip address:
    - `dhclient` (Ready!, Once done we already have an internet connection)

7. Check your internet connection:
    - `ping archlinux.org`

8. Create partition table:
    - `fdisk -l` (It will show the list of connected disks)

9. Once we have identified the hard drive that we are going to use, we will use the command:
    - `cfdisk /dev/nvme0n1` (It will show a utility to create the partitions)

10. Create the following:
    - `/boot` partition:
      - Size `512M`
      - Type `EFI System`
    
    - `/` partition:
      - Size `220GB`
      - Type `Linux filesystem`
     
    - `/home` partition:
      - Size `703G`
      - Type `Linux filesystem`
      
    - `/swap` partition:
      - Size `8G`
      - Type `Linux swap`

11. Format partitions:
    - `/boot -> mkfs.fat -F32 /dev/nvme0n1p1`
    - `/swap -> mkswap /dev/nvme0n1p2`
    - `/     -> mkfs.ext4 /dev/nvme0n1p3`
    - `/home -> mkfs.ext4 /dev/nvme0n1p4`

12. Activate swap area:
    - `swapon`

13. Mount system partition:
    - `/     ->  mount /dev/nvme0n1p3 /mnt`
    - `/boot -> mkdir /mnt/boot/efi; mount /dev/nvme0n1p1 /mnt/boot/efi`
    - `/home -> mkdir /mnt/home; mount /dev/nvme0n1p4 /mnt/home`
    
14. Install Arch linux base packages:
    - `pacstrap /mnt linux linux-firmware base nano os-prober grub networkmanager dhcpcd efibootmgr netctl wpa_supplicant dialog ntfs-3g gvfs gvfs-afc gvfs-mtp`

15. Save partitions:
    - `genfstab /mnt >> /mnt/etc/fstab` (Generate the `/etc/fstab` file)

16. Access the newly installed system:
    - `arch-chroot /mnt`

17. Set the timezone:
    - `ln -sf usr/share/zoneinfo/posix/America/Managua /etc/localtime`

18. Update the Hardware clock:
    - `hwclock --systohc`

19. Set locale:
    - `sed -i 's/#en_US.UTF-8/en_US.UTF-8/g' /etc/locale.gen`
    - `locale-gen`

20. Assign a name to the PC:
    - `echo 1ds4rth > /etc/hostname`
  
21. Install Grub:
    - `grub-install --efi-directory=/boot/efi --bootloader-id='Arch Linux' --target=x86_64-efi`
    
22. Now we can update the GRUB configuration:
    - `grub-mkconfig -o /boot/grub/grub.cfg`

  
