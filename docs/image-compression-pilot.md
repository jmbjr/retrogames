# Image compression pilot

Status: Complete  
Issue: #5  
Started: 2026-08-15

## Baseline configuration

- Source: normal Android phone camera JPEG
- Compressed master: WebP, maximum 2560px long edge, quality 0.82
- Thumbnail: WebP, maximum 640px long edge, quality 0.76
- Untouched original: not retained by default
- Processing: local browser canvas; source is not uploaded by the test tool

## Smoke test 1 — SNES cartridge

Two photos were tested: front label and back/instruction label.

| Metric | Front | Back | Combined / average |
|---|---:|---:|---:|
| Source dimensions | 3024×4032 | 3024×4032 | — |
| Source size | 3,919,483 B | 4,235,660 B | 8,155,143 B total |
| Master dimensions | 1920×2560 | 1920×2560 | — |
| Master size | 95,754 B | 148,622 B | 122,188 B average |
| Thumbnail dimensions | 480×640 | 480×640 | — |
| Thumbnail size | 8,900 B | 12,076 B | 10,488 B average |
| Master reduction | 97.56% | 96.49% | 97.0% weighted |
| Local processing time | 963.3 ms | 983.3 ms | 973.3 ms average |

Generated master + thumbnail totals 265,352 bytes for two photos, or approximately 129.6 KiB per photo.

At three photos per ordinary item, this sample projects approximately:

- 388.7 KiB per item
- 379.6 MiB per 1,000 items
- 1.85 GiB per 5,000 items

These are image-payload estimates only. They exclude exports, cache copies, retained originals, provider overhead and download bandwidth.

## Visual inspection

Pass for this first cartridge sample:

- Front title, publisher, platform branding and product code remain legible.
- Back model/region text and the small instruction text remain readable.
- Plastic texture, fasteners and small surface marks remain visible.
- WebP 2560px at quality 0.82 appears more than sufficient for ordinary cartridge front/back documentation in this sample.

Capture improvements:

- Frame the object more tightly; both images contain substantial empty background.
- A matte neutral-gray background should reduce glare/exposure variation across light and dark objects.
- The visible light reflection on the background did not obscure this item, but more diffuse side lighting would improve consistency.
- Keep the camera parallel to labels when small text is the evidence of interest.

## Provisional conclusion

Retain the 2560px / WebP 0.82 master and 640px / WebP 0.76 thumbnail defaults for the broader pilot. Do not lower master dimensions or quality yet: serial/electrical labels, connectors, damage and PCB details are the harder cases.

Do not commit the source photos or identifying filenames. Record only aggregate measurements and intentionally approved example derivatives.

## Remaining sample

Before accepting a final policy, add representative:

- Cartridge connector/bottom
- Console overall view
- Serial-number label
- Electrical-information label
- Dark or glossy object
- Cable/adapter connector ends
- Damage or corrosion
- Optional PCB detail
- iPhone-generated photo if practical

The full 25–50 item sample remains a target, but decisions may be staged after the difficult image categories have passed.


## Smoke test 2 — dark power adapter and connector details

Five edited automatic crops were visually reviewed:

1. Overall rear adapter view with prongs and attached cable
2. Adapter side profile with protruding prong
3. Embossed electrical/model label
4. Barrel connector side profile
5. Barrel connector face/contact detail

Results:

- Automatic crop plus manual adjustment handled dark plastic against the light background.
- Irregular protrusions—power prongs, cable strain relief and barrel connector—were retained.
- Plastic texture, scuffs, connector geometry, center contact and polarity-relevant physical details remain visible.
- The embossed electrical label is only partly easy to read, but the limiting factor is shallow relief, oblique camera angle and flat lighting rather than visible compression artifacts.
- The connector closeups provide useful identification evidence and validate keeping dedicated connector-tip photography for power supplies and unusual cables.
- Batch previous/next crop review worked successfully.

Capture recommendations for embossed or low-contrast electrical labels:

- Photograph the label more squarely.
- Use low-angle/raking light from one side so embossed characters cast small shadows.
- Tap/focus directly on the label and avoid digital zoom.
- Retain an explicit original only if the compressed master cannot preserve required technical text after a better capture attempt.

Provisional result: crop detection and WebP master quality pass for the dark-adapter/connector category. More ordinary adapter photos are unnecessary; remaining risk categories are glossy/reflective objects, damage/corrosion, optional PCB detail and at least one iPhone source.


## Smoke test 3 — glossy packaging, wear and PCB detail

The pilot added a reflective clear game case with aged printed packaging plus an opened-device interior/PCB photograph.

Results:

- Fine packaging text, barcode, screenshots, discoloration, creases and reflective-case edges remain visible.
- The crop retained the full case, including thin transparent edges that are harder to segment than an opaque cartridge.
- PCB traces, solder joints, fasteners, wiring colors, strain relief, dirt/corrosion-like residue and mechanical components remain inspectable.
- The sample exposed a missing workflow requirement: per-image 90-degree rotation before crop and compression.

The Auto-Crop Lab now provides Rotate left/Rotate right controls, stores rotation per queued image, rebuilds the oriented working image, reruns automatic cropping after rotation, and exports rotation with percentage crop metadata.

Provisional result: glossy/reflective and PCB-detail categories pass at the current quality setting. Rotation is part of the required image-edit metadata alongside crop.


## Initial storage cost projection

Cloud Storage for Firebase now requires the Blaze pay-as-you-go plan, including new default buckets. A new `.firebasestorage.app` bucket can still use Google Cloud Storage's Always Free allowance when created in `US-CENTRAL1`, `US-EAST1` or `US-WEST1`.

Current published no-cost allowances for a new bucket include:

- 5 GB-month stored
- 100 GB downloaded per month
- 5,000 upload operations per month
- 50,000 download operations per month

The two-photo baseline projected approximately 388.7 KiB per ordinary three-photo item for one master and one thumbnail per photo:

- 1,000 items: approximately 379.6 MiB
- 5,000 items: approximately 1.85 GiB
- 10,000 items: approximately 3.71 GiB

Storage capacity should remain inside the 5 GB no-cost allowance through roughly 13,000 ordinary three-photo items at the measured average. Upload operations are more likely to reach the free allowance first: six object uploads per three-photo item imply roughly 833 newly processed items/month before 5,000 operations, excluding retries and replacements.

Actual cost is expected to remain $0 for the initial family scale if a qualifying US regional bucket is used, but Blaze requires a billing account and permits chargeable overage. Budget alerts must be configured before activation; alerts notify but do not provide a hard spending cap.

Do not enable Blaze/Storage solely to finish this spike. Enable it when the authenticated upload vertical slice is ready to test.

Sources:

- https://firebase.google.com/docs/storage/faqs-storage-changes-announced-sept-2024
- https://firebase.google.com/pricing
- https://firebase.google.com/docs/storage/web/start

## Accepted initial image policy

- Capture at normal phone resolution.
- Apply rotation before crop.
- Generate an automatic crop against a controlled background; require a visible review and allow adjustment.
- Store rotation and resolution-independent percentage crop metadata.
- Preserve an uncropped compressed master at maximum 2560px long edge, WebP quality 0.82.
- Generate a 640px WebP thumbnail at quality 0.76.
- Generate cropped display/public derivatives from the master plus edit metadata.
- Do not upload the untouched phone original by default.
- Permit explicit original retention for hard-to-read electrical/serial labels, PCB markings, damage or other diagnostic evidence after a better capture attempt fails.
- Default every image to family-only. Publish only a reviewed, sanitized derivative under the publication ADR.

## Initial retention, deletion and export policy

- Retain the compressed master and edit metadata while its physical-item record is active.
- Treat thumbnails and public derivatives as reproducible outputs.
- Use archive/soft deletion initially; do not permanently delete masters until the backup/export ADR is revisited.
- When permanent deletion is later enabled, require an explicit authorized action and a recovery window.
- A family export must include image metadata, rotation/crop values and retained masters; a public export may contain only explicitly approved sanitized derivatives.
- Never assume Git history backs up Firestore or private object storage.

## Spike conclusion

The provisional defaults pass ordinary cartridge, small printed label, dark adapter, connector, glossy packaging, wear and PCB-detail samples. Browser processing was approximately one second per 12 MP source in the measured cartridge baseline. Automatic crop, manual adjustment, batch review and rotation were exercised successfully.

The original 25–50 item target is not necessary before implementation because the deliberately difficult categories have passed. Re-measure throughput, failure rate, mean file size and monthly operations during the first real 25–50 item vertical-slice intake batch.
