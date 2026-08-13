# Retro Game Collection System — Requirements and Planning Brief

Status: Repository baseline 0.6 — initial implementation follow-ups processed  
Purpose: Seed a GitHub repository, roadmap, and issue backlog

## 1. Product vision

Create a family-wide system that catalogs retro games, consoles, peripherals, packaging, and related physical items across multiple households. The system should help family members:

- Know what the family owns and where each item is located.
- Identify a physical item quickly without permanently altering or damaging it.
- Browse games to decide what to play.
- Find guides, videos, cheats, technical cartridge data, and family notes.
- Capture and maintain inventory efficiently from a phone or scanner-oriented app.
- Track wish-list items while shopping.
- Lend and mail items among family members.
- Eventually exchange save data with custom cartridge-reading/writing hardware.

The experience should share a visual language and sign-in pattern with the family's existing small GitHub-hosted web apps.

## 2. Product boundaries

### In scope

- Initial shared-family-password access, session user attribution, and a migration path to individual accounts/roles.
- Catalog and physical-item inventory.
- Mobile-friendly browsing and editing.
- Image capture, upload, association, and display.
- Physical identifiers such as QR codes.
- Locations and custody.
- Wish lists.
- Lightweight future loan/contact coordination; formal loans and shipping are deferred.
- Player notes and useful external resources.
- Technical cartridge/hardware metadata.
- Import or enrichment from appropriate third-party sources.
- A durable remote data store designed for future API access.

### Explicitly deferred, but architecturally anticipated

- Reading, dumping, patching, or writing ROMs.
- Reading, editing, transferring, or restoring save files.
- Integration with custom cartridge hardware.
- Automated cross-state save synchronization.
- A complete export capable of producing a limited, read-only static collection website for a local web server.

The initial architecture must not block those capabilities. In particular, physical items need stable IDs, the storage layer needs an API-capable boundary, and future save artifacts must be associable with a particular game, cartridge, user, device, and point in time.

### Likely out of scope unless later requested

- Public marketplace or sales platform.
- Collection valuation, insurance appraisal, or tax reporting.
- Emulator/ROM distribution.
- Public social network.
- Fully automated identification with no human confirmation.
- Uploads of manuals, receipts, warranties, repair records, or other documents.

## 3. Essential domain distinction

The system must separate a **catalog entity** from a **physical item**.

- Catalog entity: the abstract release or product, such as the North American NES release of *Super Mario Bros. 3*.
- Physical item: one particular owned cartridge, box, manual, console, controller, adapter, cable, or other object.

This allows one catalog entry to describe shared facts while each physical copy retains its own owner, location, condition, photos, identifier, loan status, and notes.

Packaging should not be modeled only as a checkbox. A loose cartridge, original box, manual, inserts, and protective case may be separate physical items, grouped as a set when appropriate. This supports split locations, individual condition records, and accurate loans.

## 4. Proposed information model

### Family and access

- **Family group / collection:** top-level ownership and visibility boundary.
- **Session user:** the family member name selected or entered after the shared family password is accepted; used for low-assurance attribution until individual authentication exists.
- **Household:** a family location or group of users, potentially in another state.
- **Role:** viewer/player, contributor, inventory editor, or administrator.

### Catalog

- **Platform/system model:** NES, SNES, Game Boy, PlayStation, and so on.
- **Catalog product:** a game, console model, peripheral model, accessory, or packaging type.
- **Release/edition:** region, revision, publisher, release date, product code, media format, and variant.
- **Technical specification:** ROM/RAM sizes, mapper or enhancement chips, board/PCB details, battery/save technology, supported peripherals, video region, and other platform-specific fields.
- **External resource:** guide, video, cheat database, Game Genie codes, cartridge database, manual, review, repair information, or other typed link.
- **External identifier/provenance:** source name, source record ID/URL, retrieval date, license/terms notes, and field-level origin where needed.

### Inventory

- **Physical item:** stable internal ID and item type.
- **Component relationship:** contains, belongs to set, packaged with, compatible with, or replacement for.
- **Ownership:** family, household, or individual owner, depending on the policy selected.
- **Home location:** where the item normally belongs.
- **Current location/custodian:** where it is now and who currently has it.
- **Location hierarchy:** household/site, room, shelf/cabinet, container/bin, and free-text detail.
- **Condition:** overall and optionally component-specific condition, completeness, defects, repairs, and grading notes.
- **Identifier/label:** QR payload, human-readable short ID, label status, print history, and replacement history.
- **Image:** front, back, spine/side, label, serial number, PCB, damage, accessories, receipt, or other; with caption, creator, capture time, and visibility.
- **Inventory event/history:** created, edited, relabeled, moved, loaned, returned, repaired, sold, lost, or retired.

### Family/player content

- **Product note:** shared, relatively static information or gameplay tips attached to a general game/system product.
- **Physical-item note:** information about a specific owned copy, attached alongside that item's ID, condition, and images.
- **Comment:** a simple timestamped contribution from a signed-in family player on either a product page or physical-item page.
- **Play-related data:** optional status such as unplayed, playing, completed, favorite, or abandoned.
- **Resource bookmark:** family-curated useful links with type, title, description, and broken-link status.

### Wishlist and acquisition

- **Wishlist entry:** globally shared desired catalog product/release, priority, desired condition/completeness, family notes/comments, and status.
- **Acquisition candidate / price observation:** dated store, seller, or location; observed price; optional URL/photo; and source type.
- **Duplicate/upgrade intent:** buy only if absent, seek a better copy, seek missing box/manual, or allow duplicates.

### Loans and shipping

- **Loan:** one or more physical items, lender/custodian, borrower, requested/approved/sent/received/returned states, dates, and notes.
- **Shipment:** origin, destination, carrier, tracking number, shipment status, item manifest, label reference, and costs if retained.
- **Address:** user-managed private shipping address with strict access controls and retention rules.

Formal loan, shipment, address, carrier, and postage records are deferred. The nearer-term concept is a lightweight “request/contact owner or custodian” aid rather than an approval-heavy lending system.

### Future save-data extension points

- **Save artifact:** versioned binary associated with a physical item and game release.
- **Save provenance:** source cartridge/device, player, read time, checksum, format, and tool/firmware version.
- **Transfer job:** requested, exported, shipped/delivered digitally, written, verified, or failed.

These extension-point entities need not be implemented in early phases.

## 5. User roles and permissions

The long-term system should support, at minimum:

- **Viewer/player:** browse available games, locations at an appropriate privacy level, notes, cheats, and links.
- **Contributor:** add their own notes, links, wishlist requests, and possibly images.
- **Inventory editor:** create and edit catalog and physical-item records, scan labels, change locations, and manage loans.
- **Administrator:** manage family membership, permissions, integrations, controlled fields, addresses, exports, and destructive operations.

Open decision: whether scanning a QR code can itself grant edit access. Recommended default: the QR identifies the item but does not contain a secret or bypass authentication. After scanning, a signed-in user's role determines whether the page opens in view or edit mode. A separate explicit “inventory mode” can reduce taps for authorized editors.

For the initial implementation, use one family password to unlock editing and authenticated-family functions. After unlocking, prompt for a family-member display name and retain it for the browser session so edits and comments have useful attribution. This is convenience-level access, not verified individual identity: every password holder initially has the same effective privileges and can select another display name. Individual accounts, stronger roles, revocation, and verified attribution are deferred.

## 6. Functional requirements

### 6.1 Authentication and family scope

- The initial app must support a shared family password for editing and family-only interactions.
- The app must remember the selected session user only for the current browser session unless the family later chooses longer-lived identity persistence.
- Public browsing may expose approved non-PII collection data; family-only actions must require the shared-password session.
- All records must be scoped to the family collection even though only one collection is initially required.
- Individual accounts, invitations, revocation, and granular roles are deferred, but the data model must not prevent them.
- Sensitive information such as shipping addresses must be visible only when required.
- The UI should use the same or compatible authentication and visual pattern as the existing family apps.
- The shared family password, elevated Firebase credentials, API secrets, and other secrets must never be committed to the public repository or embedded as recoverable authorization secrets in client code.

### 6.2 Browse and search

- Users must be able to search by title, platform, publisher, identifier, item type, family tag, location, owner/custodian, and wishlist/loan status.
- Users must be able to filter games by platform, location, availability, number of players, genre, region, and other useful play-selection attributes.
- Results must distinguish “family owns this release” from “this exact physical copy.”
- A game detail page must show owned copies, availability, locations at the permitted level, images, family notes, and curated resources.
- An item detail page must show its identifier, physical characteristics, condition, current/home location, custody, photos, history, notes, and related catalog product.
- The player-oriented interface should prioritize deciding what to play and accessing guides/cheats over inventory-editing controls.

### 6.3 Inventory creation and editing

- Authorized users must be able to create catalog records and physical items.
- The system must support games, systems/consoles, peripherals/controllers, cables/accessories, cartridges/discs/media, boxes, manuals, inserts, and user-defined item types if needed.
- An editor must be able to attach or detach components from a set without deleting them.
- Forms should reveal fields appropriate to the platform and item type rather than displaying every possible field.
- Edits must record who made the change and when.
- Important changes such as custody, location, loan state, ownership, and deletion should be auditable.
- Destructive operations should use soft deletion/archive where practical.
- Batch entry and batch editing should be supported because of collection scale.
- Duplicate detection should warn about likely duplicate catalog records and repeated physical-item creation.
- New records must support exactly two initial completeness states: `draft` and `confirmed`.
- Draft records may appear in the collection but must be visibly identified as unconfirmed.
- Product creation must include a version classification defaulting to `standard` and an optional free-text explanation for a non-standard item.
- Detailed modeling of regional variants, revisions, reproductions, bootlegs, homebrew, prototypes, and multicarts is deferred.
- Physical-item condition must initially use `Great`, `Good`, or `Fair`, defaulting to `Good`.
- Each physical item should support condition notes in addition to the three-level condition value.
- Console/system items must be able to associate compatible or currently assigned power supplies and AV/video cables.
- Controllers should not normally be permanently assigned to a particular console; their current location is the important inventory fact.
- Power-supply and AV-cable associations must be changeable without altering ownership or location history.

### 6.4 Scanner/capture workflow

- A mobile-friendly scanner app or scanner mode must support camera capture and QR scanning.
- The user must be able to photograph front and back, plus optional sides/spine, serial label, PCB, condition issues, packaging, and included materials.
- Captured images must upload to remote storage and associate with either a draft record or an existing physical item.
- The workflow should permit capture first and metadata completion later so slow research does not block physical processing.
- The system should preserve an upload queue and recover cleanly from interrupted or failed uploads.
- The editor should receive a simplified, fast form after capture.
- The user must be able to select an existing catalog match or create a new one.
- The app should support repeating the workflow for many items with minimal navigation.
- Images should retain original files when practical and produce web-sized thumbnails/derivatives.
- Pilot default: capture at the phone's normal camera resolution, then create a compressed upload master with a provisional maximum long edge of 2560 pixels and an efficient web format/quality setting that preserves readable labels, serial numbers, and connector details.
- Do not upload the untouched full-resolution phone original by default during the bulk-intake pilot. Allow an explicit “retain original” option for unusually valuable diagnostic images such as PCB markings, damage, or hard-to-read serial/electrical labels.
- Retain the compressed upload master and its derivatives while the physical-item record exists. Defer permanent deletion/archival rules until measured image quality, storage use, and export needs are known.
- Images must support rotation, cropping, ordering, captions, and type classification.
- PCB images should be optional and may require a separate disassembly/inspection status to avoid implying that every item should be opened.
- The primary cataloger is initially one person using their current Android phone.
- The target capture pace is approximately 2–3 seconds per image, 2–4 images per ordinary item, plus approximately 10 seconds to set simple metadata. This provisional target must be measured and revised during the vertical slice.
- The common intake path should therefore aim for roughly 18–22 seconds for two-to-four images under the stated image timing, with an upper working expectation around 26 seconds where four images each take three seconds plus ten seconds of metadata.
- The system must not require research or detailed enrichment during the fast physical-intake loop; those tasks may occur later while the record remains a draft.

Required/default image guidance by item type:

- **Cartridge games:** front and back required; top when it contains useful identifying information; bottom/connector view required unless the pilot demonstrates it adds insufficient value or is unsafe/inconvenient.
- **Systems/consoles:** top, bottom, serial-number close-up, electrical-information close-up, expansion ports when present, and cartridge/disc slot or media interface.
- **General peripherals:** front, back, and any other view containing useful identifying or technical information.
- **Power supplies/packs:** overall identifying views plus close-ups of the electrical specifications and connector tip.
- **Video adapters/cables:** identifying view plus a clear view of each end/connector.
- **PCB:** optional and off by default; normally captured while an item is already open for cleaning, repair, or inspection.

- Image requirements should be configurable by item subtype so optional views do not slow every workflow.
- Each maintained physical item must support a nullable `last cleaned` date and optional cleaning/service note.

### 6.5 Physical identification and QR labels

- Every physical item must receive a stable, opaque internal ID that never depends on title, owner, or location.
- Every individually inventoried cable must receive its own stable ID; do not use quantity-only cable records in the initial model.
- QR codes should resolve through a stable URL or redirect so the hosting/domain structure can change later.
- The visible label should include a short human-readable ID for manual lookup when scanning fails.
- Authorized users must be able to generate and reprint a label from the item page.
- Reprinting must not create a new physical-item identity unless explicitly requested.
- The system should track whether a label is unprinted, printed, applied, missing, damaged, or replaced.
- Label templates must support one or more tested label sizes and printer types.
- A label must not be the only way to locate an item record; manual ID and search must work.
- The physical marking method must be reversible and must not damage labels, cardboard, plastic, finishes, or collector value.

Recommended labeling hierarchy to test:

1. Put removable QR labels on replaceable protective sleeves, clamshells, bags, box protectors, or hang tags rather than original objects.
2. For durable plastic surfaces, test archival/removable labels on low-value samples and use an intermediate protective film if appropriate.
3. For items that cannot accept a label, associate the ID with their storage slot/container and retain visual/serial-number identification.
4. Treat invisible UV marks as a secondary recovery mark, not the primary workflow: they require special illumination, can be difficult to standardize, may still alter materials, and do not directly provide phone-readable navigation.

Before a collection-wide rollout, run an adhesion/residue/fall-off pilot across representative plastics, glossy labels, cardboard protectors, bags, and temperature/humidity conditions.

### 6.6 External resources and enrichment

- Catalog and item pages must support typed external links including walkthroughs, manuals, videos, cheats, Game Genie codes, cartridge databases, technical references, repair guides, and reviews.
- Family members with permission must be able to add, edit, rank, and report broken links.
- External metadata must retain its source and retrieval/update date.
- Imported data must not silently overwrite family-entered facts.
- Conflicts between sources or local observations must be reviewable.
- The design should support platform-specific sources equivalent to NES Cart Database.
- Importing, scraping, caching, thumbnails, and redistribution must comply with each source's API rules, terms, rate limits, and content licenses.
- Initial implementation may use curated links and manual entry before automated enrichment.
- The end-state workflow should combine automated suggestions/import with easy manual addition and correction.
- Automation should standardize repetitive catalog information across the large collection, while humans remain able to review, override, and preserve provenance.
- Photo-assisted identification should be investigated for the intake workflow. Google Lens or an equivalent service may be used only if integration, terms, privacy, latency, and correction UX are acceptable.
- The system must not assume that the consumer Google Lens interface exposes a suitable application API; the discovery spike should compare Lens-assisted handoff, barcode/OCR matching, and a supported image-recognition/search API.

### 6.7 Technical/developer information

- Authorized users must be able to view and maintain technical data such as ROM size, RAM type/size, save type, mapper/chip, PCB/board revision, battery type, and relevant identifiers.
- Technical fields must support unknown, not applicable, reported by source, and physically verified states.
- Multiple conflicting source claims should be representable without destroying provenance.
- Technical data should be retrievable later through a documented API.
- Access to copyrighted ROM data must remain separate from descriptive metadata; the catalog must not assume ROM files are stored or distributed.

### 6.8 Notes and family contributions

- Product pages must support shared, relatively static notes for general information and gameplay tips.
- Physical-item pages must support notes specific to that owned copy, alongside its unique ID and images.
- Signed-in family players must be able to add simple comments to both product and physical-item pages.
- For the initial version, anyone authenticated through the family's shared-access approach may edit shared notes.
- Comments must retain author identity (or the best identity available under the chosen family-login model), created timestamp, and edited timestamp.
- Under the initial shared-password model, comment/edit authorship is the session-selected display name and must be labeled as unverified attribution.
- Notes and comments should support simple links and safe lightweight formatting.
- Personal play status, ratings, favorites, backlogs, and personal/private notes are deferred to a future issue.
- The initial version does not require private notes, threaded discussions, or complex moderation.
- Shared notes and player comments should be distinct content types so relatively canonical information is not mixed with chronological discussion.

### 6.9 Wishlist and shopping mode

- Users must be able to add a desired game, edition, platform, component, or upgrade to one global family wishlist.
- Family members must be able to add shared notes and simple comments to wishlist entries.
- A store-friendly mobile view must quickly show owned/not owned, number of copies, wanted status, desired variant, completeness target, and price target.
- Search should tolerate partial titles and common naming differences.
- The system should warn when a candidate is already owned, without preventing intentional duplicates.
- Wishlist entries must have status such as wanted, researching, found/ordered, acquired, or dismissed.
- Acquiring an item must support converting the wishlist entry into a standard draft product/inventory workflow, followed by confirmation/approval.
- Conversion must preserve relevant wishlist notes and acquisition context without turning price observations or casual comments into canonical product facts.
- Wishlist entries must support dated price observations with physical/online location or seller and observed price.
- Store mode should display current Amazon, eBay, or other useful online comparison prices where supported and permitted.
- Live shopping-price integrations must show source, retrieval time, shipping inclusion when known, and whether the price represents an asking price, listing range, or completed sale.
- Manual price observations must remain available when marketplace APIs, affiliate access, rate limits, or terms prevent automated pricing.
- Current-price display must not be presented as an appraisal or guaranteed market value.

### 6.10 Locations and custody

- Every physical item must have a home location; current location may differ.
- Locations must support multiple households in different states.
- The system must distinguish owner, home location, current physical location, and current custodian.
- Moves should be timestamped and attributable.
- Users should be able to scan an item and assign it to a location, and optionally scan a location code followed by multiple item codes for fast batch moves.
- Location detail shown to ordinary users should be configurable to avoid exposing private addresses.
- Safe initial publication default: owner, custodian, household/site, room, shelf, bin, and free-text location details are family-only. Public pages may show only coarse availability such as `available`, `not currently available`, or `unknown` until a field allowlist is approved.
- “Available to play,” “in storage,” “on loan,” “in transit,” “missing,” “repair,” and similar availability states must be supported.

### 6.11 Loans

- Loan handling is deferred to a medium/long-term issue and should remain intentionally casual unless later discovery changes the need.
- A future lightweight workflow should help a family member contact the owner or current custodian about borrowing an exact physical item.
- Early versions do not require approval state machines, due dates, reminders, external borrowers, loan history, or collision prevention.
- Email/contact facilitation should avoid exposing contact details publicly and should use the simplest mechanism compatible with the family's authentication model.

### 6.12 Shipping labels

- Shipping regions, carriers, addresses, parcel data, postage purchase/printing, tracking, payment, and reimbursement are deferred.
- Initial and near-term versions must not store shipping addresses or purchase postage.
- The future backlog should preserve the original idea of reducing mailing friction, but it requires separate discovery after the inventory system demonstrates regular use.

### 6.13 Data import, export, and API readiness

- The system must use stable IDs and explicit relationships rather than relying on display names as keys.
- Core data access should pass through a service/API boundary even if the first app and backend are deployed together.
- Data must be exportable in documented, non-proprietary formats, including at least JSON and/or CSV for appropriate entities.
- Images and attachments must be exportable with metadata and record associations.
- Imports must support validation, preview/dry run, error reporting, and repeat-safe behavior where possible.
- API authentication and authorization must enforce the same family and role boundaries as the UI.
- Future API versioning, pagination, idempotency, rate limiting, and auditability should be anticipated.
- The system should eventually support webhooks or event notifications for integrations, without requiring them initially.
- Medium/long-term developer access must support both read-only catalog use and authorized read/write inventory use.
- No API implementation is required for the MVP; preserve the service boundary and stable identifiers needed to add it later.

## 7. Nonfunctional requirements

### Scale and performance

- The initial collection spans approximately 20–30 consoles/systems, commonly 30–40 games per system with larger NES/SNES/N64 holdings, many duplicate items, and a substantial number of peripherals and cables.
- The system must comfortably support at least several thousand physical-item records and tens of thousands of images without requiring an early rearchitecture. Exact sizing should be refined after a representative inventory sample.
- The family is distributed across three states and approximately seven current locations; the location model must allow further growth.
- Plan near-term authentication, concurrency, and usability for approximately 5–10 regular users.
- Common browse/search pages should feel responsive on phones and ordinary home internet.
- Thumbnail loading must not require full-resolution originals.
- Batch capture must remain usable with large inventories.

### Reliability and data integrity

- A stable item ID must never be reused.
- Writes involving uploads and record creation must avoid orphaned images and half-created items or provide reconciliation tooling.
- A concrete backup and restore promise is deferred to a mid-term architecture review. Until then, repository history protects committed static data, while Firestore and image data require an explicit future backup/export decision.
- Audit/history data should allow recovery from accidental edits where practical.
- Concurrent edits must not silently overwrite each other.
- Initial scanning may require an internet connection; a fully offline capture/synchronization engine is not required.
- Online capture must still make failed/in-progress uploads visible and allow retry without creating duplicate items.

### Security and privacy

- Authentication, authorization, secure sessions, input validation, and encrypted transport are required.
- Secrets and provider credentials must never be placed in the client or committed to GitHub.
- Shipping addresses, private notes, and future save files require stricter access than general catalog data.
- Public QR URLs must not reveal sensitive household or owner information before authorization.
- Uploaded images must be treated as untrusted files and served safely.
- Account removal and data-retention behavior must be defined.
- The initial system must store no mailing addresses or equivalent address PII.
- A public source/data repository may contain approved collection data, but must exclude passwords, secrets, private contact details, addresses, and any future data classified as sensitive.
- The shared-password model must be documented as an intentionally temporary, low-assurance control rather than strong per-user authentication.

### Accessibility and usability

- Primary workflows must be usable on a phone and with touch input.
- The app should meet a defined accessibility target, recommended WCAG 2.2 AA for the web UI.
- QR codes must have a text fallback and adequate print contrast/quiet zone.
- Inventory entry should minimize typing and support sensible defaults and recent values.
- The shared design system should maintain consistent navigation, typography, color, components, and status language across this app, `jmbjr/horseracing`, and `jmbjr/kuicks`.
- Android Chrome on the primary cataloger's current phone is a required initial test target.
- Current iPhone Safari must support the core browse, shared-password, comment, and inventory workflows.
- The app should be installable as a PWA on supported Android and iPhone/iPad versions, recognizing platform-specific installation and camera limitations.

### Maintainability

- Platform-specific technical fields should be extensible without a schema rewrite for every console.
- Business rules should not be embedded only in UI code.
- Automated tests should cover permissions, identity, loans, location transitions, imports, and QR resolution.
- Deployment, migrations, backup, restoration, and local development must be documented.
- Environments should be separated at least into development and production.

### Cost and operability

- Hosting, image storage, image transformations, bandwidth, authentication, database, backups, and shipping API costs must be estimated.
- The system should expose basic operational health and error reporting.
- Vendor-dependent features should have manual or export fallbacks where practical.
- Initial infrastructure should remain within Firebase's free allowances where feasible, with usage/cost visibility before paid expansion.
- The project may later split public/static catalog information into a generated GitHub Pages site while retaining private, mutable, or relational information in a proper backend.

## 8. Suggested phased delivery

### Phase 0 — Discovery and proof-of-workflow

Goal: remove the largest uncertainties before committing to the full architecture.

- Inventory the existing family-app stack, auth pattern, hosting constraints, and visual components.
- Sample and refine the known scale: 20–30 systems, typically 30–40 games per system, many duplicates/peripherals/cables, three states, and roughly seven locations.
- Define the domain glossary and minimum entity model.
- Decide collection ownership/privacy policy.
- Test physical-label methods and two or three printer/label formats on representative protectors/materials.
- Prototype QR resolution with opaque IDs and authenticated view/edit behavior.
- Prototype the phone capture loop with draft records and interrupted uploads.
- Evaluate data sources and their licenses/APIs for the first two priority platforms.
- Time a representative 25–50 item capture sample on the primary Android phone and revise the provisional 18–26 second simple-intake target.
- Run a photo-identification spike comparing Google Lens-assisted workflow, OCR/barcode matching, and supported recognition/search APIs.
- Validate Firestore, image storage, security rules, shared-family-password implementation, PWA behavior, and GitHub Pages compatibility as the initial low-cost approach.
- Inspect `jmbjr/horseracing` and `jmbjr/kuicks` to inventory their framework, deployment, PWA setup, navigation, authentication pattern, Firestore usage, design tokens, and reusable components.
- Define success metrics for inventory throughput and player usefulness.

Exit criteria: one physical item can be labeled, scanned, photographed, saved remotely, reopened, edited by an authorized user, and viewed by a player without exposing private data.

### Phase 1 — Inventory foundation (MVP)

Goal: reliably catalog the collection.

- Shared-family-password access and session-selected user attribution, with a documented future migration to individual authentication and roles.
- Platforms, catalog products/releases, physical items, components/sets, locations, and condition.
- Stable IDs, QR generation, manual ID lookup, and label reprint.
- Mobile capture of front/back/optional photos.
- Draft-first entry, simplified editor, batch-friendly loop.
- Browse, search, filters, and item/game pages.
- Manual notes and curated external links.
- Shared product notes, physical-item notes, and simple signed-in player comments.
- `draft` and `confirmed` record states.
- `Great`, `Good`, and `Fair` physical condition, defaulting to `Good`.
- Item-type-specific photo prompts and nullable last-cleaned date.
- Visible edit history for all family-authenticated users; minimal export/recovery safeguards appropriate to the chosen static/Firestore split.
- Android and iPhone web/PWA support.
- Consistent shared UI shell/design tokens.

Explicit deferrals: automated third-party enrichment, carrier label purchasing, save transfer, advanced notifications, and exhaustive technical schemas.

### Phase 2 — Family collection companion

Goal: make the catalog valuable after data entry.

- Player-focused discovery and availability views.
- Personal/family notes, favorites, and play status if approved.
- Wishlist and store mode.
- Global wishlist conversion to draft inventory and dated store/price observations.
- Marketplace price comparison spike with manual fallback.
- Improved external guides, cheats, videos, and technical references.
- Data-source adapters for selected platforms with provenance/conflict review.
- Assisted photo/OCR/barcode identification if the discovery spike proves it reliable and cost-effective.
- Location scanning and batch moves.
- Image quality/processing improvements.

### Phase 3 — Distributed custody and loans

Goal: optionally reduce coordination friction across states after the inventory and location system is established.

- Begin with a simple contact-owner/custodian action for an exact physical item.
- Reassess whether the family actually needs structured loans, due dates, reminders, external borrowers, or history.
- Treat address management, shipping carriers, postage, tracking, costs, and reimbursement as separate optional discovery topics.

### Phase 4 — Technical collection platform

Goal: support family developers and deeper preservation work.

- Platform-specific technical metadata and verification status.
- Broader source integrations and scheduled refresh/link checking.
- Documented, authenticated external API.
- Integration keys/scopes, webhooks/events, and developer documentation.
- Bulk imports and richer exports.

### Phase 5 — Save-data integration (separate future project/increment)

Goal: integrate cartridge tools without destabilizing the inventory platform.

- Threat model and legal/policy review.
- Save artifact model, checksum/versioning, provenance, and retention.
- Hardware/tool protocol and authenticated transfer jobs.
- Verification and rollback before writing to physical media.
- Cross-household save-sharing permissions and conflict handling.

### Future offline/static collection export

Goal: retain useful collection access when the hosted application or internet connection is unavailable, without reproducing the editing system.

- Export a deliberately limited, privacy-filtered snapshot of collection/catalog information.
- Generate a static website that can be served from a local web server.
- Exclude shipping addresses, secrets, private notes, and other sensitive or mutable operational data by default.
- Include a clear export timestamp and indicate that availability/location information may be stale.
- Keep this separate from scanner offline support; offline inventory capture is not currently required.

### Long-term deferred issues

- Define richer regional, revision, reproduction/bootleg, homebrew, prototype, unlicensed, multicart, and compilation modeling.
- Define personal play status, ratings, favorites, backlog, completion history, and private notes.
- Evaluate alignment with a recognized collector condition/grading system if sales, valuation, or insurance becomes a real use case.

## 9. Recommended initial GitHub structure

### Milestones

1. Discovery and architecture decisions
2. Vertical-slice prototype
3. Inventory MVP
4. Player and wishlist experience
5. Distributed coordination (loans/shipping discovery)
6. Technical data and public API
7. Future save integration

### Issue labels

- `area:catalog`
- `area:inventory`
- `area:capture`
- `area:labels`
- `area:search`
- `area:locations`
- `area:wishlist`
- `area:loans`
- `area:shipping`
- `area:integrations`
- `area:api`
- `area:auth`
- `area:design-system`
- `area:operations`
- `type:discovery`
- `type:architecture-decision`
- `type:feature`
- `type:technical-task`
- `type:bug`
- `priority:must`
- `priority:should`
- `priority:could`
- `status:blocked-needs-decision`

### Architecture decision records to open first

- ADR: deployment/hosting model compatible with the existing GitHub apps.
- ADR: remote database and object/image storage.
- ADR: authentication and family authorization model.
- ADR: catalog entity versus physical-item schema.
- ADR: packaging/components and set relationships.
- ADR: opaque QR identifier and redirect strategy.
- ADR: image originals, derivatives, retention, and cost.
- ADR: offline/interrupted capture and draft synchronization.
- ADR: external-source provenance and conflict policy.
- ADR: audit history, backups, and restore.
- ADR: API boundary and future save-artifact isolation.
- ADR: public repository/static-data boundary versus Firestore/private mutable data.
- ADR: shared code/design/auth/data integration with `jmbjr/horseracing` and `jmbjr/kuicks`.

## 10. First vertical-slice backlog

The first implementation should prove one complete path rather than many disconnected screens.

1. Define a minimal schema for user, family, platform, catalog release, physical item, image, location, note, and identifier.
2. Create an opaque ID and QR URL for a physical item.
3. Implement shared-family-password access, session display-name selection, and public-versus-family action boundaries.
4. Capture front/back photos to a draft item from a phone.
5. Match or create the catalog release.
6. Set home/current location and condition.
7. Save and reopen the item by scanning the QR code.
8. Show editing controls to a family-password session and a safe player/public view otherwise.
9. Add one guide link and one technical-data link with provenance.
10. Reprint the same item's QR label.
11. Export the record and image manifest.
12. Test failure cases: bad scan, offline upload interruption, duplicate item, unauthorized user, concurrent edit, and missing image.

## 11. Acceptance-level examples

- Given a signed-in inventory editor scans an applied QR code, when the item exists, the app opens that exact item and exposes authorized editing controls.
- Given a viewer scans the same code, the app shows safe collection information but no address, private note, or edit capability.
- Given a user captures photos with weak connectivity, the app makes upload state visible and permits retry without duplicating the physical item.
- Given two owned copies of the same release, search shows one catalog result with two distinct physical copies and their separate availability.
- Given a cartridge and its box are stored separately, each retains its own location while both appear as components of one collection set.
- Given an item is on loan, its current custodian and availability change without changing its owner or home location.
- Given imported technical metadata conflicts with a family member's physically verified PCB observation, the verified observation remains visible and the source conflict can be reviewed.
- Given a label is damaged, an editor can print a replacement that resolves to the same item and records the replacement event.
- Given a wished-for game is already owned in another state, store mode shows that fact while still allowing an intentional duplicate purchase.

## 12. Risks and design cautions

- **Collection-entry fatigue:** richness can make intake too slow. Use drafts, defaults, staged enrichment, and batch operations.
- **QR-as-security-token mistake:** labels can be photographed. Use authenticated authorization, not possession of the code, to grant editing.
- **Collector-value damage:** no adhesive choice is universally safe. Prefer protective enclosures and run a material pilot.
- **Catalog/inventory conflation:** this creates duplicate shared data and breaks loans/locations. Keep the two layers explicit.
- **Packaging oversimplification:** boxes/manuals may have independent condition and location. Model components and sets.
- **External-source fragility:** websites, schemas, terms, and URLs change. Preserve provenance and use adapters/manual fallbacks.
- **Image-storage growth:** originals and PCB photos can dominate cost. Establish retention, derivative, and export policies early.
- **Static-hosting assumptions:** GitHub-hosted front ends still require a secure remote backend for shared writes, private data, secrets, and shipping integration.
- **One-click postage risk:** purchasing labels is a financial/destructive external action. Require a final confirmation and display cost/service.
- **Future save-data sensitivity:** save files and ROM-adjacent workflows introduce integrity, privacy, copyright, and device-safety concerns. Isolate them as a later bounded subsystem.
- **Public-data leakage:** committing collection records makes their history and precise contents broadly available even after later deletion. Publish only explicitly approved, non-PII fields and keep secrets/sensitive operational data out of Git history from the start.
- **Shared-password attribution:** a selected display name supports convenience and audit readability but does not prove who performed an action. Avoid relying on it for high-impact or privacy-sensitive operations.

## 13. Clarifications needed

### Confirmed discovery decisions — round 1

1. **Initial platform:** Use Firebase/Firestore initially, aiming to remain within the free tier. Harden or migrate later if demonstrated use and scale justify it.
2. **Known scale:** Approximately 20–30 systems, many duplicates, usually 30–40 games per system with larger NES/SNES/N64 holdings, plus many peripherals and cables. The family spans three states and about seven specific locations.
3. **Connectivity:** Bulk scanning will occur online. Offline scanning is not required. Retry/recovery for failed online uploads remains required.
4. **Ownership:** Ownership and possession are mixed. A physical item may be owned by an individual or household, stored centrally or elsewhere, and held by a different custodian. Owner, home location, current location, and custodian must remain separate fields.
5. **Address privacy:** Shipping addresses must never be public. Address storage and carrier integration are deferred beyond initial versions. A mid-term workflow may require the sender to enter an address for each shipment rather than retain it in the collection database.
6. **Identity:** Every physical item/component should receive a unique stable ID. A shared product/release page holds general information and links to each owned physical copy and relevant variant/release records.
7. **Labels:** Current equipment is a local inkjet printer and few items have protective enclosures. Protective sleeves/cases/box protectors are acceptable candidates so labels can be applied to replaceable protection rather than original artifacts.
8. **First platforms:** NES, SNES, Nintendo 64, and Sega Genesis.
9. **Budget/hosting:** Budget is undecided; spending a few hundred dollars is acceptable after the system demonstrates convergence and regular use. Firebase is the initial dynamic backend candidate. GitHub Pages may host generated public/static information later.
10. **External sources:** NES Cart Database is currently expected to be linked rather than imported. Other source requirements remain to be discovered.

### Confirmed discovery decisions — round 2

11. **Cataloger and speed:** The initial cataloger is the collection owner using their current Android phone. Provisional target: 2–3 seconds per image, 2–4 images per item, and about 10 seconds for simple metadata; measure and revise after real use.
12. **Completeness:** Use only `draft` and `confirmed` initially. Draft items may exist and be browsed before enrichment is complete.
13. **Automated identification:** Photo-assisted identification is highly desirable if simple and reliable. Investigate Google Lens or an equivalent rather than making it a hard MVP dependency.
14. **Image views:** Cartridge, console, peripheral, power-supply, and video-adapter views are now specified by type; prompts must distinguish required, conditional, and optional views.
15. **PCB/cleaning:** PCB images default to optional/off and are normally captured during cleaning or service. Track a nullable last-cleaned date.
16. **Non-standard versions:** Rich variant handling is deferred. Product creation defaults to `standard`; an alternate selection exposes a free-text explanation.
17. **Condition:** Use `Great`, `Good`, and `Fair`, defaulting to `Good`, with optional condition notes. Formal market grading is deferred.
18. **Personal play tracking:** Not required initially; create a long-term issue for later definition.
19. **Notes/comments:** General products and individual physical items each have their own shared notes. Signed-in players can leave simple comments on both page types. Initially, family-authenticated users may edit shared notes.
20. **Resource enrichment:** Support both automated standardization/suggestions and easy manual editing; automation needs human review and provenance.

### Confirmed discovery decisions — round 3

21. **Documents:** No uploads are currently needed for manuals, receipts, warranties, repair records, or other documents.
22. **System associations:** Power supplies and AV cables may be associated with a console/system. Controllers remain independent and location-centered because they will be swapped frequently.
23. **Wishlist ownership/conversion:** Use one global family wishlist with family notes/comments. When acquired, convert an entry into the ordinary draft product/inventory workflow and then confirm it.
24. **Shopping information:** Wishlist entries support dated location/seller and price observations. Store mode should also show current Amazon/eBay/other online pricing where practical, with manual fallback and source timestamps.
25. **Loan formality:** Defer structured loan behavior. A future casual workflow may simply facilitate contacting the owner/custodian.
26. **External borrowers:** Deferred with the broader loan question.
27. **Due dates/reminders:** Deferred.
28. **Shipping regions/carriers:** Deferred.
29. **Postage purchase/printing:** Not currently wanted; deferred.
30. **Shipping costs/reimbursements:** Deferred.

### Architectural consequences of round 1

- Use a Firebase-facing repository/service layer in application code so Firestore documents do not become the permanent domain API. This reduces—but does not eliminate—the cost of a later backend migration.
- Define Firestore Security Rules and test them as product code. Client-side hiding or obfuscating fields is not a privacy boundary.
- Do not store shipping addresses in the initial schema. Add only a future shipping workflow boundary and requirements for manual address entry/confirmation.
- Firebase encryption at rest is useful infrastructure protection, but it does not make an address safe from an incorrectly authorized client. Authorization rules, data separation, least privilege, and retention limits are the essential controls.
- Treat public/static catalog data and private operational data as explicit publication classes. A future export/build process may publish approved catalog fields to GitHub Pages, but Firestore/private image data must not be copied automatically.
- Give every cartridge, disc, console, peripheral, cable, box, manual, and other tracked component an opaque physical-item ID. Use relationships to assemble sets.
- Distinguish three levels: product family/general product, release or variant, and owned physical item. For example, a general game page can group regional/revision variants, each of which can link to multiple owned cartridges.
- Start the label experiment with inexpensive protective enclosures and inkjet-compatible label stock. Inkjet ink and label adhesive must be tested for smearing, fading, lift, and residue before bulk use.
- Optimize the first technical schemas and enrichment work for NES, SNES, N64, and Genesis while keeping platform extensions data-driven.
- Add Firebase usage monitoring and cost thresholds before bulk image ingestion. Original photographs, rather than Firestore records, are likely to dominate storage and bandwidth.

### Remaining architecture decisions

The forty original discovery questions have now been answered. These implementation decisions remain:

1. **Existing app integration:** inspect `jmbjr/horseracing` and `jmbjr/kuicks` for framework, build/deployment, authentication, Firebase structure, PWA configuration, and reusable UI. Known baseline: both are single-page games; Horseracing uses Firestore and anonymous multiplayer sessions, while Kuicks has richer UX and potentially complex AI/game logic.
2. **Firebase topology:** determine which services/projects the existing apps actually use, whether development and production are separated, and whether Firebase Storage is acceptable for photos. This is an assisted inspection task, not a prerequisite the product owner must answer from memory.
3. **Publication boundary:** define the precise `public`, `family-only`, and `secret/system` field allowlist and decide which records are canonical static files versus Firestore documents.
4. **Location privacy:** begin family-only for all owner/custodian and precise location fields; revisit individual fields only when a public experience needs them.
5. **Low-value inventory:** every cable receives an individual ID. The capture UX must make this inexpensive enough to remain practical.
6. **Image policy:** validate the provisional 2560-pixel compressed-master/no-original-upload default against label readability, storage use, and upload speed.
7. **Scale refinement:** design for 5–10 regular near-term users and sample one representative system/location.
8. **Label pilot:** choose protector types, inkjet label stock, QR sizes, and a durability test.

### Workflow and content decisions

Questions 11–20 have been answered. The following details remain open without blocking continuation:

- Whether original-resolution photographs should be retained indefinitely or compressed after upload.
- Whether the cartridge bottom/connector photo remains required after measuring its identification value and capture burden.
- The exact secure implementation of the shared-password session on a public static host.
- Whether photo identification should happen before image capture, after front-image capture, or during later draft enrichment.
- Whether `confirmed` means only identity verified or all required images and core fields completed.
21. Are game manuals, receipts, and other document uploads required, and are any intended to remain private?
22. Do you need support for bundles/sets such as console + power supply + controller, including completeness rules?

### Wishlist, loans, and shipping decisions

Questions 23–30 have been answered at the current level. The following wishlist details remain useful but nonblocking:

- Whether “approved” is meaningfully different from the already-selected `confirmed` state; using only `draft` and `confirmed` is recommended for consistency.
- Whether wishlist acquisition creates only a draft product/release, a draft physical item, or both in one guided flow.
- Whether price observations allow an optional photograph or URL even though general document uploads are excluded.
- Which Amazon/eBay comparison matters: lowest active listing, typical range, completed/sold price, or a curated search link.
- Whether marketplace affiliate/API credentials are acceptable if live pricing requires them.
### Confirmed discovery decisions — round 4

31. **PII/address boundary:** Store no mailing addresses or comparable address PII in initial versions.
32. **Repository/public data:** The repository may be public. Approved collection data may also be committed for now, provided secrets and PII are excluded.
33. **Application/authentication:** Prefer one app containing player and administration modes. Initially unlock family functions with one shared family password; migrate to robust authentication later.
34. **Devices/PWA:** Deliver a PWA usable from the current Android phone and iPhones, consistent with the existing GitHub Pages family apps.
35. **Recovery:** Revisit backup strategy mid-term after deciding how much canonical information lives in versioned static files versus Firestore/image storage.
36. **Governance:** Initially, every family-password user may confirm drafts, resolve metadata, merge duplicates, and archive records.
37. **History:** All family-authenticated users may view edit history.
38. **API:** Preserve read-only catalog and authorized read/write inventory API capability for medium/long-term family development; no immediate API use case is required.
39. **Imports:** No existing spreadsheets, photos, lists, or databases require import.
40. **Cross-app integration:** Share design, tokens, reusable components, navigation, authentication approach, appropriate Firestore data, common code, and domain/URL conventions with `jmbjr/horseracing` and `jmbjr/kuicks`.

### Architectural consequences of round 4

- Treat the repository as publicly readable from its first commit; later deletion does not reliably retract Git history or forks.
- Only explicitly `public` fields may be committed or generated into static repository data.
- Static Git data may be canonical for stable public catalog content. Firestore better fits concurrent mutable data such as drafts, comments, locations, item movements, and edit events.
- Never implement the family password as plaintext in the public JavaScript bundle or repository. The vertical slice must choose a server-verifiable session mechanism compatible with Firebase/GitHub Pages or explicitly document the limitations of the existing approach.
- Use a session-selected display name for comments and history, clearly treated as unverified attribution until individual accounts exist.
- Use one responsive PWA with mode-appropriate navigation and controls rather than separate scanner, player, and admin apps initially.
- Make history append-oriented and visible to family-password users; prefer archival to destructive deletion.
- Defer the full backup ADR, but require export of vertical-slice sample records. Git history does not back up Firestore, Firebase Storage, or uncommitted drafts.
- Inspect both named repositories before selecting a framework or extracting shared code. Create a shared package only if real reuse justifies its release overhead.
- Preserve API-ready service boundaries without building a premature external API.

### Initial implementation follow-ups

41. **Firebase discovery:** The currently enabled services and configuration are unknown. Inspect the Firebase initialization, dependencies, hosting workflow, Firestore rules/indexes, anonymous authentication, and environment handling in the existing apps. Produce a short “reuse / adapt / replace” assessment before selecting the new app's setup.
42. **Location visibility:** Use privacy-first defaults now and refine through the vertical slice. Exact owner, custodian, household, room, shelf, bin, and free-text location are family-only; public views show coarse availability only.
43. **Cable identity:** Assign every physical cable its own stable ID and record. Optimize intake through defaults, recent-value reuse, batch location assignment, and optional minimal photography rather than replacing individual records with counts.
44. **Image handling:** The phone may capture at its normal resolution. Pilot client-side compression to a maximum 2560-pixel long edge; upload the compressed master plus generated display thumbnails, not the untouched original by default. Retain the uploaded master while its item exists and revisit policy after measuring a representative batch.
45. **Users:** Support approximately 5–10 regular users in the short-to-medium term.
46. **Existing app evidence:** Horseracing is a single-page game with basic Firestore-backed anonymous multiplayer access. Kuicks is a single-page game with a more complex UX and potentially complex AI logic. Inspect both as evidence of current conventions; do not presume their game logic or architecture should become a shared package.

### Firebase discovery checklist

This can be completed by repository inspection and the Firebase console with guided assistance:

1. Identify each app's framework, package manager, build command, and GitHub Pages deployment workflow.
2. Inventory Firebase SDK packages and locate initialization/configuration modules without copying secrets into requirements or issues.
3. Record the Firebase project IDs and which services are enabled: Firestore, Authentication providers, Storage, Hosting, Functions, App Check, Analytics, and Emulators.
4. Inspect Firestore collections, indexes, and security rules; specifically document how anonymous users are authorized and grouped into a game/session.
5. Determine whether Firebase Storage is already enabled and whether its rules support authenticated image upload/download.
6. Check whether development/test and production use separate Firebase projects or currently share one.
7. Review GitHub Actions/secrets and Pages configuration without exposing secret values.
8. Identify reusable PWA manifest/service-worker code, navigation, design tokens, UI components, session/user-name handling, and responsive patterns.
9. Summarize each discovered element as `reuse`, `adapt`, or `replace`, with the reason and risk.
10. Convert remaining console-only checks into a short guided checklist for the product owner.

## 14. Recommended immediate actions

1. Create the repository with the assumption that its entire Git history is public.
2. Inspect `jmbjr/horseracing` and `jmbjr/kuicks` using the Firebase discovery checklist, then document reusable app/auth/PWA/design conventions.
3. Define the public/family-only/secret field allowlist before committing real collection data.
4. Select one NES cartridge and one console/accessory grouping for the vertical slice.
5. Choose two reversible labeling candidates and run a physical test.
6. Time a 25–50 item capture sample and compare source versus compressed image size, upload speed, and readability to refine throughput and Firebase cost assumptions.

Once those are answered, the vertical slice can be decomposed into well-bounded GitHub issues with acceptance criteria and dependencies.
