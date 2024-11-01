# Backing Up Services
## Why I Chose to Backup at the Host Level
We might need access to all of the filesystem at the hardware the services are running when backing up. If we create a backup service using a container and give access to all of the data in other containers, I believe it undermines the whole reason to use containers. I am not coming from a feasibility view (because [it can be done](https://www.costin.rocks/blog/posts/automate-and-optimize-rclone-in-docker)) but from best principles. I have reviewed other solutions that [create periodic ISO images](https://nerd-tech.net/2022/09/08/how-to-make-a-live-backup-of-your-raspberry-pi-ubuntu-raspberry-pi-os-server-to-create-live-bootable-iso-images-on-an-external-drive/) but I felt that solution could be an overkill (even though it will provide better recovery times). I wanted to use an open source backup tool with active development that is easy to use and provide backup at no cost (I already have an OneDrive subscription so why get a new storage space for my initial backup implementation?). I ran into this [post](https://alikhallad.com/incremental-backups-with-rclone-restic/) showing how easy it is to use `restic`. After experimenting with it, I am happy to say it met my goals. 

> [!IMPORTANT]
> My current setup does not expose the host to the internet and the below setup can be a security issue for your OneDrive storage if not handled with care.
> In a more mature implementation replace the OneDrive storage with a storage only used for backing up the services. 

## Tech Stack
* `rclone`
* `restic`
* `resticprofile`
* `OneDrive`
* `nfs`

## How to Setup
1. Download `rclone` and config using `rclone config`. You need setup `rclone` in a headless machine but you need to generate the service specific configs on a machine that has a browser. I have named my OneDrive remote as `ms`.

2. Setup a NFS server as outlined using the `docker-compose.yml` in the `services/nfs` folder.

3. Download `restic` and `resticprofile`. I have used the following command to download the `resticprofile` (it doesn't download `restic`)
```bash
curl -LO https://raw.githubusercontent.com/creativeprojects/resticprofile/master/install.sh
chmod +x install.sh
sudo ./install.sh -b /usr/local/bin
```

4. Setup `resticprofile` by renaming the latest version of the configuration file in this repo (Ex:`v0.yaml`) as `profiles.yaml` and creating a `password.txt` containing only the restic profile password place them at `/usr/local/etc/resticprofile`.
```bash
sudo resticprofile --name full-backup init
```
Note: You can also create the password file using the following command
```bash
sudo bash -c "resticprofile generate --random-key > /usr/local/etc/resticprofile/FILE_NAME.txt"
```

5. Now we can schedule the backups by running `sudo resticprofile --name full-backup schedule` and that's it!
> [!NOTE]
> Seems like when we use inheritence, calling the full-backup group causes an error for v1 format of the profile. Hence I needed call each group seperatly.

## How to Recover
1. Find the snapshot id you want to recover (or simply use `latest` if you want the latest snapshot)
```bash
sudo resticprofile --name profile-name snapshots
```

2. Use that id retrieved in the last step to restore your data
```bash
sudo resticprofile --name profile-name restore <SNAPSHOT-ID> --target <PATH-TO-RESTORE>
```

### Gocthas
* If you are going to change the profile and want to unschedule the current restic profile(s), you should not remove the profile file until you unschedule the backup(s). Otherwise you might have to identify the running process [and remove them.](https://superuser.com/questions/513159/how-to-remove-systemd-services)
```bash
systemctl list-unit-files | grep restic
```
