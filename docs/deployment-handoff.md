# TGB Website — Deployment Handoff

This covers everything needed to take the built site live on the current cPanel
hosting: wiring up the contact form (Formspree) and uploading the production
build. A future step (not needed yet) will move hosting to GitHub-connected
deploys — skip that for now.

---

## 1. Set up Formspree (contact form email delivery)

The contact form on the site needs somewhere to send submissions. We're using
[Formspree](https://formspree.io) — a free service that forwards form
submissions straight to an email inbox, no server code required.

1. Go to **[formspree.io](https://formspree.io)** and sign up (free plan is fine).
2. Click **New Form**. Give it a name, e.g. "TGB Contact Form".
3. Set the destination email to wherever submissions should land (e.g.
   `info@torrington-gb.com`, or whatever inbox the team monitors — Google
   Workspace inboxes work fine, no compatibility issues).
4. Formspree will show you an **endpoint URL** that looks like:
   ```
   https://formspree.io/f/abcdwxyz
   ```
   Copy that whole URL.
5. **Important:** the first message the form sends will trigger a
   confirmation email from Formspree to the destination inbox — someone needs
   to click the confirmation link in that email, or Formspree won't forward
   any submissions after it.

## 2. Plug the endpoint into the code

1. Open `direction-lumen.jsx` in the project.
2. Near the top of the file (around line 21), find this line:
   ```js
   const CONTACT_FORM_ENDPOINT = 'https://formspree.io/f/YOUR_FORM_ID';
   ```
3. Replace `https://formspree.io/f/YOUR_FORM_ID` with the real endpoint URL
   from step 1.4. Save the file.

## 3. Build the site

The site uses a small build step (esbuild) that bundles everything into one
fast-loading file. This needs [Node.js](https://nodejs.org) installed. From
the project folder, in a terminal:

```bash
npm install
npm run build
```

This creates a `dist/` folder. **That folder's contents are the entire live
site** — nothing else in the project needs to be uploaded.

```
dist/
├── index.html
├── bundle.js
├── robots.txt
├── sitemap.xml
├── assets/
│   ├── TGB-logo-trimmed.png
│   └── favicon/
└── logos/
    ├── celemics.png
    ├── gen2me.png
    ├── centogene-clean.png
    ├── medgenome.png
    └── gentlegen.svg
```

## 4. Upload to cPanel

1. Log into cPanel → **File Manager** (or use an FTP client like FileZilla
   with the host's FTP credentials).
2. Navigate to `public_html` (or the correct subfolder if the site lives at a
   subdomain).
3. Upload the **contents** of the local `dist/` folder — not the `dist`
   folder itself — so that `index.html` ends up directly inside
   `public_html`.
4. If a placeholder `index.html` already exists there, overwrite it.

## 5. Confirm SSL is on

In cPanel, go to **SSL/TLS Status** and make sure **AutoSSL** is active (most
hosts auto-provision a free Let's Encrypt certificate). If it shows anything
other than a valid, active certificate, run AutoSSL manually from that page.

## 6. Test the live site

Once uploaded, open the real domain and check:

- [ ] Page loads with no visual glitches, on both desktop and mobile widths.
- [ ] Nav links (Services / Partners / Clients / Get in touch) scroll to the
      right sections.
- [ ] "Request Training" and "Request a quote" scroll to the contact form
      with the subject field pre-filled.
- [ ] Submitting the contact form shows the "Message sent" popup, and the
      email actually arrives in the destination inbox (check spam folder on
      the first try).
- [ ] The browser tab shows the site favicon (small hourglass/DNA mark).

---

## What's next (not needed right now)

We're planning to move this hosting to a GitHub-connected deploy later, so
that pushing a code change automatically rebuilds and republishes the site
instead of a manual upload. That's a separate step for later — no action
needed until we're ready to set it up.
