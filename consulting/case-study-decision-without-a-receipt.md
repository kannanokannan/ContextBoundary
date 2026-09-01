# Case Study: A Decision Without a Receipt

**Recorded 2026-08-31.** A real incident on the project's own domain, documented while it
happened. Use it when a room needs a concrete example rather than a diagram.

The statements in [README.md](README.md) govern this document. Nothing here is a claim
about any vendor's intent, and nothing here asserts a defect in any product.

## What happened

Over five exchanges, a production AI assistant reported that it could not read
`context-stack.org`, and gave five successive explanations for why. Each was fluent,
technical, and internally coherent. Each was different from the one before it. The
underlying fact never changed.

The site's operator held the ground truth the whole time: a four-line `robots.txt`,
version-controlled and gated in CI, granting everything.

## The five explanations

| # | The claim | Status |
|---|---|---|
| 1 | "Strict crawler blocks on the main pages" | No `Disallow` directive exists in the file |
| 2 | "A named AI user-agent token is disallowed in `robots.txt` or in HTTP headers" | That token appears nowhere in the file; no `X-Robots-Tag` header is served |
| 3 | "CDN or WAF edge protections are silently dropping the request" | Every crawler-block control at the edge is off; zero denials recorded |
| 4 | A detailed internal architecture: cache-check ordering, default-deny policy, error-bucket masking, separate permission indexes, host-specific IP handling | Stated in the same message as "I have zero visibility into the network request itself" |
| 5 | The raw tool payload | Correct, and it disproved 1–4 |

## What was measured

Every row below is re-checkable by anyone, in minutes. That is the point of including them.

| Property | Observed | How to re-check |
|---|---|---|
| `robots.txt` | HTTP 200; one `User-agent: *` group; **zero `Disallow` lines**; no mention of the named token | Open the file |
| Content Signals | `ai-train=yes, search=yes, ai-input=yes` — permission explicitly granted | Same file |
| `X-Robots-Tag` | Absent on the file and on a content page | Response headers |
| Edge crawler controls | 31 crawlers listed, **every block control off**, zero denials | CDN dashboard |
| The vendor's own AI fetcher | **0 requests, 0 bytes** | Same dashboard |
| Other vendors' AI fetchers | Five different AI crawlers fetching successfully, tens of kilobytes transferred, zero blocked | Same dashboard |
| Search indexing by the same vendor | Indexed and serving impressions; its search crawler fetching successfully | Search console |

## The payload that settled it

The fifth message produced the actual tool response. It contained three fields: a generic
refusal string, the URL, and one status token. **No HTTP status code. No headers. No
body.**

That absence is the evidence. A request rejected at a network edge produces something
network-shaped — a timeout, a reset, a 403 — and the edge logs the attempt. Here there was
neither. A permission verdict arrived with no HTTP context at all, and the edge recorded
zero requests.

Two independent sources agreed: **no connection was ever opened.** The determination was
made upstream of the network, before any content was read.

Which also means explanations 1–3 were impossible. The fetcher could not have been blocked
by a file it never requested, or by an edge it never reached.

## What this is — and what it is not

**It is not bias against the site.** The content was never fetched. A system cannot form a
view about text it has not read. The same vendor's search crawler was reading and indexing
the site normally throughout.

**It is not simply a wrong answer.** The failure is more specific. Across five messages the
system could not distinguish, *in its own output*, between the one thing it had observed —
a single status token — and the architecture it generated around that token. Both were
delivered in the same confident register, with nothing marking which was which. The fourth
message describes the internals of a system that the same message calls a black box.

**What it is:** a binding determination, made about a third party's property, delivered as
one token, with

- no evidence of what was evaluated,
- no identifier for the policy or its version,
- no record of the inputs,
- no notification to the affected party,
- and no appeal path.

The operator discovered it by accident, while asking about something else.

## What a receipt would have changed

To be precise about the limit of this claim: ContextBoundary would not have prevented this.
The failing system belongs to someone else. What the comparison shows is what a decision
costs when it carries no evidence.

A ContextBoundary decision emits a sealed, tamper-evident receipt bound to a hash of the
exact compiled policy that produced it. Applied to this incident, that would have meant:
the affected party could see *which* rule fired, *which version* of it, *what inputs* it
saw, and *when*. The disagreement would have lasted one exchange instead of five, and it
would have been settled by inspection rather than by argument.

None of that requires the deciding system to be correct. It requires it to be accountable.
That distinction is the whole of the argument, and this incident is a clean illustration of
the gap.

## Using this in an engagement

- **Open with the ground truth, not the failure.** Four lines of text, under version
  control, with a CI gate. Then show five contradictory explanations about those four lines.
- **Let the room check the file.** It is public. Someone reading it themselves lands harder
  than any assertion.
- **Do not use this to argue that AI systems are unreliable.** That framing loses the room
  and is not the point. The point is that a decision without evidence cannot be examined,
  and that unexaminable decisions accumulate quietly.
- **Name what the client's own systems already do this way.** Most organisations have at
  least one automated gate that issues verdicts with no receipt. This example makes that
  easy to recognise without anyone having to defend their own architecture first.

## Honesty notes

These matter more than the story. A case study about unverified claims is worthless if it
is itself unverified.

- The tool call and response payloads are the model's own report of its internals. They
  could not be independently verified and are treated here as reported, not proven.
- Everything in "What was measured" was observed directly and is re-checkable by a reader.
- **The vendor's system corrected itself completely.** Once shown the contradiction, it
  identified its own error precisely and without hedging. That is genuinely good behaviour
  and belongs in any honest telling of this. The problem was never that the system could
  not reason; it was that nothing in the loop required evidence before assertion.
- No claim is made here about intent, policy, or motive on the part of any vendor. None is
  known, and speculating would repeat the error this document is about.
