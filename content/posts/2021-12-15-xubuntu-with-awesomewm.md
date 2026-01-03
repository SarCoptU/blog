---
title: Xubuntu with AwesomeWM
cover:
 image: "/uploads/2021/12/xfce.png"
 alt: ""
 relative: false
 hidden: false
 hiddenInList: false
 hiddenInSingle: false
author: SarCoptU
date: 2021-12-15T15:20:05+00:00

categories:
 - GNU/Linux
tags:
 - awesomewm
 - GNU/Linux
 - linux
 - window-manager
 - xubuntu
---
After some looking about and being sick of running Windows 10 just for doing some Scratch-like programming for uni, I have decided to update my system to full a Linux pc with, if needed Win10 and Kali VMs for all my HackTheBox and TryHackMe needs. 

Because I still don&#8217;t have proper internet and using phone internet sharing, installing arch without some cable internet is a bit of a stretch.

After seeing some Debian-Titus work in progress during <a href="https://www.youtube.com/c/ChrisTitusTech" data-type="URL" data-id="https://www.youtube.com/c/ChrisTitusTech">ChrisTitusTech</a> stream, I definitely want to give AwesomeWM a try. What&#8217;s not to like about a small, quick and efficient window manager.

So here goes nothing:

The initial installation of Xubuntu was very straightforward.

After installing vim and swapping the ctrl/caps:

<pre class="wp-block-preformatted">sudo apt install vim</pre>

<pre class="wp-block-preformatted"><code>sudo vim /etc/default/keyboard</code></pre>

Look for the with XKOPTIONS and change it to `XKOPTIONS="ctrl:swapcaps"`

## Awesome {.wp-block-heading}

Install AwesomeWM &#8211; <a href="https://awesomewm.org/" target="_blank" rel="noreferrer noopener">https://awesomewm.org/</a>

<pre class="wp-block-preformatted">sudo apt install awesome</pre>

Afterwards, log out and choose awesome in the desktop manager options.

The desktop transforms from this:<figure class="wp-block-image size-large">

<img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="422" src="/uploads/2021/12/xfce.png" alt="xubuntu desktop" class="wp-image-294" sizes="auto, (max-width: 750px) 100vw, 750px" /> </figure> 

To this:<figure class="wp-block-image size-large">

<img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="422" src="/uploads/2021/12/AwesomeWM.png" alt="awesome window manager desktop" class="wp-image-295" sizes="auto, (max-width: 750px) 100vw, 750px" /> </figure> 

The configuration file for AwesomeWM is in a file written in the Lua programming language. For advanced tinkering, proficiency in Lua is advised, but for small changes, just opening the file in the text editor of choice will make the window manager more personalised. 

When lost, a good thing to remember is the shortcut for keyboard shortcuts: super (win) + s.

I found the MAKC&#8217;s video on AwesomeWM configuration to be quite good as a starting point for the initial tinkering. 

Start by copying the config file to your home dir/awesome and then edit it with the text editor of choice:

<pre class="wp-block-preformatted">cd .config</pre>

<pre class="wp-block-preformatted">mkdir awesome</pre>

<pre class="wp-block-preformatted">cp /etc/xdg/awesome/rc.lua ~/.config/awesome/</pre>

<pre class="wp-block-preformatted">vim ~/.config/awesome/</pre>

Add gaps at the end of the file. 

NB: the &#8220;&#8211;&#8221; is for comments so that the file makes sense on when reading later.

<pre class="wp-block-preformatted">-- Gaps</pre>

<pre class="wp-block-preformatted">beautiful.useless_gap = 5 </pre>

Add Compton to make the terminal windows transparent

<pre class="wp-block-preformatted">sudo apt install compton</pre>

Autostart applications

<pre class="wp-block-preformatted">-- Autostart</pre>

<pre class="wp-block-preformatted">awful.spawn.with.shell("name of app to autostart")</pre>

in out case

<pre class="wp-block-preformatted">awful.spawn.with_shell("compton")</pre>

Changing the layout to file only &#8211; comment the other layout type in the rc.lua file: search &#8220;layout&#8221; with / in vim and comment every &#8220;awful.layout&#8230;&#8221; except tile or whatever layout you prefer.<figure class="wp-block-image size-full">

<img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="335" src="/uploads/2021/12/tile.png" alt="layout options commented out" class="wp-image-302" sizes="auto, (max-width: 750px) 100vw, 750px" /> </figure> 

Now the upper left corner should look something like this without any possibility to click to change the tile (too many confusing options):

<div class="wp-block-image">
 <figure class="aligncenter size-full"><img data-recalc-dims="1" loading="lazy" decoding="async" width="209" height="30" src="/uploads/2021/12/tile-1.png" alt="" class="wp-image-303" /></figure>
</div>

AwesomeWM is a great window manager with a lot of potential for extensibility and efficiency. Configuring it requires a bit of patience, but once in the flow, it becomes quite second nature. 

## Other apps {.wp-block-heading}

Install **QEMU/KVM** &#8211; shoutout to ChrisTitusTech:

Check compatibility:

<pre class="wp-block-preformatted">grep -o 'vmx\|svm' /proc/cpuinfo</pre>

<pre class="wp-block-preformatted">sudo apt install qemu-kvm libvirt-clients libvirt-daemon-system bridge-utils libguestfs-tools virt-manager</pre>

Add user to KVM groups

<pre class="wp-block-preformatted">sudo adduser $USER libvirt </pre>

<pre class="wp-block-preformatted">sudo adduser $USER libvirt-qemu</pre>

Install Nvidia drivers, restart and check the driver in use:

<pre class="wp-block-preformatted">sudo ubuntu-drivers devices</pre>

<pre class="wp-block-preformatted">sudo ubuntu-drivers autoinstall</pre>

<pre class="wp-block-preformatted">sudo shutdown -r now</pre>

<pre class="wp-block-preformatted">nvidia-smi</pre>

Download and install **Windows** in a VM. 

<a rel="noreferrer noopener" href="https://www.microsoft.com/en-gb/software-download/windows10ISO" target="_blank">https://www.microsoft.com/en-gb/software-download/windows10ISO</a>

<div class="wp-block-image">
 <figure class="aligncenter size-full"><img data-recalc-dims="1" loading="lazy" decoding="async" width="476" height="88" src="/uploads/2021/12/Windows_10_Logo.jpg" alt="windows 10 logo" class="wp-image-310" sizes="auto, (max-width: 476px) 100vw, 476px" /></figure>
</div>

Download and install KaliLinux in a VM:

<a rel="noreferrer noopener" href="https://www.kali.org/get-kali/" target="_blank">https://www.kali.org/get-kali/</a>

<div class="wp-block-image">
 <figure class="aligncenter size-full"><img data-recalc-dims="1" loading="lazy" decoding="async" width="512" height="288" src="/uploads/2021/12/kali.png" alt="Kali linux image" class="wp-image-309" sizes="auto, (max-width: 512px) 100vw, 512px" /></figure>
</div>

The system still needs a lot of configuration, but it is a good start for a small, new awesome system.

References:

ChrisTitusTech Youtube channel: <a rel="noreferrer noopener" href="https://www.youtube.com/c/ChrisTitusTech" target="_blank">https://www.youtube.com/c/ChrisTitusTech</a>

MACK awesome configuration video: <a rel="noreferrer noopener" href="https://www.youtube.com/watch" target="_blank">https://www.youtube.com/watch

ChrisTitusTech&#8217;s KVM install: <a href="https://www.youtube.com/watch" data-type="URL" data-id="https://www.youtube.com/watch">https://www.youtube.com/watch

Linoxide install Nvidia: <a rel="noreferrer noopener" href="https://linoxide.com/how-to-install-nvidia-driver-on-ubuntu/" target="_blank">https://linoxide.com/how-to-install-nvidia-driver-on-ubuntu/</a>

Windows 10 image: <a href="https://commons.wikimedia.org/wiki/File:Windows_10_Logo.svg#/media/File:Windows_10_Logo.svg" target="_blank" rel="noreferrer noopener">https://commons.wikimedia.org/wiki/File:Windows_10_Logo.svg#/media/File:Windows_10_Logo.svg</a>

Kali image: <a href="https://commons.wikimedia.org/w/index.php" target="_blank" rel="noreferrer noopener">https://commons.wikimedia.org/w/index.php
