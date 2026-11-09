# Troubleshooting: Symptom → Cause → Fix

Every entry happened in a real build. Check here before inventing a diagnosis.

## Google Flow and Generation

| Symptom | Cause | Fix |
|---|---|---|
| `flow_status` reports not signed in or no project open | Google Flow session in Chrome is closed or logged out | Ask the user to open Google Flow in Chrome (flow.google), sign in, and open or create a project. Re-run `flow_status`. |
| Scene skipped with "quoted credits exceed max_credits" | Safety cap triggered by model quote | Check credit cost with user; if approved, raise `max_credits` in `flow_generate` or switch model to Omni 1.1 Flash (~7-12 credits). |
| `flow_wait` returns `finished: false` after 40 seconds | Long-running video generation in progress | Normal behavior. Call `flow_wait` again until `finished: true`. Video generations typically take 1 to 3 minutes. |
| Flow generation fails or returns an error | Transient network or server issue in Flow | Call `flow_retry` to re-queue failed jobs with the same parameters, or retry once with an adjusted prompt. |
| The transformation "looks like two separate videos" | The trajectory broke at the swap | One unbroken vector through the transition (same heading, position, speed), or better, choose a single-subject concept. |
| The subject looks frozen or lifeless | The prompt over-stabilized it to protect the path | Lock the path, free the body: keep the trajectory rigid but demand natural motion on the subject and ambient life in the scene. |
| A boundary crossing (into water, through glass) looks fake | The pass was too clean | Write the physical lens moment into the prompt: splash, droplets on the lens, a beat of blur. |
| The hero product reads as a placeholder | Generic unbranded object at distance | Apply the brand mark via image editing before animating, or write the ending to land close enough that the design carries it. |
| Three failed videos on one concept | Concept problem, not prompt problem | Stop iterating the prompt and pivot the concept; the laws predict which concepts land first-try. |
| The shot is strong but the ending will not rest (the subject drifts back into motion near the end) | The model overshot the composed arrival and kept animating | Do not re-roll first. Trim the raw to the last steady frame and scrub-encode in one pass (recipe in `ffmpeg-recipes.md`); the page runs on progress, not seconds, so a shorter clip costs nothing. |
| The chained journey glitches at a segment join | Segment encode parameters were not identical, or the motion vector broke between segments | Re-encode every segment with the exact same scrub command and re-concat; if the motion itself jumps, ensure `chain_previous: true` or `flow_continue` with `mode: "exact"` was used. |
| A visible cut where two chained segments meet, even though the motion continues | Fine texture was re-imagined from the start frame | Next time, storyboard every seam into motion or a texture-refresh moment (the seam law in `prompt-laws.md`); for the chain you already have, use the crossfade join in `ffmpeg-recipes.md`. |
| A job comes back flagged on an innocent abstract shot | The safety filter misread abstract sensory language | Re-roll with the prompt rewritten in plain commercial product-photography words: name the product early, describe objects not sensations, keep the same shot design. |

## The scrub hero

| Symptom | Cause | Fix |
|---|---|---|
| Scrubbing does nothing on the live site but works locally | Host lacks partial-download (Range) support, so seeks clamp to zero | Fetch the video as a Blob and play the object URL (see `scrub-pipeline.md`); works everywhere. |
| Scroll feels choppy at the hero's top and bottom, worse in Chrome | Un-gated seeks piling up plus per-frame DOM writes | Seek gating and delta-gated writes (see `scrub-pipeline.md`). |
| Still choppy after gating and delta-gating | The video's keyframe interval is too long | Re-encode with `-g 8 -keyint_min 8` (see `ffmpeg-recipes.md`). |
| Scrubbing freezes permanently mid-scroll | The seek-busy flag deadlocked (a seek errored and never fired `seeked`) | Reset the flag and clear the pending target in the video's `error` handler; the deadlock-safe pattern is in `scrub-pipeline.md`. |
| Phones download the video or poster they never show | Poster set in HTML, or the video load not behind the static-hero gate | Set the poster via JavaScript inside the same gated code path that loads the video. |
| The static hero appears on desktop, or the video loads on phones | The five gate conditions differ between CSS and JS | Make the five media queries match EXACTLY in both (see `scrub-pipeline.md`). |
| The page is broken when the video fails to load | No error path | Hide the dead video over the poster background on `error`; the page must be complete without the video. |
| Screenshots of the running site show the poster or a blank stage where the scrubbing video should be, but the site works in a real browser | The video's promoted compositor layer does not composite into embedded-browser screenshots | Verify scrub behavior through the DOM instead of pixels: probe the band `--k` variables, word opacities and transforms, and `currentTime` at several scroll positions, or watch it in a real browser window. |
| The page looks completely dead in the preview: poster never set, every caption band at opacity 0, the video never seeks, and the pane reports it is hidden | A hidden or non-displayed preview pane stops compositing, so `requestAnimationFrame` never fires | Stop using the pane and drive the machine's own installed Chrome headlessly; recipe below. |
| The hero goes blank (no poster, no captions) after rotating the device or resizing the window | JS decided static-versus-scrub once at load while the CSS gates stayed live | Arm and disarm the scrub from change listeners on all five gate queries; the live-gate pattern is in `scrub-pipeline.md`. |
| Double-clicking `index.html` shows the still hero and the video never loads | Browsers block `fetch` on file:// URLs, so the Blob loader falls back by design | That state is the designed fallback and must look complete. For the full scrub preview, serve the folder with any one-liner (`npx http-server`, `python -m http.server`) and open localhost. |

### The headless Chrome recipe (when the preview pane is dead)

When the preview pane itself is hidden or broken, verify through the machine's own installed Chrome instead, driven headlessly over the DevTools protocol:

1. Launch Chrome with `--headless=new --disable-gpu --remote-debugging-port=9222 --user-data-dir=<a temp folder> --hide-scrollbars`.
2. From Node (version 22 and later ships a global WebSocket, so this takes zero dependencies): GET `http://127.0.0.1:9222/json/list`, take the target's `webSocketDebuggerUrl`, and connect to it.
3. Over that socket, drive the page with protocol commands: `Page.navigate`, `Page.captureScreenshot`, `Runtime.evaluate`, `Emulation.setDeviceMetricsOverride`, `Emulation.setTouchEmulationEnabled`, `Emulation.setEmulatedMedia`, `Network.setBlockedURLs`, and `Input.dispatchMouseEvent`.

One gotcha: `Emulation.setTouchEmulationEnabled` rejects a `maxTouchPoints` of 0. Always send 5 and gate the behavior on the `enabled` flag instead.

## The page

| Symptom | Cause | Fix |
|---|---|---|
| An entrance animation never plays, the element just appears | A later rule won the cascade over the animation's starting state | Prefix start and end states with the container class (`.card .part`, `.card.in .part`) and prove every entrance plays. |
| Hovers on the 2nd and 3rd items of a staggered grid respond late even after the entrance finished | The cleanup rule that zeroes the stagger `transition-delay` has lower specificity than the nth-child delay rules it retires | Make the cleanup selector match or beat the delay rules (repeat the nth-child in it) or put `!important` on the `0s` delay. |
| A scroll-driven style stops responding after its entrance | `animation-fill-mode: forwards` overrides it forever | Entrance animation on the parent, dynamic style on a child. |
| A background loop flashes or snaps when it starts | Positive animation delay | Negative delays (like `-1.2s`) so every loop is mid-cycle at first paint. |
| Letter tails (g, y, p) are cut off | Masked or clipped text with zero breathing room | Em-based padding with matching negative margins on the mask (see `scrub-pipeline.md`). |
| The page can be dragged or shifted sideways | `overflow-x: hidden` alone, or a decoration poking past the edge | `overflow-x: clip` on BOTH `html` and `body`, `hidden` first as fallback. |
| Hovers start snapping after a script runs | JavaScript overwrote `el.style.transition` | Toggle a class that declares the full combined transition instead. |
| A mobile element sits off-screen with reduced motion on | Blanket `transform: none !important` wiped its positional transform | Re-apply positional transforms per breakpoint inside the reduced-motion block. |
| Animations run while the tab is hidden or the section is off-screen | Free-running loops | Scope animation rules to a class an IntersectionObserver toggles; on `visibilitychange` toggle one body class with `body.paused *, body.paused *::before, body.paused *::after { animation-play-state: paused !important }`; rAF loops rest when converged. |

## Deploy

| Symptom | Cause | Fix |
|---|---|---|
| Live site shows a directory listing or 404 | The zip contains the project folder, not its contents | Re-zip with `index.html` at the zip's top level. |
| Images look soft on the live site but sharp locally | The host resizes and recompresses images server-side | Upload larger and cleaner (about 1920px wide, one high-quality pass) so the host's pass is the only lossy step. |
| Link previews show no image or the wrong URL | og tags still carry the placeholder | Patch `og:image` and `og:url` with the live absolute URL at the `<!-- DEPLOY STEP -->` comment, re-zip, re-deploy. |
| The live site still shows the old version after a re-deploy | Cache | Hard-refresh, or verify against a string you know changed. |
| Raw videos or review files appear on the live site | They were inside the deploy folder when it was zipped | Keep raws and review copies OUTSIDE the deploy folder, always. |
| Special characters turn to gibberish after a scripted find-and-replace | A shell command read the UTF-8 file with the wrong default encoding and wrote the damage back | Never patch site files with plain shell read and write: use the editor tool for the og patch and any text change, or read and write with explicit UTF-8. |

## Setup

| Symptom | Cause | Fix |
|---|---|---|
| Google Flow tools not responding | Flow session disconnected or MCP server not running | Call `flow_status` to diagnose. Ensure Chrome is running with Flow open at flow.google and signed in. |
| ffmpeg or Node.js fails to install on a Mac | Homebrew is not installed | Have the user install Homebrew first (brew.sh). Then run installs again and verify. |
| Hostinger connector command fails on Windows | `npx` instead of `npx.cmd` | Windows uses `claude mcp add hostinger -- npx.cmd -y hostinger-api-mcp` (or equivalent tool config). |

**The two-hang rule (the subsystem rule, for any tool):** a second consecutive hang on the same tool or subsystem means it is down. Stop retrying, name it out loud, and restart. Never make a third call into a hung subsystem.