# Docker Install

## Fedora
```bash
sudo dnf install dnf-plugins-core -y
sudo dnf config-manager --add-repo \
  https://download.docker.com/linux/fedora/docker-ce.repo
sudo dnf install docker-ce docker-ce-cli containerd.io \
  docker-buildx-plugin docker-compose-plugin -y
sudo systemctl enable --now docker
sudo usermod -aG docker $USER
```

## Debian
```bash
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker $USER
```

## After either
Log out and back in, or run `newgrp docker`, then confirm the group
change took effect:

```bash
docker ps
```

If that still needs `sudo`, the group membership has not applied to your
current session yet.
