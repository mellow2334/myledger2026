# Ledger — Project & Account Tracker

A single-file web app for tracking which Claude/Google account each project is assigned to, with reassignment history. No build step, no server code — works on GitHub Pages.

## Host it on GitHub Pages (2 minutes)

1. Create a new GitHub repo (public or private, either works for Pages on a paid plan; public is required on the free plan).
2. Upload `index.html` to the repo root.
3. Go to **Settings → Pages** → under "Build and deployment", set **Source: Deploy from a branch**, branch: `main`, folder: `/root`. Save.
4. Wait ~1 minute, then your app is live at `https://<your-username>.github.io/<repo-name>/`.
5. Open that URL on your phone and add it to your home screen (Share → Add to Home Screen) so it feels like an app.

## One-time setup per device: connect a free sync backend

This app has no backend of its own, so to make your passcode work the same way on your phone *and* PC, it needs somewhere to store data that both devices can reach. The app uses **Firebase Realtime Database** (Google's free tier — no credit card needed) for this. You only set this up once, and you can reuse the same Firebase project across all your devices.

1. Go to `console.firebase.google.com` and sign in with any Google account.
2. Click **Add project** → give it any name → finish creation (you can skip Google Analytics).
3. In the left sidebar: **Build → Realtime Database → Create Database**.
4. Pick any location, choose **Start in test mode**, click **Enable**.
5. Copy the database URL shown at the top of the page (looks like `https://your-project-default-rtdb.firebaseio.com`).
6. Open the Ledger app — on first load it asks for this URL. Paste it in.
7. On your second device (PC or phone), open the app and paste the **same URL**. Both devices now read/write the same backend.

The app also walks you through this in-app via the "Show me how" link on first launch.

### Important: test mode expires after 30 days

Firebase's test mode leaves the database open to anyone with the URL for 30 days, then locks it. Since this app doesn't do real login, you'll want to keep it open (but access is still gated by your app passcode + the fact that no one else knows your database URL). To do that permanently:

1. In Firebase console, go to **Realtime Database → Rules**.
2. Replace the rules with:
   ```json
   {
     "rules": {
       ".read": true,
       ".write": true
     }
   }
   ```
3. Click **Publish**.

**Security note:** this setup is convenience-level privacy, not bank-level security — anyone who has both your database URL and your passcode could read/write your data. Don't publish your database URL publicly (e.g. don't commit it into the GitHub repo itself — it's stored only in each device's browser via localStorage). Since this is just project/account metadata (not passwords or sensitive content), that tradeoff is reasonable for this use case.

## How to use it

1. **First launch on a device:** paste your Firebase database URL (see above). This is stored locally per-device — you only do it once per device.
2. **Enter a passcode:** type any passcode you'll remember. The first time you use a given passcode, it creates a fresh empty space. Typing the same passcode later (on any device with the same Firebase URL configured) loads that same space.
3. **Add accounts:** under "Accounts," name each Google/Claude account you use (e.g. `work@gmail.com`) and give it a color.
4. **Add projects:** name the project, optionally assign it to an account right away, and optionally add a short description.
5. **Reassign:** expand any project, pick a different account from the "Reassign" dropdown, and click **Move**. This is logged automatically with a timestamp.
6. **History:** every project's expanded view shows a timestamped log — when it was created and every reassignment since.

## Files

- `index.html` — the entire app (HTML/CSS/JS, no dependencies except Google Fonts via CDN)
