# Extend Pendrive Linux to utilize your entire USB flash drive.

#### WARNING: This document is under construction. Validation testing is in progress.  06/06/2015 neh

## Problem - 
Many of us have installed Ubuntu with persistence on USB flash drives using the Pendrive Linux Universal-USB-Installer. The maximum size of the persistent storage area is 4GB a limit set by the FAT32 file system. We would rather use the entire capacity of the USB drive. We want to preserve our existing work stored on our USB Ubuntu installations.

##Solution -
The Pendrive Lunix USB Ubuntu installation consists of many read-only files and one read-write file, 'casper-rw'. The persistent information the user has created is stored in that file.  This file can be backed up to another storage device. When that file is restored to the same or another USB installation, all of the user's work will be restored. Live Ubuntu USB can read either casper-rw files or a casper-rw partion which can be of any size. Here are the general steps needed to move one's work to a larger than 4-GB persistent space, a new disk partion:
- [ ] Backup your original USB stick in case of problems
- [ ] Mount the USB stick on a another Ubuntu system 
- [ ] Save the exitsting casper-rw file from the original USB
- [ ] Remove the Fat32 casper-rw file from that USB stick
- [ ] Shrink the Fat32 partition to the minimum size possible
- [ ] Create a large ext4 partion 'casper-rw' on the USB stick
- [ ] Copy the casper-rw information to the new partition
- [ ] Reboot the updated Ubuntu USB stick and resume using it.

**Note:** *If you are intializing your Ubuntu Live USB stick for the first time*, [this link shows how to utilize your entire USB stick for persistence](http://askubuntu.com/questions/397481/how-to-make-a-persistent-live-ubuntu-usb-with-more-than-4gb). A team member has reported sucess in creating a stick with that process. **I recommend that everyone view this link** in order to get more familiarity wiht the process to be described below.
### Here are the detailed steps.
**Backup your original USB stick.** Make a backup of your target USB stick in case of unforseen problems. You can use the Windows software *USB Image Tool* to make a backup to a windows folder on your hard drive.  See:
```
http://www.alexpage.de/usb-image-tool/
```
This software is simple free and portable; *no installation needed*. Or use your favorite backup tool. I use *Paragon Backup(TM) and Recovery 14 Free*.

**Mount the USB stick on another system.** Use Ubuntu to modify your target USB stick. USB Ubuntu cannot modify itself while running.  Instead use a another running Ubuntu instance, another bootalbe USB Ubuntu stick or someone elses Ubuntu computer.  In order to avoid confusion, use the an admin *hadoop* account to perform the modifications.  Insert your target USB stick to the running Ubuntu instance and it will be automatically mounted with label *'UUI'*.     

**Save the existing persistent file**, casper-rw, from the target USB to a directory on the running Ubuntu instance. Then remove that file from the target USB stick.     
```
mkdir ~/Documents/OriginalCasperRW 
sudo cp -rp /media/hadoop/UUI/casper-rw ~/Documents/OriginalCasperRW/
```
**Remove the Fat32 casper-rw file** from the orginal USB stick.
```
rm -r /media/hadoop/UUI/casper-rw
```
**Shrink the Fat32 partition** Open the partion editor Gparted on your running Ubuntu instance. You are going to shrink the FAT32 partition. (You will need to enter the admin password.) In the Gparted menu select the target USB drive, typically */dev/sdb*. The used space on the USB will typically be just over 1 GB for a 32-bit Ubuntu, and ?? GB for 64-bit Ubuntu. (Note that this is after deleting the 4GB casper-rw file.)
Select that target partition, left click to unmount, then left click to select Resize/Move and carefully use the down arrow on the New Size selection to reduce the size to the minimum plus 10 MB for good luck.  Don't change any other selections. after you select the operation, next select the 'check mark' to apply the action(s).   When this is done, the read-only ubuntu partion will be minimized, and there will be unallocated space available on the drive.

**Create a large casper-rw partion.** In order to do that, select the unallocated space and select 'New'. Take the defaults (to create a new ext4 partion). Enter the lable *casper-rw* . After all operations complete you should have a small bootable (Ubuntu read-only) FAT32-bootable file system 'UUI' and a large ext4 persistent file system 'casper-rw'.  Exit the Gparted tool.

**Copy the original casper-rw persistent data** to the new, larger, partition. First create a temporary mount point for the saved persistent file
```
sudo mkdir /media/hadoop/casper_fle
```
We will loop mount the casper-rw file contents here
```
sudo mount -o loop ~/Documents/OriginalCasperRW/casper-rw /media/hadoop/casper_file
```
Now copy persistent information to the new casper-rw partion
```
sudo cp -rp /media/hadoop/casper_file/* /media/hadoop/casper-rw
```
This completes the update process.  You can now eject the target USB drive. The temporary mount point *casper-file* can be removed.

**Reboot from your updated USB stick** to confirm your success. 

**Testing Notes:**  Testing was performed using the Ubuntu 14.04-2 32-bit desktop version. We will test with 64-bit soon. The PCs used for boot testing did not use use UEFI bios, rather the earlier bios type. Wewill test with a UEFI bios next.  Successful backups and restores of a FAT32 USB stick were performed. The transfer of persistent files was also sucessfully tested between FAT32 live Ubuntu USB sticks.  Successful Universal Installer USB installations of Ubuntu were performed on small 1.1GB FAT32 partions.  Sucessful transfers of casper-rw information were performed multiple times. Ask if you have any questions about testing and validation procedures that were used, please ask.


 