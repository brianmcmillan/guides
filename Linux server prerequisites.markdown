#Linux Server prerequisites
##Hardware
###Development Workstation
The assumption for this guide is that you are using an Apple computer.
###Linux Server
Virtually and computer can be configured using these guides, including virtual machines hosted "in the cloud" or running on the development workstation. However, for simplicity, this guide assumes the use of a Raspberry Pi 2. 

####Raspberry Pi 2 Bill of Materials
Raspberry Pi 2 - http://www.amazon.com/Raspberry-Pi-Model-Project-Board/dp/B00T2U7R7I/ref=sr_1_4?s=pc&ie=UTF8&qid=1447692644&sr=1-4&keywords=raspberry+pi+2&refinements=p_85%3A2470955011  

MicroSD Card 16GB - http://www.amazon.com/SanDisk-Extreme-microSDHC-Adapter-SDSQXSG-016G-GN6MA/dp/B010NE3ND8/ref=sr_1_4?s=pc&ie=UTF8&qid=1447691154&sr=1-4&keywords=SANDISK+EXTREME+PLUS+16GB+microSDHC 

MicroSD Card 16GB - http://www.amazon.com/Samsung-Class-Adapter-MB-MP32DA-AM/dp/B00IVPU786/ref=sr_1_5_m?s=pc&ie=UTF8&qid=1449240140&sr=1-5&keywords=Samsung+Evo

USB Drive 64GB (USB 2) - http://www.amazon.com/SanDisk-Cruzer-Low-Profile-Drive--SDCZ33-016G-B35/dp/B005FYNSZA/ref=sr_1_fkmr0_2?s=pc&ie=UTF8&qid=1447691298&sr=1-2-fkmr0&keywords=SANDS+CRUZER+FIT™+USB+FLASH+DRIVE+62GB

USB Drive 64GB (USB 3)- http://www.amazon.com/SanDisk-Ultra-Low-Profile-Flash-SDCZ43-064G-G46/dp/B00LLEODCK

Micro USB charger - http://www.amazon.com/gp/product/B00GF9T3I0/ref=s9_al_bw_g147_i1

Case - Case - http://www.amazon.com/gp/product/B00M6G9YBM/ref=s9_hps_bw_g147_i2

Cat5 Ethernet cable - Any


##Software
###MacOS Xcode command line developers tools
`xcode-select --install`

###Homebrew package manager
http://brew.sh

####Install
`ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`

**NOTE:** There is an issue with MacOS 10.11 (El Capitan). At this time (11/19/2015) Homebrew requires resetting the permissions on the /usr/local directory back to the admin user group.  
`sudo chown -R $(whoami):admin /usr/local`

####Verify installation
`brew info`

####Test version
`brew -v`

####Upgrading
`brew update`  
`brew upgrade [—all|node]`

####Stop
[CTRL]-[C]

####Help
`brew -help`

```
Example usage:
  brew [info | home | options ] [FORMULA...]
  brew install FORMULA...
  brew uninstall FORMULA...
  brew search [foo]
  brew list [FORMULA...]
  brew update
  brew upgrade [FORMULA...]
  brew pin/unpin [FORMULA...]

Troubleshooting:
  brew doctor
  brew install -vd FORMULA
  brew [--env | config]

Brewing:
  brew create [URL [--no-fetch]]
  brew edit [FORMULA...]
  https://github.com/Homebrew/homebrew/blob/master/share/doc/homebrew/Formula-Cookbook.md

Further help:
  man brew
  brew home
```  
####Uninstall
`ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"`

###Install NMAP
`brew install nmap`

###Install WGET
`brew install wget`

###Install GIT
Download and install from:  
https://desktop.github.com

###Install The Unarchiver
Download and install from:  
https://itunes.apple.com/app/the-unarchiver/id425424353?mt=12&ls=1

##Collect some basic information
###Your Mac's IP Address
1. Open a terminal window on your mac
2. Find the IP addresses being used  
`ifconfig | grep inet`

Returns:

```
	inet6 ::1 prefixlen 128 
	inet 127.0.0.1 netmask 0xff000000 
	inet6 fe80::1%lo0 prefixlen 64 scopeid 0x1 
	inet6 fe80::6aa8:6dff:fe25:7634%en1 prefixlen 64 scopeid 0x5 
	inet 192.168.0.3 netmask 0xffffff00 broadcast 192.168.0.255
``` 

The ip address is `192.168.0.3`
The router is most likely at `192.168.0.1`
The network range is `192.168.0.0/24`

###Router configuration
Collect some basic network configuration information about the router you are connected to. 

1. Open System Preferences > Network > Interface (Wi-Fi) > Advanced > TCP/IP

Configure IPv4 Address: `Using DHCP`
IPv4 Address: `192.168.0.3` 
Subnet Mask: `255.255.255.0`   
Router: `192.168.0.1`

2. From the DNS tab 
DNS Servers: `192.168.0.1`  

3. Alternately, log into your router configuration application. This will be necessary to determine the IP addresses outside of the DHCP address range.

###User information
1. Verify user name  
`whoami` => `brianmcmillan`
2. Verify home directory
`cd ~/ && pwd` => `/Users/brianmcmillan`
2. Check for existing SSH keys  
	Open a new terminal window to your Mac  
	`ls ~/.ssh`  
	Should return a file named `id_rsa.pub`
	If not, follow the instructions for generating an new key using the directions in the SSL Setup document. 

###NTP Server
1. Verify the NTP Server  
`cat /etc/ntp.conf`  
Returns:  
`server time.apple.com`

###Identify the other devices on your network   
1. Open a terminal window
2. Run the nmap program
`sudo nmap -O -oG 192.168.0.0/24`
`Password:<ROOT PASSWORD>`
	
Returns:

```
Starting Nmap 6.47 ( http://nmap.org ) at 2015-11-19 09:27 MST
Nmap scan report for 192.168.0.1
Host is up (0.0022s latency).
Not shown: 995 closed ports
PORT      STATE SERVICE
53/tcp    open  domain
80/tcp    open  http
443/tcp   open  https
5000/tcp  open  upnp
12345/tcp open  netbus
MAC Address: 44:94:FC:47:39:94 (Netgear,)
Device type: general purpose|WAP
Running: Linux 2.6.X
...
Network Distance: 1 hop

Nmap scan report for 192.168.0.5
Host is up (0.0030s latency).
Not shown: 999 closed ports
PORT     STATE SERVICE
3689/tcp open  rendezvous
MAC Address: 68:A8:6D:26:CA:AA (Apple)
Device type: general purpose|media device|phone
Running: Apple Mac OS X 10.7.X|10.9.X|10.8.X, Apple iOS 4.X|5.X|6.X
...
Network Distance: 1 hop

Nmap scan report for 192.168.0.7
Host is up (0.0095s latency).
Not shown: 989 closed ports
PORT     STATE SERVICE
80/tcp   open  http
139/tcp  open  netbios-ssn
443/tcp  open  https
445/tcp  open  microsoft-ds
515/tcp  open  printer
631/tcp  open  ipp
6839/tcp open  unknown
7435/tcp open  unknown
8080/tcp open  http-proxy
9100/tcp open  jetdirect
9220/tcp open  unknown
MAC Address: 6C:C2:17:1E:07:62 (Hewlett Packard)
Device type: general purpose
Running: Wind River VxWorks
...
Network Distance: 1 hop

Nmap scan report for 192.168.0.3
Host is up (0.00023s latency).
Not shown: 498 closed ports, 498 filtered ports
PORT     STATE SERVICE
88/tcp   open  kerberos-sec
445/tcp  open  microsoft-ds
548/tcp  open  afp
5900/tcp open  vnc
No exact OS matches for host (If you know what OS is running on it, see http://nmap.org/submit/ ).
TCP/IP fingerprint:
...

Network Distance: 0 hops

OS detection performed. Please report any incorrect results at http://nmap.org/submit/ .
Nmap done: 256 IP addresses (7 hosts up) scanned in 110.69 seconds
```
