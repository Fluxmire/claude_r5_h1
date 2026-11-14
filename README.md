# Image to Three.js miner (claude_r5 H1.1)

Entry point: `main.py` -> `async generate(image, seed, witness) -> str | None` (the competition witness runtime).
The active plan is `ACTIVE_PLAN` in `miner404/versions.py`.

## Base and changes

Base: [SomPeer/copperbellows@d481cfc](https://github.com/SomPeer/copperbellows/tree/d481cfc14057e25d5618a76098ecc54343639f30)
(its code is identical to the round-4 winner TiltGilt/VertexLoom@2385ec1; only the plan differs).

Plan relative to the round-4 winner's:

- same 13 gated rewrite steps, fallbacks, `judge_pick` gates, `replan`, `judged_tilt` and `outline_fit_last`;
- 6 Sol-high drafts as 2 plain + 2 proportions + 2 inventory (parts, counts, colours listed before building);
- full-module rewrites on every step (no SEARCH/REPLACE patches), no draft merge, no tone pass;
- `routed=True`: one Sol-low call (request 9995) labels the photo; upright furniture/structures skip both the photo
  tilt and the judged tilt, tall photos skip the photo tilt (idea from duongnd18/abc-3d-champ@04d5fdc, re-written in
  `miner404/route.py`). A refused or failed label call leaves the base behaviour.

Code changes: `miner404/route.py` (new), `miner404/tilt.py` (`photo_is_tall`), `miner404/pipeline.py` (router call,
both tilt gates, and HTTP 504 `render_timeout` from the current renderer counted as a render timeout so a too-heavy
module is sent back to be made lighter), `miner404/versions.py` (plan). Everything else is the base verbatim.

## Local results vs the round-4 winner (VertexLoom)

Local S1 replica judge (GLM, upstream stage-1 prompts), one judge pass:

| Set | W / D / L | Net margin |
|---|---|---|
| 16 dev prompts (round 4) | 8 / 1 / 7 | +6% |
| 16 random holdout prompts (round 4) | 6 / 7 / 3 | +19% (win rate 37.5%) |

Budget per prompt in those runs: at most 53 calls and 4.51M credits (limits 60 / 5M).
