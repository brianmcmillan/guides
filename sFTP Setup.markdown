#sFTP Server Setup
##Preliminary steps
###Enable SSH passwordless login
1. Follow the steps in the SSL setup document. 
	
##sFTP server setup
###Find your SSH key name
1. Open a terminal window on your Mac
2. SSH into the RPi. This will open a new terminal window to the RPi.
	`$ ssh pi@j8twmxx`	
3. Change to the root user
`sudo -i`
4. Install vsftp
`apt-get install vsftpd`
5. Open the configuration file
`nano /etc/vsftpd.conf`
5. Edit the following lines in the file
`anonymous_enable=YES` => `anonymous_enable=NO`
`#local_enable=NO` => `local_enable=YES`
`#write_enable=YES` => `write_enable=YES`
`ssl_enable=NO` => `ssl_enable=YES`
6. Add the following lines to the end of the file
```
allow_anon_ssl=NO
force_local_data_ssl=YES
force_local_logins_ssl=YES
ssl_tlsv1=YES
ssl_sslv2=NO
ssl_sslv3=NO
force_dot_files=YES
# Hide the info about the owner (user and group) of the files.
hide_ids=YES
# Connection limit for each IP:
#max_per_ip=2
# Maximum number of clients:
#max_clients=20
```
3. Save the file. On the menu, this is WriteOut `^O`
	`[CTRL]+O` 
4. 	Exit Nano
5. Restart the service
	`sudo /etc/init.d/vsftpd restart`

###Create sFTP directory
http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_:_Ch15_:_Linux_FTP_Server_Setup#Installing_vsftpd

1. Create a new user group
`groupadd devops`
1. Create the sFTP directory
`mkdir /srv/ftp`
2. Set permissions 
`chmod -R 760 /srv/ftp/`  
`chown admin:devops /srv/ftp/`  
3. Create a test file in ftp the directory
`echo "sftp test file." >> /srv/ftp/test.txt`
`ls -l /srv/ftp/`
`cat /srv/ftp/test.txt`

###Add users
useradd -g devops -d /srv/ftp user1
useradd -g devops -d /srv/ftp user2

1. Upload their public certs


###Linking /srv/ftp/ to an external filesystem
http://uw714doc.sco.com/en/FD_files/Getting_to_a_file.html#Creating_a_link_to_a_directory


`mkdir -p /mnt/usbdrive/srv/sftp/`

ln -s /mnt/usbdrive/srv/ sftp
CD echo "test data" >> test.txt && ls -l

###Testing



###Test the setup
`$ ftp localhost`
	
	
##Useful Links
https://security.appspot.com/vsftpd.html

https://help.ubuntu.com/community/vsftpd

http://www.instructables.com/id/FerretPi-Using-Raspberry-Pi-as-a-Secure-FTP-Server/?ALLSTEPS

