# RyzenTosh
Here is an EFI for an Asus ROG Strix X570-E Gaming Motherbord with A RX580 GPU running Big Sur.

This EFI was implemented using OpenCore 0.6.4 excellent documentation.

https://dortania.github.io/OpenCore-Install-Guide/

# Adobe CS

Additionally there is some code to fix Adobe CS 2021 running on a Ryzentosh

https://github.com/megatronlabs/RyzenTosh/blob/main/AdobeFix/AdobeFix.md

# OBS

OBS Virtual Camera was not working in various applications (Teams, Chime, Zoom).  It has to do with a code siging issue.

```
020-12-18 11:18:35.473 Amazon Chime[2761:155841] Error loading /Library/CoreMediaIO/Plug-Ins/DAL/obs-mac-virtualcam.plugin/Contents/MacOS/obs-mac-virtualcam:  dlopen(/Library/CoreMediaIO/Plug-Ins/DAL/obs-mac-virtualcam.plugin/Contents/MacOS/obs-mac-virtualcam, 262): no suitable image found.  Did find:
	/Library/CoreMediaIO/Plug-Ins/DAL/obs-mac-virtualcam.plugin/Contents/MacOS/obs-mac-virtualcam: code signature in (/Library/CoreMediaIO/Plug-Ins/DAL/obs-mac-virtualcam.plugin/Contents/MacOS/obs-mac-virtualcam) not valid for use in process using Library Validation: mapping process and mapped file (non-platform) have different Team IDs
	/Library/CoreMediaIO/Plug-Ins/DAL/obs-mac-virtualcam.plugin/Contents/MacOS/obs-mac-virtualcam: stat() failed with errno=3

```

Using this thead, i resolved it:  https://github.com/johnboiles/obs-mac-virtualcam/wiki/Compatibility#apps-dont-allow-dal-plugins

Specifically

```
sudo codesign -f -s - /Applications/zoom.us.app
```
Microsoft Teams

```
sudo codesign --remove-signature /Applications/Microsoft\ Teams.app/Contents/Frameworks/Microsoft\ Teams\ Helper\ \(Plugin\).app 
```
