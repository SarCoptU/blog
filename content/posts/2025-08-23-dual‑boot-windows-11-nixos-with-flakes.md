---
title: 'Dual‑Boot: Windows 11 + NixOS (with Flakes)'
cover:
  image: "//uploads/2025/08/nixos_win11_ai_gen_image.jpg"
author: SarCoptU
date: 2025-08-23T12:55:38+00:00

categories:
 - GNU/Linux
tags:
 - GNU/Linux
---
<p class="">
 For ages I wanted a clean dual‑boot between <strong>Windows 11</strong> (for the few things that require it) and <strong>NixOS</strong> (for everything I actually enjoy). This is the full journey: GParted wizardry, fixing MBR/GPT issues, laying out a big EFI (because NixOS loves EFI space), and finishing with flakes.
</p>

<hr class="wp-block-separator has-alpha-channel-opacity" />

## Step 1 — Disk Layout in GParted {.wp-block-heading}

<p class="">
 I booted a live USB with <strong>GParted</strong> and created the following:
</p>

<ul class="wp-block-list">
 <li class="">
 <strong>EFI System Partition</strong> (ESP): <code>1030 MB</code>, FAT32, flags <code>boot, esp</code>
 </li>
 <li class="">
 <strong>Windows</strong>: <code>200 GB</code>, NTFS
 </li>
 <li class="">
 <strong>Linux swap</strong>: <code>8 GB</code>
 </li>
 <li class="">
 <strong>Linux root</strong>: rest of the disk (later Btrfs)
 </li>
</ul>

<p class="">
 Why a big ESP? <strong>NixOS + systemd‑boot keeps multiple generations</strong> of kernels/initrds. A tiny 100 MB ESP fills up fast.
</p>

## Step 2 — Windows 11 Install (and the MBR → GPT Fix) {.wp-block-heading}

<p class="">
 Windows initially refused to install with the classic message:
</p>

<blockquote class="wp-block-quote is-layout-flow wp-block-quote-is-layout-flow">
 <p class="">
 “The selected disk has an MBR partition table. On EFI systems, Windows can only be installed to GPT disks.”
 </p>
</blockquote>

<p class="">
 The fix: press <kbd>Shift</kbd> + <kbd>F10</kbd> in the installer to open a command prompt and run <code>diskpart</code>:
</p>

<pre class="wp-block-code"><code>diskpart
select disk 0 # use your install disk
clean
convert gpt

create partition efi size=1030
format quick fs=fat32 label="EFI"
assign letter=S

create partition msr size=16

create partition primary size=200000
format quick fs=ntfs label="Windows"
assign letter=W
exit
</code></pre>

<p class="">
 Back in the GUI, I selected the 200 GB NTFS partition and installation proceeded normally.
</p>

## Step 3 — NixOS Minimal with Btrfs + Subvolumes {.wp-block-heading}

<p class="">
 After Windows finished, I installed NixOS from the minimal ISO, reusing the same 1 GB ESP.
</p>

### Format & subvolumes {.wp-block-heading}

<pre class="wp-block-code"><code># Swap (8 GB) and Btrfs (rest) were created as p4 and p5
mkswap /dev/nvme0n1p4
swapon /dev/nvme0n1p4

mkfs.btrfs -f -L nixos-root /dev/nvme0n1p5

# Create subvolumes
mount /dev/nvme0n1p5 /mnt
btrfs subvolume create /mnt/@
btrfs subvolume create /mnt/@home
btrfs subvolume create /mnt/@nix
btrfs subvolume create /mnt/@log
umount /mnt
</code></pre>

### Mounts for install {.wp-block-heading}

<pre class="wp-block-code"><code>mount -o subvol=@,compress=zstd,noatime /dev/disk/by-label/nixos-root /mnt
mkdir -p /mnt/{home,nix,var/log,boot/efi}
mount -o subvol=@home,compress=zstd,noatime /dev/disk/by-label/nixos-root /mnt/home
mount -o subvol=@nix,compress=zstd,noatime /dev/disk/by-label/nixos-root /mnt/nix
mount -o subvol=@log,noatime /dev/disk/by-label/nixos-root /mnt/var/log

mount /dev/disk/by-label/EFI /mnt/boot/efi
swapon /dev/disk/by-label/swap
</code></pre><figure class="wp-block-image">

<figcaption class="wp-element-caption">Final partition map: EFI (1G), MSR (16M), Win (200G), swap (8G), NixOS (Btrfs).</figcaption></figure> 

## Step 4 — Flakes + Local Clone {.wp-block-heading}

<p class="">
 I cloned my flake locally into <code>/mnt/etc/nixos</code> (SSH keys already added to GitHub):
</p>

<pre class="wp-block-code"><code>nix-shell -p git openssh
git clone git@github.com:YOURUSER/YOUR-REPO.git /mnt/etc/nixos
nixos-generate-config --root /mnt
# copy the fresh hardware config into my host dir
cp /mnt/etc/nixos/hardware-configuration.nix /mnt/etc/nixos/hosts/&lt;hostname/
</code></pre>

<p class="">
 Because it’s a local path flake, I installed with <code>--impure</code>:
</p>

<pre class="wp-block-code"><code>nixos-install --impure --flake /mnt/etc/nixos#&lt;hostname&gt;
</code></pre>

## The Final Gotcha: `git push` or Bust {.wp-block-heading}

<p class="">
 One last wrinkle: the build <em>would not succeed</em> until I <strong>committed and pushed</strong> my changes (notably the new <code>hardware-configuration.nix</code>) to GitHub. After staging and pushing, the install finished flawlessly.
</p>

<pre class="wp-block-code"><code>cd /mnt/etc/nixos
git add .
git commit -m "Add hardware config for &lt;hostname&gt;"
git push
</code></pre>

<p class="">
</p>

## 5. Disable Fast Startup in Windows Before Dual Booting {.wp-block-heading}

<p class="">
 When setting up a dual boot between <strong>Windows 11</strong> and <strong>NixOS</strong>, one common pitfall is Windows’ <em>Fast Startup</em> feature (sometimes called <em>Fast Boot</em>).
</p>

<ul class="wp-block-list">
 <li class="">
 Linux sees the Windows partitions (including the EFI system partition) as <strong>“dirty” or hibernated</strong>.
 </li>
 <li class="">
 This prevents Linux from properly mounting the EFI partition or NTFS drives.
 </li>
 <li class="">
 As a result, <code>nixos-install</code> may fail to install the bootloader, or your Windows partition will mount read-only inside Linux.
 </li>
</ul>

<p class="">
 &#x1f449; <strong>Solution:</strong> Disable Fast Startup before installing NixOS.
</p>

### Steps in Windows 11 {.wp-block-heading}

<ol class="wp-block-list">
 <li class="">
 Open <strong>Control Panel → Hardware and Sound → Power Options</strong>.
 </li>
 <li class="">
 Click <strong>Choose what the power buttons do</strong>.
 </li>
 <li class="">
 Select <strong>Change settings that are currently unavailable</strong>.
 </li>
 <li class="">
 Uncheck <strong>Turn on fast startup (recommended)</strong>.
 </li>
 <li class="">
 Save and reboot once.
 </li>
</ol>

### Optional but recommended {.wp-block-heading}

<p class="">
 Also run this in <strong>Windows PowerShell (Admin)</strong> to fully disable hibernation:
</p>

<pre class="wp-block-code"><code>
powercfg /h off
</code></pre>

<hr class="wp-block-separator has-alpha-channel-opacity" />

### &#x1f50d; Linux: Check if Windows is Still Hibernated {.wp-block-heading}

<p class="">
 From a NixOS live USB (or any Linux environment), you can check if Windows partitions are still marked as hibernated:
</p>

<pre class="wp-block-code"><code>
lsblk -f
# find your Windows NTFS partition, e.g. /dev/nvme0n1p3

sudo ntfsfix -n /dev/nvme0n1p3
</code></pre>

<p class="">
 If you see <code>Volume is hibernated</code>, it means Fast Startup or hibernation is still active. Boot back into Windows, make sure Fast Startup is disabled, and shut down completely (not restart).
</p>

<hr class="wp-block-separator has-alpha-channel-opacity" />

<p class="">
 &#x2705; With Fast Startup disabled, both operating systems can safely share the EFI partition and see each other’s filesystems without corruption risks.
</p>

## Conclusion {.wp-block-heading}

<ul class="wp-block-list">
 <li class="">
 Convert to <strong>GPT</strong> for UEFI Windows installs.
 </li>
 <li class="">
 Use a <strong>large ESP (≈1 GB)</strong> so systemd‑boot has space for generations.
 </li>
 <li class="">
 <strong>Btrfs subvolumes</strong> keep NixOS tidy and snappy.
 </li>
 <li class="">
 With flakes, remember: <strong>commit & <code>git push</code></strong> before installing.
 </li>
 <li class="">
 Disable fast boot in win 11
 </li>
</ul>

<p class="">
</p>

<p class="">
 <strong>Note: AI used for writing this blogpost</strong>
</p>
