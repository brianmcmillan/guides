#SSL Setup
##Preliminary steps
###Find your SSH key name
1. Check for existing SSH keys  
	Open a new terminal window to your Mac
	`ls ~/.ssh`  
	Should return a file named `id_rsa.pub`
	If not, follow the instructions for generating an new key below. 
2. Generate a new key-pair
This is not needed if you have an id_rsa.pub file.
https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md
`ssh-keygen -t rsa -C "brian.mcmillan.architect@gmail.com"`	
3. Inspect your local key:
	This isn't necessary but it is helpful to recognize what a key looks like.
	`$ cat ~/.ssh/id_rsa.pub`  
	Returns:
	`ssh-rsa AAAAB3Nza...Pb7p8etvB brianmcmillan@Brians-MacBook-Pro.local`  

##Passwordless SSH logins
###Copy your key over to the server
1. Open a terminal window on your Mac
2. SSH into the RPi. This will open a new terminal window to the RPi.
	`ssh pi@192.168.0.11`
3. Create the SSH key directory on the RPi
	`mkdir -p ~/.ssh`
4. Switch back to the mac terminal window
5. Copy the public key to the RPi
	`cat ~/.ssh/id_rsa.pub | ssh pi@192.168.0.11 'cat >> .ssh/authorized_keys'`
6. Switch back to the RPi terminal window
7. Verify the key was added
`sudo cat ~/.ssh/authorized_keys`  
`ls -l ~/.ssh`
8. Set permissions on the directory and file
`sudo chmod 700 ~/.ssh`
`sudo chmod 600 ~/.ssh/authorized_keys` 	

###Log in using the SSH key
1. From the RPi terminal window
2. 	Log out the pi@192.168.0.11 user  
	`exit`
3. Log in with SSH
	`ssh pi@192.168.0.11`
	
###Remove password based logins
1. Open the SSH server configuration file for editing.
	`sudo nano /etc/ssh/sshd_config`  
2. Prevent the use of passwords  
	Change `#PasswordAuthentication yes` to `PasswordAuthentication no`
3. Change the default port to something that will not be used or not on this list (http://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml)
4. Change `Port 22` to `Port 49022`	
5. Save the file. On the menu, this is WriteOut `^O`  
	`[CTRL]+O` 
4. 	Exit Nano  
	`[CTRL]+X`
5. Reboot the server
	`sudo reboot`
	or 
	`sudo /etc/init.d/ssh restart`
6. Test passwordless login  
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