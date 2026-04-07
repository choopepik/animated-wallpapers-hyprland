# Hyprland Animated Wallpapers (mpvpaper)

Animated GIF/MP4 wallpapers with auto-switch + keybind switching.

---

## Features
- Animated wallpapers (GIF / MP4)
- Auto wallpaper switching
- Manual switching (keybind)
- Works with Hyprland + mpvpaper
- Compatible with Caelestia shell

---

## ⚙️ Install

```bash
sudo pacman -S mpvpaper
```

---

## 📁 Wallpaper Folder

Create folder:

```bash
mkdir -p ~/Pictures/Wallpapers
```

Put your .gif and .mp4 wallpapers there.

---

## 🚫 Caelestia-dots

~/.config/caelestia/shell.json

```json
wallpaperEnabled: false
```

---

## AUTO Wallpaper (Random)

~/.config/hypr/random-wallpaper.sh

```bash
#!/bin/bash

WALLDIR="$HOME/Pictures/Wallpapers"
MONITOR="*"
INTERVAL=120

while true; do
    FILE=$(find "$WALLDIR" -type f \( -iname "*.gif" -o -iname "*.mp4" \) | shuf -n 1)
    pkill mpvpaper
    mpvpaper -o "loop --no-audio" "$MONITOR" "$FILE" &
    sleep "$INTERVAL"
done
```

---

## Hyprland Config

~/.config/hypr/hyprland.conf

```ini
exec-once = bash -c "sleep 2 && ~/.config/hypr/random-wallpaper.sh"
```

---

## Next Wallpaper Script (KEYBIND)

~/.config/hypr/next-wallpaper.sh

```bash
#!/bin/bash

WALLDIR="$HOME/Pictures/Wallpapers"
MONITOR="*"
STATE_FILE="$HOME/.cache/wallpaper_index"

mapfile -t FILES < <(find "$WALLDIR" -type f \( -iname "*.gif" -o -iname "*.mp4" \) | sort)

COUNT=${#FILES[@]}
[ $COUNT -eq 0 ] && exit 1

if [ -f "$STATE_FILE" ]; then
    INDEX=$(cat "$STATE_FILE")
else
    INDEX=0
fi

FILE="${FILES[$INDEX]}"

INDEX=$(( (INDEX + 1) % COUNT ))
echo $INDEX > "$STATE_FILE"

pkill mpvpaper
mpvpaper -o "loop --no-audio" "$MONITOR" "$FILE" &
```

---

## Keybind

~/.config/hypr/hyprland/keybinds.conf

```bash
bind = Super, N, exec, ~/.config/hypr/next-wallpaper.sh
```

---

## Make Scripts Executable

```bash
chmod +x ~/.config/hypr/random-wallpaper.sh
chmod +x ~/.config/hypr/next-wallpaper.sh
```

---

## Apply

```bash
hyprctl reload
```

If it doesn’t start, relog.

---

## Notes

- Supports .gif and .mp4
- Uses mpvpaper
- MONITOR="*" works on most setups
- Disable Caelestia wallpaper to avoid conflicts
