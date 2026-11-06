# Galvanize Everything app icon

A zinc-silver lightning bolt (a galvanic spark) on a rounded blue tile with faint zinc-spangle
facets. The tile's blue runs from `#2a6fd6` to `#0f3f8f`, around the interface's deep accent
`#1659c4` (`theme::BRAND`). Original artwork; see `LICENSE.txt`.

## Geometry

A 512-unit tile with 110-unit corners. The macOS icons pad the tile onto Apple's 824/1024 icon
grid; every other render uses the full tile.

## Files

- `galvanize-everything.svg`: the master SVG (also the hicolor scalable icon).
- `galvanize-everything-1024.png`: 1024 px render of the tile.
- `galvanize-everything.icns`: macOS bundle icon.
- `galvanize-everything.ico`: Windows icon (16, 20, 24, 32, 40, 48, 64, 128, 256).
- `hicolor/<size>/apps/org.galvanizeeverything.galvanize-everything.png` (16–512) and
  `hicolor/scalable/apps/org.galvanizeeverything.galvanize-everything.svg`: Linux icon theme.
- `packaging/icons.sh`: makes every other file above from the SVG. The app draws the SVG itself
  at the exact pixel size (title bar, welcome screen); the window icon is the 256 px PNG.

## Regenerate

```sh
packaging/icons.sh   # needs resvg or rsvg-convert; iconutil on macOS for the .icns
```
