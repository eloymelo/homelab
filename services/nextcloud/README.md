# Nextcloud All-in-One

Self-hosted file sync and collaboration. The mastercontainer manages the
other Nextcloud containers itself, so this compose file only defines the
one entry point.

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 8080 | TCP | Mastercontainer admin UI, HTTPS with a self-signed certificate |
| 11000 | TCP | Apache, the port the reverse proxy forwards to |

`APACHE_IP_BINDING=0.0.0.0` is what makes port 11000 reachable from the
reverse proxy container. `SKIP_DOMAIN_VALIDATION=true` is set because the
domain resolves to a LAN address internally, which AIO's own check
rejects.

## Usage
```bash
docker compose up -d
sudo firewall-cmd --permanent --add-port=8080/tcp   # Fedora
sudo firewall-cmd --reload
```

Then open the mastercontainer UI on 8080 and accept the self-signed
certificate.

## Ownership
The Nextcloud container runs as www-data, uid 33 and gid 0, not as your
normal user. This is a second exception to the PUID 1000 pattern, and it
has to be applied from inside the container, not from the host.

## Adding files manually
Copying files into the datadir is not enough, Nextcloud's database will
not know about them. All three steps are required:

```bash
# 1. put the files in place
sudo mkdir -p ~/nextcloud/ncdata/<user>/files/Example
sudo mv ~/staging/* ~/nextcloud/ncdata/<user>/files/Example/

# 2. fix ownership and permissions from inside the container
sudo docker exec nextcloud-aio-nextcloud chown -R 33:0 /mnt/ncdata/
sudo docker exec nextcloud-aio-nextcloud chmod -R 750 /mnt/ncdata/

# 3. rescan so the database picks them up
sudo docker exec --user www-data -it nextcloud-aio-nextcloud \
  php occ files:scan --all
```

Scanning a single user and path is faster than a full rescan:

```bash
sudo docker exec --user www-data nextcloud-aio-nextcloud \
  php occ files:scan <user> --path="Example" -v
```

## Other occ commands
```bash
sudo docker exec --user www-data -it nextcloud-aio-nextcloud php occ <command>
```

## Recovering from a failed install loop
If it repeats "The initial Nextcloud installation failed":

```bash
sudo rm ~/nextcloud/ncdata/install.failed
```

Then start the containers again from the AIO panel.

## Backup caveat
Only the datadir is covered by the restic backup, not the AIO
mastercontainer volume. After a rebuild you re-run the AIO wizard, point
it at the existing datadir, then run `files:scan --all`.

## Credentials
The generated database and admin passwords live inside the
mastercontainer:

```bash
docker exec nextcloud-aio-mastercontainer \
  cat /mnt/docker-aio-config/data/configuration.json
```

That file contains real credentials. Read it when you need to, never
copy its contents into this repo.
