# Beaglebone expand storage

Beagleboard:Expanding File System Partition On A microSD
This is a tutorial on how to expand the space used by the file system on an external micro SD card on your BeagleBone Black. 
This tutorial will work with any of the Linux operating systems mentioned on Ubuntu On BeagleBoard Black or Debian On BeagleBoard Black
that boot from a micro SD card. The images on those pages are set up for 1-4 GB micro SD cards. If you are using a larger micro SD card
you can re-partition it to have more space available for the file system. You can expand the file system's partition to any unused space
on your micro SD card. This tutorial uses fdisk and resize2fs so if the distribution you are using supports those you should be able to
follow along.


The automated way
On newer Debian system images coming from BeagleBoard.org / Robert Nelson, you can run the script 'sudo /opt/scripts/tools/grow_partition.sh' (Source at https://github.com/RobertCNelson/boot-scripts/blob/master/tools/grow_partition.sh) and then reboot to grow your partition. The below steps describe how to do so manually.

Expanding the File System Partition
These steps assume you are booting from a micro SD card with some sort of extra unused space. You will need to access the command line of whatever operating system you are using. The easiest way to do this is to connect to your BeagleBone Black with a display, mouse, and keyboard. You may also try connecting over serial (described here) or SSH although that may be different depending on your version of Linux.

Just to be completely clear, you are executing these steps from the terminal on your BeagleBone Black in the OS you are using.

Note: For the following command line instructions, do not type the '#', that is only to indicate that it is a command to enter.

From the command line of your Linux OS on your BeagleBone Black, become super user. The password will depend on your version of Linux (see the page that described how to install your version of Linux).

```# sudo -i```
You don't have to become super user. If you don't want to, just put 'sudo' at the beginning of all your commands.
Check available volumes on the BeagleBone Black

```# ls /dev/mmcblk*```
Depending on what version of Linux you are running it should look something like this:
```
brw-rw---- 1 root disk 179,  0 Oct  2 23:09 /dev/mmcblk0
brw-rw---- 1 root disk 179,  1 Oct  2 23:09 /dev/mmcblk0p1
brw-rw---- 1 root disk 179,  8 Oct  2 23:09 /dev/mmcblk1
brw-rw---- 1 root disk 179, 24 Oct  2 23:09 /dev/mmcblk1boot0
brw-rw---- 1 root disk 179, 32 Oct  2 23:09 /dev/mmcblk1boot1
brw-rw---- 1 root disk 179,  9 Oct  2 23:09 /dev/mmcblk1p1
```
This shows the μSD card (mmcblk0) and eMMC (mmcblk1), each having one partition. (You can ignore the special "boot0/1" 
partitions of the eMMC.) On very old systems there may be two partitions, in which case p2 should be used instead of p1 
in the instructions below.

Examine the partitioning on your external SD card:

```
# fdisk /dev/mmcblk0
```
>Then enter 'p' and you should be shown a print out like this

```
Command (m for help): p
Disk /dev/mmcblk0: 7.4 GiB, 7948206080 bytes, 15523840 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x18e8111b
```
```
Device         Boot Start     End Sectors  Size Id Type
/dev/mmcblk0p1       8192 7667712 7659521  3.7G 83 Linux
```
```
Command (m for help):
Note: Some values may be different. An important value to note is the start sector of the Linux partition, 8192 in this case.
Next, enter 'd' to delete the Linux partition. (If you have two partitions it will ask which partition to delete, which should be 2.)
```
Now it should be recreated: enter 'n' to create a new partition. For the first two questions (partition type and number) just press enter. For the start sector, be absolutely sure to use the same number it had originally. In our example, that would be 8192. For the last sector, you can use whatever you want in case you don't want to use your whole micro SD, but you can just hit enter to use the default (the max size possible).

This is what the interaction looks like in our example:
```
Command (m for help): d
Selected partition 1
Partition 1 has been deleted.
```
```
Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): 
```
```
Using default response p.
Partition number (1-4, default 1): 
First sector (2048-15523839, default 2048): 8192
Last sector, +sectors or +size{K,M,G,T,P} (8192-15523839, default 15523839): 
```
Created a new partition 1 of type 'Linux' and of size 7.4 GiB.
You can enter 'p' again to view the new partition table:
```
Command (m for help): p
Disk /dev/mmcblk0: 7.4 GiB, 7948206080 bytes, 15523840 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x18e8111b
```
>Device         Boot Start      End  Sectors  Size Id Type
/dev/mmcblk0p1       8192 15523839 15515648  7.4G 83 Linux
```
Command (m for help):
Note that the size of the partition has increased to that of the whole card, and the start sector is still the same as it was originally.
```
>If you are satisfied with your changes at this point you can enter 'w' to commit to your changes. (If you want to cancel without making any change to the disk, just press <CTRL>+C.)
```
Command (m for help): w
```

The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.
The warning above will appear if you're repartitioning the disk you've booted from. In that case, reboot your system:

```# reboot```
Lastly, after your BeagleBone Black reboots, you need to expand the file system. Run the following command (again as root):

```# resize2fs /dev/mmcblk0p1```
Once this command completes, you're done!

Simplified
The steps in the previous section can be simplified to these essential commands:

```
sudo su
fdisk /dev/mmcblk0
d
2
n
p
2
w
reboot
sudo su (after reboot is complete)
resize2fs /dev/mmcblk0p2
```
