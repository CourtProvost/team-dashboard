[README.md](https://github.com/user-attachments/files/32655804/README.md)
# Provost's Office Team Dashboard

A shared project tracker for the office. The data lives in a Google Sheet,
and a small Google Apps Script attached to that sheet acts as the backend,
so there's no Google Cloud project, billing account, or service account
involved at all.

## 1. Set up the Google Sheet

Create one Google Sheet with four tabs. Add a header row to each matching
the columns below (the script reads data starting at row 2, so the header
row is for humans, not required by the code, but worth adding anyway).

- **Roster**: `id`, `name`, `role`
- **Projects**: `id`, `ownerId`, `ownerName`, `ownerRole`, `title`, `status`, `detail`, `question`, `updatedAt`, `history`
- **MeetingHistory**: `id`, `label`, `startedAt`, `endedAt`, `snapshot`
- **Cycle**: `label`, `startedAt`

## 2. Attach the script to the sheet

1. With the sheet open, go to **Extensions > Apps Script**. This opens an
   editor already tied to this specific sheet, no separate project to create.
2. Delete whatever's in the default `Code.gs` file and paste in the contents
   of this project's `Code.gs`.
3. Save (the disk icon, or Ctrl/Cmd+S).

## 3. Deploy it as a web app

1. Click **Deploy > New deployment**.
2. Next to "Select type," click the gear icon and choose **Web app**.
3. Set **Execute as: Me**. This means the script always runs with your
   access to the sheet, regardless of who calls it.
4. Set **Who has access**. "Anyone" is simplest and matches how the
   dashboard already works, no login required, same trust-your-team model
   as before. If you'd rather restrict it, "Anyone within [your Google
   Workspace domain]" is available, but everyone using the dashboard would
   need to be signed into a Dartmouth Google account in that browser for it
   to work.
5. Click **Deploy**, then **Authorize access** and approve it with your
   own Google account. You'll only be asked once, as the deployer, not
   your teammates.
6. Copy the URL it gives you. It ends in `/exec`.

## 4. Point the dashboard at it

Open `public/index.html` and find this line near the top of the script:

```js
const API_URL = 'PASTE_YOUR_APPS_SCRIPT_WEB_APP_URL_HERE';
(https://script.google.com/a/macros/dartmouth.edu/s/AKfycbylsc-vABdvKKtI5RvLgmjhcu1Q_iiqRpQ_mH0gb3TmnwzBMKaCFBoveQ-TBUayvjpL/exec)
```

Replace the placeholder with the URL from step 3.

## 5. Host the page

`public/index.html` is now a plain static file with no server-side code of
its own, all the logic lives in the Apps Script. You can deploy it to
Vercel the same way as ORBIT, drop it into GitHub Pages, or host it
anywhere else Dartmouth is comfortable with. There's nothing to build or
install.

## If a deploy ever needs updating

Editing `Code.gs` in the Apps Script editor doesn't update the live web
app by itself. After making changes, go to **Deploy > Manage deployments**,
click the pencil icon on the existing deployment, and select a new version.
This keeps the same URL, so you don't need to update `index.html` again.

## A couple of things worth knowing

- There's no login, so anyone with the page's link can submit or edit any
  card. For a 7-person team this is usually fine, worth a quick heads up
  to the group.
- "Mark completed" and "Start new meeting" write to the sheet immediately.
  There's no undo built in beyond editing the row back by hand in Google
  Sheets if something gets marked by mistake.
- If the dashboard shows "Could not load," the most common cause is the
  `API_URL` placeholder not being replaced, or the deployment's access
  setting not matching who's trying to use it.
