# Networking Basics
## Resolving the Right DNS
In the current setup I want all my servers to use pihole as their DNS server. For my latest server, I have manually set this up. Do not change the `/etc/resolv.conf` file as other services can modify this file. To see the current DNS resolution use `resolvectl status`. If this is not pointing to the right IP address, you can do one of the two to solve this issue.

### Solution 1: Change `netplan` Config
Modify the `/etc/netplan/50-cloud-init.yaml` file and add the following in the right interface (Ex: `wlp3s0`):
```yaml
...
nameservers:
    addresses:
        - IP1
        - IP2
```
Once this is changed run `sudo netplan try` and try `resolvectl status`. If it is pointing to the right IP address, then you have succesfully changed your DNS server. 

[Source](https://unix.stackexchange.com/questions/750906/how-do-i-permanently-configure-the-dns-resolution-in-ubuntu-for-all-programs-lay)

### Solution 2: Update `resolvectl` Directly
Run the following command with the right IP addresses:
```bash
resolvectl dns INTERFACE IP1 IP2
```
`INTERFACE` can be `wlp3s0`, `wlp0s20f3`, ...

### Confirm Changes
You can confirm your changes by running the following in two seperate terminals:
```bash
sudo tcpdump -ni interface -p port 53

dig google.com
```
