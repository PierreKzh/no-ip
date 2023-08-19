I'm probably not the first one to do this, but I had a need to use NO-IP's dynamic DNS service so I could easily configure devices and computers to access resources on my LAN when I'm away. For those less familiar with NO-IP, it is a free dynamic DNS service. Most home and small office connections to the Internet have a dynamic IP, which means the IP addresses changes most every time the connection from the router is established. Typically, a domain name requires a static IP address to work. Dynamic DNS  allows  a domain name (i.e subdomain.example.com) to use a dynamic IP by updating NO-IP with a new IP address when the IP address changes. NO-IP then associates that IP with the domain name so services can be setup against the domain rather than the IP that changes. This makes configuring things easier.

Many routers have a built in NO-IP client, but my router doesn't support NO-IP, and for whatever reason, I could never get the client that NO-IP recommended for a Linux host to work. I downloaded the source and compile it following the instructions, and it appeared to be running, but it never updated my IP address. There was no output to the console or to a log file either to diagnose the problem that I could find, so I decided to go about writing my own, which really wasn't that hard to do using a bash script and a few simple utilities on my Linux box. Here is my how-to guide and documentation for setting this up. I'm running Ubuntu server so I'm using apt and nano, but this should work on anything POSIX based such as Mac OS X, BSD, any flavor of Linux -- even the Raspberry Pi.

The script can be run as a single instance with command line parameters, as a daemon, using crontabs (a utility for running programs on a schedule) or as a Linux service. 

This script has one dependency, which is wget. This is usually installed by default on most Linux and BSD distros, but if not use your package manager to install it.

      sudo apt-get install wget

-or-

      sudo yum install wget


Usage:
------

      no-ip.sh -u=username -p=password -h=host.sample.com -d=true -l=/path/to/logfile.log


Parameters:

    -u, --username 

Username to logon to no-ip.com.

    -p, --password

Password to logon to no-ip.com.

    -h, --hostname

The domain name to update.

    -d, --detectip

Tells the script to detect your external IP address. This takes precedence over -i.

    -i, --ip

Maually sets the IP address to update. If neither -d or -i are specified, no-ip will use the IP address it detects.

    -n, --interval

When running the script as a daemon/service (see Installation), this will update no-ip every n minutes.

    -l, --logfile

Sets the path to a log file. This file must be writable.

    -c, --config

Sets the path to a config file. This file must be readable. Config file parameters take precedence over command line parameters.

    -m, --mail_dest

Set the remote email address.



Examples:
---------

    no-ip.sh 

Runs the script with the settings in the config file in the default locaion /etc/no-ip/no-ip.conf

    no-ip.sh -u=username -p=password -h=host.sample.com -d=true -l=/path/to/logfile.log

Does a one-time update, detecting the UP and logging the results

    no-ip.sh -c=/home/username/no-ip.conf

Run the script with the settings in the config file.

    no-ip.sh /dev/null 2>&1 &

Runs the script as a daemon with the settings in the config file in the default locaion /etc/no-ip/no-ip.conf.

Config File:
------------

The config file uses a simple syntax to define parameters. Any command line parameter (except --config) can also be specified in the config file. The same rules apply for the config file parameters as caommand line parameters (ie. "detectip" overrides "ip" if both are specified).

Example:

    user=username 
    password=password 
    hostname=hostname.example.com
    logfile=/var/log/no-ip.log
    interval=0
    detectip=true
	mail_dest=user@mail.com
