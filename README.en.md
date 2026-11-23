# Random Roll Call — Single-File Offline Web App

A "name sphere" picker for classrooms, meetings and live streams. One HTML file — double-click and it works. No network, no dependencies, no data ever leaves your device.

- Live demo: https://4m971vpmr3c1r.doubao.link/app/app_17j81wh6x8e
- Source: `随机点名器.html` (single file, ~128 KB, CSS/JS inlined, zero external requests)
- 中文说明: [README.md](README.md)

---

## 1. What it is

120 names are spread evenly over a slowly rotating 3D sphere using a Fibonacci distribution. Press **Start** and the sphere flips and accelerates in a random direction, leaving motion blur. Press **Pause** and it decelerates for the configured number of turns, then shows the chosen name full-screen.

Everything is Canvas 2D rendering plus Web Audio synthesis — no images, no audio files, no network requests.

## 2. Features

**Drawing**
- The sphere idles with a slow spin; a draw flips it in a random direction at a random speed (5–8×)
- After Pause it glides for the configured number of turns; set 0 and it stops instantly
- Weighted picking: each name can be set to Very high / High / Default / Low / Very low / Never, with weights 8 / 4 / 1 / 0.5 / 0.1 / 0 (all 1 by default); displayed odds always add up to exactly 100%
- "Never" names still appear on the sphere but are never drawn; if everything is set to Never the picker falls back to equal odds and says so in the total row
- 60 fps verified with 120 names

**Multiple profiles (v1.7.0)**
- Each profile keeps its **own** names, odds and extra (off-list) entries, without overwriting the others
- A **visible capsule switch** at the top right of the home screen: use the "Original" public list normally, switch with one click when needed — the sphere and the draw use the new profile immediately
- The switch is disabled while a draw is running or a result is on screen
- In Settings you can switch, rename, create an empty profile, duplicate the current one, or restore the original (120 sample names, all odds reset)
- The **profile list** shows one row per profile (with an "Active" tag and name count); the × at the end deletes any profile without switching to it first, and greys out when only one is left
- Deleting the last remaining profile is blocked, so there is always at least one

**Multi-language (v1.8.0)**
- The interface supports **Chinese + English / French / Russian / Spanish / Arabic** (the five UN working languages besides Chinese)
- Switch it under **Language** at the top of the General settings tab; it applies instantly and is stored with the config, so the next launch keeps it
- The home screen, settings panel, every status message, odds tiers, gesture positions, result screen and exit buttons all follow the language; Arabic switches to **right-to-left** layout automatically
- Built-in profile names (such as 原版 / Original) are translated for display; names you type yourself are kept as-is

**Result screen**
- Full-screen black background with a huge name (roughly 30% of viewport height); very long names shrink to 60% width and stay on one line
- Two red exit buttons on the left and right; the stay time is configurable from 3 to 60 seconds (10 s by default) with auto-return, and the buttons pulse during the last 3 seconds
- Esc or Space exits at any time

**Names**
- Paste import: line breaks, commas, Chinese enumeration commas (、) and spaces can be mixed
- Upload `txt` / `csv`: for CSV only the first column is used, header and notes rows are skipped automatically
- An empty list shows a hint instead of producing an empty result

**Settings panel** (opened through the hidden gesture)
- General: interface language, configs (multiple name lists and odds), spin speed (0.5–3×), glide turns (0–10), result duration, draw sounds and volume, name list, general password, secret gesture
- Appearance: decor density, background colour, background image (auto-compressed to 1600 px, stored locally), image opacity, font family (sans / serif / Kai / round / mono), text colour (auto-contrast against the background), title size and weight, result name scale and weight, sphere name size
- "Reset to defaults" is always available

**Hidden gesture and passwords**
- The trigger is the **blue star** among the dark star decoration; all the coloured stars are decoys
- Default gesture: **one click on the blue star at the right-middle** (positions can be changed in Settings to left-middle / top-left / bottom-left / top-right / bottom-right / top-centre / bottom-centre, with 1–4 stars in sequence and 1–3 taps per star)
- The whole gesture must finish within 20 seconds and consecutive clicks within 4 seconds; a wrong or late click resets it
- Then a password box appears: the general password opens Settings, the advanced password opens the odds page
- The two passwords are independent; 3 wrong attempts lock the box for 10 seconds; clicking the "Password" title 5 times within 3 seconds resets it to the default
- **The default password is `123456789` — change it right after your first visit**

**Sound (synthesised locally, no audio files)**
- Start: a 0.5-second air accent
- Spin and glide: a time-bomb style tick that speeds up and then slows down
- Result: a low impact plus a rising three-note chime
- Separate cues for correct/wrong passwords and button clicks; can be muted or volume-adjusted, and no audio context is created while muted

**Storage**
- All state lives in the browser's `localStorage`:
  - `rollcall.config.v1`: config JSON (profiles with names/odds, sound, background, fonts, gesture, passwords, language; single-list data from older versions is migrated to the "Original" profile)
  - `rollcall.bgimage.v1`: background image (compressed dataURL, stored separately to avoid rewriting a large string on every save)
- Changing device or browser, or clearing the cache, returns it to the initial state; nothing is uploaded anywhere

## 3. How to use

1. Put `随机点名器.html` anywhere and open it by double-clicking in a modern browser (Chrome / Edge / Safari); `file://` works directly.
2. To share, send the file to others or host it on any static server — it is a single HTML file.
3. Press **Start** at the bottom → the sphere spins → press **Pause** → it decelerates and stops → result screen → returns automatically or manually.

**Suggested first run**
1. Use the default gesture (one click on the right-middle blue star) → enter `123456789` → Settings opens
2. Change the general password and paste your own names
3. Adjust speed, duration, background, fonts, language as needed

## 4. Technical notes

- Single file, no dependencies: all CSS and JS inlined, no `fetch`, no CDN, no third-party libraries
- Sphere layout: Fibonacci spiral sampling (golden angle ≈137.5°), names and points shuffled so identical surnames do not cluster
- Rendering: Canvas 2D; each frame fades with `destination-out` to leave 1–2 frames of trail, while the background is a separate CSS layer, so flat colours and images both work
- Performance: back-side names are heavily dimmed and drawn sorted by depth; 120 names at 60 fps
- Safety: every name is written with `textContent`, preventing XSS; no network requests
- Fonts: system font stacks only, fully offline
- Language: a small dictionary keyed by the Chinese source text translates text nodes, placeholders, titles and `aria-label`s, and a `MutationObserver` translates dynamically generated messages as they appear

## 5. Version history

Major versions only; patch-level fixes are listed in `CHANGELOG.md`.

| Version | Theme | Highlights |
| --- | --- | --- |
| v1.8.0 | Languages | Chinese + English / French / Russian / Spanish / Arabic, switchable in Settings; Arabic switches to RTL |
| v1.7.2 | Profile management | Profile list with one row per profile, × deletes any profile, greyed out when only one remains |
| v1.7.0 | Profiles | Independent names and odds per profile, visible switch on the home screen, new "Never" tier, old data migrated to "Original" |
| v1.6.3 | Sound | Louder ticking countdown alternating high/low, speeding up and slowing down, plus a low impact on the result |
| v1.6.0 | Sound | First sound system: air accent, landing thump, three-note chime, on/off and volume |
| v1.5.0 | Look and background | Tighter letter spacing; background colour, background image import (auto-compressed), opacity |
| v1.4.0 | Fonts | Font family, title and result weights, redesigned settings panel |
| v1.3.0 | Gesture | Configurable gesture: taps per star, number of stars, positions; default is one click at right-middle |
| v1.2.0 | Gesture | Trigger stars fixed blue, decoys coloured, "decor tint" setting removed |
| v1.1.0 | Gesture | Decoration changed to stars and brightened; minimal density keeps 4 real / 8 fake |
| v1.0.0 | Initial | 3D name sphere, draw state machine, result screen, two passwords, weighted odds, settings panel |

## 6. Licence and feedback

Free to use for personal study and classroom use. For commercial use or redistribution, please contact the author first. Bug reports and suggestions are welcome as Issues.
