Power-Off Signaling (gpio-poweroff)
This overlay is used to signal an external power circuit (like a MOSFET or UPS) to cut the main power once the Pi has safely finished its shutdown sequence. 

    Configuration: Add this line to your config file:
    dtoverlay=gpio-poweroff,gpiopin=18,active_low=0
        gpiopin: The BCM pin that will change state to signal "safe to cut power".
        active_low: Set to 1 if your external hardware triggers on a Low signal, or 0 for High.
    Behavior: The pin will transition to its active state only after the kernel has finished shutting down, ensuring no SD card corruption occurs
