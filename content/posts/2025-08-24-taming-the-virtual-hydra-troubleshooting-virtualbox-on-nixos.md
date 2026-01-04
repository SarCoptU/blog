---
title: 'Taming the Virtual Hydra: Troubleshooting VirtualBox on NixOS'
author: SarCoptU
date: 2025-08-24T21:37:14+00:00

categories:
 - dev
 - GNU/Linux
tags:
 - GNU/Linux
 - nixOS
 - open source
---
<p class="">
 What started as a quick VirtualBox install turned into a multi‑headed boss fight: USB errors, a sulking kernel driver, a turf war with KVM, and even a Nix syntax bite. Here’s the complete journey and the fixes that finally made my VM boot.
</p>

<hr class="wp-block-separator has-alpha-channel-opacity" />

## Head #1 — USB devices wouldn’t enumerate {.wp-block-heading}

<pre class="wp-block-code"><code>Can't enumerate USB devices
Could not load the Host USB Proxy service: VERR_NOT_FOUND</code></pre>

<p class="">
 <strong>Root cause:</strong> Host USB support + Extension Pack missing, and the user not in <code>vboxusers</code>.
</p>

<p class="">
 <strong>Fix (configuration.nix):</strong>
</p>

<pre class="wp-block-code"><code>virtualisation.virtualbox.host.enable = true;
virtualisation.virtualbox.host.enableExtensionPack = true;
users.users.&lt;your-user&gt;.extraGroups = &#91; "vboxusers" ];</code></pre>

<p class="">
 <strong>Apply:</strong>
</p>

<pre class="wp-block-code"><code>sudo nixos-rebuild switch
reboot</code></pre>

<p class="">
 <strong>Verify:</strong>
</p>

<pre class="wp-block-code"><code>groups | tr ' ' '\n' | grep -x vboxusers
VBoxManage list usbhost</code></pre>

<hr class="wp-block-separator has-alpha-channel-opacity" />

## Head #2 — “VirtualBox kernel driver is not accessible” {.wp-block-heading}

<pre class="wp-block-code"><code>VERR_VM_DRIVER_NOT_ACCESSIBLE</code></pre>

<p class="">
 <strong>Root cause (in my case):</strong> launching a per‑user VirtualBox build while the system had different environment/udev context.
</p>

<p class="">
 <strong>Checks:</strong>
</p>

<pre class="wp-block-code"><code># modules & devices are fine?
lsmod | grep -E 'vboxdrv|vboxnetflt|vboxnetadp|vboxpci'
ls -l /dev/vboxdrv /dev/vboxnetctl # expect root:vboxusers, mode 660

# you should be in the group
id -nG | tr ' ' '\n' | grep -x vboxusers || echo NOT-IN-GROUP

# make sure you're using the system binaries
which VirtualBox
which VBoxManage
VBoxManage -v</code></pre>

<p class="">
 <strong>Fixes that worked:</strong>
</p>

<ul class="wp-block-list">
 <li class="">
 Use only the system package (avoid a per‑user copy): <code>environment.systemPackages = [ pkgs.virtualbox ];</code>
 </li>
 <li class="">
 Restart your session so it picks up <code>vboxusers</code> (or <code>loginctl terminate-user $USER</code>, then log back in).
 </li>
 <li class="">
 Kill stale VBox processes: <code>pkill -f VBox</code>.
 </li>
 <li class="">
 Quick test: <code>VBoxManage list hostinfo</code> should work; then <code>VBoxManage startvm "YourVM" --type headless</code>.
 </li>
</ul>

<hr class="wp-block-separator has-alpha-channel-opacity" />

## Head #3 — KVM vs. VirtualBox (VMX root mode) {.wp-block-heading}

<pre class="wp-block-code"><code>VirtualBox can't operate in VMX root mode
(VERR_VMX_IN_VMX_ROOT_MODE)</code></pre>

<p class="">
 <strong>Root cause:</strong> the KVM kernel modules had already claimed hardware virtualization.
</p>

<p class="">
 <strong>Permanent fix (configuration.nix):</strong>
</p>

<pre class="wp-block-code"><code>boot.blacklistedKernelModules = &#91;
 "kvm"
 "kvm_intel" # or "kvm_amd" on AMD
];</code></pre>

<p class="">
 <strong>Temporary test without rebuild:</strong>
</p>

<pre class="wp-block-code"><code>sudo systemctl stop libvirtd 2&gt;/dev/null || true
sudo modprobe -r kvm_intel kvm # or: kvm_amd kvm
lsmod | grep kvm || echo "KVM not loaded"</code></pre>

<hr class="wp-block-separator has-alpha-channel-opacity" />

## Head #4 — Nix syntax bite &#8211; ooops {.wp-block-heading}

<pre class="wp-block-code"><code>error: syntax error, unexpected '{', expecting INHERIT</code></pre>

<p class="">
 <strong>Fix:</strong> don’t open a new attrset mid‑file; place options directly in the top‑level <code>{ ... }</code> of <code>configuration.nix</code>.
</p>

<hr class="wp-block-separator has-alpha-channel-opacity" />

## Final validation {.wp-block-heading}

<pre class="wp-block-code"><code># devices
ls -l /dev/vboxdrv /dev/vboxnetctl

# environment
which VBoxManage && VBoxManage -v

# quick health
VBoxManage list hostinfo

# start headless (clear error output)
VBoxManage startvm "ComandoVM" --type headless

# logs (path shown in showvminfo)
tail -n 80 ~/VirtualBox\ VMs/ComandoVM/Logs/VBox.log</code></pre>

## TL;DR: Fix checklist {.wp-block-heading}

<ul class="wp-block-list">
 <li class="">
 Enable VirtualBox host + Extension Pack; add yourself to <code>vboxusers</code>.
 </li>
 <li class="">
 Use the system VirtualBox binary; kill stale processes; refresh your login session.
 </li>
 <li class="">
 Disable KVM when you need VirtualBox (they can’t share VT‑x).
 </li>
 <li class="">
 Keep <code>configuration.nix</code> tidy — one misplaced brace can block a rebuild.
 </li>
</ul>

<p class="">
 After one last reboot with KVM out of the way, the VM finally powered on. The hydra, at long last, was tamed.
</p>

<p class="">
</p>

<p class="">
 Created with AI
</p>
