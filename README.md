# ramdiskutil

## Description
A set of tools that can make a ssh ramdisk for 32-bit iDevices.

## Usage
*pzb*: Download firmware partially, only download a part of it. Saves your bandwidth and time. You just need to download restoreramdisk, ibss, ibec, devicetree and kernelcache.

*iBoot32Patcher*: Patch iBEC and iBSS for removing sigchecks and adding boot-args.

*mount.sh*: Used for mounting ramdisk. The ramdisk should be renamed to RestoreRamdisk.dmg

*unmount.sh*: Used for unmounting ramdisk. The ramdisk should be renamed to RestoreRamdisk.dmg

*xpwntool*: Decrypt firmware components. Used to decrypt ibss, ibec, and other things.

*packimg3.sh*: Pack ramdisk to a img3 container. For an iOS device, only img3 format is suitable for booting.

## Methods
1. Goto ipsw.me/keys (Requires login) or theiphonewiki.com/wiki/Firmware\_Keys to find keys and ivs. Write down the filename of RestoreRamdisk.
2. Use pzb to download:
./pzb [LINK TO IPSW]
Files needed to download:
(1) XXX-XXXX-XXX.dmg (the name of RestoreRamdisk)
(2) Firmware/dfu/iBEC.xxx.RELEASE.dfu
(3) Firmware/dfu/iBSS.xxx.RELEASE.dfu
(4) kernelcache.release.xxx
(5) Firmware/all\_flash/all\_flash.xxx.production/DeviceTree.xxx.img3
3. Decrypt all the files using xpwntool
./xpwntool \[devicetree/kernelcache\] \[out\_file\] -iv \[iv\] -k \[key\] -decrypt
./xpwntool \[ramdisk/iBEC/iBSS\] \[out\_file\] -iv \[iv\] -k \[key\]
4. Patch iBSS and iBEC
./iBoot32Patcher \[decrypted\_ibss\] \[patched\_file\]
./iBoot32Patcher \[decrypted\_ibec\] \[patched\_file\] -b "rd=md0 -v amfi=0xff cs\_enforcement\_disable=1"
5. Mount the ramdisk

