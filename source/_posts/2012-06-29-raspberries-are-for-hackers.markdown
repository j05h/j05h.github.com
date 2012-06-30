---
layout: post
title: "Raspberries are for hackers"
date: 2012-06-29 23:06
comments: true
categories: [raspberrypi]
---

I just got 2 [Raspberry Pis](http://raspberrypi.org/) this week. I immediately ordered an [HP Touchpad charger](http://www.amazon.com/gp/product/B0055QYJJM/ref=as_li_ss_tl?ie=UTF8&tag=kleinpeterorg-20&linkCode=as2&camp=1789&creative=390957&creativeASIN=B0055QYJJM) and 2 [16GB SD cards](http://www.amazon.com/gp/product/B003VNKNEQ/ref=as_li_ss_tl?ie=UTF8&tag=kleinpeterorg-20&linkCode=as2&camp=1789&creative=390957&creativeASIN=B003VNKNEQ). Got those today, so I started setting up my first pi (pi).

First, I grabbed [debian6-19-04-2012.zip](http://downloads.raspberrypi.org/images/debian/6/debian6-19-04-2012/debian6-19-04-2012.zip) (SHA-1 1852df83a11ee7083ca0e5f3fb41f93ecc59b1c8).

Once downloaded, I was off to the races:

    shasum ~/Downloads/debian6-19-04-2012.zip
    # should be 1852df83a11ee7083ca0e5f3fb41f93ecc59b1c8
    unzip ~/Downloads/debian6-19-04-2012.zip
    df -h
    # pop in the SD card
    df -h
    # check to see what the new /dev device is
    diskutil unmount /dev/diskXs1
    # note that X will be the same for each disk
    sudo dd bs=1m if=~/Downloads/debian6-19-04-2012/debian6-19-04-2012.img of=/dev/rdiskX
    diskutil eject /dev/rdiskX

Now that's done, the SD card was ready, and I popped it in the Pi.

I want to use my Pi remotely, so I needed to set up an SSH server (obnoxious the OS doesn't have it preinstalled). That's fairly easy. I connected to my HDTV, keyboard, and ethernet.

To log in initially:

    User:pi
    Pass:raspberry

Now I just installed sshd:

    sudo apt-get install openssh-server

And also, found out my IP address (my router uses sticky DHCP).

    ifconfig eth0 | grep inet

At this point, I disconnected, and went back to the comfort of my man cave. If you don't remember your IP or it changes, you can check the hosts on your network with:

    arp -a #my pi shows up as ubuntu for some reason...

Now you can log in!
    ssh pi@192.168.1.XYZ #password 'raspberry' still

Now I went ahead and added my public key into ~/.ssh/authorized_hosts

Next, I wanted to be able to access my pi easily.

    echo 'pie' | sudo tee /etc/hostname
    sudo sed -i 's/raspberrypi/pie/g' /etc/hosts
    sudo apt-get install avahi-daemon
    sudo apt-get install libnss-mdns
    sudo sed -i 's/mdns4$/mdns4 mdns/' /etc/nsswitch.conf

I added the following to /etc/avahi/services/ssh.service

    <?xml version="1.0" standalone="no"?><!--*-nxml-*-->
    <!DOCTYPE service-group SYSTEM "avahi-service.dtd">
    <service-group>
      <name replate-wildcards="yes">%h SSH</name>
      <service>
        <type>_ssh._tcp</type>
        <port>22</port>
      </service>
    </service-group>

And restarted avahi:

    sudo /etc/init.d/avahi-daemon restart

Now, I can easily get to my host from my Mac using Bonjour.

    ssh pi@pie.local

Next task...I'm wondering if I can get this to work as a media server...

### References

> 1. [SD card setup](http://elinux.org/RPi_Easy_SD_Card_Setup)
> 2. [Partition SD card](http://elsmorian.com/post/23366148056/basic-raspberry-pi-setup)
> 3. [Avahi](http://kremalicious.com/ubuntu-as-mac-file-server-and-time-machine-volume/#avahi2)


