#Raspbian master image creation
##Part 06 - Setting up monitoring
###Image requirements
Uses the image created in 05 - Advanced network setup
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

###Basic system and process viewing with htop
1. Log in with the local host name
`ssh admin@us1-bgwgc.local`
1. Install htop  
`sudo apt-get install htop`
1. Run htop
`htop` 

###Setup automatic restart using the CPU watchdog timer (RPi Specific)
1. Change to the root user
`sudo -i`
1. Install the watchdog
`sudo apt-get install watchdog`
`sudo modprobe bcm2708_wdog`
1. Backup the old configuration file  
`sudo cp /etc/modules /etc/modules.bak`
1. Run the following commands to update the configuration file  

```
sudo cat <<EOT > /etc/modules
#################################################
## RPi watchdog configuration file
#################################################
## FILE NAME modules
## FILE LOCATION /etc/
## SCM LOCATION <path to git location>
## MAINTAINER <brian.mcmillan.architect@gmail.com>
## CREATE DATE <2015-12-03>
## LAST MODIFIED <2015-12-03>
## INFO http://pythonhosted.org/watchdog/index.html
#################################################
# /etc/modules: kernel modules to load at boot time.
# This file contains the names of kernel modules 
# that should be loaded at boot time, one per line. 
# Lines beginning with "#" are ignored.

snd-bcm2835
i2c-dev
bcm2708_wdog

EOT
```

1. Backup the old configuration file  
`sudo cp /etc/watchdog.conf /etc/watchdog.conf.bak` 
1. Run the following commands to update the configuration file   

```
sudo cat <<EOT > /etc/watchdog.conf
#################################################
## RPi watchdog configuration file
#################################################
## FILE NAME watchdog.conf
## FILE LOCATION /etc/
## SCM LOCATION <path to git location>
## MAINTAINER <brian.mcmillan.architect@gmail.com>
## CREATE DATE <2015-12-03>
## LAST MODIFIED <2015-12-03>
## INFO http://pythonhosted.org/watchdog/index.html
#################################################

watchdog-device	= /dev/watchdog

# This greatly decreases the chance that watchdog won't be scheduled before
# your machine is really loaded
realtime		= yes
priority		= 1

# If your machine is really hung, the loadavg will go much higher than 25)
max-load-1		= 24
max-load-5		= 18
max-load-15		= 12
EOT
```

1. Add watchdog to start on startup  
`sudo update-rc.d watchdog defaults`
1. Start the service
`sudo service watchdog start`
1. Test the watchdog by causing a kernel panic
`: (){ :|:& };:`

###Setup Mail Transfer agent
1. Log in with the local host name
`ssh admin@us1-bgwgc.local`
1. Switch to the root user
`sudo -i`
1. Install Postfix
`sudo apt-get update && apt-get install postfix mailutils`
1. From the configuration GUI
	* General type of mail configuration: `Internet Site`
	* System mail name: `us1-bgwgc.local` 
	  NOTE: Can later be changed in `/etc/mailname`
	* The configuration script will now run
1. Add a protocol restriction to the config file
`sudo echo 'inet_protocols = ipv4' >> /etc/postfix/main.cf`
1. Add an email address to forward mail to at the end of the email alias file
`sudo echo 'root: brian.mcmillan.architect@gmail.com' >> /etc/aliases`
1. Reload the alias file  
`sudo newaliases`
1. Test email delivery  
`echo test | mail -s "test message from $HOSTNAME" root`

####Additional Postfix commands
Status: `sudo postfix status`  
Check: `sudo postfix check` 
Update: `sudo postfix upgrade-configuration` 
Stop: `sudo postfix stop`  
Start: `sudo postfix start`  
Restart: `sudo postfix reload`
Reload configuration: `postfix reload`


###System monitoring, automation, and alerting with Monit
1. Install Monit
`sudo apt-get update && sudo apt-get install monit` 
1. Backup the old configuration file
`sudo cp /etc/monit/monitrc /etc/monit/monitrc.bak`
1. Replace the default file with the following

```
cat <<EOT > /etc/monit/monitrc
#################################################
## Monit control file
#################################################
##
## Comments begin with a '#' and extend through the end of the line. Keywords
## are case insensitive. All path's MUST BE FULLY QUALIFIED, starting with '/'.
##
## FILE NAME monitrc
## FILE LOCATION /etc/monit/
## SCM LOCATION <path to git location>
## MAINTAINER <brian.mcmillan.architect@gmail.com>
## CREATE DATE <2015-12-02>
## LAST MODIFIED <2015-12-02>
## INFO https://mmonit.com/monit/documentation/monit.html
##
#################################################
## Global section
#################################################
##
set daemon 120                       # Check services at 2-minute intervals
set logfile /var/log/monit.log       # Set logfile location
set pidfile /var/run/monit.pid       # Set pid file location
set idfile /var/lib/monit/id         # Set id file location 
set statefile /var/lib/monit/state   # Set state file location
set eventqueue
    basedir /var/lib/monit/events    # set the base directory where events will be stored
    slots 100                        # optionally limit the queue size
#
## Mail configuration
set mailserver localhost             # Use localhost for mail delivery
set alert root@localhost             # All mail goes to the root account
# set alert root@localhost not on { instance, action } # Suppress startup alerts

## Set mail message format
set mail-format {
    from: monit@\$HOST
    subject: monit alert --  \$EVENT \$SERVICE
    message: \$EVENT Service \$SERVICE
        Date:        \$DATE
        Action:      \$ACTION
        Host:        \$HOST
        Description: \$DESCRIPTION
}
#
## HTTP configuration  
set httpd port 2812 and
#use address localhost     # Only accept connection from localhost
allow localhost            # Alow localhost to connect to the server and
allow 192.168.0.0/24       # Allow any host on 192.168.1.* subnet
allow admin:monit          # Require user 'admin' with password 'monit'
allow @monit               # Allow users of group 'monit' to connect (rw)
allow @users readonly      # Allow users of group 'users' to connect readonly
#
#################################################
## Monitored Services
#################################################
include /etc/monit/conf.d/*

EOT

```

1. Add basic system monitoring

```
cat <<EOT > /etc/monit/conf.d/basic
#################################################
## Monit control file
#################################################
##
## FILE NAME conf.d/basic
## FILE LOCATION /etc/monit/
## SCM LOCATION <path to git location>
## MAINTAINER <brian.mcmillan.architect@gmail.com>
## CREATE DATE <2015-12-02>
## LAST MODIFIED <2015-12-14>
## INFO https://mmonit.com/monit/documentation/monit.html
##
#################################################
## Check general system resources 
#
check system \$HOST
if loadavg (1min) > 4 then alert
if loadavg (5min) > 2 then alert
if memory usage > 75% then alert
if swap usage > 25% then alert
if cpu usage (user) > 70% then alert
if cpu usage (system) > 30% then alert
if cpu usage (wait) > 20% then alert
#
# local fs system check
check filesystem rootfs with path /
    if space > 90% then alert
    if inodes > 90% then alert
#
# sshd monitor
check process sshd with pidfile /var/run/sshd.pid
   start program  "/etc/init.d/ssh start"
   stop program  "/etc/init.d/ssh stop"
   if failed port 22 protocol ssh then restart    
#
# ntp
check process ntp with pidfile /var/run/ntpd.pid
   start program = "/etc/init.d/ntp start"
   stop  program = "/etc/init.d/ntp stop"
   if failed host 127.0.0.1 port 123 type udp then alert
#
# dhcpcd monitor
check process dhcpcd with pidfile /var/run/dhcpcd.pid
   start program  "/etc/init.d/dhcpcd start"
   stop program  "/etc/init.d/dhcpcd stop"
   if failed host 127.0.0.1 port 68 type udp then alert  
#
# cron
check process cron with pidfile /var/run/crond.pid
   group system
   start program = "/etc/init.d/cron start"
   stop  program = "/etc/init.d/cron stop"
   depends on cron_rc

 check file cron_rc with path /etc/init.d/cron
   group system
   if failed checksum then unmonitor
   if failed permission 755 then unmonitor
   if failed uid root then unmonitor
   if failed gid root then unmonitor
#   
# rsyslog monitor
check process rsyslog with pidfile /var/run/rsyslogd.pid
   start program = "/etc/init.d/rsyslog start"
   stop program = "/etc/init.d/rsyslog stop"

check file rsyslogd_file with path /var/log/syslog
   if timestamp > 65 minutes then alert # Have you seen "-- @HOSTNAME --"? 
#
#Postfix (mail server)
check process postfix with pidfile /var/spool/postfix/pid/master.pid
   group mail
   start program = "/etc/init.d/postfix start"
   stop  program = "/etc/init.d/postfix stop"
   if failed port 25 protocol smtp then restart
   depends on postfix_rc

check file postfix_rc with path /etc/init.d/postfix
   group mail
   if failed checksum then unmonitor
   if failed permission 755 then unmonitor
   if failed uid root then unmonitor
   if failed gid root then unmonitor   

EOT

```

1. Check the configuration file syntax
`monit -t`
1. Start the service
`/etc/init.d/monit start`
`service monit restart`
1. Check the status
`monit status`
1. Open the Monit web page  
http://us1-bgwgc.local:2812
1. Type in the password  
`admin / monit`
1. Log out of root
`exit`

####Monit help file
```
sudo monit -h   
Usage: monit [options] {arguments}
Options are as follows:
 -c file       Use this control file
 -d n          Run as a daemon once per n seconds
 -g name       Set group name for start, stop, restart, monitor and unmonitor
 -l logfile    Print log information to this file
 -p pidfile    Use this lock file in daemon mode
 -s statefile  Set the file monit should write state information to
 -I            Do not run in background (needed for run from init)
 --id          Print Monit's unique ID
 --resetid     Reset Monit's unique ID. Use with caution
 -t            Run syntax check for the control file
 -v            Verbose mode, work noisy (diagnostic output)
 -vv           Very verbose mode, same as -v plus log stacktrace on error
 -H [filename] Print SHA1 and MD5 hashes of the file or of stdin if the
               filename is omited; monit will exit afterwards
 -V            Print version number and patchlevel
 -h            Print this text
Optional action arguments for non-daemon mode are as follows:
 start all           - Start all services
 start name          - Only start the named service
 stop all            - Stop all services
 stop name           - Only stop the named service
 restart all         - Stop and start all services
 restart name        - Only restart the named service
 monitor all         - Enable monitoring of all services
 monitor name        - Only enable monitoring of the named service
 unmonitor all       - Disable monitoring of all services
 unmonitor name      - Only disable monitoring of the named service
 reload              - Reinitialize monit
 status              - Print full status information for each service
 summary             - Print short status information for each service
 quit                - Kill monit daemon process
 validate            - Check all services and start if not running
 procmatch <pattern> - Test process matching pattern

(Action arguments operate on services defined in the control file)
```
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
`sudo dd bs=4m if=/dev/rdisk1 of=/Users/brianmcmillan/Documents/systemimages/raspberrypi/master/raspbian_lite_06.img`
1. Unmount the SD Card
`diskutil unmountDisk /dev/disk1`

This will take some time (10-30 min.) and if you are impatient, check the status with `[CTRL] + t`.

This image can be used to bootstrap a RPi without having to reconfigure an image from scratch.