# git-init-template

A template directory for `git init`. Clone this repo and point Git at it so
that every new repository you create starts with your preferred hooks,
configuration, and other defaults.

## Quick start

```sh
git clone https://github.com/KoviRobi/git-init-template ~/.config/git/template
git config --global init.templateDir ~/.config/git/template
```

From now on, `git init` (and `git clone`) will copy the contents of that
directory into the `.git/` folder of every new repository.

## What is a template directory?

When Git initialises a new repository it copies every file from the template
directory into `.git/`. The most common use-cases are:

| Path inside template | Purpose |
|---|---|
| `hooks/pre-commit` | Run linters / formatters before each commit |
| `hooks/commit-msg` | Enforce a commit-message format |
| `hooks/prepare-commit-msg` | Pre-populate the commit message editor |
| `info/exclude` | A repo-local `.gitignore` that is never committed |
| `config` | Default Git configuration for every new repo |

Any file that does **not** start with a dot and is placed in the template
directory will be copied verbatim. Hook scripts must be executable
(`chmod +x hooks/<hook-name>`).

## Adding your own hooks

Create a hook script inside `hooks/` and make it executable:

```sh
cat > ~/.config/git/template/hooks/pre-commit <<'EOF'
#!/bin/sh
# example: prevent committing directly to main/master
branch=$(git symbolic-ref --short HEAD 2>/dev/null)
if [ "$branch" = "main" ] || [ "$branch" = "master" ]; then
  echo "Direct commits to $branch are not allowed." >&2
  exit 1
fi
EOF
chmod +x ~/.config/git/template/hooks/pre-commit
```

## Applying the template to existing repositories

The template is only applied automatically during `git init` / `git clone`.
To apply it to a repository that already exists, re-run `git init` inside it —
Git will merge the template files without touching your history:

```sh
cd /path/to/existing-repo
git init
```

## Further reading

- `man git-init` — the `--template` flag and the `init.templateDir` config key
- `man githooks` — full list of supported hook names and their arguments
