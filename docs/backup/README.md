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
```
curl -LO https://raw.githubusercontent.com/creativeprojects/resticprofile/master/install.sh
chmod +x install.sh
sudo ./install.sh -b /usr/local/bin
```

4. (Optional, can skip this step) I have used the following to setup the repository (as I was not aware of `resticprofile` at the time and [how it can achieve the same](https://creativeprojects.github.io/resticprofile/configuration/getting_started/index.html#initialize-your-repository)) Repository is `restic`'s destination location for storing data.
```
sudo rclone mount ms:backup /mnt/onedrive/backup
sudo mkdir /mnt/onedrive/backup/test
sudo restic -r /mnt/onedrive/backup/test init
```

5. Setup `resticprofile` by renaming the latest version of the configuration file in this repo (Ex:`v0.yaml`) as `profiles.yaml` and creating a `password.txt` containing only the restic profile password place them at `/usr/local/etc/resticprofile`.
If you have skipped step 4, you can use the following to initialize repositories
```
sudo resticprofile --name full-backup init
```

6. Now we can schedule the backups by running `sudo resticprofile --name full-backup schedule` and that's it!

## How to Recover
1. Find the snapshot id you want to recover (or simply use `latest` if you want the latest snapshot)
```
sudo restic -r rclone:ms:backup/test snapshots
```
Or
```
sudo resticprofile backup snapshots
```

2. Use that id retrieved in the last step to restore your data
```
sudo restic -r rclone:ms:backup/test --verbose restore <SNAPSHOT-ID> --target <PATH-TO-RESTORE>
```
Or
```
sudo resticprofile backup restore <SNAPSHOT-ID> --target <PATH-TO-RESTORE>
```