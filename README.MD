## Table of Contents:

[**Raspberry Pi Zero W:**](#setting-up-raspberry-pi-zero)  
&emsp;[Materials](#materials)  
&emsp;[Setup Procedure](#setup-procedure)  
&emsp;[Other Helpful Proceedures](#other-helpful-procedures)  
&emsp;&emsp;[Configure a Program to Run on Startup](#configure-a-program-to-run-on-startup)  
&emsp;&emsp;[Configure a Static IP](#configure-static-ip-address)  
[**Raspberry Pi 4 Model B:**](#pi-4-model-b-setup)  
&emsp;[OS Information](#os-information)  
&emsp;[System Preferences](#system-preferences)  
&emsp;&emsp;[Install Applications](#install-applications)  
&emsp;&emsp;[Setup Desktop](#set-up-desktop)  

# Setting up Raspberry Pi Zero

## Materials

- Raspberry Pi Zero
- MicroSD card - at least 4GB
- Mini HDMI Cable or adapter.
- MicroUSB cable or adapter
- 5V 2.5A power supply
- Monitor
- Keyboard
- 20x2 pin header

## Setup Procedure

> 1.  Download the imager: [Pi Imager installation](https://www.raspberrypi.com/software/)
> 2.  Follow imager instructions to install Lite OS to flash drive. (32-Bit Laegacy port of Bullseye)
> 3.  Copy ssh and wpa_supplicant to boot drive (make sure network and password are set in wpa_supplicant).
> 4.  Boot the Pi with flash drive inserted.
> 5.  Follow instructions on screen to set keyboard layout, username, and password.
>     **NOTE: If there is no video output, but you have a green light, there is likely a problem with the HDMI. SSH into device and apply updates that way.**
> 6.  Record username and password somewhere safe.
> 7.  Update system:
>
> ```bash
> sudo apt-get update
> sudo apt-get upgrade
> sudo reboot
> ```
>
> 8.  Install Git:
>
> ```bash
> sudo apt install git
> git config --global user.email "you@example.com"
> git config --global user.name "Your Name"
> ```

## Other Helpful Procedures

### Configure a Program to Run on Startup:

> 1.  Modify etc/rc.local with admin priviliges:
>
> ```bash
> sudo nano /etc/rc.local
> ```
>
> 2.  Add this line to /etc/rc.local:
>
> ```bash
> sudo python /home/pi/yourprogram/program.py &
> ```
>
> - **Replace `/home/pi/yourprogram/program.py` with your directory**
>
> - **Don't forget the `&` at the end to allow the program to run in the background**
>
> 3.  Stopping the process:
>
> - To stop the process, first find the PID number:
>
> ```bash
> ps aux | grep "main.py"
> ```
>
> - Note the number in the second column.
> - Ternimate Process:
>
> ```bash
> sudo kill -TERM ###
> ```
>
> - **replace "###" with the number from step 4**

### Configure Static IP Address:

> > [**Link to article on CircuitBasics.com**](https://www.circuitbasics.com/how-to-set-up-a-static-ip-on-the-raspberry-pi/#:~:text=If%20the%20IP%20address%20of,work%20each%20and%20every%20time)
>
> > Summary of Article
> >
> > 1. Find `Gateway` info for `wlan0`:
> >
> > ```bash
> > route -ne
> > ```
> >
> > 2. Nameserver:
> >
> > ```bash
> > cat /etc/resolv.conf
> > ```
> >
> > 3. Take a picture of the screen.
> >
> > 4. Configure settings:
> >
> > ```bash
> > sudo nano/etc/dhcpod.conf
> > ```
> >
> > - Add this line to the end of the file:
> >
> > ```bash
> > interface eth0
> > static ip_address=10.0.0.100
> > static routers=10.0.0.1
> > static domain_name_servers=75.75.75.75 75.75.76.76 2001:558:feed::1 2001:558:feed::2
> >
> > interface wlan0
> > static ip_address=10.0.0.99
> > static routers=10.0.0.1
> > static domain_name_servers=75.75.75.75 75.75.76.76 2001:558:feed::1 2001:558:feed::2
> > ```
> >
> > - static ip_address: This is the static IP address you’ll use to SSH or remotely connect to your Pi. Take your default gateway IP (found in the steps above), and change the last number to any other number between 0 and 255.
> > - static routers: This is your default gateway IP address.
> > - static domain_name_servers: These are the IP’s we found in the resolv.conf file above. Separate each IP with a single space.
> > - **Set `wlan0` differently than `etho0`**

---

# Pi 4 Model B Setup

## OS Information

> Newest working: Pi OS (Legacy) port of Bullseye 32bit. Use `config.txt` from the `/pi4` directory.
>
> Critical changes to `config.txt`  
> > Uncomment these lines:
> > - `hdmi_force_hotplug=1`
> > - `hdmi_group=2`
> > - `hdmi_mode=85` (Able to change to 82 for higher resolution after setup)
>
> **Summary:**
>
> > My Pi 4 was running Buster(v10). I had never run Bullseye(v11), but Bookworm(v12) is now the latest version at the time of writing. I attempted to create a new card on a 32GB microSD card (replicated this process across multiple card to verify card was not faulty). I was repeatedly unable to get video, but was able to verfiy device was booting because green light was active, and my old setup still worked when I put that SD card in the Pi. I went through many itteratons of trying to get the HDMI working. Ultimately the resolution boiled down to a few of things:
> >
> > > - The Bookworm system still did not boot with the working config.txt file above. I beleive there is more to this problem than just config.txt.
> > > - The working config file works in Bullsye(v11) 32-bit setup. The `config.txt` file located in the `/pi4` directry of this repo.
> > > - The changes listed above seemed to be the fixing changes.
>
> Port to Bookworm has been unsuccessful ths far, unable to get any of my current displays to function with it.

## System Preferences

### Install Applications

> **Firefox:**
>
> - Install through OS package installer: `Preferences` > `Add/Remove Software`  
> - `Mozilla Firefox web browser - Extended Support Release (ESR)`  
>
> **Visual Sudio Code:**  
>
> - Install through: `Preferences` > `Recommended Software`  
> - `Visual Studio Code`  
> - After Installing VS Code:
> &emsp;- Install Python Extension  
> &emsp;- Install Prettier Extension

### Set up Desktop

**Task bar items:**

- Right click on the launch area of the tas bar (the icons that are already pinned)  
- Click `Application Launch Bar Settings`  
- Add Firefox and Visual Studio to the taskbar. Remove Chromium.  

**Desktop Apearance:**

- Right click on the desktop and click `Desktop Appearance`.  
- Select background and color scheme. (I like aurora for the background, and dark green for taskbar.)

