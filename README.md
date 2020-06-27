# Documentation
 Building &amp; installation documentation for Ubuntu Touch on the OnePlus 3/3T

```
 * Your warranty is now void.
 *
 * We are not responsible for bricked devices, dead SD cards,
 * thermonuclear war, or you getting fired because the alarm app failed. Please
 * do some research if you have any concerns about features included in this modification
 * before flashing it! YOU are choosing to make these modifications, and if
 * you point the finger at us for messing up your device, we are not responsible.
```

to install ubuntu touch on your OP3, you don't need to compile halium source, got directly to [Treblelizing your OP3(T) & Firmware & TWRP](#treblelizing-your-op3t--firmware--twrp)


### Table of Contents
* [Install prerequisites for building](#install-prerequisites-for-building)
* [Initializing local repo](#initializing-local-repo)
* [Syncing local repository](#syncing-local-repository)
* [Applying patches](#applying-patches)
* [Building HAL parts](#building-HAL-parts)
* [Treblelizing your OP3(T) & Firmware & TWRP](#treblelizing-your-op3t--firmware--twrp)
* [Installing using Erfan GSI](#installing-using-Erfan-GSI)
* [Note](#note)
* [Files](#Files)
* [Thanks](#thanks)

## Install prerequisites for building

### Debian based systems
```
sudo dpkg --add-architecture i386
sudo apt update
sudo apt install git gnupg flex bison gperf build-essential \
  zip bzr curl libc6-dev libncurses5-dev:i386 x11proto-core-dev \
  libx11-dev:i386 libreadline6-dev:i386 libgl1-mesa-glx:i386 \
  libgl1-mesa-dev g++-multilib mingw-w64-i686-dev tofrodos \
  python-markdown libxml2-utils xsltproc zlib1g-dev:i386 schedtool \
  repo liblz4-tool bc lzop imagemagick libncurses5 rsync
```

For ubuntu Focal, repo is not in the ubuntu repository, download it manually from https://packages.ubuntu.com/groovy/all/repo/download

## Initializing local repo
```
mkdir ~/Halium/ && cd ~/Halium/
repo init -u https://github.com/Halium/android -b halium-9.0 --depth=1
git clone https://github.com/OP3-Halium/local_manifests .repo/local_manifests/
git clone https://gitlab.com/JBBgameich/halium-install/ halium/scripts/ --depth 1
```

## Syncing local repository
```
cd ~/Halium/
repo sync --no-clone-bundle --no-tags -c -j`nproc`
```

## Applying patches
```
hybris-patches/apply-patches.sh --mb
```
**NOTE:** This will fail if you've already applied them; to revert the patches **and all other local changes** run `repo sync -l`

## Building HAL parts
Type the following to initilize the current environment for building:
```
. build/envsetup.sh
breakfast oneplus3         
export LANG=C LC_ALL=POSIX
```
To produce the required `halium-boot.img` kernel image for Ubuntu Touch, execute:
```
mka halium-boot
```
**NOTE:** If you've decided to install manually (without Erfan GSI) you also need to `mka systemimage`!

## Treblelizing your OP3(T) & Firmware & TWRP
For further information please read the subject on [XDA](https://forum.xda-developers.com/oneplus-3/oneplus-3--3t-cross-device-development/treble-lineageos-15-1-treble-oneplus-3-t3830455)
 - To sumup, GSI port requires a dedicated vendor partition.

Download the "[New][A/B] LineageOS 16.0 Treble system-as-root"
- Recovery : [twrp-op3treble-3.3.1-1.img](https://mega.nz/folder/UgdQRYSD#8s-_u2HJQZDEqNnFOnejxQ)
- LineageOS 16 image : [lineage-16.0-20190826-UNOFFICIAL-oneplus3.zip](https://mega.nz/folder/UgdQRYSD#8s-_u2HJQZDEqNnFOnejxQ)
- Firmware 9.0.6 : [oxygenos-9.0.6](https://github.com/nvertigo/firmware-3t) **be careful the firmwares are device specific**

Reboot into fastboot and install the TWRP recovery => Check the official link for installation [TWRP link](https://twrp.me/oneplus/oneplusthree.html)

Treblelize your OP3(t)
 - Follow the information there : https://forum.xda-developers.com/oneplus-3/oneplus-3--3t-cross-device-development/treble-lineageos-15-1-treble-oneplus-3-t3830455

Reboot into the TWRP Recovery
- Update the firmware
- Install lineage 16.0

Reboot into Lineage, confirm everything works fine.


## Installing using Erfan GSI

1. Download the latest GSI zip from [here](https://t.me/ErfanGSI)
2. Download the https://build.lolinet.com/file/halium/GSI/tools/halium-ramdisk.zip
3. Ensure your `/vendor` (after mounting) is populated with content from an Android 9 ROM (LineageOS or otherwise)
4. Flash the GSI zip file
5. Flash the `halium-boot.img` from before to your boot partition (if you didn't yet):
```
adb push ~/Halium/out/target/product/oneplus3/halium-boot.img /tmp/
adb shell "dd if=/tmp/halium-boot.img of=/dev/block/bootdevice/by-name/boot"
```
6. Flash the halium-ramdisk.zip
7. Reboot
8. Enjoy


## Note

Assuming your device is in fastboot mode you can now flash this image by simply using:
```
fastboot flash boot out/target/product/oneplus3/halium-boot.img
```

## Files 
https://drive.google.com/drive/folders/1vnJEKkhO3xqH-fWWG55-yxwx5K1EeKq7?usp=sharing
## Thanks
- Documentation based on https://github.com/ubports-oneplus5
- Another 3.18 kernel working with ErfanGSI : https://github.com/MotoZ-2016/android_kernel_motorola_msm8996/tree/halium-9.0
- https://github.com/OP3Treble Project


## Known issues & temporary fix
- Webbrowser not reconized as a mobile 
  -  edit the file ```o nano /opt/click.ubuntu.com/com.ubports.morph.browser/0.1/usr/lib/arm-linux-gnueabihf/qt5/qml/Morph/Web/UserAgent02.qml```
  -  chang line 68 to ``` return (screenDiagonal === 0) ? "unknown" : (screenDiagonal > 0 && screenDiagonal < 190) ? "small" : "small" ```
  
- Data doesn't work 
  ```cd /usr/share/ofono/scripts/ && ./activate-context```
- Wifi doesn't restart
  -  ```sudo nano /etc/udev/rules.d/90-oneplus3.rules```
  -  add   ```ACTION=="change|remove|create", DEVPATH=="/devices/soc/600000.qcom,pcie/pci0000:00/0000:00:00.0/0000:01:00.0/ieee80211/phy?", RUN+="/usr/bin/python3 /home/phablet/wlan_restart.py"```
  -  then   ```nano /home/phablet/wlan_restart.py```
  -  ```#!/usr/bin/python3
     import subprocess
     import os.path
     subprocess.Popen("echo sta > /sys/module/wlan/parameters/fwpath", shell=True)
     print("Wifi card set up for wlan activation")
```
 
