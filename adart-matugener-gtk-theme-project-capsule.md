# ADArt-Unclaimed-Bloom GTK Theme Project Capsule

Updated: 2026-10-20

## Project Identity

This repository builds and installs `ADArt-Unclaimed-Bloom`, a GTK theme that
grew out of an older GTK Moonmix user CSS override setup (formerly
`Adart-Moonmix`).

Colours come from Unclaimed Bloom, which mixes a base palette with Matugen
wallpaper colours and re-plants the GTK palette on every wallpaper change.
Widget coverage comes from a vendored adw-gtk3 base; this repo adds the ADArt
look on top.

GTK3 is the main target. GTK4 support covers plain GTK4 apps such as
`pavucontrol`. libadwaita apps load their own stylesheet and only pick up the
palette copy Unclaimed Bloom writes to `~/.config/gtk-4.0/gtk.css`.

## Repository Shape

```text
README.md
adart-matugener-gtk-theme-project-capsule.md
gimp/
  ADArt-Unclaimed-Bloom/
    gimp.css
    gimp-dark.css
scripts/
  install-theme
  dev-install
themes/
  ADArt-Unclaimed-Bloom/
    gtk-3.0/
      gtk.css
      gtk-dark.css
      gtk-adart-unclaimed-bloom.css   # palette, generated
      base/                           # adw-gtk3 + bridge.css
      widgets/
      apps/
    gtk-4.0/
      gtk.css
      gtk-dark.css
      gtk-adart-unclaimed-bloom.css   # palette, generated
      base/                           # adw-gtk3 libadwaita.css + bridge.css
      widgets.css
input/   (ignored, old override snapshot)
docs/    (ignored, local notes)
```

## Layer Order

Both `gtk.css` entrypoints import, in order:

1. `base/` – vendored adw-gtk3 v6.5 dark (LGPL-2.1, `base/LICENSE-adw-gtk3`).
   GTK3: `adw-gtk3-dark.css` + `assets/`. GTK4: `libadwaita.css`,
   `libadwaita-tweaks.css` + `assets/`. It styles every widget using
   libadwaita colour names (`window_bg_color`, `accent_bg_color`, ...).
2. `gtk-adart-unclaimed-bloom.css` – the generated palette. Later
   `@define-color` wins, so it recolours the whole base.
3. `base/bridge.css` – maps adw-gtk3 names the palette does not emit
   (`dialog_bg_color`, `thumbnail_*`, `secondary_sidebar_*`, ...). The GTK4
   bridge also re-points `--accent-*` CSS variables that the tweaks file pins
   to blue.
4. GTK3 `widgets/*.css` then `apps/*.css`, GTK4 `widgets.css` – the ADArt look.

Without the base, GTK3/GTK4 fall back to bare unstyled defaults for every
widget the widget layer does not mention. Do not remove it.

Do not edit files under `base/` except `bridge.css`; update adw-gtk3 by
replacing them from a newer release.

## Palette Pipeline (Unclaimed Bloom)

- Config: `~/.config/unclaimed-bloom/` (not a git repo). Source of truth with
  history: `~/Development/Hyprland/unclaimed-bloom/` – mirror edits there.
- Current profile `desktop-graphite-tweak`; its GTK/GIMP run uses profile
  `daily-gtk`: base palette `nord-tweak-dark` + Matugen, mood `dormant`.
- Recipe: `targets/gtk/recipes/nord-tweak-dark.json`.
  Template: `targets/gtk/templates/unclaimed-bloom.css`.
- Plant hook `targets/gtk/hooks/plant.json` copies the rendered palette to
  `~/.local/share/themes/ADArt-Unclaimed-Bloom/gtk-{3,4}.0/gtk-adart-unclaimed-bloom.css`
  and `~/.config/gtk-4.0/gtk.css`, then sets the gsettings theme. It does not
  run `install-theme`.
- Regenerate by hand: `spore sow daily-gtk gtk && spore grow daily-gtk gtk && spore plant daily-gtk gtk`.

Recipe notes:

- Unclaimed Bloom mixes colours in plain RGB. Mixing Nord blue with a
  near-complementary wallpaper colour lands on grey, so the accent is mostly
  wallpaper (`base: blue, source: primary, mix: 0.75`).
- Matugen's dark `primary` is always a light pastel, so `accent_fg_color` is
  dark (`background_dark`).
- The template brightens text toward `{{selection_fg}}`.

The palette copies in this repo are only a fallback for a fresh install.
`scripts/install-theme` keeps an already planted palette in the installed
theme.

## GTK3 Widget Layer

- `widgets/` split by responsibility: base, headerbar, view, selection, input,
  button, menu, scrollbar, control, notebook, dialog, panel, infobar, label,
  final.
- Trimmed on 2026-10-20: 69 whole rules that produced no visible change over
  the adw-gtk3 base were removed (pixel-verified, see Testing). Rules the test
  gallery cannot exercise (app-specific nodes, `:visited`, ...) were kept.
- Menus/popovers are 80% opaque (76% backdrop); Hyprland blurs behind them
  via `decoration:blur:popups` in `~/.config/hypr/modules/decoration.lua`.
- `apps/thunar.css` – Thunar sidebar (`.thunar .shortcuts-pane`), view modes,
  backdrop.
- `apps/gimp.css` – GIMP GTK3 dialogs. `apps/yad.css` – YAD dialog label and
  Cancel icon.
- `label.css` lets labels in `:checked` and `.suggested-action` buttons inherit
  the button colour (dark on solid accent).
- Buttons use `font-weight: normal` (adw-gtk3 makes them bold).

Colour rule of thumb: `accent_fg_color` is for text on a solid (or >= ~70%)
accent. Translucent accent tints over dark surfaces need `window_fg_color`
(this broke the focused DBeaver dialog button once).

## GTK4 Widget Layer And Variants

- `gtk-4.0/widgets.css`: plain GTK4 look on top of the base. Buttons use
  normal font weight; `window` has `backdrop-filter: blur(10px)` (supported by
  GTK 4.24). A partial trim run removed 2 redundant rules; the rest was not
  fully tested, so treat it as unverified.
- `themes/ADArt-Unclaimed-Bloom-DBeaver/`: GTK3 variant that imports the main
  theme and sets menus to 0.65 (0.61 backdrop). DBeaver is SWT on GTK3
  (native Wayland); `dbeaver-bloom` in `~/Development/Hyprland/dbeaver-theme`
  exports `GTK_THEME=ADArt-Unclaimed-Bloom-DBeaver` when it is installed.
  `install-theme` installs every `ADArt-Unclaimed-Bloom-*` variant.

## GIMP Layer

`gimp/ADArt-Unclaimed-Bloom/gimp-dark.css` imports the installed palette
(`file:///home/adam/.local/share/themes/ADArt-Unclaimed-Bloom/gtk-3.0/gtk-adart-unclaimed-bloom.css`)
and GIMP's `common-dark.css`. The absolute path assumes the per-user install.

## Scripts

- `scripts/install-theme` – installs to `~/.local/share/themes/ADArt-Unclaimed-Bloom`
  and `~/.config/GIMP/3.2/themes/ADArt-Unclaimed-Bloom`, keeping the planted
  palette.
- `scripts/dev-install` – install + `thunar -q`.

## Testing

Manual: `gtk3-widget-factory`, `gtk3-demo`, `thunar`, `pavucontrol`, DBeaver
(native GTK parts; reads colours only at startup), GIMP.

Headless (no windows on the desktop):

- Parse check: PyGObject `Gtk.CssProvider` with the `parsing-error` signal,
  GTK3 and GTK4.
- Render: GTK3 `Gtk.OffscreenWindow`; GTK4 via `gtk4-broadwayd` +
  `GDK_BACKEND=broadway` and `Gtk.WidgetPaintable`.
- Offscreen/Broadway windows are always `:backdrop`. Simulate a focused
  window by loading a copy of the CSS with `:backdrop` renamed to a
  never-matching pseudo-class (`:drop(active)`).

## Gotchas

- Do not hand-edit `gtk-adart-unclaimed-bloom.css`; change the recipe or
  template.
- Backdrop state must be styled explicitly; check focused and unfocused.
- Thunar row rounding differs by view mode.
- GTK4/libadwaita boundaries are real; do not claim full libadwaita theming.
- Apps that read GTK colours only at startup (DBeaver/SWT) need a restart.
