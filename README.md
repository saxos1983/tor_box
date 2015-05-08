# tor_box
### An all-inclusive Tor configuration for Raspberry Pi, serves as both a relay and personal Tor network
  
## Preface
tor-box is a Raspberry Pi based personal anonymizing Tor network, combined with relay functionality in order to support the volunteer-based Tor network. Upon completion of configuration, tor-box will supply a secure wireless network that one can simply connect to, and instantly anonymously browse the web, while running a relay in the background as a daemon.  Keep in mind that using an out-of-date browser while connected to tor-box is a security risk!
  
### Ready to get started?
Here is an extensive list of things you'll need before using tor-box:  
- A computer
- A fundamental understanding of Raspberry Pi configuration
- A Raspberry Pi (with power source)
- An ethernet cable
- A Raspberry Pi-compatible USB wireless adaptor
- An UP-TO-DATE browser on your personal computer
  
## Overview
We'll be breaking the setup into multiple parts. Everything in this tutorial will be done using command line, no user interface stuff.
- Setup and configuration of the Raspberry Pi
- Setting up SSH and the Avahi daemon (used to easiliy connect to the Raspberry Pi via SSH)
- Setting up the Raspberry Pi as a wireless access point
- Installing and configuring Tor
  
### Setup and Configuration
To begin, start by connecting a keyboard, mouse, monitor, and ethernet-based internet connection to your Raspberry Pi. Then, plug your Raspberry Pi into a power source.  Allow the startup sequence to commence.  You should now see a login query.  By default, the username is "pi" and the password is "raspberry"
```
login as: pi
pi@raspberrypi's password: raspberry
```
You should now see a basic command line with the following prefix:
```
pi@raspberrypi ~ $
```
You're ready to start giving your Pi some commands!  To begin, we need to configure the Raspberry Pi.  To see the configuration options, type in the following command:
```
pi@raspberrypi ~ $ raspi-config
```
When the options on the screen appear, use your arrow keys to navigate to "Advanced Options" and then navigate to "Enable/Disable SSH".  We're going to select "Enable" and hit enter.

Before exiting ```raspi-config``` be sure to change the timezone, display, and other miscellaneous settings to your choosing if you so desire.  This is optional, however.  When you're done, hit the ```esc``` key to exit configuration and go back to the command line.

You'll need to make note of your Pi's IP address.  You can find it by typing the following command:
```
pi@raspberrypi ~ $ ifconfig
```
The IP will be listed under ```eth0```.  Now, run a shutdown command to your Pi by typing the following:
```
pi@raspberrypi ~ $ sudo shutdown -h now
```
After the shutdown sequence has completed, unplug the keyboard, mouse, monitor, and power from the Raspberry Pi.  Leave the ethernet connected. We'll need that.  Once you've connected all of your hardware back up to your personal computer, plug the power source back in to the Pi.

### Setting up SSH and the Avahi Daemon
Dealing with hardware is a hassle, and we want a way to communicate with our Raspberry Pi without having to use the hardware from our personal computer.  Ideally, we could just send commands from our personal computer.  Let's set that up!

If you're a Linux or Mac OS X user, you're in luck, because you can simply use the SSH command! If you're a Windows user, go ahead and download the PuTTY client.  It's free and lightweight!

#####For OS X and Linux users
Open up a shell terminal, and type in the following command:
```
~ $ ssh pi@1.2.3.4
```
Replace ```1.2.3.4``` with the IP address of your Raspberry Pi.  You should be prompted for the password, and after typing it in, you're ready to communicate with your Pi from your personal computer!

#####For Windows users
Once you have PuTTY downloaded, open it up.  Under the ```Host Name (IP address)``` field, put the IP address of your Pi.  Leave the port as 22, and hit ```Open```. You'll be prompted for both a username and password.  Log in using ```pi``` and ```raspberry```.  You're now ready to send commands to your Pi as well!

#####The Avahi Daemon
The Avahi Daemon is a way to connect to your Raspberry Pi via SSH without even having to know the IP address of the Pi.  This makes connecting via SSH far more convenient. Installing the daemon is easy.  Use the following command to install the daemon:
```
pi@raspberrypi ~ $ sudo apt-get install avahi-daemon
```
It should download and install in a matter of seconds. Next, well run the following command to ensure the daemon is running when the Raspberry Pi starts up:
```
pi@raspberrypi ~ $ sudo update-rc.d avahi-daemon defaults
```
We can now ensure the daemon has started by running:
```
pi@raspberrypi ~ $ sudo /etc/init.d/avahi-daemon restart
```
Easy.  Now instead of using the Pi's IP address (which may or may not remain consistent) to connect via SSH, the Raspberry Pi can be addressed by ```raspberrypi.local```, regardless of the IP.

So, if you're using OS X or Linux, you can simply connect by typing:
```
pi@raspberrypi ~ $ ssh pi@raspberrypi.local
```
And if you're on Windows, simply type ```raspberrypi.local``` into the ```Host Name``` field, then click ```Open```. You're now in command of your Raspberry Pi no matter what IP address it attempts to hide behind!

### Setting up the Raspberry Pi as a Wireless Access Point
Instead of reinventing the wheel here, I'm going to redirect you to an easy-to-follow and well-written tutorial written by LadyAda at Adafruit for this step.  Return here when you're done, though! We're getting to the good part.

Visit the tutorial [HERE](https://learn.adafruit.com/setting-up-a-raspberry-pi-as-a-wifi-access-point/).

### Installing and Configuring Tor
Okay, from this point on, we'll need to be running all commands as root.  You're the boss, and you want your Raspberry Pi to know it!  Simply type in:
```
pi@raspberrypi ~ $ sudo -i
```
Now all the commands you type will be as a root user.  Your prompt should now look something like this:
```
root@raspberrypi:~#
```
Now, you can do all the typing to configure the IP tables and install Tor on your own, or you can simply run the shell script I created.  It couldn't be easier to run.  The script can be found in this repository [here](https://github.com/CMoncur/tor_box/blob/master/installtor.sh), titled ```installtor.sh```.  To run the script, all you need to do is type in the following command:
```
root@raspberrypi:~# curl -fsSL https://raw.githubusercontent.com/CMoncur/tor_box/master/installtor.sh | sudo sh
```
Allow Tor to install entirely.  Now, there's one more thing to do before we call it a day.  We have to install Tor. Open up a Nano editor of the Tor configuration file by typing:
```
root@raspberrypi:~# nano /etc/tor/torrc
```
Append the following settings to the bottom of the file:
```
Log notice file /var/log/tor/notices.log
VirtualAddrNetwork 10.192.0.0/10
AutomapHostsSuffixes .onion,.exit
AutomapHostsOnResolve 1
TransPort 9040
TransListenAddress 192.168.42.1
DNSPort 53
DNSListenAddress 192.168.42.1
```
Hit ```Ctrl-X``` to exit Nano, and hit ```yes``` to save and write out the changes to the file. Now, let's restart the Tor service by typing:
```
root@raspberrypi:~# service tor restart
```
You should see the following notifications:
```
[ ok ] Stopping tor daemon...done.
[ ok ] Starting tor daemon...done.
```
Tor is now configured!

### Wrapping it all up
You should now have a fully functioning wireless Tor access point!  Congratulations!  Try connecting to it from your desktop or laptop. The wireless network should be named whatever name you gave it while setting up your wireless access point.  Mine is named tor_box.

Once connected, the VERY FIRST thing you need to do is visit ```https://check.torproject.org/``` to ensure your Tor access point is up and running correctly.  You should see a congratulations message, followed by your IP alias.

I now give you permission to browse the web anonymously. Proceed.
