# Soloterm Worktree Manager

`swm` turns a branch into an isolated, ready-to-use local development
environment with one command. It is for developers who want to work on
multiple features at once without repeatedly setting up folders, local
domains, environment files, and Solo projects by hand.

When you create a worktree, `swm`:

1. Creates a new Git branch and linked worktree from `main`, `dev`, another
   branch, a tag, or a commit.
2. Places it in a managed worktree root — `$HOME/Herd` when
   [Laravel Herd](https://herd.laravel.com) is installed, so Herd automatically
   serves it at `http://<worktree-name>.test`.
3. Optionally copies the source project's ignored `.env`, then updates
   `APP_NAME` (and `APP_URL`, when Herd provides a local site URL).
4. Registers the worktree as a separate project in
   [Solo](https://soloterm.com), ready for its own commands, terminals, and
   agents.

It also provides an interactive manager for forking existing worktrees and
cleanly removing managed worktrees when they are no longer needed.

## Requirements

- macOS and Zsh
- Git and `jq`
- Solo with its local HTTP API and `solo` CLI enabled

[Laravel Herd](https://herd.laravel.com) is optional. When it is installed,
`swm` parks the worktree root, derives the local TLD, and points `APP_URL` at
the generated site. Without Herd, worktree creation and Solo registration work
exactly the same — only the local site URL is skipped.

## Install

```sh
mkdir -p "$HOME/.local/bin"
curl -fsSL \
  https://raw.githubusercontent.com/adrenallen/soloterm-worktree-manager/main/swm \
  -o "$HOME/.local/bin/swm"
chmod +x "$HOME/.local/bin/swm"
```

Make sure `$HOME/.local/bin` is on your `PATH`, then run:

```sh
swm help
```

## Updating

```sh
swm update
```

This downloads the latest `swm` and replaces the installed script in place.
The download is rejected unless it is a non-empty, syntactically valid `swm`
script with a version, so a truncated or redirected response cannot overwrite a
working install. The replacement is a same-directory rename, so updating while
`swm` is running is safe.

`swm update` refuses to overwrite a package-manager install, and refuses to
overwrite a Git checkout unless you pass `--force`.

`swm` also checks for a newer version every two days and prints a one-line
notice. The check runs in the background and reports from a cache, so it never
delays a command. Disable it with `SWM_NO_UPDATE_CHECK=1`.

```sh
swm version
```

## Quick usage

Create a worktree from the current repository:

```sh
swm checkout-redesign dev
```

Copy the source `.env` and update `APP_NAME` (plus `APP_URL` under Herd):

```sh
swm checkout-redesign dev --with-env
```

Interactively fork or remove an existing worktree:

```sh
swm
```

`swm manage` is the explicit equivalent. With no repository argument, manage
mode automatically uses the Git repository containing your current directory.

## Adding an existing worktree to Solo

Manage mode lists every worktree in the repository and whether each is already
a Solo project:

```
  #  BRANCH                        TYPE      SOLO    PATH
  1) main                          main      yes     /Users/you/Code/myapp
  2) billing-redesign              managed   yes     /Users/you/Herd/billing-redesign
  3) hotfix                        external  no      /Users/you/scratch/hotfix

1 worktree(s) not in Solo. Select one to add it.
```

Pick one showing `SOLO no` and choose `[a]dd to Solo`. This registers the
existing directory only — it does not create a branch, copy a `.env`, or move
anything. The project is named `<repo>: <branch>`, matching what worktree
creation would have used.

Useful for worktrees made with plain `git worktree add`, or ones whose Solo
project was deleted.

## Remembering the .env choice

When you fork a worktree whose source has a `.env`, `swm` asks whether to copy
it, then offers to remember your answer for that project:

```
Copy .env and update it for the new worktree? [y/N]: y
Remember this for myapp from now on? [y/N]: y
```

Later forks of that project apply the remembered answer without asking. Clear
it to bring the prompt back:

```sh
swm forget
```

Defaults are keyed by the project's main worktree, so every linked worktree of
the same clone shares one setting, and `swm forget` works from inside any of
them. They are stored in `~/.config/swm/projects.conf`.

This applies to fork mode only. Direct creation still requires an explicit
`--with-env`, so a scripted `swm` run never copies a `.env` because of saved
state you cannot see at the call site.

Remove a managed worktree while preserving its Git branch:

```sh
swm remove checkout-redesign
```

Worktrees default to `$HOME/Herd` when Herd is installed, and `$HOME/worktrees`
otherwise. Override the root with `SWM_ROOT=/another/path`.

## Environment

| Variable | Purpose |
| --- | --- |
| `SWM_ROOT` | Worktree root directory. |
| `XDG_CONFIG_HOME` | Location of `swm/projects.conf` (default `~/.config`). |
| `SWM_NO_UPDATE_CHECK` | Set to `1` to disable the periodic update check. |
| `SWM_UPDATE_CHECK_INTERVAL` | Seconds between update checks (default `172800`). |

## Safety

Removal refuses directories not managed under `SWM_ROOT`. If a worktree has
uncommitted or untracked files, `swm` lets you show the file list, cancel, or
choose “delete anyway” with a typed-name confirmation. `--yes` never silently
authorizes deleting local changes. Environment copying requires `.env` to be
untracked and Git-ignored.

## License

[MIT](LICENSE)
