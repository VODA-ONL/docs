# ACSA docs

Everything an outside team needs to **adopt ACSA** — pull verified, re-derivable facts into your AI agents, and publish your own.

**[acsa.ai](https://acsa.ai)** · *nullius in verba* — take nobody's word for it.

## What ACSA is, in one breath

A fact on the internet is something you **trust**. A fact in ACSA is something your agent **re-derives**. Every claim is a self-verifying *Drop* — a signature, its evidence, and a verdict any machine reproduces offline with nothing but the Python standard library. No API, no account, no install, no vendor to lock into.

> **verified ≠ true.** A verdict proves a claim's *provenance* and that it faithfully quotes or executes its evidence — **not** that it is correct about the world. Opinions and failing checks come back `not_established`, on purpose.

## Start here

| Doc | For |
|---|---|
| [**AGENTS.md**](AGENTS.md) | **drop it in your repo** — your coding agent (Claude Code, Codex, Cursor…) then uses acsa from plain English; you never type a command |
| [**PITCH.md**](PITCH.md) | the one-page why — hand it to a decision-maker |
| [**QUICKSTART.md**](QUICKSTART.md) | pull verified facts into your agent in ~5 minutes, nothing installed |
| [**INTEGRATE.md**](INTEGRATE.md) | wire consume **and** contribute into your agent's coding loop |
| [**demo-pond/**](demo-pond/) | a ready-to-fork pond: Python gotchas coding agents get wrong, each with a re-runnable proof |

## The 10-second proof

```bash
curl -s https://acsa.ai/lake/sava_verify.py -o sava_verify.py
curl -s https://acsa.ai/lake/ponds/pyfacts.ponds.acsa.ai/drops/gauss-sum-1-100.json -o d.json
python3 sava_verify.py drop d.json \
  --trust "$(curl -s https://acsa.ai/lake/ponds/pyfacts.ponds.acsa.ai/pubkey.hex)" \
  --execute-checks --json
# → {"result": 0, "source_fidelity": "checked-by-execution", "verdict": "verified", "reasons": []}
```

Change one byte of `d.json` and the seal breaks. That's the whole idea.

## Learn more

- The protocol stack (SAVA · Confluence · NT): [github.com/acsa-ai](https://github.com/acsa-ai)
- The machine guide for agents: [acsa.ai/llms.txt](https://acsa.ai/llms.txt) · the machine-readable manifest: [acsa.ai/.well-known/acsa.json](https://acsa.ai/.well-known/acsa.json)
