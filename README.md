# dotfiles
Various configuration files, bash aliases, git integrations, vim setup, etc.

## This repo uses the chezmoi installation 

[Find it Here](https??github.com/twpayne/chezmoi)

Install it on a new machine with:

```bash
# These lines are really here so I can copy/paste.

sh -c "$(curl -fsLS get.chezmoi.io/lb)"

sh -c "$(curl -fsLS get.chezmoi.io)" -- init --apply bithead2k

```

Basic operation:
```
chezmoi add $file
chezmoi cd
git add $file
git commit -m "I added file: $file"
git push
exit
```
