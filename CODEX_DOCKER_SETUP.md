# Codex + Cursor Docker Setup Guide

This guide explains how to run the OpenAI Codex CLI in Docker and integrate it with Cursor so you can use it from any project without installing Node.js globally. It also covers general Docker setup tips, common pitfalls, and security best practices.

## Quick Start

1. Install Docker Desktop and ensure Docker is running.
2. Add the wrapper to PATH once: open PowerShell and run as needed:
   - `C:\\Tools\\codex-docker\\setup-cursor.ps1`
3. Authenticate Codex (choose one):
   - ChatGPT login: `C:\\Tools\\codex-docker\\codex-login.bat`
   - API key: set `OPENAI_API_KEY` in your User environment
4. Test: open a new PowerShell, run `codex.bat --version`.
5. Use from any project: in that folder, run `codex.bat …` or let Cursor invoke it.

## How It Works

- The wrapper scripts live in `codex-docker` and are added to your PATH once.
- When you run `codex.bat` (or Cursor runs `codex`), we launch Codex inside a container using Docker Compose.
- Your current working directory is mounted into the container automatically, so Codex can read/write project files.
- Auth and config persist in `C:\\Users\\<You>\\.codex` and are reused across projects.

## Files in This Repo

- `codex-docker/Dockerfile` – Lightweight Node image with Codex installed
- `codex-docker/docker-compose.yml` – Compose service with volumes and ports
- `codex-docker/codex.bat` – Batch wrapper (used by Cursor on Windows)
- `codex-docker/codex.ps1` – PowerShell wrapper (optional)
- `codex-docker/codex-login.bat` – ChatGPT login helper (opens browser)
- `codex-docker/setup-cursor.ps1` – Adds the wrapper directory to PATH
- `codex-docker/.env` – Optional overrides for mounted paths
- `codex-docker/README.md` – Folder-level documentation

## Prerequisites

- Windows 10/11 with Docker Desktop (WSL2 backend recommended)
- PowerShell or CMD
- Cursor (for IDE integration)

Mac/Linux are also supported if you adapt paths (see notes below).

## Install Docker Desktop (Windows)

- Download from Docker and install with WSL2 backend enabled.
- Start Docker Desktop and ensure it stays running in the background.
- Optional: in Docker Desktop Settings → Resources, ensure your Windows drive is shared so volumes like `C:\\Users\\…` can be mounted.

## Verify Docker and Compose

- Docker: `docker version` should report client and server.
- Compose v2: `docker compose version` should work. If not, try legacy: `docker-compose --version`.
- This project auto-detects both forms.

## One-Time Setup

- Add the wrapper directory to PATH:
  - PowerShell: `C:\\Tools\\codex-docker\\setup-cursor.ps1`
  - This updates your User PATH (no admin required). If run as Admin, also updates Machine PATH.
- Restart Cursor or your terminal so PATH changes take effect.

## Authentication

- ChatGPT login (recommended for Pro accounts):
  - `C:\\Tools\\codex-docker\\codex-login.bat`
  - A browser opens on `http://localhost:1455`. After login, credentials are saved to `C:\\Users\\<You>\\.codex\\auth.json`.
- API key (alternative):
  - Set `OPENAI_API_KEY` as a User environment variable in Windows or in your shell profile.

## Cross‑Project Usage

- From any project directory, run `codex.bat …` or use Cursor. The wrapper mounts your current working directory into the container automatically.
- Defaults:
  - `WORKSPACE_DIR` defaults to your current directory
  - `CODEX_HOME_DIR` defaults to `C:\\Users\\<You>\\.codex`
- You can override via environment variables or `codex-docker/.env`.

## Configuration

- Codex config file (optional): `C:\\Users\\<You>\\.codex\\config.toml`
  - Example keys: `model`, `model_provider`, sandbox/approval policy, features.
  - Use double quotes in TOML strings.
- MCP (Cursor) config: `C:\\Users\\<You>\\.cursor\\mcp.json`
  - Do not hardcode API keys here. Reference environment variables instead.

## Security Best Practices

- Do not commit `auth.json` or API keys into source control.
- Store secrets in User environment variables and reference them from config.
- Keep Docker Desktop and images up to date (`docker image prune -a` as needed).

## Troubleshooting

- Docker not running: start Docker Desktop, verify with `docker ps`.
- Compose command issues: this setup supports both `docker compose` (v2) and `docker-compose` (v1). Ensure one is available.
- PATH issues: open a new terminal; run `codex.bat --version`. If not found, re-run `setup-cursor.ps1`.
- Volume mounting on Windows: ensure your drive is shared in Docker Desktop; paths in Compose should be quoted if they contain spaces.
- ChatGPT login issues: ensure port `1455` is free and not blocked by a firewall.
- Permission errors: try running your terminal as the same user that owns the folders; confirm folder permissions.

## Mac and Linux Notes

- Replace Windows paths with your platform paths in `docker-compose.yml` or use environment overrides.
- PATH setup differs across shells; consider symlinking a script named `codex` to call `docker compose run …` with similar args.

## Advanced Usage

- No‑network sandboxing: set `network_mode: none` in `docker-compose.yml` to disable all network access (will break login/API calls).
- Custom Node version: change the base image in `codex-docker/Dockerfile`.
- Alternate install (host): you can `npm -g install @openai/codex` directly on your machine; this repo focuses on containerized usage.

## Uninstall / Cleanup

- Remove PATH entry by editing your User PATH and deleting the `codex-docker` entry.
- Delete the image and container cache: `docker compose down` (in the folder) and `docker image rm codex:latest`.
- Remove saved auth/config if desired: delete `C:\\Users\\<You>\\.codex`.

---

Global wrapper location: `C:\\Tools\\codex-docker`. PATH has been updated to include it.
