# Prowlarr

Central indexer manager. Syncs indexers out to Radarr, Sonarr and Lidarr
so they never need to be added in each app individually.

## Ports
| Port | Protocol | Purpose |
|------|----------|---------|
| 9696 | TCP | Web UI |

## Usage
```bash
docker compose up -d
```

## Setup
1. Indexers, then add the indexers you want.
2. Settings, then Apps, then add Radarr, Sonarr and Lidarr using each
   app's URL and API key. The API key is under that app's Settings,
   then General.
3. Indexers sync out automatically after that. Do not add them by hand
   inside Radarr or Sonarr.

## Sync errors
`No Results in configured categories` means Prowlarr ran its pre-sync
test search and got nothing back for that app's categories, so it
refused the sync with a 400.

- Check Settings, then Apps, then the app, then show Advanced, and
  confirm Sync Categories includes the right category.
- Try tagging both the indexer and the app with the same tag. Prowlarr
  does not cross-reference categories against tags on its own.
- Some public indexers simply refuse to sync to one app while syncing
  fine to another. This is worth a few minutes, not a few hours. Add a
  different indexer and move on.

If the UI reports `API Key is invalid` but a manual `curl` with that same
key against the app's `/api/v3/` endpoint returns JSON, Prowlarr itself
is out of date:

```bash
docker compose pull && docker compose up -d
```
