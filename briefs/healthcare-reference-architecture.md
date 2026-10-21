# Healthcare Reference Architecture

**Live:** [EMRArchitect.com](https://emrarchitect.com)

## The problem

Every healthcare specialty rebuilds the same machinery: patient intake, scheduling, reminders,
records, compliance, revenue cycle — then wires an EMR integration to it, badly, once. The result is
an industry of one-off systems that can't transfer learning between specialties.

## The architectural bet

Separate four things that are usually collapsed into one codebase, and never let them re-collapse:

1. **The enduring reference architecture** — patient journey engine, workflow orchestrator,
   capability framework, provider and patient portals, compliance layer, revenue cycle. Written
   once, specialty-agnostic.
2. **The specialty capability pack** — what makes dental dental and cardiology cardiology, expressed
   as compiled domain knowledge, not forked code.
3. **The presentation shell** — a domain-specific presentation *adapter*, deliberately thin.
4. **The first concrete implementation** — which validates the architecture but never defines it.
   Dental is example #1, not the product.

Success metric: not "did the dental app ship" but **how cheaply does the *next* specialty compile** —
medical, physical therapy, veterinary, cardiology.

## Governance before capability

The orchestrating agent in a clinical setting operates under absolute constraints — it never
diagnoses, never prescribes, never orders, never bills. It orchestrates: drives workflow state,
detects missing information, explains next steps, and escalates governed actions to humans. This is
enforced architecturally (the capabilities don't exist on its rail), not by prompt.

## The EMR question, answered as a seam

"How do we integrate with MyChart?" is the wrong question; the right one is "where is the FHIR
seam?" The architecture places a connector layer behind the orchestrator: Epic (or any FHIR R4
system) reduces to a base URL plus auth. The proof-of-concept runs live — real FHIR `Appointment`
writes against a zero-PHI sandbox, with production writes gated behind explicit human approval
(`write_enabled: false` by default).

## What running it taught me

- PHI boundaries must be decided *per component* before any AI vendor touches the stack — some
  realtime AI services are architecturally fine but contractually wrong for PHI.
- A capability pack is only real when the second one compiles. One instance of anything is a
  prototype wearing an architecture costume.
- Clinical users don't adopt agents that act; they adopt agents that *prepare* — the approval gate
  is a feature, not friction.
