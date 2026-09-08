# Cofiz Changelog

Release notes live on **cofiz-dist GitHub Releases** going forward. This file
summarizes what's new at a glance.

## Unreleased

- **In-app self-update (Android):** update card in Settings with version,
  changelog and one-tap APK install; push + inbox announcement on release.
- **Smoother lock screen:** no more dashboard flash on login; PIN entry shows `*`.
- **Registration flow:** register screen, role explainer, collector invites.
- **PushRelayService** with per-user opt-out.
- **Ethiopian calendar** utils, date picker, styled shared widgets, debt tags.
- **Worker:** admin handlers, phone + telegram modules.
- Removed the undeployed Firebase Functions path (Cloudflare Workers won).

## Versioning

Versions look like `v1.2.0` (tag) with a build number after `+` in pubspec.
The app compares its installed version against the latest `cofiz-dist`
release and offers the jump only when the release is newer.
