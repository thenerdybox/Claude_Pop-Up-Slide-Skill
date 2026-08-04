/* ============================================================================
   SETTINGS TEMPLATE — for the "pop-up-slide" skill
   ============================================================================
   This is a template file. When generating a real campaign, replace every
   value below based on the interview answers, then save the result as
   "settings.js" next to a copy of overlay-template.html (renamed to
   stream-popup-overlay.html) in the campaign's own output folder.

   REMINDERS FOR WHOEVER (Claude) IS FILLING THIS IN:
     - Keep the two-file split: engine (overlay html) + this settings file.
       Never merge them into one file.
     - Overlay size is always 640×220 in OBS — do not vary this by campaign.
     - If the logo/image should stay the same across every slide: leave
       "icon" and "image" OFF every message entirely. Do not set them to
       empty strings — just omit the keys. The engine's fallback already
       handles this by showing the logo throughout.
     - If the logo/image should change per slide: give each message either
       an "image" (a real file path, if the user supplied image files) or
       an "icon" (inline SVG path markup, viewBox 0 0 24 24, stroke drawn
       in currentColor, matching a simple line-icon style) — not both.
     - If the user has real brand colors/fonts, use those exact values, not
       invented ones. If a color has a stated reserved meaning (e.g. "this
       green means the server is live, nothing else"), do not use that
       color for unrelated decoration — pick a neutral accent instead.
     - Double check every placeholder below has been replaced before
       delivering — no literal "REPLACE_ME" text should remain in output.
   ========================================================================= */

const CONFIG = {

  // ---- MESSAGES -------------------------------------------------------
  // One entry per slide. Each needs a "tag" (short caps label) and "text"
  // (the bigger line underneath). Add "image" or "icon" per the rules
  // above only if this campaign uses per-slide images; otherwise omit both
  // on every message for a static logo throughout.
  messages: [
    { tag: "REPLACE_ME_TAG_1", text: "REPLACE_ME_TEXT_1" },
    { tag: "REPLACE_ME_TAG_2", text: "REPLACE_ME_TEXT_2" }
  ],

  // ---- TIMING (all in milliseconds — 1000 = 1 second) -----------------
  // See the skill's SKILL.md for a full explanation of what each field
  // controls — the short version:
  //   perMessageHold     -> how long EACH slide stays visible
  //   slideOutPause      -> the blank gap AFTER sliding out, before repeat
  //   holdBeforeSlideOut -> short beat before the slide-out motion starts
  //   holdBeforeOpening  -> short beat before the first slide's text opens
  //   textOpenDuration / swapFade -> animation speeds, not pauses
  timing: {
    holdBeforeOpening:  350,
    textOpenDuration:    450,
    perMessageHold:      2600,  // <- set from interview answer: seconds per slide
    swapFade:            260,
    holdBeforeSlideOut:  400,
    slideOutPause:       500,   // <- set from interview answer: pause before repeat
  },

  // ---- COLORS -----------------------------------------------------------
  // Use real brand tokens if the user provided them. Otherwise pick a
  // deliberate, non-generic palette suited to the content — check
  // /mnt/skills/public/frontend-design/SKILL.md for guidance rather than
  // defaulting to a cliché AI palette.
  colors: {
    bg:          "#REPLACE_ME", // badge background
    accent:      "#REPLACE_ME", // primary accent (icon ring, tag label base)
    accentSoft:  "#REPLACE_ME", // secondary accent (tag label color)
    ink:         "#REPLACE_ME", // main text color
    mute:        "#REPLACE_ME", // supporting/secondary text
  }

};
