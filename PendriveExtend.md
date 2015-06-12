# Extend Pendrive Linux to utilize your entire USB flash drive.

#### Note: Validation testing is in progress.  06/12/2015 neh

## Problem -
Many of us have installed Ubuntu with persistence on USB flash drives using the Pendrive Linux Universal-USB-Installer. The maximum size of the persistent storage area is 4GB, a limit set by the FAT32 file system. We would rather use the entire capacity of the USB drive and preserve our existing work.  This issue was raised by the [TSSG Data Analytics team](https://github.com/mikec964/chelmbigstock/wiki). They were implementing a [hands-on learning exercise invoving Apachie Hadoop running on live USB Ubuntu](https://github.com/mikec964/chelmbigstock/wiki/Learning-Hadoop). To avoid dual booting Windows PCs under warranty, several team menbers installed Ubuntu on USB 3 flash drives. 
##Solution -
The Pendrive FAT32 live USB Ubuntu installation consists of many read-only files and one read-write file, 'casper-rw'. The persistent information the user has created is stored in that file.  This file can be backed up to another storage device. When that file is restored to the same or another USB installation, all of the user's work will be restored. The live Ubuntu installation can read either casper-rw files or a casper-rw partion. A Linux formatted partion can be of any size. *Here are the general steps needed to move one's work to a larger than 4-GB persistent space, a new disk partion:*
- [ ] 1) Backup your original USB stick in case of problems
- [ ] 2) Mount the target USB stick on a another Ubuntu system 
- [ ] 3) Save the existing casper-rw file from the original USB stick
- [ ] 4) Remove the Fat32 casper-rw file from that USB stick
- [ ] 5) Shrink the Fat32 partition to the minimum size possible
- [ ] 6) Create a large ext4 partion 'casper-rw' on the rest of the USB stick
- [ ] 7) Copy the casper-rw information to that new partition
- [ ] 8) Reboot the updated Ubuntu USB stick and resume using it.

**Note:** *If you are intializing your Ubuntu Live USB stick for the first time*, [this link shows how you can utilize your entire USB stick for persistence](http://askubuntu.com/questions/397481/how-to-make-a-persistent-live-ubuntu-usb-with-more-than-4gb). A team member has reported sucess in creating a USB stick with that process. **I recommend that everyone view this link** in order to get more familiarity with the process used in steps 2 through 6 below.
### Here are the detailed steps.
**1) Backup your original USB stick.** Make a backup of your target USB stick in case of unforseen problems. You can use the Windows software *USB Image Tool* to make a backup to a windows folder on your hard drive.  See:
```
http://www.alexpage.de/usb-image-tool/
```
This software is simple free and portable; *no installation needed*. Or use your favorite backup tool.

**2) Mount the USB stick on another system.** Use Ubuntu to modify your target USB stick. *Modifying a live USB Ubuntu instance while it is running is NOT recommended.*  Use a another running Ubuntu instance, another bootable USB Ubuntu stick or someone elses Ubuntu computer.  To avoid confusion for the TSSG application of this solution, use the an admin *hadoop* account to perform the modifications.  Insert your target USB stick to the running Ubuntu instance and it will be automatically mounted with label *'UUI'*.

**3) Save the existing persistent file**, casper-rw, from the target USB to a directory on the running Ubuntu instance.
```
mkdir ~/Documents/OriginalCasperRW 
sudo cp -rp /media/hadoop/UUI/casper-rw ~/Documents/OriginalCasperRW/
```
Note that you can't easily view the contents of the persistent information; it is compressed and stored as a file.

```
sudo ls -l ~/Documents/Original/CasperRW/
```

**4) Remove the Fat32 casper-rw file** from the orginal USB stick.
```
rm -r /media/hadoop/UUI/casper-rw
```
**5) Shrink the Fat32 partition** Open the partion editor *Gparted* on your running Ubuntu instance. (You will need to enter the admin password.) You are going to shrink the FAT32 partition. In the Gparted menu select the target USB drive, typically */dev/sdb*. The used space on the USB will typically be just over 1 GB for a 32-bit Ubuntu, and ?? GB for 64-bit Ubuntu. (Note that this is after deleting the 4GB casper-rw file.)
Select that target partition, left click to unmount, then left click to select Resize/Move and carefully use the down arrow on the New Size selection to reduce the size to the minimum plus 10 MB for good luck.  Don't change any other selections. After you select the operation, next select the 'check mark' to apply the action(s).   When this is done, the read-only ubuntu partion will be minimized, and there will be unallocated space available on the drive.

**6) Create a large casper-rw partion.** In order to do that, select the unallocated space and select 'New'. Take the defaults (to create a new ext4 partion). Enter the lable *casper-rw* . After all operations complete you should have a small bootable (Ubuntu read-only) FAT32-bootable partition 'UUI' and a large ext4 partition 'casper-rw'.  Exit the Gparted tool.

**7) Copy the original casper-rw persistent data** to the new, larger, partition. First create a temporary mount point for the saved persistent file
```
sudo mkdir /media/hadoop/casper_file
```
Then loop-mount the casper-rw file contents there.
```
sudo mount -o loop ~/Documents/OriginalCasperRW/casper-rw /media/hadoop/casper_file
```
Note that now the content of the persistent files can be viewed at the new mount point. These are some sort of delta files, not sure if you can see the indivdual files. ??
```
sudo ls -l /media/hadoop/casper_file
```

Now copy the existing persistent information to the new casper-rw partion
```
sudo cp -rp /media/hadoop/casper_file/* /media/hadoop/casper-rw
```
Optionally you can compare the original to the new persistent files on your USB stick.
```
sudo ls -l /media/hadoop/casper_file
sudo ls -l /media/hadoop/casper-rw
```
This completes the update process.  You can now eject the target USB drive and remove it from the computer. Optionally the temporary mount point *casper-file* can be removed; it will persist otherwise.

**8) Reboot from your updated USB stick** Install the updated USB stick in your target computer and reboot to confirm your success. Once your Ubuntu system comes up go to your home directory and check how much free space you have a available. 

**Testing Notes:**  Testing was performed using the Ubuntu 14.04-2 32-bit desktop version. Testing was done on PCs with BIOS boot. *Volunteers are needed to test the 64-bit version and UEFI booting.*  Successful backups and restores of a FAT32 USB stick were performed. The transfer of persistent info was succesfully performed between FAT32 live Ubuntu USB sticks either as a file or a partition.  Note: You can view and modify partions under windows using the AOMEI Partion Assistant, free version. Successful Universal Installer USB installations of Ubuntu were performed on small 1.1GB FAT32 partions.  It may be possible to backup and restore persistent information between different versions of Ubuntu/Linux, for example between 14.04-1 and 14.04-2 and 32-bit/64-bit, but this nas not been researched or tested. If you have any questions about testing and validation procedures that were used, please ask.


 
