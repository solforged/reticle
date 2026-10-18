# Reticle

Terminal and editor themes adapted from the USGC RETICLE and POLYIMIDE
palettes, with contrast checks and conventional ANSI color meanings.

| Variant          | Background | Body text | Text contrast | Comment contrast |
| ---------------- | ---------- | --------- | ------------- | ---------------- |
| Reticle Dark     | `#000000`  | `#64b580` | 8.46:1        | 6.86:1           |
| Reticle Graphite | `#1c1c1c`  | `#8ac69e` | 8.64:1        | 7.04:1           |
| Reticle Light    | `#ffffff`  | `#0b6b39` | 6.61:1        | 6.45:1           |
| Polyimide        | `#000000`  | `#ffbf00` | 12.70:1       | 6.86:1           |

Graphite keeps Reticle's green text on a dark gray background instead of pure
black, for dark mode in a bright room.

## Ports

| Directory            | Application | Install to                       |
| -------------------- | ----------- | -------------------------------- |
| `ghostty/`           | Ghostty     | `~/.config/ghostty/themes/`      |
| `omp/`               | oh-my-pi    | `~/.omp/agent/themes/`           |
| `helix/`             | Helix       | `~/.config/helix/themes/`        |
| `nushell/`           | Nushell     | a directory your config reads    |
| `nvim/colors/`       | Neovim      | `~/.config/nvim/colors/`         |
| `nvim/lua/reticle/`  | Neovim      | `~/.config/nvim/lua/reticle/`    |

Omarchy themes live in their own repositories, so `omarchy theme install` can
install each one by URL:

```sh
omarchy theme install https://github.com/solforged/omarchy-reticle-dark-theme.git
omarchy theme install https://github.com/solforged/omarchy-reticle-light-theme.git
omarchy theme install https://github.com/solforged/omarchy-reticle-graphite-theme.git
omarchy theme install https://github.com/solforged/omarchy-polyimide-theme.git
```

Their `neovim.lua` selects a colorscheme from the Neovim port above.

## Selecting a variant

- Ghostty: set `theme` to `RETICLE-DARK-GH`, `RETICLE-GRAPHITE-GH`,
  `RETICLE-LIGHT-GH`, or `POLYIMIDE-GH`, for example
  `theme = dark:RETICLE-GRAPHITE-GH, light:RETICLE-LIGHT-GH`.
- oh-my-pi: select `reticle-dark`, `reticle-graphite`, `reticle-light`, or
  `polyimide` in `/settings`.
- Neovim: `:colorscheme reticle-graphite` or another variant. `:colorscheme
  reticle` follows `background`, using Light for light and Graphite for dark;
  set `vim.g.usgc_reticle_dark` to `"dark"` or `"polyimide"` to change the dark
  half.
- Helix: `theme = "reticle-graphite"`, or `:theme reticle-light` during a
  session.
- Nushell: load a theme's JSON into `$env.config.color_config`.
- Omarchy: select the installed theme by name.

Helix and Nushell do not follow system appearance changes on their own.

## Findings and changes

The original terminal palette put amber in ANSI green, purple in ANSI blue, and
orange in bright blue. That reproduced upstream iTerm colors, but made a green
success prompt amber and a blue path purple. The ports give ANSI colors their
conventional meanings. Purple types and orange numbers remain distinct RGB
syntax colors in Neovim and oh-my-pi.

Light mode's original `#8f8d88` secondary gray measured only 3.32:1 against
white; line numbers at `#b8b6b0` measured 2.03:1. Secondary text now uses
`#615e59` (6.45:1), with darker gray punctuation. Amber, orange, and pink were
also darkened enough for shaded popups and selected rows. The white background
and green body text remain.

Dark red and purple originally measured 3.61:1 and 3.57:1 against black. Both
are brighter now. Graphite keeps its `#1c1c1c` background and raises green text
from `#6fa886` to `#8ac69e`. Diff highlights were adjusted so text does not
disappear against the tinted background. Structural borders remain quieter than
text.

A later Graphite audit found ANSI green identical to body text, so diff context
and additions matched in Git and Lazygit. Its green is now `#a8d977`, and cyan
moves to `#6cc4d6` to separate it from body text. Changed and deleted diff lines
are tinted more strongly, and changed text inside them is blue rather than
green. ANSI black uses the border gray, so Lazygit's inactive borders show
without Ghostty's `minimum-contrast`. Helix colors strings amber, as Neovim and
oh-my-pi do, tints selections without replacing syntax colors, and draws
secondary cursors in gray. The check covers text on the selection background.

## Sunlight and typography

Polyimide's upstream foreground is bright amber on **pure black**. It improves
foreground luminance, but keeps the black background that is troublesome on a
reflective screen. Light mode is the first option to try with sun behind you;
Graphite is a compromise when keeping dark mode. A screenshot or calculated
color ratio cannot reproduce your display's reflections or room lighting.

Brighter text is what changed here. A thinner font weight is a different change;
it reduces the visible stroke area and is not the first adjustment to try for
glare. Ghostty offers `font-variation = wght=450` for variable fonts and macOS
`font-thicken`.

Ghostty's `minimum-contrast = 4.5` provides a fallback for hard-coded app
colors. It can change their displayed colors and does not correct reflections.
The palettes pass without relying on it.

## Shared color meanings

| Meaning                           | Color          | Terminal slot |
| --------------------------------- | -------------- | ------------- |
| Success, added, clean             | Green          | 2 / 10        |
| Warning, attention, modified      | Amber          | 3 / 11        |
| Error, removed, failed            | Red            | 1 / 9         |
| Path, link, information, function | Blue           | 4 / 12        |
| Keyword                           | Pink / magenta | 5 / 13        |
| Secondary information             | Cyan / teal    | 6 / 14        |
| Comment, secondary label          | Gray           | 8             |
| Type                              | Purple         | RGB only      |
| Number                            | Orange         | RGB only      |

The Neovim and oh-my-pi ports share syntax, diagnostic, and Git roles. The
oh-my-pi ports use a neutral selected-row background because selected rows can
contain several foreground colors; terminal text selection has an explicit
foreground.

## Maintenance

`palettes.json` holds the colors and `omp-roles.json` holds oh-my-pi's semantic
mapping. `generate.py` renders every port in this repository; generated files
are committed so applications do not need Python. The Omarchy repositories are
maintained by hand from the same palettes.

```sh
python3 generate.py          # regenerate ports
python3 generate.py --check  # fail on stale ports or low contrast
python3 preview.py           # palette specimen; keys 1-4, q to quit
```

The check fails on stale generated files or contrast below 4.5:1 for text on the
base, popup, selected-row, and diff backgrounds, plus selection and cursor
pairs. ANSI slots 1–15 are checked against the base. Slot 0 remains a dark
surface on dark themes; decorative borders are deliberately excluded. These are
sRGB relative-luminance checks, not a claim of complete application
accessibility. Run the preview in a fresh terminal; it is a palette specimen,
not a substitute for checking the real apps.

## Sources and license

These are local semantic adaptations, not exact upstream reproductions. Upstream
describes its Sublime themes as text-oriented, with no programming syntax rules.
Reticle Light and Graphite are local adaptations, not upstream themes or part
numbers. Polyimide keeps upstream's black, amber foreground, green caret, and
blue/cyan text selection, with local TUI accents.

- [USGC themes](https://github.com/usgraphics/usgc-themes/tree/14f74251d91e364c0d90844f0bd52feb43b08c60),
  audited 2026-09-05, used under the BSD 3-Clause License in [LICENSE](LICENSE).
- [Polyimide source](https://github.com/usgraphics/usgc-themes/blob/14f74251d91e364c0d90844f0bd52feb43b08c60/themes/sublime-text/USGC-POLYIMIDE-ST.sublime-color-scheme).
- [Ghostty `minimum-contrast`](https://ghostty.org/docs/config/reference#minimum-contrast).
- [Contrast definition](https://www.w3.org/TR/WCAG22/#dfn-contrast-ratio).
