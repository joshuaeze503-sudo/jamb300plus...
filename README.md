# JAMB 300+

A native Android study planner for the four JAMB subjects.

Built with Kotlin, Jetpack Compose and Material 3. Single `:app` module, manual dependency
injection, no Hilt, no chart library (rings are drawn with Compose Canvas), no WebView.

---

## What it does

| Area | Details |
|---|---|
| Onboarding | Persisted. Use of English is compulsory and locked; exactly 3 more unique subjects. Target score, exam date, reminder setup. |
| Syllabus tracker | 14 subjects with topic structures, 4 statuses (Not started / Learning / Done / Needs revision), per-subject and overall progress, search, custom topics. |
| Real past questions | Fetched from ALOC with **your own** API key. Cached in Room, de-duplicated, year/source/explanation shown only when the provider supplies them. |
| CBT mock | 60 English + 40 each of 3 subjects = 180 questions, 2 hours, absolute end timestamp, auto-submit, palette, flags, restoration after process death. Scored out of 400. |
| Practice | Untimed, by subject, with a year filter that only appears when real year metadata exists. |
| Mistake notebook | Wrong answers saved with source metadata, spaced review at 1 / 3 / 7 / 14 days, repeated misses raise Sunday revision priority. |
| Schedule | 5 default slots (editable), generated toward the exam date, regenerated on demand, unfinished tasks roll forward. **Sunday is revision and past-question practice only.** |
| Reminders | AlarmManager alarms before each slot with configurable lead time, Start / Snooze / Skip actions, rescheduled after reboot, schedule changes and time changes. |
| Focus timer | Pomodoro (25 min) and full-slot (60 min) modes with pause/resume/stop. Completed time is stored against the subject and topic. |
| Progress | Syllabus completion, streak, XP, rank (Beginner → 300+ Champion), mock history, mistake count. |
| Score predictor | Deterministic 0–400 estimate from your own coverage, mock results and consistency. Explicitly labelled an estimate. |
| Backup / restore | JSON export and import with validation. **The API key is excluded unless you explicitly opt in.** |

## Honesty rules this app follows

- No bundled API key. No key in `BuildConfig`, resources or assets. The key never gets logged.
- No generated, invented or substituted past questions. If the network fails, you get a real
  error state with Retry — never a fake question.
- No fabricated years, sources or explanations. If the provider did not send them, the app says so.
- The bundled topic structures are a study checklist. They are **not** the official JAMB
  syllabus for any year — use *Official Updates* to confirm on jamb.gov.ng.
- Exam dates, registration dates, fees and syllabus versions are never hardcoded. They come
  from a configuration URL you control, or display `Unconfirmed — check jamb.gov.ng`.
- Raw server errors and secrets are never shown in the UI.

## Stack

Kotlin 2.0.21 · Jetpack Compose (BOM 2024.10.01) · Material 3 · Room 2.6.1 (KSP) ·
Retrofit 2.11 + Moshi 1.15 (hand-written defensive `JsonReader` parsing) · DataStore
Preferences · WorkManager-ready · AlarmManager · coroutines · `minSdk 26` · `compileSdk 34`

## Build it

```bash
./gradlew assembleDebug
# -> app/build/outputs/apk/debug/app-debug.apk
```

Requires JDK 17 and an Android SDK with platform 34.

## Questions source

Primary:

```
GET https://dev.aloc.com.ng/api/v1/questions?subject={subject}&examType=jamb&year={year}
X-API-Key: <your key>
```

Fallback:

```
GET https://questions.aloc.com.ng/api/v2/m?subject={subject}
AccessToken: <your key>
```

Subject codes are editable in **Settings → ALOC subject codes** so you can match whatever
scheme your key uses.

Questions are credited to ALOC (aloc.com.ng) in the About screen.
