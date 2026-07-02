# dotfiles

Personal dotfiles for Kirk Roybal (`bithead2k`).

This repo is a **bare git repository tracking `$HOME` directly** — files live in
their real locations (no symlinks, no `stow`, no `chezmoi`). It contains shell
scripts (`~/bin`, `~/.local/bin`), editor/terminal/desktop configs (`~/.vim`,
`~/.config/*`), and assorted home dotfiles.

Third-party Vim plugins, secrets, shell histories, and browser/app data are
**intentionally not tracked**. Plugins are reinstalled with the included
`bin/install_vim_plugins` script.

---

## 1. Check out the dotfiles

On a fresh machine (needs `git`):

```sh
# 1. Clone the repo as a *bare* repo into ~/.dotfiles
git clone --bare https://github.com/bithead2k/dotfiles.git "$HOME/.dotfiles"

# 2. Define the management alias for this shell
alias dotfiles='git --git-dir=$HOME/.dotfiles --work-tree=$HOME'

# 3. Check the files out into $HOME
dotfiles checkout
```

If step 3 fails because files already exist in `$HOME`:

```sh
# Back up the conflicting files, then check out again
mkdir -p "$HOME/.dotfiles-backup"
dotfiles checkout 2>&1 | grep -E '^\s+\.' | awk '{print $1}' | \
    xargs -I{} sh -c 'mkdir -p "$HOME/.dotfiles-backup/$(dirname {})"; mv "$HOME/{}" "$HOME/.dotfiles-backup/{}"'
dotfiles checkout
```

Then hide the sea of untracked `$HOME` files so `dotfiles status` is usable:

```sh
dotfiles config status.showUntrackedFiles no
```

**Make the alias permanent.** `~/.bashrc`/`~/.bash_aliases` are deliberately
**not** tracked, so add the alias to your shell startup yourself:

```sh
echo "alias dotfiles='git --git-dir=\$HOME/.dotfiles --work-tree=\$HOME'" >> ~/.bashrc
```

## 2. Install Vim

**Debian / Ubuntu** (aorus-master, downwarddog):

```sh
sudo apt update && sudo apt install -y vim git
```

**Termux / Android** (ZFlip7):

```sh
pkg install -y vim git
```

Other platforms: `sudo dnf install vim git` (Fedora/RHEL),
`sudo pacman -S vim git` (Arch), `brew install vim git` (macOS).

## 3. Install the Vim extensions

The auto-loaded plugins (under `~/.vim/pack/*/start/`) are cloned by the
tracked script. Ensure `~/bin` is on your `PATH`, then:

```sh
install_vim_plugins            # clone missing / update existing
install_vim_plugins --list     # show the configured plugins
install_vim_plugins --dry-run  # preview actions, change nothing
install_vim_plugins --force    # wipe and re-clone everything
```

This installs: `vim-airline`, `vim-airline-themes`, `nerdtree`, `rust.vim`,
`obsidian.nvim`, and `markdown-preview.nvim` (whose server binary is built
automatically via `:call mkdp#util#install()` / `yarn` when available).

Open Vim to confirm the plugins loaded (`:Airline`, `:NERDTree`, etc.).

---

## Day-to-day use

```sh
dotfiles status                      # what changed
dotfiles add -f ~/bin/newscript      # track a new/changed file (force past deny-all)
dotfiles commit -m "add newscript"
dotfiles push
```

The repo is **deny-all by default** (`~/.dotfiles/info/exclude` contains `*`),
so nothing is tracked unless you explicitly `add -f` it — this prevents
accidentally committing secrets. Note that `info/exclude` is local to each
clone and is not pushed; recreate it (`printf '*\n' > ~/.dotfiles/info/exclude`)
if you want the same safety on a new checkout.

## Not tracked (by design)

Secrets and machine state: `~/.ssh` private keys, `~/.gnupg`, `~/.pgpass`,
`~/.config/gh`, browser/Electron app data, all shell histories, and `~/.bash*`
(kept out so a bad checkout can't break your login shell).
