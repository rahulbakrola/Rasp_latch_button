# Raspberry Pi - Power On and Shutdown Button HAT

## Mini HAT with a Push Button to **Power** the Raspberry Pi **On** and **Off** Safely After the Shutdown Sequence Completes.
Useful for home devices, like audio players or home theater systems, to avoid having to initiate the shutdown in the user interface, wait, and then cut off the power manually.

## The Mini HAT:

<p float="center">
<img src="Docs/front1.jpeg" width="45%">
<img src="Docs/Rasp_hut.jpeg" width="45%">
</p>


- USB-C power connector. Use only this connector to power the Raspberry Pi.
- Push button:
  - When the Raspberry Pi is off, a button press powers it on.
  - When the Raspberry Pi is on, a button press initiates the shutdown sequence, and when it completes, cuts the power off.
  - An additional switch in the USB cable is not necessary; after the shutdown, the power is completely off.
- 5x2 socket to plug into the Raspberry Pi header. It provides access to GPIO2, GPIO3, and power pins. 

<p float="center">
<img src="Docs/hut_render.jpg" width="45%">
<img src="Docs/back1.jpeg" width="45%">
</p>

---

## GPIO pins:

### GPIO 2: Shutdown (gpio-shutdown) 
- This overlay allows you to initiate a graceful shutdown by shorting a GPIO pin to Ground.
- Behavior: Adding dtoverlay=gpio-shutdown to your config file enables GPIO 2 as the shutdown trigger.
- Configuration: Add this line to your config file:
    
    ``dtoverlay=gpio-shutdown,gpio_pin=2,active_low=1,gpio_pull=up``

### GPIO 3: Power-Off Signaling (gpio-poweroff)
- This overlay is used to signal an external power circuit (like this HUT) to cut the main power once the Pi has safely finished its shutdown sequence. 
- Behavior: The pin will transition to its active state (LOW) only after the kernel has finished shutting down, ensuring no SD card corruption occurs 

  - active_low=1: The pin is HIGH (3.3V) while the Pi is running and transitions to LOW (0V) once it is safe to cut power

- Configuration: Add this line to your config file:
    
    ``dtoverlay=gpio-poweroff,gpiopin=3,active_low=1``


 
### Config file Location
Configure the behaviour of the GPIO pins in the Raspberry Pi config file:

- Raspberry Pi OS Bookworm (and newer): The file is located at /boot/firmware/config.txt.
- Older OS Versions (Bullseye and earlier): The file is located at /boot/config.txt.
- On another computer: If you insert the SD card into a PC or Mac, the file is located in the root directory of the small partition named boot or bootfs.

For example:

```bash
[all]
# Add your shutdown/poweroff overlays here
dtoverlay=gpio-shutdown,gpio_pin=2,active_low=1,gpio_pull=up
dtoverlay=gpio-poweroff,gpiopin=3,active_low=1
```


## The circuit 
Basic power latch circuit combined with the ***shutdown*** and ***poweroff*** GPIO's to manage the power off sequence.

<img src="Docs/hut_schematic.jpg" width="90%">

- Before the button is pressed Q1 is off because R1 keeps the gate at the same voltage than the source. There is no power at the 5V pin.
- When the button is pressed, it brings the gate of Q1 to GND, activating the power.  C1 keeps it active for a short time.
- When the Rasperry Pi boots it keeps the GPIO 3 (power_off pin) up, keeping Q2 on, what keeps Q1 also on.
- When the Rasperry Pi is on and the button is pressed again, it sends a LOW signal to the GPIO 2 (sht_dwn pin), what initiates the shut down sequence.
- When shut down sequence completes, the GPIO 3 (power_off pin) goes LOW, what switchs Q2 off, and therefor Q1 switchs off also, and the power gets cut off.


## Restrictions
- The total current, including anything connected to the Raspeberry Pi, should not be more than 6A (about 30W), to be on the safe side. The absolute maximum rating of the main mosfet is 7.5A.
- The HAT covers the first 10 pins of the header, to use two 5V and two GND pins, and also for stability.  It means that the pins of GPIO's 2,3,4,14,15 are not available in the header for other uses.


<img src="Docs/Rasp_hut2.jpeg" width="60%">


