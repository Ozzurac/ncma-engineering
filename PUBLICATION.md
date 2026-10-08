# Public publication boundary

## Included

- Anonymized technical case studies and component responsibilities.
- Dated measured outcomes with explicit sample sizes and limitations.
- General architecture diagrams that do not reveal private access surfaces.
- Runnable **new standalone examples** designed for public reuse.

## Deliberately excluded

- The private repositories' complete history, logs, local paths, task IDs and operational configuration.
- Secrets, access tokens, service URLs behind access control, personal data, family conversations and audio.
- Private infrastructure topology, identity/authorization setup and production tool definitions.
- Game assets, proprietary binaries, model weights, user media and other materials with uncertain redistribution rights.
- Current employer/client information, incident records, internal processes and confidential performance data.
- Benchmarks without source, environment, scope, date and limitations.

## Claim discipline

- **Implemented** means a specific component exists.
- **Tested** means a specific observed test surface passed.
- **Integrated** requires working cross-component behavior.
- **Release-ready** requires separate end-to-end and human acceptance.
- **Unknown/pending** does not mean successful.

Anyone using the public examples must independently evaluate their own security and production suitability.