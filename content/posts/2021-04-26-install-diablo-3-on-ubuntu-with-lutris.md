---
title: Install Diablo 3 on Ubuntu with Lutris
author: SarCoptU
date: 2021-04-26T07:54:08+00:00

categories:
 - GNU/Linux
tags:
 - Diablo 3
 - Gaming
 - GNU/Linux
 - Lutris
---
<div class="wp-block-image is-style-default">
 <figure class="aligncenter size-large is-resized"><img data-recalc-dims="1" loading="lazy" decoding="async" src="/uploads/2021/04/diablo-3-1.jpeg" alt="diablo 3 wallpaper" class="wp-image-94" width="666" height="416" sizes="auto, (max-width: 666px) 100vw, 666px" /></figure>
</div>

<div class="wp-block-group">
 <div class="wp-block-group__inner-container is-layout-flow wp-block-group-is-layout-flow">
 <div class="wp-block-group">
 <div class="wp-block-group__inner-container is-layout-flow wp-block-group-is-layout-flow">
 <p>
 Linux gaming had become easier and easier over the years thanks to the wonderful communities at <a rel="noreferrer noopener" href="https://lutris.net/" data-type="URL" data-id="https://lutris.net/" target="_blank">Lutris</a> and <a rel="noreferrer noopener" href="https://www.playonlinux.com/en/" data-type="URL" data-id="https://www.playonlinux.com/en/" target="_blank">PlayOnLinux</a> and the implementations of <a rel="noreferrer noopener" href="https://www.amd.com/en/technologies/vulkan" data-type="URL" data-id="https://www.amd.com/en/technologies/vulkan" target="_blank">Vulcan</a> on the Linux side of computing. I have arranged the options by steps, but sometimes the steps need to be changed based on the local implementation, previous installed packages and preference. Without further ado, let&#8217;s begin:
 </p>
 </div>
 </div>
 </div>
</div>

<div class="wp-block-group">
 <div class="wp-block-group__inner-container is-layout-flow wp-block-group-is-layout-flow">
 <p>
 <strong>Install 32 bit architecture and 32 bit core libraries</strong>
 </p>
 
 <p class="has-small-font-size">
 <code>sudo apt-get install libc6-i386</code>
 </p>
 </div>
</div>

<div class="wp-block-group">
 <div class="wp-block-group__inner-container is-layout-flow wp-block-group-is-layout-flow">
 <p>
 <strong>Update/upgrade the system</strong>
 </p>
 
 <p class="has-small-font-size">
 <code>sudo apt-get update</code>
 </p>
 
 <p class="has-small-font-size">
 <code>sudo apt-get upgrade</code>
 </p>
 </div>
</div>

<div class="wp-block-group">
 <div class="wp-block-group__inner-container is-layout-flow wp-block-group-is-layout-flow">
 <p>
 <strong>Install Graphics Card or Proprietary Drivers using &#8220;Additional Drivers&#8221;</strong>
 </p>
 
 <p>
 This process differs for NVIDIA/AMD graphic cards respectively, but it should be relatively easy in Ubuntu. Specifically for NVIDIA, the ubuntu-drivers tool can be used.
 </p>
 
 <p class="has-small-font-size">
 <code>ubuntu-drivers devices</code>
 </p>
 
 <p class="has-small-font-size">
 <code>sudo apt install nvidia-driver-xxx</code>
 </p>
 
 <p>
 Where xxx is the driver version found with the ubuntu-drivers tool.
 </p>
 
 <p>
 Shot out to <a rel="noreferrer noopener" href="https://linuxize.com/post/how-to-nvidia-drivers-on-ubuntu-20-04/" data-type="URL" data-id="https://linuxize.com/post/how-to-nvidia-drivers-on-ubuntu-20-04/" target="_blank">linuxize</a> blog for the tool. Head there if you need more details.
 </p>
 </div>
</div>

<div class="wp-block-group">
 <div class="wp-block-group__inner-container is-layout-flow wp-block-group-is-layout-flow">
 <p>
 <strong>Install Microsoft fonts</strong>
 </p>
 
 <p class="has-small-font-size">
 <code>sudo apt-get install ttf-mscorefonts-installer</code>
 </p>
 </div>
</div>

<div class="wp-block-group">
 <div class="wp-block-group__inner-container is-layout-flow wp-block-group-is-layout-flow">
 <p>
 <strong>Install Ubuntu restricted extras</strong>
 </p>
 
 <p class="has-small-font-size">
 <code>sudo apt-get install ubuntu-restricted-extras</code>
 </p>
 </div>
</div>

<div class="wp-block-group">
 <div class="wp-block-group__inner-container is-layout-flow wp-block-group-is-layout-flow">
 <p>
 <strong>Install <a rel="noreferrer noopener" href="https://lutris.net/downloads/" data-type="URL" data-id="https://lutris.net/downloads/" target="_blank">Lutris</a></strong>
 </p>
 
 <p class="has-small-font-size">
 <code>sudo add-apt-repository ppa:lutris-team/lutris</code><br /><code>sudo apt update</code><br /><code>sudo apt install lutris</code>
 </p>
 </div>
</div>

Once installed, search for the game page and run script.

Happy hunting!
