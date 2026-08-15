# Existing Family Apps — Reuse Assessment

Status: Complete for Issue #2  
Inspected: `jmbjr/horseracing` and `jmbjr/Kuicks`  
Date: 2026-08-15

## Executive decision

Start Retrogames as a **build-free, modular JavaScript PWA deployed from GitHub Pages**.

Use **Kuicks as the structural baseline**: separate HTML, design tokens, application modules, domain modules, persistence modules, a manifest, a service worker, and Node's built-in test runner.

Use **Horseracing as a behavioral reference** for Firebase anonymous authentication, realtime Firestore subscriptions, transactions, retry queues, room/session recovery, export, and multi-device coordination. Do **not** copy its current single-file architecture or Firebase code directly into the Retrogames UI.

Do not create a shared cross-repository package yet. First copy/adapt the small proven conventions into Retrogames. Extract a package only after at least two applications need the same versioned component or service and the shared release overhead is justified.

## Evidence summary

| Concern | Horseracing | Kuicks | Retrogames decision |
|---|---|---|---|
| Delivery | Static files, no package/build configuration found | Static ES modules; package only runs `node --test` | Reuse build-free GitHub Pages delivery initially |
| Application structure | HTML, CSS, UI, domain behavior and Firebase code concentrated in `index.html` | HTML, CSS tokens, app controller, UI, rules, CPU and persistence separated | Adapt Kuicks structure; reject a monolithic application file |
| PWA | Manifest + cache-first service worker; explicit Android install guidance | Manifest + network-first service worker and modular app shell | Adapt both; use versioned cache and deliberate offline/read behavior |
| Mobile UX | Phone-first, safe-area support, large touch targets, installable | Responsive shell, safe areas, semantic sections, ARIA/live regions | Reuse principles; create Retrogames-specific visual identity |
| Firebase | Modular Firebase web SDK imports, anonymous auth, Firestore snapshots, transactions, retry queue, shared rooms/statistics | No Firebase integration found in inspected application files | Adapt Horseracing patterns behind Retrogames services |
| Local persistence | Local saved race, room/profile recovery, pending-write retry | Schema-versioned storage envelope and defensive load validation | Reuse Kuicks' versioned-envelope discipline |
| Domain model | Rich but tightly coupled to UI and Firestore | Explicit schema/rules/engine versions, stable IDs, pure domain functions | Reuse Kuicks' versioning and pure-domain approach |
| Testing | No package/test harness or checked-in test configuration found | `node --test` script and testable ES modules | Reuse Node built-in tests; add browser-level smoke tests later |
| Styling | Strong phone-first design, tokens embedded in page | Dedicated `css/tokens.css` and component-oriented CSS | Adapt token separation; do not force either game's theme |
| Deployment automation | No workflow/Firebase hosting files found | No workflow/Firebase hosting files found | Confirm GitHub Pages source/settings manually in Issue #3 |

## Reuse / adapt / replace

### Reuse

- Relative URLs and static-file deployment compatible with a GitHub Pages project path.
- Web app manifest conventions: relative `start_url`, relative `scope`, standalone display, theme/background colors, and maskable icons.
- Safe-area-aware, phone-first layouts and touch-sized controls.
- Kuicks' separated CSS tokens.
- Kuicks' native ES-module organization and no mandatory compilation step.
- Kuicks' schema-versioned persistence envelope and validation before restore.
- Kuicks' explicit domain/schema/engine version fields and stable identifiers.
- Node's built-in test runner for pure JavaScript modules.
- Horseracing's user-visible connection/sync status, local retry queue, idempotent record IDs, Firestore transactions, realtime listeners, and export patterns as concepts.

### Adapt

- Horseracing anonymous Firebase authentication: useful for establishing a Firebase principal, but not sufficient to prove family membership or individual identity.
- Horseracing room/profile/session recovery: adapt the separation among Firebase UID, selected family profile/display name, and browser session ID.
- Horseracing transaction and revision patterns: adapt for duplicate prevention, draft confirmation, location changes, comments, and concurrent edits.
- Kuicks service worker: cache the application shell, but define Retrogames' read-only offline behavior carefully because scanning/upload is online-only.
- Both apps' design language: create shared token names and navigation conventions while allowing each app its own colors/character.
- Local storage: use only for resumable client workflow/session hints, never as the canonical collection database.
- Build IDs/cache versioning: automate or document it so stale PWAs are diagnosable.

### Replace or avoid

- Horseracing's monolithic `index.html`; it has become too coupled for Retrogames' larger domain and permission surface.
- Inline Firebase initialization, Firestore calls and UI mutation in one module.
- Treating anonymous authentication as family authorization.
- Storing a family password in client JavaScript, Git history, local static files, or Firestore-readable configuration.
- Client-only hiding of private locations or edit controls.
- Manual cache-busting scattered through query-string versions.
- Copying game-specific rules, CPU/AI logic, color themes, or visual components that do not serve the inventory workflows.
- Creating a shared npm/package repository before concrete cross-app reuse exists.

## Recommended Retrogames baseline

```text
/
├── index.html
├── manifest.webmanifest
├── service-worker.js
├── css/
│   ├── tokens.css
│   └── app.css
├── js/
│   ├── app.js
│   ├── config.js
│   ├── domain/
│   ├── services/
│   │   ├── auth.js
│   │   ├── catalog-repository.js
│   │   ├── inventory-repository.js
│   │   ├── image-store.js
│   │   └── export.js
│   ├── ui/
│   └── workflow/
├── tests/
├── docs/
└── package.json
```

The domain and service boundaries are more important than these exact directory names. UI code should not know raw Firestore collection paths, and domain rules should remain testable without Firebase or the DOM.

## Firebase findings and remaining console checks

### Confirmed from source

Horseracing uses:

- Firebase modular browser SDK imports.
- Anonymous Firebase Authentication.
- Firestore document/collection reads and realtime snapshots.
- Transactions for idempotent race recording and shared-room updates.
- Server timestamps.
- Local pending-write recovery.
- Separate concepts for anonymous Firebase UID, reusable family profile, and browser session.
- Multiple collections for rooms, race results, player/horse statistics, simulations, and profiles.

### Not found in either repository

- `firebase.json`
- `.firebaserc`
- checked-in `firestore.rules`
- checked-in Firestore indexes
- checked-in Storage rules
- GitHub Actions deployment workflows
- evidence of separate development and production Firebase projects
- Firebase App Check configuration

### Required guided console checks for Issue #3

1. Open Firebase Console and identify the project used by Horseracing.
2. Confirm enabled Authentication providers; anonymous auth is expected.
3. Inspect Firestore database location, mode, collections, indexes, usage, and security rules.
4. Determine whether Firebase Storage is enabled and record its region/rules/usage.
5. Check whether App Check, Functions, Hosting, Analytics or emulators are configured.
6. Decide whether Retrogames receives a separate Firebase project; recommended default is **yes**.
7. Decide how development/test data is isolated from production.
8. Export or recreate version-controlled Firestore/Storage rules and indexes in Retrogames.
9. Confirm the GitHub Pages source/branch and custom-domain settings for both reference apps.
10. Record only project identifiers and non-secret public Firebase client configuration; never copy credentials or secret values into issues.

## Security interpretation

Firebase web client configuration is not itself a secret, but its presence does not grant authorization. Security must live in correctly tested Firestore/Storage rules and, for the temporary family gate, a server-verifiable mechanism. Anonymous auth provides a UID, not proof that the user knows the family password or belongs to the family.

The session-selected display name can support friendly attribution but remains unverified until individual authentication exists.

## Decision for Issue #3

Carry these recommendations forward:

1. Separate Firebase project for Retrogames.
2. Static modular PWA on GitHub Pages.
3. Anonymous Firebase auth may bootstrap a session, but family authorization must be a distinct server-verified claim/session.
4. Firestore and Storage accessed only through small repository/service adapters.
5. Version-controlled and emulator-tested Firestore/Storage rules and indexes.
6. Kuicks-style modularity, schema versioning and native tests.
7. Horseracing-style realtime/transaction/retry concepts, reimplemented cleanly.
8. No shared package until demonstrated reuse warrants it.

## Sources inspected

- [Horseracing README](https://github.com/jmbjr/horseracing/blob/main/README.md)
- [Horseracing application](https://github.com/jmbjr/horseracing/blob/main/index.html)
- [Horseracing manifest](https://github.com/jmbjr/horseracing/blob/main/manifest.json)
- [Horseracing service worker](https://github.com/jmbjr/horseracing/blob/main/sw.js)
- [Kuicks application shell](https://github.com/jmbjr/Kuicks/blob/main/index.html)
- [Kuicks package configuration](https://github.com/jmbjr/Kuicks/blob/main/package.json)
- [Kuicks design tokens](https://github.com/jmbjr/Kuicks/blob/main/css/tokens.css)
- [Kuicks application controller](https://github.com/jmbjr/Kuicks/blob/main/js/app.js)
- [Kuicks persistence module](https://github.com/jmbjr/Kuicks/blob/main/js/game/persistence.js)
- [Kuicks domain model](https://github.com/jmbjr/Kuicks/blob/main/js/rules/model.js)
- [Kuicks service worker](https://github.com/jmbjr/Kuicks/blob/main/service-worker.js)
