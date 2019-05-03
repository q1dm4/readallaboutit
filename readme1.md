
## Information
* [Website](https://www.maddev.de/) - For general information
* [Discord](https://discord.gg/7TT58jU) - For general support
* [Github Issues](https://github.com/Map-A-Droid/MAD/issues) - For reporting bugs (not for support!)
* [Supported Devices](https://github.com/Map-A-Droid/MAD-device-list) - Supported devices by MAD
* [Wiki Page](https://github.com/Map-A-Droid/MAD/wiki) - For new stuff & How-to guides.

## Requirements
- Python **3.6**
- MySQL database, with RocketMap or Monocle structure
- Rooted Android device
- PogoDroid token (only necessary for MITM Mode), obtainable [via Patreon](https://www.patreon.com/user?u=14159560)
- Game running in English, Deutsch or Français (for Quest mode)

>MAD is compatible with [this Monocle schema](https://raw.githubusercontent.com/whitewillem/PMSF/master/sql/cleandb.sql) and [this RocketMap fork](https://github.com/cecpk/OSM-Rocketmap). Please use them or change your database accordingly.

## Setup
### Ubuntu/Debian

Install `python 3.6` & `pip3` according to docs for your platform. Make sure you carefully check the command, since even if python3.6 is installed, the `python3` command could still point to `python3.5` or below!

Once Python is installed, ensure that `pip` and `python` is installed correctly by running:
* `python3 --version` - should return `3.6.x`
* `pip3 --version` - If it returns a version, it is working.

Clone this repository:
```bash
git clone https://github.com/Map-A-Droid/MAD.git
```

Make sure you're in the directory of MAD and run:
```bash
pip3 install -r requirements.txt
```
If you want to use OCR to scan raids or scanning for Quests, run with `requirements_ocr.txt` and install additional packages: `apt-get install tesseract-ocr python-opencv`


## MAD concept
![MAD concept graphic](static/concept.jpg)

**RGC (Remote GPS Controller)** is responsible for receiving the GPS commands from your server, taking screenshots (if OCR is enabled) and managing PoGo on the phone (restarting, detecting if PoGo is opened, etc)

**PogoDroid** is the MITM (Man in the middle) App for reading the data from the game and send it to your server. If you use the OCR method, you don’t need this app.

## Configuration
Inside the `configs` folder, duplicate the `config.ini.example` and rename it to `config.ini`. Then populate it with at least the database and websocket configurations.

### Mappings.json
In order to map devices to areas, do the same with `mappings_example.json` and rename it to `mappings.json`
Refer to mappings_example.json for examples or run `python3 start.py -wm` and open the MADMIN mappings editor (http://localhost:5000).

#### IV
MAD can scan for IVs with two different modes: `iv_mitm` scans already scanned mon specified by `mon_ids_iv`. A typical usecase is to just IV check rare spawns with it. The second mode is `mon_mitm`. That is just like regular mon scanning but every encounter defined in `mon_ids_iv` gets IV checked automatically with the same scanner - one encounter per location. Every other config parameter is explained in MADmin. 

### Geofence
Each area *requires* `geofence_included`. A geofence can easily be created with [geo.jesparke.net](http://geo.jasparke.net/)
> A geofence requires a name: `[geofence name]` with `lat, lng` per line, no empty lines at the end of file  

## Phone Setup
#### Lineage Install & Rooting
You need a device (phone) with root, running Lineage OS, with Magisk, PogoDroid and RGC installed.
1. Phone - Aquire one of the phones on this list.  (https://github.com/Map-A-Droid/MAD-device-list)
2. Lineage OS - Your phone needs to have an unlocked bootloader and be able to support applications running as root. Its recommended that you install [Lineage OS](https://lineageos.org/).  They have good installation instructions for each device.  Follow the Lineage instructions then comeback for the next step.  Make sure to install the Playstore as described in the Lineage instructions.  If you are an expert you can use a different OS, if not use Lineage.
2.1. Note, you not only need a phone on the MAD Device List, but you also need a version of that phone that can have Lineage installed.  For ex. Samsung has numerous versions numbers of the S5.  Some can have Lineage installed and others cant.  Lineage lists the phone version numbers that it is compatible with.
3. Magisk - Install [Magisk](https://www.xda-developers.com/how-to-install-magisk/) to root the phone via recovery. 
3.1. Repackage the MagiskManager App and add to Magisk Hide. Make sure to delete the folder `/sdcard/MagiskManager` after repackaging.

#### Applications
##### Pogo, RGC, PogoDroid
1. Pogo - Install the PoGo app from the Play Store.
1.1. Add Pogo to Magisk Hide
1.2. Inside of Magisk run the Safetynet Check.  You need to see all green.
>It's necessary to pass the Safetynet check to run the game on rooted phones. Check the Safetynet status in the MagiskManager App.

2. RGC - Install [RGC (Remote GPS Controller)](https://github.com/Map-A-Droid/MAD/blob/master/APK/RemoteGpsController.apk) 
2.1. RGC must be converted to a system app after it is installed.
2.2. Install [link2sd](https://play.google.com/store/apps/details?id=com.buak.Link2SD).  Once installed go into Link2sd and convert RGC to a system app.
2.3. In Lineage go into `Settings`, `Developer Options`.  Click on `Select mock location App`, select RGC.
2.4. Here are the configuration options for RGC
**Socket section**
-Socket = `ws://ipofyourserver:8080`
-Websocket origin = pick a short name for your phone, this will need to match what you put in PogoDroid.  This Origin needs to be unique per running python instance.
-Auth = off
**Rooted devices section**
-Reset GMS data = On
-Override OOM = Off
**Location Section**
-Reset AGPS data continuously = Off
-Reset AGPS once = Off
-Use Android Mock Location = On
**General Section**
-Start on Boot = On
-Start RGC Delay = 0
-Start services on appstart = On
-Start mediaprojection on appstart = Off

3. PogoDroid - Install [PogoDroid](https://www.maddev.de/apk/PogoDroid.apk) (only necessary for MITM mode) on the phone. 
3.1. Configure the POST destination for PogoDroid as `http://ipofyourserver:8000`.
3.2. To login into PogoDroid you need to be a [Patreon supporter](https://www.patreon.com/user?u=14159560).  Become a supporter and link your account to Discord.  
3.3. You can obtain a token by clicking on `Get Token` in PogoDroid and sending the command `!settoken <your_token>` to the MAD Discord Bot.

4. Turn on Airplane mode and make sure Wifi is still connected.
5. Before we finish, go inside of Magisk and run the Safetynet Check one last time.  You need to see all green before proceeding.

#### Rubberbanding
If you are experiencing weird and quick GPS jumps to the actual location from the phone, you can try these steps to fix it.

**Keep in mind that any of these possible fixes can cause boot loops or crashes. Please create a backup of your phone, if you don't want to start over.**

 - Use tinfoil around your phone (seriously)
 - Enable GMS reset
 - Disable Google Play Services background data
 - Set the device to GPS only
 - Disable Wi-Fi location (same settings tab as GPS)
 - Try flightmode with only Wi-Fi enabled

## Launching MAD
Make sure you're in the directory of MAD and run:
```bash
python3 start.py
```

Usually, you want to append `-wm` and `-os` as arguments to start madmin (browser-based monitoring) and the scanner (`-os`) responsible for controlling devices and receiving data from Pogodroid (if OCR enabled, also take screenshots).

If you want to run OCR on screenshots, run `-oo` to analyse screenshots

## MADMIN
MADMIN is a web frontend to configure MAD to your needs, see the current position of your devices, fix OCR failures. You can enable it with `with_madmin` in the config file or `-wm` as a command line argument. The default port is 5000. See the config.ini.example for more options.

## Tools
To make your life easier you can use the scripts in `scripts/`.

### Spawnpoint Importer (import_allspawns.sh)
If you used to scan before and happen to still have your spawnpoints in your Monocle or Rocketmap database then you can use this script to import them to MAD. You must have the trs_spawn table already in your database and you must have filled out the Database portion of the MAD config file!

### Intel Importer (intelimport.sh)
If you ran the MITM method for the first time, you will probably notice that all gyms are missing names and pictures. If you want to add this information, you can use this script. First of all, you'll need a CSV Export from the  [Ingress Intel Map](https://intel.ingress.com/intel). Install [IITC](https://iitc.me/) and the `IntelCsvExporterMADedition.js`
 in the scripts directory. Make sure to scrape all the necessary portals in your area and export the CSV file to your server. The second step is to run the script with the csv file as the first parameter.  
Example: `./intelimport.sh export.csv`.

## Security
RGC and PogoDroid both support wss/HTTPS respectively. Thus you can setup reverse proxies for MAD. The Auth headers in RGC and Pogodroid both use Basic auth. This means the password/username is not encrypted per default, that's to be done by SSL/TLS (wss, HTTPS).