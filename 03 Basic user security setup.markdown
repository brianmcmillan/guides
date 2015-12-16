#Raspbian master image creation
##Part 3 - Basic user security setup
###Image requirements
Uses the image created in Part 1 - Initial configuration
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
1. Insert the SD card in the RPi and pug power back in

---

###Find your SSH key name
The `id_rsa.pub` key can be used but it may be desirable to generate a new key specifically for system administration and development.

1. Check for existing SSH keys  
	Open a new terminal window to your Mac
	`ls ~/.ssh`  
	Should return a file named `id_rsa.pub`
1. Inspect your local key:
	This isn't necessary but it is helpful to recognize what a key looks like.
	`$ cat ~/.ssh/id_rsa.pub`  
	Returns:
	`ssh-rsa AAAAB3Nza...N/ryTGNj brianmcmillan@Brians-MacBook-Pro.local`

###Copy your key over to the server
1. Open a terminal window on your Mac
1. SSH into the RPi. This will open a new terminal window to the RPi.
`ssh pi@192.168.0.11`
1. Create the SSH key directory on the RPi
`mkdir -p ~/.ssh`
1. Switch back to the mac terminal window
1. Copy the public key to the RPi
`cat ~/.ssh/id_rsa.pub | ssh pi@192.168.0.11 'cat >> .ssh/authorized_keys'`
1. Switch back to the RPi terminal window
1. Verify the key was added
`sudo cat ~/.ssh/authorized_keys`  
`ls -lah ~/.ssh` => `-rw-r--r-- 1 pi pi  420 Dec 13 21:01 authorized_keys`
1. Set permissions on the directory and file
`sudo chmod 700 ~/.ssh`
`sudo chmod 600 ~/.ssh/authorized_keys` 

###Log in using the SSH key
1. From the RPi terminal window
1. 	Log out the pi@192.168.0.11 user  
	`exit`
1. Log in with SSH
	`ssh pi@192.168.0.11`
1. The login should not prompt for a password	
###Remove password based logins
1. Open the SSH server configuration file for editing.
	`sudo nano /etc/ssh/sshd_config`  	
1. Prevent root login  
	Change `PermitRootLogin without-password` to `PermitRootLogin no`
1. Prevent the use of passwords  
	Change `#PasswordAuthentication yes` to `PasswordAuthentication no`
1. Save the file and exit	
1. Restart the SSH service
	`sudo /etc/init.d/ssh restart`

###Test passwordless login  
1. Open a new terminal window on your Mac
`ssh root@192.168.0.11` should not work.
1. `exit`
1. `ssh pi@192.168.0.11` should not require a password.  

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
`sudo dd bs=4m if=/dev/rdisk1 of=/Users/brianmcmillan/Documents/systemimages/raspberrypi/master/raspbian_lite_03.img`
1. Unmount the SD Card
`diskutil unmountDisk /dev/disk1`

This will take some time (10-30 min.) and if you are impatient, check the status with `[CTRL] + t`.

This image can be used to bootstrap a RPi without having to reconfigure an image from scratch.

---

```
cat /etc/ssh/sshd_config
# Package generated configuration file
# See the sshd_config(5) manpage for details

# What ports, IPs and protocols we listen for
Port 22
# Use these options to restrict which interfaces/protocols sshd will bind to
#ListenAddress ::
#ListenAddress 0.0.0.0
Protocol 2
# HostKeys for protocol version 2
HostKey /etc/ssh/ssh_host_rsa_key
HostKey /etc/ssh/ssh_host_dsa_key
HostKey /etc/ssh/ssh_host_ecdsa_key
HostKey /etc/ssh/ssh_host_ed25519_key
#Privilege Separation is turned on for security
UsePrivilegeSeparation yes

# Lifetime and size of ephemeral version 1 server key
KeyRegenerationInterval 3600
ServerKeyBits 1024

# Logging
SyslogFacility AUTH
LogLevel INFO

# Authentication:
LoginGraceTime 120
PermitRootLogin without-password
StrictModes yes

RSAAuthentication yes
PubkeyAuthentication yes
#AuthorizedKeysFile	%h/.ssh/authorized_keys

# Don't read the user's ~/.rhosts and ~/.shosts files
IgnoreRhosts yes
# For this to work you will also need host keys in /etc/ssh_known_hosts
RhostsRSAAuthentication no
# similar for protocol version 2
HostbasedAuthentication no
# Uncomment if you don't trust ~/.ssh/known_hosts for RhostsRSAAuthentication
#IgnoreUserKnownHosts yes

# To enable empty passwords, change to yes (NOT RECOMMENDED)
PermitEmptyPasswords no

# Change to yes to enable challenge-response passwords (beware issues with
# some PAM modules and threads)
ChallengeResponseAuthentication no

# Change to no to disable tunnelled clear text passwords
#PasswordAuthentication yes

# Kerberos options
#KerberosAuthentication no
#KerberosGetAFSToken no
#KerberosOrLocalPasswd yes
#KerberosTicketCleanup yes

# GSSAPI options
#GSSAPIAuthentication no
#GSSAPICleanupCredentials yes

X11Forwarding yes
X11DisplayOffset 10
PrintMotd no
PrintLastLog yes
TCPKeepAlive yes
#UseLogin no

#MaxStartups 10:30:60
#Banner /etc/issue.net

# Allow client to pass locale environment variables
AcceptEnv LANG LC_*

Subsystem sftp /usr/lib/openssh/sftp-server

# Set this to 'yes' to enable PAM authentication, account processing,
# and session processing. If this is enabled, PAM authentication will
# be allowed through the ChallengeResponseAuthentication and
# PasswordAuthentication.  Depending on your PAM configuration,
# PAM authentication via ChallengeResponseAuthentication may bypass
# the setting of "PermitRootLogin without-password".
# If you just want the PAM account and session checks to run without
# PAM authentication, then enable this but set PasswordAuthentication
# and ChallengeResponseAuthentication to 'no'.
UsePAM yes
```








###Create an Administrative user
1. Open a terminal window on your Mac
1. SSH into the RPi. This will open a new terminal window to the RPi.
`ssh pi@192.168.0.11`
1. Create an administrator user  
`adduser --disabled-password --gecos GECOS admin`
1. Add the user to the sudo group
`usermod -a -G sudo admin`
