# Setting up Raspberry Pi Zero

## Materials

- Raspberry Pi Zero
- MicroSD card - at least 4GB
- Mini HDMI Cable or adapter.
- MicroUSB cable or adapter
- 5V 2.5A power supply
- Monitor
- Keyboard

Optional:

- 20x2 pin header

## Set Up Proceedure

1. Download the imager: [Pi Imager installation](https://www.raspberrypi.com/software/)
2. Follow imager instructions to install Lite OS to flash drive.
3. Copy ssh and wpa_supplicant to boot drive (make sure network and password are set in wpa_supplicant).
4. Boot pi with flash drive inserted.
5. Follow instructions on screen to set keyboard layout, username, and password.
6. Record username and password somewhere safe.
7. Run the following commands:

```bash
sudo apt update
sudo apt install git
```
