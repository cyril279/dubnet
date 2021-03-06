# Bootable USB how-to's

- [MultiBoot USB](#multiboot-usb)  
- [Windows USB](#windows-usb)
- [Clonezilla (installed to hdd)](#clonezilla-install-to-hard-disk)
- [Update PC BIOS](#update-pc-bios)  
- [Volume label](#volume-label)  

## MultiBoot USB
https://mbusb.aguslr.com/  
let's have our standard util-iso's on one usb.  
clonezilla, gparted, etc...  

git clone the project, then from within the folder, prepare the usb.  
per [using the script](https://mbusb.aguslr.com/install.html#using-the-script):  
`./makeUSB.sh -b -e /dev/sdc ext4`

**Get bootable files**  
>Once you have a bootable USB drive, it only remains to copy the bootable files (ISO or kernel) to the pendrive. [...] save them into $mntusb/boot/isos.  

## Windows USB
WoeUSB (gui tool)  
Windows USB installation media creator for the gnu/linux platform  
`sudo zypper in WoeUSB`  
commandline usage example:
`sudo woeusb --workaround-bios-boot-flag -d Windows1809ALL.iso /dev/sdc`  
workaround flag ensures that the boot flag is set, because many legacy bios will skip the device if the flag is not set.  
It is unclear why [this is not enabled by default](https://github.com/slacka/WoeUSB/issues/193).  

## Clonezilla (Install to hard-disk)
https://clonezilla.org/livehd.php

- Decide mount/boot location (could be separate partition or space on local drive)
- Download clonezilla.zip
- unzip clonezilla to chosen directory  
  `unzip /path/of/clonezilla.zip -d /chosen/directory/`
- Rename _live_ to _live-hd_  
  `mv /chosen/directory/live /chosen/directory/live-hd`
- Append [grub] menuentry to `/etc/grub.d/40_custom`
```
menuentry "Clonezilla" {
  set root=(hd0,2)
  set livePath=/path/to/live-hd
  set liveUUID=<PARTUUID>
  linux $livePath/vmlinuz boot=live union=overlay username=user config components quiet noswap nolocales edd=on nomodeset ocs_live_run=\"ocs-live-general\" ocs_live_extra_param=\"\" keyboard-layouts= ocs_live_batch=\"no\" locales= vga=788 ip=frommedia nosplash live-media-path=$livePath bootfrom=/dev/disk/by-partuuid/$liveUUID toram=filesystem.squashfs
  initrd $livePath/initrd.img
  }
```
- update grub  
  `grub2-mkconfig -o /boot/grub2/grub.cfg`
- Reboot & profit

## Update PC BIOS
FreeDos [(per ArchWiki)]:  
**Procedure:**  
1. Grab the `USB "Full" installer` from http://www.freedos.org/download/
2. Extract the archive, locate the .img file
2. Determine which of /dev/sdX is your USB stick  
`lsblk`  
2. Write the image directly to the block device:  
`dd if=FD12FULL.img of=/dev/sdX status=progress` (where X is the letter representing your USB stick as a block device, don't write the image to a partition)  
2. Double-check that the image copying worked:  
`fdisk -l` (you should see a single partition on a DOS disk with the bootable ("boot") flag set)  
2. Mount the partition, and copy over the .exe used to update your firmware
  - Stay on the safe side and limit the filename to 8 characters (without extension), upper case
  - Ensure that you verified any checksums provided by your hardware vendor
2. Unmount and reboot. Do whatever is needed to boot from the USB drive

Once you are in the FreeDOS live installation environment:

1. Select your language
2. You will be prompted to install FreeDOS
  - Select `"No - Return to DOS"`
2. You should see a prompt (C:\>)
2. Verify that your firmware upgrade tool is present  
`dir /w`  
2. Run the executable
  - author note: in the case of the Dell tool, the machine displayed a spash screen and then rebooted. Upon reboot, it started the firmware upgrade automatically, and ran for about 2 minutes with the fan at full speed)
2. Once the process specific to your vendor completes, optionally verify through the BIOS setup screen, as well as by running `dmidecode` when you're back in linux  

## Volume label
https://www.tecmint.com/change-modify-linux-disk-partition-label-names/  
https://superuser.com/questions/1022992/how-to-change-a-usb-stick-name  

ext2,3,4  
`sudo e2label /dev/sdb3 multiboot-repo`  

ntfs  
`ntfslabel /dev/sda5 NTFS_DIR`  

exfat  
`exfatlabel /dev/sda3 EX_PART`

[per ArchWiki](https://wiki.archlinux.org/index.php/Flashing_BIOS_from_Linux#Using_a_FreeDOS-provided_Disk_Image_+_USB_stick)

