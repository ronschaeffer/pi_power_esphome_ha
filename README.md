# pi_power_esphome_ha
Remote WiFi power switch for Raspberry Pi using ESPHome with or without Home Assistant

Use an ESP8266 board with ESPHome (D1 mini by default) to remotely power on and off your Raspberry Pi from any device connected to your wireless network and/or from your Home Assistant dashboard. Inspired by https://github.com/lukehod/Pi-Off. Substantial amounts of the README and the wiring diagram are reused from there, too. Thanks, @lukehod.

This method works with either USB or PoE power to the Pi. I have only tested it with the [official PoE+ hat](https://thepihut.com/products/raspberry-pi-poe-plus-hat).

This is an ESPHome configuration file for the ESP8226 that:
1. Creates a button on a webpage at a local IP to switch the power on the Pi. Note that the swtich only initates power on or power off. It doesn't actually know whether the Pi is on or off.
2. If you are using ESPHome together with Home Assistant, also creates a button in Home Assistant to switch the Pi on or off. Note that Home Assistant only initates power on or power off. It doesn't actually know whether the Pi is on or off.
3. Enables the ESP's built-in led as a power indictation.

## How to get running:

### Parts needed:
* Raspberry Pi and correct power supply (I used a 3B+, but should work with any version)
* LOLIN/Wemos D1 Mini or other ESP8266 or ESP32 based device (guide assumes D1 Mini, but minor changes would be needed for most other devices)
* Dupont cables or other wires and solder for wiring
* Micro-usb cable to connect D1 Mini to computer

### Setup the Raspberry Pi to use GPIO shutdown
1. Go to the command line on the RPi (ssh if on another system)
2. `sudo nano /boot/config.txt`
3. Scroll to bottom and add the following line:
  ```
  # Enable GPIO power trigger
  dtoverlay=gpio-shutdown
  ```
4. `crl+x`, `Y`, `enter` to save and exit the editor
5. `sudo reboot` to restart the system

### ESPHome
See [ESPHome docs](https://esphome.io) for general instructions on using ESPHome. There are also great resources for ESPHome and helpful people on the [Home Assistant Community forum](https://community.home-assistant.io/).

Edit the substitutions in `pi-power.yaml` as needed and install it on the D1 Mini.

After everything is up and running, go to the IP for the D1 mini (or the `.local` address if using mDNS) in a browser on any device connected to your home WiFi. If you have not setup a static IP or mDNS, you may have to use a network analyser app to find the correct IP.

Remember that ESPHome does not know the power state of the Pi. You will have to determine that yourself.

[Example ESPHome webpage](webpage.png)

To reboot the Pi from the webpage:
1. Press the button.
2. Wait a minute or so to be sure that the Pi is fully powered down.
3. Press the button again.

### Home Assistant
Home Assistant will automaticaly discover the ESPHome device so that you can the button to your dashboard as you see fit.

Remember that Home Assistant does not know the power state of the Pi. You will have to determine that yourself.

To reboot the Pi from your Home Assistant Dashboard:
1. Press the button.
2. Wait a minute or so to be sure that the Pi is fully powered down.
3. Press the button again.

### Wiring it all together
> A word of caution here: Incorrectly wiring the Raspberry Pi and/or the Wemos D1 Mini could ***permanently damage*** either devices! It is safest to do your wiring while there is no power connected and be sure to double check your connections to make sure you are not sending power to a pin that cannot handle it. I accept no liability for any damage from incorrect wiring!

![wiring diagram](/img/wifi_button_wiring.png)

Also refer to a pinout diagram for [Raspberry Pi](https://pinout.xyz/) and [D1 Mini](https://docs.wemos.cc/en/latest/d1/d1_mini.html) to make it easier to select the correct pins.
1. Connect `pin 3` on the Raspberry Pi to `pin D3` (GPIO0 in ESPHome) on the D1 Mini
2. Connect one of the 5V pins and one of the ground pins on the Raspberry Pi to the `5V` pin and `GND` on the D1 Mini.  (I use `pin 4` and `pin 6` on the RPi)
3. Double check that your setup is correct (especially that no 5V pin is connected to ground or an IO pin anywhere!) and then you can plug in the power cable for your Raspberry Pi to let it boot up.

Note that if you are using the official PoE+ hat, you will need to connect the wires very low on the pins so that the hat will still fit. I made tiny loops in the wires to go aorund the Pi's pins and then soldered them in place:

![wiring photo](/img/pi_wired.jpg)

With the offical PoE+ hat installed:

![wiring photo_with_poe_hat](/img/with_PoE_hat.jpg)

