# My Self-Hosted Server Configuration

## Overview

This repository is a cleaned backup of my media server setup. It includes:

- Docker Compose files for various services (`docker-compose/`)
- Initial setup script (`first-install`)
- Systemd service files (`etc/systemd/system/`)
- Configuration templates and structure

**Note:** You must supply your own configuration files for rclone servers and any sensitive credentials. This repo does not include personal secrets or API keys.

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