---
title: "Secure Synology With Tailscale"
date: 2026-03-20T23:01:14Z
draft: true
---

# Securing Nextcloud on a Synology NAS with Tailscale

Running Nextcloud at home is great — until you realise your login page is visible to the entire internet. Even with two-factor authentication, you're relying on Nextcloud's code being bulletproof, and it's had its fair share of CVEs over the years.

The fix? Put it behind Tailscale and make it completely invisible to the outside world. Here's how I set it up on a Synology NAS running Nextcloud in Docker, accessible from GrapheneOS and NixOS machines — with HTTPS and zero port forwarding.

## Why Bother if You Already Have 2FA?

The real win isn't "better login security." It's a fundamentally different security posture:

- **No public attack surface.** Your Nextcloud instance doesn't exist on the internet. No bots, no scanners, no script kiddies probing your login page.
- **No port forwarding.** Nothing open on your router.
- **WireGuard encryption** baked in for free.
- **Tailscale runs in the background** and only routes traffic destined for your tailnet — everything else (browsing, streaming) goes through your normal connection. You leave it on and forget about it.

## Step 1: Tailscale on the Synology

Install the Tailscale package from the Synology Package Center. Once it's connected, your NAS gets a Tailscale IP (e.g. `100.x.y.z`) and a MagicDNS name (e.g. `your-nas.your-tailnet.ts.net`).

Confirm with:

```bash
tailscale ip -4
tailscale status
```

## Step 2: Bind Nextcloud to the Tailscale Interface Only

This is the key move. In your `docker-compose.yml`, bind the port to your NAS's Tailscale IP — not `0.0.0.0`:

```yaml
services:
  nextcloud:
    image: nextcloud:latest
    ports:
      - "100.x.y.z:8080:80"  # Your NAS's Tailscale IP
    volumes:
      - ./nextcloud:/var/www/html
      - ./data:/var/www/html/data
    restart: unless-stopped

  db:
    image: mariadb:10
    environment:
      - MYSQL_ROOT_PASSWORD=changeme
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_PASSWORD=changeme
    volumes:
      - ./db:/var/lib/mysql
    restart: unless-stopped
```

This means the container is **only listening on the Tailscale interface**. Nobody on your LAN or the internet can reach it directly.

## Step 3: Trusted Domains

Nextcloud will block access unless you add the Tailscale address to `config.php`:

```php
'trusted_domains' =>
array (
  0 => '100.x.y.z:8080',
  1 => 'your-nas.your-tailnet.ts.net',
),
```

At this point, you can already access Nextcloud over Tailscale via `http://100.x.y.z:8080`. But we want HTTPS.

## Step 4: HTTPS with Tailscale Certs

Synology makes this easy. If you have the Tailscale package installed, you can run:

```bash
tailscale configure synology-cert
```

This automatically provisions a TLS certificate for your MagicDNS hostname and installs it into DSM's certificate manager. It also handles renewal — the certs expire every 90 days, but this command sets up a scheduled task to keep them fresh.

You can verify the cert is in place under **DSM → Control Panel → Security → Certificate**.

## Step 5: Reverse Proxy

Set up a reverse proxy rule in DSM to terminate HTTPS and forward to the Nextcloud container:

Go to **Control Panel → Login Portal → Advanced → Reverse Proxy** and create a new rule:

| Field | Value |
|---|---|
| Source protocol | HTTPS |
| Source hostname | `your-nas.your-tailnet.ts.net` |
| Source port | 443 |
| Destination protocol | HTTP |
| Destination hostname | `localhost` |
| Destination port | 8080 |

**Important:** Use `localhost` as the destination hostname, not the Tailscale IP. Both the reverse proxy and the Docker container are on the same machine, and `localhost` avoids routing quirks.

Then assign the Tailscale certificate to this rule: **Control Panel → Security → Certificate → Settings** — find your reverse proxy rule in the list and select the Tailscale cert from the dropdown.

## Step 6: Update Nextcloud config.php

Add the HTTPS overwrite settings so Nextcloud generates correct URLs:

```php
'trusted_domains' =>
array (
  0 => '100.x.y.z:8080',
  1 => 'your-nas.your-tailnet.ts.net',
),
'overwriteprotocol' => 'https',
'overwritehost' => 'your-nas.your-tailnet.ts.net',
```

## Step 7: Connect Your Devices

On every device — phone, laptop, desktop — just make sure Tailscale is running and point the Nextcloud client at `https://your-nas.your-tailnet.ts.net`. That's it. No DNS records, no port forwarding, no dynamic DNS.

- **GrapheneOS**: Tailscale runs as an always-on VPN with minimal battery impact.
- **NixOS**: `services.tailscale.enable = true;` in your config and it runs quietly in the background.

## The Result

A Nextcloud instance that:

- Has a valid HTTPS certificate with auto-renewal
- Is completely invisible to the public internet
- Requires zero open ports on your router
- Works seamlessly on all your devices with Tailscale always on
- Took about 30 minutes to set up

If your Nextcloud is just for personal or family use and everyone is already on your tailnet, this is a no-brainer. The only trade-off is that sharing files with people outside your tailnet gets more complicated — but for a private cloud, it's hard to beat.

### Post Written with AI 
