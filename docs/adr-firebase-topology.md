# ADR: Firebase topology and temporary family access

Status: Accepted  
Date: 2026-08-15

## Context

Retrogames is a static modular PWA deployed through GitHub Pages. It initially serves 5–10 family users and contains no PII. The existing Horsies Firebase project has anonymous authentication and broad authenticated-user Firestore writes, making it unsuitable as the shared backend for the new catalog.

## Decision

- Use a separate Firebase project named `Retrogames` with project ID `retrogames-24fbd`.
- Remain on the Spark plan.
- Register the web app as `RetrogamesWeb`; continue hosting the PWA on GitHub Pages.
- Use the Standard `(default)` Firestore database in `nam5 (United States)`.
- Enable Anonymous Authentication with automatic cleanup for ordinary sessions.
- Use Firebase Email/Password Authentication for one temporary shared family-editor account identified as `family@retrogames.invalid`.
- Authorize family edits by the account's Firebase UID in `firestore.rules`.
- Permit reads to any authenticated session. Because the app can sign visitors in anonymously, collection data is effectively public and must contain no sensitive information.
- Keep Firebase Storage, Cloud Functions, and App Check out of the initial slice. Storage and Functions currently require a billing-plan upgrade; App Check remains a later hardening task.
- Keep Firebase rules and future indexes in version control and test them with the Firebase Emulator Suite.

## Why

A separate project isolates Horsies data and security, avoids collection and configuration collisions, permits Retrogames to evolve independently, and remains free within the account's project quota. Firebase Authentication verifies the family password server-side, so the password is never embedded in the public client bundle or repository.

## Rejected alternatives

### Reuse the Horsies Firebase project

Rejected because Retrogames experiments could affect existing game data, security rules, quotas, and configuration. Horsies currently treats any anonymously authenticated visitor as eligible for broad reads and writes.

### Verify a family password in browser code

Rejected because GitHub Pages serves a public client bundle. Any embedded password or equivalent hash/secret could be inspected or bypassed.

### Add Cloud Functions immediately

Rejected for the first slice because Functions are not enabled on the Spark project and the current console requires a pricing-plan upgrade. The shared Firebase Auth account provides server-side password verification without a custom backend.

### Make the collection completely unauthenticated

Rejected. Anonymous Firebase Authentication provides a UID and a path to later per-session controls, even though current authenticated reads are effectively public.

## Security limitations

- The shared family account cannot identify which family member made a change.
- Anyone who knows the shared password receives editor authority.
- Password rotation is manual through Firebase Console because the reserved `.invalid` address cannot receive reset email.
- The authorized UID is an identifier, not a secret; security depends on Firebase Authentication verifying the password.
- Broad authenticated reads are unsuitable for PII, addresses, secrets, private photographs, or other sensitive data.
- UI hiding is never treated as authorization.
- QR-code possession must not grant editor authority.

## Configuration summary

- Firebase project: `retrogames-24fbd`
- Web app nickname: `RetrogamesWeb`
- Hosting: GitHub Pages
- Plan: Spark
- Firestore: Standard, `(default)`, `nam5`
- Authentication providers: Anonymous and Email/Password
- Storage: not enabled
- Functions: not enabled
- App Check: not configured
- Firestore rules: [`/firestore.rules`](../firestore.rules)

Public Firebase web configuration may be committed when the application is implemented. Passwords, private keys, service-account JSON, and verification codes must never be committed.

## Rule-test expectations

Emulator tests must prove:

1. Unauthenticated clients cannot read or write.
2. An anonymous authenticated UID can read.
3. An anonymous authenticated UID cannot create, update, or delete.
4. The configured family-editor UID can read, create, update, and delete.
5. A different Email/Password UID cannot write.
6. Nested documents receive the same authorization behavior.
7. Sensitive or privileged collections introduced later receive narrower explicit rules before deployment.

## Consequences and follow-up

- The initial rule is intentionally broad across collections to support rapid catalog setup; narrow it by collection as the schema stabilizes.
- Add emulator configuration and automated rule tests before application writes are enabled in production workflows.
- Revisit individual accounts, audit attribution, App Check, photo storage/compression, backups, and Blaze-only services in the mid-to-long term.
- If the shared account is replaced, update the authorized UID in version-controlled rules and deploy the new rule before removing the old account.
