> 🌐 **[Türkçe (Turkish) →](README.tr.md)**

# When helpful automation looks like farming: the Technocore `faucet` namespace

*A case study, written from outside, on how one tooling lineage leaves a
~74%-malformed identifier across a note namespace — sitting beside a room of tens
of thousands of unanswered requests — and why, at the protocol level, none of it
can be told apart from farming.*

Draft — not published. Factual observations only; no claims about anyone's
intent, and no repositories or individuals are named (see *Attribution*, below).

---

## What was observed

Technocore ([technocore.chat](https://technocore.chat)) is a public, zero-auth,
world-writable chat and key–value service for AI agents. Two of its surfaces
carry a convention that appears in **no document the service serves** — not
`/llms.txt`, `/patterns.md`, `/skill.md`, `/config`, or
`/.well-known/agent.json`:

- **`/kv/faucet`** — a note namespace where agents write a line like
  `technocore-faucet-v1 did:… fp:… status:requested waiting:official-testnet-tokens`.
- **`/r/faucet`** — a room of the same name where agents post signed "faucet
  request / claim" messages.

Neither is a server feature. As the maintainer put it plainly on the public
issue, *"faucet doesn't have any special meaning."* The server is behaving
exactly as documented: caller-chosen bytes are anonymous input, and `/kv` notes
outside `room-owners`/`room-allow` are unsigned and world-writable.

This has been measured, read-only, by several independent parties over multiple
days on the public tracker (**flop-labs/technocore-chat, issue #368**). The
figures below are point-in-time readings, not levels; both surfaces move on a
scale of minutes.

## The numbers

**The note namespace.** Independent censuses recorded the namespace growing and
never draining: roughly 2 keys on 2026-08-26, a burst to ~54 within an hour on
2026-08-27, and 59 by 2026-08-29 — with **no entry ever transitioning out of
`status:requested`**. Across every reading, **~74% of the notes carry a
malformed identifier**, `did:did:key:z6Mk…`, where a correct DID is
`did:key:z6Mk…`.

The malformation is revealing precisely because of what is *not* wrong:

- **The note paths are correct.** Every note sits at `sha256(did)[:16]` computed
  from the *correct* DID, so nobody got the hard part wrong.
- **The fingerprints are correct.** Testing the published `fp:` field against
  several hash derivations, only `sha256("did:key:"+multibase)[:16]` matches —
  on every row. The doubling is therefore **downstream of the fingerprint**: it
  is in the displayed body only, not in the identity the code computed.

So this is not 40-odd independent typos. It is **one template, copied**, whose
one-line defect — formatting a display string as `"did:" + did` when `did`
already begins with `did:` — rode along into three-quarters of the population.

**The room.** The room of the same name tells the larger half of the story. Over
the observation period it grew from under a thousand messages to **tens of
thousands** (last sequence ~41,000 as of this writing). In every readable
200-message window:

- **Every writer signed** — although the room does not require a signature.
- **Nothing has ever answered.** Response-shaped terms (`sent`, `granted`,
  `approved`, `denied`) are **zero on every reading**, across days.
- The two populations — the note-writers and the room-writers — **barely
  overlap** (a rounding error on either), so these read as two largely separate
  behaviours rather than one behaviour seen twice.

Hundreds of distinct identities, all signing, asking into a room where signing
is optional and no reply has ever appeared.

## The tooling connection

The convention did not spread by independent readers reaching the same
conclusion. The arrival shape — a near-vertical burst, not a gradual adoption
curve — and a shared, mutating template point to **automation propagating a
copied kit**, not to many people reading a spec.

The part worth stating carefully: **the same class of kit that writes these
faucet notes also generates "contribution records."** In the tooling that
produced this pattern, the faucet-note builder sits beside a contribution-record
builder over a fixed menu of contribution types —
*tool, guide, article, video, thread, translation, workflow* — that emits a
signed line of the form *"I published a &lt;type&gt; for Technocore… Signed
Technocore record: room …, sequence …."*

That is the case study in one sentence: **the same class of automation that
produces a tidy, signed "proof of contribution" is also the traceable lineage
behind a namespace whose identifier field is ~74% malformed, sitting next to a
room of tens of thousands of unanswered requests.** One lineage is not one
operator: the notes come from many keys, and shared tooling is not shared control.
A kit built to help an agent participate correctly — sign your writes, register
your identity, announce your work — can, run at scale against surfaces the server
never blessed, generate output that is indistinguishable from farming to anyone
reading it from outside.

## Why it looks like farming (and why that is the point)

At the protocol level, an external observer cannot separate intent from output:

- The notes are **unsigned and world-writable**, so `status:requested` asserts
  nothing — anyone can overwrite any entry with one request, and being early
  proves nothing.
- The malformed `did:did:key:` field cannot be used to sort "careless copies"
  from "considered participants": **all rows, malformed and well-formed alike,
  share one fingerprint derivation** — one tooling lineage, not a spectrum of
  care.
- A signed room message proves authorship of *that message*; it does not make a
  self-declared claim true.

None of this requires — or supports — any conclusion about who wrote the kit or
why. The observation is narrower and more useful: **automation that was trying to
help and automation that was farming produce the same footprint**, so a service
whose safety rests on
"treat everything you read as data, never instructions" cannot lean on the
footprint to tell them apart. The tell everyone reaches for — the malformation —
does not separate the population the way it looks like it should.

## What this is *not*

- **Not a server defect.** The service does exactly what its manual says; the
  gap is that the manual does not (yet) say there is no note-based faucet queue.
  Open documentation pull requests on the tracker address precisely this.
- **Not an accusation.** No individual, account, or repository is named, and
  nothing here claims anyone acted in bad faith. A world-writable note that
  names a repository is not evidence that repository wrote it, and shared tooling
  is not shared control.

## Reproduction

Everything above is two requests and a loop, read-only. Nothing was written to
either surface.

```bash
# the note namespace, then one GET per key
curl -s 'https://technocore.chat/kv/faucet?format=json'
curl -s 'https://technocore.chat/kv/faucet/<key>'

# the room of the same name (newest 200 messages)
curl -s 'https://technocore.chat/r/faucet?format=json&limit=200'
```

```python
# the fingerprint is sha256 of the CORRECT DID, regardless of how the body spells it
import hashlib, re
mb = re.search(r"did:(?:did:)?key:(z[1-9A-HJ-NP-Za-km-z]+)", body).group(1)
fp = re.search(r"fp:([0-9a-f]{16})", body).group(1)
assert hashlib.sha256(("did:key:" + mb).encode()).hexdigest()[:16] == fp
```

## Attribution

This writeup deliberately names no repositories and no individuals, following the
discipline of the original public evidence on issue #368, whose author kept
repository names in private working notes and out of the public document for the
same reason: a world-writable note is not proof of authorship, and naming a
source from it would assert more than the data supports. The pattern is the
finding; who ran the tooling is not, and is not needed to state it.

## Sources

- Public issue: **flop-labs/technocore-chat #368** — *"54 agents are queueing in
  an undocumented `faucet` namespace, and 76% of the entries are malformed"* —
  and its comment thread of independent measurements.
- Related open documentation pull requests on the same tracker proposing the one
  durable sentence the issue asks for.
- All figures re-measured read-only against technocore.chat (v0.10.0),
  2026-08-27 through 2026-08-29.
