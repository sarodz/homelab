# Networking Basics
In the current setup I want all my servers to use pihole as their DNS server. For my latest server, I have manually set this up. Do not change the `/etc/resolv.conf` file as some people might recommend. To see the current DNS resolution use `resolvectl status`. If this is not pointing to the right IP address, change it by modifying `/etc/netplan/50-cloud-init.yaml` file and add the following in the right interface (Ex: `wlp3s0`):
```yaml
...
nameservers:
    addresses:
        - IP1
        - IP2
```
Once this is changed run `sudo netplan try` and try `resolvectl status`. If it is pointing to the right IP address, then you have succesfully changed your DNS server. You can further confirm this by running the following in two seperate terminals:
```bash
sudo tcpdump -ni interface -p port 53

dig google.com
```

[Source](https://unix.stackexchange.com/questions/750906/how-do-i-permanently-configure-the-dns-resolution-in-ubuntu-for-all-programs-lay)