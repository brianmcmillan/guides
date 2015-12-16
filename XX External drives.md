###Connecting external drives
1. Log in with the local host name
`ssh pi@us1-n873h.local`
1. Identify the name of the disk (USB drive) and any partitions
`sudo fdisk -l`

```
...
Device         Boot  Start      End  Sectors  Size Id Type
/dev/mmcblk0p1        8192   122879   114688   56M  c W95 FAT32 (LBA)
/dev/mmcblk0p2      122880 15523839 15400960  7.4G 83 Linux

Disk /dev/sda: 59.2 GiB, 63518539776 bytes, 124059648 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: AEA9F32A-6E09-422F-8182-56B826FE5AC7

Device      Start       End   Sectors  Size Type
/dev/sda1      40    409639    409600  200M EFI System
/dev/sda2  411648 124057599 123645952   59G Microsoft basic data
```

1. Format the partition
`sudo mkfs.ext4 /dev/sda2 -L usb1`
1. Verify the partitions
`sudo blkid`

```
/dev/sda2: LABEL="usb1" UUID="f3d96949-ef26-4723-b35a-6580aa3a7016" TYPE="ext4" PARTLABEL="UNTITLED" PARTUUID="d3b7ff3f-6857-4312-ad0e-1f4922603cc2"
/dev/mmcblk0p1: SEC_TYPE="msdos" LABEL="boot" UUID="74BD-74CF" TYPE="vfat" PARTUUID="ba2edfb9-01"
/dev/mmcblk0p2: UUID="ec2aa3d2-eee7-454e-8260-d145df5ddcba" TYPE="ext4" PARTUUID="ba2edfb9-02"
/dev/sda1: LABEL="EFI" UUID="67E3-17ED" TYPE="vfat" PARTLABEL="EFI System Partition" PARTUUID="706c605b-91b0-4e84-affa-277db35daf4b"
/dev/mmcblk0: PTUUID="ba2edfb9" PTTYPE="dos"
```

1. Create a directory to mount the drive to  
`sudo mkdir /mnt/usb1`
1. Mount the drive  
`sudo mount -a /dev/sda2 /mnt/usb1`
1. Set the ownership of the directory  
`sudo chown -R pi:pi /mnt/usb1`
1. Set the permissions on the directory  
`sudo chmod -R 775 /mnt`
1. Verify the directory is writable  
`echo "some text" >> /mnt/usb1/test.txt && cat /mnt/usb1/test.txt`
1. Mount the USB drive at boot  
`sudo nano /etc/fstab`
1. Adding the following to the fstab file  
`/dev/sda2 /mnt ext4 defaults 0 0`
1. Save the file and exit  
1. Reboot  
`sudo reboot`
1. Log in with the local host name  
`ssh -p 49022 pi@us1-n873h.local`
1. Verify the USB drive is automatically mounted  
`echo "some text" >> /mnt/usb1/test.txt && cat /mnt/usb1/test.txt`
1. Delete the file  
`rm /mnt/usb1/test.txt`
