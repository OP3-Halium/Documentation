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

### Table of Contents
 * [Install prerequisites for building](# Install prerequisites for building)
 * [Initializing local repo](# Initializing local repo)
 * [Syncing local repository](# Syncing local repository)
 * [Applying patches](# Applying patches)
 * [Building HAL parts](# Building HAL parts)
 * [Treblelizing your OP3(T) & Firmware & TWRP](# Treblelizing your OP3(T) & Firmware & TWRP)
 * [Installing using Erfan GSI](# Installing using Erfan GSI)
 * [Note](# Note)
 * [Halium-boot.img MD5SUM](# Halium-boot.img MD5SUM)
 * [Thanks](# Thanks)

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

## Halium-boot.img MD5SUM
0e50d15afd6e187045875829a7166018  halium-boot.img

## Thanks
- Documentation based on https://github.com/ubports-oneplus5
- Another 3.18 kernel working with ErfanGSI : https://github.com/MotoZ-2016/android_kernel_motorola_msm8996/tree/halium-9.0
