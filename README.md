# Purpose

Enhance Raspbian with a mechanism by which *any* host system used to image an SD card can add a setup script to the SD card to initiate a process on first boot.

The mechanism will allow communities to form around Raspbian as the base for special purpose Raspberry Pi configuration without the need to build and host an entire OS. It should be possible to prepare SD cards that initiate a setup process the yeilds a system that does not need to be further configured by following lengthy explainations found on forums and wikis.

# Practical Applications

1. Set up secured wifi on headless systems
2. Add or remove packages
3. Build and install drivers

# Implementation

#### Add to /etc/rc.local
    [ -e /boot/boot_script.sh ] && ( RC_LOCAL=called /boot/boot_script.sh )

#### Create /boot/boot_script.sh with
    #!/bin/bash                                                                                                                                                                                             
    #
    # /boot/boot_script.sh
    #
    # This script is executed by /etc/rc.local
    #
    # By default this script does nothing, and removes itself after the
    # first run when called by /etc/rc.local

    # This setting will cause this script to exit if there are any errors.
    set -ue
    # Do not add anything above this line.

    # If you want this script to remain and run at ever boot comment
    # this variable.
    REMOVE_SELF=1

    # This is where you put the setup code.
    
    ## Here are some examples of things you might do...
    ##
    ## Remove unneeded packages
    ## apt-get -y remove --purge xserver-common x11-common gnome-icon-theme gnome-themes-standard penguinspuzzle
    ## apt-get -y remove --purge desktop-base desktop-file-utils hicolor-icon-theme raspberrypi-artwork omxplayer
    ## apt-get -y autoremove
    ##
    ## Install a package that will automatically mount & unmount USB drives
    ## apt-get install usbmount
    ##
    ## Setup wifi so you can connect to a secured network without a keyboard & monitor!
    ## cat << EOF | sudo tee -a /etc/wpa_supplicant/wpa_supplicant.conf
    ## network={
    ##         ssid="MyWifiName"
    ##         scan_ssid=1
    ##         psk="MyWifiPassword"
    ##         id_str="work"
    ##         priority=5
    ## }
    ## EOF
    ## ifdown wlan0
    ## ifup wlan0

    # Leave this block alone (unless you took out REMOVE_SELF above, I guess)
    if [[ $REMOVE_SELF == 1 && $RC_LOCAL == called && $0 == /boot/boot_script.sh ]]; then
      mv /boot/boot_script.sh /boot/boot_script.removed
    fi

# Alternate Implementation

Instead of adding to rc.local, we could create a script like /etc/init.d/regenerate_ssh_host_keys
