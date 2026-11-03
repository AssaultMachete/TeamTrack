# TeamTrack

## First try it on your computer

Install Node.js 18 or newer. In PowerShell, open this folder and run:

```powershell
$env:TEAMTRACK_ADMIN_KEY = "choose-a-private-setup-key"
node server.js
```

Open http://localhost:3000. Create the first account and enter that setup key to make it an administrator. Local mode stores data in `data/teamtrack.json`.

## Free hosted setup (Render + Supabase)

The local JSON file is only for trying the app on your computer. Use Supabase for hosted data so Render restarts do not erase accounts and class records.

### A. Prepare the database

1. Create a free Supabase project.
2. In Supabase, open **SQL Editor**, choose **New query**, paste the contents of `supabase-schema.sql`, and click **Run**.
3. In **Project Settings → API Keys**, copy the **Project URL** and create/copy a **Secret key**. Keep the secret private. It belongs only in the Render server environment, never in browser code or GitHub.

### B. Publish the code

1. Create a private GitHub repository and upload this project, including `server.js`, `package.json`, `app.js`, `styles.css`, `index.html`, and `supabase-schema.sql`. Do not upload keys or a `.env` file.
2. In Render, choose **New → Web Service**, connect that GitHub repository, and select the **Free** instance.
3. Use build command `npm install` and start command `npm start`.
4. In the Render service's **Environment** settings, add:
   - `SUPABASE_URL` = the Project URL from Supabase
   - `SUPABASE_SECRET_KEY` = the Supabase Secret key
   - `TEAMTRACK_SESSION_SECRET` = a long random secret (generate it with the PowerShell command below)
   - `TEAMTRACK_ADMIN_KEY` = a different long random setup key
   - `NODE_ENV` = `production`
5. Save and deploy. Open the Render `onrender.com` address when deployment finishes.
6. On that hosted page, create your first account and enter the admin setup key. This hosted administrator is a new account; a local account is not copied automatically.

To generate two separate random values in PowerShell:

```powershell
[guid]::NewGuid().ToString() + [guid]::NewGuid().ToString()
```

Run it twice: use one result for `TEAMTRACK_SESSION_SECRET` and the other for `TEAMTRACK_ADMIN_KEY`. Treat both as private. The setup key is only for establishing an admin role when no admin exists. Store the actual admin email and password safely; that is what you use to sign in.

### C. Add teachers

Share the deployed website address with the teachers. Each teacher selects **Create an account**, registers with their own name, email, and password, and signs in. Their accounts are teacher accounts by default. The administrator can use **Admin** in the sidebar to view accounts and promote a trusted teacher to admin. All accounts on this version work in one shared classroom workspace, so teachers see the same student, activity, group, and assessment data.

Signup is currently open to anyone who has the website address. Do not post the address publicly if you only want invited teachers using it. Invite-only registration is not implemented yet.

## Important free-tier limits

Render's free web service can sleep after inactivity, so the first page load after a quiet period may take a little while. Supabase's free database can pause after a period of low activity. Check the current limits before relying on the site for important records. Back up the database periodically from Supabase. The Supabase secret key gives the backend broad database access; never put it in frontend files, source control, or messages.

## Backend routes

- `POST /api/auth/register`, `POST /api/auth/login`, `POST /api/auth/logout`, `GET /api/auth/me`
- `GET /api/state`, `PUT /api/state`
- `GET /api/admin/users`, `PATCH /api/admin/users`
- `GET /api/health`
