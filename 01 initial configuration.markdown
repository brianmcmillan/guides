#Raspbian master image creation
##Part 1 - Initial configuration
###SD Card requirements
8GB microSD card class 6-10

###Download an image
1. Download and unzip an image  
	**Standard RPi Image Repository:**  
	RASPBIAN JESSIE - https://www.raspberrypi.org/downloads/raspbian/

	**Minimal RPi image:**  
	RASPBIAN JESSIE LITE - https://www.raspberrypi.org/downloads/raspbian/  

	**Docker host image:**  
	http://blog.hypriot.com/downloads/

1. Create a directory to store the downloaded image files  
	e.g., `/Users/brianmcmillan/Documents/systemimages/raspberrypi/src`
1. Move any downloaded images to this directory

###Copy an image to an SD card
1. Insert a SD card into your computer
1. Find out the ID of the SD Card
	`diskutil list` => /dev/disk1
1. Unmount the SD Card
	`diskutil unmountDisk /dev/disk1`
1. Copy the image for the card
	`sudo dd bs=1m if=/Users/brianmcmillan/Documents/systemimages/raspberrypi/src/2015-11-21-raspbian-jessie-lite.img of=/dev/rdisk1`
1. Wait for the process to finish

###Boot up the RPi
1. Plug the SD card into the RPi
1. Plug in the power supply

###Find the RPi on the network
1. Open a terminal window from the mac
1. Run the nmap program  
`nmap 192.168.0.0/24`

Returns: 

```
...
Nmap scan report for 192.168.0.11
Host is up (0.0051s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
...
```
###Log in for the first time
1. SSH into the RPi
	`ssh pi@192.168.0.11`  
	The default password is `raspberry`
Returns:

```
The authenticity of host '192.168.0.11 (192.168.0.11)' can't be established.
ECDSA key fingerprint is SHA256:kZ7NKj8+qFv...euWvgQAoNsZQaT94VQ.
Are you sure you want to continue connecting (yes/no)? yes
```	
1. 	If the connection was successful, select `yes`

####Login error message
If another server has used this IP address before, you will need to remove that server fingerprint from the known hosts list.

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
...
```

1. Remove the old fingerprint with 	
`ssh-keygen -R 192.168.0.11`	

###Expanding the SD card partition
1. SSH into the RPi
	`ssh pi@192.168.0.11`  
	The default password is `raspberry`
1. Start the configuration application  
	`sudo raspi-config`
1. Expand the file system on the card  
	Select menu choice `1. Expand Filesystem`
1. Exit the config. application    
	Use the tab key to select `<Finish>`
1. Would you like to reboot 
	`<YES>`

### See part 2 for anually editing the RPi config file

###Set basic configuration choices from the GUI
1. SSH into the RPi
	`ssh pi@192.168.0.11` 
1. Start the configuration application
	`sudo raspi-config`
1. Update the config. application and RPi  
	Select menu choice `1. Advanced Options`   
	Select menu choice `A0 Update`  
	Wait for the changes to be made   
1. Set the default boot options  
	Select menu choice `1. Boot Options`  
	Select menu choice `B1 Console` 
1. Set the Locale
	Select menu choice `1. Internationalization Options`  
	Select menu choice `I1 Change Locale`  
	Un select any checked locales with the [shift] key  
	Select `[*] en_US.UTF-8 UTF-8` with the [shift] key   
	Select `<OK>`  
	Select `en_US.UTF-8`  
	Wait for the changes to be made  
1. Set the Timezone
	Select menu choice `1. Internationalization Options`  
	Select menu choice `I2 Timezone`
	Select the Geographic area: `US`  
	Select the Time zone: `Arizona` 
	Select `<OK>` 
1. Set the memory split for a console only RPi
	Select menu choice `1. Advanced Options`  
	Select menu choice `A3 Memory Split`
	Set the minimum GPU memory split: `016` for 16MB    
	Select `<OK>` 	
1. Set a new default user password
	Select menu choice `1. Change User Password`  
	Enter and reenter a new password 		
1. Exit the config. application  
	Use the tab keys to select `<Finish>`
1. Select `<OK>` to reboot

##Shutting down safely
`sudo shutdown -h now` or `sudo halt`
`sudo shutdown -r now` or `sudo reboot`

##Backing up the SD Card / Create the master image 
1. With power off to the RPi, remove the SD card.
1. Insert a SD card into your computer
1. Open a terminal window
1. Find out the ID of the SD Card
	`diskutil list` => /dev/disk3
1. Copy the image on the card to the computer
	`sudo dd bs=4m if=/dev/rdisk3 of=/Users/brianmcmillan/Documents/systemimages/raspberrypi/master/raspbian_lite_01.img`
1. Unmount the SD Card
`diskutil unmountDisk /dev/disk1`	

This will take some time (10-30 min.) and if you are impatient, check the status with `[CTRL] + t`.

This image can be used to bootstrap a RPi without having to reconfigure an image from scratch.