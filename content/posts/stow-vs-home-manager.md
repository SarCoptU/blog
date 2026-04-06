---
title: "Stow vs Home Manager: Managing Dotfiles on NixOS (and Beyond)"
date: 2026-04-06
draft: false
tags: ["nixos", "dotfiles", "home-manager", "gnu-stow", "linux"]
description: "Why I moved from GNU Stow to Home Manager for dotfiles, and when Stow still makes sense."
---

I've used GNU Stow to manage dotfiles for a while. It's simple, elegant, and gets out of your way. You organise configs into directories, run `stow */`, and symlinks appear in `$HOME`. Done.

But once I committed to NixOS as my daily driver, Stow started feeling like the wrong tool for the job — an imperative shim bolted onto a system designed to never need one.

This post covers what changed, why Home Manager replaced Stow in my workflow, and when Stow is still the better choice.

## What Stow Does Well

Stow manages symlinks. That's it, and that's the point. You keep your dotfiles in a Git repo, structured by program:

```
dotfiles/
  tmux/.tmux.conf
  zsh/.zshrc
  hyprland/.config/hypr/hyprland.conf
```

Run `stow tmux zsh hyprland` and the symlinks land where they belong. It's language-agnostic, distro-agnostic, and has almost no learning curve.

For years this was enough. Clone the repo on a new machine, install the packages manually, stow the configs, and you're running.

## Where It Gets Awkward on NixOS

NixOS is declarative. Packages live in `/nix/store`, system config is generated from Nix expressions, and the entire OS state is reproducible from a single flake. Stow sits outside all of this.

The friction shows up in a few places:

**Packages and configs are decoupled.** You declare `programs.hyprland` in your NixOS config but manage `hyprland.conf` separately through Stow. Enable a program in one place, configure it in another. Disable a program and its config just lingers.

**No validation.** Stow doesn't know or care whether your config is valid. A typo in `hyprland.conf` silently exists until you launch Hyprland and something breaks. Home Manager type-checks options at evaluation time — you find out before the switch completes.

**Rebuilds don't capture everything.** `nixos-rebuild switch` handles system state but knows nothing about your stowed dotfiles. You end up with two separate workflows for what should be one operation.

**New machine setup is still partly manual.** The promise of NixOS is "clone the flake, rebuild, done." With Stow in the mix, it's "clone the flake, rebuild, also clone the dotfiles repo, also stow everything, also hope the directory structure still matches."

None of these are dealbreakers on their own. Together, they add up to a system that's half-declarative and half-imperative — which on NixOS is worse than being fully either.

## What Home Manager Brings

Home Manager is the NixOS-native answer to dotfile management. It uses the same module system as NixOS itself and treats packages, config files, and user services as a single unit.

Enable a program, configure it, and both the binary and the config are managed together:

```nix
programs.alacritty = {
  enable = true;
  settings = {
    terminal.shell.program = "tmux";
    font.size = 14.0;
  };
};
```

Disable it and everything goes away cleanly. No orphaned symlinks, no leftover config files.

When integrated as a NixOS module, `nixos-rebuild switch` handles both system and user config in one command. One flake, one rebuild, full reproducibility.

### The "Inlining" Trick

The biggest misconception about Home Manager is that you need to rewrite all your configs in Nix from day one. You don't.

Almost every HM module has an `extraConfig` or equivalent escape hatch that accepts raw config as a string:

```nix
wayland.windowManager.hyprland = {
  enable = true;
  extraConfig = ''
    $terminal = alacritty
    $browser = firefox
    # ... paste your entire hyprland.conf here verbatim
  '';
};
```

This is "inlining" — zero restructuring, just wrap your existing config in `''` delimiters and it works. You get the integration benefits immediately and can convert to native Nix options at your own pace. There's no deadline. Leaving things in `extraConfig` forever is perfectly valid.

### Progressive Conversion

Once inlined and working, you can Nixify piece by piece. Take tmux as an example.

Start fully inlined:

```nix
programs.tmux = {
  enable = true;
  extraConfig = ''
    set -g mouse on
    set -g default-terminal "xterm-256color"
    unbind -T root WheelUpPane
    # ...
  '';
};
```

Then replace lines with native HM options as you find them:

```nix
programs.tmux = {
  enable = true;
  mouse = true;
  terminal = "xterm-256color";
  extraConfig = ''
    # only the scroll bindings remain — no native option for these
    unbind -T root WheelUpPane
    # ...
  '';
};
```

Some programs convert fully. Some always keep a few lines in `extraConfig`. Both are fine.

## Structuring It in a Flake

If you're already running NixOS from a flake, Home Manager slots in as a module. Add it as an input:

```nix
inputs = {
  nixpkgs.url = "github:NixOS/nixpkgs/nixos-unstable";
  home-manager = {
    url = "github:nix-community/home-manager";
    inputs.nixpkgs.follows = "nixpkgs";
  };
};
```

Then wire it into your host configuration. From there you can split your home config into composable modules — a base module for terminal essentials (tmux, zsh, alacritty) that every machine gets, a desktop module for Hyprland/Waybar/Wofi that only workstations import, and per-host files for machine-specific overrides like monitor profiles.

The result is a single flake that fully describes every machine: system config, user environment, services, the lot.

## When Stow Still Wins

Home Manager is the right tool on NixOS. That doesn't make it the right tool everywhere.

**Non-NixOS systems.** If you're on Arch, Ubuntu, macOS, or anything else, Stow is simpler and has zero ecosystem buy-in. You don't need to learn Nix to manage symlinks.

**Rapid iteration.** Edit a config, see the change immediately. No rebuild cycle. If you're tweaking Hyprland keybinds every five minutes, `$EDITOR hyprland.conf` beats `nixos-rebuild switch` for speed.

**Mixed environments.** If you manage dotfiles across NixOS and non-NixOS machines from one repo, Stow is the lowest common denominator that works everywhere.

**Simplicity.** Stow is one concept: symlinks. Home Manager is a module system with its own options, types, activation scripts, and generations. The power is real, but so is the complexity.

## Migration Order

If you're moving from Stow to Home Manager, start with the programs that have the simplest HM modules and work outward:

1. **tmux** — minimal module, well-documented, quick win
2. **alacritty** — settings map nearly 1:1 from TOML to Nix
3. **zsh/oh-my-zsh** — excellent native HM support, worth Nixifying properly
4. **kanshi** — fully native profiles, no `extraConfig` needed at all
5. **wofi** — small config, easy conversion
6. **waybar** — inline the CSS, convert JSON settings to Nix attrsets
7. **hyprland/sway** — start inlined, convert to native `settings` later

## The Bottom Line

Stow manages symlinks. Home Manager manages your environment. On NixOS, where the whole point is declarative reproducibility, Home Manager is the natural fit — it extends the same model from system config down to your shell aliases and status bar theme.

But the right tool depends on your system. If you're not on NixOS, or if you value simplicity over integration, Stow is still excellent at what it does. The two aren't really competitors — they solve the same problem at different levels of abstraction.

## References

Free Software Foundation (2024) *GNU Stow*. Available at: https://www.gnu.org/software/stow/ (Accessed: 6 April 2026).

Nix Community (2025) *Home Manager Manual*. Available at: https://nix-community.github.io/home-manager/ (Accessed: 6 April 2026).

Nix Community (2025) *Home Manager — GitHub repository*. Available at: https://github.com/nix-community/home-manager (Accessed: 6 April 2026).

NixOS Foundation (2025) *NixOS Manual*. Available at: https://nixos.org/manual/nixos/stable/ (Accessed: 6 April 2026).

NixOS Foundation (2025) *Nix Pills*. Available at: https://nixos.org/guides/nix-pills/ (Accessed: 6 April 2026).

NixOS Wiki Contributors (2025) 'Flakes', *NixOS Wiki*. Available at: https://wiki.nixos.org/wiki/Flakes (Accessed: 6 April 2026).

NixOS Wiki Contributors (2025) 'Home Manager', *NixOS Wiki*. Available at: https://wiki.nixos.org/wiki/Home_Manager (Accessed: 6 April 2026).

Roudjane, Y. (2025) *Home Manager Options Search*. Available at: https://home-manager-options.extradocs.com (Accessed: 6 April 2026).

MyNixOS Contributors (2025) *MyNixOS — NixOS and Home Manager Option Search*. Available at: https://mynixos.com (Accessed: 6 April 2026).

### Written With AI
