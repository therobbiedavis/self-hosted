# My Self-Hosted Server Configuration
## Personal Anecdote

This setup is running the latest Ubuntu LTS (24.04 (Noble Numbat)) on a dedicated server from the Hetzner auction market ([Hetzner Server Auction](https://www.hetzner.com/sb/) for around $35 USD/month as of 2025). The current machine is an Intel Core i9-9900K with 2x 1TB NVMe SSDs and 64GB DDR4 RAM (4x 16GB sticks). The server is located in Germany and offers unmetered bandwidth, which is perfect for hosting media services, cloud storage, and remote access for friends and family.

This server was chosen specifically for the value and performance of the i9-9900K which has Intel Quick Sync video decoding capabilities. With built-in support for h265 (HEVC) and AV1, hardware transcoding is trivial, and a dedicated graphics card is unnecessary for most media workloads. This makes the setup efficient, cost-effective, and ideal for streaming high-quality content to multiple users.

This hardware allows dozens of containers to run simultaneously, including Plex, Jellyfin, Emby, and various automation tools. The NVMe drives allows for no IOPS bottleneck (storage device can't keep up with read/write requests) and provide fast access for transcoding and downloads, while the generous RAM ensures smooth multitasking and caching. Hetzner's auction servers are a great value for anyone looking to self-host with serious performance and reliability.

## Overview

This repository is a cleaned backup of my media server setup. It includes:

- Docker Compose files for various services (`docker-compose/`)
- Initial setup script (`first-install`)
- Systemd service files (`etc/systemd/system/`)
- Configuration templates and structure

**Note:** You must supply your own configuration files for rclone servers and any sensitive credentials. This repo does not include personal secrets or API keys.

## Common Environment Variables

Most containers use the following environment variables, which should be set in your `.env` file:

- `PUID` and `PGID`: User and group IDs for file permissions. These are set automatically by the `first-install` script to match the `server` user.
- `TZ`: Timezone for containers (e.g., `America/New_York`).
- `WATCHTOWER_ENABLED`: Controls whether Watchtower auto-updates are enabled for containers. Set to `true` to allow updates.

Copy `.env.example` to `.env` and fill out all required values. The `first-install` script will update `PUID` and `PGID` for you.

### Example

```env
PUID=1000
PGID=1000
TZ=America/New_York
WATCHTOWER_ENABLED=true
```

These variables are referenced in Docker Compose files using `${VAR_NAME}` syntax.

## Usage Instructions

1. **Clone the repository:**
	```bash
	git clone https://github.com/therobbiedavis/self-hosted.git
	cd self-hosted
	```

2. **Review and update configuration files:**
	- Edit the Docker Compose files in `docker-compose/` to match your environment.
	- Supply your own rclone configuration files and credentials.
	- Update any service files in `etc/systemd/system/` as needed for your system.

3. **Run the initial setup script (if needed):**
	```bash
	./first-install
	```

4. **Start services:**
	- Use Docker Compose to start containers:
	  ```bash
	  docker-compose -f docker-compose/arrs.yml up -d
	  # Repeat for other compose files as needed
	  ```
	- Enable and start systemd services:
	  ```bash
	  sudo systemctl enable rclone-drive.service
	  sudo systemctl start rclone-drive.service
	  # Repeat for other services as needed
	  ```

## Customization

- This setup is intended as a template. You should customize all configuration files to suit your own media server requirements.
- Sensitive information (API keys, passwords, rclone configs) must be provided by you and are not included in this repo.


## Common Docker Compose Tasks

Here are some common tasks for managing containers with Docker Compose:


### Updating Container Images

To update all images defined in a compose file and restart containers with the latest versions:

```bash
docker-compose -f docker-compose/arrs.yml pull
docker-compose -f docker-compose/arrs.yml up -d
```

This will pull the latest images and recreate containers as needed.


To update a single container image from a specific compose file (e.g., `sonarr` in `arrs.yml`):

```bash
docker-compose -f docker-compose/arrs.yml pull sonarr
docker-compose -f docker-compose/arrs.yml up -d sonarr
```

This will only update and restart the specified container from the given compose file.

### Restarting Containers

To restart all containers defined in a compose file:

```bash
docker-compose -f docker-compose/arrs.yml restart
```

### Stopping and Removing Containers

To stop and remove all containers, networks, and volumes defined in a compose file:

```bash
docker-compose -f docker-compose/arrs.yml down
```

### Viewing Logs

To view logs for all containers in a compose file:

```bash
docker-compose -f docker-compose/arrs.yml logs
```

## Gluetun VPN Setup Guide

Gluetun is used to route container traffic through a VPN. This means you will need a VPN plan with a supported provider to use Gluetun. This guide covers setting up WireGuard with Torguard and using the built-in AirVPN provider.

### WireGuard with Torguard

1. **Get your WireGuard config from Torguard:**
	- Log in to your Torguard account and generate a WireGuard config for your desired server/location.
	- Download the config file (usually named `wg0.conf`).

2. **Place the config file:**
	- Save your WireGuard config in a directory accessible to the Gluetun container, e.g. `/root/Server/.config/gluetun/wg0.conf`.

3. **Set environment variables in your `.env` file:**
	```env
	VPN_SERVICE_PROVIDER=custom
	VPN_TYPE=wireguard
	TZ=America/New_York
	WIREGUARD_PRIVATE_KEY=<your_private_key>
	WIREGUARD_ADDRESSES=<your_wireguard_address>
	WIREGUARD_PRESHARED_KEY=<your_preshared_key> # if provided
	OPENVPN_CUSTOM_CONFIG=
	FIREWALL_VPN_INPUT_PORTS=32400,3001,8090,9696
	FIREWALL_INPUT_PORTS=32400,3001,8090,9696
	```
	- The private key, address, and preshared key can be found in your WireGuard config file.

4. **Update your Docker Compose file:**
	- Mount the config file as a volume:
	  ```yaml
	  volumes:
		 - /root/Server/.config/gluetun/:/gluetun/
	  ```
	- Set the environment variables as shown above.

5. **Start the container:**
	```bash
	docker-compose -f docker-compose/proxy.yml up -d gluetun (or whatever the container is called)
	```

### Built-in AirVPN Provider

1. **Set environment variables in your `.env` file:**
	```env
	VPN_SERVICE_PROVIDER=airvpn
	VPN_TYPE=wireguard
	TZ=America/New_York
	FIREWALL_VPN_INPUT_PORTS=9191,6881,43413,5030,50300,3100,9696
	FIREWALL_INPUT_PORTS=9191,6881,43413,5030,50300,3100,9696
	WIREGUARD_PRIVATE_KEY=<your_private_key>
	WIREGUARD_PRESHARED_KEY=<your_preshared_key>
	WIREGUARD_ADDRESSES=<your_wireguard_address>
	SERVER_COUNTRIES=<country_code> # e.g. US, CA, NL
	```
	- You can get your WireGuard credentials from AirVPN's client area.

2. **Update your Docker Compose file:**
	- No custom config file is needed; Gluetun will use the built-in provider.

3. **Start the container:**
	```bash
	docker-compose -f docker-compose/proxy.yml up -d gluetun (or whatever the container is called)
	```

**Tip:**
- For both setups, ensure your firewall and port forwarding settings match your use case.
- For more details, see the [Gluetun documentation](https://github.com/qdm12/gluetun/wiki).

Refer to the [Docker Compose documentation](https://docs.docker.com/compose/) for more details and advanced usage.

## Support

This repo is not actively maintained for public use, but feel free to use it as a reference or starting point for your own media server setup.

## Documentation Links

Below are documentation links for the main services and applications used in this setup:

### Media Management & Downloaders
- [Sonarr](https://sonarr.tv/docs/)
- [Radarr](https://radarr.video/docs/)
- [Lidarr](https://lidarr.audio/docs/)
- [Readarr](https://wiki.servarr.com/Readarr)
- [Bazarr](https://wiki.bazarr.media/)
- [Prowlarr](https://wiki.servarr.com/Prowlarr)
- [Mylar3](https://github.com/mylar3/mylar3/wiki)
- [Soulseek (slskd)](https://github.com/slskd/slskd)
- [qBittorrent](https://github.com/qbittorrent/qBittorrent/wiki)
- [SABnzbd](https://sabnzbd.org/wiki/)
- [JDownloader](https://support.jdownloader.org/)
- [Kometa (Plex Meta Manager)](https://kometa.wiki/en/latest/)
- [Picard](https://picard-docs.musicbrainz.org/en/index.html)
- [Overseerr](https://docs.overseerr.dev/)
- [Requestrr](https://github.com/hotio/requestrr)
- [Kavita](https://www.kavitareader.com/docs/)
- [Emby](https://github.com/MediaBrowser/Emby/wiki)
- [Audiobookshelf](https://www.audiobookshelf.org/docs/)
- [Plex](https://support.plex.tv/)
- [Ghost](https://ghost.org/docs/)
- [Jellyfin](https://jellyfin.org/docs/)
- [Romm](https://docs.romm.app/)
- [Romm-Comm](https://github.com/idiosync000/romm-comm)
- [Navidrome](https://www.navidrome.org/docs/)
- [Portainer](https://docs.portainer.io/)
- [Podfetch](https://github.com/samuel19982/podfetch)
- [Homebox](https://docs.homebox.app/)
- [MySQL](https://dev.mysql.com/doc/)
- [MariaDB](https://mariadb.com/kb/en/documentation/)

### Automation & Monitoring
- [Watchtower](https://containrrr.dev/watchtower/)
- [Autoscan](https://github.com/Cloudbox/autoscan)
- [Tautulli](https://tautulli.com/)
- [Uptime Kuma](https://github.com/louislam/uptime-kuma)
- [Notifiarr](https://docs.notifiarr.com/)
- [Wrapperr](https://github.com/aunefyren/wrapperr)
- [Homepage](https://gethomepage.dev/docs/)
- [Pattrmm](https://github.com/insertdisc/pattrmm)

### Proxy, VPN, and Security
- [SWAG (Secure Web Application Gateway)](https://docs.linuxserver.io/images/docker-swag)
- [Gluetun (VPN)](https://github.com/qdm12/gluetun/wiki)
- [AirVPN](https://airvpn.org/forums/forum/3-english/)
- [Torguard](https://torguard.net/support/)
- [Firefox (LinuxServer)](https://docs.linuxserver.io/images/docker-firefox)

### Authentication & Identity
- [Authentik](https://goauthentik.io/docs/)
- [PostgreSQL](https://www.postgresql.org/docs/)
- [Redis](https://redis.io/docs/)

### Other Services
- [Bluesky PDS](https://github.com/bluesky-social/pds)
- [Duplicati](https://duplicati.readthedocs.io/en/latest/)
- [Yamtrack](https://github.com/fuzzygrim/yamtrack)
- [OpenAI Whisper ASR](https://github.com/oneRahmet/openai-whisper-asr-webservice)
- [Speedtest](https://github.com/robinmanuelthiel/speedtest)
- [Autoscan Adapter](https://github.com/dantebarba/autoscan-adapter)
- [Wizarr](https://docs.wizarr.io/)
- [PlexTraktSync](https://github.com/Taxel/PlexTraktSync)