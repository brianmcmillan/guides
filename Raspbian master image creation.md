#Raspbian master image creation
##Creating a master SD card image
###SD Card requirements
8GB microSD card class 6-10

###Download an image
1. Download and unzip an image  
	Standard RPi Image Repository:  
	https://www.raspberrypi.org/downloads/raspbian/

	Minimal RPi images:  
	http://dietpi.net    
	http://www.alpinelinux.org/downloads/   

	Docker host image:  
	http://blog.hypriot.com/downloads/

1. Create a directory to store the downloaded image files  
	e.g., `/Users/brianmcmillan/Documents/systemimages/raspberrypi/src`
1. Move any downloaded images to this directory


###Copy an image to an SD card
1. Insert a SD card into your computer
1. Find out the ID of the SD Card
	`diskutil list` => /dev/disk3
1. Unmount the SD Card
	`diskutil unmountDisk /dev/disk3`
1. Copy the image for the card
	`sudo dd bs=1m if=/Users/brianmcmillan/Documents/systemimages/raspberrypi/src/2015-09-24-raspbian-jessie.img of=/dev/rdisk3`

###Boot up the RPi
1. Plug the SD card into the RPi and start the RPi

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

1. 	If the connection was successful, select `yes`
```
The authenticity of host '192.168.0.11 (192.168.0.11)' can't be established.
ECDSA key fingerprint is SHA256:kZ7NKj8+qFv...euWvgQAoNsZQaT94VQ.
Are you sure you want to continue connecting (yes/no)? yes
```	

####Login error message
If another server has used this IP address before, you will need to remove that server fingerprint from the known hosts list.

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
...
```

1. Remove the fingerprint with 	
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
1. Reboot the RPi
	`sudo reboot`

###Set configuration choices
1. SSH into the RPi
	`ssh pi@192.168.0.11` 
1. Start the configuration application
	`sudo raspi-config`
1. Update the config. application and RPi  
	Select menu choice `1. Advanced Options`   
	Select menu choice `A0 Update`  
	Wait for the changes to be made  
1. Set a new default user password
	Select menu choice `1. Change User Password`  
	Enter and reenter a new password  
1. Set the default boot options  
	Select menu choice `1. Boot Options`  
	Select menu choice `B1 Console` 
1. Set the Locale
	Select menu choice `1. Internationalization Options`  
	Select menu choice `I1 Change Locale`   
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
1. Exit the config. application  
	Use the tab keys to select `<Finish>`
1. Reboot the RPi
	`sudo reboot`
	
###Create a dynamically generated hostname
1. SSH into the RPi
	`ssh -p 49022 pi@192.168.0.11` 
1. Lookup the hostname
	`echo $HOSTNAME` => `raspberrypi`
1. Generate a new hostname
	`GEO=$"us1" && OLD=$HOSTNAME && SEQ=$(cat /dev/urandom | tr -cd 'abcdfghjkmnprstvwxy0-9' | head -c 5) && sudo sed -i "s/$OLD/$GEO-$SEQ/" /etc/hostname && sudo sed -i "s/$OLD/$GEO-$SEQ/g" /etc/hosts && sudo reboot`
1. SSH into the RPi again with the IP Address
	`ssh -p 49022 pi@192.168.0.11`  
	The console prompt should now read something like 	`pi@us1-n873h ~ $`
1. Lookup new hostname
	`echo $HOSTNAME` => `us1-n873h`
	
###Setup SSH and passwordless logins
1. Follow the SSL setup instruction. 

###Adding external / backup DNS resolution
1. Open a terminal window on your Mac
1. SSH into the RPi. This will open a new terminal window to the RPi.
	`ssh -p 49022 pi@192.168.0.100`
1. Edit the network configuration file
	`sudo nano /etc/resolv.conf`
1. Add the Google public DNS servers to the end of the file

```
#Google public name servers
nameserver 8.8.8.8
nameserver 8.8.4.4
```
1. Save the file and exit
1. Restart the networking service
`sudo /etc/init.d/networking restart`

###Setting a static IP Address (Optional)
1. Open a terminal window on your Mac
1. SSH into the RPi. This will open a new terminal window to the RPi.
	`ssh -p 49022 pi@192.168.0.11`  
1. Edit the network configuration file
	`sudo nano /etc/dhcpcd.conf`
1. Add the following to the end of the file

```
# Setup a static ip address
#static
#interface eth0
#static ip_address=192.168.0.100/24
#static routers=192.168.0.1
#static domain_name_servers=192.168.0.1
```

1. Save the file and exit	

###Setup system auto discovery (Bonjour, mDNS, Zeroconf)
1. SSH into the RPi.
	`ssh -p 49022 pi@192.168.0.100`
1. Download and install Avahi
`sudo apt-get install avahi-daemon` 
1. Log out
`exit`
1. Log in with the local host name
`ssh -p 49022 pi@us1-n873h.local`

###Setup Mail Transfer agent
1. Log in with the local host name
`ssh -p 49022 pi@us1-n873h.local`
1. Install Postfix
`sudo apt-get update && apt-get install postfix mailutils`
1. From the configuration GUI
	* General type of mail configuration: `Internet Site`
	* System mail name: `us1-n873h.local` 
	  NOTE: Can later be changed in `/etc/mailname`
	* The configuration script will now run
1. Edit the email alias file
`sudo nano /etc/aliases`
1. Add an email address to forward mail to at the end of the file
`root: brian.mcmillan.architect@gmail.com`
1. Save and exit
1. Reload the alias file
`sudo newaliases`
1. Test email delivery
`echo test | mail -s "test message from $HOSTNAME" root`

###System monitoring and management
####RPi Monitor (RPi Specific)
1. Log in with the local host name
`ssh -p 49022 pi@us1-n873h.local`
1. Install RPi Monitor
`sudo apt-get install apt-transport-https ca-certificates`
`sudo apt-key adv --recv-keys --keyserver keyserver.ubuntu.com 2C0D3C0F`
`sudo apt-get update`
`sudo apt-get install rpimonitor`
1. Open the RPi Monitor web page
http://us1-n873h.local:8888

####Setup automatic restart using the CPU watchdog timer
1. Log in with the local host name
`ssh -p 49022 pi@us1-n873h.local`
1. Change to the root user
`sudo -i`
1. Install the watchdog
`sudo apt-get install watchdog`
`sudo modprobe bcm2708_wdog`

1. Run the following command to update the configuration file

```
sudo cat <<EOT > /etc/modules
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
# /etc/modules: kernel modules to load at boot time.
# This file contains the names of kernel modules 
# that should be loaded at boot time, one per line. 
# Lines beginning with "#" are ignored.

snd-bcm2835
i2c-dev
bcm2708_wdog

EOT

```

1. Add watchdog to start on startup
`sudo update-rc.d watchdog defaults`

1. Run the following command to update the configuration file

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

1. Start the service
`sudo service watchdog start`
1. Test the watchdog by causing a kernel panic
` : (){ :|:& };:`

####Basic system and process viewing with htop
1. Log in with the local host name
`ssh -p 49022 pi@us1-n873h.local`
1. Install htop  
`sudo apt-get install htop`
1. Run htop
`htop` 

####System monitoring, automation, and alerting with Monit
1. Log in with the local host name
`ssh -p 49022 pi@us1-n873h.local`
1. Change to the root user
`sudo -i`
1. Install Monit
`sudo apt-get update && sudo apt-get install monit` 
1. Backup the old configuration file
`sudo cp /etc/monit/monitrc monitrc.bak`
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
http://us1-n873h.local:2812
1. Log out of root
`exit`

####System monitoring and data collection with collectd
https://collectd.org
https://collectd.org/wiki/index.php/Main_Page
http://oss.oetiker.ch/rrdtool/doc/index.en.html

1. Log in with the local host name
`ssh -p 49022 pi@us1-n873h.local`
1. Change to the root user
`sudo -i`
1. Install collectd and rrdtool
`sudo apt-get install collectd rrdtool`
1. Make sure collectd is not running
`sudo /etc/init.d/collectd stop`
1. Backup the collectd configuration file
`sudo cp /etc/collectd/collectd.conf /etc/collectd/collectd.conf.bak`
1. Replace the default file with the following

```
cat <<EOT > /etc/collectd/collectd.conf
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

#################################################
# Global                                                                     #
#-----------------------------------------------#
# Global settings for the daemon.                                            #
#################################################

#Hostname "localhost"
FQDNLookup true
#BaseDir "/var/lib/collectd"
#PluginDir "/usr/lib/collectd"
#TypesDB "/usr/share/collectd/types.db" "/etc/collectd/my_types.db"

#-------------------------------------------------#
# When enabled, plugins are loaded automatically with the default options
# when an appropriate <Plugin ...> block is encountered.                     
# Disabled by default.                                                       #
#-------------------------------------------------#
#AutoLoadPlugin false

#-------------------------------------------------#
# Interval at which to query values. This may be 
# overwritten on a per-plugin base by using the 
# 'Interval' option of the LoadPlugin block:
#
#   <LoadPlugin foo>                                   
#       Interval 60                                                          
#   </LoadPlugin>                                                            #
#-------------------------------------------------#
#Interval 10

#Timeout 2
#ReadThreads 5
#WriteThreads 5

# Limit the size of the write queue. Default is no limit. Setting up a limit
# is recommended for servers handling a high volume of traffic.
#WriteQueueLimitHigh 1000000
#WriteQueueLimitLow   800000

####################################################
# Logging                                                                    #
#--------------------------------------------------#
# Plugins which provide logging functions should be 
# loaded first, so log messages generated when 
# loading or configuring other plugins can be
# accessed.                                                                  #
####################################################

#LoadPlugin logfile
LoadPlugin syslog

#<Plugin logfile>
#	LogLevel "info"
#	File STDOUT
#	Timestamp true
#	PrintSeverity false
#</Plugin>

<Plugin syslog>
	LogLevel info
</Plugin>

#######################################################
# LoadPlugin section                                                         #
#-----------------------------------------------------#
# Specify what features to activate.                                         #
#######################################################

LoadPlugin aggregation
#LoadPlugin amqp
#LoadPlugin apache
#LoadPlugin apcups
#LoadPlugin ascent
#LoadPlugin battery
#LoadPlugin bind
#LoadPlugin cgroups
#LoadPlugin conntrack
#LoadPlugin contextswitch
LoadPlugin cpu
#LoadPlugin cpufreq
#LoadPlugin csv
#LoadPlugin curl
#LoadPlugin curl_json
#LoadPlugin curl_xml
#LoadPlugin dbi
LoadPlugin df
LoadPlugin disk
#LoadPlugin dns
#LoadPlugin email
#LoadPlugin entropy
LoadPlugin ethstat
#LoadPlugin exec
#LoadPlugin filecount
#LoadPlugin fscache
#LoadPlugin gmond
#LoadPlugin hddtemp
LoadPlugin interface
#LoadPlugin ipmi
#LoadPlugin iptables
#LoadPlugin ipvs
#LoadPlugin irq
#LoadPlugin java
#LoadPlugin libvirt
LoadPlugin load
#LoadPlugin lvm
#LoadPlugin madwifi
#LoadPlugin mbmon
#LoadPlugin md
#LoadPlugin memcachec
#LoadPlugin memcached
LoadPlugin memory
#LoadPlugin modbus
#LoadPlugin multimeter
#LoadPlugin mysql
#LoadPlugin netlink
#LoadPlugin network
#LoadPlugin nfs
#LoadPlugin nginx
#LoadPlugin notify_desktop
#LoadPlugin notify_email
#LoadPlugin ntpd
#LoadPlugin numa
#LoadPlugin nut
#LoadPlugin olsrd
#LoadPlugin openvpn
#<LoadPlugin perl>
#	Globals true
#</LoadPlugin>
#LoadPlugin pinba
#LoadPlugin ping
#LoadPlugin postgresql
#LoadPlugin powerdns
#LoadPlugin processes
#LoadPlugin protocols
#<LoadPlugin python>
#	Globals true
#</LoadPlugin>
#LoadPlugin rrdcached
LoadPlugin rrdtool
#LoadPlugin sensors
#LoadPlugin serial
#LoadPlugin snmp
#LoadPlugin statsd
LoadPlugin swap
#LoadPlugin table
#LoadPlugin tail
#LoadPlugin tail_csv
#LoadPlugin tcpconns
#LoadPlugin teamspeak2
#LoadPlugin ted
#LoadPlugin thermal
#LoadPlugin tokyotyrant
#LoadPlugin unixsock
LoadPlugin uptime
LoadPlugin users
#LoadPlugin uuid
#LoadPlugin varnish
#LoadPlugin vmem
#LoadPlugin vserver
#LoadPlugin wireless
#LoadPlugin write_graphite
#LoadPlugin write_http
#LoadPlugin write_riemann

#######################################################
# Plugin configuration                                                       #
#-----------------------------------------------------#
# In this section configuration stubs for each plugin 
# are provided. A description of those options is 
# available in the collectd.conf(5) manual page.
#
#######################################################

<Plugin "aggregation">
	<Aggregation>
		Plugin "cpu"
		Type "cpu"
		GroupBy "Host"
		GroupBy "TypeInstance"
		CalculateNum true
		CalculateSum true
		CalculateAverage true
		CalculateMinimum true
		CalculateMaximum true
		CalculateStddev true
	</Aggregation>
</Plugin>

#<Plugin amqp>
#	<Publish "name">
#		Host "localhost"
#		Port "5672"
#		VHost "/"
#		User "guest"
#		Password "guest"
#		Exchange "amq.fanout"
#		RoutingKey "collectd"
#		Persistent false
#		StoreRates false
#	</Publish>
#</Plugin>

#<Plugin apache>
#	<Instance "foo">
#		URL "http://localhost/server-status?auto"
#		User "www-user"
#		Password "secret"
#		VerifyPeer false
#		VerifyHost false
#		CACert "/etc/ssl/ca.crt"
#		Server "apache"
#	</Instance>
#
#	<Instance "bar">
#		URL "http://some.domain.tld/status?auto"
#		Host "some.domain.tld"
#		Server "lighttpd"
#	</Instance>
#</Plugin>

#<Plugin apcups>
#	Host "localhost"
#	Port "3551"
#	ReportSeconds true
#</Plugin>

#<Plugin ascent>
#	URL "http://localhost/ascent/status/"
#	User "www-user"
#	Password "secret"
#	VerifyPeer false
#	VerifyHost false
#	CACert "/etc/ssl/ca.crt"
#</Plugin>

#<Plugin "bind">
#	URL "http://localhost:8053/"
#
#	ParseTime false
#
#	OpCodes true
#	QTypes true
#	ServerStats true
#	ZoneMaintStats true
#	ResolverStats false
#	MemoryStats true
#
#	<View "_default">
#		QTypes true
#		ResolverStats true
#		CacheRRSets true
#
#		Zone "127.in-addr.arpa/IN"
#	</View>
#</Plugin>

#<Plugin "cgroups">
#	CGroup "libvirt"
#	IgnoreSelected false
#</Plugin>

#<Plugin csv>
#	DataDir "/var/lib/collectd/csv"
#	StoreRates false
#</Plugin>

#<Plugin curl>
#	<Page "stock_quotes">
#		URL "http://finance.google.com/finance?q=NYSE%3AAMD"
#		User "foo"
#		Password "bar"
#		VerifyPeer false
#		VerifyHost false
#		CACert "/etc/ssl/ca.crt"
#		MeasureResponseTime false
#		<Match>
#			Regex "<span +class=\"pr\"[^>]*> *([0-9]*\\.[0-9]+) *</span>"
#			DSType "GaugeAverage"
#			Type "stock_value"
#			Instance "AMD"
#		</Match>
#	</Page>
#</Plugin>

#<Plugin curl_json>
## See: http://wiki.apache.org/couchdb/Runtime_Statistics
#  <URL "http://localhost:5984/_stats">
#    Instance "httpd"
#    <Key "httpd/requests/count">
#      Type "http_requests"
#    </Key>
#
#    <Key "httpd_request_methods/*/count">
#      Type "http_request_methods"
#    </Key>
#
#    <Key "httpd_status_codes/*/count">
#      Type "http_response_codes"
#    </Key>
#  </URL>
## Database status metrics:
#  <URL "http://localhost:5984/_all_dbs">
#    Instance "dbs"
#    <Key "*/doc_count">
#      Type "gauge"
#    </Key>
#    <Key "*/doc_del_count">
#      Type "counter"
#    </Key>
#    <Key "*/disk_size">
#      Type "bytes"
#    </Key>
#  </URL>
#</Plugin>

#<Plugin "curl_xml">
#	<URL "http://localhost/stats.xml">
#		Host "my_host"
#		Instance "some_instance"
#		User "collectd"
#		Password "thaiNg0I"
#		VerifyPeer true
#		VerifyHost true
#		CACert "/path/to/ca.crt"
#
#		<XPath "table[@id=\"magic_level\"]/tr">
#			Type "magic_level"
#			InstancePrefix "prefix-"
#			InstanceFrom "td[1]"
#			ValuesFrom "td[2]/span[@class=\"level\"]"
#		</XPath>
#	</URL>
#</Plugin>

#<Plugin dbi>
#	<Query "num_of_customers">
#		Statement "SELECT 'customers' AS c_key, COUNT(*) AS c_value \
#				FROM customers_tbl"
#		MinVersion 40102
#		MaxVersion 50042
#		<Result>
#			Type "gauge"
#			InstancePrefix "customer"
#			InstancesFrom "c_key"
#			ValuesFrom "c_value"
#		</Result>
#	</Query>
#
#	<Database "customers_db">
#		Driver "mysql"
#		DriverOption "host" "localhost"
#		DriverOption "username" "collectd"
#		DriverOption "password" "secret"
#		DriverOption "dbname" "custdb0"
#		SelectDB "custdb0"
#		Query "num_of_customers"
#		Query "..."
#		Host "..."
#	</Database>
#</Plugin>

<Plugin df>
#	Device "/dev/sda1"
#	Device "192.168.0.2:/mnt/nfs"
#	MountPoint "/home"
#	FSType "ext3"

	# ignore rootfs; else, the root file-system would appear twice, causing
	# one of the updates to fail and spam the log
	FSType rootfs
	# ignore the usual virtual / temporary file-systems
	FSType sysfs
	FSType proc
	FSType devtmpfs
	FSType devpts
	FSType tmpfs
	FSType fusectl
	FSType cgroup
	IgnoreSelected true

#	ReportByDevice false
#	ReportReserved false
#	ReportInodes false

#	ValuesAbsolute true
#	ValuesPercentage false
</Plugin>

<Plugin disk>
	Disk "hda"
	Disk "/sda[23]/"
	IgnoreSelected false
</Plugin>

#<Plugin dns>
#	Interface "eth0"
#	IgnoreSource "192.168.0.1"
#	SelectNumericQueryTypes false
#</Plugin>

#<Plugin email>
#	SocketFile "/var/run/collectd-email"
#	SocketGroup "collectd"
#	SocketPerms "0770"
#	MaxConns 5
#</Plugin>

<Plugin ethstat>
	Interface "eth0"
	Map "rx_csum_offload_errors" "if_rx_errors" "checksum_offload"
	Map "multicast" "if_multicast"
	MappedOnly false
</Plugin>

#<Plugin exec>
#	Exec user "/path/to/exec"
#	Exec "user:group" "/path/to/exec"
#	NotificationExec user "/path/to/exec"
#</Plugin>

#<Plugin filecount>
#	<Directory "/path/to/dir">
#		Instance "foodir"
#		Name "*.conf"
#		MTime "-5m"
#		Size "+10k"
#		Recursive true
#		IncludeHidden false
#	</Directory>
#</Plugin>

#<Plugin gmond>
#	MCReceiveFrom "239.2.11.71" "8649"
#
#	<Metric "swap_total">
#		Type "swap"
#		TypeInstance "total"
#		DataSource "value"
#	</Metric>
#
#	<Metric "swap_free">
#		Type "swap"
#		TypeInstance "free"
#		DataSource "value"
#	</Metric>
#</Plugin>

#<Plugin hddtemp>
#	Host "127.0.0.1"
#	Port 7634
#</Plugin>

<Plugin interface>
	Interface "eth0"
	IgnoreSelected false
</Plugin>

#<Plugin ipmi>
#	Sensor "some_sensor"
#	Sensor "another_one"
#	IgnoreSelected false
#	NotifySensorAdd false
#	NotifySensorRemove true
#	NotifySensorNotPresent false
#</Plugin>

#<Plugin iptables>
#	Chain "table" "chain"
#</Plugin>

#<Plugin irq>
#	Irq 7
#	Irq 8
#	Irq 9
#	IgnoreSelected true
#</Plugin>

#<Plugin java>
#	JVMArg "-verbose:jni"
#	JVMArg "-Djava.class.path=/usr/share/collectd/java/collectd-api.jar"
#
#	LoadPlugin "org.collectd.java.GenericJMX"
#	<Plugin "GenericJMX">
#		# See /usr/share/doc/collectd/examples/GenericJMX.conf
#		# for an example config.
#	</Plugin>
#</Plugin>

#<Plugin libvirt>
#	Connection "xen:///"
#	RefreshInterval 60
#	Domain "name"
#	BlockDevice "name:device"
#	InterfaceDevice "name:device"
#	IgnoreSelected false
#	HostnameFormat name
#	InterfaceFormat name
#</Plugin>

#<Plugin madwifi>
#	Interface "wlan0"
#	IgnoreSelected false
#	Source "SysFS"
#	WatchSet "None"
#	WatchAdd "node_octets"
#	WatchAdd "node_rssi"
#	WatchAdd "is_rx_acl"
#	WatchAdd "is_scan_active"
#</Plugin>

#<Plugin mbmon>
#	Host "127.0.0.1"
#	Port 411
#</Plugin>

#<Plugin md>
#	Device "/dev/md0"
#	IgnoreSelected false
#</Plugin>

#<Plugin memcachec>
#	<Page "plugin_instance">
#		Server "localhost"
#		Key "page_key"
#		<Match>
#			Regex "(\\d+) bytes sent"
#			ExcludeRegex "<lines to be excluded>"
#			DSType CounterAdd
#			Type "ipt_octets"
#			Instance "type_instance"
#		</Match>
#	</Page>
#</Plugin>

#<Plugin memcached>
#	<Instance "local">
#		Socket "/var/run/memcached.sock"
# or:
#		Host "127.0.0.1"
#		Port "11211"
#	</Instance>
#</Plugin>

#<Plugin modbus>
#	<Data "data_name">
#		RegisterBase 1234
#		RegisterType float
#		Type gauge
#		Instance "..."
#	</Data>
#
#	<Host "name">
#		Address "addr"
#		Port "1234"
#		Interval 60
#
#		<Slave 1>
#			Instance "foobar" # optional
#			Collect "data_name"
#		</Slave>
#	</Host>
#</Plugin>

#<Plugin mysql>
#	<Database db_name>
#		Host "database.serv.er"
#		Port "3306"
#		User "db_user"
#		Password "secret"
#		Database "db_name"
#		MasterStats true
#	</Database>
#
#	<Database db_name2>
#		Host "localhost"
#		Socket "/var/run/mysql/mysqld.sock"
#		SlaveStats true
#		SlaveNotifications true
#	</Database>
#</Plugin>

#<Plugin netlink>
#	Interface "All"
#	VerboseInterface "All"
#	QDisc "eth0" "pfifo_fast-1:0"
#	Class "ppp0" "htb-1:10"
#	Filter "ppp0" "u32-1:0"
#	IgnoreSelected false
#</Plugin>

#<Plugin network>
#	# client setup:
#	Server "ff18::efc0:4a42" "25826"
#	<Server "239.192.74.66" "25826">
#		SecurityLevel Encrypt
#		Username "user"
#		Password "secret"
#		Interface "eth0"
#	</Server>
#	TimeToLive "128"
#
#	# server setup:
#	Listen "ff18::efc0:4a42" "25826"
#	<Listen "239.192.74.66" "25826">
#		SecurityLevel Sign
#		AuthFile "/etc/collectd/passwd"
#		Interface "eth0"
#	</Listen>
#	MaxPacketSize 1024
#
#	# proxy setup (client and server as above):
#	Forward true
#
#	# statistics about the network plugin itself
#	ReportStats false
#
#	# "garbage collection"
#	CacheFlush 1800
#</Plugin>

#<Plugin nginx>
#	URL "http://localhost/status?auto"
#	User "www-user"
#	Password "secret"
#	VerifyPeer false
#	VerifyHost false
#	CACert "/etc/ssl/ca.crt"
#</Plugin>

#<Plugin notify_desktop>
#	OkayTimeout 1000
#	WarningTimeout 5000
#	FailureTimeout 0
#</Plugin>

#<Plugin notify_email>
#	SMTPServer "localhost"
#	SMTPPort 25
#	SMTPUser "my-username"
#	SMTPPassword "my-password"
#	From "collectd@main0server.com"
#	# <WARNING/FAILURE/OK> on <hostname>.
#	# Beware! Do not use not more than two placeholders (%)!
#	Subject "[collectd] %s on %s!"
#	Recipient "email1@domain1.net"
#	Recipient "email2@domain2.com"
#</Plugin>

<Plugin ntpd>
	Host "localhost"
	Port 123
	ReverseLookups false
	IncludeUnitID true
</Plugin>

#<Plugin nut>
#	UPS "upsname@hostname:port"
#</Plugin>

#<Plugin olsrd>
#	Host "127.0.0.1"
#	Port "2006"
#	CollectLinks "Summary"
#	CollectRoutes "Summary"
#	CollectTopology "Summary"
#</Plugin>

#<Plugin openvpn>
#	StatusFile "/etc/openvpn/openvpn-status.log"
#	ImprovedNamingSchema false
#	CollectCompression true
#	CollectIndividualUsers true
#	CollectUserCount false
#</Plugin>

#<Plugin perl>
#	IncludeDir "/my/include/path"
#	BaseName "Collectd::Plugins"
#	EnableDebugger ""
#	LoadPlugin Monitorus
#	LoadPlugin OpenVZ
#
#	<Plugin foo>
#		Foo "Bar"
#		Qux "Baz"
#	</Plugin>
#</Plugin>

#<Plugin pinba>
#	Address "::0"
#	Port "30002"
#	<View "name">
#		Host "host name"
#		Server "server name"
#		Script "script name"
#	<View>
#</Plugin>

#<Plugin ping>
#	Host "host.foo.bar"
#	Host "host.baz.qux"
#	Interval 1.0
#	Timeout 0.9
#	TTL 255
#	SourceAddress "1.2.3.4"
#	Device "eth0"
#	MaxMissed -1
#</Plugin>

#<Plugin postgresql>
#	<Query magic>
#		Statement "SELECT magic FROM wizard WHERE host = $1;"
#		Param hostname
#
#		<Result>
#			Type gauge
#			InstancePrefix "magic"
#			ValuesFrom "magic"
#		</Result>
#	</Query>
#
#	<Query rt36_tickets>
#		Statement "SELECT COUNT(type) AS count, type \
#		                  FROM (SELECT CASE \
#		                               WHEN resolved = 'epoch' THEN 'open' \
#		                               ELSE 'resolved' END AS type \
#		                               FROM tickets) type \
#		                  GROUP BY type;"
#
#		<Result>
#			Type counter
#			InstancePrefix "rt36_tickets"
#			InstancesFrom "type"
#			ValuesFrom "count"
#		</Result>
#	</Query>
#
#	<Writer sqlstore>
#		# See /usr/share/doc/collectd-core/examples/postgresql/collectd_insert.sql for details
#		Statement "SELECT collectd_insert($1, $2, $3, $4, $5, $6, $7, $8, $9);"
#		StoreRates true
#	</Writer>
#
#	<Database foo>
#		Host "hostname"
#		Port 5432
#		User "username"
#		Password "secret"
#
#		SSLMode "prefer"
#		KRBSrvName "kerberos_service_name"
#
#		Query magic
#	</Database>
#
#	<Database bar>
#		Interval 60
#		Service "service_name"
#
#		Query backend # predefined
#		Query rt36_tickets
#	</Database>
#
#	<Database qux>
#		Service "collectd_store"
#		Writer sqlstore
#		# see collectd.conf(5) for details
#		CommitInterval 30
#	</Database>
#</Plugin>

#<Plugin powerdns>
#	<Server "server_name">
#		Collect "latency"
#		Collect "udp-answers" "udp-queries"
#		Socket "/var/run/pdns.controlsocket"
#	</Server>
#	<Recursor "recursor_name">
#		Collect "questions"
#		Collect "cache-hits" "cache-misses"
#		Socket "/var/run/pdns_recursor.controlsocket"
#	</Recursor>
#	LocalSocket "/opt/collectd/var/run/collectd-powerdns"
#</Plugin>

#<Plugin processes>
#	Process "name"
#	ProcessMatch "foobar" "/usr/bin/perl foobar\\.pl.*"
#</Plugin>

#<Plugin protocols>
#	Value "/^Tcp:/"
#	IgnoreSelected false
#</Plugin>

#<Plugin python>
#	ModulePath "/path/to/your/python/modules"
#	LogTraces true
#	Interactive true
#	Import "spam"
#
#	<Module spam>
#		spam "wonderful" "lovely"
#	</Module>
#</Plugin>

#<Plugin rrdcached>
#	DaemonAddress "unix:/var/run/rrdcached.sock"
#	DataDir "/var/lib/rrdcached/db/collectd"
#	CreateFiles true
#	CreateFilesAsync false
#	CollectStatistics true
#
# The following settings are rather advanced
# and should usually not be touched:
#	StepSize 10
#	HeartBeat 20
#	RRARows 1200
#	RRATimespan 158112000
#	XFF 0.1
#</Plugin>

<Plugin rrdtool>
	DataDir ""
#	CacheTimeout 120
#	CacheFlush 900
#	WritesPerSecond 30
#	CreateFilesAsync false
#	RandomTimeout 0
#
# The following settings are rather advanced
# and should usually not be touched:
#	StepSize 10
#	HeartBeat 20
#	RRARows 1200
#	RRATimespan 158112000
#	XFF 0.1
</Plugin>

#<Plugin sensors>
#	SensorConfigFile "/etc/sensors3.conf"
#	Sensor "it8712-isa-0290/temperature-temp1"
#	Sensor "it8712-isa-0290/fanspeed-fan3"
#	Sensor "it8712-isa-0290/voltage-in8"
#	IgnoreSelected false
#</Plugin>

# See /usr/share/doc/collectd/examples/snmp-data.conf.gz for a
# comprehensive sample configuration.
#<Plugin snmp>
#	<Data "powerplus_voltge_input">
#		Type "voltage"
#		Table false
#		Instance "input_line1"
#		Scale 0.1
#		Values "SNMPv2-SMI::enterprises.6050.5.4.1.1.2.1"
#	</Data>
#	<Data "hr_users">
#		Type "users"
#		Table false
#		Instance ""
#		Shift -1
#		Values "HOST-RESOURCES-MIB::hrSystemNumUsers.0"
#	</Data>
#	<Data "std_traffic">
#		Type "if_octets"
#		Table true
#		InstancePrefix "traffic"
#		Instance "IF-MIB::ifDescr"
#		Values "IF-MIB::ifInOctets" "IF-MIB::ifOutOctets"
#	</Data>
#
#	<Host "some.switch.mydomain.org">
#		Address "192.168.0.2"
#		Version 1
#		Community "community_string"
#		Collect "std_traffic"
#		Inverval 120
#	</Host>
#	<Host "some.server.mydomain.org">
#		Address "192.168.0.42"
#		Version 2
#		Community "another_string"
#		Collect "std_traffic" "hr_users"
#	</Host>
#	<Host "some.ups.mydomain.org">
#		Address "192.168.0.3"
#		Version 1
#		Community "more_communities"
#		Collect "powerplus_voltge_input"
#		Interval 300
#	</Host>
#</Plugin>

#<Plugin statsd>
#	Host "::"
#	Port "8125"
#	DeleteCounters false
#	DeleteTimers   false
#	DeleteGauges   false
#	DeleteSets     false
#	TimerPercentile 90.0
#</Plugin>

<Plugin swap>
	ReportByDevice false
	ReportBytes true
</Plugin>

#<Plugin table>
#	<Table "/proc/slabinfo">
#		Instance "slabinfo"
#		Separator " "
#		<Result>
#			Type gauge
#			InstancePrefix "active_objs"
#			InstancesFrom 0
#			ValuesFrom 1
#		</Result>
#		<Result>
#			Type gauge
#			InstancePrefix "objperslab"
#			InstancesFrom 0
#			ValuesFrom 4
#		</Result>
#	</Table>
#</Plugin>

#<Plugin "tail">
#	<File "/var/log/exim4/mainlog">
#		Instance "exim"
#		<Match>
#			Regex "S=([1-9][0-9]*)"
#			DSType "CounterAdd"
#			Type "ipt_bytes"
#			Instance "total"
#		</Match>
#		<Match>
#			Regex "\\<R=local_user\\>"
#			ExcludeRegex "\\<R=local_user\\>.*mail_spool defer"
#			DSType "CounterInc"
#			Type "counter"
#			Instance "local_user"
#		</Match>
#	</File>
#</Plugin>

#<Plugin "tail_csv">
#	<Metric "dropped">
#		Type "percent"
#		Instance "dropped"
#		ValueFrom 1
#	</Metric>
#	<Metric "mbps">
#		Type "bytes"
#		Instance "wire-realtime"
#		ValueFrom 2
#	</Metric>
#	<Metric "alerts">
#		Type "alerts_per_second"
#		ValueFrom 3
#	</Metric>
#	<Metric "kpps">
#		Type "kpackets_wire_per_sec.realtime"
#		ValueFrom 4
#	</Metric>
#	<File "/var/log/snort/snort.stats">
#		Instance "snort-eth0"
#		Interval 600
#		Collect "dropped" "mbps" "alerts" "kpps"
#		TimeFrom 0
#	</File>
#</Plugin>

#<Plugin tcpconns>
#	ListeningPorts false
#	LocalPort "25"
#	RemotePort "25"
#</Plugin>

#<Plugin teamspeak2>
#	Host "127.0.0.1"
#	Port "51234"
#	Server "8767"
#</Plugin>

#<Plugin ted>
#	Device "/dev/ttyUSB0"
#	Retries 0
#</Plugin>

#<Plugin thermal>
#	ForceUseProcfs false
#	Device "THRM"
#	IgnoreSelected false
#</Plugin>

#<Plugin tokyotyrant>
#	Host "localhost"
#	Port "1978"
#</Plugin>

#<Plugin unixsock>
#	SocketFile "/var/run/collectd-unixsock"
#	SocketGroup "collectd"
#	SocketPerms "0660"
#	DeleteSocket false
#</Plugin>

#<Plugin uuid>
#	UUIDFile "/etc/uuid"
#</Plugin>

#<Plugin varnish>
#	<Instance>
#		CollectCache true
#		CollectBackend true
#		CollectBan false           # Varnish 3 only
#		CollectConnections true
#		CollectDirectorDNS false   # Varnish 3 only
#		CollectSHM true
#		CollectESI false
#		CollectFetch false
#		CollectHCB false
#		CollectObjects false
#		CollectPurge false         # Varnish 2 only
#		CollectSession false
#		CollectSMA false           # Varnish 2 only
#		CollectSMS false
#		CollectSM false            # Varnish 2 only
#		CollectStruct false
#		CollectTotals false
#		CollectUptime false
#		CollectdVCL false
#		CollectWorkers false
#	</Instance>
#
#	<Instance "myinstance">
#		CollectCache true
#	</Instance>
#</Plugin>

#<Plugin vmem>
#	Verbose false
#</Plugin>

#<Plugin write_graphite>
#	<Node "example">
#		Host "localhost"
#		Port "2003"
#		Protocol "tcp"
#		LogSendErrors true
#		Prefix "collectd"
#		Postfix "collectd"
#		StoreRates true
#		AlwaysAppendDS false
#		EscapeCharacter "_"
#	</Node>
#</Plugin>

#<Plugin write_http>
#	<URL "http://example.com/collectd-post">
#		User "collectd"
#		Password "secret"
#		VerifyPeer true
#		VerifyHost true
#		CACert "/etc/ssl/ca.crt"
#		Format "Command"
#		StoreRates false
#	</URL>
#</Plugin>

#<Plugin write_riemann>
#	<Node "example">
#		Host "localhost"
#		Port 5555
#		Protocol UDP
#		StoreRates true
#		AlwaysAppendDS false
#		TTLFactor 2.0
#	</Node>
#	Tag "foobar"
#</Plugin>

<Include "/etc/collectd/collectd.conf.d">
	Filter "*.conf"
</Include>

EOT

```

1. Test the configuration
`collectd -t`
`collectd -T`

1. Remove any old data
`sudo rm -r /var/lib/collectd/rrd/*`
1. Start collectd
`sudo /etc/init.d/collectd enable && sudo /etc/init.d/collectd start`
1. Verify the creation of the RRD files
`ls /var/lib/collectd/us1-n873h`

##Update packages
1. Log in with the local host name
`ssh -p 49022 pi@us1-n873h.local`
1. Update the apt package manager
	`sudo apt-get update && apt-get dist-upgrade`
	
##Backing up the SD Card / Create the master image 
https://www.raspberrypi.org/documentation/linux/filesystem/backup.md

1. With power off to the RPi, remove the SD card.
1. Insert a SD card into your computer
1. Open a terminal window

1. Find out the ID of the SD Card
	`diskutil list` => /dev/disk3
1. Copy the image on the card to the computer
	`sudo dd bs=4m if=/dev/rdisk3 of=/Users/brianmcmillan/Documents/systemimages/raspberrypi/master/raspbian_base_20151120.img`

This will take some time (10-30 min.) and if you are impatient, check the status with `[CTRL] + t`.

This image can be used to bootstrap a RPi without having to reconfigure an image from scratch.

##Copy a master image to a new SD card
1. Insert a SD card into your computer
1. Find out the ID of the SD Card
	`diskutil list` => /dev/disk3
1. Unmount the SD Card
	`diskutil unmountDisk /dev/disk3`
1. Copy the image for the card
	`sudo dd bs=4m if=/Users/brianmcmillan/Documents/systemimages/raspberrypi/master/raspbian_base_20151120.img of=/dev/rdisk3`
1. Unmount the SD Card
	`diskutil unmountDisk /dev/disk3`

##Shutting down safely
`sudo shutdown -h now` or `sudo halt`
`sudo shutdown -r now` or `sudo reboot`





---
---


###Deleting a user
1. Log in with the local host name
`ssh -p 49022 pi@us1-n873h.local`  
1. Change to the root user
	`sudo -i`	
1. List all users
`cat /etc/passwd/`
1. Identify any extra users in the system
`pi:x:1000:1000:,,,:/home/pi:/bin/bash`
1. Delete the user and their home directory
`userdel -r pi`




 




---

###Internal sensor data
https://www.maketecheasier.com/monitor-pc-temperature-linux/

###Manually editing the RPi config file
https://www.raspberrypi.org/documentation/configuration/config-txt.md

###Configuring Network Time Protocol (NTP)
```
ntpq -pn

sudo cat /etc/ntp.conf
# pool.ntp.org maps to about 1000 low-stratum NTP servers.  Your server will
# pick a different set every time it starts up.  Please consider joining the
# pool: <http://www.pool.ntp.org/join.html>
server 0.debian.pool.ntp.org iburst
server 1.debian.pool.ntp.org iburst
server 2.debian.pool.ntp.org iburst
server 3.debian.pool.ntp.org iburst

# If you want to provide time to your local subnet, change the next line.
# (Again, the address is an example only.)
#broadcast 192.168.123.255

```
###Connecting external drives
1. Log in with the local host name
`ssh -p 49022 pi@us1-n873h.local`
1. Identify the name of the disk (USB drive) and any partitions
`sudo fdisk -l`

```
...
Device         Boot  Start      End  Sectors  Size Id Type
/dev/mmcblk0p1        8192   122879   114688   56M  c W95 FAT32 (LBA)
/dev/mmcblk0p2      122880 15523839 15400960  7.4G 83 Linux

Disk /dev/sda: 59.2 GiB, 63518539776 bytes, 124059648 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: AEA9F32A-6E09-422F-8182-56B826FE5AC7

Device      Start       End   Sectors  Size Type
/dev/sda1      40    409639    409600  200M EFI System
/dev/sda2  411648 124057599 123645952   59G Microsoft basic data
```

1. Format the partition
`sudo mkfs.ext4 /dev/sda2 -L usb1`
1. Verify the partitions
`sudo blkid`

```
/dev/sda2: LABEL="usb1" UUID="f3d96949-ef26-4723-b35a-6580aa3a7016" TYPE="ext4" PARTLABEL="UNTITLED" PARTUUID="d3b7ff3f-6857-4312-ad0e-1f4922603cc2"
/dev/mmcblk0p1: SEC_TYPE="msdos" LABEL="boot" UUID="74BD-74CF" TYPE="vfat" PARTUUID="ba2edfb9-01"
/dev/mmcblk0p2: UUID="ec2aa3d2-eee7-454e-8260-d145df5ddcba" TYPE="ext4" PARTUUID="ba2edfb9-02"
/dev/sda1: LABEL="EFI" UUID="67E3-17ED" TYPE="vfat" PARTLABEL="EFI System Partition" PARTUUID="706c605b-91b0-4e84-affa-277db35daf4b"
/dev/mmcblk0: PTUUID="ba2edfb9" PTTYPE="dos"
```

1. Create a directory to mount the drive to  
`sudo mkdir /mnt/usb1`
1. Mount the drive  
`sudo mount -a /dev/sda2 /mnt/usb1`
1. Set the ownership of the directory  
`sudo chown -R pi:pi /mnt/usb1`
1. Set the permissions on the directory  
`sudo chmod -R 775 /mnt`
1. Verify the directory is writable  
`echo "some text" >> /mnt/usb1/test.txt && cat /mnt/usb1/test.txt`
1. Mount the USB drive at boot  
`sudo nano /etc/fstab`
1. Adding the following to the fstab file  
`/dev/sda2 /mnt ext4 defaults 0 0`
1. Save the file and exit  
1. Reboot  
`sudo reboot`
1. Log in with the local host name  
`ssh -p 49022 pi@us1-n873h.local`
1. Verify the USB drive is automatically mounted  
`echo "some text" >> /mnt/usb1/test.txt && cat /mnt/usb1/test.txt`
1. Delete the file  
`rm /mnt/usb1/test.txt`




###Setup fail2ban
http://blog.self.li/post/63281257339/raspberry-pi-part-1-basic-setup-without-cables

`sudo apt-get install fail2ban`
`sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local`
`sudo service fail2ban restart`
`sudo iptables -L`

###Other network services
http://automationguy.co.uk/home-lab/home-lab-build-part-12-raspberry-pi/
https://samhobbs.co.uk/2013/12/raspberry-pi-email-server-part-1-postfix



###System monitoring


###Installing Node
`wget https://nodejs.org/dist/v4.2.2/node-v4.2.2.tar.gz`  
`tar -zxf node-v4.2.2.tar.gz`
`cd node-v4.2.2.tar.gz`
`./configure`
`make`
`sudo make install`




###RTC
https://afterthoughtsoftware.com/products/rasclock




###Create an admin user
1. SSH into the RPi  
	`ssh pi@192.168.0.11`
1. Change to the root user  
	`sudo -i`	
1. Add an administrator user  
	`adduser --disabled-password --gecos GECOS admin`
1. Enable sudo for the admin user  
	`sudo visudo`	
1. Add the admin user to the file

```
#User privilege specification  
root    ALL=(ALL:ALL) ALL  
admin   ALL = NOPASSWD: ALL
```

1. Save and exit the editor  
`[CNTRL]+O`  
`[CNTRL]+X`



###Setting up a firewall
http://guides.webbynode.com/articles/security/ubuntu-ufw.html
http://blog.self.li/post/63281257339/raspberry-pi-part-1-basic-setup-without-cables

1. Install Uncomplicated Firewall  
`sudo apt-get install ufw`
1. Allow the SSH port  
`sudo ufw allow 49022`
1. Start the firewall on startup  
`sudo ufw enable`
1. View firewall rules
`sudo ufw status verbose`

1. RPi-Monitor
`sudo ufw allow 8888`



##Useful links
https://www.raspberrypi.org/documentation/installation/installing-images/README.md  

MacOS: https://www.raspberrypi.org/documentation/installation/installing-images/mac.md  

http://blog.self.li/post/63281257339/raspberry-pi-part-1-basic-setup-without-cables  

http://elinux.org/RPi_Advanced_Setup

https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md 

Dynamic hostname: http://www.howtogeek.com/167195/how-to-change-your-raspberry-pi-or-other-linux-devices-hostname/

http://pi.gadgetoid.com/article/who-watches-the-watcher

http://guides.webbynode.com/articles/security/ubuntu-ufw.html

https://github.com/AppThemes/salt-config-example/tree/master/monit/conf/conf.d

http://www.howtogeek.com/139433/how-to-turn-a-raspberry-pi-into-a-low-power-network-storage-device/

http://www.modmypi.com/blog/how-to-mount-an-external-hard-drive-on-the-raspberry-pi-raspian

http://rpi-experiences.blogspot.fr/p/rpi-monitor.html
http://rpi-experiences.blogspot.fr/p/rpi-monitor-installation.html

https://www.raspberrypi.org/documentation/configuration/raspi-config.md

https://mmonit.com/monit/
https://mmonit.com/monit/documentation/monit.html
http://ghosttx.com/2012/03/how-to-install-monit-on-ubuntu-11-10-server-to-monitor-apache-web-server-mysql-ssh/
https://www.digitalocean.com/community/tutorials/lemp-stack-monitoring-with-monit-on-ubuntu-14-04

http://www.howtogeek.com/167190/how-and-why-to-assign-the-.local-domain-to-your-raspberry-pi/  
http://linux.die.net/man/5/avahi-daemon.conf