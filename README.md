# Setting Up a Minimal and Space-Efficient Tiny Core Linux Environment with Essential Packages for Browser Use

Tiny Core Linux is a lightweight operating system that loads entirely in RAM, making it fast but volatile. This guide explains how to shift storage from RAM to the Drive, make installed packages persistent, and configure `startx` to launch automatically.

## Table of Contents
1. [Shift Storage from RAM to Drive](#1-shift-storage-from-ram-to-drive)
   - [Mount the Drive](#11-mount-the-drive)
   - [Create a Partition](#12-create-a-partition)
   - [Format the Partition](#13-format-the-partition)
   - [Mount the Partition](#14-mount-the-partition)
2. [Shift Storage from RAM to Drive](#2-shift-storage-from-ram-to-drive)
   - [Set Drive as the Backup Location](#21-set-drive-as-the-backup-location)
   - [Perform Backup](#22-perform-backup)
   - [Automate the Backup](#23-automate-the-backup)
3. [Make Installed Packages Permanent](#3-make-installed-packages-permanent)
   - [Install Python and pip](#31-install-python-and-pip)
   - [Install Packages](#32-install-packages)
   - [Move Packages to Drive](#33-move-packages-to-drive)
   - [Add Packages to OnBoot List](#34-add-packages-to-onboot-list)  
4. [Minimal GUI Setup for Browser Use](#4-minimal-gui-setup-for-browser-use)
   - [Install Minimal GUI Components](#41-install-minimal-gui-components)
   - [Modify Boot Settings for GUI](#42-modify-boot-settings-for-gui)
   - [Test the Setup](#43-test-the-setup)
- [Conclusion](#conclusion)
- [Issues being faced](#issues-being-faced)
- [Some common Tiny Core Linux commands](#some-common-tiny-core-linux-commands)

## 1. Shift Storage from RAM to Drive

### 1.1 Mount the Drive
- Identify your Drive partition:
  ```bash
  fdisk -l

### 1.2 Create a Partition
- Select the drive (e.g., `/dev/sda`):
  ```bash
  sudo fdisk /dev/sda
- Follow the `fdisk` instructions to create a new partition:
  - Press `n` to create a new partition.
  - Press `p` for the primary partition.
  - Choose the partition number and specify its size.
    - Note: Press `Enter` to allocate all available space on the Drive.
  - Press `w` to write the changes and exit.
### 1.3 Format the Partition
- Format the newly created partition (e.g., `/dev/sda1`) with a file system such as ext4:
  ```bash
  sudo mkfs.ext4 /dev/sda1
### 1.4 Mount the Partition
- Mount the partition to a directory (e.g., `/mnt/sda1`):
  ```bash
  sudo mount /dev/sda1 /mnt/sda1

## 2. Shift Storage from RAM to Drive
### 2.1 Set Drive as the Backup Location
- Edit the `/opt/.filetool.lst` to include the files and directories you want to persist across reboots:
  ```bash
  sudo vi /opt/.filetool.lst
- Add directories like `/home` and `/etc`:
  ```bash
  /home
  /etc
- Set the backup device to the Drive:
  ```bash
  sudo vi /opt/.backup_device
### 2.2 Perform Backup
- Save your settings and make the backup manually:
  ```bash
  filetool.sh -b
### 2.3 Automate the Backup
- To automatically back up your data on shutdown, add the following command to `/opt/bootlocal.sh`:
  ```bash
  sudo vi /opt/bootlocal.sh
- Add:
  ```bash
  filetool.sh -b
  
 - Note: If you prefer using `nano` for simplicity, you can install it using the following command:
   ```bash
   tce-load -wi nano
  - Add `/mnt/sda1` as the backup location.
## 3. Make Installed Packages Permanent
### 3.1 Install Python and pip
- Installing Python:
  ```bash
  tce-load -wi python
- Download `get-pip.py`:
  ```bash
  wget https://bootstrap.pypa.io/get-pip.py

- Install pip:
  ```bash
  sudo python get-pip.py

### 3.2 Install Packages
- Use `tce-load` to install essential packages (e.g., a browser):
  ```bash
  tce-load -wi nano
  pip install aiohttp
  pip install schedule
  pip install pymodbus
  pip install flask
  pip install sqlalchemy==1.3.23
  pip install flask_sqlalchemy==2.5.1
  pip install flask-cors
  curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
  tce-load -wi apache2.4.tcz
  tce-load -wi node
  tce-load -wi chromium-browser
### 3.3 Move Packages to Drive
- Move the installed packages to the persistent location:
  ```bash
  sudo mv /etc/sysconfig/tcedir/optional/* /mnt/sda1/tce/optional/
### 3.4 Add Packages to OnBoot List
- Ensure packages load automatically by adding them to the `onboot.lst`:
  ```bash
  sudo vi /mnt/sda1/tce/onboot.lst
- Add the required package names (e.g., `chromium-browser.tcz`).
## 4. Minimal GUI Setup for Browser Use
### 4.1 Install Minimal GUI Components
- Install the graphical server (`Xorg`) and the window manager (`flwm_topside`), which provides the most minimal GUI:
  ```bash
  tce-load -wi Xorg
  tce-load -wi flwm_topside
- Note: If you are using Virtual Box to run Tiny Core Linux, then run the following command:
  ```bash
  tce-load -wi virtualbox-guest-additions
### 4.2 Modify Boot Settings for GUI
- Edit the `/opt/bootlocal.sh` file to start the GUI (Xorg and browser) automatically on boot:
  ```bash
  sudo vi /opt/bootlocal.sh
- Add:
  ```bash
  /usr/bin/startx &
### 4.3 Test the Setup
- Test the Setup:
  ```bash
  sudo reboot
- The system should boot into the minimal GUI, automatically launching the browser.

## Conclusion
By following these steps, you'll have a minimal, space-efficient Tiny Core Linux setup with a partitioned Drive, persistent packages, and a lightweight GUI for browser use.

## Issues being faced
### Browser Not Loading on Boot
Description: After configuring the system to start the browser (e.g., Chromium) on boot by adding the command `chromium-browser` to the `/opt/bootlocal.sh file`, the browser fails to load automatically. Despite the setup in `bootlocal.sh`, the browser does not start as expected during system boot.

### Tiny Core Linux Flashing Issue
Description: When attempting to flash Tiny Core Linux (initially 16 MB) onto an SD card, the flashing process completes, but upon accessing the SD card, an error is displayed and the system prompts to format the disk. The error suggests that the disk might be corrupted or not properly recognized.

## Some common Tiny Core Linux commands
### System Commands
- Check disk space:
  ```bash
  df -h
- Check memory usage:
  ```bash
  free -m
- List mounted drives:
  ```bash
  mount
- Shut down the system:
  ```bash
  sudo poweroff
- Reboot the system:
  ```bash
  sudo reboot
### File Management Commands
- List files in a directory:
  ```bash
  ls
- Copy a file:
  ```bash
  cp source_file destination
- Move/rename a file:
  ```bash
  mv old_name new_name
- Create a directory:
  ```bash
  mkdir new_directory
- Delete a file:
  ```bash
  rm filename
### Package Management Commands
- Search for a package:
  ```bash
  tce
  tce-ab
- Install a package:
  ```bash
  tce-load -wi package_name
- Remove a package:
  ```bash
  tce-remove package_name
### System Configuration
- Edit a file using vi editor:
  ```bash
  vi filename
- Edit a file using nano editor:
  ```bash
  nano filename
- Edit the bootloader:
  ```bash
  sudo nano /opt/bootlocal.sh
### Backup and Restore
- Backup current configuration:
  ```bash
  filetool.sh -b
- Restore configuration:
  ```bash
  filetool.sh -r
