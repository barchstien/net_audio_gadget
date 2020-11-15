# net_audio_gadget

Based on armbian 
Using Nanopi Duo2, but any board supported by armbian with USB device, USB power and network would do
Use USB 3 that provides more power

# Folder and files
TODO

# Architecture
```
1.Host 
2.   |---usb (g_audio)---> nano-audio
3.                                  |---wifi---> nano-audio-render
4.                                                               |------> Amp + Speakers

1. any host supporting USB audio card
2. nanopi duo2 with audio gadget module
3. any linux PC with audio out, running nano-audio-render
4. any aplifier and speaker(s)
```

## nano-audio
Reference platform is **nanopi duo2**
Requirements are :
* USB powered
* USB device mode
* Wifi
* run nano-audio
  * auto-start
  * look for Wifi config file, apply it
  * look for wifi status, modprobe g_audio
  * stream to nano-audio-render.local:2543 as UDP

Python ? Bash ?
OverlayFS

## nano-audio-render
Any Linux with an audio out card
* auto-start
* hostname to nano-audio-render
* avahi enabled


# Build
## armbian image
Using debian based
Download : https://www.armbian.com/nanopi-duo-2/

Or build from source
```bash
git clone --depth 1 https://github.com/armbian/build  
cd build
# run ./compile.sh for interrcative menu or use options
./compile.sh  BOARD=nanopiduo2 BRANCH=current RELEASE=buster BUILD_MINIMAL=no BUILD_DESKTOP=no KERNEL_ONLY=no KERNEL_CONFIGURE=yes COMPRESS_OUTPUTIMAGE=sha,gpg,img

sudo dd bs=4M if=output/images/Armbian_20.11.0-trunk_Nanopiduo2_buster_current_5.9.8.img of=/dev/sdX status=progress
```

## Configure wifi
```bash
minicom -s
# /dev/ttyACM0

nmcli d wifi connect SSID password XXX

# enable avahi for name.local
armbian-config
```

## Configure audio gadget
```bash
# remove USB serial first
sudo modprobe -r g_serial

sudo modprobe g_audio

# device shows up audio to host
# from device, test record from CLI with
arecord -l

# use card (c), device (d), subdevice (s)
arecord -D hw:c,d,s -f S16_LE -c 2 -r 48000 recorded.wav
```

## TODO
* use https://docs.armbian.com/Developer-Guide_User-Configurations/ to custom make :
  * user/pass
  * script to stream
  * script to config wifi from SD card (SSID/PASS)

* script to set prefered wifi SSID/PASS
* on wifi connect, remove g_serial, insert g_audio
* and vice versa
* Readme on how to list/connect to wifi
* print user/pass on board
* put board in simple box, to avoid shortcuts with env

* stream audio as OGG
* receive audio on remote (bash ? python ?), should auto start
* use avahi or alike to auto discover (better than static IP)
* read-only root, or overlayFS

### AIM
* recipe to build/deploy/config all
* archive with :
  * img ready to insert (Wifi confif needed)
  * remote app ready to install

