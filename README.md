# Traefik Proxy Application Configuration Guide

## Quick Start: Adding Your Application to Traefik

To configure your application to work with Traefik proxy, add these essential elements to your `docker-compose.yml`:

### Basic Configuration

```yaml
services:
  your_app:
    image: your_image:latest
    # ... other configurations ...

    labels:
      - traefik.enable=true
      - traefik.http.routers.your_app.rule=Host(`yourdomain.com`)
      - traefik.http.routers.your_app.entrypoints=web
      - traefik.http.services.your_app.loadbalancer.server.port=8080 (internal port)

    networks:
      - traefik_net

networks:
  traefik_net:
    external: true
```
### Label Breakdown

1. **`traefik.enable=true`**
    - Enables Traefik discovery for this container
2. **`traefik.http.routers.your_app.rule=Host('yourdomain.com')`**
    - Defines the routing rule (which domain/path triggers this service)
    - Replace `your_app` with a unique router name
    - Replace `yourdomain.com` with your actual domain
3. **`traefik.http.routers.your_app.entrypoints=web`**
    - Specifies which Traefik entrypoint to use (usually `web` for HTTP)
4. **`traefik.http.services.your_app.loadbalancer.server.port=8080`**
    - The internal port your application listens on inside the container
    - Replace `8080` with your app's actual port

### Complete Example

```yaml
version: '3.8'

services:
  my_nodejs_app:
    build: .
    container_name: my_nodejs_app

    # Optional: expose port for direct access (debugging)
    ports:
      - "3001:3000"

    environment:
      - NODE_ENV=production

    labels:
      # Enable Traefik
      - traefik.enable=true

      # Router configuration
      - traefik.http.routers.my_nodejs_app.rule=Host(`api.example.com`)
      - traefik.http.routers.my_nodejs_app.entrypoints=web

      # Service configuration (internal port)
      - traefik.http.services.my_nodejs_app.loadbalancer.server.port=3000

    networks:
      - traefik_net

    restart: unless-stopped

networks:
  traefik_net:
    external: true
```

### Create treffik network

```
docker network create traefik_net
```
