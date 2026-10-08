# Case study: Web Research V1

**Area:** Retrieval, web extraction, source attribution, local LLM integration

**Status:** Development / integration in progress (2026-10 checkpoint).

## Goal

Give a local-first desktop agent a web research path that can acquire sources, extract useful text and return an answer **grounded in inspectable evidence**, instead of letting model confidence stand in for provenance.

## Component design

```text
User research request
        |
        v
Search provider (SearXNG)
        |
        v
Extraction worker (Crawl4AI)
        |
        v
Normalize and keep source references
        |
        v
LLM synthesis with source citations
        |
        v
User answer + inspectable sources
```

## Implemented vs. pending

Search and extraction services were deployed in an isolated OCI environment, with bounded component and security tests documented during October 2026. The locally served Qwen synthesis and complete end-to-end citation behavior were **still pending integration** at that checkpoint.

The development objective is to prevent unsupported citations, retain the origin of extracted passages and report extraction failures without manufacturing a complete answer.

This is a **case study of active work**, not a benchmarked public search product or a deployed service endpoint.