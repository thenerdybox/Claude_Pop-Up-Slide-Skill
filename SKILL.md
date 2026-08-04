---
name: pop-up-slide
description: Creates an animated slide-in popup/badge overlay (self-contained HTML + settings.js, always sized 640x220 for OBS Browser Source) used to advertise a website, brand, game, server, or campaign on a livestream. Always use this skill whenever the user asks to build a "stream popup", "popup slide", "ad slide", "promo overlay", "badge overlay", "stinger-style ad", or any recurring campaign overlay meant for OBS, Twitch, TikTok, Kick, Trovo, or YouTube. Before writing any code, interview the user: whether the logo/image stays the same across every slide or changes per slide, how long each slide displays, how long the pause is before the loop repeats, what each slide should say, and whether real brand colors/fonts/logo files are available to use instead of invented ones.
---

# Pop Up Slide

Produces a small animated badge that slides in from a corner of the
screen, cycles through one or more "slides" (a short tag + a line of
text, optionally its own icon or image), then slides back out, goes
blank for a set pause, and repeats — forever, on loop, as an OBS Browser
Source.

This skill was built from a real campaign (see the worked example in
`references/nerdybox-example.md`) — read that file if you want to see
exactly what a finished settings.js/HTML pair looks like before
generating a new one.

## Fixed conventions — apply these silently, don't ask about them

- **Size: always 640×220.** Every campaign built with this skill uses
  this exact size, so overlays stay visually consistent with each other
  across campaigns. Never ask the user about dimensions; never vary this.
- **Always exactly two code files per campaign**: the engine
  (`stream-popup-overlay.html`, copied from `assets/overlay-template.html`
  with minimal edits) and the content file (`settings.js`, copied from
  `assets/settings-template.js` and fully filled in). Never merge these
  into one file, and never split the content across more than one file.
- **Badge position: bottom-right corner** of the OBS scene, unless the
  user asks for a different corner.
- **Transparent background always** — never let `html`/`body` end up with
  a background color, or OBS will show a solid box instead of an overlay.

## Step 1: Interview the user

Ask before writing any code. Batch the free-text/numeric questions
together in one message rather than one at a time. Use `ask_user_input_v0`
only for the one genuinely either/or question (logo behavior) — the rest
need actual text/number answers, which that tool isn't built for.

1. **Logo/image behavior** (use `ask_user_input_v0`, two options: "Same
   image every slide" / "Different image per slide"):
   - If "different per slide": ask what should appear on each slide — do
     they have real image files to use (thumbnails/icons), or should
     Claude draw simple inline line-icons per slide instead?
2. **Slide content**: how many slides, and for each one, a short tag
   label (1-2 words, e.g. "WEB") and the main line of text.
3. **Timing**:
   - How long should each slide stay on screen? → sets `perMessageHold`
   - How long should the pause be after the badge fully disappears,
     before it slides back in and starts over? → sets `slideOutPause`
4. **Brand assets**: do they have brand colors/fonts, a logo file, or a
   brand kit (zip) to reference? If yes, ask them to send it and pull the
   *real* tokens from it (look for a README or a stylesheet with actual
   hex/font values — don't invent colors if real ones exist to find). If
   no brand kit exists, ask for at least a logo file, and choose a
   deliberate, non-generic color palette and font pairing suited to the
   content — check `/mnt/skills/public/frontend-design/SKILL.md` before
   picking colors/fonts from scratch, to avoid defaulting to a cliché
   AI-generated look.
5. **Anything else pertinent worth surfacing**:
   - A different corner than bottom-right?
   - Does any brand color have a reserved/specific meaning (the way one
     past campaign's green specifically meant "server is live, nothing
     else")? If so, don't use that color for unrelated decoration
     anywhere in the build — pick a neutral accent instead.
   - If this is a re-run for an existing brand already built with this
     skill before, ask whether to reuse that brand's existing
     colors/fonts/logo rather than re-deriving them.

Don't ask about overlay size — it's always 640×220, applied silently.

## Step 2: Generate the files

1. Copy `assets/overlay-template.html` → `stream-popup-overlay.html` and
   `assets/settings-template.js` → `settings.js` into a fresh output
   folder for this campaign.
2. Fill in `settings.js` completely from the interview answers:
   - `messages`: one object per slide, each with `tag` + `text`.
     - **Static logo across all slides** → leave `icon`/`image` off every
       message entirely (don't set them to `""` or `null` — just omit the
       keys). The engine's built-in fallback already makes this show the
       logo throughout; no other change is needed anywhere.
     - **Different image per slide** → add an `image` field (a real file
       path) to each message if the user supplied image files, or an
       `icon` field (inline SVG path markup, `viewBox="0 0 24 24"`,
       shapes drawn so they render in `currentColor`/`stroke`) if they
       want simple drawn icons instead. Don't mix both fields on the same
       message.
   - `timing.perMessageHold`: from the "how long per slide" answer
     (convert seconds to milliseconds: seconds × 1000).
   - `timing.slideOutPause`: from the "pause before repeat" answer (same
     conversion). Leave `holdBeforeOpening`, `textOpenDuration`,
     `swapFade`, and `holdBeforeSlideOut` at the template's defaults
     unless the user specifically asks to change pacing beyond the two
     values above.
   - `colors`: real brand tokens if available, otherwise a deliberately
     chosen palette (never leave `#REPLACE_ME` placeholders in delivered
     output).
3. In `stream-popup-overlay.html`, only the font `@import` line and the
   `font-family` values in `.msg-tag`/`.msg-text` typically need editing
   (to match real brand fonts) — the CSS variable *names* don't need to
   change, only their fallback hex values if you want the file to look
   right even before JS runs. The animation/engine JavaScript should not
   need edits.
4. Copy any logo/thumbnail image files the user provided into the same
   output folder as the two code files.
5. Before presenting: search the delivered files for leftover template
   placeholders (`REPLACE_ME`, `REPLACE_ME_TAG`, etc.) — none should
   remain. This skill's output is sometimes published publicly (e.g. to a
   GitHub repo), so double-check for clean, consistent comments and no
   template artifacts before calling this done.
6. Present the files with `present_files`. Briefly summarize what was set
   from the interview (slide count/content, timing values in seconds,
   logo behavior, whether real brand colors were used or chosen fresh),
   and remind the user OBS Browser Source should be set to 640×220 with
   "Local file" pointed at the `.html` file.

## Reference: what each timing field actually controls

Be precise about this if the user asks — this exact distinction has
caused confusion before:

| Field | What it controls |
|---|---|
| `perMessageHold` | How long **each individual slide** stays fully visible before the next one swaps in. The "how long does each slide play" duration. |
| `slideOutPause` | The pure blank/idle gap **after the badge has fully slid off-screen**, before the next loop starts sliding back in. The "delay before it repeats." |
| `holdBeforeSlideOut` | A short beat after the last slide closes but before the slide-out motion itself begins — separate from `slideOutPause`, usually left at its default. |
| `holdBeforeOpening` | A short beat after sliding in but before the first slide's text opens — cosmetic pacing, usually left at its default. |
| `textOpenDuration` / `swapFade` | Animation speeds for the open/close and message-swap transitions themselves — not pauses at all. |

## Reference files

- `references/nerdybox-example.md` — a real finished example (content,
  reasoning, and full file listing) from the campaign this skill was
  built from. Read it if you want a concrete before/after to model a new
  campaign on, especially for how to handle a provided brand kit.
