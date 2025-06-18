# Microsecond-RPI-NTP
Clone of https://austinsnerdythings.com NTP Server with Corrections and Updates

## 1. Update rpi and install components
`sudo apt update` <br>
`sudo apt upgrade`<br>
`sudo rpi-update`<br>
`sudo apt install pps-tools gpsd gpsd-clients python3-gps chrony`<br>

## 2. add GPIO and module info where needed
`sudo bash -c "echo '# the next 3 lines are for GPS PPS signals' >> /boot/firmware/config.txt"` <br>
`sudo bash -c "echo 'dtoverlay=pps-gpio,gpiopin=18' >> /boot/firmware/config.txt"` <br>
`sudo bash -c "echo 'enable_uart=1' >> /boot/firmware/config.txt"` <br>
`sudo bash -c "echo 'init_uart_baud=9600' >> /boot/firmware/config.txt"` <br>

In /etc/modules, add ‘pps-gpio’ to a new line. <br>
`sudo bash -c "echo 'pps-gpio' >> /etc/modules"`

Reboot <br>
`sudo reboot`

## 3. Wire up the module:
Pin connections:

GPS PPS to RPi pin 12 (GPIO 18) <br>
GPS VIN to RPi pin 2 or 4 <br>
GPS GND to RPi pin 6 <br>
GPS RX to RPi pin 8 <br>
GPS TX to RPi pin 10 <br>

### 3.1 Free Up the UART Serial Port for the GPS Device
Run raspi-config -> 3 – Interface options -> I6 – Serial Port -> Would you like a login shell to be available over serial -> No. -> Would you like the serial port hardware to be enabled -> Yes.

## 4. Verify PPS is Working
Check that PPS is installed<br>
`lsmod | grep pps` <br>

The output should look like: <br>
pps_ldisc               16384  1 <br>
pps_gpio                16384  0 <br>

Check for PPS pulses with: <br>
`sudo ppstest /dev/pps0` <br>

This will give an output every second looking similar to: <br> <br>
trying PPS source "/dev/pps0" <br>
found PPS source "/dev/pps0" <br>
ok, found 1 source(s), now start fetching data... <br>
source 0 - assert 1750210874.998541444, sequence: 10286 - clear  0.000000000, sequence: 0 <br>
source 0 - assert 1750210875.998542306, sequence: 10287 - clear  0.000000000, sequence: 0 <br>
source 0 - assert 1750210876.998542241, sequence: 10288 - clear  0.000000000, sequence: 0 <br>

## 5. Change GPSd to start on bootup:
Edit /etc/default/gpsd to the following with <br>
`sudo nano /etc/default/gpsd` <br>

The full file should look like the following after modifying:

  `# Default settings for the gpsd init script and the hotplug wrapper.`<br>
  `# Start the gpsd daemon automatically at boot time`<br>
  `START_DAEMON="true"`<br>
  `# Use USB hotplugging to add new USB devices automatically to the daemon`<br>
  `USBAUTO="true"`<br>
  `# Devices gpsd should collect to at boot time.`<br>
  `# They need to be read/writeable, either by user gpsd or the group dialout.`<br>
  `DEVICES="/dev/ttyS0 /dev/pps0"`<br>
  `# Other options you want to pass to gpsd`<br>
  `GPSD_OPTIONS="-n"`<br>

  Use gpsmon command to verify information is being recieved from the GPS module.


\\\Unfinished code.
