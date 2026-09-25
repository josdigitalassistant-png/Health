[CHANGELOG.md](https://github.com/user-attachments/files/32647579/CHANGELOG.md)
# Changelog

## Unreleased

## 0.3.2 — 2026-09-25

- Movement: the “Strava connected” line now shows only on the demo account (Strava isn’t connected for real accounts yet).
- Movement: Strava workouts show the activity name and start time, plus average/max heart rate and elevation when recorded. Manual runs, walks, cycles and swims can have an optional distance.
- Movement: when a Strava workout matches a manual one of the same sport that day, the app asks whether to merge them or keep both.
- Data model v5: `title`, `start`, `avgHr`, `maxHr`, `elev`, `keepSeparate` on Workout; `km` on manual workouts.

## 0.3.1 — 2026-09-25

- Allergies: new 1–5 severity scale (mild → severe), shown in the section summary and the Calendar day summary.
- Data model v3: `allergySev` on Day.
- Settings → Your data: Export backup (a JSON file; uses the share sheet on phones so you can save it to Files or iCloud) and Import backup (merge or replace). No stored fields changed.
- Accounts now sync: sign in with Google or an emailed link (no password), and your entries are saved to your own private cloud account and kept on the phone for offline use. Existing entries on the phone are uploaded on first sign-in. Device-only accounts and the demo are still available.
- Data model v4: cloud layout `users/{uid}` + `days`; new localStorage keys for cloud sessions and email-link sign-in.

## 0.3.0 — 2026-09-24

- Mobile: removed the see-through bar pinned at the top, so content starts under the phone’s own status bar.
- Today: new Alcohol yes/no card, and a new Allergies section (itchy eyes, runny nose, sneezing… plus your own tags and a note). Both show in the Calendar day summary; alcohol days also feed the Alcohol theme in the Migraine report.
- Settings: “Sections on Today” — switch any section off per account. Hidden sections drop out of Today and the shortcuts; past data is kept.
- Data model v2: `alcohol`, `allergy`, `allergyNote` on Day; `allergy` tag group; `hiddenSecs` setting.

## 0.2.0 — 2026-09-23

**Look & feel**
- New colour palette: Dusk, Sunset, Pink, Goldenrod, Morning Sea (mint) and Periwinkle.
- Watercolour ribbon background. Choose Palette, Periwinkle, Mint, Pink or Off.
- Today starts straight with the date. The mood strip and the "logged" count are gone.

**Settings (per user)**
- New Settings section in the account sheet: background colour, "Start sections collapsed" and "Show section shortcuts".
- Version number shown on the sign-in screen and in the account sheet.

**Today**
- Tag editing now has a clear Done button, with "Tap a tag to remove it" shown while editing.
- Sports: Run, Walk, Yoga, Swim, Cycle, Weights, plus "+ add" for your own.

**Calendar**
- The likely first day of your next period is circled with a dotted line, and the rest of the predicted period gets a light fill.
- A green dot marks movement days and an "M" badge marks migraine days.
- A selected period day keeps a pink ring.
- Plain wording ("Cycle day 3 · period", "next period around Mon 12 Oct") instead of phase names.

**Reports**
- Cycle report: new Medication chart, one colour per medication, plus findings like "You took Progesterone mostly on days 15–28".
- Migraine report: clearer legends, with colour scales for mood, sleep and stress, day labels on every strip, and a key for the "usual level" line.

## 0.1.0

- First clickable prototype: Today log, Calendar, Trends, Cycle and Migraine reports (PDF), sign-in and log out, medication, Strava placeholder.
