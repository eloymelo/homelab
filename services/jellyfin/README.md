# Jellyfin

Media server for movies and TV.

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 8096 | TCP | Web UI |

## Usage
```bash
docker compose up -d
```

## NVIDIA GPU passthrough
The `runtime: nvidia` line and the two `NVIDIA_*` variables are required
for hardware transcoding. They are not in Jellyfin's stock compose file.
Install the NVIDIA Container Toolkit on the host first, otherwise the
container will not start with this runtime set.

```bash
sudo nvidia-ctk runtime configure --runtime=docker
sudo systemctl restart docker
docker exec jellyfin nvidia-smi     # should list the GPU
```

Without passthrough, transcoding fails with `Cannot load libcuda.so.1`,
but only for files that actually need transcoding. Anything the client
can direct play still works, which makes a missing GPU config look like
a random per-file problem rather than a configuration gap.

If you are on a host with no NVIDIA GPU, remove the `runtime` line and
the two `NVIDIA_*` variables.

## Folders that are not in backup
Only `config` is worth backing up. After a restore, recreate the rest
before starting the container, or Jellyfin crash-loops with
`Access to the path '/cache/.jellyfin-cache' is denied`:

```bash
mkdir -p ~/jellyfin/{cache,movies,tv-shows}
sudo chown -R $USER:$USER ~/jellyfin
```

## Folder naming
Jellyfin matches metadata from the folder name, not the file name:

```
Movie Name (Year)/
    Movie Name (Year).mkv
```

Let Radarr and Sonarr handle renaming and moving. Raw release names
moved in by hand will not match correctly.

## Transcoding notes
Some 10-bit HEVC sources cannot be encoded to H.264 on certain
GPU and driver combinations, reported as `10 bit encode not supported`.
Switching the target to `hevc_nvenc` is the fix. In the Jellyfin UI,
under Playback and Transcoding, confirm that HEVC 10-bit hardware
decoding and HEVC encoding are both enabled.
