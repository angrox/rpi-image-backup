# rpi-image-backup
Shell script to backup and restore a Raspberry Pi image from and to an sdcard

## Purpose
This script is used to create an as-small-as-possible image dump from an sdcard containing a raspberry pi operating system. It does not use dd to dump the whole sdcard, it clone partition by partition with partclone and save the partition layout and the MBR. 

## Requirements
The script runs on a linux distribution of your choice. You must have the following tools installed:

* sfdisk
* partclone
* dd
* blkid


## Usage: 

```
    Usage: rpi-image-backup [--backup | --restore] -d <device> -n <name> -b <basedir>
        One of the following commands are required:
      * -d|--device <device>   : Storage device to backup
      * -n|--name <name        : Name of the raspberry to use as backup dir
      * -b|--basedir <dir>     : Output directory. Structure <name>/<date> will
                                 be created automatically during backup.
                                 For restore options the basedir is the directory
                                 the backup files are present
      * --backup               : Perform a backup
      * --restore              : Perform a restore. Note: All data on the device will
                                 be overwritten, so be careful!
      Backup and Restore are mutually exclusive!
        And any combination of the following OPTIONS are optional:
        -h|--help            : Print this help message.
    * Required

    Examples:

    Backup a raspi image from /dev/sdi to /backup:
    # rpi-image-backup --backup -d /dev/sdi -n mypi -b /backup
    This creates the backup files in /backup/mypi/<yyyy>-<mm>-<dd>/

    Restore the backup on a sdcard (which could also be /dev/sdi)
    # rpi-image-backup --restore -d /dev/sdi -n mypi -b /backup/mypi/<yyyy>-<mm>-<dd>
```

### Backup
The backup process creates a directory structure based on the name of the raspberry pi and the current date in the format YYYY-MM-DD. The directory structure is created beneath the base directory: 

```
# rpi-image-backup --backup -d /dev/sdi -n mypi -o /backup
```
This creates a directory /backup/mypi/YYYY-MM-DD

At least 4 files will be created: 
* sfdisk.dump: contains the partition information
* mbr.dump: Contains the MBR (512 byte from the beginning of the sdcard)
* The vfat boot partition of the raspi
* The main root patition of the raspi

Depending on your used OS and/or if you used N00bs to install your Raspi the number of saved partitions may vary.

### Restore
Restoring wipes the destiniation device completly - so BE CAREFUL and check twice which device you will using to restore the backup.

```
# rpi-image-backup --restore -d /dev/sdi -n mypu -o /backup/mypi/YYYY-MM-DD
```

Restores the MBR, partition information and all partitions to the given device. 

REMEMBER: This will WIPE your destination device. There is NO fallback. You have been warned.



