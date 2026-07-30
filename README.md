# Soloterm Worktree Manager

`swm` turns a branch into an isolated, ready-to-use local development
environment with one command. It is for developers who want to work on
multiple features at once without repeatedly setting up folders, local
domains, environment files, and Solo projects by hand.

When you create a worktree, `swm`:

1. Creates a new Git branch and linked worktree from `main`, `dev`, another
   branch, a tag, or a commit.
2. Places it under `$HOME/Herd` so [Laravel Herd](https://herd.laravel.com)
   automatically serves it at `http://<worktree-name>.test`.
3. Optionally copies the source project's ignored `.env`, then updates
   `APP_NAME` and `APP_URL` for the new Herd site.
4. Registers the worktree as a separate project in
   [Solo](https://soloterm.com), ready for its own commands, terminals, and
   agents.

It also provides an interactive manager for forking existing worktrees and
cleanly removing Herd/Solo worktrees when they are no longer needed.

## Requirements

- macOS and Zsh
- Git and `jq`
- Laravel Herd with a parked worktree directory
- Solo with its local HTTP API and `solo` CLI enabled

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

## Quick usage

Create a worktree from the current repository:

```sh
swm checkout-redesign dev
```

Copy the source `.env` and update `APP_NAME` and `APP_URL` for Herd:

```sh
swm checkout-redesign dev --with-env
```

Interactively fork or remove an existing worktree:

```sh
swm manage
```

Remove a managed worktree while preserving its Git branch:

```sh
swm remove checkout-redesign
```

Worktrees default to `$HOME/Herd`. Override that parked directory with
`SWM_ROOT=/another/path`.

## Safety

Removal refuses dirty worktrees and directories not managed under `SWM_ROOT`.
Environment copying requires `.env` to be untracked and Git-ignored.

## License

[MIT](LICENSE)
