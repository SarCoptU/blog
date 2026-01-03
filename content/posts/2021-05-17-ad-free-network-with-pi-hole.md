---
title: Ad-Free Network with Pi-hole
cover:
 image: "/uploads/2021/05/tools1.jpg"
 alt: ""
 relative: false
 hidden: false
 hiddenInList: false
 hiddenInSingle: false
author: SarCoptU
date: 2021-05-16T23:21:19+00:00

categories:
 - dev
 - GNU/Linux
tags:
 - home network
 - IT tutorial
 - pi-hole
 - rapsberry pi
---
This is a tutorial for installing Pi-hole on your home network. As the devs on the <a rel="noreferrer noopener" href="https://pi-hole.net/" target="_blank">pi-hole.net</a> put it, it is a &#8220;network-wide ad blocking&#8221; solution. The app acts as a DNS (domain name system) server. More info on this <a rel="noreferrer noopener" href="https://en.wikipedia.org/wiki/Domain_Name_System" target="_blank">wiki</a>. It basically sends ad queries that you generate when you visit a website to nowhere (hence the cosmic flavor of the app name). 

### Ingredients {.wp-block-heading}

A few elements are needed for the setup. In this tutorial, I will use a pi zero computer, but the setup can be done on a slew of devices and there is also a docker install.

What I used (from left to right):<figure class="wp-block-image size-full is-style-default">

<img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="617" src="/uploads/2021/05/tools1.jpg" alt="Devices used for Pi-hole setup" class="wp-image-132" sizes="auto, (max-width: 750px) 100vw, 750px" /> <figcaption>Pi-hole ingredients</figcaption></figure> 

<ul class="wp-block-list">
 <li>
 micro USB to USB stick adaptor
 </li>
 <li>
 mini HDMI to HDMI adaptor &#8211; not used, as I performed a headless installation
 </li>
 <li>
 Micro USB B to USB A adaptor
 </li>
 <li>
 USB 2.0 and Ethernet hub &#8211; used for the Ethernet port as I installed it wired
 </li>
 <li>
 Official Raspberry Pi Zero Case with 16 GB micro SD card on top
 </li>
 <li>
 Raspberry Pi Zero WH
 </li>
 <li>
 USB 2.0 to micro USB from an old Chromecast for power
 </li>
</ul>

I spent about 60 quid on PiHut for the <a rel="noreferrer noopener" href="https://thepihut.com/products/raspberry-pi-zero-wh-starter-kit" target="_blank">Pi Zero Started kit</a> and the <a rel="noreferrer noopener" href="https://thepihut.com/products/usb-2-0-and-ethernet-hub-3-usb-ports-and-1-ethernet" target="_blank">USB to Ethernet hub</a>, the rest of the stuff I had around the house.

### Pi OS installation {.wp-block-heading}

For our use, a very small OS for the pi should be more than enough. It is advised to use the pi device for the pi-hole installation exclusively.

The first order of business is to download and install the raspberry pi imager from <a rel="noreferrer noopener" href="https://www.raspberrypi.org/software/operating-systems/" target="_blank">raspberrypi.org</a>.<figure class="wp-block-image size-large is-style-default">

<img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="494" src="/uploads/2021/05/raspberry-pi-os.png" alt="Raspberry Pi imager download" class="wp-image-136" sizes="auto, (max-width: 750px) 100vw, 750px" /> <figcaption>Imager download</figcaption></figure> 

The app will prepare and install the OS on your SD card. Attach the SD with the adaptor to your main computer and let the imager do its magic. Once you choose your SD card and OS, it will download and install everything.<figure class="wp-block-image size-large is-style-default">

<img data-recalc-dims="1" loading="lazy" decoding="async" width="682" height="453" src="/uploads/2021/05/raspberry-pi-os4.png" alt="Raspberry Pi imager window" class="wp-image-137" sizes="auto, (max-width: 682px) 100vw, 682px" /> <figcaption>RPi imager</figcaption></figure> <figure class="wp-block-image size-large is-resized is-style-default"><img data-recalc-dims="1" loading="lazy" decoding="async" src="/uploads/2021/05/raspberry-pi-os5.png" alt="Raspberry Pi imager OS options" class="wp-image-138" width="681" height="451" sizes="auto, (max-width: 681px) 100vw, 681px" /><figcaption>I used is Raspberry Pi OS Lite.</figcaption></figure> <figure class="wp-block-image size-large is-resized is-style-default"><img data-recalc-dims="1" loading="lazy" decoding="async" src="/uploads/2021/05/raspberry-pi-os6.png" alt="Raspberry pi imager SD card format notice" class="wp-image-139" width="686" height="452" sizes="auto, (max-width: 686px) 100vw, 686px" /><figcaption>SD card format notice</figcaption></figure> <figure class="wp-block-image size-large is-style-default"><img data-recalc-dims="1" loading="lazy" decoding="async" width="682" height="453" src="/uploads/2021/05/raspberry-pi-os8.png" alt="Raspberry pi imager sucessful write operation" class="wp-image-140" sizes="auto, (max-width: 682px) 100vw, 682px" /><figcaption>Success</figcaption></figure> 

To connect to the pi later, create an ssh.txt file on the boot partition of the SD card.<figure class="wp-block-image size-large is-style-default">

<img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="432" src="/uploads/2021/05/create-ssh-file2-1.png" alt="Create ssh tect file" class="wp-image-142" sizes="auto, (max-width: 750px) 100vw, 750px" /> <figcaption>ssh file location in linux</figcaption></figure> 

Once prepared, insert the card into the Pi device, power it up and attach it with an Ethernet cable to your router. I have an extra USB port on the router which I use for powering up the little pi.

### Reserve the Pi&#8217;s ip on the home router {.wp-block-heading}

This is somehow variable depending on the home router manufacturer. Once you log on to the router administration page, on the DHCP settings page of your router, you should see all connected devices, both the LAN and the wireless ones. Look for the device called rapsberrypi and note the ip and MAC address. Now look for a setting called DHCP reservation, or in my case is called DHCP binding, that will allow you to set the ip of your raspberry device as reserved or static. This is necessary because the pi will function as a DHCP server that needs a static ip. For more information, check out this <a rel="noreferrer noopener" href="https://www.howtogeek.com/184310/ask-htg-should-i-be-setting-static-ip-addresses-on-my-router/" target="_blank">tutorial</a> from howtogeek.<figure class="wp-block-image size-large is-style-default">

<img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="393" src="/uploads/2021/05/DHCP-binding-1.png" alt="Home router DHCP settings" class="wp-image-144" sizes="auto, (max-width: 750px) 100vw, 750px" /> <figcaption>This is how it looks on my router</figcaption></figure> 

Once I added the ip and the MAC address, the lease time of the DHCP provided ip changed from about 84000 seconds to infinity.

### Install Pi-hole {.wp-block-heading}

The OS installed on the pi has a text-based user interface, so we will connect to it by secure shell or ssh. More info on this <a rel="noreferrer noopener" href="https://en.wikipedia.org/wiki/Secure_Shell_Protocol" target="_blank">wiki</a>. SSH is very convenient as the pi does not need a user interface for the pi-hole, and we will rarely need to configure the pi. For the pi-hole configuration, we can use a nifty web interface, as we will see.

The ssh connection app depends on your computer main OS. For windows, you can use <a href="https://www.chiark.greenend.org.uk/~sgtatham/putty/" target="_blank" rel="noreferrer noopener">Putty</a>, a handy terminal emulator. For Linux/Mac, the default terminal is more than adequate.<figure class="wp-block-image size-large is-style-default">

<img data-recalc-dims="1" loading="lazy" decoding="async" width="544" height="600" src="/uploads/2021/05/putty.png" alt="Putty start screen" class="wp-image-145" sizes="auto, (max-width: 544px) 100vw, 544px" /> <figcaption>Putty</figcaption></figure> 

In putty, use the pi&#8217;s ip address as the hostname, in my case 192.168.1.104 and raspberry as the password.<figure class="wp-block-image size-large is-style-default">

<img data-recalc-dims="1" loading="lazy" decoding="async" width="495" height="348" src="/uploads/2021/05/putty2-1.png" alt="Putty connected to the pi screen" class="wp-image-147" sizes="auto, (max-width: 495px) 100vw, 495px" /> <figcaption>Putty connected to the pi</figcaption></figure> 

On Linux/Mac, open a terminal and type ssh pi@pi&#8217;s ip address or in my case:

`ssh pi@192.168.1.104`

Type the password as above. 

From now on, the commands and the screens are the same on all OSes, so let&#8217;s soldier on:

<pre class="wp-block-preformatted">wget -O basic-install.sh https://install.pi-hole.net</pre>

Once the script is downloaded type:

<pre id="block-13bf21da-7997-43aa-b116-230e656c2684" class="wp-block-preformatted">sudo bash basic-install.sh</pre>

Now the install turns into a classic old-school GUI setup:<figure class="wp-block-image size-large is-style-default">

<img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="424" src="/uploads/2021/05/install3-1.png" alt="pi-hole install screen 1" class="wp-image-149" sizes="auto, (max-width: 750px) 100vw, 750px" /> <figcaption>Intro</figcaption></figure> <figure class="wp-block-image size-large is-style-default"><img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="423" src="/uploads/2021/05/install4.png" alt="pi-hole installation donation page" class="wp-image-150" sizes="auto, (max-width: 750px) 100vw, 750px" /><figcaption>If you like it, why not contribute</figcaption></figure> <figure class="wp-block-image size-large is-style-default"><img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="423" src="/uploads/2021/05/install5.png" alt="" class="wp-image-151" sizes="auto, (max-width: 750px) 100vw, 750px" /><figcaption>Done this in the router configuration</figcaption></figure> <figure class="wp-block-image size-large is-style-default"><img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="469" src="/uploads/2021/05/install6.png" alt="pi-hole installation network internet selection" class="wp-image-152" sizes="auto, (max-width: 750px) 100vw, 750px" /><figcaption>Choose eht0 (Ethernet 0) here for wired</figcaption></figure> <figure class="wp-block-image size-large is-style-default"><img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="422" src="/uploads/2021/05/install7.png" alt="pi-hole installation DNS provider" class="wp-image-153" sizes="auto, (max-width: 750px) 100vw, 750px" /><figcaption>I selected Cloudfare as it does not collect any info </figcaption></figure> <figure class="wp-block-image size-large is-style-default"><img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="423" src="/uploads/2021/05/install8.png" alt="pi-hole installation ad list" class="wp-image-154" sizes="auto, (max-width: 750px) 100vw, 750px" /><figcaption>Ad lists contain known ad domains. The default list is perfect for now.</figcaption></figure> <figure class="wp-block-image size-large is-style-default"><img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="424" src="/uploads/2021/05/install9.png" alt="pi-hole installation select ip protocols" class="wp-image-155" sizes="auto, (max-width: 750px) 100vw, 750px" /><figcaption>Default is fine</figcaption></figure> <figure class="wp-block-image size-large is-style-default"><img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="422" src="/uploads/2021/05/install10.png" alt="" class="wp-image-156" sizes="auto, (max-width: 750px) 100vw, 750px" /></figure> <figure class="wp-block-image size-large is-style-default"><img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="424" src="/uploads/2021/05/install11.png" alt="pi-hole installation ip conflict notice" class="wp-image-157" sizes="auto, (max-width: 750px) 100vw, 750px" /><figcaption>We already configured the router</figcaption></figure> 

You might get an IPv6 confirmation screen for your ipv6 address. Just ok it. I skipped the image as it contained my ipv6 address.<figure class="wp-block-image size-large is-style-default">

<img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="468" src="/uploads/2021/05/install13.png" alt="pi-hole installation web interface options" class="wp-image-158" sizes="auto, (max-width: 750px) 100vw, 750px" /> <figcaption>I left on as the interface is very useful</figcaption></figure> <figure class="wp-block-image size-large is-style-default"><img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="467" src="/uploads/2021/05/install14.png" alt="pi-hole installation web server option" class="wp-image-159" sizes="auto, (max-width: 750px) 100vw, 750px" /><figcaption>Needed for the web interface</figcaption></figure> <figure class="wp-block-image size-large is-style-default"><img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="469" src="/uploads/2021/05/install15.png" alt="" class="wp-image-160" sizes="auto, (max-width: 750px) 100vw, 750px" /><figcaption>Not really needed. Turn off to protect the SD card extensive rewriting.</figcaption></figure> 

If this is left on, it will reduce the life of your SD card. One option is to use <a rel="noreferrer noopener" href="https://github.com/azlux/log2ram" target="_blank">log2ram</a>, a script that enables saving of all logs on the ram instead of the SD card. If left on, the option can easily be disabled in the web interface settings. Just look for the disable query logging option.<figure class="wp-block-image size-large is-style-default">

<img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="468" src="/uploads/2021/05/install16.png" alt="" class="wp-image-161" sizes="auto, (max-width: 750px) 100vw, 750px" /> <figcaption>Faster Than Light DNS services provided by Pi-hole. I left it at default.</figcaption></figure> <figure class="wp-block-image size-large is-style-default"><img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="469" src="/uploads/2021/05/install17.png" alt="pi-hole installation confirmation screen" class="wp-image-162" sizes="auto, (max-width: 750px) 100vw, 750px" /><figcaption>Congratulations. Pi-hole is installed.</figcaption></figure> 

Replace &#8220;password&#8221; with whatever is displayed on your screen. Please make a note of it because you will need it for the web interface configuration login. 

### Post-installation {.wp-block-heading}

Now that the pi acts as your DNS server, you need to let your home router in on that too. This needs another visit to the router configuration page. Just type the ip of the pi device as DNS server. In my case, this is in Network > LAN > DHCP Server > DNS Server 1 IP Address.

Now restart your pi and the rest of the devices connected, and Bob&#8217;s your uncle. 

Another option would be to leave the DNS unchanged on the router and only change it on the devices you want your ads blocked by the pi-hole. This is slightly more involved and is different depending on the device, but it might be useful if someone else in the household is experiencing difficulties with your stringent ad diet. 

Web interface after some browsing should look as below. To login for more information and setting changes, use the info on the last page of the installation screen. <figure class="wp-block-image size-large is-style-default">

<img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="544" src="/uploads/2021/05/web-interface.png" alt="pi-hole web interface" class="wp-image-164" sizes="auto, (max-width: 750px) 100vw, 750px" /> <figcaption>Web interface</figcaption></figure> 

Of note, the pi-hole won&#8217;t block ads on websites that offer ads on their own pages, i.e. Youtube or Facebook, because the ad and the content domain are the same. Also, some ads are beneficial for sustaining some small websites and content creators, so please consider whitelisting ads on websites you want to endorse. 

Hopefully this tutorial proves useful for installing Pi-hole on your home network. I certainly learned a whole lot by writing it. 

Useful links:

<ul class="wp-block-list">
 <li>
 <a href="https://pi-hole.net/" target="_blank" rel="noreferrer noopener">https://pi-hole.net/</a>
 </li>
 <li>
 <a href="https://www.smarthomebeginner.com/pi-hole-setup-guide/" target="_blank" rel="noreferrer noopener">https://www.smarthomebeginner.com/pi-hole-setup-guide/</a>
 </li>
 <li>
 <a href="https://pimylifeup.com/raspberry-pi-pi-hole/" target="_blank" rel="noreferrer noopener">https://pimylifeup.com/raspberry-pi-pi-hole/</a>
 </li>
 <li>
 <a href="https://www.raspberrypi.org/software/" target="_blank" rel="noreferrer noopener">https://www.raspberrypi.org/software/</a>
 </li>
 <li>
 <a href="https://phoenixnap.com/kb/enable-ssh-raspberry-pi" target="_blank" rel="noreferrer noopener">https://phoenixnap.com/kb/enable-ssh-raspberry-pi</a>
 </li>
 <li>
 <a href="https://www.chiark.greenend.org.uk/~sgtatham/putty/" target="_blank" rel="noreferrer noopener">https://www.chiark.greenend.org.uk/~sgtatham/putty/</a>
 </li>
 <li>
 <a href="https://docs.pi-hole.net/ftldns/privacylevels" target="_blank" rel="noreferrer noopener">https://docs.pi-hole.net/ftldns/privacylevels</a>
 </li>
 <li>
 <a href="https://www.howtogeek.com/184310/ask-htg-should-i-be-setting-static-ip-addresses-on-my-router/" target="_blank" rel="noreferrer noopener">https://www.howtogeek.com/184310/ask-htg-should-i-be-setting-static-ip-addresses-on-my-router/</a>
 </li>
 <li>
 <a href="https://en.wikipedia.org/wiki/Secure_Shell_Protocol" target="_blank" rel="noreferrer noopener">https://en.wikipedia.org/wiki/Secure_Shell_Protocol</a>
 </li>
 <li>
 <a href="https://en.wikipedia.org/wiki/Domain_Name_System" target="_blank" rel="noreferrer noopener">https://en.wikipedia.org/wiki/Domain_Name_System</a>
 </li>
</ul>
