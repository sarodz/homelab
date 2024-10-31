# Source
Docker compose from [here](https://github.com/jonas-merkle/container-cloudflare-tunnel/tree/master)

# How to Use local Reverse Proxy with Cloudflare Tunnel
Steps:
------

1.  Install Cloudflare tunnel:
    
    1.  Install `cloudflared` from TrueCharts.
        
    2.  Go to [cloudflare team dash](https://dash.teams.cloudflare.com/) -> Access -> Tunnels -> Create a tunnel. Name isn't important
        
    3.  Copy the token from the `Install and run a connector` section. **Just** the token, save it somewhere safe.
        
    4.  In the `cloudflared` install menu, enter the token under `Tunnel Token`. Finish the installation.
        
2.  Install Nginx Proxy Manager and link the tunnel to it:
    
    1.  Install `nginx-proxy-manager` from the official community repo (haven't tested the TrueCharts one). Leave everything default.
        
    2.  In your Cloudflare tunnel configuration, go to Public Hostname -> Add a public hostname -> empty subdomain, domain = your domain name, empty path, service type = HTTP, URL = the address calculated in the previous step with `:30001` appended to it (that is the HTTP port of Nginx Proxy Manager).
        
    3.  Replicate the previous step, but for subdomain enter `*`. The rest is exactly the same.
        
    4.  Make sure that the public hostname without the `*` is on top (three dots -> move up).
        
    5.  While in the tunnel configuration, go to Private Network -> Add a private network -> `172.16.0.0/16`. This allows the tunnel to access the Kubernetes network (see the link mentioned in step 2.2 for more information).
        
    6.  Wildcard tunnels require special DNS configuration. Go to your Cloudflare DNS Records -> Add record -> Type = CNAME, Name = `*`, Target = `<your-tunnel-id-here>.cfargotunnel.com`, proxied = true. Your tunnel ID can be retrieved from the tunnels overview on the Cloudflare one dash dashboard.
        
    7.  Make sure a CNAME record for your domain name is present as well. If not, add it: Type = CNAME, Name = `<your-domain-name>`, target = same as previous step, proxied = true.
        
3.  Configure wildcard SSL certificate
    
    1.  Go to your Nginx Proxy Manager dashboard (TrueNAS dashboard -> Apps -> nginx-proxy-manager -> Web Portal). Default login is `admin@example.com` with `changeme` ([see these docs](https://nginxproxymanager.com/guide/#quick-setup)). Obviously you really need to change that, and it will make you.
        
    2.  Get a Cloudflare DNS API token for the next step. Go to [your profile](https://dash.cloudflare.com/profile) -> API Tokens -> Create Token. Use the 'Edit zone DNS' template. Under Zone Resources, set it to Include Specific zone your-domain-name. Leave the rest default and continue to create. Safe this token somewhere safe.
        
    3.  Go to SSL Certificates -> Add SSL Certificate -> Let's encrypt -> domain name = `*.yourdomain.com`. Enable `Use a DNS Challenge`, set DNS Provider to Cloudflare and leave propagation seconds default. Set the token to the token created in the previous step. Agree to the Let's Encrypt Terms of Service and press save. This might take a few minutes.
        
4.  You are now ready to add hosts! Keep the following things in mind:
    
    1.  Domain Names must be full length, so including your domain name (e.g. `hello.your-domain.name`).
        
    2.  Scheme must be HTTP.
                
    3.  To enable the SSL wildcard certificate, go to the SSL tab and under SSL Certificate select the existing one which starts with `*`.

[Source](https://gist.github.com/prateekrajgautam/75afbaa9bcda8eb1dfb6b5ceecd25e8c)
