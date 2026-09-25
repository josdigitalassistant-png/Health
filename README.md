[README.md](https://github.com/user-attachments/files/32647589/README.md)
# Almanac — clickable prototype

A clickable test version of **Almanac** (working name), a private daily health log. It covers cycle, mood, sleep, movement, symptoms, allergies, alcohol, medication, headaches and a diary, plus a calendar, trends and PDF reports.

**Live link:** https://YOUR-USERNAME.github.io/almanac-prototype/

> ⚠️ **Prototype only. Please don't enter real health data.**
> Accounts and entries are saved only in the browser you use. They aren't sent anywhere, backed up or encrypted, and clearing browser data deletes them.

## Trying it

1. Open the link on your phone (or a desktop browser).
2. Create an account, or tap **Try the demo account** to see about 6 months of sample data.
3. On iPhone: Safari → **Share → Add to Home Screen** opens it like an app.

## What to test

- **Today:** logging each section, adding and removing tags, regular vs as-needed medication, several exercises in a day.
- **Calendar:** period days and predictions, tapping a day, opening a past day to edit.
- **Trends:** 14 / 30 / 60-day views, widgets, patterns.
- **Reports:** Cycle report and Migraine report → **Save PDF**.
- **Account:** tap your initial (top right of Today) → Log out, then sign back in.

## Feedback

Open an **Issue** in this repo, with a screenshot where possible and the phone and browser you used.

## Status

| | |
|---|---|
| Version | 0.3.2 (shown on the sign-in screen and in the account sheet) |
| Stage | Design prototype, not the production app |
| Sign-in & storage | Google or email-link sign-in via Firebase; entries sync to a private cloud account (Firestore, EU) and stay on the phone for offline use. Demo and device-only accounts stay in the browser |
| Strava | Shown in the design, not connected |
| Next | Production build with React Native (Expo) and Firebase |

## Updating

`index.html` is a single file exported from the design tool. To update, replace it with the new export and commit.
