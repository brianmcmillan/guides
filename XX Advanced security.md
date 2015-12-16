###Setup fail2ban
http://blog.self.li/post/63281257339/raspberry-pi-part-1-basic-setup-without-cables

`sudo apt-get install fail2ban`
`sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local`
`sudo service fail2ban restart`
`sudo iptables -L`




###Setting up a firewall
http://guides.webbynode.com/articles/security/ubuntu-ufw.html
http://blog.self.li/post/63281257339/raspberry-pi-part-1-basic-setup-without-cables

`netstat -anp | head -20`
`sudo netstat -tulpn | grep --color "ntpd"`
`lsof -i TCP` (displays online open tcp ports)
`lsof -i UDP`

1. Install Uncomplicated Firewall  
`sudo apt-get install ufw`
1. Configure firewall rules

|Application|protocol|Port|Command|
|:---|:---:|:---:|:---|
|ssh|tcp|22|`sudo ufw allow 22`|
|dns|udp|68|`sudo ufw allow XX`|
|ntp|udp|123|`sudo ufw allow XX`|
|Postfix|tcp|25|`sudo ufw allow XX`|
|monit http|tcp|2812|`sudo ufw allow XX`|
|RPi Monitor http|tcp|8888|`sudo ufw allow 8888`|

1. Start the firewall on startup  
`sudo ufw enable`
1. View firewall rules
`sudo ufw status verbose`


---

###Check for rootkit
http://www.cyberciti.biz/faq/howto-check-linux-rootkist-with-detectors-software/

###Disabling a user procedure
http://www.cyberciti.biz/faq/former-employees-keep-accessing-linux-unix-server/
