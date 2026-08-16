# Image compression pilot

Status: In progress  
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
