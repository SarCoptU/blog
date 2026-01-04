---
title: NixOS Cheatsheet
author: SarCoptU
date: 2025-08-24T20:52:07+00:00
categories:
 - dev
 - GNU/Linux
tags:
 - GNU/Linux
 - IT
 - nixOS
---
<p class="">
 If you’ve ever felt overwhelmed by NixOS commands, flakes, or the dreaded <em>garbage collection panic</em>, this guide is for you. It’s not just a cheatsheet—it’s a practical survival kit for running NixOS with flakes, keeping your system clean, and always having a rollback strategy when things go sideways.
</p>

<hr class="wp-block-separator has-alpha-channel-opacity" />

## &#x1f5a5;&#xfe0f; NixOS Essentials <figure class="wp-block-table">

<table class="has-fixed-layout">
 <tr>
 <th>
 Task
 </th>
 
 <th>
 Command
 </th>
 </tr>
 
 <tr>
 <td>
 Show version
 </td>
 
 <td>
 <code>nixos-version</code>
 </td>
 </tr>
 
 <tr>
 <td>
 Edit config (classic)
 </td>
 
 <td>
 <code>sudo nano /etc/nixos/configuration.nix</code>
 </td>
 </tr>
 
 <tr>
 <td>
 Rebuild + switch
 </td>
 
 <td>
 <code>sudo nixos-rebuild switch</code>
 </td>
 </tr>
 
 <tr>
 <td>
 Build only (no switch)
 </td>
 
 <td>
 <code>sudo nixos-rebuild build</code>
 </td>
 </tr>
 
 <tr>
 <td>
 Roll back last rebuild
 </td>
 
 <td>
 <code>sudo nixos-rebuild switch --rollback</code>
 </td>
 </tr>
 
 <tr>
 <td>
 List generations
 </td>
 
 <td>
 <code>sudo nix-env --list-generations --profile /nix/var/nix/profiles/system</code>
 </td>
 </tr>
</table></figure> 

<hr class="wp-block-separator has-alpha-channel-opacity" />

## &#x26a1; Flakes Basics<figure class="wp-block-table">

<table class="has-fixed-layout">
 <tr>
 <th>
 Task
 </th>
 
 <th>
 Command
 </th>
 </tr>
 
 <tr>
 <td>
 Init flake
 </td>
 
 <td>
 <code>nix flake init -t templates#nixos</code>
 </td>
 </tr>
 
 <tr>
 <td>
 Show flake outputs
 </td>
 
 <td>
 <code>nix flake show</code>
 </td>
 </tr>
 
 <tr>
 <td>
 Update all inputs
 </td>
 
 <td>
 <code>nix flake update --commit-lock-file</code>
 </td>
 </tr>
 
 <tr>
 <td>
 Update one input
 </td>
 
 <td>
 <code>nix flake lock --update-input nixpkgs --commit-lock-file</code>
 </td>
 </tr>
 
 <tr>
 <td>
 Build system
 </td>
 
 <td>
 <code>sudo nixos-rebuild build --flake .#&lt;hostname&gt;</code>
 </td>
 </tr>
 
 <tr>
 <td>
 Switch system
 </td>
 
 <td>
 <code>sudo nixos-rebuild switch --flake .#&lt;hostname&gt;</code>
 </td>
 </tr>
 
 <tr>
 <td>
 Boot (safe)
 </td>
 
 <td>
 <code>sudo nixos-rebuild boot --flake .#&lt;hostname&gt;</code>
 </td>
 </tr>
</table></figure> 

<hr class="wp-block-separator has-alpha-channel-opacity" />

## &#x1f504; Safe Updates & Rollbacks {.wp-block-heading}

<p class="">
 Keeping your system updated is great… until it doesn’t boot. Here’s how to avoid disasters.
</p><figure class="wp-block-table">

<table class="has-fixed-layout">
 <tr>
 <th>
 Scenario
 </th>
 
 <th>
 Command / Action
 </th>
 </tr>
 
 <tr>
 <td>
 Preview changes
 </td>
 
 <td>
 <code>nix store diff-closures /nix/var/nix/profiles/system ./result</code>
 </td>
 </tr>
 
 <tr>
 <td>
 Safe upgrade (boot next gen only after reboot)
 </td>
 
 <td>
 <code>sudo nixos-rebuild boot --flake .#&lt;host&gt;</code>
 </td>
 </tr>
 
 <tr>
 <td>
 Roll back after bad switch
 </td>
 
 <td>
 <code>sudo nixos-rebuild switch --rollback</code>
 </td>
 </tr>
 
 <tr>
 <td>
 Boot into old system
 </td>
 
 <td>
 Select an older generation at bootloader
 </td>
 </tr>
 
 <tr>
 <td>
 Revert to last good commit
 </td>
 
 <td>
 <code>git checkout &lt;commit&gt;</code> + <code>sudo nixos-rebuild boot --flake .#&lt;host&gt;</code>
 </td>
 </tr>
</table></figure> 

### Quick Routine {.wp-block-heading}

<pre class="wp-block-code"><code>nix flake update --commit-lock-file

sudo nixos-rebuild build --flake .#&lt;host>

nix store diff-closures /nix/var/nix/profiles/system ./result

sudo nixos-rebuild boot --flake .#&lt;host>

sudo reboot
</code></pre>

<hr class="wp-block-separator has-alpha-channel-opacity" />

## &#x1f9f9; Garbage Collection & Store Management {.wp-block-heading}

<p class="">
 NixOS keeps everything by default—which is great for rollbacks but eats disk space fast.
</p><figure class="wp-block-table">

<table class="has-fixed-layout">
 <tr>
 <th>
 Task
 </th>
 
 <th>
 Command
 </th>
 </tr>
 
 <tr>
 <td>
 Collect garbage
 </td>
 
 <td>
 <code>sudo nix-collect-garbage</code>
 </td>
 </tr>
 
 <tr>
 <td>
 Delete old gens + GC
 </td>
 
 <td>
 <code>sudo nix-collect-garbage -d</code>
 </td>
 </tr>
 
 <tr>
 <td>
 GC older than 14 days
 </td>
 
 <td>
 <code>sudo nix-collect-garbage --delete-older-than 14d</code>
 </td>
 </tr>
 
 <tr>
 <td>
 Optimize store (deduplication)
 </td>
 
 <td>
 <code>sudo nix store optimise</code>
 </td>
 </tr>
 
 <tr>
 <td>
 Check system size
 </td>
 
 <td>
 <code>nix path-info -Sh /run/current-system</code>
 </td>
 </tr>
</table></figure> 

### Configure Automatic GC (NixOS module) {.wp-block-heading}

<pre class="wp-block-code"><code>{
 boot.loader.systemd-boot.configurationLimit = 20;
 nix.gc = {
 automatic = true;
 dates = "weekly";
 options = "--delete-older-than 14d";
 };
}
</code></pre>

<hr class="wp-block-separator has-alpha-channel-opacity" />

## &#x1f9ed; Pro Tips {.wp-block-heading}

<ul class="wp-block-list">
 <li class="">
 <strong>Always prefer <code>boot</code> over <code>switch</code></strong> when testing major updates. Reboot to try it, rollback at bootloader if it fails.
 </li>
 <li class="">
 <strong>Keep your lockfile in git.</strong> Rollbacks are trivial when you can checkout an old commit.
 </li>
 <li class="">
 <strong>GC smartly.</strong> Don’t delete all history immediately—keep 14+ days for safety.
 </li>
 <li class="">
 <strong>Use <code>nix store diff-closures</code></strong> before switching, to see what really changed.
 </li>
</ul>

<hr class="wp-block-separator has-alpha-channel-opacity" />

## &#x1f680; Final Thoughts {.wp-block-heading}

<p class="">
 Running NixOS with flakes doesn’t have to feel like juggling chainsaws. With a solid update routine, rollback plan, and controlled garbage collection, you’ll have both cutting-edge packages <em>and</em> peace of mind.
</p>

<p class="">
 This cheatsheet is meant to be your daily companion—bookmark it, print it, or stick it on your wall. Happy hacking!
</p>

<p class="">
</p>

<p class="">
 Blogpost created with AI help
</p>
