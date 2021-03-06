
# HowTo: ADI - My first KBUS application using ADI/DAL

## Every package is bundled to a specific firmware version: For Version 2019.3.1 - FW:03.00.35(99)

This HowTo describes how to add your first KBUS application for PFC.

Find within 3 simple demo programs for using the
Application-Device-Interface(ADI) to control KBUS from your own C/C++
application or retrieve information about connected IO-Terminals.
- kbusdemo	: Operating mode "running"
- getkbusinfo	: Using DBUS to retrieve Kbus details
- getkbusheader	: Build suitable header files for connected IO-Terminals

# ATTENTION:
The ADI/DAL could only be used by one master, it could be the CoDeSys-Runtime
or your own application. But not both.
Take care to stop Runtime before run this demo.
- Temporally:
```
	>/etc/init.d/runtime stop
```
- Permanent:
```
	#//Delete symbolic link
	>rm /etc/rc.d/S98_runtime
	#Recover symbolic link
	>ln -s /etc/init.d/runtime /etc/rc.d/S98_runtime
```

The device specific ADI/DAL extension for KBUS published in **"adi_application_interface.h"**.
Check also attached **"ptxproj/src_kbusdemo/adi_functions.txt"** for details.

# PREREQUISITES
This HowTo is / based on a clean installation of Ubuntu LTS, with an installed
and working WAGO Software-Development Kit for PFCx00-family.
Working means that you successfully built the standard image “sd.hdimg”

# About KBUS:

The Kbus self working like a shifting register, means reading inputs and
writing outputs are capsulated within one Kbus-cycle.

A Kbus-cycle triggered by a call of device specific function "libpackbus_Push()".

Operating modes for driving the Kbus:
## 1) Operating mode “running”, a better name would be “manual”

In mode “running” Kbus have to be driven by your application.
Kbus data only read or written on calling function "libpackbus_Push()".

## Kbus operating mode "running":
- Highly deterministic(RealTimePrio)
- More program code needed.
- You have to think about Kbus-TimeOut of Output-IO-Terminals

## What the hell means “Kbus-TimeOut of Output-IO-Terminals”?
Every WAGO Output-IO-Terminals monitors elapsed time since last Kbus-cycle.
If elapsed time exceed 50ms, IO-Terminal shut down the outputs.

## And what is ADI/DAL's "WatchDog" functions good for?
Function „adi->WatchdogTrigger()“ only needed for PROFIBUS and CANopen
where fieldbus driven by a separate CHIP(DPC31) or software-stack.
“WatchdogTrigger()” is used to inform external fieldbus stack(CHIP)
about a living PLC program.
For KBUS, where entire fieldbus controlled by software, there is
no need to call “WatchdogTrigger()”.

# Step by Step:
----------------------------------------------------------------------------
## 1.1) Copy content of attached folder "ptxproj/rules" into ~/<ptxproj>/rules.
```
	>cp rules/* ~/<ptxproj>/rules/
```

## 1.2) Copy and rename folder "ptxproj/src_kbusdemo" into ~/<ptxproj>/src/kbusdemo.
```
	>cp src_kbusdemo ~/<ptxproj>/src/kbusdemo
```

## 1.3) Take a look into the source code file for additional information.

##1.4) Select kbusdemo in ptxdist to be build
```
  >cd ~/<ptxproj>/
  >ptxdist menuconfig

  Wago Specific      -->
      Demo           -->
           [*] KBUS demo programs

  Save changes and exit.
```

## 1.5) Build examples
```
  >ptxdist targetinstall kbusdemo
```

If everything working fine, you should find newly created install package as: ~/<ptxproj>/platform-wago-pfcXXX/packages/kbusdemo_3.0_armhf.ipk

## 1.6) Build complied firmware image "sd.hdimg"
```
  >ptxdist images
```

If everything working fine, you should find newly created image as: ~/<ptxproj>/platform-wago-pfcXXX/images/sd.hdimg

---

# As usual, you can:
- Copy "binaries" somehow by hand into PFC's file system, and make them executable
- Copy image file "sd.hdimg" with command "dd" to SD-Card and boot PFC200 from it.
- Transfer package somehow "<pkg-name>.ipk" into PFC200 file system and call "ipkg install <pkg-name>.ipk
- Utilize Web-Based-Management(WBM) feature "Software-Upload"

----------------------------------------------------------------------------------------------------------------------
Using Web-Based-Management(WBM) feature "Software-Upload" for upload and installing IPKG packages
----------------------------------------------------------------------------------------------------------------------

## 2.1) Start your local browser, and navigate of PFC's default homepage(WBM)
```
  https://192.168.1.17
```

Ignore Cert-Warning ...

## 2.2) Select "Software-Upload" in left hand "Navigation" pane, You will request to authenticate!
```
  Login as "admin" with password "wago" (default)
```

## 2.3) Click on button [Browse...] open the local file dialog.
```
  Browse to folder "~/<ptxproj>/platform-wago-pfcXXX/packages/"
  Select package to install or update, here "kbusdemo_3.0_armhf.ipk".
```

## 2.4) Click on button [Start Upload].
Transfer selected file into PFC200 file system and show button [Submit].

## 2.5) In newly shown section "Activate new software", click on button [Submit] install package.
```
  Internally WBM just calls:
  >cd /home/
  >opkg install kbusdemo_3.0_armhf.ipk
```

Depending on type of package a restart of PFC200 may required.

## 2.6) Open a (ssh or serial) terminal session to PFC200
```
Login as "root" with password "wago" (default)
```

## 2.7) Exit Runtime before run this demo.
```
  >/etc/init.d/runtime stop
```

## 2.8) Let it run
```
 > kbusdemo

	**************************************************
	***       KBUS Demo Application V1.03          ***
	**************************************************
	DEBUG: source/LibraryLoader.c:300:LibraryLoader_ScanDirectory(): Processing libpackbus.so
	DEBUG: source/LibraryLoader.c:300:LibraryLoader_ScanDirectory(): Processing libmbs.so
	DEBUG: source/LibraryLoader.c:300:LibraryLoader_ScanDirectory(): Processing libdps.so
	DEBUG: source/LibraryLoader.c:300:LibraryLoader_ScanDirectory(): Processing libcanopens.so
	DEBUG: source/LibraryLoader.c:300:LibraryLoader_ScanDirectory(): Processing libcanopenm.so
	DEBUG: source/LibraryLoader.c:300:LibraryLoader_ScanDirectory(): Processing libcanlayer2.so
	DEBUG: source/LibraryLoader.c:300:LibraryLoader_ScanDirectory(): Processing ..
	DEBUG: source/LibraryLoader.c:300:LibraryLoader_ScanDirectory(): Processing .
	KBUS device found as device 0
	switch to RT Priority 'KBUS_MAINPRIO'
	KBUS device open OK
	Set application state to 'Unconfigured'
	0:00:01 State =
			Loop/s = 1  Input Data = 00 Output data = 01
	0:00:02 State =
			Loop/s = 403  Input Data = 00 Output data = 02
	0:00:03 State =
			Loop/s = 970  Input Data = 00 Output data = 03
	0:00:04 State =
			Loop/s = 965  Input Data = 00 Output data = 04
	0:00:05 State =
			Loop/s = 968  Input Data = 00 Output data = 05
	0:00:06 State =
			Loop/s = 969  Input Data = 00 Output data = 06
	0:00:07 State =
			Loop/s = 968  Input Data = 00 Output data = 07
	0:00:08 State =
			Loop/s = 969  Input Data = 00 Output data = 08

```

# Compatibility list:
| PFC | Compatible |
|:-------------|:------------:|
| **PFC 100** | |
| 750-8100 | Y |
| 750-8101 | Y |
| 750-8102 | Y |
|  |  |
| **PFC 200** | |
| 750-8202 | Y |
| 750-8203 | Y |
| 750-8204 | Y |
| 750-8206 | Y |
| 750-8207 | Y |
| 750-8208 | Y |
|  |  |
| **PFC 200 G2** | |
| 750-8212 | Y |
| 750-8213 | Y |
| 750-8214 | Y |
| 750-8216 | Y |

