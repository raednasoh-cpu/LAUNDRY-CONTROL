# Laundry Control

Android app (and laptop browser version) for running a laundry: POS sales, expenses, materials stock with low-stock alerts, supplier payables, and daily, weekly and monthly profit reports. Data is shared between devices through the shop's own Firebase project and works offline.

- **Install and set up:** see [SETUP.md](SETUP.md)
- **Download the app:** Releases page → latest `LaundryControl-*.apk`

Project layout: the screens are in `app/src/main/assets/www/index.html`; the Android shell is `app/src/main/java/com/laundrycontrol/app/MainActivity.java`; the automatic build is `.github/workflows/build.yml`.
