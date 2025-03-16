# Networking Basics
## Resolving the Right DNS
After moving to AdGuard, I do not need to manually update DNS server for each client in the network. Once they connect to the network AdGuard simply handles the rest.

## Useful Commands
You can use the following to trace your queries (They run in seperate terminals)
```bash
sudo tcpdump -ni interface -p port 53

dig google.com
```
`interface` can be `wlp3s0`, `wlp0s20f3`, ... . Whatever your machine is using.

## Useful Sources
[Configuring DNS](https://unix.stackexchange.com/questions/750906/how-do-i-permanently-configure-the-dns-resolution-in-ubuntu-for-all-programs-lay)