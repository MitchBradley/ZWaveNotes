---
ZWave Home Automation Notes: The garage overhead lights and the house water heater switch are controlled with a home automation method called ZWave.  This page tells about the setup of that system.
---
## Overview
There are three groups of overhead lights in the garage - one light over the workbench next to the window, a cluster over the workbench in the middle of the room, and some over the walkway near the garage door opener.  There are three switches - a wired toggle switch next to the outside door and on-wall wireless (battery powered) switches next to the stairway door and the downstairs bedroom door.  There are also some task lights that can only be controlled by switches right on the lights.

The intention is that turning on any switch (up button/toggle) will turn on all the lights, and similarly, turning off (down button/toggle) will turn off all the lights.  In addition, the controller has a rule to turn off the lights at 10PM in case someone forgot to turn them off.

The water heater switch in the laundry room controls power to the main house water heater.  On sunny days, the water will be kept hot by the two large thermal solar panels on the roof.  The electricity is a backup for when there is not enough sun.  The water heater has a thermostat so it will not draw power if the water is already hot.  The automation system is set to turn on the power at 5PM and off at 11PM.  This only matters on rainy/overcast days because of the thermostat.  The turn-off at 11PM prevents wasting electricity keeping the tank hot at night.

The controller is a Raspberry Pi computer with an interface board for the ZWave wireless communication protocol.  It runs some software named ZWave.me that lets you set up rules for what happens on various events like switch activations and time of day.  It is located near the outside door in the garage, plugged in to an Ethernet switch that is on the home LAN (192.168.68.x subnet).  That location puts it in wireless range of all the ZWave devices in the garage, as well as the water heater switch in the laundry room.  The controller Pi does not have WiFi, just Ethernet (one less thing to go wrong!).

## Hardware Details

As with all things electronic, available products change rapidly.  Most or all of the devices listed below are old versions that have been superseded by newer versions accomplishing the same function with shiny new features - or not.

### Controller
The controller is an old second-generation (I think) Raspberry Pi computer.  The interface board is a ZWave.Me Razberry2 - [$20 from Amazon](https://www.amazon.com/Z-Wave-Me-RaZberry2-Plug-Raspberry-frequency/dp/B01M3Q764U) .  It is an older model that might be hard to get at some point.  There is a newer Razberry 7 model that is more expensive.  I chose the older model because all of our ZWave devices are old and do not have the version 7 chipset (version 7 devices have longer range and the batteries last longer), so the new model would not give any tangible improvements.  Also I wasn't sure this was going to work out so I didn't want to spend extra money in case it was a failure.  This Pi+Razberry setup replaces the "Vera" box that stopped working.  The Vera box tended to do a lot of work via the cloud, so internet connectivity problems caused interruptions in system functionality.  The new setup can be controlled remotely via the cloud if you wish, but it does not depend on it for day to day operation.  You can do everything from the local network.  In addition to the reliability improvment, things are faster, such as the time between pressing a button and the lights going on.

The interface board is supposed to work with all models of Raspberry Pi.  It plugs in to the expansion connector that is the same on all Raspberry Pis. For a large ZWave network it might be better to have a newer/faster Raspberry Pi, but for our setup the old one seems to work just fine.

### Water Heater Control
The water heater control box in the laundry room is [Aeotec Heavy Duty Smart Switch](https://www.amazon.com/Aeotec-Security-controller-electricity-consumption/dp/B00MBIRF5W).  In addition to its ability to switch the water heater power, it also monitors the water heater power (voltage, current, watts, and kilowatt-hours) and the temperature in the laundry room.  We are not using the monitoring functions for any automation functions but you could, for example, use the controller software dashboard to see if the water heater is drawing current and thus trying to heat up.

There is button on the box behind a hole in the cover.  If you press it you can turn the water heater on or off manually.  If the red LED is on, the power is on.

### Outside Door Switch
The outside door switch is similar or maybe identical to [this one](https://www.lowes.com/pd/GE-Z-Wave-ZigBee-Bluetooth-3-Way-White-Toggle-Light-Switch/999911949) .  It is like an ordinary wall switch in that the light is directly connected to the switch so you can control the attached light directly from the switch even if the ZWave controller computer is not working.  It also has a ZWave wireless connection to the controller so, when you activate the toggle by pushing it up or down, the controller gets an event notification that it can use to control the other lights in the room.  Similarly, the controller can tell this switch to turn its light on or off over ZWave.

It is powered from the house wiring so it does not need a battery.

When you first add a device like this to the ZWave network (pair it), the name comes up as some variant of the manufacturer name, which you can then change to a name you prefer.  For this one, I assigned the name "Outside Door Switch".

### Bedroom and Stairway Door Switches

These [WA00Z-1](https://www.gocontrol.com/manuals/WA00Z-1-Install.pdf) switches are wireless only, battery powered, screwed to the wall, not connected to the house wiring.  They do not directly switch any devices; instead they just send ZWave data to the controller when you press one of the two buttons.  The controller uses that to affect the other devices.  They each have two CR2032 coin cell batteries inside, which need replacing every so often.  To get to the batteries you just pull on the cover and it pops off.

The device identifies its manufacturer as "Nortek Security & Control LLC".

The assigned names are "Bedroom Door Switch" and "RightOverheadInWallSwitch".

### Plug-in Outlet near Garage Door Opener

It is a ZWave-controlled switch that plugs into an outlet and then you can plug other things into its outlet.  It is branded by GE but the innards are made by Jasco.  I think the part number is [ZW4103](https://manual.zwave.eu/backend/make.php?lang=en&sku=39444/%20ZW4103&cert=ZC10-19126814).  It has a button that you can use to turn it on or off manually, but you need a ladder to reach it.  Normally it is controlled wirelessly - the controller sends it on/off commands according to rules established in the controller software configuration.

The assigned name is "Left Ceiling Switch".

### Outlet Above Main Workbench
This is a relatively new (as of Jan 2025) "NEO Z-Wave Plus Smart Mini Plug" plug-in outlet.  In addition to on-off, it also monitors voltage, current, power and energy.  Its assigned name is "AboveBenchOutlet".  There is a manual at szneo.com.  Like the plug-in outlet, it has a manual switch that you can reach with a ladder, and is normally controlled wirelessly.
#### Factory Reset
Hold the button for 10 seconds until "led lights is on", then release.  The LED will blink 5 times.

#### Inclusion Mode
Plug it in then press the button 3 times within 1.5s.  The LED will blink 3 times.

### Garage Door Sensor
There is a battery-powered sensor on the side of the garage door near the bedroom door.  A magnet attached to the garage door tells it when the door is closed.  In the distant past I used it to use this to close the garage door automatically at night, but I don't use it anymore. The gadget that I built to activate the garage door opener remotely stopped working and I haven't fixed it.  This sensor is not currently paired with the controller.

## Pairing Tricks

Pairing a ZWave device with the controller can be tricky.  In principle, it is as simple as "tell the controller software to go into pairing mode, then press a button on the device".  In practice, this only works easily on a fresh-out-of-the-box device that has never been paired to a different network, and often only when the device is physically near the controller.  If the device is already paired, you first have to unpair it from the old network.  That is easy if the old controller is still functional - just put the controller in unpair mode and press the button.  Things get interesting if you don't have the old controller or it is dead.  Then you have to do a "factory reset" on the device, which requires a chicken dance.  Every device has its own special chicken dance sequence of button pushes or whatever.  The timing can be very picky so sometimes you need several tries to get it right.

The manuals for the various devices give details of pairing, unpairing, and factory reset or you can Google it.  The PITA reset dances are summarized below.

### Water Heater Factory Reset
Per https://manuals.plus/Aeotec/aeotec-heavy-duty-smart-switch-gen5-zw078-f-manual, I think you have to press the switch and hold it for 20 seconds.  The light will blink when it is unpaired.
### Outside Door Factory Reset
Toggle switch up three times in rapid succession, then down three times in rapid succession.
### Plugin Outlet Factory Reset
Per https://support.getvera.com/hc/en-us/article_attachments/360047115674/ZW4103__GEJasco_Plug-in_Smart_Switch.pdf, unplug it, hold down the button, then plug it in and continue to hold the button for 3 seconds.
### Outlet Above Main Workbench Factory Reset
Hold the button for 10 seconds until "led lights is on", then release.  The LED will blink 5 times.
### Wireless Wall Switch Factory Reset
Per chrome-extension://oemmndcbldboiebfnladdacbdfmadadm/https://www.gocontrol.com/manuals/WA00Z-1-Install.pdf, press the top button 5 times then the bottom button 5 times, all within 5 seconds.  The light will blink 7 times if you get it right.
## Controller Software
### Credentials
You can get to the Rasberry Pi Linux prompt with ssh.  The static IP address is 192.168.4.20, assigned via /etc/conf.d/netif.eth0 on the Pi.  The Linux user name for that Raspberry Pi is "pi", with password "razberry" (note the 'z', not the normal spelling).  The Linux prompt is not necessary for normal administration of the ZWave network, but you might need it if you need to update the computer or change the IP address.

For the Z-Wave.Me automation software, the login is "admin" and the password is via my usual schema with "ZW".  You can find the automation software without knowing its IP address by browsing to "find.z-wave.me" from a machine on the CampOlinda network, then click on the IP address shown at the bottom, just below "Direct connect to Z-Way".  Or you can browse directly to 192.168.4.20:8083 .

If you want to access the controller from outside the CampOlinda network, you can browse to find.zwave.me and enter the Remote ID  190419 with the Z-Wave.Me login (admin) and password as given above.  There is a phone app Z-Wave.Me for which you will need the same credentials.

### Reinstalling the software
The software was installed by imaging the Raspberry Pi's MicroSD card via https://z-wave.me/z-way/download-z-way/ .  I used Balena Etcher to copy the image to the card but there are many ways to do that.  The image installation technique is faster and easier than first installing a Raspbian distro then adding the software.  The prebuilt image omits a lot of Linux services that you don't need, so it boots surprisingly fast.  After you image it, you might need to login and setup the network - but sometimes it just works out of the box if WiFi is not involved - as is the case with the Ethernet-connected Pi.

The [ZWave.Me software manual](https://z-wave.me/files/manual/z-way/#) has pretty good instructions for installation and initial connection, but it is rather poor at telling you how to set up the automation, so I will explain that here.

#### Changing the IP Address
You might have to do this if the subnet changes due to a new router or whatever.
* Take down the Raspberry Pi and plug in a USB keyboard and HDMI monitor.
* Power it on and wait for the login prompt
* Login as pi / razberry.
* **sudo nano /etc/conf.d/netif.eth0**  (there is also **editor** with EMACS-like bindings, and of course **vi**)
* Change the ipaddrs, gateway, and nameservers lines to reflect the new subnet.
* Save the file and exit
* **sudo reboot**
* Login again and check the network setup with `ip addr`

### Controls and Sensor

A control is something that can be turned on or off, like a light.  A sensor sends a signal to the controller, based on a user action or a door closure or a reading like temperature.  Many ZWave gadgets have both a control function and one or more sensor functions.  For example the wall switch near the outside door has a toggle on the wall that you can push up or down (sensor) and an internal switch that turns on the power to the light that is wired to it.  In a lot of cases, the sensor both directly activates the associated control, but also sends a wireless message to the controller.  The controller can then decide what to do with other controls in different gadgets.

### Overview

The process of configuring the network involves:
- Adding (pairing) the various devices to the network, so the controller knows that they exist. At this phase, or later, you can choose your own name for each device and optionally assign it to a "room" for convenience.
- Creating "scenes" that establish coherent settings for groups of controls. For example "all of the garage lights are on" is a scene.
- Creating "rules" that say what to do when something happens. For example "when you press the top button on a door switch, activate the "all lights on" scene.

### Rooms

You can assign any device to a "room".  This is just a convenience for grouping and does not have have any inherent functional behavior.  The rooms are currently "Garage" and "Laundry", reflecting the physical location of the devices.  There is a screen for creating new rooms.  Each device has a settings screen where you can assign it to a room.

### Scenes

A "scene" is a collection of settings for controls.  It is a way of creating a group of devices with corresponding states.  Right now there are two scenes "Garage Lights On" and "Garage Lights Off".  The first one, obviously, has all of the lights on, and the second has all of them off.

To manage scenes, click on the "Automation" tab (gear icon), then select Scenes.  You can then select a scene to edit or make a new one.  You can also run a scene to apply all of its setting to their devices, deactivate it to make it temporarily unavailable (or reactivate it), or delete it.

When editing a scene, there is a list of Assigned Devices that are already part of that scene, with the corresponding state - on or off.  There is also a list of Available Devices that you could add to the scene if you want, grouped by the room the device is in.  A device that is already in the Assigned list will not be shown in the Available list.

### Rules

A "rule" tells the controller software what to do when something happens, like when a sensor activates (for example, a button is pushed).  After the devices are paired (see below), you can change the automation rules by clicking the Automation tab (gear icon) then selecting Rules.  

Rules are basically "IF .. THEN" structures.  E.g. IF (the top button on the bedroom door switch is pushed OR the top button on the stairway door switch is pushed) THEN (activate the scene "Garage Lights On"). The IF clause can also include time-based clauses like (time >= 11PM), but they do not directly trigger the action, they only modify (enable or disable based on time) the other trigger. If you want to trigger something at a certain time you need to use a "Schedule".

The editing panel for a given Rule is a little involved, but you can figure it out with a little experimentation.  Basically it involves assigning sensors to the IF part, perhaps in combinations with AND/OR, and assigning controls or scenes to the THEN part.
### Schedules
If you want something to happen at a certain time of day, you can create a "Schedule" via Automation>Schedules. There are schedules for turning off the garage lights late at night ("Lights Out Tonite", see https://www.youtube.com/watch?v=eFYMl_OhiEs - classic Springsteen from when he was writing poetry instead of cheezy anthems - also great instrumental break starting at 2:20) and for turning on/off the water heater (aptly named "Water Heater On" and "Water Heater On").
