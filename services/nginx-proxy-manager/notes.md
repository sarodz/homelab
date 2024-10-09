# Default user
Email:    admin@example.com
Password: changeme

# Steps To Follow
These will be used to setup the URLs for pihole and this proxy manager
1. Set up a wildcard SSL certificate for your domain. `*.domain.com` (will help with later steps)
2. Set up proxy host such that `Source` is the subdomain (`pihole.domain.com`) and Destination is the IP of the machine the container is running in and points to the relevant port. Don't forget to add the wildcard SSL certificate.

## Gotchas
* pihole needs to be routed to `/admin` route otherwise you will see a 403 error. In Edit, go to `Custom locations` and add a location such that you define it for `/` and add a custom command by clicking the gear and adding `rewrite ^/$ /admin break;`