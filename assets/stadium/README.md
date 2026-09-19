# Stadium visual assets

Concept: **modern academy** (2026-09-18 pivot, `docs/planning/02_UI_UX/현대_미소녀_덱_시뮬레이션_GUI_개선_종합보고서.md` §2.1 · §4.1). The three background illustrations were generated on 2026-09-18 with the repository's headless ComfyUI recipe (Z-Image Turbo, `ModelSamplingAuraFlow` shift 3, `res_multistep`/`simple`, 14 steps, cfg 2.0, seed 918002) as **image-to-image at denoise 0.8 from a synthetic layout guide** that paints the reference field geometry, so all three tiers share one camera and one diamond. The 1408×1056 outputs were upscaled to 1448×1086 (lanczos) and encoded as WebP with FFmpeg `libwebp`, quality 85, compression level 6. Facility and sponsor overlays are original transparent SVGs in the token palette (navy · warm white · sakura · info blue · gold · teal). No external artwork or runtime network provider is required.

The source-of-truth values for tier capacity, investment costs, ownership, sponsorship and attendance remain the server management DTO. Filenames and pictures carry no economy values. `manifest.json` (version 2) lists byte size and SHA-256 of every file; `apps/web/src/presentation/stadium-visual-assets.test.ts` locks manifest, files and the presentation contract together.

## Prompt set (positive; shared negative below)

1. **tier0_park.webp — 아카데미 오픈 베이스 파크.** Clean modern anime background art for a school baseball simulation game, stylized game concept illustration, crisp lines, vivid natural colors, soft cel shading. A modern academy open-air baseball park seen from a high wide viewpoint directly behind home plate, centered, looking toward center field. Bright synthetic turf with mowing stripes, crisp white foul lines, light ochre clay infield with grass inside the base paths, exactly one home plate, one pitcher's mound and three bases. A slim blue padded outfield wall with a red-brown warning track, low modern aluminum bleachers behind the wall, a compact LED scoreboard in center field. Behind the park a bright glass-and-white school campus with a clock tower, cherry blossom trees, a light-rail viaduct and green hills under a clear blue sky with soft clouds. Quiet empty paved plazas outside the field at the lower left, lower right and upper right. Small modern grandstand behind home plate at the bottom edge. Gentle midday light, empty seats. No players, no crowd, no UI, no lettering, no logos, no watermark.
2. **tier1_metro.webp — 메트로폴리스 스타디움.** Same opening, then: A modern mid-size city baseball stadium … A single-tier grandstand of empty blue and white seats wraps the whole field under a slim white cantilever roof, a continuous LED ribbon board runs along the outfield wall, four tall steel floodlight towers, a large video scoreboard in center field, glass concourse behind the stands, skyline of a modern metropolis with skyscrapers and a river under a clear sky. Quiet empty paved plazas outside the stands at the lower left, lower right and upper right. Daylight, empty seats. No players, no crowd, no UI, no lettering, no logos, no watermark.
3. **tier2_dome.webp — 사이버 넥서스 스마트 돔.** Same opening, then: A futuristic smart dome baseball arena … A two-tier grandstand of empty seats with holographic cyan and white accents wraps the whole field, sweeping white structural arches carry a retractable transparent roof that is open above the field, glass-fronted VIP skyboxes, thin neon cyan and soft pink light strips along the tiers, a giant curved video board in center field, a neo-city skyline of sleek towers visible through the open roof under a bright sky. Quiet empty paved plazas outside the stands at the lower left, lower right and upper right. Daylight, empty seats. No players, no crowd, no UI, no lettering, no logos, no watermark.

Negative (all three): ancient architecture, chinese palace, temple, pagoda, curved tile roof, lantern, red banner, flag, dragon, castle wall, wooden palisade, fortress, medieval, fantasy, extra bases, duplicated bases, text, letters, writing, logo, watermark, signature, people, crowd, spectators, players, blurry, low quality, deformed field, tilted horizon, fisheye, night, dark.

## Layout guide and measured anchors

The guide paints the reference logical layout (home .50/.755, mound .50/.522, second .50/.407, first .742/.525, third .258/.525 in normalized 4:3 space) with the infield pre-shrunk, because the sampler enlarges the diamond at denoise 0.8. The delivered Tier 0 art was then measured (centroids of the painted plate, rubber and bases):

| anchor | x | y |
| --- | --- | --- |
| HOME | .500 | .741 |
| MOUND | .500 | .548 |
| SECOND | .500 | .462 |
| FIRST | .773 | .531 |
| THIRD | .227 | .531 |

`FIELD_ART_ANCHORS_V2` carries these values for pitch trails and runner paths; the DOM marker perspective (`toStadiumArtCoordinateV2`) follows them within 3.5% because the 13 chips must also keep the 320px no-overlap layout. Outfield wall ≈ y .20–.27 at centre, ≈ .33 at the foul poles. This mapping affects display only.
