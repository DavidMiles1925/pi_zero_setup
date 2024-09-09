## Table of Contents:

[**Raspberry Pi Zero W (and Zero 2 W):**](#setting-up-raspberry-pi-zero)  
&emsp;[Materials](#materials)  
&emsp;[Setup Procedure](#setup-procedure)  
&emsp;[Other Helpful Proceedures](#other-helpful-procedures)  
&emsp;&emsp;[Configure a Program to Run on Startup](#configure-a-program-to-run-on-startup)  
&emsp;&emsp;[Configure a Static IP](#configure-static-ip-address)  
[**Raspberry Pi 4 Model B:**](#pi-4-model-b-setup)  
&emsp;[OS Information](#os-information)  
&emsp;[System Preferences](#system-preferences)  
&emsp;&emsp;[Install Applications](#install-applications)  
&emsp;&emsp;[Set GPU Memory](#set-gpu-memory)  
&emsp;&emsp;[Setup Desktop](#set-up-desktop)

# Setting up Raspberry Pi Zero

## Materials

- Raspberry Pi Zero W or Zero 2 W
- 20x2 pin header (if header is not included with Pi)
- MicroSD card - at least 4GB
- HDMI-to-Mini HDMI Cable or adapter.
- USB-to-MicroUSB cable or adapter
- 5V power supply, at least 2.5A
- Monitor
- Keyboard

## Setup Procedure

> > 1.  Download the Raspberry Pi imager on a separate computer: [Pi Imager installation](https://www.raspberrypi.com/software/)
>
> > 2.  Follow imager instructions to install Lite OS to flash drive. (32-Bit Laegacy port of Bullseye)
>
> > 3.  Download `ssh` file from above (or create a blank file and name it ssh). NOTE: This file has no contents. It's only purpose is to signal to the RPi that SSH should be enabled.
>
> > 4.  Copy the ssh file to the flash drive.
>
> > 5.  Remove the flash drive from the computer and place in the Raspberry Pi Zero 2 W.

> > 6.  Connect your peripherals and boot the device.
>
> > 7.  Once the device boots, follow instructions on screen to set keyboard layout, username, and password.  
> >     **NOTE: If there is no video output, but you have a green light, there is likely a problem with the HDMI. See troubleshooting below.**
>
> > 8.  Record username and password somewhere safe.
>
> > 9.  Configure your Wifi by typing this into the command line:
> >
> > ```bash
> > sudo raspi-config
> > ```
> >
> > &emsp;Select "System Settings" and then "Wireless LAN"  
> > &emsp;The "SSID" refers to the name of your network  
> > &emsp;The "psk" is your password.
>
> > 10.
>
> > 10. Set the timezone. From the raspi-config menu, select "4."
>
> > 11. Update your device
> >
> > ```bash
> > sudo apt-get update
> > sudo apt-get upgrade
> > sudo reboot
> > ```
>
> > 8.  Install Git:
> >
> > ```bash
> > sudo apt install git
> > git config --global user.email "you@example.com"
> > git config --global user.name "Your Name"
> > ```
> >
> > - **NOTE: You may need to generate a key to be able to clone:**  
> >    Documentation for creating a key: [Managing Your Personal Access Tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens)
> >
> >   Once a key is generated, configure git:
> >
> > ```bash
> > git config --global credential.helper store
> > sudo nano ~/.git-credentials
> > ```

> > 10. (Optional) [Configure Static IP Address](#configure-static-ip-address) >> &emsp;This is useful if you want to be able to access your device remotely via SSH.

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
> - **Replace `pi` with your directory**
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
> - _Note the number in the second column._
>
> - Ternimate Process:
>
> ```bash
> sudo kill -TERM ###
> ```
>
> - _replace "###" with the number from step 4_

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
> > Then find your `IP address`:
> >
> > ```bash
> > ip addr
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
> > sudo nano /etc/dhcpcd.conf
> > ```
> >
> > - Add this line to the end of the file:
> >
> > ```bash
> > interface eth0
> > static ip_address=192.168.1.100
> > static routers=192.168.1.1
> > static domain_name_servers=192.168.1.1 2001:558:feed::1 2001:558:feed::2
> >
> > interface wlan0
> > static ip_address=192.168.1.101
> > static routers=192.168.1.1
> > static domain_name_servers=192.168.1.1 2001:558:feed::1 2001:558:feed::2
> > ```
> >
> > - static ip_address: This is the static IP address you’ll use to SSH or remotely connect to your Pi. Take your default gateway IP (found in the steps above), and change the last number to any other number between 0 and 255.
> > - static routers: This is your default gateway IP address.
> > - static domain_name_servers: These are the IP’s we found in the resolv.conf file above. Separate each IP with a single space.
> > - **Set `wlan0` differently than `etho0`**

---

# Pi 4 Model B Setup

## OS Information

> **Newest working: `Pi OS (Legacy) port of Bullseye 32bit`. Use `config.txt` from the `/pi4` directory.**
>
> Critical changes to `config.txt`
>
> > Uncomment these lines:
> >
> > - `hdmi_force_hotplug=1`
> > - `hdmi_group=2`
> > - `hdmi_mode=85` (Able to change to 82 for higher resolution after setup)
>
> **Summary:**
>
> > My Pi 4 was running `Buster(*v10*)`. I had never run `Bullseye(*v11*)`, but `Bookworm(*v12*)` is now the latest version at the time of writing. I attempted to create a new OS image on a 32GB microSD card (I replicated this process across multiple cards to verify they were not faulty). I tried both `Bookworm` and `Bullseye` in both 32 and 64 bit versions. I was repeatedly unable to get video, but was able to verfiy device was booting because green light was active, and my old setup still worked when I put that SD card in the Pi. The `config.txt` file from `Buster` did not work with either of the newer versions. I went through many itteratons of trying to get the HDMI working. Ultimately the resolution boiled down to a few of things:
> >
> > > - The `Bookworm` system still did not boot with the working config.txt file above. I beleive there is more to this problem than just `config.txt`.
> > > - The working config file works with the `Bullsye` 32-bit setup. The `config.txt` file located in the `/pi4` directry of this repo.
> > > - The changes listed above seemed to be the fixing changes.
>
> A port to `Bookworm` has been unsuccessful ths far, unable to get any of my current displays to function with it.

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
>   &emsp;- Install Python Extension  
>   &emsp;- Install Prettier Extension

### Set GPU Memory

> The default memory (76MB) is fine for most things. Videos will need more at higher resolutions.
>
> - `Preferences` > `Rasberry Pi Configuration` > `Performance` Tab > `GPU Memory`
> - Set value to `124`. **NOTE: Going higher than 128MB can result in failure to boot.**

### Set up Desktop

**Task bar items:**

> - Right click on the launch area of the task bar (the icons that are already pinned)
> - Click `Application Launch Bar Settings`
> - Add Firefox and Visual Studio to the taskbar. Remove Chromium.

**Desktop Apearance:**

> - Right click on the desktop and click `Desktop Appearance`.
> - Select background and color scheme. (I like aurora for the background image, and dark green for taskbar.)
