# Steps To Follow
1. Setup A Record (DNS Record) to map one of your subdomains. For example: `pihole.domain.com` -> IP of the machine the container is running at
2. Setup CNAME Record to map other subdomains to the A Record. For example: `proxy.domain.com` -> `pihole.domain.com` (They both are running at the same machine at the end of the day) 

## Gotchas
* Need to turn off IP6 for local DNS to take hold
* Consider flushing your DNS cache by `ipconfig /flushdns`

## Reference
 -  [1](https://roadtohomelab.blog/local-dns-for-docker-containers-using-pi-hole-portainer-nginx-proxy-manager/)
