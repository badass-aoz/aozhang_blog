---
title: 'MInt 13: volume control doesn’t change volume'
author: Ao Zhang
type: post
date: 2012-08-09T08:34:00+00:00
url: /?p=63
blogger_blog:
  - qianxuweiren.blogspot.com
blogger_author:
  - Ao Zhang
blogger_permalink:
  - /2012/08/mint-13-volume-control-doesnt-change.html
blogger_internal:
  - /feeds/5891851243558319332/posts/default/810199334398079934
restapi_import_id:
  - 5aa60451b191e
original_post_id:
  - 63
categories:
  - Scribble | 荒唐言

---
This article involves choosing a non-default sound device.

#### Description:

<div>
  1.When Adobe flash player 11 played online music, it went too fast. As soon as a certain point was buffered, the player jumped to it; in other word, if at this time the player was playing at 1:20, and the media was buffered till 2:30, then the player skipped the music between 1:20 and 2:30, and jumped to the 2:30 point. (I hope I have made myself understood) This is similar for local music player Banshee, though it did not buffer the music.
</div>

<div>
</div>

<div>
  2. When a music or a video was played, pressing volume up/down button on the keyboard or directly using the volume control on desktop did not change the volume at all.&nbsp;
</div>

<div>
</div>

<div>
</div>

#### Solutions:&nbsp;

<div>
  I first suspected that the flash player was not working properly. However, while I was re-installing the plugin, I happened to download a music, and realize that it didn&#8217;t work properly in my disk as well. Then I opened the control center, and realized the cause related to the sound device. I had two listed sound device, and the default choice was the HDMI one, as shown below. I chose the built-in one, and it worked sometimes, but after reboot, the device in work became the HDMI again.
</div>

<div style="clear:both;text-align:center;">
  <a href="http://wp.docker.localhost:8000/wp-content/uploads/2018/03/4328e-selection_002-1.png" style="margin-left:1em;margin-right:1em;"><img loading="lazy" decoding="async" border="0" height="402" src="http://wp.docker.localhost:8000/wp-content/uploads/2018/03/4328e-selection_002-1.png?w=300" width="640" /></a>
</div>

<div>
</div>

<div>
  Then this <a href="http://forums.linuxmint.com/viewtopic.php?f=48&t=95337" target="_blank">thread</a> worked out.
</div>

<div>
  And here&#8217;s my output
</div>

<div>
  &nbsp;<code>$ sudo cat /proc/asound/modules&lt;code>&nbsp;</code></code>
</div>

``

<div>
  <code>[sudo] password for ao:&nbsp;</code>
</div>

``

<div>
  <code>&nbsp;0 snd_hda_intel</code>
</div>

``

<div>
  <code>&nbsp;1 snd_hda_intel</code>
</div>

<div>
</div>

<div>
</div>

Yes, there were actually no difference between these two; I believe if you get this output as well, you may refer to the index of item in your Sound Preferences in Control Center, (i.e. the first= index=0)

Follow the instructions in the link, and hopefully it will work fine.