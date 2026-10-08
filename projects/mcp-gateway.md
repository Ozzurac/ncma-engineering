# Case study: NCMA MCP Gateway

**Area:** MCP, API integration, constrained automation, operational security, auditability

**Status:** Active internal engineering system; public content is architectural and omits operational access details.

## Problem

Connecting an LLM to operating-system and production tools is trivial compared with determining **what it may actually do**. An agent that can propose arbitrary shell commands, invent paths or equate a response with success is not suitable for governed operations.

## Architecture

```text
User intention
     |
Model/client proposes a named capability
     |
MCP bridge with fixed allowlist
     |
Gateway validates arguments, scope and risk
     |
+------------------+-------------------------+
| Read-only action | Consequential mutation  |
| Observe evidence | Prepare exact operation  |
| Return result    | Human confirmation       |
|                  | Execute bounded action   |
|                  | Verify / record outcome  |
+------------------+-------------------------+
```

## Engineering choices

- Static allowlists and explicit input contracts, rather than arbitrary tool exposure.
- Separate ownership of **domain truth** (domain engine) and **execution authority** (gateway).
- Preparation and single-use confirmation on consequential operations; proposed arguments do not grant permission.
- Idempotency, operation identity and receipts to make retry/replay behavior auditable.
- Bounded execution and refusal when targets or evidence cannot be verified.
- Deployment/cutover as a separate concern from working-tree source changes.

## Trade-offs and lessons

Strong boundaries can become counterproductive if they prevent real end-to-end product testing. The objective is **controlled working functionality**, with observability and a usable human confirmation flow, not endless defensive scaffolding without a working path.

No private tool schema, service hostname, Cloudflare identity, key, audit token or sensitive infrastructure wiring appears in this case study.

**Related public code:** [Agent Governance Lab](https://github.com/Ozzurac/agent-governance-lab) demonstrates a reduced controller/verification loop without network or system privileges.