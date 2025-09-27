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

## Support

This repo is not actively maintained for public use, but feel free to use it as a reference or starting point for your own media server setup.