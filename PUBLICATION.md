# Public publication boundary

## Included

- Anonymized technical case studies and component responsibilities.
- Independently authored, high-level **professional-work** case studies describing the portfolio author's contributions and general engineering patterns, with clear employer attribution and non-endorsement statements.
- Dated measured outcomes with explicit sample sizes and limitations.
- General architecture diagrams that do not reveal private access surfaces.
- Runnable **new standalone examples** designed for public reuse.

## Deliberately excluded

- The private repositories' complete history, logs, local paths, task IDs and operational configuration.
- Secrets, access tokens, service URLs behind access control, personal data, family conversations and audio.
- Private infrastructure topology, identity/authorization setup and production tool definitions.
- Game assets, proprietary binaries, model weights, user media and other materials with uncertain redistribution rights.
- Non-public employer/client information, actual incident records, internal company procedures/playbooks, proprietary implementation sequences, protected communications, confidential performance data and original employer-controlled assets.
- Benchmarks without source, environment, scope, date and limitations.

See the [architecture publication checklist](architecture/PUBLICATION_REVIEW.md) for a separate truth, threat and redistribution review before publishing new flows or diagrams.

## Professional employment attribution

The independently authored [Operational Backlog Compliance](projects/operational-backlog-compliance.md) and [Evidence-Assisted Backlog Triage](projects/evidence-assisted-backlog-triage.md) accounts describe **general engineering responsibilities**, based on the author's experience during employment at Accenture.

They do not establish Accenture authorization or endorsement, are not public releases of employer systems and should not be mistaken for NCMA-owned products. Generalized portfolio language must not be used to disclose restricted process logic, source, source data or unpublished operational metrics. If a claim cannot be substantiated without protected information, omit or qualify it rather than imply unseen proof.

## Claim discipline

- **Implemented** means a specific component exists.
- **Tested** means a specific observed test surface passed.
- **Integrated** requires working cross-component behavior.
- **Release-ready** requires separate end-to-end and human acceptance.
- **Unknown/pending** does not mean successful.

Anyone using the public examples must independently evaluate their own security and production suitability.