# DISCLAIMER
USE AT YOUR OWN RISC

AUF EIGENE GEFAHR ZU VERWENDEN



This is the result of a leisure project. I am not a professional programmer.

Dies ist das Ergebnis eines Freizeitprojektes. Ich bin kein professioneller Programmierer.




If you want to support "The masterpice of gonzo7734", you can support him via paypal: https://www.paypal.com/paypalme/amelu96 Shoutout to gonzo7734 thx :)



# dbus-goecharger
Integrate go-eCharger (v4) into Victron Energiy Venus OS with automatic mode and PvSurPlus without the need of Go-e Controller.

Integrierte den go-eCharger (v4) in das Victron Energy Venus OS mit automatischem Lademodus mit Überschussladung ohne den Go-e Controller.



may be compatible with v3. please try.

könnte mit v3 kompatibel sein. bitte ausprobieren.



if you have Hardware Version v1 or v2 please remove the lines 38 / 39

    if hardwareVersion < 3:
    
      raise ValueError("Minimum hardware version required is 3.")
      
and report, if everything is working correctly. Some posts on the internet said, that the go-eCharger API or the API Key are different in the hardware versions v1/v2/v3/v4


## Purpose
With the scripts in this repo it should be easy possible to install, uninstall, restart a service that connects the go-eCharger to the VenusOS and GX devices from Victron.
Idea is inspired on @fabian-lauer and @trixing project linked below, many thanks for sharing the knowledge:
- https://github.com/fabian-lauer/dbus-shelly-3em-smartmeter
- https://github.com/trixing/venus.dbus-twc3

Based on good work from @0x7878 and @vikt0rm and gonzo7734
- https://github.com/vikt0rm/dbus-goecharger
- https://github.com/0x7878/dbus-goecharger
- https://github.com/gonzo7734/dbus-goecharger

## How it works
### My setup
- Grid: 3-Phase installation 230v/400v
- Ekrano + 3x15kva MPII + victron ct75em + 3xAC PV shelly 3em kaco/sma/kostal (28kva)+ opendtu/ahoydtu 3x hoymiles 4kva +8kwp DC MPPT every PV is installed on the (half side island)
  - Make sure in your go-eCharger app that _http api v2_ is activated and both ess and go e is @same VLAN /subnet

### Details / Process
What is the script doing:
- Running as a service
- connecting to DBus of the Venus OS `com.victronenergy.evcharger.http_{DeviceInstanceID_from_config}`
- After successful DBus connection go-eCharger is accessed via REST-API - simply the /api/status is called and a JSON is returned with all details
- Paths are added to the DBus with default value 0 - including some settings like name, etc
- After that a "loop" is started which pulls go-eCharger data every 750ms from the REST-API and updates the values in the DBus
- You can interact with VRM Portal CONTROL section with your go-eCharger: AUTOMATIC mode & MANUAL mode are Working, futhermore you can enable / disable Charging in both modes.
- In AUTOMATIC mode the go-eCharger is feeded with the information of your Victron World and decides with it's integrated algorithm how to charge the car (1 / 3 phase and Current from 6 to 32 amps)

Thats it 😄

### Restrictions
Planned Charging / Shedulded Charing is no implemented.
If you have (only) DC connected PV System you have to edit the lines 201 - 205. (section: '# pPv ermitteln. Kumulierte Leistung aller PV Anlagen auf allen Phasen')


### Pictures
![VenusOS device list](/pics/device-list.png)
![VenusOS / device / go-eCharger](/pics/go-eCharger-overview.png)
![VRM / controls](/pics/vrm-controls.png)
![VRM / overview](/pics/vrm-overview.png)
![VRM / details](/pics/vrm-details.png)

## Install & Configuration

## Configure your go-eCharger
- Wifi Connection has to be established
- HTTP v2 API has to be enabled
- set Max Ampere: Settings / Security / Grid / _set your correct settings referring to your country, energy-provider and installation_
- set PV SurPlus: Settings / charging config / PV-surplus / _active first parameter and set the parameters you like or leave default_

### Get the code
Just grap a copy of the main branche and copy them to a folder under `/data/` e.g. `/data/dbus-goecharger`.
Set permissions to execute for "install.sh", "restart.sh" and "uninstall.sh"
After that call the install.sh script.


### Change config.ini
Within the project there is a file `/data/dbus-goecharger/config.ini` - just change the values - most important is the deviceinstance under "DEFAULT" and host in section "ONPREMISE". More details below:

| Section  | Config vlaue | Explanation |
| ------------- | ------------- | ------------- |
| DEFAULT  | AccessType | Fixed value 'OnPremise' |
| DEFAULT  | SignOfLifeLog  | Time in minutes how often a status is added to the log-file `current.log` with log-level INFO |
| DEFAULT  | Deviceinstance | Unique ID identifying the go-eCharger in Venus OS |
| DEFAULT  | HardwareVersion | Type in your hardware version of you go-eCharger, only v4 supported atm |
| DEFAULT  | Position | for correct display of the EVcharger in your VRM
| DEFAULT  | Logging | set to ERROR for normal run. if you are heaving trouble understanding whats going on or whats the status set to WARNING or INFO
| ONPREMISE  | Host | IP or hostname of on-premise go-eCharger web-interface |


## Usefull links
- https://github.com/victronenergy/venus/wiki/dbus#evcharger
- https://github.com/goecharger/go-eCharger-API-v2/blob/main/apikeys-de.md

