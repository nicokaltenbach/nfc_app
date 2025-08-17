# nfc_app

A simple Ionic/Capacitor app for NFC-TAG Scanning on Android.

---

## 1) Prerequisites

| Tool | Version (min) | Notes |
|------|---------------|-------|
| Node.js | 18+ | `node -v` |
| npm | 9+ | `npm -v` |
| Git | — | `git --version` |
| Android Studio | 2023+ | Includes Android SDK + Platform Tools (ADB) |
| Java JDK | 17 | Installed via Android Studio is fine |

---

## 2) Clone & Install

```bash
git clone https://github.com/nicokaltenbach/nfc_app.git
cd nfc_app
npm install
```bash
Lizenzschlüssel einrichten:
npm config set @capawesome-team:registry https://npm.registry.capawesome.io/
npm config set //npm.registry.capawesome.io/:_authToken <DEIN_LIZENZSCHLÜSSEL>
```

---

## 3) Run in the Browser (Dev Only)

```bash
npm run dev
```

Or, if the Ionic CLI is installed:

```bash
ionic serve
```

> This is just for UI development; microphone plugins won’t work in a plain browser.

---

## 4) Android: First-Time Setup

Add the Android platform and sync:

```bash
npm run build
npx cap add android   # only once
npx cap sync android
```

Open in Android Studio:

```bash
npx cap open android
```

In Android Studio:
- Select an **emulator** or **physical device**
- Click **Run**

---

## 5) Android Workflow

```bash
npm run build
npx cap sync android
npx cap open android
```

You can also run directly from the CLI:

```bash
npx cap run android
```
