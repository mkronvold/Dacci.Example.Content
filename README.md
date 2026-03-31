# Dacci.Example.Content

This repository is a small example of the content-repo shape that Dacci expects.

Use it as a reference when you create a new content repository or when you want a safe repo for manual multi-repo testing.

## Required repository shape

A Dacci content repo must be a normal Git checkout with a `data/` directory at the repo root:

```text
your-content-repo/
  .git/
  data/
    Topic Name/
      Document Name.md
```

Minimum requirements:

- the repository root must be a real Git repository
- Markdown content must live under `data/`
- Dacci should be pointed at the repo root, not directly at `data/`

## Suggested setup steps

Create a repo with the expected branch and folder layout:

```bash
mkdir Your.Content.Repo
cd Your.Content.Repo
git init --initial-branch=default
mkdir -p data/Getting\ Started
cat > data/Getting\ Started/Welcome.md <<'EOF'
# Welcome

This is a Dacci content repo.
EOF
git add data
git commit -m "Initial content"
```

If you want sync pull and push to work, add an SSH remote and push the branch:

```bash
git remote add origin git@github.com:YOUR-ORG/YOUR-CONTENT-REPO.git
git push -u origin default
```

## Adding the repo in Dacci

When Dacci and the content repo are both running on the host:

- add the repo from the Library panel
- set the repository root to the full checkout path
- leave the content root blank unless you intentionally keep content somewhere other than `repoRoot/data`
- enter the GitHub username whose SSH key should be used for pull and push

Docker note:

- Dacci-in-Docker needs the in-container repo path, not the host path
- for the default sibling layout, `/home/.../Dacci.Example.Content` on the host becomes `/library-workspace/Dacci.Example.Content` inside the container

## SSH key naming for Docker sync

The packaged Docker runtime expects the mounted host SSH directory to contain:

- `known_hosts`
- one private key file per GitHub username, named exactly as that username

Example:

```text
~/.ssh/
  known_hosts
  mkronvold
  teammate-a
```

When a saved Dacci repo entry stores username `mkronvold`, the API container will use the mounted file `~/.ssh/mkronvold` for Git operations in that browser session.

## Content authoring notes

Keep these changes inside `data/` when using Dacci sync:

- add, edit, rename, move, or delete Markdown files
- create or rename topic and subtopic folders

Avoid mixing unrelated repo changes on the same branch before using sync:

- changes outside `data/`
- rebases or branch surgery you have not finished
- broken `.git` state or missing upstream configuration

## Example Library entry

For this local example repo:

- Name: `Dacci Example Content`
- Repository root: `/home/mkronvold/src/Dacci.Example.Content`
- Content root override: leave blank
- GitHub username: your own GitHub username
