# Vaultwarden (Bitwarden self-hosted) on Cloudflare Tunnel

Self-hosted Bitwarden instance (Vaultwarden) using Cloudflare Tunnel to expose the service

---

## Features

* Bitwarden self-hosted, with HAProxy as a reverse proxy
* Automatic HTTPS certificate, DNS record management Cloudflare Tunnel
* Blocking brute-force attempts with fail2ban and Cloudflare IP Rules
* Country-wide blocking through Cloudflare Firewall

## Pre-requisites

Before you start, ensure you have the following:

1. A VM/Compute Instance/VPS for hosting the service (External IP **not** required)
   - Docker and Docker Compose installed
   - Internet access/egress must be allowed
2. A Cloudflare-managed Domain. Any domain registrar will work, but Cloudflare must be managing the DNS records for the domain.

## Step 1: Pull and Configure Project

SSH into VPS and clone this repo:

```bash
git clone https://github.com/n0k0m3/vaultwarden_cloudflare
cd vaultwarden_cloudflare
```

### Configure Environmental Variables with `.env`

```bash
cp .env.example .env
```
I provide `.env.template` which should be copied to `.env` and filled out; filling it out is self-explanitory and requires certain values such as a domain name, Cloudflare API tokens, etc. 
Be aware that there is an optional backup section that allows you to have an encrypted backup regularly backed up and emailed or synced to cloud storage. More documentation on this feature is available [here](https://bradford.la/2020/self-host-bitwarden-on-google-cloud/#configure-bitwarden-backups-optional).

### Configure `fail2ban` (_optional_)

`fail2ban` stops brute-force attempts at your vault. To configure how long a ban is and how many attempts will trigger a ban, edit `fail2ban/jail.d/jail.local`:

```conf
bantime = 6h <- how long to enforce the ip ban
maxretry = 5  <- number of times to retry until a ban occurs
```

This will work out of the box - no `fail2ban` configuration is needed unless you want e-mail alerts of bans. To enable this, enter the SMTP settings in `.env`, and follow the instructions in `fail2ban/jail.d/jail.local` by uncommenting and entering `destemail` and `sender` and uncommenting the `action_mwl` action in the `vaultwaden` and `vaultwaden-admin` jails in the same file.

## Step 3: Start Services

To start up, use `docker-compose`:

```bash
docker network create cloudflared
docker-compose up
```

You can now use your browser to visit your new Bitwarden site. 
