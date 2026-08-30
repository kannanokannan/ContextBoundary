# Demonstration Guide

Every other document in this directory helps you **assess** a client's boundary. This one helps you **show them one working**.

The distinction matters. An assessment produces a score and a gap list, which a client can dispute or defer. A demonstration produces an observed behaviour and a piece of evidence, which is harder to argue with and much harder to forget.

Read the claim statements in the [README](README.md) before using this in front of a client. They apply to everything below.

## What you are demonstrating

Three things, in this order:

1. **Enforcement sits outside the model.** The decision is made by a compiled policy, not by asking an LLM whether an action is acceptable.
2. **The decision is reproducible.** The same policy and the same action produce the same outcome, on any machine.
3. **The decision leaves evidence.** Every outcome emits a sealed receipt bound to a hash of the exact policy that produced it, so a reviewer can establish *which* rules were in force when the decision was made.

If a client takes away only one of these, make it the third. Most organisations already accept that AI needs controls; very few have thought about what evidence those controls should leave behind.

## Before the session

Run it yourself first, on the machine you will present from. Do not demonstrate anything you have not personally seen work that day.

```
git clone https://github.com/kannanokannan/contextboundary-gw
cd contextboundary-gw
npm ci
npm run test:conformance
```

You need Node and OPA available. Expect 22 scenarios green, in roughly ten minutes end to end including the clone.

**If it does not pass, do not present it.** Report the failure rather than narrating around it — an unexplained red in front of a client costs more than a cancelled demo.

## Running the demonstration

**1. Show the suite pass.** Twenty-two scenarios, five of them adversarial — the attack is placed inside prompts and tool results, on the assumption that the model is already compromised and the boundary must hold anyway. That framing usually lands harder than the number.

**2. Show a denial.** Pick a scenario where an agent attempts an egress its tier does not permit. The useful part is not that it was denied; it is that nothing consulted a model to decide.

**3. Open the receipt.** Show the fields — the decision, the rule, the accountable owner, the tier in force, the policy hash. Then ask the question that does the work:

> "When your AI system refuses something today, what can you show a regulator six months later about why?"

Most clients have no answer. That silence is the engagement.

**4. Connect it to their own gap list.** Return to whichever assessment category scored lowest and describe what the equivalent evidence would look like in their estate. Do not promise it; describe it.

## What to say when asked "can we just use this?"

You will be asked. The honest answer has three parts:

- It is Apache-2.0 and self-hostable, so yes, in the sense that nothing stops them.
- It is a **reference implementation maintained by one person**, with no independent review and no production users. It demonstrates the control; it is not a supported product, and nobody should be told otherwise.
- What it is genuinely useful for is **settling arguments about what "enforced" means**. A team that has watched a denial and inspected the receipt will specify their own control differently.

Say all three. Saying only the first is the failure mode this package exists to prevent.

## What not to do

- **Do not present a receipt you did not generate in that session or capture from a real run.** A fabricated receipt in a governance engagement is the single most damaging thing in this directory. If the live demo fails, show nothing.
- **Do not describe the gateway as conformant, certified or approved.** See the README.
- **Do not imply the client is buying anything.** They are being shown an open-source reference so that they can specify their own requirement.
- **Do not demonstrate against client data.** Use the shipped scenarios.

## Where this fits

| Phase | This guide's role |
|---|---|
| 1 · Discovery Workshop | Optional closer, if the room is technical and time allows. |
| 2 · Current-State Assessment | Establishes a shared reference for what "enforced" looks like before scoring against it. |
| 5 · Remediation Roadmap | Lets the target state point at something runnable rather than a diagram. |
| 6 · Executive Readout | Do not demo here. Executives need the evidence question, not the terminal. |
