---
title: Import an OVA into QEMU/KVM
cover:
  image: "/uploads/2025/09/qemu_import_feature.png"
author: SarCoptU
date: 2025-09-02T02:23:52+00:00

categories:
 - dev
 - GNU/Linux
tags:
 - GNU/Linux
 - nixOS
 - QEMU/KVM
---
<p class="">
 A reliable, step-by-step guide to bring a VirtualBox/VMware <code>.ova</code> into QEMU/KVM, with both GUI and CLI paths—and fixes for the most common pitfalls.<strong>Contents</strong>
</p>

<ol class="wp-block-list">
 <li class="">
 <a href="#prereqs">Prerequisites</a>
 </li>
 <li class="">
 <a href="#extract-ova">1) Extract the OVA</a>
 </li>
 <li class="">
 <a href="#convert">2) Convert VMDK → QCOW2</a>
 </li>
 <li class="">
 <a href="#virtmanager">3) Create the VM in virt-manager (GUI)</a>
 </li>
 <li class="">
 <a href="#virtinstall">4) Alternative: <code>virt-install</code> (CLI)</a>
 </li>
 <li class="">
 <a href="#gotchas">5) Common gotchas & fixes</a>
 </li>
 <li class="">
 <a href="#checklist">Quick troubleshooting checklist</a>
 </li>
</ol>

## Prerequisites {#prereqs.wp-block-heading}

<ul class="wp-block-list">
 <li class="">
 QEMU/KVM, libvirt, and virt-manager installed on the host.
 </li>
 <li class="">
 User is allowed to manage libvirt (typically being in the <code>libvirt</code>/<code>libvirtd</code> and <code>kvm</code> groups).
 </li>
 <li class="">
 Enough disk space to hold the converted image (QCOW2).
 </li>
</ul>

## 1) Extract the OVA {#extract-ova.wp-block-heading}

<p class="">
 An OVA is a tar archive containing an <code>.ovf</code> (metadata) and one or more <code>.vmdk</code> disks.
</p>

<pre class="wp-block-code"><code>mkdir ~/ova-import && cd ~/ova-import
tar -xvf /path/to/your-image.ova
# Expect: appliance.ovf, disk0.vmdk, (maybe disk1.vmdk, .mf), etc.</code></pre>

## 2) Convert VMDK → QCOW2 (recommended) {#convert.wp-block-heading}

<p class="">
 QCOW2 supports snapshots and sparse allocation.
</p>

<pre class="wp-block-code"><code># Single-disk OVA
qemu-img convert -p -f vmdk disk0.vmdk -O qcow2 disk0.qcow2

# Inspect the result
qemu-img info disk0.qcow2

# (Optional) Grow before first boot
qemu-img resize disk0.qcow2 +20G</code></pre>

<p class="">
 For multi-disk OVAs, convert each <code>.vmdk</code> to its own <code>.qcow2</code>.
</p>

## 3) Create the VM in virt-manager (GUI) {#virtmanager.wp-block-heading}

<ol class="wp-block-list">
 <li class="">
 Open <strong>Virtual Machine Manager</strong> → <strong>Create a new virtual machine</strong>.
 </li>
 <li class="">
 Select <strong>Import existing disk image</strong>.
 </li>
 <li class="">
 <strong>Browse</strong> to <code>disk0.qcow2</code>.
 </li>
 <li class="">
 Choose the closest <strong>Guest OS type</strong> (Linux/Windows and version).
 </li>
 <li class="">
 <strong>Firmware</strong>: <ul class="wp-block-list">
 <li class="">
 Try <strong>UEFI (OVMF)</strong> first if the source VM used UEFI.
 </li>
 <li class="">
 If it fails to boot, recreate the VM using <strong>BIOS (SeaBIOS)</strong>.
 </li>
 </ul>
 </li>
 
 <li class="">
 Assign CPU/RAM to taste.
 </li>
 <li class="">
 Tick <strong>Customize configuration before install</strong>, then: <ul class="wp-block-list">
 <li class="">
 <strong>Chipset</strong>: <code>q35</code> is a good default; try <code>i440fx</code> if needed.
 </li>
 <li class="">
 <strong>Disk bus</strong>: start with <strong>VirtIO</strong> for performance. If the guest can’t boot/find the disk, switch to <strong>SATA</strong>. (Windows may need VirtIO drivers—see below.)
 </li>
 <li class="">
 <strong>NIC model</strong>: <strong>virtio</strong> (or <strong>e1000</strong> for maximum compatibility).
 </li>
 <li class="">
 If using SCSI, add a <strong>VirtIO SCSI</strong> controller and attach the disk to it.
 </li>
 </ul>
 </li>
 
 <li class="">
 Click <strong>Begin Installation</strong>.
 </li>
</ol>

## 4) Alternative: `virt-install` (CLI) {#virtinstall.wp-block-heading}

<pre class="wp-block-code"><code>virt-install \
 --name my-imported-vm \
 --memory 4096 --vcpus 4 \
 --import \
 --disk path=/path/to/disk0.qcow2,format=qcow2,bus=virtio \
 --os-variant detect=on,require=off \
 --network network=default,model=virtio \
 --graphics spice \
 --boot uefi</code></pre>

## 5) Common gotchas & fixes {#gotchas.wp-block-heading}

### “network &#8216;default&#8217; is not active” {.wp-block-heading}

<p class="">
 If the default NAT network isn’t up:
</p>

<pre class="wp-block-code"><code>virsh net-start default
virsh net-autostart default</code></pre>

<p class="">
 If the network definition is missing, define it, then start and autostart:
</p>

<pre class="wp-block-code"><code># Path may vary by distro
virsh net-define /usr/share/libvirt/networks/default.xml
virsh net-start default
virsh net-autostart default</code></pre>

### Black screen / “No bootable device” {.wp-block-heading}

<ul class="wp-block-list">
 <li class="">
 Try switching <strong>Firmware</strong> (UEFI &#x2194; BIOS).
 </li>
 <li class="">
 Change <strong>Disk bus</strong> from VirtIO → SATA to test.
 </li>
 <li class="">
 Verify the disk has a bootloader/ESP. Attach it to a helper VM and inspect with <code>lsblk</code>/<code>fdisk -l</code>.
 </li>
</ul>

### Windows guests (imported from OVA) {.wp-block-heading}

<ul class="wp-block-list">
 <li class="">
 If using VirtIO disk/NIC, mount the <strong>virtio-win</strong> ISO in virt-manager and install drivers (during setup or via Device Manager).
 </li>
 <li class="">
 If you see <em>INACCESSIBLE_BOOT_DEVICE</em>, switch the disk bus to <strong>SATA</strong> to boot, install VirtIO storage drivers, then switch back to VirtIO.
 </li>
</ul>

### Linux guests boot but no network {.wp-block-heading}

<ul class="wp-block-list">
 <li class="">
 Predictable interface names may differ. Check <code>ip a</code> inside the guest and update its network config.
 </li>
 <li class="">
 For very old distros, try NIC model <strong>e1000</strong>.
 </li>
</ul>

### CPU features / performance quirks {.wp-block-heading}

<ul class="wp-block-list">
 <li class="">
 In VM <strong>CPU</strong> settings, use <strong>Copy host CPU configuration</strong> (aka <code>host-passthrough</code>) for best compatibility.
 </li>
</ul>

### Multi-disk OVAs {.wp-block-heading}

<ul class="wp-block-list">
 <li class="">
 Convert each VMDK and attach them in the same order as in the OVF (disk0 → vda/sda, disk1 → vdb/sdb, …).
 </li>
</ul>

<hr class="wp-block-separator has-alpha-channel-opacity" />

## Quick troubleshooting checklist {#checklist.wp-block-heading}

<ul class="wp-block-list">
 <li class="">
 Used the right firmware? (Try UEFI first, then BIOS.)
 </li>
 <li class="">
 Tried switching disk bus VirtIO &#x2194; SATA?
 </li>
 <li class="">
 Does the libvirt <code>default</code> network exist and run?
 </li>
 <li class="">
 For Windows: installed VirtIO drivers or temporarily used SATA?
 </li>
 <li class="">
 For Linux: interface name changed? Adjust network config.
 </li>
 <li class="">
 Set CPU mode to host-passthrough?
 </li>
</ul>

<p class="">
 Tip: Keep the original OVA and the converted QCOW2 until the new VM boots cleanly.
</p>

<p class="">
 Blogpost created with AI help
</p>
