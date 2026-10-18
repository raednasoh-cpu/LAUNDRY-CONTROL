# Laundry Control: setup guide

Laundry Control records POS sales, expenses (water, electricity, salaries, rent, maintenance, other), materials stock and supplier payments, and produces daily, weekly and monthly performance reports. It runs as an Android app, and the same screens can run in a laptop browser. All devices share one cloud database (Google Firebase), and the Android app keeps working offline, syncing when the connection returns.

Setup takes about 15 minutes, once. Staff phones need only steps 4 and 5.

---

## 1. Create the shop's Firebase project (free)

1. Open <https://console.firebase.google.com> and sign in with the shop's Google account.
2. **Create a project**, for example `my-laundry`. Google Analytics is not needed.
3. On the project home page click the **web** icon (`</>`) to add a web app. Name it `Laundry Control`. Hosting is not needed.
4. Firebase shows a block that starts with `const firebaseConfig = {`. Copy and keep it; every phone pastes it once.
   You can find it again later under **Project settings → Your apps → SDK setup and configuration → Config**.

## 2. Turn on the database and sign-in

**Database**

1. **Build → Firestore Database → Create database**.
2. Location: choose the nearest region (for Saudi Arabia, `me-central2 (Dammam)` if listed). This cannot be changed later.
3. Start in **production mode**.
4. Open the **Rules** tab, replace everything with the contents of [`firestore.rules`](firestore.rules), put your staff email addresses in the list, and click **Publish**.

**Sign-in**

1. **Build → Authentication → Get started → Email/Password → Enable → Save**.
2. **Users → Add user** for the owner and each staff member (email + password). Add the same emails to the rules list above.
3. Optional, recommended: **Authentication → Settings → User actions**, untick **Enable create (sign-up)** so nobody can create accounts from outside.

To remove a staff member, delete them under **Users** and remove their email from the rules.

## 3. The app build (already done for you)

Every change pushed to this repository's `main` branch builds a new APK automatically (**Actions** tab). Each build appears on the **Releases** page as `LaundryControl-1.0.N.apk`.

The build signs the app with the shop's own key, kept in this private repository at `app/laundry.keystore` (its password is in `app/build.gradle`). Every update must be signed with this same key, or phones refuse to install it over the existing app, so keep the backup copy you received.

**Keep this repository private.** If you ever want to make it public (for example for the free laptop version below), first move the key into repository secrets: add `ANDROID_KEYSTORE` (the key file in base64) and `KEYSTORE_PASSWORD` under **Settings → Secrets and variables → Actions**, then delete `app/laundry.keystore` and the password from `app/build.gradle`.

## 4. Install on an Android phone

1. On the phone, open this repository's **Releases** page (sign in to GitHub, because the repository is private), or download the APK on a laptop and send it to the phone by WhatsApp, email or USB.
2. Tap the `.apk` file. If Android asks, allow **Install unknown apps** for the browser or file app you used, then tap **Install**.
3. Open **Laundry Control**.

Requires Android 8.0 or newer.

## 5. First launch on each phone

1. **Connect your shop**: paste the `firebaseConfig` block from step 1 and tap **Connect**.
2. **Sign in** with the email and password created in step 2.
3. On the first device only: tap **Load example data** to try the reports, then **Clear example data** before entering real records.

Suggested order for real data: **Sales → Price list**, **Stock → New item** (with minimum levels), **Suppliers → Supplier** (with payment terms), then the monthly expenses.

## Updating the app

Install the newer APK from **Releases** over the old one. Records are in the cloud and stay as they are.

## Using it on a laptop

The workflow also publishes the screens to GitHub Pages. This needs **Settings → Pages → Source: GitHub Actions**, and GitHub only offers Pages for private repositories on a paid plan. If the repository is made public, Pages is free: the code becomes visible, but the shop's records stay protected by the sign-in and the database rules. Move the signing key to secrets first, as described in step 3.

## Daily use

| Task | Where |
|---|---|
| Enter the day's POS sales | **Sales → Import POS file**, or **+ Record sale** |
| Detergent, softener and bag usage | **Stock → Record usage** (subtracts from stock) |
| Materials bought | **Stock → Purchase** (adds to stock, creates the supplier invoice) |
| Bills, salaries, rent | **Expenses → + Add expense**, "Whole month" spreads it across each day |
| Pay a supplier | **Suppliers → Pay** on the invoice |
| Reports | **Reports → Daily / Weekly / Monthly**, **Export CSV** saves to Downloads |

**Alerts** on the dashboard show items at or below their minimum stock, and supplier invoices overdue or due within 7 days.

**POS file format**: a CSV with `date, service, qty, price` per line (dates as `YYYY-MM-DD`), plus an optional fifth column `total` when a discount applied. A header row is ignored. New services found in the file are added to the price list.

## Free plan limits

Firebase's free Spark plan includes 50,000 database reads and 20,000 writes per day, which covers a single shop with several staff phones comfortably. Usage is shown under **Firestore → Usage**.
