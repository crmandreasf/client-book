# Client book

The shop's internal customer book: customer profiles, purchase history, the made-to-measure email workflow, and emails to groups of customers.

- `index.html` is the website. It goes on GitHub Pages.
- `schema.sql` sets up the database. You run it once in Supabase.
- Customer data lives only in Supabase, never in this repository.

## Setting it up

### 1. Create the Supabase project

1. Sign up at supabase.com and click **New project**.
2. Name it `client-book` and choose a strong database password. Save it in a password manager.
3. For the region, pick the specific region **North EU (Stockholm)**, not the general "Europe" option, so the data stays in the EU.

### 2. Set up the database

1. Open `schema.sql` and scroll to the bottom. Replace the two example email addresses with the staff who should have access, one line per person.
2. In Supabase, open **SQL Editor**, click **New query**, paste the whole file, and click **Run**.

It is safe to run again if you change something.

### 3. Create the staff logins

1. In **Authentication**, turn off **Allow new users to sign up**, so nobody can create their own account.
2. In **Authentication > Users**, click **Add user > Create new user**. Enter the email and a password, and tick **Auto Confirm User**.
3. Each person needs both a login and a line in the staff list from step 2.

To add someone later, create their login, then run this in the SQL Editor:

```sql
insert into public.staff (email, name) values ('name@yourshop.no', 'Name');
```

To remove someone, delete their user under **Authentication > Users**, then run:

```sql
delete from public.staff where email = 'name@yourshop.no';
```

### 4. Connect the page to the database

1. In Supabase, open **Project Settings > API Keys**. Copy the **publishable** key, which starts with `sb_publishable_`. If there is none, click **Create new API keys** first.
2. Copy the **Project URL**, which looks like `https://abcdefgh.supabase.co`.
3. Open `index.html` in a text editor. Near the top, replace the two placeholder values in `CLIENT_BOOK_CONFIG` with these.

The publishable key is meant to sit on a public page; the access rules from step 2 are what protect the data.

**Never put the secret key (`sb_secret_`) in this file.** If you paste it by mistake, the page refuses to run and tells you to replace it.

### 5. Make the password reset link work

In **Authentication > URL Configuration**, set **Site URL** to your address, for example `https://crm.yourshop.no`, and add the same address under **Redirect URLs**.

### 6. Put the site on GitHub Pages

1. Create a repository, preferably private, and upload `index.html`. You can upload this README too. If the repository is public, leave `schema.sql` out or remove the staff emails from it first.
2. In the repository, go to **Settings > Pages**, choose **Deploy from a branch**, then `main` and `/ (root)`.
3. Add your custom domain under **Custom domain**, then add a CNAME record at your domain provider: name `crm`, pointing to `yourname.github.io`.
4. When GitHub shows the domain as working, tick **Enforce HTTPS**.

### 7. Sign in and bring in your records

Open the site, sign in, and go to **Records** to import your earlier customers from a CSV file or a Shopify order export.

## Sending the emails

The **To do** page lists the emails due today and overdue, the follow-ups, and orders that need a look.

- **Assigning:** each customer can have a contact person, and each made-to-measure order a person who handles it. Tasks go to that person. Staff names come from the staff list in step 2.
- **Your list:** everyone sees their own tasks plus unassigned ones by default. Use **Show tasks for** to see everyone's, or one colleague's.
- **Sending:** **Open in mail** opens your mail program with the address, subject and text filled in. Send it, then click **Mark sent**. **Skip** is for an email you don't need, for example when you've just spoken to the customer in the shop.
- **Using Gmail in the browser:** "Open in mail" opens the computer's default mail program. To use Gmail instead, open Gmail in Chrome, click the handler icon at the right end of the address bar, and allow Gmail to open email links. If you skip this, **Preview** still lets you copy the text.

## Good to know

- **Backups:** the free Supabase plan has limits, including fewer backups and pausing projects that sit unused. Check the current plans before relying on it for the business. Either way, use **Records > Download all customers and orders** now and then to keep your own copy.
- **Password reset emails:** these come from Supabase's built-in sender, which only allows a few emails per hour. That is fine for a small team. When automatic customer emails are added, the same sending service can take this over.
- **Changes from colleagues** appear straight away on every open copy of the site.

## Next steps

These are not built yet:

1. **Live Shopify sync**, so each web order is filed on its own.
2. **Live Front Systems sync**, so each in-store sale is filed on its own.
3. **Automatic sending**, if emails start getting missed. It could cover just the fitting reminder and the care email and leave the rest personal.

Each runs as a small function inside the same Supabase project, so no other hosting is needed.
