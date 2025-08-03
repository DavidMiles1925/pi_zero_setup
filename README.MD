# Setting up Raspberry Pi Zero

**Navigation Tip:** Use the "Table of Contents" icon in the top right corner of the document.

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
> > 2.  Follow imager instructions to install Lite OS to flash drive. (32-Bit Laegacy port of Bullseye, 64 bit version can run into memory issues even on RPi ZW2)
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

---

## Other Helpful Procedures

### Configure a Program to Run on Startup:

**etc/rc.local no longer exists by default and must be created.**

> 1.  Modify (or create) etc/rc.local with admin priviliges:
>
> ```bash
> sudo nano /etc/rc.local
> ```
>
> 2.  Add this to /etc/rc.local:
>
> ```bash
> #!/bin/sh -e
>
> sudo python /home/pi/yourprogram/program.py &
>
> exit 0
> ```
>
> - **Replace `pi` with your directory**
>
> - **Don't forget the `&` at the end to allow the program to run in the background**
>
> 3. Modify file owner and owner group, as well as permissions:
>
> ```bash
> sudo chown root:root /etc/rc.local
> sudo chmod 755 /etc/rc.local
> ```
>
> 4. Stopping the process:
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

---

### Configure Static IP Address With nmcli (NetworkManager Command Line Interface)

It seems that this is the new preferred way to set up a Static IP, it has replaced the DHCP instructions that were previously here.

Official docs for nmcli: [**Link to Official nmcli Docs**](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/7/html/networking_guide/ch-configuring_ip_networking)

1. **Ensure Pi is up to date**

```bash
sudo apt-get update
sudo apt-get upgrade
```

2. **Enable NetworkManager**

```bash
sudo raspi-config
```

3. **Check current connection**

```bash
nmcli connection show
```

Find the active Wi-Fi connection name (often wlan0 or something like MyWifiSSID under the "NAME" column).

4. **Get your Wi-Fi interface name**

Look under the DEVICE column for the Wi-Fi interface (usually wlan0).

If you cannot find it, use this command:

```bash
nmcli device status
```

5. **Find IP Address**

```bash
ip addr
```

Look for IPv4 address

6. **Find you Gateway**

```bash
route -ne
```

7. **Find nameserver for IPv4**

```bash
cat /etc/resolv.conf
```

Look at "nameserver" line (usually top line)

8. **Edit the existing connection**

Use the name from step 1. For this example, let's assume your connection is called MyWifiSSID.

nmcli connection modify MyWifiSSID ipv4.addresses 192.168.1.100/24
nmcli connection modify MyWifiSSID ipv4.gateway 192.168.1.1
nmcli connection modify MyWifiSSID ipv4.dns "8.8.8.8 1.1.1.1"
nmcli connection modify MyWifiSSID ipv4.method manual

Replace:

    192.168.1.100/24 with your desired static IP and subnet (the /24 means subnet mask 255.255.255.0).

    192.168.1.1 with your actual router/gateway IP.

    The DNS servers result from step 5.

9. **Save and apply**

nmcli connection up MyWifiSSID

If you're already connected, it may reconnect automatically.

---

### Set up SSH Key (to eliminate need for password)

1. On you remote machine (often Windows machine) run this command in the CLI:

```bash
ssh-keygen -t rsa
```

- If you asked to overwrite a key, verify that you do not already have a public key in use or it may destroy other connections.
- Press Enter through the prompts leaving them blank

2. Copy key to Pi (run on remote machine)

```bash
scp ~/.ssh/id_rsa.pub PI_NAME@192.168.1.100:~
```

3. SSH back to Pi. You will still be prompted for a password at this point. (run on remote machine)

```bash
ssh PI_NAME@192.168.1.100
```

4. Set up the key on the RPi. (run on RPi)

```bash
mkdir -p ~/.ssh
cat ~/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
rm ~/id_rsa.pub
```

5. Verify that it is working by SSH into RPi

## Troubleshooting

### SSH Connection Rejected

This message was seen when attempting to connect to Raspberry Pi Zero via SSH from a Windows 11 Machine using Git Bash.

- The Pi was a replacement using the exact same IP address and Pi Username.

> @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@  
> @ WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED! @  
> @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@  
> IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!  
> Someone could be eavesdropping on you right now (man-in-the-middle attack)!  
> It is also possible that a host key has just been changed.  
> The fingerprint for the ED25519 key sent by the remote host is  
> SHA256:U1T27KHowc9wOdZ6WAWrffxsyPG47YeAM+99uGkK3FE.  
> Please contact your system administrator.  
> Add correct host key in /c/Users/astro/.ssh/known_hosts to get rid of this message.  
> Offending ECDSA key in /c/Users/astro/.ssh/known_hosts:17  
> Host key for 192.168.1.161 has changed and you have requested strict checking.  
> Host key verification failed.

**Solution:**

I used this command to generate a new key for the IP:

```bash
ssh-keygen -R 192.168.1.161
```

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
