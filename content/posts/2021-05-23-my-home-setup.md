---
title: Tweaks on Home Computer (Mostly GNU/Linux)
author: SarCoptU
date: 2021-05-23T22:29:45+00:00

categories:
 - GNU/Linux
tags:
 - GNU/Linux
 - keyboard mapping
 - open source
 - system time
 - tplink-archer-t2u-nano
---
I have some peculiarities for my desktop setup that I use every time I reinstall a new OS. So instead of researching how to do the setup all over every time I reinstall an OS, dual boot, or distro hop, I will throw them all in a blog post and save some keystrokes and a bit of time. On my main computer, an old Dell Optiplex, with an NVIDIA GTX1650, I am dual-booting Kubuntu 20.04 and Windows 10.

### Caps Lock/Left Control Swap <figure class="wp-block-image size-large is-style-default">

<img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="361" src="/uploads/2021/05/mac-keyboard.jpg" alt="Apple's magic keyboard" class="wp-image-194" sizes="auto, (max-width: 750px) 100vw, 750px" /> <figcaption>The culprit</figcaption></figure> 

It all started with 20 quid spent on Amazon. I was hooked when I saw the design but did not want to spend a fortune on it, so I bought it used and with a Japanese layout. The layout on this keyboard and the playing around with Emacs gave me an idea to make life easier for my left pinky: swap Caps Lock key with Left Control. Here&#8217;s how to do it on Windows, GNU/Linux and macOS.

##### Windows 

There are a plethora of small apps to change the keyboard mapping. The one I use at the moment is KeyTweak. The program was created by a developer called Travis Krumsick and it works well for my purpose. 

<div class="wp-block-image is-style-default">
 <figure class="aligncenter size-large"><img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="499" src="/uploads/2021/05/keytweak.jpg" alt="KeyTweak app screen" class="wp-image-197" sizes="auto, (max-width: 750px) 100vw, 750px" /><figcaption>KeyTweak in action</figcaption></figure>
</div>

The use is straightforward: select a key and remap it. I could not find an official website to download, but there are two links below which I scanned with <a rel="noreferrer noopener" href="https://www.virustotal.com/gui/" target="_blank">virus total</a>. The scan was clean for 68 out of 69 antivirus engines. I suspect the file was found malicious on one engine because it changes the windows registry, which is needed to remap the keyboard. Windows Defender is happy with it, and so am I.

MajorGeeks: <a rel="noreferrer noopener" href="https://www.majorgeeks.com/files/details/keytweak.html" target="_blank">https://www.majorgeeks.com/files/details/keytweak.html</a>

Bleeping Computer: <a rel="noreferrer noopener" href="https://www.bleepingcomputer.com/download/keytweak/" target="_blank">https://www.bleepingcomputer.com/download/keytweak/</a>

##### GNU/Linux {.wp-block-heading}

On Linux, there are just a few letters to add to a file and the mod is ready. This is done in the terminal and the file to change is the aptly named &#8220;keyboard&#8221;.

`sudo nano /etc/default/keyboard`

Look for the with XKOPTIONS and change it to `XKOPTIONS="ctrl:swapcaps"`

then press Ctrl + x, confirm changes with y and reboot. 

If not happy with the result, change the row back to 

`XKOPTIONS=""`

##### MacOS {.wp-block-heading}

The good people at Apple provided a settings option for changing keys on the macOS, and you will find it in Settings > Keyboard > Modifier Keys&#8230; On macs, Command is the Ctrl key equivalent, so this is the one replaced.

<div class="wp-block-image is-style-default">
 <figure class="aligncenter size-large"><img data-recalc-dims="1" loading="lazy" decoding="async" width="400" height="250" src="/uploads/2021/05/keys.jpg" alt="Keyboard settings macOS" class="wp-image-199" sizes="auto, (max-width: 400px) 100vw, 400px" /><figcaption>Change from this</figcaption></figure>
</div>

<div class="wp-block-image is-style-default">
 <figure class="aligncenter size-large"><img data-recalc-dims="1" loading="lazy" decoding="async" width="400" height="249" src="/uploads/2021/05/keys2.jpg" alt="Keyboard settings macOS changed" class="wp-image-200" sizes="auto, (max-width: 400px) 100vw, 400px" /><figcaption>To this</figcaption></figure>
</div>

### GNU/Linux-Windows 10 Dual Boot Date/Time {.wp-block-heading}

When dual-booting Linux and Windows there always seem to be a disconnect between the system time on the two operating systems. This is because Windows and Linux interpret the hardware clock, the CMOS/BIOS clock on the computer motherboard, differently. Windows thinks of it as local time, while Linux assume it&#8217;s UTC and applies an offset to it. 

The easiest way to resolve the issue is to make Linux read the hardware time as local time.

`timedatectl set-local-rtc 1 --adjust-system-clock`

### Other Useful Mods {.wp-block-heading}

##### Wireless USB Adapter GNU/Linux Driver Install {.wp-block-heading}

This no longer applies to the desktop setup as I am using a cable, but the Github repository with the modified driver was invaluable for a long while while I was using a TP Link Archer T2U Nano wireless USB adapter. 

<div class="wp-block-image is-style-default">
 <figure class="aligncenter size-large"><img data-recalc-dims="1" loading="lazy" decoding="async" width="500" height="281" src="/uploads/2021/05/archer-nano.jpeg" alt="TP Link Archer T2U Nano wireless adapter" class="wp-image-204" sizes="auto, (max-width: 500px) 100vw, 500px" /><figcaption>TP Link Archer T2U Nano</figcaption></figure>
</div>

I have successfully tried this on Fedora 33 and Ubuntu 20.04. The fix requires a Github account and it is done in terminal.

Install git:

`sudo apt install git dkms`

Clone the repository. It will require your Github username and password or PAT (Personal Authentication Token).

`git clone https://github.com/aircrack-ng/rtl8812au.git`

Go to the downloaded repository folder:

`cd rtl8812au`

Install driver:

`sudo make dkms_install`

Now restart the PC and the network options in the system setting will show the available wireless networks.

##### Audio Issues on Bluetooth Devices {.wp-block-heading}

Not an issue at the moment as I don&#8217;t use bluetooth headphones, but this <a rel="noreferrer noopener" href="https://wiki.archlinux.org/title/PulseAudio/Troubleshooting#Glitches,_skips_or_crackling" target="_blank">Arch wiki</a> page saved me a lot of frustration.

##### NVIDIA Driver Install {.wp-block-heading}

Last but not least, the dreaded NVIDIA driver. I can&#8217;t get out of my head the image and message of Linus Torvalds to NVIDIA. Oh the memes&#8230;

On the Ubuntu distro I use at the moment, this is not a major issue, but just for documentation I will attach a tutorial on if-not-true-then-false website that I installed on my Fedora machine. The install is a bit convoluted and the people on this website do a fantastic job in explaining it.<figure class="wp-block-embed is-type-wp-embed is-provider-if-not-true-then-false wp-block-embed-if-not-true-then-false">

<div class="wp-block-embed__wrapper">
 <blockquote class="wp-embedded-content" data-secret="veehI6MfTZ">
 <a href="https://www.if-not-true-then-false.com/2015/fedora-nvidia-guide/">Fedora 34/33/32 NVIDIA Drivers Install Guide</a>
 </blockquote>
</div></figure> 

These are some of the tweaks on my home setup and here are some extra info links:

<ul class="wp-block-list">
 <li>
 <a rel="noreferrer noopener" href="https://scribles.net/mapping-caps-lock-key-to-control-key/" target="_blank">https://scribles.net/mapping-caps-lock-key-to-control-key/</a>
 </li>
 <li>
 <a rel="noreferrer noopener" href="https://opensource.com/article/18/11/how-swap-ctrl-and-caps-lock-your-keyboard" target="_blank">https://opensource.com/article/18/11/how-swap-ctrl-and-caps-lock-your-keyboard</a>
 </li>
 <li>
 <a rel="noreferrer noopener" href="https://www.nickang.com/2020-03-06-how-to-setup-access-to-github-from-terminal-with-2fa/" target="_blank">https://www.nickang.com/2020-03-06-how-to-setup-access-to-github-from-terminal-with-2fa/</a>
 </li>
 <li>
 <a rel="noreferrer noopener" href="https://askubuntu.com/questions/1149117/tp-link-ac600-archer-t2u-nano-driver-for-ubuntu-18-04" target="_blank">https://askubuntu.com/questions/1149117/tp-link-ac600-archer-t2u-nano-driver-for-ubuntu-18-04</a>
 </li>
 <li>
 <a rel="noreferrer noopener" href="https://www.howtogeek.com/323390/how-to-fix-windows-and-linux-showing-different-times-when-dual-booting/" target="_blank">https://www.howtogeek.com/323390/how-to-fix-windows-and-linux-showing-different-times-when-dual-booting/</a>
 </li>
 <li>
 <a rel="noreferrer noopener" href="https://itsfoss.com/wrong-time-dual-boot/" target="_blank">https://itsfoss.com/wrong-time-dual-boot/</a>
 </li>
 <li>
 <a rel="noreferrer noopener" href="https://askubuntu.com/questions/1149117/tp-link-ac600-archer-t2u-nano-driver-for-ubuntu-18-04" target="_blank">https://askubuntu.com/questions/1149117/tp-link-ac600-archer-t2u-nano-driver-for-ubuntu-18-04</a>
 </li>
 <li>
 <a rel="noreferrer noopener" href="https://wiki.archlinux.org/title/PulseAudio/Troubleshooting#Glitches,_skips_or_crackling" target="_blank">https://wiki.archlinux.org/title/PulseAudio/Troubleshooting#Glitches,_skips_or_crackling</a>
 </li>
 <li>
 <a rel="noreferrer noopener" href="https://www.if-not-true-then-false.com/2015/fedora-nvidia-guide/#nvidia-install" target="_blank">https://www.if-not-true-then-false.com/2015/fedora-nvidia-guide/#nvidia-install</a>
 </li>
 <li>
 Featured image made by <a href="https://www.flaticon.com/authors/kiranshastry" target="_blank" rel="noreferrer noopener">Kiranshastry</a> from <a href="https://www.flaticon.com/" target="_blank" rel="noreferrer noopener">www.flaticon.com</a>
 </li>
</ul>
