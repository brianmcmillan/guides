#Raspbian master image creation
##Part 2 - Manual setup
###Image requirements
Uses the image created in Part 1 - Initial configuration
###Copy a master image to a new SD card (Optional)
1. Insert a SD card into your computer
1. Find out the ID of the SD Card
	`diskutil list` => /dev/disk1
1. Unmount the SD Card
	`diskutil unmountDisk /dev/disk1`
1. Copy the image for the card
	`sudo dd bs=4m if=/Users/brianmcmillan/Documents/systemimages/raspberrypi/master/raspbian_base_20151120.img of=/dev/rdisk1`
1. Unmount the SD Card
	`diskutil unmountDisk /dev/disk1`
1. Insert the SD card in the RPi and pug power back in

---


###Manually editing the RPi config file
https://www.raspberrypi.org/documentation/configuration/config-txt.md

1. SSH into the RPi
	`ssh pi@192.168.0.11`  
	The default password is `raspberry`
1. Switch to the root user
`sudo -i`
1. Update the /boot/config.txt file to control how the RPi starts up

```
sudo cat <<EOT > /boot/config.txt
#################################################
## RPi Boot file
#################################################
##
## FILE NAME config.txt
## FILE LOCATION /boot/
## SCM LOCATION <path to git location>
## MAINTAINER <brian.mcmillan.architect@gmail.com>
## CREATE DATE <2015-12-02>
## LAST MODIFIED <2015-12-12>
## INFO https://mmonit.com/monit/documentation/monit.html
#################################################
#
# For more options and information see
# http://www.raspberrypi.org/documentation/configuration/config-txt.md
# Some settings may impact device functionality. See link above for details
# http://linuxonflash.blogspot.com/2015/02/a-look-at-raspberry-pi-2-performance.html
# NOTE: Because this is a headless server, the parameters 

# Boot settings
gpu_mem=016
start_x=0
disable_splash=1
boot_delay=1 #helps to avoid sdcard corruption when force_turbo is enabled.

# Overclock settings
arm_freq=1000  #700 default
core_freq=500  #250 default
sdram_freq=500 #400 default, if unstable, try 483
over_voltage=0 #0 default, must be less than 6
over_voltage_sdram_p=0
over_voltage_sdram_i=0
over_voltage_sdram_c=0
temp_limit=80 #Will throttle to default clock speed if hit.

# Interface settings
dtparam=audio=off
dtparam=i2c_arm=off
dtparam=i2s=off
dtparam=spi=off

EOT
```

###Update locale configuration
1. Update the timezone file  
`sudo echo "US/Arizona" >> /etc/timezone`
1. Update the locale file  
`sudo echo "LANG=en_US.UTF-8" >> /etc/default/locale`

###Restart the system
1. Send the reboot command
`reboot`

##Backing up the SD Card / Create the master image 
1. With power off to the RPi, remove the SD card.
1. Insert a SD card into your computer
1. Open a terminal window
1. Find out the ID of the SD Card
`diskutil list` => /dev/disk1
1. Copy the image on the card to the computer
`sudo dd bs=4m if=/dev/rdisk1 of=/Users/brianmcmillan/Documents/systemimages/raspberrypi/master/raspbian_lite_02.img`
1. Unmount the SD Card
`diskutil unmountDisk /dev/disk1`

This will take some time (10-30 min.) and if you are impatient, check the status with `[CTRL] + t`.

This image can be used to bootstrap a RPi without having to reconfigure an image from scratch.

