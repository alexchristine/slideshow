# Drive Slideshow

A full-screen photo slideshow that plays a Google Drive folder. It's a single page
(`index.html`) with no build step.

## Run it

Double-click `start.cmd`. It serves the folder at http://localhost:8080 and opens
your browser. Google sign-in only works from an `http://localhost` (or https) address,
so opening `index.html` straight from disk won't work for the Drive sources.

## Use it on all your devices (GitHub Pages)

1. Sign in at https://github.com, click **+ → New repository**, name it `slideshow`,
   choose **Public**, and click **Create repository**.
2. On the new repo page, click **uploading an existing file**. Drag in every file from
   the `github-upload` folder, then click **Commit changes**.
3. Go to **Settings → Pages**. Under *Build and deployment*, set Source to
   **Deploy from a branch** and Branch to **main** / **(root)**, then **Save**. After about
   a minute the site is live at `https://<your-username>.github.io/slideshow/`.
4. In Google Cloud Console, allow the new address:
   - API key: add `https://<your-username>.github.io/*` under *Website restrictions*.
   - Sign-in client, if you use it: add `https://<your-username>.github.io` under
     *Authorized JavaScript origins*.
5. Open the site on one PC, fill in Settings, and use **Copy setup link** (or **Share…**
   on a phone). Open that link on each other device, and everything is filled in.
6. To install it:
   - **Android:** open the site in Chrome, then **⋮ → Add to home screen → Install**.
   - **PC (Chrome or Edge):** click the install icon in the address bar.

   It launches full screen with its own icon.

   On a PC the installed app opens without a title bar. The photo fills the whole window,
   and only the minimise / maximise / close buttons float in the top-right corner. Drag
   the top edge of the window to move it, and drag its edges to resize it. To get the title
   bar back, use the small ⌃ arrow next to those buttons.

To update later, upload the changed files to the repo again. Devices pick up the new
version the next time they open the app.

Your API key never goes into the repo. It lives only in each device's settings and in
the setup link, so only send that link to yourself.

## Connect Google Drive (one-time, ~5 minutes)

You need your own Google Cloud OAuth client so the page can read your Drive.

1. Go to https://console.cloud.google.com/ and create a project (any name).
2. **APIs & Services → Library** → search **Google Drive API** → **Enable**.
3. **Google Auth Platform** (formerly *OAuth consent screen*):
   - Audience: **External**, publishing status **Testing**.
   - Add your own Google account under **Test users**.
   - Data access: add the scope `https://www.googleapis.com/auth/drive.readonly`.
4. **Clients → Create client** → type **Web application**.
   - Authorized JavaScript origins: `http://localhost:8080`
     (add any other address you'll host it at).
5. Copy the client ID (`…apps.googleusercontent.com`).
6. In the slideshow, open **Settings**: source **Google Drive — sign in**, paste the
   client ID and the folder's link, then **Apply & restart** and sign in.

Google shows an "app isn't verified" warning because the app is yours and in testing.
Click **Continue**. Access is read-only.

**About sessions.** Google's browser tokens last an hour. The page tries to renew on its
own. If the browser blocks that, the show keeps playing photos it already has in memory
and shows a **Reconnect** button. For an unattended display like a TV or picture frame,
the API-key source below never expires.

### Alternative: API key + link-shared folder

1. In the same project, **Credentials → Create credentials → API key**. Restrict it to
   the Google Drive API (and to your site's address, if you like).
2. Share the Drive folder as **Anyone with the link → Viewer**.
3. In Settings choose **Google Drive — API key** and paste the key and the folder link.

Anyone who has the folder link can see the photos, so only use this for photos you're
comfortable sharing.

## Settings

| Setting | What it does |
|---|---|
| Seconds per photo | How long each photo stays up. A pan runs for this whole time, then the next photo comes in. |
| Display mode → **Pan** | Portrait photos are scaled to the window's width and pan top → bottom → top. Landscape photos are scaled to the window's height and pan left → right → left. A photo with nothing left to pan, such as a 3:2 photo on a 16:9 screen, is shown fitted. |
| Pan direction | **By photo orientation** (default) works as described above. **By window shape** pans whichever side overflows the window instead, so the screen is always filled. |
| Pan speed / pause at each edge | How fast the photo moves, as a share of the screen per second, and how many seconds it rests at each edge. It keeps sweeping back and forth until the photo's time is up. |
| Only pan when overflow ≥ | Photos that would barely move are shown fitted instead. |
| Display mode → **Fit** | The whole photo, never cropped, scaled to the window. The letterbox space is black or a blurred copy of the photo. |
| Order → **Shuffle** | Fully random. Every photo plays once per round before any repeats. |
| Order → **Curated random** | Random, but newer photos come up more often. A photo's chance halves every *half-life* of age, measured from your newest photo, and never drops below the *floor*. Recently shown photos are skipped. The help text shows the resulting odds. |
| Photo date | Date taken from the camera's EXIF data, falling back to the upload date. Or use the date added to Drive or the date modified. |
| Check for new photos | Re-reads the folder on a timer, so new uploads show up without a reload. |

Everything adapts to the window live: resizing, rotating a tablet or going fullscreen
recalculates the current photo's fit and pan without restarting it.

Click or tap anywhere to pause. The screen blurs so the photo can't be made out, and a big
clock with the date appears in the middle. You can turn the blur off in Settings. Click or
tap again to resume.

A small clock can also stay in a corner while the photos play: time only, or time and date.
Set it under Settings → Presentation, along with its corner and whether it's 12- or 24-hour.

Keys: `Space` pause · `←` `→` previous/next · `F` fullscreen · `S` settings · `D` frame-rate
meter. Swipe left or right on touch screens to change photos.

## Notes

- With the API key source, photos load as Drive's own previews sized for your screen. The
  full original is downloaded only if those fail. Google rate-limits full-size downloads
  through API keys, and the previews are much smaller and faster.

- HEIC photos (iPhone) can't be decoded by Chrome, Edge or Firefox. For those, the app
  shows Drive's rendered JPEG preview at screen resolution.
- **Folder on this computer** is also available, for trying the app without Drive.
- Settings are saved in the browser (`localStorage`). Your client ID and API key stay on
  your machine.
