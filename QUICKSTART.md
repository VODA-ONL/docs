# Quickstart — consume verified facts in ~5 minutes

Nothing installed beyond **system `python3` (≥3.10)**. Every step ends in bytes you re-verify locally. You trust nothing on this page.

## 1. Verify a single fact (10 seconds)

```bash
curl -s https://acsa.ai/lake/sava_verify.py -o sava_verify.py
curl -s https://acsa.ai/lake/ponds/pyfacts.ponds.acsa.ai/drops/gauss-sum-1-100.json -o d.json
python3 sava_verify.py drop d.json \
  --trust "$(curl -s https://acsa.ai/lake/ponds/pyfacts.ponds.acsa.ai/pubkey.hex)" \
  --execute-checks --json
# → {"result": 0, "source_fidelity": "checked-by-execution", "verdict": "verified", "reasons": []}
```

**Gate on `result`, never the verdict word.** A tampered Drop can still read `"verdict":"verified"` while returning a non-zero `result`. Trust only `result == 0`. (Try it: change a byte in `d.json` and re-run — the seal breaks.)

Before you run a tool, hash-check it against the pin published in [`/.well-known/acsa.json`](https://acsa.ai/.well-known/acsa.json): `sha256(sava_verify.py)` must match `tools.verify.sha256`.

## 2. Consume a whole pond, keyless

The keyless anchor is a small file that pins each pond's own key — you trust those per-pond keys, not a central authority:

```bash
curl -s https://acsa.ai/lake/nt-bootstrap.json
# [{ "domain": "...", "pond_head_url": "...", "pubkey_hex": "..." }, ...]
```

For each pond you care about:

1. **Verify its head against its pinned key** — `python3 sava_verify.py head <pond_head.json> --trust <pubkey_hex> --now <ISO8601>` → `result 0`.
2. **Get its members** from `manifest.json`; each claim is at `/lake/ponds/<domain>/drops/<id>.json`. The members reproduce the head's signed `merkle_root`, so a host can't add, drop, or swap a claim.
3. **Verify each Drop** as in step 1 and **build only on `result 0`.** No central lake key enters this path.

The human-readable catalog of what's on offer is [`/lake/index.json`](https://acsa.ai/lake/index.json).

## 3. Or: the whole network in one file (zero-install one-liner)

Skip the per-Drop steps — fetch one file and get the entire keyless, trust-ranked view:

```bash
curl -s https://acsa.ai/lake/acsa_consume.py -o acsa_consume.py
python3 acsa_consume.py https://acsa.ai/lake/nt-bootstrap.json --execute-checks
#   score  src  claim
#    1.00    1  round() uses banker's rounding: round(2.5) is 2 and round(3.5) is 4
#    ...
```

It verifies each pond by its pinned key, reproduces each signed membership, re-runs every
check itself, and ranks — no lake key, no install. `--json` for machine output; `--crawl`
to discover more ponds by signed peer links. Hash-check `acsa_consume.py` against
`.well-known/acsa.json` (`tools.consume.sha256`) before running it.

## What you get back

- `result == 0` → the Drop is authentic, internally consistent, and its evidence re-derives (a quote is byte-present, or a check re-ran to `True`). Build on it.
- `not_established` → the protocol declined to stamp it (an opinion, or a check that didn't pass). Don't build on it — and that refusal is the feature.

Next: [wire this into your agent's loop →](INTEGRATE.md)
