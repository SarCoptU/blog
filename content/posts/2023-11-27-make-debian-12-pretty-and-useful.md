---
title: Make Debian 12 Pretty and Useful
cover:
 image: "/uploads/2023/11/debian-1.png"
 alt: ""
 relative: false
 hidden: false
 hiddenInList: false
 hiddenInSingle: false
author: SarCoptU
date: 2023-11-27T17:40:10+00:00

categories:
 - GNU/Linux
tags:
 - bash
 - Debian
 - scripting
---
<p class="">
 There are a few customisations I have come to love on the personal Debian install. This blog post is meant to describe the steps for my ideal (at this point) Debian 12 post-installation cleanup and provide a few security applications for everyday use and a few programs for light security work.
</p>

## Download and Install Debian 12 {.wp-block-heading}

<p class="">
 Download the DVD version for 64-bit PC architecture from here:
</p>

<p class="">
 <a href="https://cdimage.debian.org/debian-cd/current/amd64/iso-dvd/">https://cdimage.debian.org/debian-cd/current/amd64/iso-dvd/</a>
</p>

<p class="">
 Installation-wise, I chose to install the Gnome and nothing else:
</p><figure class="wp-block-image size-full is-resized">

<img data-recalc-dims="1" loading="lazy" decoding="async" width="583" height="582" src="/uploads/2023/11/debian-1.png" alt="" class="wp-image-378" style="width:443px;height:auto" sizes="auto, (max-width: 583px) 100vw, 583px" /> </figure> 

<p class="">
 I am dual-booting the Linux install with Windows on the same hard disk drive for the odd times when Windows is needed.
</p>

<p class="">
 No password is inserted for the admin to avoid the creation of a root account that might be hacked.
</p>

<p class="">
 I went myself for minimal – no extras install, but the games and LibreOffice were still installed. I can always remove them afterwards. There is probably a way to remove all games from the command line interface, but the most convenient (and time-consuming) way to remove the games is through the Software app.
</p>

## The minor stuff {.wp-block-heading}

<p class="">
 After the initial Firefox sign-in and synchronisation of all plugins, it is time to move on to the actual customisations.
</p>

<p class="">
 The first thing to do is to remove the CD-ROM repository. The system update will fail if this repository is not removed.
</p>

<p class="">
 <em>Open Software & Updates > Other Software tobsoleteab > Disable CD-ROM repositories > Close > Reload</em> (Average Linux User)
</p>

<p class="">
 If the root user was enabled, the current user needs to be added to the sudo group:
</p>

<pre class="wp-block-code"><code># add user to sudo group
su -
usermod -aG sudo &lt;username&gt;</code></pre>

<p class="">
 Restart for the changes to be made.
</p>

<p class="">
 The next order of business is to sort out the Windows/Linux dual boot clock issue.
</p>

<pre class="wp-block-code"><code>timedatectl set-local-rtc 1 --adjust-system-clock</code></pre>

<p class="">
 The following personal step is to swap the CapsLock and the left Control buttons on the keyboard:
</p>

<p class="">
 There are just a few letters to add to a file on Linux, and the mod is ready. This is done in the terminal, and the file to change is named “keyboard”.
</p>

<pre class="wp-block-code"><code>sudo nano /etc/default/keyboard</code></pre>

<p class="">
 Look for the with XKOPTIONS and change it to
</p>

<pre class="wp-block-code"><code>XKOPTIONS="ctrl:swapcaps"</code></pre>

<p class="">
 Another reboot is in order at this point.
</p>

<p class="">
 If you are not happy with the result, change the row back to
</p>

<pre class="wp-block-code"><code>XKOPTIONS=""</code></pre>

## The ****major stuff {.wp-block-heading}

<p class="">
 Most of the major stuff is actually contained in a neat little script on GitHub.
</p>

<p class="">
 GitHub intial debian script:
</p>

<p class="">
 <a href="https://github.com/SarCoptU/linux-scripts/blob/main/Debian/deb_12_initial.sh">https://github.com/SarCoptU/linux-scripts/blob/main/Debian/deb_12_initial.sh</a>
</p>

<p class="">
 Pick a working directory, update and upgrade the system and install git. Clone my GitHub repository, make it executable and run the script:
</p>

<pre class="wp-block-code"><code>sudo apt update && sudo apt upgrade -y
sudo apt install git
git clone https://github.com/SarCoptU/linux-scripts/blob/main/Debian/deb_12_initial.sh
cd Debian
chmod +x install_script.sh
./install_script.sh</code></pre>

<p class="">
 This script is still a work in progress but contains all the useful configuration details and programs I use on a daily basis.
</p>

<p class="">
 After installing and configuring Oh My Zsh, a theme needs installation. The Kali-like theme will transform the terminal into a Kali terminal, autocomplete included. Instructions from the repository are below.
</p>

<pre class="wp-block-code"><code>wget -O ~/.oh-my-zsh/themes/kali-like.zsh-theme https://raw.githubusercontent.com/clamy54/kali-like-zsh-theme/master/kali-like.zsh-theme
vim ~/.zshrc</code></pre>

<p class="">
 Set ZSH_THEME=&#8221;current_theme&#8221; to ZSH_THEME=&#8221;kali-like&#8221;
</p>

<p class="">
 Enjoy Debian!
</p>

<p class="">
 References:
</p>

<ul class="wp-block-list">
 <li class="">
 Average Linux User (2021) <em>20 Essentials Things to Do After Installing Debian 11</em> Available at: <a href="https://averagelinuxuser.com/debian-11-after-install/">https://averagelinuxuser.com/debian-11-after-install/</a> (Accessed 20 November 2023)
 </li>
 <li class="">
 R. Sferle(2021) <em>Tweaks on Home Computer (Mostly GNU/Linux)</em> Available at <a href="https://remussferle.com/">https://remussferle.com/ (Accessed 22 November 2023)
 </li>
 <li class="">
 SarCoptU (2023) Debian GitHub Repository Available at: <a href="https://github.com/SarCoptU/linux-scripts/tree/main/Debian">https://github.com/SarCoptU/linux-scripts/tree/main/Debian</a> (Accessed 27 November 2023)
 </li>
 <li class="">
 Clamy54 Github Repository Available at <a href="https://github.com/clamy54/kali-like-zsh-theme">https://github.com/clamy54/kali-like-zsh-theme</a> (Accessed 11 January 2024)
 </li>
</ul>
