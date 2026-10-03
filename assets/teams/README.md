# Season team emblems

Ten original round badges, one per Season V1 team (`src/data/fixtures/season-v1/season-v1.manifest.json` `teamOrder`). The web shows them next to team names in the Manager season panel (24 px in the standings, 20 px in the next-opponent line). The file name is the teamId; `src/season-v1/config-v1.ts` requires `emblemKey === teamId`. Pictures carry no season data — order, names and records stay server DTOs.

Design and decisions: `docs/superpowers/specs/2026-10-02-season-team-emblems-design.md`. Verification: `docs/verification/2026-10-02-season-team-emblems-qwen.md`.

## How they were made

Generated locally on 2026-10-02 with Qwen-Image 2.1. `scripts/build-team-emblems-qwen.ts` is the single source of the prompts, the selected seeds and the graph:

- local ComfyUI 0.37.0 — no cloud or partner nodes, no account credits, no API keys
- `qwen_image_2.1_int8_convrot` (UNET) + `qwen3vl_8b_int8_convrot` (CLIP type `qwen_image`) + `qwen_image_2.1_vae_bf16` (VAE), from `Comfy-Org/Qwen-Image-2.1`
- the official `image_qwen_image_2_1_t2i` template values: `TextEncodeQwenImage21` without reference images, `EmptyLatentImage` 1024×1024, euler/simple, 25 steps, cfg 1
- one prompt per team: a round badge with a thick team-coloured outer ring and a mascot over a baseball, flat vector mascot-logo style, no text, on a plain white background
- four seeds per team (2001–2004) were rendered and one was picked by eye at 256, 48 and 24 px on the dark UI surface; the same prompt and seed reproduce the PNG pixel-for-pixel on the generating machine

The alpha channel is not the model's. The template's RGBA mode painted the inside of the badges semi-transparent, so the badges were generated opaque on white and matted by `scripts/team-emblem-matte.ts`: the near-white background connected to the frame edge is removed, the largest remaining region is kept, eroded by one pixel, normalised so the badge spans 252 px of a 256×256 canvas, and downscaled with premultiplied area averaging in linear light. The matte refuses a badge whose ring is broken — a detached piece larger than 0.5 % of the badge, a rim that leaves its fitted circle (max residual over 3 % or RMS over 1.5 % of the radius) or a coverage below 0.9 means the background leaked inside. The files are WebP quality 90 with lossless alpha; `manifest.json` records byte size, SHA-256, seed, prompt SHA-256, source PNG SHA-256, the circle-fit residuals and the coverage of every file. `apps/web/src/presentation/season-team-emblems-v1.test.ts` locks manifest, files and the presentation contract together.

`--install` only accepts a PNG named `<teamId>_s<selected seed>_…` whose SHA-256 appears in the generation records (`.kn-runtime/team-emblems-qwen21-records.json`, written after every image) under the current prompt, model and sampling, so `promptSha256` in the manifest names the prompt that really produced the picture. It stages and verifies every file before renaming any of them into place; a failure leaves the published files and the manifest untouched.

Production serves public files with a one-year immutable cache, so the presentation contract appends `?v=<first 8 hex of the file's SHA-256>` to every emblem URL. `--install` prints those versions; the presentation test fails until the contract matches the installed files.

| team | display name | motif | ring |
| --- | --- | --- | --- |
| `player-club` | 내 구단 | baseball in a gold home plate, gold star | navy · gold |
| `npc-wei-tigers` | 위 호표기 | tiger head | royal blue · silver |
| `npc-shu-dragons` | 촉 오호장 | angular modern dragon head | crimson · gold |
| `npc-wu-marines` | 오 강동수군 | anchor and wave | emerald · white |
| `npc-qun-riders` | 군웅 연합 | horse head | violet · silver |
| `npc-jingzhou-phoenix` | 형주 봉황단 | flame phoenix | orange · gold |
| `npc-hanzhong-shields` | 한중 철벽대 | steel shield | slate · ice blue |
| `npc-hebei-halberds` | 하북 대극단 | crossed bats and lightning (no weapons) | black · yellow |
| `npc-xiliang-wolves` | 서량 철기대 | wolf head | charcoal · bronze |
| `npc-central-command` | 중원 책사단 | owl | burgundy · gold |

The prompts follow the card-art banned vocabulary (`docs/card-art-prompts.v1.json` `policy.bannedVocabulary`) except the three mascot animals the team names need (dragon, phoenix, horse), and never name a real league, club or person.

## Licence

Qwen-Image 2.1 weights are released under the Qwen Research License, which grants non-commercial research and evaluation use only. A separate commercial licence may be required before these emblems ship in a commercial build. Regenerating the emblems with the same prompts on the Apache-2.0 Qwen-Image (20B) would avoid that restriction; the files here stay Qwen-Image 2.1 outputs until then. Qwen-Image-3.0 Pro and Qwen-Image 2.0 have no downloadable weights (hosted API only) and were not used.

## Open human review

Before a public release, check by eye at full size that no emblem resembles a real club's mark and that the dragon and phoenix read as modern sports mascots rather than oriental motifs.
