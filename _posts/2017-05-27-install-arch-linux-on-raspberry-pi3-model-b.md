---
layout: post
cover: 'assets/images/cover9.jpg'
title: Installing Arch Linux on Raspberry Pi 3 Model B
date:   2017-05-27 00:00:00
tags: DevOps
subclass: 'post tag-fiction'
categories: 'casper'
navigation: True
logo: 'assets/images/home.png'
---

Today I installed [Arch Linux](https://www.archlinux.org/) on my [Raspberry Pi](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/). The [Arch Linux guide](https://archlinuxarm.org/platforms/armv8/broadcom/raspberry-pi-3) is sufficiant and I followed it mostly overcoming few small hiccups which I plan on documenting here. 

The general procedure is to take up a Micro SD card and write the preferred OS in it. Put the card in your pi, apply power, connect ethernet and you are good to go! 

#####Step 1
The first step is to partition the SD card. We will create two partions in our SD card. After inserting the SD card in your computer find out its name in your computer. If your card is named as /dev/sd**X** you can easily follow the arch guide. In my Ubuntu 14.04 system (yeah I know thats old!) it was identified as /dev/mmcblk0p1. This was the minor hiccup I referred above. What it means is that the mmc sub-system of my computer has registered the device in its well known format `mmcblkXpY`. 

- Each mmc device registered withing the Linux kernel receives it own mmc device number **X**.
- Each partition on a particular device receives it own number **Y**.More about this [here](https://stackoverflow.com/a/21752229/5285588)

So just to make things clear when I do ``df`` I have

		Filesystem     1K-blocks      Used Available Use% Mounted on
		/dev/mmcblk0p1  15541408         8  15541400   1% /media/arka/5E95-21CD

And on `lsblk` I get 

		NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
		mmcblk0     179:0    0  14.9G  0 disk 
		└─mmcblk0p1 179:1    0  14.9G  0 part /media/arka/5E95-21CD

First umount the device 

		umount /dev/mmcblk0p1

Now we have to create partition in this SD card. Start fdisk to partion:

		fdisk /dev/mmcblk0

Delete old partions and create new one at fdisk prompt:

- Type **o**. This will clear out any partitions on the drive.
- Type **p** to list partitions. There should be no partitions left.
- Type **n**, then **p** for primary, **1** for the first partition on the drive, press *ENTER* to accept the default first sector, then type **+100M** for the last sector.
- Type **t**, then **c** to set the first partition to type W95 FAT32 (LBA).
- Type **n**, then **p** for primary, **2** for the second partition on the drive, and then press *ENTER* twice to accept the default first and last sector.
- Write the partition table and exit by typing **w**.

After this if you do `lsblk` you should have something like:

		NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
		mmcblk0     179:0    0  14.9G  0 disk 
		├─mmcblk0p1 179:1    0   100M  0 part 
		└─mmcblk0p2 179:2    0  14.8G  0 part

#####Step 2
Now we have create and mount FAT filesystem in partion 1:

		mkfs.vfat /dev/mmcblk0p1
		mkdir boot
		mount /dev/mmcblk0p1 boot

#####Step 3
Similarly create and mount ext4filesystem in partition 2:

		mkfs.ext4 /dev/mmcblk0p2
		mkdir root
		mount /dev/mmcblk0p2 root

#####Step 4
Download and extract the root filesystem (as root, not via sudo):

		wget http://os.archlinuxarm.org/os/ArchLinuxARM-rpi-2-latest.tar.gz
		bsdtar -xpf ArchLinuxARM-rpi-2-latest.tar.gz -C root
		sync

#####Step 5
Move boot files to the first partition:

		mv root/boot/* boot

#####Step 6
Unmount the two partitions:

		umount boot root

#####Step 7
Insert SD card in Raspberry Pi 3 , connect ethernet and apply power. We can connect to a monitor via HDMI cable and add a USB mouse and keyboard to get started. Better still use SSH to have remote access. Find out the IP of your Pi (via nmap, etc.) and then :

		ssh alarm@192.168.0.101
		alarm@192.168.0.101's password: 
		Welcome to Arch Linux ARM

     	Website: http://archlinuxarm.org
       	    Forum: http://archlinuxarm.org/forum
                  IRC: #archlinux-arm on irc.Freenode.net
		Last login: Sat May 27 13:37:59 2017 from 192.168.0.106
		[alarm@alarmpi ~]$ pwd
		/home/alarm
		[alarm@alarmpi ~]$ 

- Login as the default user *alarm* with the password *alarm*.
- The default root password is *root*.

*****
The procedure was simple, credits goes mostly to the well written arch guide. After meddling with the Pi soon you will find sudo is not installed! Hopefully I will get to document that in the future!
