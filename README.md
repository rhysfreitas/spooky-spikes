# Spooky Spikes scoreboard

Scoring app for the Spooky Spikes Halloween volleyball tournament (Oct 25): pool play, standings, and playoff brackets for the women's and men's divisions.

## Going live

`index.html` is the whole app. Without Firebase settings it runs in demo mode, where scores stay on one phone. With Firebase settings, every phone sees scores live. Setup takes about 10 minutes and is free.

## 1. Create the database (Firebase)
1. Go to https://console.firebase.google.com and click **Create a project**. Name it something like `spooky-spikes`. Google Analytics can be turned off.
2. In the left menu, open **Build > Realtime Database > Create Database**. Pick the US location and choose **Start in test mode**.
3. Open the **Rules** tab, replace what's there with the rules below, and click **Publish**:
   ```json
   {
     "rules": {
       "spooky-spikes-2026": { ".read": true, ".write": true },
       "$other": { ".read": false, ".write": false }
     }
   }
   ```
   Test mode switches itself off after 30 days. These rules don't, and they keep the database open to anyone, which is how "anyone can enter and edit" works.
4. Click the gear icon, then **Project settings**. Under **Your apps**, click the **</>** (Web) icon, register an app (skip Hosting), and copy the `firebaseConfig` object it shows you.

## 2. Paste the config into the app
Open `index.html` in a text editor and find this line:
```js
const FIREBASE_CONFIG = null;
```
Replace `null` with your config. It must include `databaseURL`:
```js
const FIREBASE_CONFIG = {
  apiKey: "AIza...",
  authDomain: "spooky-spikes.firebaseapp.com",
  databaseURL: "https://spooky-spikes-default-rtdb.firebaseio.com",
  projectId: "spooky-spikes",
  appId: "1:...:web:..."
};
```
If `databaseURL` is missing, copy it from the top of the Realtime Database page. The apiKey is safe to put in a public page because it only identifies the project.

## 3. Hosting (GitHub Pages)
The app is served from this repo by GitHub Pages: **Settings > Pages > Deploy from a branch > main, / (root)**.
Live link: https://rhysfreitas.github.io/spooky-spikes/

To update, replace `index.html` in this repo. The link stays the same.

The status dot in the header should read **Live**. If it reads "This device only", the config is missing or wrong.

## After the tournament
In Firebase, change `".write": true` to `".write": false` in the rules. Results stay viewable but can no longer be changed.

## Before the 25th
- Test with two phones. Enter a score on one and watch it show up on the other.
- Clear the test scores with **Organizer tools > type RESET > Clear all scores**.
- With no connection, phones keep entered scores and sync them when signal returns.

## How the app works
- **Pool play:** your 8-round schedule. Women's Pool A plays on courts 1–2 and Pool B on 3–4; Men's Pool A on 5–6 and Pool B on 7–8. Players can filter the schedule to their own team.
- **Scores:** one game to 21, win by 2, no cap. Impossible scores (for example, 24–20) trigger a warning, and there's a **Save anyway** option for unusual cases.
- **Standings order:** record, then point differential, then head-to-head among the tied teams, then points scored. If teams are still tied after all of that, the Standings view asks the organizer to flip a coin and tap in the result.
- **Bracket:** fills in once each pool is complete. Pool winners get a bye. QF1 is A2 vs B3 and QF2 is B2 vs A3. In the semis, A1 plays the winner of QF2 and B1 plays the winner of QF1. Then the Final and the 3rd-place match.
- **Corrections:** if a pool score changes after the bracket has started, any playoff game whose teams changed goes back to unplayed.
- **Team changes:** to change a team name, edit the `DIVS` list near the top of the script.
