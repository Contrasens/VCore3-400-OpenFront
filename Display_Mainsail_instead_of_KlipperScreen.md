# How to switch from using KlipperScreen to Mainsail on the HDMI output of the Raspberry Pi?

## Stop and disable KlipperScreen service
  ```
  sudo systemctl stop KlipperScreen
  sudo systemctl disable KlipperScreen
  ```

## Install Chromium and use the page served by Mainsail in kiosk mode
Read the guidelines (German) available here: [Kiosk Modus auf Touchscreen am Raspberry Pi mit automatischer Anmeldung im Browser](https://smarthomeyourself.de/wiki/raspberrypi/kiosk-modus-auf-touchscreen-am-raspberry-pi-mit-automatischer-anmeldung-im-browser). Basically, you need to do the following (I am just summarizing the steps from the link for future reference in case the content will disappear):

### Raspi Setup 
Disable Overscan compensation
  
  ```
  sudo raspi-config
  2 - Display Options -> D2 - Underscan =>> NO (DISABLE overscan compensation)
  ```
Enable Auto-login
  ```
  sudo raspi-config
  1 - System Options -> S5 – Boot / Auto Login =>> B2 – Console Autologin
  ```

### Install Necessary Packets
Update Raspbian
```
sudo apt update
sudo apt upgrade
```
Install X11 server and the Chromium browser
```
sudo apt-get install --no-install-recommends xserver-xorg x11-xserver-utils xinit openbox xdotool
sudo apt-get install --no-install-recommends chromium-browser
```

### Configure Autostart
```
sudo nano /etc/xdg/openbox/autostart
```
In the file, add the following (when finished, close & save with Ctrl+X):
```
xset -dpms            # turn off display power management system
xset s noblank        # turn off screen blanking
xset s off            # turn off screen saver

# Remove exit errors from the config files that could trigger a warning
sed -i 's/"exited_cleanly":false/"exited_cleanly":true/' ~/.config/chromium/'Local State'
sed -i 's/"exited_cleanly":false/"exited_cleanly":true/; s/"exit_type":"[^"]\+"/"exit_type":"Normal"/' ~/.config/chromium/Default/Preferences

# Run Chromium in kiosk mode
chromium-browser  --noerrdialogs --check-for-update-interval=31536000 --disable-infobars --kiosk $KIOSK_URL &
```

### Set the Home Address to be opened in the Browser
```
sudo nano /etc/xdg/openbox/environment
```
In the file, add the following (when finished, close & save with Ctrl+X):
```
  export KIOSK_URL=http://127.0.0.1
```

### Start xserver (without showing the cursor)
```
sudo nano ~/.bash_profile
```
In the file, add the following (when finished, close & save with Ctrl+X):
```
[[ -z $DISPLAY && $XDG_VTNR -eq 1 ]] && startx -- -nocursor
```

### Automatically login into the browser after boot
```
sudo nano /etc/xdg/openbox/autostart
```
Add the following at the end of the file (when finished, close & save with Ctrl+X):
```
sleep 30
exec /home/pi/start_ha.sh
```
Now, create the start_ha.sh file (a shell script):
```
sudo nano /home/pi/start_mainsail.sh
```
In the file, add the following (when finished, close & save with Ctrl+X):
```
# login to Mainsail (not necessary?)
sleep 30
export XAUTHORITY=/home/pi/.Xauthority; export DISPLAY=:0; xdotool type YOUR_MAINSAIL_USERNAME
sleep 1;
export XAUTHORITY=/home/pi/.Xauthority; export DISPLAY=:0; xdotool key Tab
sleep 1;
export XAUTHORITY=/home/pi/.Xauthority; export DISPLAY=:0; xdotool type YOUR_MAINSAIL_PASSWORD

sleep 1;
export XAUTHORITY=/home/pi/.Xauthority; export DISPLAY=:0; xdotool key Return
sleep 5;

# click save login-button
export XAUTHORITY=/home/pi/.Xauthority; export DISPLAY=:0; xdotool mousemove 1835 1030;
sleep 1;
export XAUTHORITY=/home/pi/.Xauthority; export DISPLAY=:0;xdotool click 1;
sleep 2;
```
Change the rights of the newly created file to be executable:
```
sudo chmod +x /home/pi/start_ha.sh
```
Reboot, and enjoy!
```
sudo reboot now
```