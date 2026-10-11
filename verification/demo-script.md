# Demo video script — YT-CLI

Google's reviewer watches this video to confirm three things: the app is the
one named on the consent screen, the consent screen shows the real
`youtube.readonly` scope, and the app uses that scope for the purpose it
claims. Record it in one unbroken take. Target length: 60–90 seconds.

## Before you record

1. Set the recording to **1080p or better**, and make the terminal font large
   enough to read in a compressed upload.
2. Set the browser language to **English**. If the consent screen still opens
   in another language, append `&hl=en` to the authorization URL.
3. Make sure the **browser URL bar is visible and not cropped**. The reviewer
   has to read the `client_id` in it. Do not hide the address bar.
4. Sign the browser into the Google account that is on the test-user list.
5. Start clean so the consent screen actually appears:

   ```sh
   YT-CLI logout --forget-account
   rm -rf ~/.yt-cli
   ```

6. Revoke any old grant at https://myaccount.google.com/permissions, so Google
   shows the full consent screen rather than a silent re-approval.
7. Turn off notifications and hide anything private on screen.

## The take

### Step 1 — Start the TUI

- Show the terminal. Type and run:

  ```sh
  YT-CLI login
  ```

- Say, on screen or in narration: *"This is YT-CLI, a read-only YouTube client
  for the terminal."* The spoken name must match the name on the consent screen
  exactly: **YT-CLI**.

### Step 2 — Show the full consent screen

This is the part the reviewer grades. Do not rush it.

- The browser opens Google's sign-in. Let it load fully.
- **Hold on the URL bar for 3 seconds**, so `accounts.google.com/o/oauth2/...`
  and the `client_id=` parameter are both readable. Zoom in if your recorder
  can.
- Pick the Google account.
- The consent screen appears with the app name **YT-CLI** and its logo. Hold
  for 3 seconds.
- Scroll through the **whole** screen so every requested permission is visible.
  Do not cut away. The reviewer must see:
  - *"See, edit, and permanently delete your YouTube videos, ratings, comments
    and captions"* — no, this is **not** the one you request. If you see this
    line, you are on the wrong scope; stop and fix the client.
  - The line you must show is: **"View your YouTube account"** —
    `https://www.googleapis.com/auth/youtube.readonly`.
- If Google shows the "YT-CLI wants access to your Google Account" summary with
  a *What this means* expander, open it, so the scope text is on screen in
  full.

### Step 3 — Grant access

- Click **Continue** / **Allow**.
- Show the browser returning to the success page, then switch back to the
  terminal and show the confirmation that the login succeeded.

### Step 4 — Show the subscription list in the TUI

- Run:

  ```sh
  YT-CLI feed --refresh
  ```

- The grid appears. Hold for 5 seconds so the reviewer can see real
  subscription channels and their uploads. This is the direct proof that the
  scope is used for the stated purpose.
- Move the selection with the arrow keys to show it is a live TUI.
- Press `q` to exit.

### Optional closing shot (recommended)

- Open `https://paul-h-w-j.github.io/` and hold on the homepage and its
  privacy-policy link for 2 seconds. It ties the video to the URLs on the
  consent screen.

## After you record

1. Upload the video to YouTube. **Unlisted** is accepted; private is not.
2. Paste the link into the Google Cloud verification form, in the demo-video
   field.
3. Keep the video online until verification finishes. A removed video fails
   the review.

## Checklist before submitting

- [ ] Video is in English.
- [ ] The URL bar with `client_id=` is readable.
- [ ] The app name on screen is **YT-CLI**, the same as the consent screen,
      the homepage title, and the Google Cloud project's app name.
- [ ] Every requested scope is visible, including `youtube.readonly`.
- [ ] The grant is completed on camera.
- [ ] The subscription grid is shown after the grant, in the same take.
- [ ] The video is unlisted or public on YouTube.
