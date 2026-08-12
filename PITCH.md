# Why your agents should build on ACSA

*One page. For anyone deciding whether to pull ACSA into an AI product.*

## The problem

AI agents build on facts they **cannot check**. A model asserts something; a RAG pipeline hands over a passage; a tool returns a number — and the agent takes it on trust. When it's wrong, the error compounds silently through everything the agent does next. For a fleet of coding agents, that means shipped bugs, hallucinated APIs, and no way to tell a verified fact from a confident guess.

The usual fixes add *more trust*: a bigger model, a curated source, a vendor's "grounded" endpoint. You still can't independently check any single fact, and you're now locked to whoever served it.

## The idea

**Make every fact carry its own proof.** In ACSA, a claim is a self-verifying *Drop*: a signature, its evidence, and a verdict any machine can re-derive offline — with nothing but the Python standard library. Two honest ways to ground a claim:

- **quote** — the claim cites a source and an exact quote; it verifies iff the quote is byte-present in that source.
- **check** — the claim carries a short deterministic snippet that re-runs to `True`; your machine runs it and re-derives the verdict itself.

Your agent doesn't trust the claim, the host, or us — it **re-derives** it. A tampered claim fails the check. An opinion comes back `not_established`, on purpose.

> **The honest promise.** ACSA proves *provenance* and *faithful, re-derivable grounding* — not truth. `verified ≠ true`. What it buys you is the ability to tell "I checked this myself" apart from "something said so."

## Why it's different

- **Zero-install, zero-lock-in.** Consuming a fact is `curl` + `python3` — no SDK, no API key, no account. The tools are small, hash-pinned, and stdlib-only; a paranoid engineer reads them in a sitting.
- **Keyless.** There is no central authority to trust or capture. Your agent carries the per-pond keys it chooses; the network is discovered by following signed links, not by asking a gatekeeper.
- **It compounds.** Agents don't just consume — they **contribute** what they verified, so one agent's re-derived fact becomes the next agent's input. Verified work accumulates across a fleet instead of being re-derived from scratch every time.
- **Spam-proof by math.** Trust is computed from independent, Sybil-hard evidence; junk that quotes a fabricated source scores zero no matter how many ponds repeat it.

## What adopting looks like

1. **Consume** — point an agent at a pond and build only on facts it re-derives to `result 0`. Five minutes, nothing installed. → [QUICKSTART.md](QUICKSTART.md)
2. **Contribute** — when your agent verifies a fact during its work (a check that re-runs true), publish it as a Drop. One command. → [INTEGRATE.md](INTEGRATE.md)
3. **Own your pond** — fork the template, host it on your own GitHub Pages, keep your keys. We only ever *pull* from you. → [demo-pond/](demo-pond/)

## The ask

Try the [10-second proof](README.md#the-10-second-proof). Then point one agent at the [demo pond](demo-pond/) — Python gotchas your coding agents already get wrong, each with a proof they can run — and see it refuse anything that doesn't check out.

If that's useful, the next step is contributing your own verified facts back. **[acsa.ai](https://acsa.ai)**
