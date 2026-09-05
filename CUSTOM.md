# CUSTOM.md — Recreating the "Dark" Icon Theme & Preview Sprite

This document is an objective, reproducible specification for creating the
**Dark Charmed Icons** theme and its preview sprite (`assets/dark.webp`).

It captures every final constraint used: theme name, icon colors, theme JSON
structure, sprite layout, DPI, fonts, and label colors.

---

## 1. Theme overview

| Field | Value |
| --- | --- |
| Theme id / folder | `dark` |
| Theme display name | `Dark Charmed Icons` |
| Top-level extension name | `Charmed Icons` |
| Appearance | `dark` |
| Icon source | copy of `icons/base/` |
| Icon count | 119 SVGs |

---

## 2. Icon set — `icons/dark/`

1. Copy every `.svg` from `icons/base/` into `icons/dark/`.
2. Recolor the primary palette to the dark brown **`#6D4C41`**.

### 2.1 Colors to replace with `#6D4C41`

Replace these 16 base primary palette colors (case-insensitive):

```
#5CBAFD  #5D80FF  #FF845B  #FFAD4C
#BEBED1  #AB7AFF  #9595AC  #FF5B78
#84D877  #41C09C  #EEA8E3  #F269A4
#7E7E92  #FFDDC8  #762F35  #354763
```

Apply the replacement to every hex color token in both `fill` and `stroke`
attributes.

### 2.2 Colors to keep unchanged

```
#372118  #AE3B3B  #B46138  #F2C57E
```

Also keep the named values `black`, `white`, and `none` exactly as they are.

---

## 3. Theme definition — `icon_themes/dark-theme.json`

1. Copy `icon_themes/base-theme.json` to `icon_themes/dark-theme.json`.
2. Make these two replacements:

| From | To |
| --- | --- |
| `"name": "Base Charmed Icons"` | `"name": "Dark Charmed Icons"` |
| `./icons/base/` | `./icons/dark/` |

3. Leave everything else identical (top-level name, `appearance: "dark"`,
   `file_stems`, `file_suffixes`, `file_icons`).

Validation: all 119 referenced icon paths must exist in `icons/dark/`.

---

## 4. Preview sprite — `assets/dark.webp`

### 4.1 Source

- All 119 SVGs from `icons/dark/`.
- Render order: alphabetical, left-to-right, top-to-bottom.

### 4.2 Sections

| Section | Contents | Count | Rows |
| --- | --- | --- | --- |
| Top — file icons | every icon whose label does **not** start with `folder` | 117 | 24 |
| Bottom — folder icons | icons whose label starts with `folder` (`folder`, `folder_open`) | 2 | 1 |

- The bottom section is placed below the top section.
- A vertical gap separates the two sections.
- Both sections use the same 5-column, left-to-right grid.

### 4.3 Labels

- Label = SVG filename **without the `.svg` extension**.
- Remove **leading underscores** from the label.
  - `_file` → `file`
  - `_folder` → `folder`
  - `_folder_open` → `folder_open`
- Labels sit to the **right** of their icon.
- Labels are **vertically centered** to the middle of the icon.

### 4.4 Layout values (design units)

| Property | Value |
| --- | --- |
| Columns | `5` |
| Column width | `220px` |
| Icon size | `28.8px` (30% of 96px) |
| Icon–label gap | `8px` |
| Column left padding | `16px` |
| Row height | `44px` |
| Section gap | `44px` |
| Outer margins (all sides) | `20px` |

Derived design dimensions:

```
content width  = 5 × 220            = 1100px
content height = 24×44 + 44 + 1×44  = 1144px
canvas         = 1100+40 × 1144+40  = 1140 × 1184px
```

### 4.5 Colors

| Property | Value |
| --- | --- |
| Background | `#12151D` |
| Icon primary color | `#6D4C41` |
| Label color | `#ECE2DF` |

Label color derivation (HSL):

```
#6D4C41 → hsl(15°, 25.3%, 34.1%)
set lightness to 90%
hsl(15°, 25.3%, 90%) → #ECE2DF
```

### 4.6 Font

| Property | Value |
| --- | --- |
| Family | `'Berkeley Mono', monospace` |
| Size | `14px` |
| Label baseline offset | `fontSize × 0.35` below the icon's vertical center |

---

## 5. Rendering

1. For each icon, rasterize the SVG to a `144 × 144` PNG and embed it in the
   combined SVG at `28.8 × 28.8` design units.
2. Position icons and text labels according to the grid above:

```
iconX  = margin + col × 220 + 16
iconY  = margin + sectionY + row × 44 + (44 − 28.8) / 2
labelX = iconX + 28.8 + 8
labelY = (iconY + 28.8 / 2) + 14 × 0.35
```

3. Rasterize the combined SVG at **200 DPI** (density `200`).
4. Encode as WebP (quality `92`).

### 5.1 Final output dimensions

At 200 DPI the design scales by `200 / 72 = 2.777…`:

```
width  = 1140 × (200/72) ≈ 3167px
height = 1184 × (200/72) ≈ 3289px
```

Final file: `assets/dark.webp` at `3167 × 3289px`.

---

## 6. Verification checklist

- [ ] 119 dark SVGs exist and are valid XML.
- [ ] Dark SVGs contain only `#6D4C41`, `#372118`, `#AE3B3B`, `#B46138`,
      `#F2C57E`, plus `black`/`white`/`none`.
- [ ] `dark-theme.json` is valid JSON and every referenced path exists.
- [ ] Sprite has 5 equally-spaced icon columns.
- [ ] Top section has 24 file-icon rows; bottom section has 1 folder-icon row.
- [ ] Section gap contains no content pixels.
- [ ] Outer margins are clean (no content in the border).
- [ ] Labels use `#ECE2DF`, Berkeley Mono, no leading underscores.
- [ ] Output is a valid WebP at `3167 × 3289px`.
