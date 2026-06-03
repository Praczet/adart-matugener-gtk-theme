# Adart-Moonmix

Adart-Moonmix is a GTK theme source tree based on the old GTK Moonmix user CSS
override setup. The theme keeps the Tokyonight Moon mood, but lets Matugen
generate the wallpaper-aware palette.

GTK3 is the main target. GTK4 support exists for plain GTK4 apps such as
`pavucontrol`, but this is not a full libadwaita theme.

## Layout

```text
matugen/
├── matugen.config.toml
└── templates/
    └── gtk-adart-moonmix.css

themes/
└── Adart-Moonmix/
    ├── gtk-3.0/
    │   ├── gtk.css
    │   ├── gtk-dark.css
    │   ├── matugen-generated.css
    │   ├── widgets/
    │   └── apps/
    │       └── thunar.css
    └── gtk-4.0/
        ├── gtk.css
        ├── gtk-dark.css
        ├── matugen-generated.css
        └── widgets.css

gimp/
└── Adart-Moonmix/
    ├── gimp.css
    └── gimp-dark.css
```

`input/` is ignored by Git and kept only as a local snapshot of the previous
override setup.

## Matugen

The project config generates both GTK3 and GTK4 palettes:

```sh
scripts/update-matugen-palette image /path/to/wallpaper.jpg
```

This writes:

```text
themes/Adart-Moonmix/gtk-3.0/matugen-generated.css
themes/Adart-Moonmix/gtk-4.0/matugen-generated.css
```

Edit palette logic in:

```text
matugen/templates/gtk-adart-moonmix.css
```

Do not hand-edit `matugen-generated.css` unless you are doing a temporary test.

Note: the normal `~/.config/matugen/config.toml` is not automatically changed by
this repo. If you want your usual Matugen wallpaper workflow to update this
theme, add a template block there that points to this repo's template/output.

## Install

Install the theme for the current user:

```sh
scripts/install-theme
```

It installs to:

```text
~/.local/share/themes/Adart-Moonmix
~/.config/GIMP/3.2/themes/Adart-Moonmix
```

Set GTK to use:

```text
Adart-Moonmix
```

For Hyprland, make sure no stale environment variable overrides it. In this
setup, `GTK_THEME` should be `Adart-Moonmix` or unset.

## Development Loop

After CSS changes:

```sh
scripts/dev-install
```

That reinstalls the theme and quits Thunar so the next launch reloads CSS.

For GTK3 testing:

```sh
gtk3-demo
gtk3-widget-factory
thunar
```

For GTK4 testing:

```sh
pavucontrol
```

For GIMP, select the matching app theme inside GIMP:

```text
Edit > Preferences > Interface > Theme > Adart-Moonmix
```

GIMP ships its own theme CSS and the default GIMP theme overrides normal GTK
theme colors. The bundled GIMP theme maps GIMP's own color variables back to
the Adart-Moonmix Matugen palette.

## Scope

GTK3:

- normal theme entrypoints under `gtk-3.0/`
- widget CSS split under `gtk-3.0/widgets/`
- Thunar fixes enabled by default under `gtk-3.0/apps/thunar.css`
- GIMP app theme under `gimp/Adart-Moonmix/`

GTK4:

- shared Matugen palette
- conservative plain GTK4 widget CSS
- useful for apps like `pavucontrol`
- not expected to fully style libadwaita apps

## Current Notes

The theme deliberately avoids copying the entire Adwaita source for now. It is a
proper installable theme package built from the old override prototype, with the
CSS organized so generated palette and widget rules stay separate.
