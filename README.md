# Android-klipperscreen-
Apps and how to Tut for use old tablet or smartphone as klipperscreen 

AndroidKlipperScreen
Step 1 Download and install Klipper Screen Via Kiuah
https://github.com/th33xitus/kiauh
Step 2 Download and install XSDL and Configure.
Android can use the Play Store
https://play.google.com/store/apps/details?id=x.org.server&hl=en_US&gl=US
Fire Devices need to Sideload APK called x.org.server.apk listed in the data folder (uploading shortly however here is a direct download link) This version IS required for Fire devices
https://sourceforge.net/projects/libsdl-android/files/apk/XServer-XSDL/XServer-XSDL-1.11.40.apk/download
After Install open app Youll see SDL splash screen click “CHANGE DEVICE CONFIGURATION” Click Mouse Emulation the Mouse Emulation Mode then Select Desktop, No Emulation
Step 3 Time to create the loading script to forward the display to XSDL im doing this in putty to ssh to my PI
SSH into PI then move to KlipperScreen folder
cd ~/KlipperScreen
nano ./launch_klipperscreen.sh

paste this code into your nano screen for USB CABLE
#!/bin/bash
DISPLAY=(your ip from blue screen):0 /home/pi/.KlipperScreen-env/bin/python3 /home/pi/KlipperScreen/screen.py

Save Buffer by "Ctrl X" then yes now run
sudo chmod +x ./launch_klipperscreen.sh

this is necessary to make our script executable
Step 4 test the screen output by running the script
./launch_klipperscreen.sh
this is to verify a few items
that the screen actually works
to determine whether or not KlipperScreen is connecting to the printer
If your screen works and connects to the printer AWESOME move onto step 5 there is nothing further for you to do
if your screen works and does not connect to the printer we need to retrieve the API Key
this is normally caused by logins being enabled on the Fluidd UI this is an easy hurdle however
retrieve your API key and place in a Configuration file
example code Save in klipper_config (/home/pi/klipper_config/KlipperScreen.conf)
# Define printer and name. Name is anything after the first printer word
[printer yourprintername]  
# Define the moonraker host/port if different from 127.0.0.1 and 7125
moonraker_host: 127.0.0.1
moonraker_port: 7125
# Moonraker API key if this is not connecting from a trusted client IP
moonraker_api_key: 
#~# --- Do not edit below this line. This section is auto generated --- #~#

#~#
#~# [main]
#~# screen_blanking = off
#~# theme = material-dark
#~# confirm_estop = True
#~#
now kill the script by pressing Ctrl C twice
Step 5 Now its time to setup the Script as a system service that survives throughout reboots look at example.service in data folder that is word for work what you need in your file i have it listed as i am going to be compiling an autoscript
time to go to the Systemd
cd /etc/systemd/system

now time to create file
sudo nano ./KlippyScreenAndroid.service

paste in nano
[Unit]
Description=KlippyScreen
After=moonraker.service
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=1
User=pi
WorkingDirectory=/home/pi/KlipperScreen
ExecStart=/usr/bin/bash  /home/pi/KlipperScreen/launch_klipperscreen.sh

[Install]
WantedBy=multi-user.target

save, now time to enable and reload services
sudo systemctl daemon-reload
sudo systemctl enable KlippyScreenAndroid
sudo systemctl restart KlippyScreenAndroid

Now it should be working and no matter what it will run every reboot
