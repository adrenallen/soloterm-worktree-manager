# Soloterm Worktree Manager

`swm` creates and cleans up Git worktrees that are automatically registered
with [Solo](https://soloterm.com) and served by [Laravel Herd](https://herd.laravel.com).

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
