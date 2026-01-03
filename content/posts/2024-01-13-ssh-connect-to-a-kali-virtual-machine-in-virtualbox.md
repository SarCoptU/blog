---
title: SSH Connect to a Kali Virtual Machine in Virtualbox
cover:
 image: "/uploads/2024/01/Screenshot-from-2024-01-13-09-04-13.png"
 alt: ""
 relative: false
 hidden: false
 hiddenInList: false
 hiddenInSingle: false
author: SarCoptU
date: 2024-01-13T09:26:48+00:00

categories:
 - GNU/Linux
 - IT/Cybersec
tags:
 - cybersecurity
 - Kali-linux
 - penetration testing
 - SSH
 - VirtualBox
---
<p class="">
 Kali is a great learning tool for learning cybersecurity, especially the red-attacking side of it.
</p>

<p class="">
 For the longest time, I had trouble running the Kali VM on my rather old Thinkpad X1 Carbon, as the image consumes quite a few resources.
</p>

<p class="">
 There is a way to use most of Kali&#8217;s tools with little resource consumption: ssh into the image via your distro terminal.
</p>

<p class="">
 It turns out it&#8217;s quite easy to set this up in VirtualBox, as described in this article:
</p>

<p class="">
 <a href="https://averagelinuxuser.com/ssh-into-virtualbox/#install-ssh-in-virtualbox-os">https://averagelinuxuser.com/ssh-into-virtualbox/#install-ssh-in-virtualbox-os</a>
</p>

<p class="">
 Steps:
</p>

<p class="">
 1. Download the latest Kali VirtualBox VM from <a href="https://www.kali.org/get-kali/#kali-virtual-machines">https://www.kali.org/get-kali/#kali-virtual-machines</a>
</p>

<p class="">
 2. Go to the machine settings, network, port forwarding
</p><figure class="wp-block-image size-full">

<img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="494" src="/uploads/2024/01/Screenshot-from-2024-01-13-09-04-13.png" alt="virtulabox setting network page" class="wp-image-392" sizes="auto, (max-width: 750px) 100vw, 750px" /> </figure> 

<p class="">
 3. Set name, protocol, host port and guest port
</p><figure class="wp-block-image size-full">

<img data-recalc-dims="1" loading="lazy" decoding="async" width="668" height="426" src="/uploads/2024/01/Screenshot-from-2024-01-13-09-06-59.png" alt="virtualbox port forwarding rules settings page" class="wp-image-393" sizes="auto, (max-width: 668px) 100vw, 668px" /> </figure> 

<p class="">
 4. Open the terminal in the VM and enable SSH access
</p>

<pre class="wp-block-code"><code>systemctl enable ssh --now</code></pre>

<p class="">
 5. Open your system terminal and connect via SSH by the chosen port
</p><figure class="wp-block-image size-large">

<img data-recalc-dims="1" loading="lazy" decoding="async" width="750" height="273" src="/uploads/2024/01/terminal.png" alt="terminal showing an ssh connection to a kali virtual machine" class="wp-image-395" sizes="auto, (max-width: 750px) 100vw, 750px" /> </figure> 

<p class="">
 The VM can be left open on the login screen.
</p>

<p class="">
 This will consume a lot less resources to run the image and keep the option to run the GUI options in the Kali VM if needed.
</p>

<p class="">
 Happy hacking!
</p>

<p class="">
 References:
</p>

<ul class="wp-block-list">
 <li class="">
 Average Linux User (2022). <em>How to SSH into VirtualBox machine</em><a href="https://averagelinuxuser.com/ssh-into-virtualbox/#install-ssh-in-virtualbox-os">. Available at: https://averagelinuxuser.com/ssh-into-virtualbox/#install-ssh-in-virtualbox-os</a> (Accessed 10 January 2024)
 </li>
 <li class="">
 Kali Downloads page: <a href="https://www.kali.org/get-kali/#kali-virtual-machines">https://www.kali.org/get-kali/#kali-virtual-machines</a>
 </li>
</ul>

<p class="">
</p>
