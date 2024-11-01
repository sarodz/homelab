To use Cloud Tunnel:
* Zero Trust > Networks > Tunnels > Create a tunnel 
* Set the token created in Docker env as the env var (CLOUDFLARE_TUNNEL_TOKEN)
* In Public Hostname set the followng `web.domain.com` and `HTTP://web` (if using the web server in this docker compose file, or the actual local IP otherwise)
* Navigate to `web.domain.com` to access your local service

Docker Compose [from](https://github.com/jonas-merkle/container-cloudflare-tunnel/tree/master)
