# AA Taxi Darlington – website and admin panel

This folder is the whole website. The public page is `index.html` and is unchanged from the approved design,
except for one line that loads `assets/js/boot.js`. That script only changes the page when you have saved
something in the admin panel. If nothing has been saved, the page looks exactly as before.

## What is in the folder

| Path | What it does |
|---|---|
| `index.html` | The public one-page website (approved design). |
| `assets/js/boot.js` | Applies saved contact details, airport fares, services and logo to the public page. |
| `admin/index.html` | Admin sign-in page, reached at `/admin`. |
| `admin/dashboard.html` | Protected admin panel. Redirects to sign-in if you are not signed in. |
| `admin/admin.css` | Admin styling, same colours and fonts as the website. |
| `netlify/functions/api.js` | The one back-end function. Already bundled into a single file, nothing to install. |
| `src/api.js` | Readable source of the function. Used to rebuild `api.js` (see "Changing the code"). |
| `src/seed.json` | Admin username and a scrypt hash of the password. The plain password is not stored anywhere. |
| `src/defaults.json` | The original website text, used for "Restore original website text". |
| `_redirects` | Sends `/api/...` to the function and blocks the function folders from being shown as pages. |
| `_headers` | Keeps `/admin` and `/api` out of search engines. |
| `robots.txt` | Tells search engines not to index the admin and API. |
| `404.html` | Page shown for missing pages. |
| `netlify.toml` | Netlify build settings. |
| `tests/` | Automated tests (see the end of this guide). |

## Part 1: Put the project on GitHub (step by step)

You need a free GitHub account. If you do not have one, go to https://github.com and click **Sign up**.

1. Sign in to GitHub. Click the **+** at the top right, then **New repository**.
2. Repository name: `aa-taxi-darlington`. Choose **Public** or **Private**. Click **Create repository**.
3. On the new empty repository page, click **uploading an existing file**.
4. Unzip the ZIP file on your computer first (right-click it, then **Extract All**).
5. Open the extracted folder. Select **everything inside it** (press Ctrl+A on Windows, or Cmd+A on Mac) and drag it onto the GitHub page.
   Make sure the hidden files and folders are included: `netlify`, `admin`, `assets`, `src`, `tests`, `_redirects`, `_headers`, `netlify.toml`, `package.json`.
6. Wait for the uploads to finish. Click **Commit changes** at the bottom.

## Part 2: Connect the project to Netlify

**Important:** do not drag the folder onto Netlify's drop box. That method does not turn on the admin functions. Import from GitHub instead.

1. Sign up or sign in at https://app.netlify.com. Choose **Log in with GitHub** so Netlify can see your repository.
2. Click **Add new project → Import an existing project**.
3. Click **Deploy with GitHub** and select the `aa-taxi-darlington` repository.
4. On the settings screen leave everything empty (build command and publish directory are already set by `netlify.toml`).
5. Click **Deploy aa-taxi-darlington**. Wait until the status shows **Published**.
6. Netlify gives you an address such as `https://something.netlify.app`. Open it to check the website.

## Part 3: Sign in to the admin panel for the first time

1. Go to `https://YOUR-ADDRESS.netlify.app/admin`.
2. Username: **alihussain**.
3. Password: the password you were given. It is stored only as a scrypt hash.
4. Click **Sign in**. You will see the admin panel.

**Change the password now:** open **Account**, type the current password, type a new password of at least 12 characters, and click **Update account**.
Do not keep using the password you typed into the chat. Choose a new one and store it in a password manager.

Five wrong passwords in a row lock sign-in for five minutes. Changing the username or password signs out every other session.

## Part 4: Using the admin panel

- **Dashboard:** counts and a restore button for the original website text.
- **Contact:** phone, WhatsApp number and the Google reviews link. Saving updates the website.
- **Airport fares:** add, change or delete airports and their fixed fares.
- **Services:** add, edit, reorder and delete the service cards. A deleted service stays deleted.
- **Images:** upload PNG, JPG, WEBP, GIF or SVG files up to 2 MB. Copy the path or delete an image. Choose **Use as logo** to change the logo.
- **Account:** change the username and password.

Each section has its own Save button. Changes are stored on Netlify and are shown to visitors straight away. Refresh the website to check.

## Changing the code later

Only needed if you change `src/api.js`. Install Node.js (version 18 or newer), open a terminal in the project folder, then run:

    npm install
    npm run build:api
    npm test

Upload the changed files to GitHub. Netlify deploys them automatically.

## Security summary

- Passwords: scrypt with a random salt, compared in constant time. The plain password is never stored.
- Sessions: an HMAC-signed cookie that is HttpOnly, Secure and SameSite=Lax, valid for eight hours.
- Changing the username or password invalidates all older sessions.
- Five wrong passwords lock sign-in for five minutes.
- Every write needs a valid session. Without one the server returns 401.
- Uploads are checked by file extension and by file signature. SVG files containing script or event handlers are rejected. Size is capped at 2 MB.
- Saved content keeps only known fields. Everything shown on the website is inserted as text, not HTML.
- `/admin` and `/api` are excluded from search engines.

## Known limits

- The public site reads saved values when a visitor opens it. A visitor who already has the page open will see changes after refreshing.
- Only the sections listed above are editable. Home-page text, the car and hero photos, About Me, testimonials, colour themes and a contact-message inbox are not in this version, because this website is a single page with no contact form that stores messages.
- The admin account and password are stored in Netlify Blobs, not in your code.
