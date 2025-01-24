**Commands used in this tutorial:**

---

**Installing Arch Linux:**

1. **Prepare your disk:**

   - Open Disk Management (Windows):
     - Use `diskmgmt` utility to label your disk.
     - Example: `disk0 TOM: (C:/)`

2. **Download Arch Linux ISO:**

   - Go to [Arch Linux download page](https://archlinux.org).

3. **Prepare the bootable USB:**

   - Use Ventoy or Rufus (if using Windows) to create a bootable USB drive.

4. **Boot from BIOS:**
   - Boot into the Arch Linux image from your USB.

---

**Building Arch Linux:**

1. **Set big fonts for the ISO:**

   ```bash
   setfont ter-132n
   ```

2. **Connect to Wi-Fi (if using iwd):**

   - List devices:
     ```bash
     device list
     ```
   - Get networks:
     ```bash
     station wlan0 get-networks
     ```
   - Connect to your Wi-Fi:
     ```bash
     station wlan0 connect <your_wifi>
     ```
     - Enter the Wi-Fi password when prompted:
       ```bash
       Passphrase: <your_password>
       ```

3. **Check network connectivity:**

   ```bash
   ping google.com
   ```

4. **Check disk labels:**

   ```bash
   lsblk
   ```

   Example:

   ```
   labelRoot
     |_ label1    size
     |_ label2    size
     |_ label3    size
   ```

5. **Partition your disk with `cfdisk`:**

   - Use `cfdisk` to label the disk:
     ```bash
     cfdisk /dev/labelRoot
     ```

6. **Create partitions in `cfdisk`:**

   - EFI partition (1GB)
   - Swap partition (4GB or more)
   - Linux filesystem (default size)

7. **Format the partitions:**

   - Format EFI partition:
     ```bash
     mkfs.fat -F32 /dev/label-efi
     ```
   - Format Linux filesystem partition:
     ```bash
     mkfs.ext4 /dev/label-linuxFileSystem
     ```
   - Create swap:
     ```bash
     mkswap /dev/label-swap
     ```

8. **Mount the partitions:**

   ```bash
   mount /dev/label-linux-FileSystem /mnt
   mount --mkdir /dev/label-efi /mnt/boot
   swapon /dev/label-swap
   ```

9. **Install Arch using `pacstrap`:**

   ```bash
   pacstrap -i /mnt base base-devel linux linux-firmware git sudo neofetch htop xf86-video-amdgpu mesa lib32-mesa vulkan-radeon lib32-vulkan-radeon amd-ucode opencl-mesa radeontop tlp tlp-rdw nvim vim nano networkmanager
   ```

10. **Generate fstab:**

    ```bash
    genfstab -U /mnt >> /mnt/etc/fstab
    ```

    Check the fstab file to ensure it's correct.

11. **Enter Arch chroot:**

    ```bash
    arch-chroot /mnt
    ```

12. **Check system info with `neofetch`:**

    ```bash
    neofetch
    ```

13. **Set root password:**

    ```bash
    passwd
    ```

14. **Add a new user:**

    ```bash
    useradd -m -g users -G wheel,storage,power,video,audio -s /bin/bash <username>
    ```

    Example:

    ```bash
    useradd -m -g users -G wheel,storage,power,video,audio -s /bin/bash maaru
    ```

15. **Set password for the new user:**

    ```bash
    passwd <username>
    ```

    Example:

    ```bash
    passwd maaru
    ```

16. **Grant sudo access to the user:**

    ```bash
    EDITOR=nvim visudo
    ```

    Uncomment the following line:

    ```bash
    %wheel ALL=(ALL:ALL) ALL
    ```

    Verify sudo access:

    ```bash
    su - <username>
    ```

17. **Set the time zone:**

    ```bash
    ln -sf /usr/share/zoneinfo/<Region>/<City> /etc/localtime
    hwclock --systohc
    ```

    Example:

    ```bash
    ln -sf /usr/share/zoneinfo/Asia/Bishkek /etc/localtime
    hwclock --systohc
    ```

18. **Set your locale (language and keyboard layout):**

    - Edit `/etc/locale.gen` to enable your locales.
      Example:

      ```bash
      ru_RU.UTF-8 UTF-8
      en_US.UTF-8 UTF-8
      ```

    - Generate locales:
      ```bash
      locale-gen
      ```

19. **Set the default locale:**

    - Edit `/etc/locale.conf`:
      ```bash
      LANG=en_US.UTF-8
      ```

20. **Set the hostname:**

    - Edit `/etc/hostname`:
      ```bash
      Anjel
      ```

21. **Configure hosts file:**

    - Edit `/etc/hosts`:
      ```bash
      127.0.0.1    localhost
      ::1          localhost
      127.0.1.1    Anjel.localdomain    Anjel
      ```

22. **Install and configure GRUB bootloader:**

    ```bash
    pacman -Syu grub efibootmgr dosfstools mtools
    grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
    grub-mkconfig -o /boot/grub/grub.cfg
    ```

23. **Enable NetworkManager:**

    ```bash
    systemctl enable NetworkManager
    ```

24. **Unmount and exit chroot:**

    ```bash
    exit
    umount -lR /mnt
    ```

25. **Reboot the system:**
    ```bash
    reboot
    ```

---

**Adding Windows Entry to GRUB:**

1. Install `os-prober`:

   ```bash
   sudo pacman -Sy os-prober
   ```

2. Edit the GRUB configuration:

   ```bash
   sudo nvim /etc/default/grub
   ```

   Set:

   ```bash
   GRUB_TIMEOUT=20
   ```

   Uncomment:

   ```bash
   GRUB_DISABLE_OS_PROBER=false
   ```

3. Recompile GRUB:
   ```bash
   grub-mkconfig -o /boot/grub/grub.cfg
   ```

---

# if os-prober no Detecting

- To find BIOS boot partition:

  ```bash
  sudo fdisk -l
  ```

- To get the UUID of the BIOS boot partition:

  ```bash
  sudo blkid /dev/sda1
  ```

- Edit the GRUB file:

  ```bash
  sudo nano /etc/grub.d/40_custom
  ```

- Windows menu entry code:

  ```bash
  menuentry "Windows" --class windows --class os {
      search --fs-uuid --no-floppy --set=root UUID_Here
      chainloader (${root})/EFI/Microsoft/Boot/bootmgfw.efi
  }
  ```

- Update GRUB:
  ```bash
  sudo update-grub
  ```

If the OS is not found, you might need to update the GRUB configuration after making changes.
