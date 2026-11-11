# Meesho Seller Manager — Firebase Spark edition

This version replaces Supabase with Firebase Authentication + Cloud Firestore. It supports shared cloud data between Android and PC for the same signed-in account, with real-time updates.

## Free plan
Use Firebase's Spark (no-cost) plan. Current Cloud Firestore free quota includes 1 GiB stored data, 50,000 reads/day, 20,000 writes/day, 20,000 deletes/day and 10 GiB/month outbound transfer. Most email/password authentication is no-cost. Quotas apply and can change; if Spark quota is exceeded, service can be disabled for the rest of the period. No payment method is required to start on Spark. See https://firebase.google.com/pricing/ and https://firebase.google.com/docs/projects/billing/firebase-pricing-plans.

## Setup
1. Go to https://console.firebase.google.com/ and create a project. Stay on the Spark plan; do not add billing.
2. In Project settings -> General -> Your apps, add a Web app. Copy the Firebase config values into `config.js`.
3. In Build -> Authentication -> Get started -> Sign-in method, enable Email/Password.
4. In Build -> Firestore Database, create a database (choose a location close to you; location choice may be permanent).
5. Open Firestore Rules and publish:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/orders/{orderId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

6. Host this folder as a static website. You can test locally using a local static web server, but do not open via `file://`.
7. Open the same website on Android and PC. Create an account on one device, then sign in using the same email/password on the other.

## Use
- Download CSV reports from Meesho Supplier Panel and import them.
- Rows with matching Order IDs update existing records rather than creating duplicates.
- Changes sync live between signed-in devices.
- Export CSV for your own backup.

## Limitations
- This is not an official Meesho integration and does not log into Meesho or automatically scrape its panel. CSV export/import is the data input.
- The importer recognises common column headings; Meesho report formats may differ. Verify imported rows.
- The dashboard's delivered sales total is a simple sum of the imported Amount field, not confirmed settlement or profit.
- The app currently supports one seller account's own login data; keep your login private.
- Firestore security rules are essential. Do not make the database publicly readable/writable.
- Firebase Hosting may have its own Spark quotas. You can use another free static host if preferred.
