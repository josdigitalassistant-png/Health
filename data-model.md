# Almanac — data model

**Model version:** 1 · **App version:** 0.2.0 (+ unreleased) · **Last updated:** 2026-09-23
Machine-readable version: [`data-model.schema.json`](./data-model.schema.json)

> Keep this file and the JSON schema in step with `Almanac.dc.html`. Any change to stored fields bumps **Model version** and adds a line to the change log at the bottom.

---

## 1. Overview

```
User ─┬─ Profile            (name, email, consent)
      ├─ Settings           (background, collapsed, section shortcuts)
      ├─ Tags               (custom + hidden, per group: mood · sym · med · sport)
      ├─ Regular meds       (name, dose/timing)
      └─ Days  (1 per date) ─┬─ Fundamentals, Cycle, Mood, Sleep, Symptoms, Meds, Headache, Diary
                             └─ Workouts[]  (manual or Strava)
```

One **Day** document per user per calendar date (`YYYY-MM-DD`, the user's local date). Everything the reports and trends show is worked out from Days; nothing derived is stored.

---

## 2. Entities

### 2.1 User / Profile
| Field | Type | Notes |
|---|---|---|
| `uid` | string | Firebase Auth UID (prototype: email) |
| `name` | string | Shown in the account sheet |
| `email` | string | Lower-cased |
| `createdAt` | timestamp | Production only |
| `consentAt` | timestamp | Production only — explicit health-data consent (GDPR) |

### 2.2 Settings
| Field | Type | Default | Notes |
|---|---|---|---|
| `bg` | enum `Palette` · `Periwinkle` · `Mint` · `Pink` · `Off` | `Mint` | Background ribbons |
| `collapsed` | boolean | `true` | Sections start collapsed on Today |
| `strip` | boolean | `false` | Show section shortcut chips |
| `reminderTime` | `HH:mm` | — | Planned (V1.1) |

Defaults come from the app config until the user changes a setting.

### 2.3 Tags (per group)
Groups: `mood`, `sym`, `med` (as-needed meds), `sport`.

| Field | Type | Notes |
|---|---|---|
| `custom` | string[] | Tags the user added |
| `hidden` | string[] | Built-in tags the user removed (not used for `sport`) |

Removing a *custom* tag deletes it from `custom`. Removing a *built-in* tag adds it to `hidden`. Re-adding a hidden built-in removes it from `hidden`. Past Days keep whatever tags they recorded.

**Built-in lists (app config, not stored):**
- Mood: Anxious, Irritable, Low, Calm, Content, Overwhelmed, Energetic, Sad, Tearful, Focused, Motivated, Foggy, Restless, Numb
- Symptoms: Cramps, Bloating, Hot flash, Night sweats, Brain fog, Fatigue, Breast tenderness, Joint pain, Nausea, Breakouts, Cravings
- As-needed meds: Ibuprofen, Paracetamol, Antihistamine
- Sports: Run, Walk, Yoga, Swim, Cycle, Weights
- Fundamentals (fixed, 5): 3 main meals & 3L water · Connected with someone you love · Moved your body outside, 45 min · 1 hour of play, no screens · Honoured your needs (eat, pee, sleep)

### 2.4 Regular medication
| Field | Type | Notes |
|---|---|---|
| `id` | string | Production only |
| `name` | string | Unique per user; this string is what goes in `Day.meds` |
| `dose` | string | Free text, e.g. "100mg · nightly, days 15–28" |
| `active` | boolean | Production only (hide without losing history) |

### 2.5 Day
Key: `YYYY-MM-DD`. Every field is optional; if a field is missing, it wasn't logged.

| Field | Type | Values / range | Section |
|---|---|---|---|
| `fund` | boolean[5] | Same order as the Fundamentals list | Fundamentals |
| `cycle` | enum | `None` · `Spotting` · `Ovulation` · `Period` | Cycle |
| `flow` | enum | `Light` · `Medium` · `Heavy` · `Very heavy` — only when `cycle = Period` | Cycle |
| `mood` | int | 1–5 (low → great) | Mood |
| `stress` | int | 1–5 (calm → high) | Mood |
| `moodTags` | string[] | Built-in + custom mood tags | Mood |
| `moodNote` | string | Free text | Mood |
| `sleep` | int | 1–5 (poor → great) | Sleep |
| `sleepNote` | string | Free text | Sleep |
| `moved` | boolean | `true` if any workout, `false` = explicit rest day, absent = not logged | Movement |
| `workouts` | Workout[] | See 2.6 | Movement |
| `sym` | string[] | Built-in + custom symptom tags | Symptoms & body |
| `symNote` | string | Free text | Symptoms & body |
| `meds` | string[] | Names of regular **and** as-needed meds taken | Medication |
| `headache` | enum | `None` · `Headache` · `Migraine` | Headaches |
| `diary` | string | Free text | Diary |
| `updatedAt` | timestamp | Production only | — |

### 2.6 Workout (inside `Day.workouts`)
| Field | Type | Notes |
|---|---|---|
| `id` | string | Unique within the day |
| `source` | enum `manual` · `strava` | Strava entries: sport/mins/km are read-only |
| `sport` | string | Built-in or custom sport |
| `mins` | int | Duration; manual step 5, min 5 |
| `km` | number | Strava only (optional) |
| `intensity` | int | 1–5 (gentle → intense), optional |
| `note` | string | "How it felt" |
| `stravaId` | string | Production only — for de-duplication on webhook updates |

---

## 3. Derived values (worked out, never stored)

| Value | Rule |
|---|---|
| Period start | A `Period` day whose previous day isn't `Period` |
| Cycle length | Days between consecutive starts; ignore gaps < 19 or > 45 |
| Period length | Consecutive `Period` days from a start |
| Phases | Period 1–P · Follicular P+1 to L−16 · Ovulatory L−15 to L−13 · Luteal L−12 to L (L = average cycle length, P = average period length) |
| Next period | Last start + L (default 28); the next 3 are shown |
| Symptom/med timing | Windows: first days of period, week after period, around ovulation (L−17 to L−11), second half of the cycle, 7 days before next start. A window is mentioned if it has ≥ 2 occurrences and ≥ 20% of the total |
| Migraine lead-up | Averages for days −7…0 compared with "usual" (days not within 7 days before a migraine). A finding is shown when the difference is ≥ 0.3 |
| Note themes | Keyword match on all free-text fields in the 7 days before a migraine |

---

## 4. Storage

### 4.1 Production (Firebase, planned)
```
users/{uid}                     Profile + settings
users/{uid}/tags/{group}        { custom[], hidden[] }        group = mood | sym | med | sport
users/{uid}/meds/{medId}        Regular medication
users/{uid}/days/{YYYY-MM-DD}   Day
private/{uid}/strava/token      Server-only (no client access)
```
Security rule: a user can read/write only `users/{their uid}/**`, and `private/**` is blocked for all clients. Firestore location is in the EU.

### 4.2 Prototype (browser localStorage — demo only)
Prefix `almanac-proto-v6` (bumped when the stored shape changes; the old data is dropped).

| Key | Contents |
|---|---|
| `…:users` | `{ [email]: { name, pw } }` (pw = simple hash, **not secure**) |
| `…:session` | Signed-in email |
| `…:u:{email}` | `{ data: { [YYYY-MM-DD]: Day }, custom: Custom }` |

`Custom` (prototype) = `{ mood[], sym[], med[], sport[], hidden: { mood[], sym[], med[] }, regular: [{ name, dose }], settings: { bg, collapsed, strip } }`, which maps to Tags + Regular meds + Settings in production.

**Legacy fields** (in old demo data, not used by the UI, drop when migrating): `res`, `mhTags`, `mhNote`, `sport`/`mins`/`intensity`/`moveNote` at Day level (old single-workout shape, converted to `workouts[]` on read).

---

## 5. Change log
| Model | Date | Change |
|---|---|---|
| 1 | 2026-09-23 | First documented version: Day, Workout, Tags (+ hidden, sport), Regular meds, Settings |
