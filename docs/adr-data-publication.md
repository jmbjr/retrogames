# ADR: data publication and location privacy

Status: Accepted  
Issue: #4  
Date: 2026-08-15

## Policy

The repository and GitHub Pages output are treated as permanently public. Only explicitly allowlisted fields may enter versioned static data or a public export. Everything else defaults to family-only. Secrets and system credentials are never stored as catalog data.

No mailing addresses or comparable address PII are stored in the initial system.

## Classification

### Public allowlist

These fields may be committed to Git and included in the public static site/export after validation.

#### Catalog and release facts

- Opaque catalog product and release IDs
- Title and normalized sort title
- Product type and platform/system
- Region, revision, publisher, developer, release date/year
- Product code, media format and variant classification
- Player count, genre and play-selection tags
- Public description and public gameplay notes
- Technical cartridge/hardware facts: ROM/RAM sizes, mapper/chips, board details, battery/save technology, video region and supported peripherals
- Curated external-resource type, title and URL
- External source/provenance name, record ID/URL, retrieval date and license/terms note

#### Coarse inventory facts

- Opaque physical-item ID and stable QR/item URL
- Item type and associated catalog/release ID
- Draft/confirmed status
- Version classification
- Component/set relationships expressed only through opaque item IDs
- Coarse availability: available, unavailable, or unknown
- Aggregate owned-copy count

Public data must not reveal a person, household, city, exact storage position, condition, private note, serial number, or movement history.

#### Selective item publication and QR behavior

Physical-item data remains family-only by default. Each item may later have an explicitly reviewed public projection built only from the public allowlist, using the same opt-in review model as sanitized image derivatives.

- A signed-in family editor may approve individual public fields and reviewed image derivatives.
- Approval applies to the generated public projection, never to the underlying Firestore document or original image.
- Unauthenticated QR scans show the approved public projection when one exists.
- Without an approved projection, the QR route shows only a minimal private-collection notice and a link to the associated public catalog product; it exposes no condition or location.
- Authenticated family users may continue from the same stable QR URL to the full authorized item view.
- Condition remains family-only under this ADR and cannot be selectively published unless the policy is explicitly revised.

### Family-only

These fields belong in authenticated Firestore views and must be removed from public exports.

- Owner and current custodian
- Household/site and any state/city-level family location
- Room, shelf, cabinet, container, bin and free-text location
- Home and current location IDs
- Session-selected display names
- Edit, movement, custody, loan, repair and label history
- Condition level (Great, Good or Fair), condition notes, defect details, repair notes and cleaning/service notes
- Physical-item and family notes not explicitly authored as public
- Comments and comment authorship
- Wishlist priorities, family discussion and acquisition candidates
- Store/seller observations that could reveal family routines or location
- Image files and image metadata by default
- Serial numbers, electrical-label closeups and PCB photos
- Upload queues, drafts and incomplete intake records
- Loan/contact coordination
- Audit timestamps beyond a coarse public record update date
- Internal duplicate/merge/archive workflow data

Images remain family-only by default because backgrounds, labels, serial numbers and metadata can leak location or identity. A specifically reviewed, sanitized derivative may be published only after an explicit `visibility: public` decision. Publication must strip metadata and exclude visible people, addresses, location clues, serial numbers, account information and private notes; the original remains family-only.

### Secret/system

These values must never enter Firestore catalog documents, static data, public exports, screenshots, issues, or source control:

- Family password
- Password hashes or password-equivalent verifier material
- Firebase ID/refresh tokens and session cookies
- Private API keys and third-party secrets
- Service-account JSON/private keys
- Recovery codes and verification codes
- Private signing keys
- Unredacted diagnostic payloads containing credentials

Firebase web client configuration and Firebase UIDs are public identifiers, not secrets, but they are configuration rather than catalog/export content.

### Prohibited initially

- Mailing or shipping addresses
- Personal email addresses or telephone numbers
- Shipment tracking numbers
- Receipts, warranties or other personal documents
- ROMs, save binaries or copyrighted document uploads

## Storage boundary

### Static Git data

Static/versioned data may be canonical for stable public catalog and release facts, public technical metadata, curated links and approved coarse inventory summaries.

### Firestore

Firestore is canonical for mutable records: drafts, physical items, ownership/custody, exact locations, comments, wishlists, history, audit events, image metadata and synchronization state.

Public exports must be generated from an explicit projection; they must never serialize Firestore documents wholesale.

## Export validation

Implement a schema-based public exporter with these controls:

1. Construct output from a public-field allowlist rather than deleting known private keys.
2. Reject unknown fields.
3. Recursively reject denylisted names and aliases such as `owner`, `custodian`, `household`, `room`, `shelf`, `bin`, `address`, `email`, `phone`, `serialNumber`, `sessionUser`, `createdBy`, and token/secret/password variants.
4. Reject non-public image references.
5. Reject free-text fields unless the field is specifically public and has been intentionally authored for publication.
6. Validate generated files in automated tests and CI before committing or deploying them.
7. Keep a fixture proving nested private fields cannot leak.
8. Fail closed when the schema version is unknown.

## Future public repository implications

Removing sensitive data in a later commit does not reliably remove it from Git history, forks, caches or prior exports. A field must pass public-export validation before its first commit.

## Accepted publication decisions

- [x] Reviewed/sanitized image derivatives may be public; originals and all new images default to family-only.
- [x] Condition level and all condition details remain family-only; public output may show only coarse ownership/availability.
- [x] Item records default to family-only. Stable QR routes show a reviewed public projection when one exists; otherwise they reveal only a minimal private-collection notice and the associated public catalog link.
