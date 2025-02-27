## **Команды, использованные в этом руководстве:**

**Установка Arch Linux:**

1. **Подготовка диска:**

   - Откройте «Управление дисками» (Windows):
     - Используйте утилиту `diskmgmt` для меток на диске.
     - Пример: `disk0 TOM: (C:/)`

2. **Скачайте ISO-образ Arch Linux:**

   - Перейдите на [страницу скачивания Arch Linux](https://archlinux.org).

3. **Подготовьте загрузочную флешку:**

   - Используйте Ventoy или Rufus (если используете Windows) для создания загрузочной флешки.

4. **Загрузитесь с BIOS:**
   - Загрузитесь в Arch Linux с вашего USB-накопителя.

---

**Установка Arch Linux:**

1. **Настройка больших шрифтов для ISO:**

   ```bash
   setfont ter-132n
   ```

2. **Подключение к Wi-Fi (если используете iwd):**

   - Список устройств:
     ```bash
     device list
     ```
   - Поиск сетей:
     ```bash
     station wlan0 get-networks
     ```
   - Подключение к Wi-Fi:
     ```bash
     station wlan0 connect <your_wifi>
     ```
     - Введите пароль Wi-Fi, когда будет предложено:
       ```bash
       Passphrase: <your_password>
       ```

3. **Проверка сетевого соединения:**

   ```bash
   ping google.com
   ```

4. **Проверка меток дисков:**

   ```bash
   lsblk
   ```

   Пример:

   ```
   labelRoot
     |_ label1    size
     |_ label2    size
     |_ label3    size
   ```

5. **Разметка диска с помощью `cfdisk`:**

   - Используйте `cfdisk` для разметки диска:
     ```bash
     cfdisk /dev/labelRoot
     ```

6. **Создание разделов в `cfdisk`:**

   - Раздел EFI (1 ГБ)
   - Раздел подкачки (4 ГБ или больше)
   - Раздел для Linux (по умолчанию)

7. **Форматирование разделов:**

   - Форматирование раздела EFI:
     ```bash
     mkfs.fat -F32 /dev/label-efi
     ```
   - Форматирование раздела Linux:
     ```bash
     mkfs.ext4 /dev/label-linuxFileSystem
     ```
   - Создание раздела подкачки:
     ```bash
     mkswap /dev/label-swap
     ```

8. **Монтирование разделов:**

   ```bash
   mount /dev/label-linux-FileSystem /mnt
   mount --mkdir /dev/label-efi /mnt/boot
   swapon /dev/label-swap
   ```

9. **Установка Arch с помощью `pacstrap`:**

   ```bash
   pacstrap -i /mnt base base-devel linux linux-firmware git sudo neofetch htop xf86-video-amdgpu mesa lib32-mesa vulkan-radeon lib32-vulkan-radeon amd-ucode opencl-mesa radeontop tlp tlp-rdw nvim vim nano networkmanager
   ```

10. **Генерация fstab:**

    ```bash
    genfstab -U /mnt >> /mnt/etc/fstab
    ```

    Проверьте файл fstab, чтобы убедиться, что всё правильно.

11. **Вход в Arch через chroot:**

    ```bash
    arch-chroot /mnt
    ```

12. **Проверка системной информации с помощью `neofetch`:**

    ```bash
    neofetch
    ```

13. **Установка пароля для root:**

    ```bash
    passwd
    ```

14. **Добавление нового пользователя:**

    ```bash
    useradd -m -g users -G wheel,storage,power,video,audio -s /bin/bash <username>
    ```

    Пример:

    ```bash
    useradd -m -g users -G wheel,storage,power,video,audio -s /bin/bash maaru
    ```

15. **Установка пароля для нового пользователя:**

    ```bash
    passwd <username>
    ```

    Пример:

    ```bash
    passwd maaru
    ```

16. **Предоставление прав sudo пользователю:**

    ```bash
    EDITOR=nvim visudo
    ```

    Раскомментируйте следующую строку:

    ```bash
    %wheel ALL=(ALL:ALL) ALL
    ```

    Проверьте права sudo:

    ```bash
    su - <username>
    ```

17. **Настройка часового пояса:**

    ```bash
    ln -sf /usr/share/zoneinfo/<Region>/<City> /etc/localtime
    hwclock --systohc
    ```

    Пример:

    ```bash
    ln -sf /usr/share/zoneinfo/Asia/Bishkek /etc/localtime
    hwclock --systohc
    ```

18. **Настройка локали (язык и раскладка клавиатуры):**

    - Отредактируйте файл `/etc/locale.gen`, чтобы активировать нужные локали.
      Пример:

      ```bash
      ru_RU.UTF-8 UTF-8
      en_US.UTF-8 UTF-8
      ```

    - Генерация локалей:
      ```bash
      locale-gen
      ```

19. **Установка локали по умолчанию:**

    - Отредактируйте файл `/etc/locale.conf`:
      ```bash
      LANG=en_US.UTF-8
      ```

20. **Настройка имени хоста:**

    - Отредактируйте файл `/etc/hostname`:
      ```bash
      Anjel
      ```

21. **Настройка файла hosts:**

    - Отредактируйте файл `/etc/hosts`:
      ```bash
      127.0.0.1    localhost
      ::1          localhost
      127.0.1.1    Anjel.localdomain    Anjel
      ```

22. **Установка и настройка загрузчика GRUB:**

    ```bash
    pacman -Syu grub efibootmgr dosfstools mtools
    grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
    grub-mkconfig -o /boot/grub/grub.cfg
    ```

23. **Включение NetworkManager:**

    ```bash
    systemctl enable NetworkManager
    ```

24. **Отмонтирование и выход из chroot:**

    ```bash
    exit
    umount -lR /mnt
    ```

25. **Перезагрузка системы:**
    ```bash
    reboot
    ```

---

**Добавление Windows в меню GRUB:**

1. Установите `os-prober`:

   ```bash
   sudo pacman -Sy os-prober
   ```

2. Отредактируйте конфигурацию GRUB:

   ```bash
   sudo nvim /etc/default/grub
   ```

   Установите:

   ```bash
   GRUB_TIMEOUT=20
   ```

   Раскомментируйте:

   ```bash
   GRUB_DISABLE_OS_PROBER=false
   ```

3. Перекомпилируйте GRUB:
   ```bash
   grub-mkconfig -o /boot/grub/grub.cfg
   ```

---

# Если os-prober не видит Windows

- Чтобы найти BIOS-раздел:

  ```bash
  sudo fdisk -l
  ```

- Чтобы получить UUID BIOS-раздела:

  ```bash
  sudo blkid /dev/sda1
  ```

- Отредактировать файл GRUB:

  ```bash
  sudo nano /etc/grub.d/40_custom
  ```

- Код для меню Windows:

  ```bash
  menuentry "Windows" --class windows --class os {
      search --fs-uuid --no-floppy --set=root UUID_Here
      chainloader (${root})/EFI/Microsoft/Boot/bootmgfw.efi
  }
  ```

- Обновить GRUB:
  ```bash
  sudo update-grub
  ```

Если ОС не была найдена, возможно, нужно обновить конфигурацию GRUB после внесения изменений.
