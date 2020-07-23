# ramdiskutil

## Description
A set of tools that can make a ssh ramdisk for 32-bit iDevices. <br />
macOS supported only. Will never support other OSes. <br />

## Usage
**pzb**: Download firmware partially, only download a part of it. Saves your bandwidth and time. You just need to download restoreramdisk, ibss, ibec, devicetree and kernelcache. <br />

**iBoot32Patcher**: Patch iBEC and iBSS for removing sigchecks and adding boot-args. <br />

**mount.sh**: Used for mounting ramdisk. The ramdisk should be renamed to RestoreRamdisk.dmg <br />

**unmount.sh**: Used for unmounting ramdisk. The ramdisk should be renamed to RestoreRamdisk.dmg <br />

**xpwntool**: Decrypt firmware components. Used to decrypt ibss, ibec, and other things. <br />

**packimg3.sh**: Pack ramdisk to a img3 container. For an iOS device, only img3 format is suitable for booting. <br />

**irecovery**: A tool for communicating with device in DFU mode <br />

## Methods
1. Goto ipsw.me/keys (Requires login) or theiphonewiki.com/wiki/Firmware\_Keys to find keys and ivs. Write down the filename of RestoreRamdisk. <br />
2. Use pzb to download: <br />
./pzb [LINK TO IPSW] <br />
Files needed to download: <br />
**(1) XXX-XXXX-XXX.dmg (the name of RestoreRamdisk) <br />
(2) Firmware/dfu/iBEC.xxx.RELEASE.dfu <br />
(3) Firmware/dfu/iBSS.xxx.RELEASE.dfu <br />
(4) kernelcache.release.xxx <br />
(5) Firmware/all\_flash/all\_flash.xxx.production/DeviceTree.xxx.img3 <br />**
3. Decrypt all the files using xpwntool <br />
**./xpwntool \[devicetree/kernelcache\] \[out\_file\] -iv \[iv\] -k \[key\] -decrypt <br />
./xpwntool \[ramdisk/iBEC/iBSS\] \[out\_file\] -iv \[iv\] -k \[key\] <br />**
4. Patch iBSS and iBEC <br />
**./iBoot32Patcher \[decrypted\_ibss\] \[patched\_file\] <br />
./iBoot32Patcher \[decrypted\_ibec\] \[patched\_file\] -b "rd=md0 -v amfi=0xff cs\_enforcement\_disable=1" <br />**
5. Mount the ramdisk <br />
**mkdir mp <br />
./mount.sh <br />**
6. Extract sshd to ramdisk <br />
**tar -xvf ssh.tar -C mp <br />**
*You can modify mp/etc/rc.boot for doing something when booting ramdisk.* <br />
7. Unmount the ramdisk, then make the img3 image of ramdisk.  <br />
**./unmount.sh <br />
./packimg3.sh <br />**
8. Boot the ramdisk <br />
*Let your device enter kDFU mode. <br />
for iPhone, send kloader and patched iBSS to the root directory of your device. for iPad, send iBEC instead of iBSS. <br />
ssh into your device and run: /kloader /\[Your ibss or ibec\]* <br />
**\{ <br />
./image3maker -t ibec -f \[patched\_ibec\] -o pwnediBEC <br />
./irecovery -f pwnediBEC <br />
} <br />**
*Skip these command in* ***\{\}*** *if it is an iPad <br />*
**./irecovery -s <br />
On the shell, type: <br />
/send \[devicetree\] <br />
devicetree <br />
/send \[ramdisk\] <br />
ramdisk <br />
/send \[kernelcache\] <br />
bootx <br />**
*Then your ramdisk will be successfully booted!*
# NOTE
***ida_patcher and restored_external_verbose_patch.dif is made for booting in verbose mode.

If you don't want apple logo shown when booted, please patch mp/usr/local/bin/restored\_external using this command:

./ida\_patcher -i restored\_external -p restored_external_verbose_patch.dif
