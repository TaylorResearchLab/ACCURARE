# ACCURARE private AI research access (architecture draft)

## Purpose

ACCURARE connects childhood cancer, rare genetic disorders, and human and nonhuman primate developmental expression. The public website is hosted on GitHub Pages at `accurare.org`. The research graph and restricted datasets remain behind controlled services.

## Distinct components

| Component | Function | Deployment |
| --- | --- | --- |
| `accurare.org` | Public homepage, released evidence, and browser visualizations | GitHub Pages (static) |
| ACCURARE skill | Instructions for evidence retrieval and hypothesis evaluation | Versioned `SKILL.md` distributed to compatible AI clients |
| ACCURARE MCP server | Structured, authenticated read-only tools | Private service with OAuth 2.1 or controlled private tunnel |
| Scientific API | Shares logic with MCP server; approved JSON summaries for the browser | Server-side HTTPS service |
| Publication graph | Validated, license-cleared subset of DDKG relationships | Private Neo4j Community Edition instance |
| Full research DDKG | Source integrations and analysis work | Existing research infrastructure |
| Model | Natural-language reasoning and tool orchestration | Researcher's supported AI account, or separately funded hosted service |

## Data and access boundaries

- Only reviewed and license-cleared relationships may be published in browser-accessible JSON.
- Authenticated MCP callers receive bounded responses according to their authorizations.
- Never expose Neo4j credentials, Bolt ports, unrestricted Cypher, arbitrary SQL, or model API keys to browser clients.
- Neo4j Community Edition is not the security boundary for a multitenant public service. Authorization, input validation, audit logging, rate limiting, and data scopes must be enforced by the API/MCP layer; use a dedicated publication graph.
- Each query response carries identifiers, source, version, provenance, and context as applicable.
- Data use agreements, human-subjects privacy constraints, and third-party rights govern which datasets can be returned.

## Prospective MCP tool interfaces (not deployed)

- `list_datasets()` lists approved datasets, versions, and access conditions.
- `find_gene(symbol_or_id)` resolves identifiers and available evidence.
- `get_cancer_evidence(gene_id, cancer_type, limit)` returns bounded OpenPedCan-derived evidence.
- `get_developmental_expression(gene_id, species, tissue, stage, limit)` returns comparable expression observations and metadata.
- `get_disease_assertions(gene_id, disease_id, limit)` returns curated associations with sources.
- `compare_gene_programs(program_ids, context)` requests a predefined analysis and returns a versioned result if available.
- `get_evidence_bundle(assertion_ids)` supplies provenance and citation material.

These are proposals. The production server will define exact tool names and schemas.

## Researcher workflow

1. A researcher installs the ACCURARE skill or an ACCURARE plugin in a compatible AI environment.
2. The researcher authorizes a private ACCURARE MCP connection when restricted data access is appropriate.
3. The AI client uses its own model and invokes the MCP tools to retrieve evidence.
4. The skill guides the model through evidence review, alternative mechanisms, predictions, and reproducible tests.
5. The researcher can export a structured hypothesis with citations and source versions.

This model can shift LLM inference cost to researchers' AI subscriptions where supported. ACCURARE still pays for MCP/API hosting, graph storage, access control, maintenance, and any compute-intensive analyses. A sponsored hosted LLM service remains an optional later offering.

## Initial release

Release the homepage, documentation, and instruction-only skill first. Publish the first dataset only after provenance, quality, licensing, and scope checks. Develop the authenticated MCP server and its scientific evaluation tests before opening access.
