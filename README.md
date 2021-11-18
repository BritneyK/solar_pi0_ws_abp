# Solar Powered Weather Station

## Description (to improve)
In this project, we seek to make a fully autonomous control unit that will at the same time communicate sensor data through LoRa and receive LoRa packages excersing as LoRa Gateway. Also, the periodicity of this process will change depending on external factors (battery %, sunshine... ) and the LoRa Messages content.

## Raspbian OS configuration 

### Download Raspberry Pi OS Lite and flash (on MacOs BigSur)

At first, go to the raspberry pi official site and download the image you want to upload into the raspberry (https://www.raspberrypi.org/downloads/raspberry-pi-os/). For this tutorial, we are going to use the Raspberry Pi OS (32-bit) Lite image (the May 2021 version in our case). Once you have downloaded the image, you have to flash it into your raspberry pi sd card. For this, open 
a terminal :

    - diskutil disk --> you can see the number of the  diskx (x is the number)
    - diskutil unmountDisk /dev/diskx
    - sudo dd bs=1 if=/path_to_folder/2021-05-07-raspios-buster-armhf-lite.img of=/dev/diskx

Now, you have two choice : you can work with SSH (you work with a personal wifi network) or not (this is the case if you work at university with eduroam wifi network).

**Case 1** : we have the possibility to set up the Pi Zero W for headless SSH access over wifi

At this point, you should have finished to flash the Raspbian Os into your SD card. As we are going to use the raspberry without keyboard or screen, we will have to add some configuration files to our sd card.

on the same terminal :

    - touch /Volumes/boot/ssh                   
    - touch /Volumes/boot/avahi
    - sudo nano config.txt --> append this line to the bottom of it
            dtoverlay=dwc2
    - sudo nano cmdline.txt --> append this line after rootwait
            modules-load=dwc2,g_ether quiet init=/usr/lib/raspi-confog/init_resize.sh
    - sudo nano /Volumes/boot/wpa_supplicant.conf
    add this lines to this file :
            ```
            country=FR (this has to be changed to your county code)
            ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
            update_config=1

            network={
                    ssid="SSID" (this has to be changed to your network SSID)
                    scan_ssid=1
                    psk="PASSWORD" (this has to be changed to your network password)
                    key_mgmt=WPA-PSK (this has to be changed to your network key management policy)
            }
            ```

Then, put back the sd card into your raspberry, all the OS configuration is done!
Once the raspberry is fully inited, you can ssh to your raspberry IP and all should be ready to start working!. To know the ip of the raspberry, you can use a network scanner. Log in as user **pi** with password **raspberry**.

**Case 2** : we don't have the possibility to set up by SSH because eduroam is protected by 802.1X and the connection cannot be shared

        - boot the Raspberry Pi Zero W with the SD card
        - change keyboard layout if not a qwerty keyboard
            sudo nano /etc/default/keyboard (key / is !)
            change XBLAYOUT="gb" to "fr"
        - sudo nano /etc/wpa-supplicant/wpa_supplicant.conf
            ```
            country=FR (this has to be changed to your county code)
            ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
            update_config=1

            network={
                    ssid="eduroam"
                    scan_ssid=1
                    key_mgmt=WPA-EAP
                    proto=WPA2
                    eap=PEAP
                    identity="name@uiversity.com"
                    anonymous_identity="anonymous@university.com"
                    psk="PASSWORD" (this has to be changed to your network password)
                    phase1="peaplabel=0"
                    phase2="auth=MSCHAPV2"
            }
            ```
        - sudo nano /etc/network/interfaces
           ```
           allow-hotplug=wlan0
           iface wlan0 inet dhcp
           wpa-conf /etc/wpa-supplicant/wpa_supplicant.conf
           wpa-driver wext
           ```
        - remove dhcpcd (2 dhcp clients installed on buster version !) --> sudo apt-get remove dhcpcd5
        - cp /usr/share/zoneinfo/Europe/Paris/ /etc/localtime if you work in France
        - sudo raspi-config
                1.      system options
                S5.     Boot/Autologin
                B2.     Console Auto Login
                Ok -->  Finish --> reboot (yes)

Now you are inside you will need to install one only dependency by yourself. To be able to access the github repo (and then the install scripts), you have to install git (**sudo apt-get install git**, you might need to apt-get update before).

## Initialisation and process

After that, clone this repository and go to the **/02-configuration/raspberry** folder and read the **README_CONF.md** document.