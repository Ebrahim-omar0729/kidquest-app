# KidQuest — Reward Tasks 🏆

A colorful, gamified chore & reward app for families, built with Flutter. Parents
create tasks and rewards; kids complete "quests" and earn points they can redeem
— all backed by local, offline storage (Hive), so there's no backend to configure.

> **⚠️ Important — read this first**
> This project was generated in an environment with no Flutter SDK, no Android/iOS
> build tools, and no internet access. That means the `/build` folder does **not**
> contain a real compiled `app-release.apk` or `Runner.ipa` — it was not possible
> to produce genuine binaries without a real toolchain, and fake placeholder files
> would just fail to install. Everything else here is a complete, real, runnable
> Flutter source project. Follow **"Building the app"** below and you'll have real
> binaries in a couple of minutes.
>
> Also see **"iOS project note"** below — the `ios/` folder includes all the
> supporting Flutter/iOS config files (Info.plist, AppDelegate, Podfile,
> storyboards, app icon) but not a hand-authored `.xcodeproj`, since a subtly
> broken Xcode project file is worse than none. One `flutter create .` command
> safely generates/repairs it — full instructions below.

---

## What's included

- **Full Dart source** in `/lib`, organized into `models`, `services`, `providers`,
  `screens/auth`, `screens/parent`, `screens/kid`, `widgets`, and `utils`.
- **13 custom SVG illustrations** in `/assets/images` (dishes, homework, bed, toys,
  trash, pet, teeth, laundry, reading, cleaning, app logo, gift, empty state).
- **Android project scaffold** (`/android`) — Gradle build files, manifest,
  Kotlin `MainActivity`, generated launcher icons.
- **iOS project scaffold** (`/ios`) — `AppDelegate.swift`, `Info.plist`,
  `Podfile`, storyboards, generated app icon.
- **Dummy data**: 1 demo parent, 2 demo kids, 5 sample tasks, 4 sample rewards —
  seeded automatically on first launch so you can test immediately.

---

## Demo logins (seeded automatically on first run)

| Role   | Username / Email        | Password      | PIN  |
|--------|--------------------------|---------------|------|
| Parent | `parent@kidquest.com`   | `password123` | 1234 |
| Kid    | `ava`                    | `kidpass`     | —    |
| Kid    | `leo`                    | `kidpass`     | —    |

Family code for linking a **new** kid account: `DEMO01`

---

## Features

**Auth & roles**
- First screen: role selection ("I am a Parent" / "I am a Kid").
- Parent sign-up: name, email, password, and a required 4-digit PIN.
- Kid sign-up: username, password, avatar, and the parent's family code.
- Local auth via Hive (SHA-256 password hashing) — no Firebase/backend needed.

**Parent**
- Dashboard: all tasks, weekly/monthly completed-task stats, per-kid points.
- Full task CRUD: title, description, points, due date, assigned kid, icon, status.
- Reward management: create/edit/delete rewards with a points cost.
- Approval flow: kid marks a task "Done" → parent Approves (PIN required, points
  awarded) or Rejects (PIN required, task returns to pending).
- PIN protection on editing/deleting tasks, deleting rewards, removing a kid,
  and confirming any reward redemption.
- Kid management: view linked kids, remove a kid, see their weekly progress.
- Weekly/Monthly report export as **PDF** or **CSV**, shareable via the OS share
  sheet (`pdf`, `csv`, `path_provider`, `share_plus`).

**Kid**
- Fun, colorful dashboard with a points badge and quest list.
- Sees only their own assigned tasks; "Mark as Done" moves a task to "Waiting for
  Parent Approval" — kids cannot approve their own tasks or edit/delete anything.
- Tapping a locked task shows "Ask your parent for the code."
- Reward Store: browse rewards, redeem points (parent PIN required to confirm).

---

## Tech stack

- Flutter 3.x, null safety, Material 3
- State management: `provider`
- Local storage: `hive` + `hive_flutter` (manual `TypeAdapter`s — no `build_runner`
  step required)
- Reports: `pdf`, `csv`, `path_provider`, `share_plus`
- Charts-ready: `fl_chart` (dependency included; dashboard currently uses simple
  stat cards, easy to extend with charts)
- `image_picker`, `flutter_svg`, `intl`, `uuid`, `crypto`

---

## Project structure

```
lib/
  main.dart
  models/          # Hive models: ParentUser, KidUser, TaskModel, RewardModel, RedemptionModel
  services/        # StorageService (Hive), AuthService, ExportService (PDF/CSV)
  providers/       # AuthProvider, TaskProvider, RewardProvider (ChangeNotifier)
  screens/
    auth/          # role selection, parent login/signup, kid login/signup
    parent/        # dashboard, task form, approvals, rewards, kids, reports
    kid/           # dashboard (quests tab + rewards tab)
  widgets/         # TaskCard, PointsBadge, StatCard, parent-PIN dialog
  utils/           # theme, constants, dummy-data seeder
assets/images/     # SVG illustrations
android/           # Gradle project
ios/               # iOS project files (see note below)
```

---

## Setup instructions

### 1. Prerequisites
- [Flutter SDK](https://docs.flutter.dev/get-started/install) 3.x installed and
  on your `PATH` (`flutter --version` to check).
- Android: Android Studio + an Android SDK (or a connected device/emulator).
- iOS (macOS only): Xcode 14+ and CocoaPods (`sudo gem install cocoapods`).

### 2. Get dependencies
```bash
cd kidquest
flutter pub get
```

### 3. Run it
```bash
flutter devices          # see what's available
flutter run               # runs on the first connected device/emulator
```
The app seeds demo data on first launch — log in with the credentials in the
table above and you'll immediately see tasks, points, and rewards.

---

## Building the app

### Android APK
```bash
flutter build apk --release
```
Output: `build/app/outputs/flutter-apk/app-release.apk` — install directly on a
device with `adb install build/app/outputs/flutter-apk/app-release.apk`, or copy
it over and tap to install (allow "install unknown apps" if prompted).

Note: the included `android/app/build.gradle` release build type currently signs
with the **debug** key so `flutter build apk --release` works out of the box for
testing. Before publishing anywhere, replace that with your own signing config
(`keytool` + a real keystore, referenced via `key.properties`).

### iOS IPA (macOS + Xcode required)
```bash
flutter build ios --release
```
This produces `build/ios/iphoneos/Runner.app`. To get a distributable `.ipa`:
1. Open `ios/Runner.xcworkspace` in Xcode (see the note below if it doesn't exist yet).
2. Select **Product → Archive**.
3. In the Organizer window, choose **Distribute App** and export the `.ipa`.

A device or paid Apple Developer account is required to run on real hardware or
distribute outside the simulator.

### iOS project note
This ZIP includes every Flutter/iOS support file (`Info.plist`, `AppDelegate.swift`,
`Podfile`, storyboards, `Assets.xcassets` with a generated app icon, `Flutter/`
xcconfig files) but **not** a hand-written `Runner.xcodeproj/project.pbxproj` —
that file is a large, easy-to-corrupt Xcode-specific format, and generating one
by hand without Xcode to validate it risks handing you a project that silently
fails to open. Instead, run:
```bash
flutter create .
```
from the project root. This is safe — it will **not** touch your `lib/`,
`pubspec.yaml`, or assets — it only (re)generates the platform boilerplate
(`ios/Runner.xcodeproj`, `android/` Gradle wiring, etc.) from Flutter's official
templates, merging with what's already here. Then:
```bash
cd ios && pod install && cd ..
flutter build ios --release
```

---

## Notes & next steps for production

- **Auth**: passwords are SHA-256 hashed for this offline demo. For a real
  production app, use a proper backend (e.g. Firebase Auth, Supabase) with
  salted/peppered hashing or OAuth — don't ship local-only auth for anything
  storing real family data.
- **Notifications**: the "approval needed" flow is currently in-app (a banner on
  the parent dashboard). Wire up `flutter_local_notifications` or push
  notifications for a real "ping the parent's phone" experience.
- **App icons**: generated programmatically as a simple trophy mark — swap in
  your own branded icon before publishing (update files under
  `android/app/src/main/res/mipmap-*/` and `ios/.../AppIcon.appiconset/`).
- **Signing**: replace the debug-signed Android release config and set up a
  proper Apple provisioning profile before publishing to app stores.

---

## License

This is a demo/starter project generated for you — use, modify, and ship it
however you like.
