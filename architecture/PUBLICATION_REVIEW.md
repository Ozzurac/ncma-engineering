# Architecture publication review

Every public NCMA diagram and code example should survive this checklist *before publication*. Sanitizing a drawing requires more than erasing the IP addresses.

## Allowed public representations

- Functional components and **logical** trust / authority boundaries.
- Generalized source-to-evidence and specification-to-validation flows.
- Fictional request and response examples created independently for publication.
- Public technology names where technically relevant and already reviewed.
- Dated, attributable measurement results with method, scope and caveats.
- Explicit failure paths, human approval, refused operations and UNKNOWN states.

## Never publish from private systems

- Real network topology, host lists, IPs, VPC/VNet/subnet details, ports, tunnels, protected DNS names, cloud account/resource IDs, tenant topology or firewall / Access policy.
- Private MCP tool catalogs, mutation schemas, confirmation token mechanics, replay protection internals or privileged execution entry points.
- Environment files, secrets, access identities, credentials, API tokens, user media, chats, employer/client records or personal data.
- Raw traces, query logs, datasets, retrieval corpus, embeddings, prompts, original training checkpoints, model weights or private Git history.
- Proprietary game resources, copyrighted assets or screenshots whose redistribution rights are unclear.
- Synthetic results styled as if they were measured production telemetry.

## Review method

1. **Inventory the exact commit/branch**: list tracked paths, file extensions and linked resources, including Mermaid source and images.
2. **Check truth claims**: label implemented, tested, integrated, owner-accepted and pending separately; verify each number against an evidence record.
3. **Generalize architecture**: replace infrastructure coordinates and service wiring with capability zones and responsibilities. Remove secrets from hidden diagram source too.
4. **Threat review**: ask whether combined diagrams reveal a privilege path, control-plane endpoint, weak boundary or lateral-movement route even without an IP.
5. **Rights and provenance**: publish original independently authored content; do not push private repository history as a shortcut.
6. **Validate**: preview Mermaid, check links, inspect diff and run any relevant tests and scanning.
7. **Review the history**: a deleted secret may still exist in prior commits; rotate compromised credentials and remediate history separately.
8. **Human release gate**: review the entire public diff before merging, especially when adding a new project, screenshot, diagram or measured claim.

## Disclosure principles

- A conceptual arrow is **not** proof of a deployed integration.
- Retrieval ranking is **not** a domain authority.
- A PASS from an internal check is **not** user acceptance.
- An observed measurement is **not** a forward-looking reliability guarantee.
- If sanitization removes information necessary to substantiate a claim, **lower the claim**, do not imply hidden evidence supports more than it does.

Parent policy: [PUBLICATION.md](../PUBLICATION.md).
