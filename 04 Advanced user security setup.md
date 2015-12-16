#Raspbian master image creation
##Part 4 - Advanced user security setup
###Image requirements
Uses the image created in Part 3 - Basic user security setup
###Copy a master image to a new SD card (Optional)
1. Insert a SD card into your computer
1. Find out the ID of the SD Card
	`diskutil list` => /dev/disk1
1. Unmount the SD Card
	`diskutil unmountDisk /dev/disk1`
1. Copy the image for the card
	`sudo dd bs=4m if=/Users/brianmcmillan/Documents/systemimages/raspberrypi/master/raspbian_<image>.img of=/dev/rdisk1`
1. Unmount the SD Card
`diskutil unmountDisk /dev/disk1`

---

###Create a new user
1. SSH into the RPi   
`ssh pi@192.168.0.11`
1. Create an administrator user   
`sudo adduser --gecos GECOS admin`
1. Create a strong password

###Enable sudo command use
1. Add the user to the sudo group
`sudo usermod -a -G sudo admin`
1. Add the user to the sudoers file
`sudo visudo`
1. Add the following to the end of the file. Then save and exit the file.  
`admin ALL=(ALL) NOPASSWD: ALL`

TEST THIS - `sudo echo 'admin ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers`

###Enable SSH access for this user
1. Create the SSH key directory and file on the RPi
`sudo mkdir -p /home/admin/.ssh`
`sudo touch /home/admin/.ssh/authorized_keys`
`sudo chown -R admin:admin /home/admin/.ssh`
1. Open the authorized_keys file for editing
`sudo nano /home/admin/.ssh/authorized_keys`

###Copy your key over to the server
1. Open a terminal window on your Mac
1. Open the new ssh key file  
`cat ~/.ssh/id_rsa.pub`
1. Copy the contents of that file to the clipboard manually
`ssh-rsa AAAAB3Nza...p8etvB brianmcmillan@Brians-MacBook-Pro.local`
1. Switch back to the RPi terminal window
1. Paste the contents of the clipboard into the authorized_keys file. Save and exit.
1. OR - Copy the public key to the RPi with a command
	`cat ~/.ssh/id_ras.pub | ssh pi@192.168.0.11 'cat >> /home/admin/.ssh/authorized_keys'`
1. Restrict permissions on the directory and file
`sudo chmod 700 /home/admin/.ssh`
`sudo chmod 600 /home/admin/.ssh/authorized_keys` 
1. Restart the SSH service
`sudo /etc/init.d/ssh restart`

###Log in using the SSH key
1. From the RPi terminal window
2. 	Log out the pi@192.168.0.11 user  
	`exit`
3. Log in with SSH
	`ssh -v admin@192.168.0.11`

###Issues
1. Most issues have to do with setting the correct permissions

```
ls -lah ~/.ssh

drwx------ 2 admin admin 4.0K Dec 13 21:19 .
drwxr-xr-x 3 admin admin 4.0K Dec 13 21:19 ..
-rw------- 1 admin admin  420 Dec 13 21:29 authorized_keys

```
###Disable the user pi
1. Disable password login for the user account
`sudo passwd -l pi`
1. Disable shell access
`sudo usermod -L -s /bin/nologin pi`
1. Test access
`exit`
1. SSH into the RPi. This should fail.  
`ssh pi@192.168.0.11`

###Other commands
Determine user status: `sudo chage -l joe`  
Disable a user account: `passwd -l joe`  
Delete a user and keep files: `sudo userdel joe`  
Delete a user and all files: `sudo userdel -r joe`  

##Shutting down safely
`sudo shutdown -h now` or `sudo halt`
`sudo shutdown -r now` or `sudo reboot`

##Backing up the SD Card / Create the master image 
1. With power off to the RPi, remove the SD card.
1. Insert a SD card into your computer
1. Open a terminal window
1. Find out the ID of the SD Card
`diskutil list` => /dev/disk1
1. Copy the image on the card to the computer
`sudo dd bs=4m if=/dev/rdisk1 of=/Users/brianmcmillan/Documents/systemimages/raspberrypi/master/raspbian_lite_04.img`
1. Unmount the SD Card
`diskutil unmountDisk /dev/disk1`

This will take some time (10-30 min.) and if you are impatient, check the status with `[CTRL] + t`.

This image can be used to bootstrap a RPi without having to reconfigure an image from scratch.

