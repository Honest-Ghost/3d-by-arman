# The Hero-Video Laws and Google Flow Prompt Construction

Design every shot by these laws BEFORE generating. They predict which concepts land first-try and which waste credits. Each one came from a real build that either succeeded because of it or failed without it.

## The twelve laws

1. **The motion agrees with the scroll.** Scrolling down must read as going down, opening up, or arriving: a pour, a descent, a teardown, an approach. Ask of every concept: "when the visitor scrolls down, does this motion feel like down?" A subject that flies UP while the visitor scrolls down fights the page and always loses.

2. **One subject, one continuous motion, no cuts.** A single subject travels through a single journey. Do not ask AI video to turn one thing into a different thing: transformations between two subjects are the hardest shot in AI video and burn credits. If the user insists on one, hide the swap inside a flash or cloud moment, keep one unbroken trajectory (same heading, same position, same speed on both sides of the swap), and expect retries.

3. **Lock the path, free the body.** The trajectory stays rigid, but the subject on it must stay alive: natural movement, ripple, small adjustments. The scene needs life too: drifting steam, streaking cloud, shifting light. Never stabilize a shot by freezing the subject. A frozen subject on a clean path reads as dead footage.

4. **Plan the ending first.** The final frame is where the page comes to rest, so write it into the prompt explicitly as a composed, satisfying arrival: the cup settled on the counter, the product assembled, the destination reached. An awkward ending makes an awkward website. If the ending is a product showcase, compose it with generous margin above and below the whole product: the site's header sits over the top of the frame, and cover-cropping eats the edges on wider or shorter screens. A product with its top cut off reads as an accident; squeezed against the nav it reads as busy. The alternative that dodges the problem entirely: a full-bleed texture ending with nothing croppable is text-safe on every screen. Verify by viewing the ending frame with the header mocked over it, at a wide window and a short one, before approving.

5. **Choose forgiving subjects.** Fluids, mist, steam, light, fabric, and distant silhouettes render beautifully. Anything whose exact anatomy every viewer knows up close (controllers, keyboards, hands, familiar animals in detail) will show its errors instantly. Keep risky detail distant, simple, and moving.

6. **Prefer a vertical motion axis.** A straight up-and-down journey matches the scroll axis one to one, so the reveal moves with the page in both directions. Not mandatory, but when two concepts are equal, take the vertical one.

7. **Compose for the layout.** Decide during concept design where the action sits in frame, and leave intentional negative space for the page's captions and story to live in. The action lane stays clear; the words flank it.

8. **Sell the boundary crossings.** When the camera passes through a surface (into water, through mist, past glass), write the physical lens moment into the prompt: a splash, droplets on the lens, a beat of blur. A clean pass through a boundary reads as fake; the mess is the realism.

9. **If the hero features a product, brand it or frame it close.** A generic unbranded object at distance reads as a placeholder. Either apply the brand mark via image editing before animating, or write the ending to land close enough that the design carries it.

10. **Text over footage earns its legibility.** Live video behind type is a moving background you do not control frame to frame. So every text band gets a legibility system: a local scrim (a soft dark gradient behind the words) that deepens only while that band is active, a real text shadow, and a contrast check against the WORST frame of that band, never the average. Place each band in the calmest region of its frames. If a line cannot be read at a glance over the busiest moment of its band, it fails. The working system, with code and the audit, is in `scrub-pipeline.md`.

11. **Pace text in scroll distance, not seconds.** A scroll site is read in flicks, not played at 24 frames per second. Give every caption beat a long fully-visible plateau (most of its band, enough to survive several normal scroll flicks) with short eased ramps at the edges, so a reader never sees text pop in and vanish between two flicks and never has to stop dead to catch a line. Test the beat map by flick-scrolling like a real visitor, not by slow dragging. The exact numbers and the flick test are in `scrub-pipeline.md`.

12. **The standing guards:** write "no text, no logos, no lettering anywhere" into every image and video prompt. Decline default presets when you have a designed shot. And never build the site around footage the user has not approved: silent scaffolding during a render wait is fine, but nothing gets shown or finished until the video passes its gate.

## Google Flow prompt construction

### Start frame template (Image: Nano Banana, 16:9, 0 credits / free)

Compose the image as frame one of the motion: the subject positioned so the journey can begin.

```
[SUBJECT] at [POSITION IN FRAME], composed as the first moment of a motion
that will [ONE-SENTENCE JOURNEY]. [LIGHTING: source, direction, mood].
[PALETTE: the three to five brand colors described as materials and light,
not hex codes]. [ATMOSPHERE: the ambient life the scene carries]. Intentional
negative space at [WHERE THE CAPTIONS WILL LIVE]. Cinematic, photorealistic,
16:9. No text, no logos, no lettering anywhere.
```

Call `flow_generate` with:
- `type: "image"`
- `aspect_ratio: "16:9"`
- `download_quality: "upscaled"`
- `prompt`: the text above

**The negative-space phrasing trap: never name empty space as darkness or emptiness.** When you reserve room for captions, describe the scene as one continuous world filling the frame edge to edge, with the calm region as part of that world: soft shadow, receding depth, a plain surface. Ask for "generous empty darkness left and right" and the model paints literal black side panels, which costs a re-roll. Edge-to-edge phrasing lands first try.

If the user supplied a real product photo, that photo can be the start frame instead. Inspect it for resolution and composition first, and confirm the negative space works for the layout.

### Video template (Image-to-Video via Google Flow, 1080p, 6 to 8 seconds)

```
One continuous shot, no cuts. [SUBJECT] [VERB OF THE JOURNEY: pours, descends,
approaches, assembles] from [START STATE] to [END STATE] along [THE EXPLICIT
TRAJECTORY: straight down the center of frame, a slow forward push, etc].
The [SUBJECT] stays alive throughout: [SMALL NATURAL MOTION: ripple, sway,
micro-adjustments]. The scene stays alive: [AMBIENT LIFE: drifting steam,
shifting light, streaking cloud]. [IF A BOUNDARY IS CROSSED: the physical
lens moment, e.g. a splash and droplets on the lens with a beat of blur].
The shot ends at rest: [THE COMPOSED FINAL FRAME, fully described: what sits
where, what the light does, why it feels arrived]. No text or lettering
anywhere.
```

Call `flow_generate` with:
- `type: "video"`
- `first_frame`: absolute file path to the approved starting image (or `asset:<title>`)
- `duration`: 6 or 8 (Omni models support 4, 6, 8, 10; Veo 3.1 is fixed at 8s)
- `aspect_ratio`: `"16:9"`
- `download_quality`: `"upscaled"` (downloads 1080p video)
- `technique`: (optional) id from `flow_techniques` (e.g. camera move)
- `prompt`: the prompt above

## Flow camera techniques

Google Flow provides native camera and cinematic motion techniques via `flow_techniques`. Use `flow_techniques` with category `"camera"` or `"product"` to discover available presets (e.g. `orbit-360`, forward push, slow tilt). You can pass the chosen technique id directly in `flow_generate` scene parameter `technique: "<id>"`, and Flow automatically appends the exact tested phrasing to your prompt.

## The chaining recipe (Tier 2: a 15 to 20 second scroll journey)

Google Flow makes multi-segment journeys seamless without manual upload bridges or presigned URLs:

### Method A: Native scene chaining in `flow_generate` (Recommended)
You can queue consecutive scenes in a single `flow_generate` call:
1. Scene 1 has `first_frame: <path_to_start_image>` and your first prompt.
2. Scene 2 has `chain_previous: true` and the continuation prompt. Flow automatically extracts Scene 1's real final frame and animates Scene 2 directly from it!
3. Scene 3 has `chain_previous: true` and the final arrival prompt.

### Method B: Interactive step-by-step chaining with `flow_continue`
When you want to gate each segment individually before spending credits on the next:
1. Generate segment 1 from the start frame. Inspect and pass the ⛔ VIDEO GATE.
2. Call `flow_continue` with:
   - `from_asset: "<segment-1-title>"`
   - `mode: "exact"` (uses Flow's first-frame I2V animation so the cut opens on the exact real last frame)
   - `prompt`: continuation prompt describing the next stage of motion.
   - `download_quality`: `"upscaled"`
3. Gate segment 2.
4. Repeat for segment 3 with the composed resting ending.

### Method C: Local frame extraction with ffmpeg
Extract the final frame of segment N as a lossless PNG:
```
ffmpeg -sseof -0.1 -i seg1.mp4 -update 1 -frames:v 1 -q:v 1 seg1-final.png
```
Pass `first_frame: "<absolute_path_to_seg1-final.png>"` into the next `flow_generate` video scene.

### Joining the segments
Join the approved segments into one file with the single-encode concat in `ffmpeg-recipes.md`: feed the raw segments into one filter and encode once with the scrub settings (`-g 8 -keyint_min 8`).

## Cost and credit management in Google Flow

Google Flow operates on Google AI credits with transparent quotes:
- **Images:** 0 credits (free Nano Banana model). Iterating on starting frames and supporting stills costs nothing.
- **Videos:**
  - **Omni 1.1 Flash:** approx. 7 to 12 credits per clip. Fast, responsive, fluid motion. Best value for iterative builds.
  - **Veo 3.1 - Fast:** approx. 20 credits per clip (8 seconds). Excellent realism and physical weight.
  - **Veo 3.1 - Quality:** approx. 100 credits per clip (8 seconds). Maximum fidelity.
- `flow_status` reports the user's available plan and remaining credits before generating.
- `max_credits` parameter on `flow_generate` acts as a safety ceiling (default 25 credits). If Flow quotes higher than `max_credits`, the job is skipped with nothing spent.