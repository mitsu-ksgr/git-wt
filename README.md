git-wt
======

A simple `git worktree` helper that organizes worktrees under a single base directory, mirroring the repository's remote URL structure.



## Installation

Copy `git-wt` file to somewhere on your `PATH` and make it executable:

```sh
cp git-wt ~/.local/bin/git-wt
chmod +x ~/.local/bin/git-wt
```

Then add an alias to your `.gitconfig` so it's available as `git wt`:

```ini
[alias]
    wt = !git-wt
```

Now you can use it as a git subcommand:

```sh
git wt <command> [args]

Commands:
    add <name>          Create a new worktree (creates branch if it doesn't exist)
    list, ls            List all worktrees
    paths               List worktree paths
    remove, rm <name>   Remove a worktree
```



## Configuration

By default, worktrees are stored under:

```
$XDG_DATA_HOME/git-worktrees/ # if XDG_DATA_HOME is set
~/.local/share/git-worktrees/ # otherwise
```

To override, set `git-wt.baseDir` in your `.gitconfig`:

```ini
[git-wt]
    baseDir = ~/dev/worktrees
```

Within the base directory, worktrees are placed at `<host>/<owner>/<repo>/<name>`, derived from the repository's `origin` remote URL.
For repositories without a remote, `localhost/<repo-name>` is used as the path.


### With peco
You can use [peco](https://github.com/peco/peco) to interactively select and `cd` into a worktree.
Add the following to your `.zshrc`:

```zsh
function peco-git-wt-cd() {
    local worktrees
    worktrees=$(git wt paths 2>/dev/null)

    if [[ -z "$worktrees" ]]; then
        zle -M "no worktrees found"
        return
    fi

    local selected
    selected=$(echo "$worktrees" | peco | awk '{print $3}')

    if [[ -n "$selected" ]]; then
        BUFFER="cd ${selected}"
    fi
    zle clean-screen
}
zle -N peco-git-wt-cd
bindkey '^G' peco-git-wt-cd
```

Press `Ctrl+G` inside a git repository to open a fuzzy finder over all worktrees and jump to the selected one.

For bash, add the following to your `.bashrc` instead:

```bash
function peco-git-wt-cd() {
    local worktrees
    worktrees=$(git wt paths 2>/dev/null)
    [[ -z "$worktrees" ]] && return

    local selected
    selected=$(echo "$worktrees" | peco | awk '{print $3}')

    if [[ -n "$selected" ]]; then
        READLINE_LINE="cd ${selected}"
        READLINE_POINT=${#READLINE_LINE}
    fi
}
bind -x '"\C-g": peco-git-wt-cd'
```



## License

GPL-3.0

