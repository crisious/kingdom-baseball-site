# Stadium visual assets

Concept: **modern academy** (2026-09-18 pivot, `docs/planning/02_UI_UX/현대_미소녀_덱_시뮬레이션_GUI_개선_종합보고서.md` §2.1 · §4.1).

The three backgrounds were **repainted locally with Qwen-Image 2.1 on 2026-09-28**. Each tier's 2026-09-18 WebP went in as the reference image (`<image1>`) of an instruction edit that keeps the camera, horizon, diamond, bases, foul lines and outfield wall where they are and repaints only the surfaces (evenly striped turf, clean clay and chalk, clearly painted bases) and the background detail. The recipe and the exact prompts live in `scripts/build-stadium-art-qwen.ts`, which is their single source:

- local ComfyUI 0.37.0 — no cloud or partner nodes, no account credits
- `qwen_image_2.1_int8_convrot` (UNET) + `qwen3vl_8b_int8_convrot` (CLIP type `qwen_image`) + `qwen_image_2.1_vae_bf16` (VAE), from `Comfy-Org/Qwen-Image-2.1`
- `TextEncodeQwenImage21` with reference resolution 1440 (the 4:3 reference becomes 1664×1248), sampled on the reference latent it returns · euler/simple · 25 steps · cfg 1 (the official `image_qwen_image_2_1_image_edit` template values)
- seeds: park 918004 · metro 918003 · dome 918003. The same seed and prompt reproduce the PNG pixel-for-pixel on the generating machine.
- outputs lanczos-downscaled to 1448×1086 and encoded as yuv420p WebP (FFmpeg `libwebp`, quality 85, compression level 6; the VAE's alpha channel is dropped)

**License.** Qwen-Image 2.1 weights are released under the Qwen Research License, which grants non-commercial research and evaluation use only. A separate commercial license may be required before these three backgrounds ship in a commercial build. Qwen-Image-3.0 Pro and Qwen-Image 2.0 have no downloadable weights (hosted API only) and were not used.

Facility and sponsor overlays are original transparent SVGs in the token palette (navy · warm white · sakura · info blue · gold · teal). No external artwork or runtime network provider is required.

The source-of-truth values for tier capacity, investment costs, ownership, sponsorship and attendance remain the server management DTO. Filenames and pictures carry no economy values. `manifest.json` (version 2) lists byte size and SHA-256 of every file; `apps/web/src/presentation/stadium-visual-assets.test.ts` locks manifest, files and the presentation contract together.

## Repaint prompt structure

Every tier prompt is the same four parts in order: a geometry lock ("Redraw `<image1>` … Keep the composition of `<image1>` exactly … must stay at exactly the same places in the frame"), a surface cleanup, the tier's scene sentence and a style line that excludes people, text, logos and watermarks. The scene sentences keep what each 2026-09-18 picture already showed: the park's padded wall, aluminum bleachers, blank LED board, glass campus with clock tower, cherry trees, light-rail viaduct, the empty plazas at lower left and lower right and the small grandstand along the bottom edge; the metro stadium's single-tier seats under a cantilever roof, blank ribbon board, floodlight towers and skyline; the dome's two-tier seats with cyan accents, open transparent roof on white arches, VIP skyboxes and neo-city towers — and remove the dome's stray cyan rectangle and white dots on the left-field grass. The negative prompt is passed too but has no effect at cfg 1.

## Measured anchors

The delivered Tier 0 WebP was measured on 2026-09-28 (centroids of the painted plate, rubber and bases — near-white, low-saturation blobs). Every mark stays within 0.4% of `FIELD_ART_ANCHORS_V2`, which is inside the 0.44% gap the constants already had to the 2026-09-18 measurement, so the constants were not changed:

| anchor | constant | measured 2026-09-28 | delta |
| --- | --- | --- | --- |
| HOME | .500 / .741 | .4999 / .7426 | −.0001 / +.0016 |
| MOUND | .500 / .548 | .4968 / .5448 | −.0032 / −.0032 |
| SECOND | .500 / .462 | .4978 / .4581 | −.0022 / −.0039 |
| FIRST | .773 / .531 | .7720 / .5307 | −.0010 / −.0003 |
| THIRD | .227 / .531 | .2291 / .5297 | +.0021 / −.0013 |

Second base is now painted clearly enough to measure; the 2026-09-18 picture's second base was too faint to detect. `FIELD_ART_ANCHORS_V2` carries these points for pitch trails and runner paths; the DOM marker perspective (`toStadiumArtCoordinateV2`) follows them within 3.5% because the 13 chips must also keep the 320px no-overlap layout. Outfield wall ≈ y .20–.27 at centre, ≈ .33 at the foul poles. This mapping affects display only.

Known residual: in all three tiers the foul lines continue past home plate toward the bottom edge, as they did in the 2026-09-18 pictures. Asking the edit to erase them removed the lines but made the model repaint the faint second base somewhere new (10 park renders over 9 seeds, none with all five anchors inside 0.5%: second base off by y −0.6 to −2.7% in 8 and missing in 1, home plate off by +0.8 to +1.1% in 3), and a second edit pass over the finished picture degraded it and erased the foul lines entirely, so the lines were left as they were (`docs/verification/2026-09-28-stadium-qwen-image-local.md`).

## Lineage — 2026-09-18 base art

The pictures the repaint started from were generated on 2026-09-18 with Z-Image Turbo (`ModelSamplingAuraFlow` shift 3, `res_multistep`/`simple`, 14 steps, cfg 2.0, seed 918002) as image-to-image at denoise 0.8 from a synthetic layout guide that painted the reference field geometry (home .50/.755, mound .50/.522, second .50/.407, first .742/.525, third .258/.525 in normalized 4:3 space, infield pre-shrunk because the sampler enlarges the diamond), then upscaled from 1408×1056 to 1448×1086. That run fixed the shared camera and diamond of all three tiers (`docs/verification/2026-09-18-stadium-modern-pivot.md`). Its prompts, kept here for provenance:

1. **tier0_park — 아카데미 오픈 베이스 파크.** Clean modern anime background art for a school baseball simulation game, stylized game concept illustration, crisp lines, vivid natural colors, soft cel shading. A modern academy open-air baseball park seen from a high wide viewpoint directly behind home plate, centered, looking toward center field. Bright synthetic turf with mowing stripes, crisp white foul lines, light ochre clay infield with grass inside the base paths, exactly one home plate, one pitcher's mound and three bases. A slim blue padded outfield wall with a red-brown warning track, low modern aluminum bleachers behind the wall, a compact LED scoreboard in center field. Behind the park a bright glass-and-white school campus with a clock tower, cherry blossom trees, a light-rail viaduct and green hills under a clear blue sky with soft clouds. Quiet empty paved plazas outside the field at the lower left, lower right and upper right. Small modern grandstand behind home plate at the bottom edge. Gentle midday light, empty seats. No players, no crowd, no UI, no lettering, no logos, no watermark.
2. **tier1_metro — 메트로폴리스 스타디움.** Same opening, then: A modern mid-size city baseball stadium … A single-tier grandstand of empty blue and white seats wraps the whole field under a slim white cantilever roof, a continuous LED ribbon board runs along the outfield wall, four tall steel floodlight towers, a large video scoreboard in center field, glass concourse behind the stands, skyline of a modern metropolis with skyscrapers and a river under a clear sky. Quiet empty paved plazas outside the stands at the lower left, lower right and upper right. Daylight, empty seats. No players, no crowd, no UI, no lettering, no logos, no watermark.
3. **tier2_dome — 사이버 넥서스 스마트 돔.** Same opening, then: A futuristic smart dome baseball arena … A two-tier grandstand of empty seats with holographic cyan and white accents wraps the whole field, sweeping white structural arches carry a retractable transparent roof that is open above the field, glass-fronted VIP skyboxes, thin neon cyan and soft pink light strips along the tiers, a giant curved video board in center field, a neo-city skyline of sleek towers visible through the open roof under a bright sky. Quiet empty paved plazas outside the stands at the lower left, lower right and upper right. Daylight, empty seats. No players, no crowd, no UI, no lettering, no logos, no watermark.

Negative (all three, 2026-09-18): ancient architecture, chinese palace, temple, pagoda, curved tile roof, lantern, red banner, flag, dragon, castle wall, wooden palisade, fortress, medieval, fantasy, extra bases, duplicated bases, text, letters, writing, logo, watermark, signature, people, crowd, spectators, players, blurry, low quality, deformed field, tilted horizon, fisheye, night, dark.
