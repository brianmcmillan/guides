#Raspbian master image creation
##Part 09 - Log file management
###Image requirements
Uses the image created in Part 08 - System metrics collection with Collectd 
###Copy a master image to a new SD card (Optional)
1. Insert a SD card into your computer
1. Find out the ID of the SD Card
	`diskutil list` => `/dev/disk1`
1. Unmount the SD Card
	`diskutil unmountDisk /dev/disk1`
1. Copy the image for the card
	`sudo dd bs=4m if=/Users/brianmcmillan/Documents/systemimages/raspberrypi/master/raspbian_<image>.img of=/dev/rdisk1`
1. Unmount the SD Card
`diskutil unmountDisk /dev/disk1`

---

###Log file rotation
1. SSH into the server  
`
1. Switch to the root user  
`sudo -i`
1. Backup the collectd configuration file
`sudo cp /etc/logrotate.d/rsyslog /etc/logrotate.d/rsyslog.bak`
1. Replace the default file with the following

```
sudo cat <<EOT > /etc/logrotate.d/rsyslog
#################################################
## Collectd configuration file
#################################################
##
## FILE NAME collectd.conf
## FILE LOCATION /etc/collectd/
## SCM LOCATION <path to git location>
## MAINTAINER <brian.mcmillan.architect@gmail.com>
## CREATE DATE <2015-12-02>
## LAST MODIFIED <2015-12-02>
## INFO https://collectd.org  and man collectd.conf
##
#################################################
/var/log/syslog
{
	rotate 7
	daily
	missingok
	notifempty
	delaycompress
	compress
   dateext
   dateyesterday
   dateformat _%Y-%m-%d
	postrotate
		invoke-rc.d rsyslog rotate > /dev/null
	endscript
}

/var/log/mail.info
/var/log/mail.warn
/var/log/mail.err
/var/log/mail.log
/var/log/daemon.log
/var/log/kern.log
/var/log/auth.log
/var/log/user.log
/var/log/lpr.log
/var/log/cron.log
/var/log/debug
/var/log/messages
{
	rotate 4
	weekly
	missingok
	notifempty
	compress
	delaycompress
	sharedscripts
	postrotate
		invoke-rc.d rsyslog rotate > /dev/null
	endscript
}

EOT

```


### Add application log rotation

1. Create a new application log rotation configuration file

```
sudo cat <<EOT > /etc/logrotate.d/monit
#################################################
## Collectd configuration file
#################################################
##
## FILE NAME collectd.conf
## FILE LOCATION /etc/collectd/
## SCM LOCATION <path to git location>
## MAINTAINER <brian.mcmillan.architect@gmail.com>
## CREATE DATE <2015-12-02>
## LAST MODIFIED <2015-12-02>
## INFO https://collectd.org  and man collectd.conf
##
#################################################
/var/log/monit.log {
daily
missingok
rotate 7
compress
delaycompress
notifyifepmty
dateext
dateyesterday
dateformat _%Y-%m-%d
}

EOT

```


##Shutting down safely
`sudo shutdown -h now` or `sudo halt`
`sudo shutdown -r now` or `sudo reboot`

##Backing up the SD Card / Create the master image 
1. With power off to the RPi, remove the SD card.
1. Insert a SD card into your computer
1. Open a terminal window
1. Find out the ID of the SD Card
`diskutil list` => `/dev/disk1`
1. Copy the image on the card to the computer
`sudo dd bs=4m if=/dev/rdisk1 of=/Users/brianmcmillan/Documents/systemimages/raspberrypi/master/raspbian_lite_09.img`
1. Unmount the SD Card
`diskutil unmountDisk /dev/disk1`

This will take some time (10-30 min.) and if you are impatient, check the status with `[CTRL] + t`.

This image can be used to bootstrap a RPi without having to reconfigure an image from scratch.