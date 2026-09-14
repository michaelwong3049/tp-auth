# Tock

A Chrome extension that generates TOTP two-factor authentication codes directly in your browser — no phone required.

Built for CUNY students and staff who need 2FA codes during login without interrupting their workflow.

---

## Goal

Tock keeps your 2FA codes one click away. Instead of picking up your phone every time you log in, Tock generates the same time-based codes right in your browser popup and refreshes them every 30 seconds.

---

## How it works

1. **Setup** — You paste your TOTP secret key (the Base32 string from your 2FA enrollment page) into Tock once. It validates the format, encrypts it with AES-256-GCM using the Web Crypto API, and stores the encrypted result in Chrome's local storage. The encryption key and IV are also stored locally — nothing is sent anywhere.

2. **Code generation** — On every popup open, Tock decrypts your secret and feeds it into a standard TOTP algorithm (RFC 6238, 6-digit codes, 30-second window). The code updates every second so you always see the live value. Click **Copy code** to copy it to your clipboard.

3. **Auto-fill** — When a login page shows a one-time code field, Tock fills in the current code and submits the form for you.

4. **Logout** — If you need to change your secret, click **Logout**. It wipes the stored secret and walks you through setup again.

Everything runs locally. No accounts, no servers, no network requests.

---

## Setup

### Prerequisites

- Node.js 20.19+ or 22.12+ (required by Vite 8)
- npm
- Google Chrome (or any Chromium-based browser)

### 1. Install and build

```bash
git clone https://github.com/michaelwong3049/tp-auth.git
cd tp-auth
npm install
npm run build
```

This outputs the extension to the `dist/` directory. Run `npm run build` again whenever you pull new changes.

### 2. Load the extension in Chrome

1. Open Chrome and go to `chrome://extensions`
2. Enable **Developer mode** (toggle in the top-right corner)
3. Click **Load unpacked**
4. Select the `dist/` folder from this project

Chrome warns that the extension can read and change data on all websites. Tock needs this permission to auto-fill the code field on the login page.

Click the puzzle piece icon in the Chrome toolbar and pin **tock** so it's always visible.

After a rebuild, click the reload icon on the Tock card in `chrome://extensions` to pick up the changes.

### 3. Get your secret key

Tock needs the Base32 secret key that CUNY generates when you register an authenticator app.

1. Open Chrome and go to `https://ssologin.cuny.edu/oaa/rui`
2. Log in normally (this will be your last time! If you get errors, see [Troubleshooting](#troubleshooting))
3. Register a new authenticator app. When the page shows a QR code, look for the text version of the key and copy it. It's a string of letters A–Z and digits 2–7, at least 16 characters long.

If the page shows only a QR code, scan it with any QR reader. The QR code contains a link like `otpauth://totp/...?secret=JBSWY3DPEHPK3PXP&...`. The value after `secret=` is your key.

Treat this key like a password. Anyone who has it can generate your codes.

### 4. Add the key to Tock

1. Click the Tock icon in the toolbar
2. Click **Get started**
3. Paste your secret key and click **Save token**

Tock now shows your current 6-digit code. If CUNY asks you to confirm the authenticator during registration, enter this code.

### Troubleshooting

If the CUNY page doesn't load properly after you log in, the site is probably under maintenance. Try again in a little while.

<!-- TODO: add a screenshot of the maintenance page -->

Try an incognito window. This fixes the problem in many cases.

If Tock's codes are rejected, check that your computer's clock is set automatically. TOTP codes depend on the current time.

### Issues

If you run into a problem, [open an issue](https://github.com/michaelwong3049/tp-auth/issues).
