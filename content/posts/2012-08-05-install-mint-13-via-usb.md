---
title: Install MInt 13 via USB
author: Ao Zhang
type: post
date: 2012-08-05T07:42:00+00:00
url: /?p=64
blogger_blog:
  - qianxuweiren.blogspot.com
blogger_author:
  - Ao Zhang
blogger_permalink:
  - /2012/08/install-mint-13-via-usb.html
blogger_internal:
  - /feeds/5891851243558319332/posts/default/296896308637916998
restapi_import_id:
  - 5aa60451b191e
original_post_id:
  - 64
categories:
  - Scribble | 荒唐言

---
Yes, I knew that this topic has been mentioned in many places. And here is a good [example (installation along with Win7)][1] However, maybe it does not hurt to provide some tips. So here we go!

# Create a Live CD/USB 

Here is a [nice place to start][2]. I have tried all three ways, but it seemed only the first one work: ImageWriter crashed in Windows XP for no good reason (I &#8220;successfully&#8221; burned the USB, but it turned out that no booting medium was found when I booted through it). In Ubuntu 11, this software caused an error when it tried to unmount USB disk before finishing all the work (I am still unable to figure out why it needs to unmount it). Then I moved on to terminal. As for this method, here is a note you may want for finding USB disk under command line: In terminal, type in `sudo fdisk -l`. You may get similar results like this: 

<div style="clear:both;text-align:center;">
  <a href="http://wp.docker.localhost:8000/wp-content/uploads/2018/03/878ec-selection_001-1.png" style="margin-left:1em;margin-right:1em;"><img loading="lazy" decoding="async" border="0" height="341" src="http://wp.docker.localhost:8000/wp-content/uploads/2018/03/878ec-selection_001-1.png?w=300" width="400" /></a>
</div>

Here, I got  
`Disk /dev/sdb: 3918 MB, 3918495744 bytes`  
whose size is equivalent to my USB&#8217;s. And  
`Disk /dev/sdb1: 941 MB, 941621248 bytes`  
is a partition of my USB disk (since I have made it a Live CD/USB). So /dev/sdb should be used as the path, that is  
`sudo dd if=~/path_of_linux_mint_iso of=/dev/sdb oflag=direct  bs=1M`  
Notice that the device is refered to as /dev/sdx instead of /media/xxx. The first one is the device node assigned for your device, the other one is where the device is actually mounted (so that you can browse your files). Also note that your USB type could be FAT if you are using Windows. 

# Install Mint

This step is easy yet time-consuming. Insert your USB, reboot the computer and set USB to highest priority in BIOS boot option. If all work as expected, you should see a menu asking you to choose installing Mint (which is the first choice). Press , and you will enter a desktop, in which there is an icon saying &#8220;install Linux Mint&#8221;. Double click it, and you should be all set.

Two tips here: </p> 

  1. If a booting medium cannot be found when you boot via a USB, the reason could be that your USB lost connection to the computer. In that case, it helps just to reboot the computer and reinsert your USB. My Kingston USB worked well that way. 
  2. If the estimated language package downloading appears unreasonable (e.g.several hundreds/thousands minutes), you can skip it by clicking the bottom triangle and then click the &#8220;skip&#8221; button appeared. 

### _Keep patient, and you should be fine._

 [1]: http://www.howtogeek.com/howto/20079/install-linux-mint-on-your-windows-computer-or-netbook/
 [2]: http://community.linuxmint.com/tutorial/view/744