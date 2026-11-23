# English version — Reddit / Hacker News / Product Hunt

> **Target audience**: Homelab / self-hosted / DevOps engineers. Write as a maker sharing a real tool, not as marketing.
> **Key rule**: On Reddit and HN, "please star my repo" reads as spam. Lead with the *problem*, show the screenshots, be honest about the limitations, let the link speak for itself.
> **Link**: one link is enough — `https://github.com/lichangzhou/Docker-link`

---

## A. Show HN (Hacker News)

**Title** (HN titles should be plain, no marketing):

```
Show HN: Docker-Link – a single-container Docker dashboard with Compose, file manager, and web terminal
```

**Body**:

```
Hi HN — I built Docker-Link because I got tired of my Docker panel and my compose
files drifting apart.

The core problem: most dashboards store their own notion of "stacks". Portainer
keeps Stack definitions in its own database, so when you add a container in the UI
your on-disk compose YAML is never updated. The next `docker compose up -d`
silently reverts it. Docker-Link scans your real compose directory instead, so the
panel and the YAML are always the same source of truth. Editing a service and
clicking "deploy" runs `docker compose up -d` for real.

What it does:
- Containers / images / volumes / networks / ports — full CRUD
- Compose project browser over your real compose directory
- File manager (browse, edit in place, upload, download)
- Web terminal: `docker exec` with a real PTY, not a fake read-only shell
- Multi-host: HTTP agent mode (token auth) or plain SSH, your choice
- Enterprise WeChat alerts on container failure / image update
- Image update detection that compares *manifest digests*, not tags
- Three-layer pull fallback: registry mirror → daemon → in-container proxy load

Stack: FastAPI + docker-py + SocketIO. The frontend is a single `index.html`
file — no build step, no bundler, which is why the image stays small.

Honest limitations:
- `linux/amd64` only for now; ARM users need to build it themselves
- No built-in auth by default. It mounts `docker.sock`, so access ≈ host root.
  Set `ADMIN_USER`/`ADMIN_PASSWORD` and don't expose it to the internet.
- The single-file frontend is great while the feature set is small; it will get
  harder to maintain as it grows.
- The project is young and the API will change.

MIT licensed. Issues and PRs welcome — especially from people running it on
ARM hardware.

https://github.com/lichangzhou/Docker-link
```

**Posting notes**
- Best time: US Eastern morning, i.e. roughly **evening in China time**.
- Do NOT ask for upvotes in the first hour; HN penalizes obvious vote solicitation.
- Reply to every comment, especially technical ones, with substance.

---

## B. Reddit

**Target subreddits** (post to 1–2, not all at once): `r/selfhosted`, `r/docker`, `r/homelab`, `r/HomeLab`

**Title**:

```
I built a single-container Docker dashboard after Portainer's stacks kept drifting from my compose files
```

**Body**:

```
Long-time lurker here. I run ~20 containers on my NAS (Jellyfin, Emby, qBittorrent,
alist, Home Assistant, a proxy, etc.) and I was using Portainer CE.

The thing that finally broke it for me: Portainer's Stack definitions live in its own
database. If I add a container through the UI, my on-disk compose YAML doesn't get
updated — so the next time I ran `docker compose up -d` from the shell, it silently
reverted my changes. Every change meant: SSH in, edit the YAML by hand, then go back
and click "redeploy".

So I wrote my own panel. Docker-Link. Single container, no database, and the compose
files on disk are the single source of truth.

What it does:
- Full container / image / volume / network / port management
- Compose project browser that reads your actual compose directory
- File manager — edit YAML in the browser instead of over SSH
- Web terminal via `docker exec` with a real PTY
- Multi-host support over either an HTTP agent (token auth) or plain SSH
- Enterprise WeChat notifications on failure/update
- Update checks that compare manifest digests rather than tags
- Pull fallback chain: registry mirror → daemon → in-container proxy

Tech: FastAPI + docker-py + SocketIO, single-file frontend (no bundler).

Caveats, because they matter:
- amd64 images only for the moment
- No auth by default and it needs `docker.sock` — that means host-root-equivalent
  access. Enable `ADMIN_USER`/`ADMIN_PASSWORD` and keep it on your LAN.
- Young project, API may change

MIT licensed, source here: https://github.com/lichangzhou/Docker-link

Happy to answer questions or take PRs. Especially if anyone runs ARM hardware and
wants to help with multi-arch builds.
```

**Posting notes**
- Include **2–4 real screenshots** (container list, compose editor, web terminal).
- Say plainly that you wrote it — never hide that it's your own project.
- Don't cross-post the same text to multiple subs on the same day; some subs
  penalize it. Space them out or pick the one that fits best.
- Never include referral/affiliate links (there are none here).

---

## C. Product Hunt

**Name**: Docker-Link

**Tagline** (max 60 chars):

```
Single-container Docker dashboard: Compose, files, terminal, multi-host
```

**Description**:

```
Docker-Link is an open-source Docker management dashboard that runs as a single
container with no database.

Unlike dashboards that keep their own copy of your stack definitions, Docker-Link
reads your real compose directory, so what you see in the UI and what's on disk
are always the same. Editing a service and deploying runs `docker compose up -d`
for real.

Highlights:
- Containers, images, volumes, networks and ports — full CRUD
- Compose project browser over your actual compose files
- Built-in file manager (browse, edit, upload, download)
- Web terminal with a real PTY via docker exec
- Multi-host: HTTP agent mode or plain SSH, no extra daemon required
- Update detection that compares manifest digests, not tags
- Three-layer pull fallback for restricted networks
- Enterprise WeChat alerts

MIT licensed. Runs on any Docker host with the socket mounted.
```

**First comment** (post this as the maker, immediately after launching):

```
Hi Product Hunt! I'm the solo developer behind Docker-Link.

I started it because I was tired of my dashboard and my compose files disagreeing
with each other — the UI would show one state, the YAML on disk another, and
`docker compose up -d` would quietly undo my changes.

The dashboard runs as a single container, needs no database, and reads your real
compose directory rather than maintaining its own copy of it.

Currently amd64-only and the auth story is "you set the env vars yourself" — I'm
working on multi-arch builds. Would love feedback from people running this on
ARM NAS hardware.

GitHub: https://github.com/lichangzhou/Docker-link
```
