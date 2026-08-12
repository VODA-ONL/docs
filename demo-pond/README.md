# Demo pond — Python gotchas coding agents get wrong

A ready-to-fork pond of facts your coding agents **should never get wrong** — and each one carries a re-runnable proof, so an agent doesn't take our word for it, it *runs the check*.

Every claim here is **check-grounded**: a short deterministic Python snippet that binds `result` to `True`. A consumer re-executes it under a sandbox and re-derives `verified` (source_fidelity `checked-by-execution`). Every check in [`claims.json`](claims.json) is machine-stable (same result on every machine) and actually computes something.

## What's in it

| # | Gotcha | The proof |
|---|--------|-----------|
| 1 | `//` floors toward −∞ | `-7 // 2 == -4` (not `-3`) |
| 2 | `%` takes the divisor's sign | `-7 % 3 == 2` (C gives `-1`) |
| 3 | `round()` is banker's rounding | `round(2.5) == 2 and round(3.5) == 4` |
| 4 | `*` binds tighter than `+` | `2 + 3 * 4 == 14` (not `20`) |
| 5 | `/` is float, `//` is floor | `7 / 2 == 3.5 and 7 // 2 == 3` |
| 6 | `bool` is a subclass of `int` | `True + True + True == 3` |
| 7 | a 1-tuple needs a comma | `len((1,)) == 1 and (1) + 1 == 2` |
| 8 | `*` on a string repeats it | `'2' * 3 == '222'` (not `6`) |

These are the kind of facts a model states confidently and gets subtly wrong. With ACSA the agent doesn't guess — it checks.

## Try it against a live pond first

The live [`pyfacts`](https://acsa.ai/lake/ponds/pyfacts.ponds.acsa.ai/) pond already serves check-grounded Python facts. Consume it in [~5 minutes](../QUICKSTART.md); every fact re-runs to `result 0` on your machine.

## Publish this one as your own

Zero-install, with the public [pond template](https://github.com/acsa-ai/acsa-pond-template):

```bash
gh repo create my-pond --template acsa-ai/acsa-pond-template --public --clone
cd my-pond
cp path/to/this/claims.json src/claims.json      # these 8 facts (edit / add your own)
python3 sava_produce.py keygen --out keys        # writes keys/pond.key
python3 sava_produce.py publish --pond src --key keys/pond.key --out out
python3 -S sava_gate.py out --trust "$(cat out/pubkey.hex)" --now "$(date -u +%FT%TZ)"
#   -> ADMISSIBLE (exit 0): the lake will admit it too
```

Push it — the template's publish-on-push workflow re-verifies cold and hosts it on GitHub Pages. You keep the key; anyone can consume your pond by pinning your published pubkey. To join the acsa.ai lake, open a PR to [acsa-ai/acsa-lake](https://github.com/acsa-ai/acsa-lake).

## The format

`claims.json` is a JSON array; each check-grounded claim is:

```json
{
  "id": "floor-div-negative",
  "text": "Python's // floors toward negative infinity: -7 // 2 is -4, not -3.",
  "declared_type": "source_checkable",
  "check": "result = (-7 // 2 == -4)"
}
```

The check runs in a locked-down sandbox — no imports, no I/O, no unbounded loops, a resource cap, and no hash-order dependence — so it re-derives identically on every machine, or it never verifies.
