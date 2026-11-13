# Edwin Moindi Habit & Energy Tracker (free, device-only)

A chat-style habit, emotion and energy tracker. It asks for each time block (hourly, 5am to 1am), checks in every hour, and gives a daily summary with guidance to increase, reduce, remove and create.

Data is stored only on each person's phone or computer. Nothing is sent to a server, so it costs nothing to run.

## Put it online for free (GitHub Pages)

1. Create a free account at github.com and click **New repository**. Name it `habit-coach` and set it to **Public**.
2. Click **uploading an existing file**, drag in every file from this folder (`index.html`, `sw.js`, `manifest.webmanifest`, the three `.png` icons, and this README), then click **Commit changes**.
3. Go to **Settings > Pages**. Under *Build and deployment*, set Source to **Deploy from a branch**, Branch to **main** and folder to **/ (root)**, then click **Save**.
4. After about a minute the site is live at `https://YOUR-USERNAME.github.io/habit-coach/`. Use that link.

Netlify Drop (app.netlify.com/drop) also works: drag this folder onto the page for an instant free address.

## Install it on a phone

- **iPhone (Safari):** open the link, tap Share, then **Add to Home Screen**. Open it from the home screen icon.
- **Android (Chrome):** open the link, tap the menu (three dots), then **Install app** or **Add to Home screen**.

## Turn on hourly reminders

Open the app and tap **Turn on reminders**, then allow notifications.
- Notifications only arrive while the app is open or running in the background. A phone that is fully closed or asleep will not get them.
- On iPhone, notifications only work after the app is added to the Home Screen (iOS 16.4 or later). Check Settings > Notifications and make sure the app is allowed.
- If notifications were blocked earlier: Android, tap the lock icon in Chrome's address bar, then Permissions, then Notifications, then Allow.

## Keep your data safe

- Data lives in that browser on that device. Clearing browser data, changing phones, or uninstalling the browser can erase it.
- Use **Download my data** from the menu to save a backup file regularly. Use **Restore from backup** to load it on a new device.

## Fill in on paper or a Word file, then upload

1. Use the file `habit-energy-coach-weekly-form.docx` that comes with this package. It has one section per day (Monday to Sunday). Keep a copy of the blank form to print or reuse.
2. Fill it in. Each day needs a date written as DD/MM/YYYY. Sleep hours are entered once per day, and sleep is placed automatically from 12:00am.
3. Save it as a Word (.docx) file.
4. In the app, open the menu and tap **Import filled Word form**, then choose the file. Each day with a valid date replaces that day's entries on the device.

The import reads the Word file's text, so use the form as provided and avoid adding or removing rows. Importing needs an internet connection, because it loads a small file-reading library from cdnjs.com.

## Changing the content

Everything is in `index.html`. Commit the edited file to GitHub and the live site updates within a minute.

## Option 2: habit.edwinmoindi.co.ke (free, using Netlify)

No GitHub account is needed for this route.

1. **Create a free Netlify account** at app.netlify.com (sign up with your email).
2. **Upload the app:** on the Netlify dashboard, go to **Sites**, then drag the whole `habit-energy-coach-app` folder onto the page marked "Drag and drop your site output folder here". It gets a free address such as `random-name.netlify.app`.
3. **Add your domain:** open the site, go to **Domain management**, click **Add a domain**, and enter `habit.edwinmoindi.co.ke`.
4. **Set the DNS record:** Netlify shows the value to use. At the company where edwinmoindi.co.ke is managed, add a record:
   - Type: `CNAME`
   - Name (host): `habit`
   - Value (target): the address Netlify shows, for example `random-name.netlify.app`
   - TTL: default or 1 hour
5. **Turn on HTTPS:** back in Netlify, under the domain settings, click **Verify DNS configuration**, then **Provision certificate**. This can take up to an hour after the DNS change.
6. **Check it:** open `https://habit.edwinmoindi.co.ke` on your phone. Install it from the browser menu.

Keep the site in your Netlify account. If the site is not claimed, Netlify deletes it.

## Client sign-in and account storage (Supabase, free)

Clients sign in with an email and password. Each person's entries are stored in their own account and can only be read by them.

### One-time setup

1. **Create a project.** Go to supabase.com, sign up free, and click **New project**. Choose a name, set a database password and pick the nearest region. Wait a couple of minutes.
2. **Set the site address.** Go to **Authentication > URL Configuration** and set **Site URL** to `https://habit.edwinmoindi.co.ke`.
3. **Create the data table.** Go to **SQL Editor**, paste the SQL below, and click **Run**.
4. **Copy your keys.** Go to **Project Settings > API**. Copy the **Project URL** and the **anon public** key.
5. **Fill in `config.js`.** Replace `YOUR-PROJECT-REF` and `YOUR-ANON-PUBLIC-KEY` with those values. Upload the updated `config.js` to the app folder, replacing the old one.
6. **Email confirmation.** By default Supabase emails a confirmation link when someone creates an account. Keep this on, so only people who own the email can sign in.

```sql
create table public.habit_data (
  user_id uuid primary key references auth.users(id) on delete cascade,
  data jsonb not null default '{}'::jsonb,
  updated_at timestamptz not null default now()
);
alter table public.habit_data enable row level security;
create policy "Users read own data" on public.habit_data for select using (auth.uid() = user_id);
create policy "Users insert own data" on public.habit_data for insert with check (auth.uid() = user_id);
create policy "Users update own data" on public.habit_data for update using (auth.uid() = user_id) with check (auth.uid() = user_id);
```

### Notes
- **Coach access:** these rules mean only each client can read their own data. Coach Edwin can see entries in the Supabase dashboard (**Table Editor**, `habit_data`) once you're signed in there as the project owner. Use the dashboard to view or delete a client's record on request.
- **Deleting a client's data:** delete the row in `habit_data`, then delete the user under **Authentication > Users**.
- **Consent:** the create-account form asks clients to agree to storage and to deletion on request. Keep that wording, and keep a record of who joined.
- **Offline:** entries are also saved on the phone. They sync when the phone is online.
