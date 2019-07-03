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

# TODO: FINISH
