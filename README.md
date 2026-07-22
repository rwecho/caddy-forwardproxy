# caddy-forwardproxy

Caddy with [klzgrad/forwardproxy@naive](https://github.com/klzgrad/forwardproxy/tree/naive) plugin — NaïveProxy padding layer integrated into Caddy forward proxy.

## Usage

### Docker (recommended)

```bash
docker pull ghcr.io/<your-username>/caddy-forwardproxy:latest
```

Or build locally:

```bash
docker build -t caddy-forwardproxy .
```

### docker-compose

```bash
docker compose up -d
```

Edit `Caddyfile` with your domain and credentials before starting.

### Build with xcaddy

```bash
go install github.com/caddyserver/xcaddy/cmd/xcaddy@latest
xcaddy build --with github.com/caddyserver/forwardproxy=github.com/klzgrad/forwardproxy@naive
```

## Caddyfile Example

```caddyfile
{
  order forward_proxy before file_server
  log {
    exclude http.log.error
  }
}
:443, example.com {
  tls me@example.com
  encode
  forward_proxy {
    basic_auth user pass
    hide_ip
    hide_via
    probe_resistance
  }
  file_server {
    root /var/www/html
  }
}
```

## GitHub Actions

On push to `main` or tag `v*.*.*`, the CI workflow builds multi-arch images (linux/amd64, linux/arm64) and pushes to:
- Docker Hub (`${{ secrets.DOCKER_USERNAME }}/caddy-forwardproxy`)
- GitHub Container Registry (`ghcr.io/<owner>/caddy-forwardproxy`)

Set the following **repository secrets**:
- `DOCKER_USERNAME` — Docker Hub username
- `DOCKER_PASSWORD` — Docker Hub password/token
