# Arch Linux Tips

## Wifi
After installed Arch Linux, you can use `iwd` or `nmtui` to connect wifi.

## Customize Grub
```
sudo vim /etc/default/grub
GRUB_GFXMODE=1024x768
```

```bash
git clone https://github.com/vinceliuice/grub2-themes.git
cd grub2-themes
sudo ./install.sh -t stylish -s 2k
```

## Vmware

```
sudo systemctl start vmware-networks-configuration.service
sudo systemctl start vmware-networks.service
sudo modprobe -a vmw_vmci vmmon


sudo pacman -S open-vm-tools xf86-video-vmware xf86-input-vmmouse
sudo systemctl enable vmtoolsd.service vmware-vmblock-fuse.service
```

## Install Fish

```bash
curl https://raw.githubusercontent.com/oh-my-fish/oh-my-fish/master/bin/install | fish
omf theme
omf install eclm
```

## Install Zsh

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

## Neovim

```bash
curl -sL https://raw.githubusercontent.com/jorgebucaran/fisher/main/functions/fisher.fish | source && fisher install jorgebucaran/fisher
fisher install jorgebucaran/nvm.fish

nvm install 18
nvm use 18
npm install -g tree-sitter-cli
```

## Vim

```
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \\\n    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim

cd ~/.vim/plugged/youcompleteme
./install.py --clang-completer
```

## Link sh to bash

```bash
ls -l /bin/sh
```

## Sudo Timeout

lengthen sudo timeout, for example 60 min
```bash
sudo visudo

Defaults env_reset, timestamp_timeout=60
```

## Change Chrome Font

| Type              | Font                  |
| ----------------- | --------------------- |
| Standard font     | Arial                 |
| Serif font        | Arial                 |
| Sans-serif font   | Arial                 |
| Fixed-width font  | Noto Sans Mono CJK TC |
| Mathematical font | Noto Sans Mono CJK TC |

## Chezmoi

```bash
chezmoi --init apply zyrethor

chezmoi update
```

## error: GPGME error: No data

```bash
sudo rm -r /var/lib/pacman/sync/
```
[resource](https://www.reddit.com/r/archlinux/comments/wvdr7u/error_gpgme_error_no_data/?rdt=56472)

## Neovim different indent

```
:set expandtab
:retab
```

## remove serial number in filename (notion backup)

```bash
file:
find . -type f -name "*.md" -exec perl-rename 's/ [0-9a-z]{32}//' {} +

folder:
#!/bin/bash
find . -type d -name "* *" | sort -r | while read -r dir; do
  newdir=$(echo "$dir" | sed 's/ [0-9a-z]{32}//')
  mv "$dir" "$newdir"
done
```

## chmod

only directories

```bash
find * -type d -exec chmod 755 {} +
```

only files

```bash
find * -type f -exec chmod 644 {} +
```


use fd to batch process, use `-X` instead of `-x`

```bash
fd -t d -X chmod 755 {}
```

```bash
fd -t f -X chmod 644 {}
```

## Convert Video Format

```bash
ffmpeg -i test.webm -c copy test.mp4
```

## yt-dlp

download only audio

```bash
yt-dlp --extract-audio 'https://www.youtube.com/watch?v='
```

it will select opus format (default)

if you want to select differnt format, use `--audio-format`

download only mp3

```bash
yt-dlp --extract-audio --audio-format mp3 'https://www.youtube.com/watch?v='
```

## Repeat Key Speed

in `~/.xinitrc`

xset r rate [delay] [rate]

```bash
xset r rate 200 20
```
