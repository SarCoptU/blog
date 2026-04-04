---
title: "The Start of Home Manager Journey"
author: SarCoptU
date: 2026-04-04T00:00:00+00:00

categories:
 - NixOS
 - Linux
tags:
 - NixOS
 - home-manager
 - tmux
 - zsh
 - alacritty
 - oh-my-zsh
 - flakes
---

One of the things I love most about NixOS is that once you get something working, it is reproducible everywhere. This post covers how I wired up Alacritty, tmux, and Zsh with Oh My Zsh into a single shared home-manager module that applies across all my machines.

## The setup

All my machines share a `home/modules/base.nix` file that is imported into each host's home-manager configuration. The goal was a terminal experience that:

- Opens directly into a persistent tmux session
- Uses Zsh with syntax highlighting and autosuggestions
- Uses the `kali-like` Oh My Zsh theme, pinned by hash

## Alacritty launches straight into tmux

Instead of landing at a bare shell, Alacritty is configured to start tmux and attach to (or create) a named session:

```nix
programs.alacritty = {
  enable = true;
  settings = {
    terminal.shell = {
      program = "tmux";
      args = [ "new-session" "-A" "-D" "-s" "main" ];
    };
    font.size = 14.0;
  };
};
```

The flags mean:
- `-A` — attach if the session already exists
- `-D` — detach any other client connected to that session
- `-s "main"` — session name

This means every time you open Alacritty, you land in the same persistent session. Close the window, reopen it, and you are right back where you were.

## Tmux config

Mouse support and a sane scroll behaviour are the main additions. By default tmux scroll can be awkward — this config makes it so that scrolling up enters copy mode, and scrolling down exits it naturally:

```nix
programs.tmux = {
  enable = true;
  mouse = true;
  terminal = "xterm-256color";
  extraConfig = ''
    unbind -T root WheelUpPane
    unbind -T root WheelDownPane
    bind -n WheelUpPane   if-shell -F '#{pane_in_mode}' 'send-keys -M' 'copy-mode -e; send-keys -M'
    bind -n WheelDownPane if-shell -F '#{pane_in_mode}' 'send-keys -M' 'send-keys -M'
  '';
};
```

## Zsh with Oh My Zsh

Home-manager handles the Zsh configuration, including the plugins and theme. The syntax highlighting and autosuggestions packages are installed by Nix, so they are available before the theme tries to load them:

```nix
programs.zsh = {
  enable = true;
  syntaxHighlighting.enable = true;
  autosuggestion.enable = true;
  oh-my-zsh = {
    enable = true;
    plugins = [ "git" "z" ];
    theme = "kali-like";
    custom = "${config.home.homeDirectory}/.local/share/omz-custom";
  };
};
```

The `custom` path points to a directory where home-manager drops the theme file.

## Pinning the theme by hash

The `kali-like` theme is not in the Oh My Zsh theme collection, so it is fetched from GitHub at build time and placed in the custom themes directory:

```nix
home.file.".local/share/omz-custom/themes/kali-like.zsh-theme".source = pkgs.fetchurl {
  url = "https://raw.githubusercontent.com/clamy54/kali-like-zsh-theme/main/kali-like.zsh-theme";
  hash = "sha256-uoOJ4/9YN9TbQBODZxi0GJwDHh0OtpUnoTynIAqmVGg=";
};
```

If the hash ever needs updating (e.g. after the theme file changes upstream), run:

```bash
nixos-rebuild build --flake .#<host> 2>&1 | grep "got:"
```

and replace the hash with the one reported.

## One module, all machines

Because this lives in `home/modules/base.nix` and is imported by every host, updating a single file propagates to all machines on the next rebuild. That is the real win — no drift, no manual setup.

```
home/
  modules/
    base.nix       ← shared config (this post)
  hosts/
    host1.nix
    host2.nix
    ...
```

Each host file imports `base.nix` and adds only what is host-specific.

## One side effect worth knowing

Because Alacritty always launches inside tmux, and the `kali-like` theme detects the `$TMUX` environment variable, your prompt will always show a `(tmux)` indicator. It is not a bug — it is just the theme doing its job. Honestly, I can live with it.

---

**Written With AI**
