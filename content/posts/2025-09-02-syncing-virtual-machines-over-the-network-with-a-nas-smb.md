---
title: Syncing Virtual Machines Over The Network with a NAS (SMB)
cover:
  image: "//uploads/2025/09/nas-sync1.jpg"
author: SarCoptU
date: 2025-09-02T00:45:15+00:00

categories:
 - GNU/Linux
---
<p class="">
 <em>A simple, repeatable workflow for one VM image (Kali.qcow2), root-owned files, and an SMB share.</em>
</p>

## Overview {.wp-block-heading}

<p class="">
 I run a Kali Linux virtual machine under QEMU/KVM and wanted to use the same VM across three different machines, while keeping a central backup on my NAS. The approach below keeps <strong>one master copy on the NAS</strong> and uses <code>rsync</code> to <strong>push</strong> updates from whichever host I used last and <strong>pull</strong> them on the next host.
</p>

## Step 1 — The Starting Point {.wp-block-heading}

<p class="">
 On my main machine, virt-manager created the Kali VM image at:
</p>

<pre class="wp-block-code"><code>~/Documents/VMs/Kali.qcow2</code></pre>

<p class="">
 This <strong>qcow2</strong> file is the only thing I sync.
</p>

## Step 2 — Create a Location on the NAS {.wp-block-heading}

<p class="">
 Create a folder on the NAS to store the master copy:
</p>

<pre class="wp-block-code"><code>/mnt/nas/vms/Kali/</code></pre>

## Step 3 — Mount the SMB Share on Each Host {.wp-block-heading}

<ol class="wp-block-list">
 <li class="">
 Install SMB client tools:
 </li>
</ol>

<pre class="wp-block-code"><code>sudo apt install cifs-utils</code></pre>

<ol start="2" class="wp-block-list">
 <li class="">
 Create a mount point:
 </li>
</ol>

<pre class="wp-block-code"><code>sudo mkdir -p /mnt/nas/vms</code></pre>

<ol start="3" class="wp-block-list">
 <li class="">
 Mount the share (adjust server/share, user, and options to your environment):
 </li>
</ol>

<pre class="wp-block-code"><code>sudo mount -t cifs //nas/vms /mnt/nas/vms \
 -o username=nasuser,password=secret,vers=3.0,uid=root,gid=root</code></pre>

<p class="">
 Add an <code>/etc/fstab</code> entry for auto-mount at boot:
</p>

<pre class="wp-block-code"><code>//nas/vms /mnt/nas/vms cifs username=nasuser,password=secret,vers=3.0,uid=root,gid=root 0 0</code></pre>

<p class="">
 <small><strong>Tip:</strong> For better security, store credentials in <code>/etc/samba/credentials</code> and reference with <code>credentials=/etc/samba/credentials</code> instead of inline <code>username=</code>/<code>password=</code>.</small>
</p>

## Step 4 — Push the VM to the NAS (Create/Update the Master Copy) {.wp-block-heading}

<p class="">
 Shut down the VM, then from the host you just used:
</p>

<pre class="wp-block-code"><code>sudo rsync -a --sparse --info=progress2 \
 ~/Documents/VMs/Kali.qcow2 \
 /mnt/nas/vms/Kali/Kali.qcow2</code></pre>

<p class="">
 This preserves ownership/permissions, handles sparse qcow2 efficiently, and only transfers changed blocks after the first run.
</p>

## Step 5 — Pull the VM to Another Machine {.wp-block-heading}

<p class="">
 On a different host, before starting work, pull the latest image down:
</p>

<pre class="wp-block-code"><code>sudo rsync -a --sparse --info=progress2 \
 /mnt/nas/vms/Kali/Kali.qcow2 \
 ~/Documents/VMs/Kali.qcow2</code></pre>

<p class="">
 Then start the VM:
</p>

<pre class="wp-block-code"><code>virsh start Kali</code></pre>

<p class="">
 …or open it in virt-manager.
</p>

## Daily Workflow Summary {.wp-block-heading}

<ul class="wp-block-list">
 <li class="">
 <strong>After using the VM:</strong> shut it down → <em>push</em> to NAS.
 </li>
 <li class="">
 <strong>Before using it on another host:</strong> <em>pull</em> from NAS → start VM.
 </li>
</ul>

<p class="">
 Core commands:
</p>

<pre class="wp-block-code"><code># Push (backup/update)
sudo rsync -a --sparse ~/Documents/VMs/Kali.qcow2 /mnt/nas/vms/Kali/Kali.qcow2

# Pull (restore/update)
sudo rsync -a --sparse /mnt/nas/vms/Kali/Kali.qcow2 ~/Documents/VMs/Kali.qcow2</code></pre>

## Best Practices {.wp-block-heading}

<ul class="wp-block-list">
 <li class="">
 <strong>Always shut down</strong> the VM before syncing to avoid image corruption.
 </li>
 <li class="">
 Keep the mount point consistent across all machines (e.g., <code>/mnt/nas/vms</code>) to reuse the same commands.
 </li>
 <li class="">
 Occasionally make versioned backups for restore points: <code>sudo rsync -a --sparse \ ~/Documents/VMs/Kali.qcow2 \ /mnt/nas/vms/Kali/Kali-$(date +%Y%m%d).qcow2</code>
 </li>
 <li class="">
 Do not run the same VM simultaneously on two hosts—this workflow is for sync, not shared live access.
 </li>
 <li class="">
 (Optional) If your VM uses UEFI and a persistent NVRAM file, sync that too (path varies by distro).
 </li>
</ul>

<p class="">
 <strong>Wrap-up:</strong> With a single master copy on the NAS and two rsync commands (push & pull), I can move my Kali VM between machines in seconds without changing ownership from <code>root:root</code>.
</p>

## Keeping the libvirt XML in Sync (Portable XML) {.wp-block-heading}

<p class="">
 In addition to syncing the <code>Kali.qcow2</code> image, keep the VM’s libvirt definition (the domain XML used by Virtual Machine Manager / virt-manager) portable and synced via your NAS. This approach is simple and manual, fits the rsync workflow, and avoids per-host drift.
</p>

### 1) Export once on the source host {.wp-block-heading}

<p class="">
 Dump the inactive domain XML to your home folder:
</p>

<pre class="wp-block-code"><code>virsh dumpxml --inactive Kali &gt; ~/Documents/VMs/Kali.xml</code></pre>

### 2) Store it on the NAS next to the image {.wp-block-heading}

<p class="">
 Push the XML to your mounted SMB share (mounted at <code>/mnt/nas/vms</code>):
</p>

<pre class="wp-block-code"><code>sudo rsync -a ~/Documents/VMs/Kali.xml /mnt/nas/vms/Kali/Kali.xml</code></pre>

### 3) (Re)define from the NAS on other hosts {.wp-block-heading}

<p class="">
 On any other machine, pull the image as usual, then (re)define the VM from the NAS copy:
</p>

<pre class="wp-block-code"><code>sudo virsh define /mnt/nas/vms/Kali/Kali.xml
virsh autostart Kali # optional, one-time</code></pre>

### 4) When you change VM settings {.wp-block-heading}

<p class="">
 If you edit CPU/RAM/devices in virt-manager on any host:
</p>

<pre class="wp-block-code"><code># Re-export XML on the host where you made changes
virsh dumpxml --inactive Kali &gt; ~/Documents/VMs/Kali.xml

# Update the NAS copy
sudo rsync -a ~/Documents/VMs/Kali.xml /mnt/nas/vms/Kali/Kali.xml

# On the other hosts, re-define from NAS
sudo virsh define /mnt/nas/vms/Kali/Kali.xml</code></pre>

### Portable XML checklist {.wp-block-heading}

<ul class="wp-block-list">
 <li class="">
 <strong>Disk path:</strong> use the same local path on all hosts, e.g. <code>&lt;source file='~/Documents/VMs/Kali.qcow2'/&gt;</code>. Keep that path consistent across machines so you don’t have to edit XML per host.
 </li>
 <li class="">
 <strong>Network:</strong> prefer a portable choice such as the default NAT network: <code>&lt;interface type='network'&gt; &lt;source network='default'/&gt; &lt;/interface&gt;</code> or ensure the same bridge name exists on each host.
 </li>
 <li class="">
 <strong>MAC address:</strong> to avoid conflicts if two hosts might be online with the VM config, remove the fixed MAC from the XML (libvirt will generate one on define), or maintain distinct MACs per host.
 </li>
 <li class="">
 <strong>UEFI firmware:</strong> if your XML includes OVMF, ensure each host has a valid <code>&lt;loader ...&gt;OVMF_CODE.fd&lt;/loader&gt;</code> path. For the NVRAM line: <ul class="wp-block-list">
 <li class="">
 Either copy the VARS file to the same path on all hosts and keep the <code>&lt;nvram&gt;</code> line,
 </li>
 <li class="">
 or remove the <code>&lt;nvram&gt;</code> line and let libvirt create a fresh VARS file on each host.
 </li>
 </ul>
 </li>
 
 <li class="">
 <strong>Host-specific devices:</strong> avoid pinning GPU PCI addresses or USB passthrough in the shared XML unless all hosts match. Keep those in per-host overrides if needed.
 </li>
</ul>

### Commands recap (image + XML) {.wp-block-heading}

<p class="">
 <em>Push updated image and XML from a host you just used:</em>
</p>

<pre class="wp-block-code"><code>sudo rsync -a --sparse ~/Documents/VMs/Kali.qcow2 /mnt/nas/vms/Kali/Kali.qcow2
sudo rsync -a ~/Documents/VMs/Kali.xml /mnt/nas/vms/Kali/Kali.xml</code></pre>

<p class="">
 <em>Pull image and (re)define on another host:</em>
</p>

<pre class="wp-block-code"><code>sudo rsync -a --sparse /mnt/nas/vms/Kali/Kali.qcow2 ~/Documents/VMs/Kali.qcow2
sudo virsh define /mnt/nas/vms/Kali/Kali.xml
virsh start Kali</code></pre>

<p class="">
 NOTE: if default network not starting, try
</p>

<pre class="wp-block-code"><code>sudo virsh net-start default 
# or for autostart
sudo virsh net-autostart default</code></pre>

<p class="">
 Blogpost written with AI
</p>
