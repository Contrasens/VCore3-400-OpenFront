# How to switch from using KlipperScreen to Mainsail on the HDMI output of the Raspberry Pi?

1. Stop and disable KlipperScreen service
  ```
  sudo systemctl stop KlipperScreen
  sudo systemctl disable KlipperScreen
  ```

3. Install Chromium and use the page served by Mainsail in kiosk mode
   Read the guidelines (German) available here: [Kiosk Modus auf Touchscreen am Raspberry Pi mit automatischer Anmeldung im Browser](https://smarthomeyourself.de/wiki/raspberrypi/kiosk-modus-auf-touchscreen-am-raspberry-pi-mit-automatischer-anmeldung-im-browser)
