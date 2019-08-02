# Creating the live USB
- Download [rufus](https://rufus.ie/ "rufus") and [Manjaro XFCE](http:/https://manjaro.org/download/xfce/ "Manjaro XFCE")
- Create the bootable usb drive using rufus. A 4GB usb drive is enough. When asked, select dd for the copy setting.

# Booting
- Change the keytable to your language (pt, in my case). This won't change the language in grub, but it will once you boot
- Press e over the boot option, in order to change the kernel flags (in order to skip hardware detection and to not load the free nouveau drivers):
  - Change `nouveau.modeset=1` to `nouveau.modeset=0`
  - Add `systemd.mask=mhwd-live.service` after `quiet`
 
# Installing
- Before starting the instalation, open the file `/lib/calamares/modules/mhwdcfg/main.py` with root (`sudo nano /lib/calamares/modules/mhwdcfg/main.py`), scroll all the way down to the bottom to find the run() function, add # before the return line, and add `return None`.  This stops the installer from trying to run mhwd, which freezes the installer.
- Start the instalation, carefully setting up the partitioning.
- Wait for the instalation to finish.

# Post Installation
- Instal the necessary packages for the drivers to work (**MOVE THE MOUSE FREQUENTLY, DONT LET THE COMPUTER SUSPEND DURING THIS STEP**):
  - `sudo pacman -Syu nvidia-utils lib32-nvidia-utils virtualgl lib32-virtualgl mesa mesa-demos bumblebee`
- Instal the linux headers for your kernel version. For example:
  - `sudo pacman -S linux419-headers`
- Add yourself to the bumblebee group:
  - `sudo gpasswd -a yourusername bumblebee`
- Enable the bumblebee daemon:

    sudo systemctl enable bumblebeed.service 
    sudo systemctl enable bumblebeed
    
- Edit the grub file (`sudo nano /etc/default/grub`) and add `acpi_osi=! acpi_osi=\"Windows 2009\"` after `quiet` in the line `GRUB_CMDLINE_LINUX_DEFAULT="quiet"`
- Run `sudo update-grub`
- Reboot
- Install the nvidia-intel-bumblebee driver:
  - `sudo mhwd -i pci video-hybrid-intel-nvidia-bumblebee`
- Reboot again

# Checking Drivers
To check that the drivers are working properly, we can:
- Run `systemctl status bumblebeed`
  - The output should state that the daemon is running
- Run `optirun nvidia-smi`
  - The output should show what is currently running on the graphics card
- Run `optirun glxgears`
  - A window with gears should appear. After 5 seconds, the terminal should show an fps counter (mine was little over 2000). You can close the window after that.
- Run `optirun -b none nvidia-settings -c :8`
  - The nvidia control panel should open

If everything is working properly, then you should have a stable system running. Note that everything will use the cpu by default, unless you specifically run the command with optirun. 

# Installing yay
Optionally, you can install yay to have access to the AUR:

    sudo pacman -S gcc make binutils git
    git clone https://aur.archlinux.org/yay.git
    cd yay
    makepkg -si
    cd ..
    rm -rf yay

Quick yay reference guide (most used commands):
- `yay`: fully update and upgrade the system (equivalent to `sudo pacman -Syu`, if you did not install yay)
- `yay -S name`: install a package from either the repos or the arch user repositories (equivalent to `sudo pacman -S name`, if you did not install yay, although pacman will not install from the AUR)
- `yay -Rns name`: uninstall a package and all the freed dependencies (equivalent to `sudo pacman -Rns name`, if you did not install yay)


# Touchpad Natural Scrolling
By default, the touchpad two-finger scrolling feels weird (reverse from what I'm used too, at least). Manjaro uses libinput to handle input, so you can fix this easily:
- `sudo nano /usr/share/X11/xorg.conf.d/40-libinput.conf`
- Add `Option "Natural Scrolling" "true"` under the touchpad section (not pointers)
