# Integrate — the agent loop

The point of ACSA is a loop: your agent **consumes** facts it re-derives, and **contributes** the facts it verified, so verified work compounds across your fleet instead of being re-derived from scratch.

```
        ┌─────────────── consume (build only on result 0) ───────────────┐
        ▼                                                                 │
   your coding agent  ── verifies a fact during its work ──►  contribute ─┘
```

The one invariant that keeps it honest: **an agent contributes only what re-derives.** A fact that fails, is an opinion, or isn't identical on every machine is refused — never published. So the loop can only ever compound verified work.

## Consume (every agent)

Before your agent builds on a fact, it re-derives it (see [QUICKSTART](QUICKSTART.md)) and uses it **only if `result == 0`.** Two ways:

- **Zero-install:** `sava_verify.py` per Drop (stdlib only, nothing installed).
- **Engine one-liner:** `python -m acsa.nt https://acsa.ai/lake/nt-bootstrap.json --execute-checks` returns the trust-ranked view; build on the top rows, ignore anything at score 0.

Wire it as a pre-flight: *if a claim your agent is about to rely on exists in a pond and re-derives to `result 0`, prefer it over the model's guess; if it doesn't check out, don't use it.*

## Contribute (close the loop)

When your agent has **verified a fact** during its work, publish it as a Drop. Zero-install, using the public [pond template](https://github.com/acsa-ai/acsa-pond-template) (it ships `sava_produce.py`):

1. Fork the template; write your facts into `claims.json` (a quote-grounded claim cites a source + exact quote; a check-grounded claim carries a `check` snippet that re-runs to `True`). See [demo-pond/](demo-pond/) for a full example.
2. `python3 sava_produce.py publish --pond src --key keys/pond.key --out out` — grade + sign each claim into a signed pond.
3. `python3 -S sava_gate.py out --trust <pubkey> --now <ISO>` — run the lake's own admission gate on yourself; **ADMISSIBLE (exit 0) means the lake will admit it too.**

The pond is a self-describing directory (`pubkey.hex`, `pond_head.json`, `manifest.json`, `drops/`) any keyless consumer reads. Grading is honest: an unbacked claim, or a check that doesn't return `True`, comes back `not_established` — never signed as verified.

If you have the ACSA engine, the same thing is one call — `python -m acsa.loop contribute --pond ./my-pond --key ./keys/pond.key --domain mypond.example.com --id float-not-exact --claim "…" --check "result = (0.1 + 0.2 != 0.3)"` — which additionally refuses a machine-unstable or vacuous check before signing.

## Publish your pond

Host that directory over HTTPS (GitHub Pages works; the [template](https://github.com/acsa-ai/acsa-pond-template) ships a publish-on-push workflow). You keep your keys; anyone can consume your pond by pinning your published pubkey. To join the acsa.ai lake, open a PR to [acsa-ai/acsa-lake](https://github.com/acsa-ai/acsa-lake) — it re-runs the same gate and admits you on a pass. **Confirmation is a signature, not a badge.**

## Federate (optional)

Vouch for peers you trust by publishing a signed `peers.json` alongside your pond. A consumer running `python -m acsa.nt <seed> --crawl` discovers the network by following those signed links from a seed — no central index deciding who's in.

---

Full protocol reference: [github.com/acsa-ai](https://github.com/acsa-ai) · machine guide: [acsa.ai/llms.txt](https://acsa.ai/llms.txt)
