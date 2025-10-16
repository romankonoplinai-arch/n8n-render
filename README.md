# n8n on Render (Blueprint)

This repository contains the minimal setup to deploy **n8n** on **Render** with persistence for workflows/credentials.

## Files

- `Dockerfile` — uses the official `n8nio/n8n:latest` image.
- `render.yaml` — Render Blueprint that creates a Web Service for n8n and mounts a persistent disk at `/home/node/.n8n`.
- `.gitignore` — basic ignore file.

## Quick Deploy (Blueprint)

1. Create a new GitHub repo and upload these files.
2. Go to https://render.com → **New +** → **Blueprint** → connect your GitHub repo.
3. Review and **Apply**. Render will provision the service.
4. When Live, open the URL Render gives you (e.g., `https://n8n-yourname.onrender.com`).

### Environment Variables

These are set in `render.yaml` already. You can change them in the Render dashboard later too.

- `N8N_ENCRYPTION_KEY`: CHANGE THIS to a long random string before going to production.
- `GENERIC_TIMEZONE`: change to your region if needed.

### Persistence

The `disk` section in `render.yaml` mounts a 1 GB persistent volume at `/home/node/.n8n`. This is where n8n stores workflows, credentials and settings by default, so they survive restarts/redeploys.

> If you prefer a database (Postgres) instead of a disk, configure the following env vars and remove the `disk` section:
>
> - `DB_TYPE=postgresdb`
> - `DB_POSTGRESDB_HOST=...`
> - `DB_POSTGRESDB_DATABASE=...`
> - `DB_POSTGRESDB_USER=...`
> - `DB_POSTGRESDB_PASSWORD=...`
>
> Then create a Render Postgres instance and wire its connection details here. Keep `N8N_ENCRYPTION_KEY` stable.

### MCP (optional)

After deploy, you can connect `n8n-mcp`:
```
n8n-mcp --api-url https://<your-render-url> --api-key <your-n8n-api-key>
```

Generate the API key inside n8n → Settings → API.

## Local Test (optional)

You can also run locally to test:
```
docker run --name n8n -p 5678:5678 -e N8N_ENCRYPTION_KEY=dev-local-key -v %cd%/n8n_data:/home/node/.n8n n8nio/n8n:latest
```
On Linux/Mac, replace the volume path with `$PWD/n8n_data`.
