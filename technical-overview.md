# Cofiz Technical Overview

Public, secret-free summary of how Cofiz is built. Secrets (bot tokens,
service keys, relay credentials) live only in deploy-time secret stores and
never in any repo.

## Pieces

| Piece | Tech | Repo |
|---|---|---|
| Mobile app (Android) | Flutter/Dart, `provider` state | `cofiz-app` |
| Push/auth relay | Cloudflare Worker (JS) | `cofiz-backend` |
| Distribution | Versioned APKs on GitHub Releases | `cofiz-dist` |
| Website | New build (AI-assisted marketing) | `cofiz-web` |
| Docs | Guides, changelog, ADRs | `cofiz-docs` |

## App architecture

- **Providers** (`ChangeNotifier`) stream Firestore and expose UI state;
  **services** own network, cache and device I/O. UI holds no business logic.
- **Offline-first:** Hive + SharedPreferences cache, connectivity-aware queue,
  outbox banner with retry/discard.
- **Money model:** one `transactions` collection; transfers are linked pairs
  sharing a `transferId`, batch-confirmed by the receiver.
- **Locking:** per-user PIN + idle lock (2 min), exponential cooldowns,
  forced sign-out after 5 failures, optional biometrics.
- **Localization:** English + Amharic via `gen-l10n`.

## Backend

- The worker relays FCM pushes (HTTP v1 with a service account), WhatsApp/
  Telegram OTP, registration decisions, nightly reminders and cron jobs.
- Firestore rules + indexes are deployed as code alongside it.

## Self-update design

- `UpdateService` polls `repos/cofiz-org/cofiz-dist/releases/latest` on launch
  and at most daily (SharedPreferences cache); prereleases/drafts ignored.
- `UpdateProvider` drives the Settings card; `open_filex` hands the APK to
  the Android installer (`REQUEST_INSTALL_PACKAGES`).
- A `release: published` Action POSTs to the worker's `/release/announce`,
  which fans out an `app_update` push. The push only opens Settings — the app
  re-checks fresh before downloading, so stale pushes can't install old builds.
- Releases require the same signing keystore, one universal APK per tag.

## Security posture

- Firebase Auth sessions; PIN-gated app access; secure storage for tokens.
- Append-only audit log for logins, money movement and settings changes.
- No secret is baked into the app or any public repo.
