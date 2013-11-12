# Fix Backlight Display Brightness in Linux

Some workarounds to adjust the backlight brightness using the function left/right keys on my Acer laptop. 

Currently using elementaryOS, based on Ubuntu LTS(12.04).

Edit Grub
===

You can fix the issue by editing /etc/default/grub and adding:
* acpi_osi=Linux acpi_backlight=vendor
to
* GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
resulting in:
* GRUB_CMDLINE_LINUX_DEFAULT="quiet splash acpi_osi=Linux acpi_backlight=vendor"

Then, save and close the /etc/default/grub file and update the grub:
* "sudo update-grub"

Script that stuff
===

What you can do is to use a manual override that , by modifying /etc/rc.local as follows:

```shell
#!/bin/sh -e
#
# rc.local
#
# This script is executed at the end of each multiuser runlevel.
# Make sure that the script will "exit 0" on success or any other
# value on error.
#
# In order to enable or disable this script just change the execution
# bits(chmod).
#
# By default this script does nothing.
echo 978 > /sys/class/backlight/intel_backlight/brightness
chmod 777 /sys/class/backlight/intel_backlight/brightness
exit 0
```

The downside is that you can't change the brightness easily except by manually modifying the file /sys/class/backlight/intel_backlight/brightness

What do?

Use the brightchg.sh script to map the function keys and change programmatically the brightness of the screen, using custom keys on your keyboard.

```shell
#!/bin/bash
# Acer Travelmate P253-M brightness control workaround
# Note: add the following to /etc/rc.local
#       chmod 777 /sys/class/backlight/intel_backlight/brightness
# For convenience, assign whatever keys you want to run this script
# Fine tune the bump parameter as required
#
#488 Lowest with backlight on
#976
#1464
#1952
#2440
#2928
#3416
#3904
#4392
#4880 Brightest
#
# Usage:
#    ./brightchg.sh up   # bump up brightness
#    ./brightchg.sh down # bump down brightness
#
curr=`cat /sys/class/backlight/intel_backlight/brightness`
bump=244
if [ "$1" == "up" ]; then
  curr=`echo "$curr + $bump" | bc`
else
  curr=`echo "$curr - $bump" | bc`
fi
echo $curr | tee /sys/class/backlight/intel_backlight/brightness'
```
