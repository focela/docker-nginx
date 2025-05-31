# Docker Nginx

[![GitHub release](https://img.shields.io/github/v/tag/focela/docker-nginx?style=flat-square)](https://github.com/focela/docker-nginx/releases/latest)
[![Build Status](https://img.shields.io/github/actions/workflow/status/focela/docker-nginx/main.yml?branch=main&style=flat-square)](https://github.com/focela/docker-nginx/actions)
[![Docker Stars](https://img.shields.io/docker/stars/focela/nginx.svg?style=flat-square&logo=docker)](https://hub.docker.com/r/focela/nginx/)
[![Docker Pulls](https://img.shields.io/docker/pulls/focela/nginx.svg?style=flat-square&logo=docker)](https://hub.docker.com/r/focela/nginx/)
[![Become a sponsor](https://img.shields.io/badge/sponsor-focela-181717.svg?logo=github&style=flat-square)](https://github.com/sponsors/focela)
[![Paypal Donate](https://img.shields.io/badge/donate-paypal-00457c.svg?logo=paypal&style=flat-square)](https://www.paypal.me/focela)

## Overview

A production-ready Docker image for [Nginx](https://www.nginx.org) web server with enhanced features, security, and monitoring capabilities.

### Key Features

- **Mainline Release Tracking** - Always up-to-date with the latest stable Nginx version
- **Enhanced Security** - Includes [Nginx Ultimate Bad Bot Blocker](https://github.com/mitchellkrogza/nginx-ultimate-bad-bot-blocker) and DDoS protection
- **Multiple Authentication Methods** - Support for Basic Auth, LDAP, and LemonLDAP:NG
- **Advanced Compression** - Built-in Brotli and Gzip compression
- **Performance Optimized** - Configurable caching, worker processes, and connection handling
- **Built-in Monitoring** - Nginx status endpoint on port 73
- **Automated Log Management** - Logrotate configured for daily rotation with 7-day retention
- **Multi-Architecture Support** - Available for `amd64`, `arm64`, and `arm/v7`

## Quick Start

### Using Docker Compose (Recommended)

```yaml
version: '3.8'
services:
  nginx:
    image: focela/nginx:latest
    ports:
      - "80:80"
    volumes:
      - ./html:/www/html
      - ./logs:/www/logs
    environment:
      - NGINX_WEBROOT=/www/html
      - NGINX_ENABLE_COMPRESSION_BROTLI=TRUE
    restart: unless-stopped
```

### Using Docker CLI

```bash
docker run -d \
  --name nginx \
  -p 80:80 \
  -v $(pwd)/html:/www/html \
  -v $(pwd)/logs:/www/logs \
  focela/nginx:latest
```

## Installation

### Pull from Docker Hub

```bash
docker pull focela/nginx:latest
```

### Pull from GitHub Container Registry

```bash
docker pull ghcr.io/focela/docker-nginx:latest
```

### Available Tags

| Alpine Base | Tag | Debian Base | Tag |
|-------------|-----|-------------|-----|
| Latest | `:latest` | Latest | `:debian` |
| Latest | `:alpine` | Bookworm | `:debian-bookworm` |
| Edge | `:alpine-edge` | Bullseye | `:debian-bullseye` |
| 3.21 | `:alpine-3.21` | Buster | `:debian-buster` |

### Build from Source

```bash
git clone https://github.com/focela/docker-nginx
cd docker-nginx
docker build -t focela/nginx:custom .
```

## Configuration

### Basic Site Configuration

Create your site configuration in `/etc/nginx/sites.available/` with a `.conf` extension:

```nginx
server {
    listen {{NGINX_LISTEN_PORT}};
    server_name localhost;
    root {{NGINX_WEBROOT}};
    
    index index.html index.htm;
    
    location / {
        # Your custom directives here
    }
    
    # Security and optimization includes
    include /etc/nginx/snippets/site_optimization.conf;
    include /etc/nginx/snippets/exploit_protection.conf;
}
```

### Persistent Storage

| Directory | Description |
|-----------|-------------|
| `/www/html` | Web content directory |
| `/www/logs` | Nginx access and error logs |

### Environment Variables

<details>
<summary><b>Core Configuration</b></summary>

| Variable | Description | Default |
|----------|-------------|---------|
| `NGINX_WEBROOT` | Document root directory | `/www/html` |
| `NGINX_LISTEN_PORT` | HTTP listening port | `80` |
| `NGINX_USER` | Nginx process user | `nginx` |
| `NGINX_GROUP` | Nginx process group | `www-data` |
| `NGINX_MODE` | Operation mode: `NORMAL`, `MAINTENANCE`, `PROXY`, `REDIRECT` | `NORMAL` |

</details>

<details>
<summary><b>Authentication Options</b></summary>

| Variable | Description | Default |
|----------|-------------|---------|
| `NGINX_AUTHENTICATION_TYPE` | Authentication type: `NONE`, `BASIC`, `LDAP`, `LLNG` | `NONE` |
| `NGINX_AUTHENTICATION_TITLE` | Login prompt message | `Please login` |
| `NGINX_AUTHENTICATION_BASIC_USER1` | Basic auth username | `admin` |
| `NGINX_AUTHENTICATION_BASIC_PASS1` | Basic auth password | `password` |

For LDAP configuration options, see the [full documentation](docs/AUTHENTICATION.md).

</details>

<details>
<summary><b>Bot Protection</b></summary>

| Variable | Description | Default |
|----------|-------------|---------|
| `NGINX_ENABLE_BLOCK_BOTS` | Enable bot blocking | `FALSE` |
| `NGINX_BLOCK_BOTS` | Bots to block (comma-separated) | - |
| `NGINX_BLOCK_BOTS_WHITELIST_IP` | Whitelisted IPs | `127.0.0.1,10.0.0.0/8,172.16.0.0/12,192.168.0.0/24` |

</details>

<details>
<summary><b>Logging Configuration</b></summary>

| Variable | Description | Default |
|----------|-------------|---------|
| `NGINX_LOG_ACCESS_LOCATION` | Access log directory | `/www/logs/nginx` |
| `NGINX_LOG_ACCESS_FILE` | Access log filename | `access.log` |
| `NGINX_LOG_ERROR_FILE` | Error log filename | `error.log` |
| `NGINX_LOG_LEVEL_ERROR` | Error log verbosity | `warn` |

</details>

<details>
<summary><b>Performance Tuning</b></summary>

| Variable | Description | Default |
|----------|-------------|---------|
| `NGINX_WORKER_PROCESSES` | Number of worker processes | `auto` |
| `NGINX_WORKER_CONNECTIONS` | Max connections per worker | `1024` |
| `NGINX_KEEPALIVE_TIMEOUT` | Keep-alive timeout (seconds) | `75` |
| `NGINX_CLIENT_BODY_BUFFER_SIZE` | Client body buffer size | `16k` |

</details>

## Maintenance

### Shell Access

```bash
docker exec -it nginx bash
```

### Maintenance Mode

Enable maintenance mode temporarily:

```bash
docker exec nginx maintenance ON
docker exec nginx maintenance SLEEP 300  # 5 minutes
docker exec nginx maintenance OFF
```

### Log Rotation

Logs are automatically rotated daily at 23:59 and retained for 7 days with compression.

## Documentation

- [Advanced Configuration Guide](docs/CONFIGURATION.md)
- [Authentication Setup](docs/AUTHENTICATION.md)
- [Performance Optimization](docs/PERFORMANCE.md)
- [Troubleshooting Guide](docs/TROUBLESHOOTING.md)

## Contributing

Contributions are welcome! Please read our [Contributing Guidelines](CONTRIBUTING.md) before submitting PRs.

## Support

- **Community Support**: Use the [Discussions](https://github.com/focela/docker-nginx/discussions) board
- **Bug Reports**: Submit via [Issues](https://github.com/focela/docker-nginx/issues)
- **Professional Support**: [Sponsor](https://focela.com/sponsor) for priority support

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- [Nginx](https://nginx.org/) - The amazing web server
- [Mitchell Krog](https://github.com/mitchellkrogza/nginx-ultimate-bad-bot-blocker) - Bad Bot Blocker
- All contributors and supporters

---

**Maintained by** [Focela Labs](https://www.focela.com)