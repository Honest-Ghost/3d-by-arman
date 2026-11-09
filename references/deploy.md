# Deploy: The Hostinger Flow

One command from folder to live site. The order matters because the og tags need the live URL before the zip is made.

## The honest-costs talk (give it during setup, repeat before spending; the hosting bullet waits for Phase 10)

Credit talk happens BEFORE credits move, every time:

- **Hero starting images and supporting stills:** **0 credits (free)** using Google Flow's Nano Banana model.
- **Hero video clips:** depend on the chosen Google Flow model:
  - **Omni 1.1 Flash:** approx. 7 to 12 credits per clip (fast, fluid, highly cost-effective).
  - **Veo 3.1 - Fast:** approx. 20 credits per clip (8 seconds, rich physical weight).
  - **Veo 3.1 - Quality:** approx. 100 credits per clip (8 seconds, cinematic photorealism).
- Every generation's balance can be verified anytime with `flow_status`. Flow's safety cap (`max_credits`) prevents accidental overspending.
- Present the planned credit total in plain words to the user before queuing any video.
- **Hosting is separate**, and this bullet waits: it belongs to Phase 10, at the moment the user says they want the site online, never to setup. Any Hostinger plan with hPanel (or similar static hosting) serves a static site. The cheapest qualifying family is shared web hosting, usually a few dollars a month. Deploying and re-deploying cost nothing.

## Step 0: Connect the hosting (first deploy only)

The connector is added now, not during setup, because nothing before this moment needs it. If the hosting tools already respond (connected in an earlier project, or earlier in this one), skip to Step 1.

1. The user needs a hosting plan (this is the moment the honest-costs talk's hosting bullet gets said, if it has not come up yet). If Hostinger tools are configured, proceed; otherwise guide the user or connect via their hosting MCP / CLI.
2. Trigger sign-in with a harmless read request (e.g. listing websites). The browser opens to the host to authorize once.
3. Confirm the read call returns real data before deploying anything.

## Step 1: Settle the address, then patch the og tags

First, where will the site live? Look before asking: list the domains on the account. If one of them clearly belongs to this brand (the name matches the site you just built), propose it by name and ask: "I can see [the domain] on your account. Want the site live there, or on a free temporary address first?" Never assume the match, even when it is obvious; the domain is theirs and the choice is theirs.

Then patch the tags. `og:image` and `og:url` need absolute URLs. Find the `<!-- DEPLOY STEP -->` comment left during the build and patch both with the chosen live URL. On the temporary path, create the website on the hosting and generate the free subdomain first, so the address exists to patch with. Do all of this BEFORE zipping, or the shipped page carries dead preview tags.

Patch with the editor tool, never a shell one-liner. A scripted find-and-replace can read the UTF-8 file with the wrong default encoding and mangle every special character on the page.

## Step 2: Zip the CONTENTS, not the folder

The zip's top level must be `index.html` itself with `assets/` beside it. Zipping the project folder instead nests everything one level down and the live site shows a directory listing or a 404.

Exclude review folders, raw videos, and anything else that should not ship. If the raws were kept outside the deploy folder as instructed, the zip is just the folder's contents as-is.

Windows PowerShell example (contents, not folder):

```powershell
Compress-Archive -Path .\index.html, .\assets -DestinationPath ..\site.zip -Force
```

Keep the zip itself out of the deploy folder too.

## Step 3: Create the website, then deploy

1. If the website does not exist on the hosting yet, create it on the address chosen in Step 1.
2. Deploy the zip with the static deploy tool.
3. Large uploads are fine: expect a deploy carrying a hero video to take a few seconds per handful of MB.

## Step 4: Verify the live site yourself (before telling the user it's done)

- The page loads over HTTPS with a 200.
- The video URL itself serves (fetch it directly).
- The browser console is clean on the live URL.
- Scrub the hero on the live site: the Blob fetch should make seeking work even on hosts without Range support, but confirm it.

**A brand-new custom domain needs a settling window.** The SSL certificate (HTTPS) can take a few minutes to issue on a newly connected domain. If the browser shows a security warning right after the first deploy, explain that the certificate is still issuing, wait a few moments, and re-check.

## Step 5: The speed receipts (measure, then present)

Right after live verification, measure the live site and record the numbers:

- **Total page weight excluding the video:** A healthy build lands in the tens of KB.
- **Page load time:** Well under a second.
- **The video's size and arrival time behind the loading ring:** A single shot arrives in a few seconds while the page is already fully usable.

Measure via curl timing:
```bash
curl -s -o /dev/null -w "TTFB %{time_starttransfer}s, total %{time_total}s, %{size_download} bytes\n" https://the-live-url/
```

Present the receipts to the user: plain static files with zero backend overhead, instant poster load, and streamed video.

## Step 6: The user's real-device test

Hand the user the live link and have them test the site on desktop AND their phone on a real network connection.

## Step 7: Iterating is cheap

Change the files, re-zip, re-deploy. One command. Polish rounds cost nothing but a minute.