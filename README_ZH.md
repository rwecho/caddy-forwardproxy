# caddy-forwardproxy

带 [klzgrad/forwardproxy@naive](https://github.com/klzgrad/forwardproxy/tree/naive) 插件的 Caddy 构建 — 将 NaïveProxy 填充层集成到 Caddy 正向代理中。

## 使用

### Docker（推荐）

```bash
docker pull ghcr.io/<你的用户名>/caddy-forwardproxy:latest
```

或本地构建：

```bash
docker build -t caddy-forwardproxy .
```

### docker-compose

```bash
docker compose up -d
```

启动前请编辑 `Caddyfile`，填入你的域名和认证信息。

### 使用 xcaddy 构建

```bash
go install github.com/caddyserver/xcaddy/cmd/xcaddy@latest
xcaddy build --with github.com/caddyserver/forwardproxy=github.com/klzgrad/forwardproxy@naive
```

## Caddyfile 示例

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

推送到 `main` 分支或 `v*.*.*` 标签时，CI 工作流会构建多架构镜像（linux/amd64, linux/arm64）并推送到：
- Docker Hub（`${{ secrets.DOCKER_USERNAME }}/caddy-forwardproxy`）
- GitHub Container Registry（`ghcr.io/<仓库所有者>/caddy-forwardproxy`）

需要在仓库中设置以下 **Secrets**：
- `DOCKER_USERNAME` — Docker Hub 用户名
- `DOCKER_PASSWORD` — Docker Hub 密码或令牌
