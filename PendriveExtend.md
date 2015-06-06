# Extend Pendrive Linux to utilize your entire USB flash drive.

## This document is under construction. It has only been partially validated. 

## Problem - 
Many of us have installed Ubuntu with persistence on USB flash drives using Pendrive Linux. The maximum size of the persistent storage area is 4GB, a limit set by the FAT32 file system. We would like to instead use the entire capacity of the USB drive. Further, we would like to preserve the existing work done on our USB Ubuntu installations.

##Solution -
The Pendrive Lunix USB installation consistes of mainly read-only files and one read-write file, 'casper-rw'. The persistent information the user has created is stored in that file.  The file casper-rw can be copied and saved to another storage device. When that file is restored to the same or another USB installation all of the user's work will be restored. In order to crate a larger than 4-GB persistent space
- [ ] Backup the entire USB stick in case of problems
- [ ] on a second USB system insert the USB stick, label 'UUI'
- [ ] Save the exitsting casper-rw file from UUI
- [ ] remove the casper-rw file from the USB stick UUI
- [ ] shrink the partition to the minimum ssize with partimage
- [ ] create an ext4 filesystem 'casper-rw' on the unused space
- [ ] reboot 

Here are the detailed steps.
Backup your existing USB stick
You can use the Windows software USB Image Tool to make a backup
http://www.alexpage.de/usb-image-tool/
(I have no affiliation with the author, I found positive recommendations, and the tool works for me.  This software is free and does not need to be installed in windows, it is portable.)
Or you can use your favorite backup tool. I use Paragon Backup(TM) and Recovery 14 Free. (This software is free, but needs to be installed on Windows. This software can also backup drives with multiple partions.)

- - - - ONLY VALIDATED UP TO THIS POINT  - - - - 

Assume that user on the ubuntu machine is logged  in as administrative user hadoop
First copy the existing persistent file, casper-rw, from the target USB.
cd ~
sudo cp -rp /media/hadoop/UUI/casper-rw .
Second, remove that file from the target USB 
sudo rm -r /media/hadoop/UUI/casper-rw .
Now open Gparted Partion Editor (you will need to enter admin password)
In the Gparted menu select the target USB drive, typically /dev/sdb 
The used space on the USB will typically be just over 1 GB for a 32-bit Ubuntu, and Y GB for 64-bit Ubuntu.   (This is after we have deleted the 4GB casper-rw file.)
Select the target partition, left click to unmount, then left click to select Resize/Move carefully use the down arrow on the New Size selection to reduce the size to the minimum plus 10 MB for good luck.  Don't change any other selections. Note that you need to select the operation, then next check the 'check mark' to apply the action(s).   When this is done, the read-only ubuntu partion will be minimized, and there will be unallocated space available on the drive.  Select the unallocated space and select 'New'. Take the defaults (to create a new ext4 partion) After all operations complete you should have a small bootable (Ubuntu read-only) FAT 32  bootable file system 'UUI' and a large ext4 persistent file system 'casper-rw'.  Exit Gparted.
Create a temporary mount point for the saved persistent file
sudo mkdir /media/hadoop/casper_fle
We will loop mount the casper-rw file contents here
cd ~ <to location of the saved casper-rw file from original installation>
sudo mount -o loop casper-rw /media/hadoop/casper_file
cd to UUI usb ubuntu direcory
sudo cp -r casper-rw ~
cd /media/hadoop
copy contents of the now-mounted saved persistent file to the casper-rw partion
sudo cp -rp casper_file/*  casper-rw

Thats it. You can select the UUI drive in the GUI and 'Savely eject the parent drive'. If all went well :) then you are good to go.  Just boot of the target USB
  520  cd /media/hadoop/casper_file
  523  cp -r * ../casper-rw
  524  sudo cp -r * ../casper-rw

  554  ls -l casper-rw

  563  mdir ~/Doc*/San_new_casper-rw

  571  mkdir San_new_casper-rw

  633  sudo mount -o loop casper-rw /media/hadoop/casper_file
  634  cd /media/hadoop

  655  sudo cp -rp casper_file/* casper-rw

  657  sudo diff -r casper_file casper-rw |more
  lash
