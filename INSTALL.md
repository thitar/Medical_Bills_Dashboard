# Installation

## Prerequisites

- Docker and Docker Compose installed on your server
- A running Paperless-NGX instance
- A Paperless-NGX API token (found in **Settings → API Token** inside Paperless-NGX)

## Files

```
medical-bill-dashboard/
├── docker-compose.yml          # Runs nginx in a container on port 8020
├── nginx.conf                  # Serves the dashboard + proxies API calls to Paperless-NGX
├── html/
│   └── index.html              # The dashboard (copied from medical-bill-dashboard.html)
├── README.md                   # User documentation
└── INSTALL.md                  # This file
```

## Setup

### 1. Clone and prepare

```bash
git clone <your-repo-url> medical-bill-dashboard
cd medical-bill-dashboard
mkdir -p html
cp medical-bill-dashboard.html html/index.html
```

### 2. Configure the Paperless-NGX address

Edit `nginx.conf` and update the `proxy_pass` URL to point to your Paperless-NGX instance:

```nginx
location /api/ {
    proxy_pass  http://192.168.188.100:8000/api/;   # ← change this
    # ...
}
```

Use the **local/LAN IP** of your Paperless-NGX server. Using a domain name that goes through an external reverse proxy can cause DNS and SSL issues inside Docker containers.

If you must use an HTTPS domain, add these directives to the `location /api/` block:

```nginx
proxy_ssl_server_name  on;
proxy_set_header       Host your-domain.example.com;
```

### 3. Start

```bash
docker compose up -d
```

The dashboard is now available at `http://<your-server-ip>:8020`.

### 4. Create tags in Paperless-NGX

Create these tags in Paperless-NGX (exact names matter):

**Type tags:**
`type:classic`, `type:pid`, `type:non-cns`, `type:cns-report`

**Status tags:**
`status:cns-to-send`, `status:cns-pending`, `status:cns-reimbursed`, `status:dkv-to-send`, `status:dkv-pending`, `status:dkv-reimbursed`, `status:complete`

The dashboard's **Tag Reference** section (bottom of the page) shows which tags are missing.

## How it works

The dashboard is a single static HTML file served by nginx. It talks to the Paperless-NGX API to read documents and update tags. The nginx reverse proxy forwards all `/api/` requests to your Paperless-NGX instance, which avoids CORS issues entirely — the browser only ever talks to one origin.

```
Browser  ──→  nginx:8020
                ├── /           →  serves index.html (the dashboard)
                └── /api/*      →  proxies to Paperless-NGX
```

Credentials (URL and API token) are stored in the browser's localStorage so you don't need to re-enter them each time.

## Changing the port

Edit `docker-compose.yml` and change `8020:80` to your preferred port:

```yaml
ports:
  - "9090:80"   # now available on port 9090
```

## Docker DNS issues with Tailscale

If you run Tailscale on the Docker host, containers may fail to resolve external domain names. Fix this by setting explicit DNS for Docker in `/etc/docker/daemon.json`:

```json
{
  "dns": ["192.168.188.1", "1.1.1.1"]
}
```

Then restart Docker: `sudo systemctl restart docker`.

Alternatively, use the local IP address of Paperless-NGX in `nginx.conf` instead of a domain name to avoid DNS resolution entirely.
