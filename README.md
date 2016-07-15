# ffos_on_lgfx0
update Instructions for ffos 2.6 (Firefox OS 2.6) on LG fx0 (LGL25)

i collected all the install instructions and its necessary files into these github repo.
it woked for me, but can maybe brick you phone. ;)

<img src="https://github.com/stephanfriedrich/ffos_on_lgfx0/blob/master/lg_fx0.jpeg?raw=true" alt="Cell Phone LG Fx0">

# installation

######################
##1) enable fastboot
At first you have to unlock your Device to install Firefox OS.

"Fastboot mode is important because it is the first step in flashing custom updated and newer builds of Firefox OS/B2G on our phones. Fastboot is the first step because it is the mode that will actually allow us to flash new images (like custom recoveries new Firefox OS versions) to our devices in a mostly safe manner. Access to fastboot mode simply means that our device is "unlocked" and ready to hack and modify!"(quote from source)

1] Enable the DEVELOPER-MENU and ADB-DEBUGGING on your phone,  Settings > Device Information > More Information > Developer Menu switch.

2] check adb knows device
- adb devices

3] enter schell
- sudo adb shell

4] start root
- su

5] Backup your laf partition before deletion
- dd if=/dev/block/platform/msm_sdcc.1/by-name/laf of=/sdcard/laf.img

6] exit shell
- exit

7] pull the laf backup
- adb pull /sdcard/laf.img

8] Enter the shell again:
- sudo adb shell

9] Erase laf partition
- dd if=/dev/zero of=/dev/block/platform/msm_sdcc.1/by-name/laf

10] exit shell
- exit

11] reboot
- adb reboot


12] Enter into FASHBOOT

12_1] shutdown device

12_2] First hold down the VOLUME UP button and insert a usb cable that is CONNECTED TO A COMPUTER.
Then the Display should show Download-Titel on Black-Background

-> see helper images from ./install_fastboot/fastboot_menu.jpg

#### source:
https://www.reddit.com/r/FireFoxOS/comments/3uf92h/fx0_lgl25_fastboot_access_and_information/


#########################
##2) install TWRP v2.8.7.0
TWRP is a custom recovery used to install custom software on your Android device.
Boot TWRP:
- 1) press and hold button power & volume down, until "au"-logo appears, then release both buttons and quickly hold both buttons again (in my case i it wont start TWRP, but an FX0 programm which i dont nee)ded
-  2) connect usb cable to phone, type into terminal ´sudo adb reboot recovery´


1] install TWRP by comunity-pre-compiled img
- fastboot flash recovery twrpfilename.img

2] after that completes successfully 

2_1] you remove battery, reinsert usb-cable, power on to start ffOS

3] boot into recovery mode, to start TWRP-Menu to see if twrp is installed 
- adb reboot recovery

4] reboot device
- twrp-menu > reboot > system

### start TWRP
- adb reboot recovery

#### source:
https://www.reddit.com/r/fx0/comments/41p2tf/twrp_download_how_to_build/?sort=new


########################
##3) install FFOS 2.6

0] connect the device to the PC using USB wire with the debug mode and adb activated

1] give the install script exec permission
- chmod +x shallow_flash.sh

2] start the script to flash the device (see folder ./install_ffos/ffos2_6/ )
- ./shallow_flash.sh --gaia=gaia.zip --gecko=b2g-46.0a1.en-US.android-arm.tar.gz

3] reboot device if successful eg with:
- adb reboot

#### source:
https://ftp.mozilla.org/pub/b2g/nightly/latest-mozilla-central-flame-kk/
https://github.com/Mozilla-TWQA/B2G-flash-tool/blob/master/shallow_flash.sh

########################
##4) install bluetooth and NFC bugfix for FFOS 2.6
It fixes the bluetooth and NFC issues by:
Flashing a modified boot image that contains an updated ramdisk (kernel is untouched).
This flashable zip also fixes the tiny bootanimation problem.



1] connect device to PC via USB and start TWRP
- adb reboot recovery

2] copy fxup.zip ( ./install_bugfix/fxup.zip ) to internat or microSD storage

2] within TWRP install fxup.zip
- twrp-menu > install > select Storage > internal or microSD > click fxup.zip and install it

3] reboot device into ffos and it should work

#### source:
https://www.reddit.com/r/fx0/comments/47makh/fxos_26_compatibility_update_package/?sort=new


########################
##5) change Default Network from CDMA to 3G/4G/...
e.g. to use your Device from Europe

1] pull config
- adb pull /system/build.prop

2] edit the line with "ro.telephony.default_network=5" within build.prop
- change line with "ro.telephony.default_network=10"

3] remount the system with writte permissions
- adb shell
- su && stop b2g
- mount -o rw,remount /
- mount -o rw,remount /system
- exit && exit

4] upload the edited file to the internal SD card
- adb push build.prop /sdcard/

5] to pass the edited file from sdcard to the /system partition
- adb shell && su
- cp -R /sdcard/build.prop /system/
- chmod 644 /system/build.prop
- reboot

6] we go to "Settings/mobile network and data" and at "Operator" we choose "automatic"... ready


#### source:
https://www.reddit.com/r/fx0/comments/430x8q/how_to_install_firefox_os_26_en_lg_fx0/?sort=new

