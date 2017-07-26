---
layout: post
title:  "树莓派备份到大容量SD卡"
date:   2017-07-25 19:06:05
categories: 树莓派
excerpt: 树莓派备份到大容量SD卡
---

* content
{:toc}


## 目的

备份树莓派SD卡, 原卡为16G,新卡为32G

---

## 准备工作（PC操作）

> * PC为linux, 命令行运行:
>      备份 16G卡: dd if=/dev/sdc of=/pi.img bs=4M && sync
>
>      烧到新的32G卡上:  dd if=pi.img of=/dev/sdc
> * PC为windows:
>      可以用工具  Win32DiskImager   
>   
>
>  经过上面的步骤后，把新卡插入树莓派，发现可以用的空间只有16G

## 难点（树莓派上操作）

下面的例子是在国外一个网站上找的，

原地址[https://dracoy.com/2016/06/26/expanding-partition-on-sd-card-for-raspberry-pi-with-noobs-pre-installed/](https://dracoy.com/2016/06/26/expanding-partition-on-sd-card-for-raspberry-pi-with-noobs-pre-installed/)

不翻译了，直接上原文：

> I ran into an issue where I bought the RPI3 with a 16 gig SD card and needed to expand it to 128 gigs. With the NOOBS installation, I am not able to use sudo raspi-config to expand to the full size of the SD card. I couldn’t find good information on the internet so here is a guide on how to expand the partition so I can do it again in the future. The below steps allow the user to resize the partitions on NOOBS without having to take out the SD Card or shutting down the operations of the RPI.
> 
> * Background information on the NOOBS partitions – https://github.com/raspberrypi/noobs/wiki/NOOBS-partitioning-explained
> 
> * Logic behind moving partitions – https://www.raspberrypi.org/forums/viewtopic.php?t=111325&p=763825 [by beta-tester » Sun May 24, 2015 7:05 am]
>
> * Resize partitions on RPI instructions – http://elinux.org/RPi_Resize_Flash_Partitions [Manually resizing the SD card on Raspberry Pi]
>
> * Resize partitions when there are both primary and logical partitions: http://www.runeaudio.com/forum/how-to-expand-sd-card-filesystem-t1231.html
>
> * Additional resource: https://raspberry-hosting.com/en/faq/how-expand-arch-linux-root-partition
>
> * Fixing value out of range error when the original sectors were not setup correctly: http://www.claudiokuenzler.com/blog/621/force-new-partition-start-at-certain-sector-number-parted#.V29JxLgrL1t

## The above information was used to create the below guide. Highlighted in red text are the commands I used. Same commands in the command line description.

>
> * 1 Backup the original SD card – https://www.raspberrypi.org/forums/viewtopic.php?p=239331
> * 2 Write the backup to the new larger SD Card
> * 3 Log into putty.
> * 4 Enter “sudo fdisk /dev/mmcblk0” to enter into partition management.
> * 5 Enter “p” to see partitions. 
>   ![pic](/css/pics/p2017-0726-01.png)
>    *  5.1 From my image you can see that I have 2 primary partitions “1” and “2”.
>    *  5.2 Logical partitions “5”,”6″, and “7” are embedded within  primary partition “2”.
> * 6 Some relevant data to consider…
>    * 6.1 For historical reasons (only 4 ‘slots’ in the partition table), hard-drives and SD cards can only have a maximum of 4 primary partitions. To work around that limitation, one of those primary partitions can optionally be an extended partition. An extended partition can contain an unlimited number of logical partitions inside it. Under Linux, the primary partitions are always numbered 1-4 (i.e. seen as /dev/mmcblk0p1 – /dev/mmcblk0p4 on the Pi), and any logical partitions are always numbered 5 and above (i.e. seen as /dev/mmcblk0p5 and above on the Pi).
> * 7  Not sure why, but do I not have a primary partition “3” which comes standard in RPI NOOBS installation according to #1 background information above.
> * 8 In my case my main root partition is partition “7”. Notice how partition “2” is the summation of “5”, “6”, and “7”. Also notice how partition “7” is the largest.
> * 9 Logic wise, I want to expand partition 2 (primary) and within this partition, I want to subsequently expand partition 7 (logical).
> * 10 First, delete “7”, “6”, “5”, and “2”:
>    * 10.1 Type “d”, hit enter, then work your way backwards, by deleting “7” first then “6”, then “5”, and finally “2”.
>     ![pic](/css/pics/p2017-0726-02.png)
> * 11 Create a new extended partition 2 to hold the logical partitions.
>    * 11.1 Type “n”, hit enter, type “e”, and hit enter again.
>    * 11.2 Type the first sector that matches the original print out in my first picture (but matching your own print out not mine).
>    * 11.3 Hit enter again as the last sector will default as the last sector on your SD card. This expands your extended sector to the full size of the SD card.
> * 12 Hit “p” again to see what is left. You should now have two primary sectors.
> * 13 To resize partitions, every re-added partition must equal the same start sector number as the original print out. This is important.
> * 14 When I tried to add partition “5” it would not let me force the start sector to one of my original numbers.
>    * 14.1 See resource #5 for illustration.
>    * 14.2 I could not add the nested logical partitions because of a Value out of range error. Researched showed the following: “The partition is not aligned and fdisk decided the new partition’s start sector must begin with a proper alignment.”
>    ![pic](/css/pics/p2017-0726-03.png)
> * 15 At this point I hit “w” and enter to write my current changes.
> * 16 I added partition “5”, “6”, and “7” using parted instead as I can dictate what my starting sector would be for my logical partitions.
>    * 16.1 Enter “sudo parted”
>    * 16.2 Change units to sector by typing “unit s”
>    * 16.3 Type “print” to see the partition layout.
>    * 16.4 Finally start adding “5”, “6”, and “7” sequentlly by using the line “mkpart logical START END” and replacing start/end with the start and end of your sector numbers that match the original layout.
>       *Since I wanted to expand 7, I used my final command as “mkpart logical START -1s” – this forced the END sector to to be the last usable sector on the disk.
>    * 16.5 Hit quit to exit parted.
> * 17 
>    ![pic](/css/pics/p2017-0726-04.png) 
> * 18 Now back to  fdisk.
>    * 18.1 Enter “sudo fdisk /dev/mmcblk0” to enter into partition management.
>    * 18.2 Enter “p” to see partitions.
>    * 18.3 One issue is that the ID and TYPE for partition “6” did not match the original.
>       * 18.3.1 Enter “t” and then “6” and then “c” to change the type to ID “c”.
>       * 18.3.2 Enter “p” again. 
>       * 18.3.3 The new table should match the old table except for the ending sectors in partition “2” primary and partition “7” logical. All else should remain the same. This proves that the resizing of my partition to use the whole SD card worked!
> * 19 Now we have to reboot to force the changes. To reboot enter: “sudo reboot”
> * 20 Finally, we type “sudo resize2fs /dev/mmcblk0p7” to resize the partition to the end of the SD card. 
> * 21
>  ![pic](/css/pics/p2017-0726-05.png)
> * 22 Type df -h to validate the /dev/root partition updated to the appropriate size.

