---
title: "Migrating from NixOS Flakes to Arch Linux"
date: 2026-05-28
draft: false
tags: ["arch", "nixos", "linux", "hyprland", "dotfiles", "gnu-stow", "automation"]
description: "How I moved my daily driver from a NixOS flake to a scripted Arch install, and what it took to get everything working."
---

I've been running NixOS with Flakes as my daily driver for a while. The reproducibility is genuinely impressive — declare your system once, deploy it anywhere. But over time the cognitive overhead started to outweigh the benefits for a single-user setup. Every small config change requires understanding the Nix language, the module system, and whether something is managed by NixOS or Home Manager. I wanted something I could debug with basic Linux knowledge without reaching for the manual.

So I moved to Arch. This is the full account of how that went.

## Why Arch

Arch isn't the obvious choice if you're fleeing NixOS complexity. But the goals aligned well:

- Rolling release, so packages are current without chasing overlays
- Minimal base, so I build up exactly what I want
- An AUR helper covers obscure packages that aren't in the official repos
- Btrfs + snapper gives snapshot-based rollback, which was one of the things I'd miss most from NixOS

The tradeoff is that reproducibility becomes your own responsibility. NixOS declares state; Arch assumes you can reconstruct it. That's what the automation is for.

## The Automation Strategy

The approach is split across two repos:

- **linux** — install scripts, package lists, and host-specific setup
- **dotfiles** — configs managed with GNU Stow, one directory per program

The idea: boot an Arch ISO, run through a handful of manual steps, clone the linux repo, and run `install.sh`. Everything else is automated.

## Step 1 — Connect to WiFi

The Arch ISO uses `iwd`. There's no NetworkManager yet, so:

```bash
iwctl
```

Inside the prompt:

```
device list
station wlan0 scan
station wlan0 get-networks
station wlan0 connect "YourSSID"
quit
```

Verify:

```bash
ping -c 3 archlinux.org
```

## Step 2 — Base Install via archinstall

```bash
archinstall
```

The key choices:

| Option | Value |
|---|---|
| Filesystem | Btrfs with default subvolumes (`@`, `@home`, `@snapshots`, `@var_log`) |
| Bootloader | grub |
| Kernels | mainline + lts |
| Audio | pipewire |
| Network | NetworkManager |
| Hostname | must match a directory under `hosts/` in your linux repo |
| Additional packages | `git stow vim curl base-devel openssh` |

The hostname is critical — the install script reads `/etc/hostname` to decide which host-specific setup to run. Get it wrong and host config is silently skipped.

The Btrfs subvolume layout is also non-negotiable if you want snapper. It expects `@snapshots` at `/.snapshots`. The default archinstall layout provides exactly that.

## Step 3 — SSH Key for GitHub

Private repos need SSH. `openssh` is now installed, so:

```bash
ssh-keygen -t ed25519 -C "username@hostname"
cat ~/.ssh/id_ed25519.pub
```

Copy the output and add it to GitHub via **Settings → SSH and GPG keys → New SSH key** from any browser — a phone works fine.

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
ssh -T git@github.com
```

A note on the comment flag: `-C` is just a label. Using `username@hostname` instead of an email means each machine key is identifiable in GitHub's key list, and you can have different keys per machine without confusion.

## Step 4 — Running the Installer

```bash
git clone git@github.com:you/linux.git ~/linux
cd ~/linux
./install.sh
```

The installer:

1. Enables multilib (needed for 32-bit packages like Steam runtime libs)
2. Updates the system
3. Installs an AUR helper
4. Installs everything in `packages/common.txt` via the AUR helper — official and AUR packages in one pass
5. Configures snapper for Btrfs snapshots
6. Enables the display manager, Bluetooth, NetworkManager, libvirtd, CUPS, and other services
7. Clones and runs the dotfiles installer

## Lessons Learned Along the Way

### `hostname` command isn't in the base install

The script originally used `$(hostname)` to detect the machine. `hostname` comes from a package that isn't installed in a minimal Arch base. Switched to `$(cat /etc/hostname)` which reads directly from the filesystem with no dependencies.

### pacman can't install AUR packages

The original script ran `pacman -S` on the full package list before the AUR helper was set up. With `set -e`, the whole script aborted on the first AUR package not found in the official repos. Fix: install the AUR helper first, then use it exclusively for everything — it falls back to official repos automatically.

### multilib needs to be enabled explicitly

Some 32-bit compatibility packages live in the multilib repository, which is disabled by default. The script now uncomments the multilib section in `/etc/pacman.conf` before the first system update.

### Oh My Zsh creates a `.zshrc` that blocks stow

Oh My Zsh generates `~/.zshrc` during install. When stow then tries to link the dotfiles version, it finds a regular file — not a symlink — and refuses:

```
cannot stow dotfiles/zsh/.zshrc over existing target .zshrc
since neither a link nor a directory and --adopt not specified
```

Fix: `rm -f ~/.zshrc` before the stow loop runs.

### The default shell doesn't change itself

Zsh was installed but the default shell was still bash. Opening a terminal dropped into bash and you had to type `zsh` manually. Fixed by adding `chsh -s $(which zsh)` to the dotfiles installer.

### A display manager is needed for automatic Hyprland launch

Without one, you land at a tty login prompt after boot. SDDM handles the graphical login and launches Hyprland automatically. It has solid Wayland support and is the most commonly paired DM with Hyprland.

## The Dotfiles Setup

Configs are managed with GNU Stow. The repo is structured as one directory per program:

```
dotfiles/
  alacritty/.config/alacritty/alacritty.toml
  hypr/.config/hypr/hyprland.conf
  tmux/.tmux.conf
  waybar/.config/waybar/config
  zsh/.zshrc
  hosts/
    laptop/.config/kanshi/config
    desktop/.config/kanshi/config
```

Base configs stow universally. Host-specific configs — like monitor layouts — live under `hosts/<hostname>/` and are stowed separately based on the machine name read from `/etc/hostname`.

The dotfiles installer also handles Oh My Zsh and a custom theme, sets zsh as the default shell, and removes conflicting files before stowing.

## End Result

Boot → SDDM login screen → Hyprland desktop, fully configured. The whole process from ISO boot to working desktop takes about 30–40 minutes, most of which is package installation time.

The NixOS config isn't going anywhere — it covers other machines where the declarative model makes more sense. But for a laptop where I want a fast, debuggable, rolling system without the Nix overhead, this setup hits the mark.

---

## References

Arch Linux (no date) *Archinstall* [online]. Available at: https://wiki.archlinux.org/title/Archinstall (Accessed: 28 May 2026).

Arch Linux (no date) *Btrfs* [online]. Available at: https://wiki.archlinux.org/title/Btrfs (Accessed: 28 May 2026).

Arch Linux (no date) *iwd* [online]. Available at: https://wiki.archlinux.org/title/Iwd (Accessed: 28 May 2026).

Arch Linux (no date) *Snapper* [online]. Available at: https://wiki.archlinux.org/title/Snapper (Accessed: 28 May 2026).

GNU (no date) *Stow* [online]. Available at: https://www.gnu.org/software/stow/ (Accessed: 28 May 2026).

Hyprland (no date) *Hyprland Wiki* [online]. Available at: https://wiki.hyprland.org/ (Accessed: 28 May 2026).

Oh My Zsh (no date) *Oh My Zsh* [online]. Available at: https://ohmyz.sh/ (Accessed: 28 May 2026).

SDDM (no date) *SDDM* [online]. Available at: https://github.com/sddm/sddm (Accessed: 28 May 2026).

Tommeurs (no date) *Arch vs NixOS Workstation* [online]. Available at: https://tommeurs.nl/posts/arch-vs-nixos-workstation/ (Accessed: 28 May 2026).

---

*Written with AI*
