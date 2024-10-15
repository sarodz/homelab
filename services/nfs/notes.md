Expected Env vars with sample value:
- PERMITTED_IPS: "192.168.2.*"


Run following in the client machine to persist the mount (from Source 1):
```
sudo apt install nfs-common
sudo mkdir /mnt/nfs
sudo chmod -R 777 /mnt/nfs
```
You can change the destination after IP to mount to a specifc folder you want to mount to
```
sudo mount.nfs4 -v 1.2.3.4:/ /mnt/nfs
```
add below in /etc/fstab to always mount, otherwise it will unmount in restart

```
1.2.3.4:/   /mnt/nfs nfs     rw,auto,auto,nolock,intr,tcp,actimeo=1800       0       0
```
Sources:
- [1](https://github.com/sinawic/4yt/tree/master/nfs-docker)
- [2](https://github.com/sjiveson/nfs-server-alpine/blob/master/README.md)