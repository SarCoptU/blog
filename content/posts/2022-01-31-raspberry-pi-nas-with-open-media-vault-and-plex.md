---
title: Raspberry Pi NAS with Open Media Vault and Plex
cover:
 image: "/uploads/2022/01/ingredients.jpg"
 alt: ""
 relative: false
 hidden: false
 hiddenInList: false
 hiddenInSingle: false
author: SarCoptU
date: 2022-01-31T21:27:24+00:00

categories:
 - GNU/Linux
tags:
 - NAS
 - openmediavault
 - plex
 - raspberrypi
---
Ingredients:

<ul class="wp-block-list">
 <li>
 Raspberry pi 4 &#8211; I used one with 8Gb of RAM with whatever case is available
 </li>
 <li>
 1 or 2 external hard disk drives (HDDs) &#8211; if 2 are used, a powered USB hub is required, as the pi does not provide enough power for more than 1 HDD
 </li>
 <li>
 of note also, no RAID configurations are possible with external, USB connected HDDs
 </li>
 <li>
 USB microSD card adaptor for installing Raspberry Pi OS
 </li>
 <li>
 raspberry pi power plug
 </li>
 <li>
 cables: USB for HDDs, internet cable with RJ45 plugs
 </li>
</ul>

<div class="wp-block-image">
 <figure class="aligncenter size-full"><img data-recalc-dims="1" loading="lazy" decoding="async" width="640" height="480" src="/uploads/2022/01/ingredients.jpg" alt="hardware needed for the setup" class="wp-image-320" sizes="auto, (max-width: 640px) 100vw, 640px" /><figcaption>Ingredients</figcaption></figure>
</div>

## Step 1: Install Raspberry Pi OS {#step-1-install-raspberry-pi-os.wp-block-heading}

Download the Raspberry Pi imager from <https://www.raspberrypi.com/software/> and select the image to be installed on the Pi.

For the 8Gb pi I have, I have used the 64 bit Raspberry Pi OS lite from <a rel="noreferrer noopener" href="https://downloads.raspberrypi.org/raspios_lite_arm64/images/raspios_lite_arm64-2021-11-08/" target="_blank">https://downloads.</a>[raspberrypi][1]<a rel="noreferrer noopener" href="https://downloads.raspberrypi.org/raspios_lite_arm64/images/raspios_lite_arm64-2021-11-08/" target="_blank">.org/raspios_lite_arm64/images/raspios_lite_arm64-2021-11-08/</a>

<div class="wp-block-image">
 <figure class="aligncenter size-full is-resized"><img data-recalc-dims="1" loading="lazy" decoding="async" src="/uploads/2022/01/imager.png" alt="main menu for the raspberry pi imager" class="wp-image-322" width="640" height="480" /><figcaption>Raspberry Pi imager</figcaption></figure>
</div>

A very cool hack from [NetworkChuck][2] is before writing the changes, hit Ctrl + Shift + x to go into a new menu with some extra options including activating secure shell (SSH) access to the Pi from a different computer. 

Once done, just insert the card into the Pi and use an ssh connection from a different computer to login into the Pi: username &#8220;pi&#8221;, password &#8220;raspberry&#8221;.

Then a quick update of the system with:

<pre class="wp-block-preformatted">sudo apt update</pre>

<pre class="wp-block-preformatted">sudo apt upgrade</pre>

## Step 2 Install Open Media Vault {#step-2-install-open-media-vault.wp-block-heading}

<div class="wp-block-image">
 <figure class="aligncenter size-large is-resized"><img data-recalc-dims="1" decoding="async" src="/uploads/2022/01/omv6_dashboard.png" alt="open media vault dashboard " class="wp-image-324" sizes="(max-width: 750px) 100vw, 750px" /><figcaption>Open Media Vault dashboard &#8211; credits https://www.openmediavault.org</figcaption></figure>
</div>

The quickest way to install Open Media Vault is to run the script from the developers GitHub page <https://github.com/OpenMediaVault-Plugin-Developers/installScript>.

<pre class="wp-block-preformatted">sudo wget -O - https://github.com/OpenMediaVault-Plugin-Developers/installScript/raw/master/install | sudo bash</pre>

Once installed, open a web browser page and go the IP address of the Pi (router setting might be helpful to find it). 

<div class="wp-block-image">
 <figure class="aligncenter size-large is-resized"><img data-recalc-dims="1" decoding="async" src="/uploads/2022/01/omv6_login.png" alt="open media vault web interface" class="wp-image-325" sizes="(max-width: 750px) 100vw, 750px" /><figcaption>Open Media Vault web interface &#8211; credits https://www.openmediavault.org</figcaption></figure>
</div>

Default username is &#8220;admin&#8221; and the default password is &#8220;openmediavault&#8221;.

The interface is quite straightforward to use. Some initial tweaks might be in order like changing the default password, mounting the disks in the Storage option, creating a file system and enabling SMB and/or NFS.

<div class="wp-block-image">
 <figure class="aligncenter size-full"><img data-recalc-dims="1" loading="lazy" decoding="async" width="286" height="419" src="/uploads/2022/01/omv.png" alt="open media vault main options" class="wp-image-326" sizes="auto, (max-width: 286px) 100vw, 286px" /><figcaption>Main options</figcaption></figure>
</div>

Some user credentials setup will be useful for accessing the drives.

If there are existing folders on the HDD, just create a share with the same name as the existing folder.

## Step 3 Install plex on the NAS {#step-3-install-plex-on-the-nas.wp-block-heading}<figure class="wp-block-image size-full">

<img data-recalc-dims="1" loading="lazy" decoding="async" width="640" height="208" src="/uploads/2022/01/plex.png" alt="plex company logo" class="wp-image-327" sizes="auto, (max-width: 640px) 100vw, 640px" /> <figcaption>Plex logo &#8211; credits Wikipedia, Plex Inc. &#8211; Plex Media Server</figcaption></figure> 

Install the GNU/Linux transport software that allows the use of repositories accessed via the HTTP Secure protocol (HTTPS), also referred to as HTTP over TLS.

<pre class="wp-block-preformatted">sudo apt install apt-transport-https</pre>

Add and check plex repositories:

<pre class="wp-block-preformatted">curl https://downloads.plex.tv/plex-keys/PlexSign.key | sudo apt-key add -
</pre>

<pre class="wp-block-preformatted">echo deb https://downloads.plex.tv/repo/deb public main | sudo tee /etc/apt/sources.list.d/plexmediaserver.list</pre>

Update repositories and install the plex media server:

<pre class="wp-block-preformatted">sudo apt update</pre>

<pre class="wp-block-preformatted">sudo apt install plexmediaserver</pre>

To access the web interface for plex, open a web browser and input the Pi address with the default port for the plex server. For example:

<pre class="wp-block-preformatted">192.168.1.21:32400/web</pre>

Sign up or sign in to plex and add the raspberry pi NAS to the sources. 

<div class="wp-block-image">
 <figure class="aligncenter size-full is-resized"><img data-recalc-dims="1" decoding="async" src="/uploads/2022/01/plex2.jpeg" alt="plex interface" class="wp-image-330" sizes="(max-width: 474px) 100vw, 474px" /><figcaption>Plex interface</figcaption></figure>
</div>

Raspberry Pi is a great little computer that is more than capable of providing entertainment for home and backing up those beautiful memories on a very functional NAS.



<p id="references">
 References:
</p>

<ul class="wp-block-list">
 <li>
 Download Raspberry imager <a href="https://www.raspberrypi.com/software/">https://www.raspberrypi.com/software/</a>
 </li>
</ul>

<ul class="wp-block-list">
 <li>
 Raspberry OS 64 bit <a href="https://downloads.raspberrypi.org/raspios_lite_arm64/images/raspios_lite_arm64-2021-11-08/">https://downloads.raspberrypi.org/raspios_lite_arm64/images/raspios_lite_arm64-2021-11-08/</a>
 </li>
</ul>

<ul class="wp-block-list">
 <li>
 Install script <a href="https://github.com/OpenMediaVault-Plugin-Developers/installScript">https://github.com/OpenMediaVault-Plugin-Developers/installScript</a> on github
 </li>
</ul>

<ul class="wp-block-list">
 <li>
 Running a 64 bit OS <a rel="noreferrer noopener" href="https://pimylifeup.com/raspberry-pi-64-bit/" target="_blank">https://pimylifeup.com/raspberry-pi-64-bit/</a>
 </li>
</ul>

<ul class="wp-block-list">
 <li>
 NetworkChuck <a rel="noreferrer noopener" href="https://www.youtube.com/watch" target="_blank">https://www.youtube.com/watch
 </li>
</ul>

<ul class="wp-block-list">
 <li>
 GaryExplains <a rel="noreferrer noopener" href="https://www.youtube.com/watch" target="_blank">https://www.youtube.com/watch
 </li>
</ul>

<ul class="wp-block-list">
 <li>
 ExplainingComputers <a rel="noreferrer noopener" href="https://www.youtube.com/watch" target="_blank">https://www.youtube.com/watch
 </li>
</ul>

 [1]: https://downloads.raspberrypi.org/raspios_lite_arm64/images/raspios_lite_arm64-2021-11-08/
 [2]: https://www.youtube.com/channel/UC9x0AN7BWHpCDHSm9NiJFJQ
