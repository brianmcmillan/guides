#SSL Setup
##Preliminary steps
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
	`ssh-rsa AAAAB3Nza...N/ryTGNj brian.mcmillan.architect@gmail.com`

###Generate a new DevOps key-pair (optional)
This is not needed if you want to use your id_rsa.pub file.

1. Open a terminal window on your mac.
1. Generate a new key
`ssh-keygen -t rsa -b 2048 -C brian.mcmillan.architect@gmail.com -f ~/.ssh/devops_brianmcmillan`
1. Enter a passphrase its should be long and memorable.  
Results:

```
Your identification has been saved in /Users/brianmcmillan/.ssh/devops_brianmcmillan.
Your public key has been saved in /Users/brianmcmillan/.ssh/devops_brianmcmillan.pub.
The key fingerprint is:
SHA256:...
brian.mcmillan.architect@gmail.com
The key's randomart image is:
+---[RSA 2048]----+
|ooo              |
|++ + .           |
...
|=B=...           |
+----[SHA256]-----+
```

###Generate a new OpsAutomation key-pair (optional)
This key will be used for later operations automation tasks.

1. Open a terminal window on your mac.
1. Generate a new key
`ssh-keygen -t rsa -b 2048 -C brian.mcmillan.architect@gmail.com -f ~/.ssh/ops_auto`
1. Enter a passphrase its should be long and memorable.  
Results:

```
Your identification has been saved in /Users/brianmcmillan/.ssh/ops_auto.
Your public key has been saved in /Users/brianmcmillan/.ssh/ops_auto.pub.
The key fingerprint is:
SHA256:...
The key's randomart image is:
+---[RSA 2048]----+
| .o .            |
|.+ = o . .       |
...
|   .o.*o+.  o=o  |
+----[SHA256]-----+
```	
  

##Passwordless SSH logins
###Copy your key over to the server
1. Open a terminal window on your Mac
1. SSH into the RPi. This will open a new terminal window to the RPi.
	`ssh pi@192.168.0.11`
1. Create the SSH key directory on the RPi
	`mkdir -p ~/.ssh`
1. Switch back to the mac terminal window
1. Copy the public key to the RPi
	`cat ~/.ssh/devops_brianmcmillan.pub | ssh pi@192.168.0.11 'cat >> .ssh/authorized_keys'`
1. Switch back to the RPi terminal window
1. Verify the key was added
`sudo cat ~/.ssh/authorized_keys`  
`ls -lh ~/.ssh`
1. Set permissions on the directory and file
`sudo chmod 700 ~/.ssh`
`sudo chmod 600 ~/.ssh/authorized_keys` 	

###Log in using the SSH key
1. From the RPi terminal window
2. 	Log out the pi@192.168.0.11 user  
	`exit`
3. Log in with SSH
	`ssh pi@192.168.0.11`

###Limit the groups that can login with SSH

	
###Remove password based logins


1. Open the SSH server configuration file for editing.
	`sudo nano /etc/ssh/sshd_config`  	
1. Prevent root login  
	Change `PermitRootLogin without-password` to `PermitRootLogin no`
1. Prevent the use of passwords  
	Change `#PasswordAuthentication yes` to `PasswordAuthentication no`


1. Reboot the server
	`sudo reboot`
	or 
	`sudo /etc/init.d/ssh restart`
1. Test passwordless login  
	`ssh -p 49022 pi@192.168.0.11` should not require a password.  
	`ssh -p 49022 root@192.168.0.11` should not work.




##Useful links
`$ man ssh`
`$ man ssh-keygen`

Logging in as root `sudo -i`

http://steve.dynedge.co.uk/2012/05/30/logging-into-a-rasberry-pi-using-publicprivate-keys/

https://www.raspberrypi.org/documentation/remote-access/ssh/passwordless.md

https://help.github.com/articles/generating-ssh-keys/

https://help.github.com/articles/working-with-ssh-key-passphrases/

https://www.raspberrypi.org/documentation/remote-access/ssh/passwordless.md   

https://dvpizone.wordpress.com/2014/03/02/how-to-connect-to-your-raspberry-pi-using-ssh-key-pairs/

http://automationguy.co.uk/home-lab/home-lab-build-part-13-raspberry-pi-hardening/#more-365	

https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md