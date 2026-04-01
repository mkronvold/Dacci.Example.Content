# Dacci.Example.Content

This repository is a small example of the content-repo shape that Dacci expects today.

Use it as a safe repo for testing workspace discovery, repo switching, document editing, manual push, and background pull without touching production content.

## Repository shape

A Dacci content repo is a normal Git checkout with Markdown content under `data/`:

```text
your-content-repo/
  .git/
  data/
    Topic Name/
      Document Name.md
```

Minimum requirements:

- the repository root must be a real Git checkout
- Markdown content must live under `data/`
- Dacci should point at the repository root, not directly at `data/`

## Recommended local layout

The packaged runtime now expects workspace repos under the Dacci repo's local `workspace/` directory:

```text
dacci/
  workspace/
    Dacci.Example.Content/
      .git/
      data/
```

When this repo is cloned there, Dacci discovers it automatically at startup.

## Creating a repo like this

If you want to make another repo with the same shape:

```bash
mkdir Your.Content.Repo
cd Your.Content.Repo
git init --initial-branch=main
mkdir -p data/Getting\ Started
cat > data/Getting\ Started/Welcome.md <<'EOF'
# Welcome

This is a Dacci content repo.
EOF
git add data
git commit -m "Initial content"
git remote add origin git@github.com:YOUR-ORG/YOUR-CONTENT-REPO.git
git push -u origin main
```

## Using this repo with Dacci

Preferred flow:

- clone this repo into `dacci/workspace/Dacci.Example.Content`
- start Dacci from the app repo with `./scripts/up`
- open the Library panel only if you want to confirm discovery or save a browser-local override

The packaged runtime exposes workspace repos inside the container as `/workspace/<repo-name>`. For this repo, the in-container path is:

```text
/workspace/Dacci.Example.Content
```

If you add it manually instead of relying on discovery, use:

- Name: `Dacci Example Content`
- Repository root: `/workspace/Dacci.Example.Content`
- Content root override: leave blank
- Sync branch: `main`

## Git access in Docker

The packaged Docker runtime uses a staged copy of the host `~/.ssh` directory, resolves SSH-config symlinks before mounting it, and forwards `SSH_AUTH_SOCK` when available.

Before using Dacci sync, make sure the same checkout already works with plain Git from the host:

```bash
git pull
git push
```

That same SSH setup is what Dacci will reuse for pull and push.

## Sync expectations

Dacci sync is intended for content changes under `data/`.

Good candidates:

- editing Markdown files
- creating, renaming, or moving content files
- creating or renaming topic and subtopic folders

Handle these with Git directly instead of Dacci sync:

- changes outside `data/`
- repo maintenance such as rebases, resets, or branch surgery
- editor settings or other non-content files mixed into the same branch

Background pull only performs guarded pulls. Push stays manual.

## What to try in this repo

- edit `data/Getting Started/Welcome.md`
- test repo switching from the Library panel
- verify manual pull and push against a safe repo
- test background pull behavior without touching production content
