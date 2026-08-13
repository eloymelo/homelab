# Local AI Stack, Ollama with Open WebUI and SearXNG

Ollama runs natively rather than in Docker, since it needs direct GPU
access. Open WebUI runs as a container and talks to it over the host
gateway.

## Ollama
```bash
curl -fsSL https://ollama.com/install.sh | sh
```

Ollama binds `127.0.0.1` by default, which makes it unreachable from any
container. This override is required:

```bash
sudo mkdir -p /etc/systemd/system/ollama.service.d
sudo nano /etc/systemd/system/ollama.service.d/override.conf
```

```ini
[Service]
Environment="OLLAMA_HOST=0.0.0.0:11434"
```

```bash
sudo systemctl daemon-reload
sudo systemctl restart ollama
sudo ss -tlnp | grep 11434     # must show *:11434, not 127.0.0.1
```

`systemctl edit ollama.service` has been observed to silently fail to
write this file. Creating `override.conf` by hand is more reliable.

## Open WebUI
```bash
docker run -d -p 3003:8080 --gpus all \
  --add-host=host.docker.internal:host-gateway \
  -v open-webui:/app/backend/data \
  --name open-webui --restart always \
  ghcr.io/open-webui/open-webui:cuda
```

Connect it under Settings, then Ollama API, using
`http://host.docker.internal:11434`. Verify from inside the container:

```bash
docker exec -it open-webui curl http://host.docker.internal:11434/api/tags
```

Behind the reverse proxy, Websockets Support must be on. Without it,
responses hang on "loading" forever and never stream, which looks like a
model problem rather than a proxy one.

## SearXNG as the search backend
See [the SearXNG service](../services/searxng/) for the compose file.
JSON output is off by default and Open WebUI needs it.

In Open WebUI, under Admin Settings, then Web Search:

- Engine: `searxng`
- Query URL: `http://host.docker.internal:8888/search?q=<query>&format=json`

## NVIDIA driver, Fedora
```bash
sudo dnf install akmod-nvidia xorg-x11-drv-nvidia \
  xorg-x11-drv-nvidia-cuda xorg-x11-drv-nvidia-cuda-libs \
  xorg-x11-drv-nvidia-libs xorg-x11-drv-nvidia-power \
  xorg-x11-drv-nvidia-xorg-libs nvidia-modprobe \
  nvidia-persistenced nvidia-settings -y
sudo akmods --force --rebuild
sudo reboot
nvidia-smi
```

A warning worth repeating: `dnf remove` on an NVIDIA-adjacent package can
cascade and take the entire driver stack with it as unused dependencies.
This has actually happened here, triggered by removing two packages that
looked unrelated. The symptom is
`libnvidia-ml.so.1: cannot open shared object file` when starting GPU
containers, plus `nvidia-smi` failing outright.

Always read the full package list before confirming a removal, and audit
afterwards:

```bash
sudo dnf history list
sudo dnf history info <ID>
```
