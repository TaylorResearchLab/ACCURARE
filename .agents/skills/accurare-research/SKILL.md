---
name: accurare-research
description: Use ACCURARE developmental, pediatric cancer, and rare disease evidence to investigate gene and disease relationships, construct alternative hypotheses, and propose reproducible tests. Use when a researcher explicitly invokes ACCURARE tools or asks to explore ACCURARE data. Access to private datasets requires an authorized ACCURARE MCP connection.
---

# ACCURARE scientific research skill (draft v0.1)

## Scientific purpose
Integrate pediatric cancer genomics, human and nonhuman primate developmental expression, pediatric tissue expression, and curated genetic disease knowledge. Retain organism, developmental stage, tissue, cell type, assay, cohort, and disease context.

## Use of tools
1. Discover which ACCURARE MCP tools are connected and which published datasets and versions are available. Do not assume a tool, endpoint, or dataset exists.
2. Query the smallest relevant evidence set using supported, bounded, read-only MCP tools.
3. Preserve source identifiers, version, analytical method, cohort definition, and licensing constraints in results.
4. Distinguish measured observations, curated assertions, computational predictions, and proposed biological mechanisms.
5. Never issue unrestricted Cypher or request direct database credentials. The MCP server enforces access scope.

## Hypothesis generation
For each proposed hypothesis:
- Write a specific mechanistic claim linking a developmental program with one or more diseases.
- Report supporting observations with exact provenance, species, tissue, stage, assay and effect direction.
- Include evidence that would distinguish the hypothesis from at least one competing explanation.
- Suggest measurable predictions and a reproducible analysis or experiment.
- Indicate the evidence needed to assess the claim and whether it is currently available.
- Provide the provenance of every consequential quantitative statement.

## Analysis safeguards
- Avoid comparing expression quantities across species or platforms without an explicit harmonization approach.
- Treat tumor expression resemblance to developmental expression as a testable association; assess cell composition and technical effects before assigning mechanism.
- Respect donor or patient independence, missingness, consent, data use agreements, and publication restrictions.
- Report unavailable tools or data as unavailable. Do not invent results.

## Intended delivery
This is a scientific workflow specification. It can be used as a repository skill in compatible clients. Broad ChatGPT distribution may require packaging this skill and the authenticated MCP service as a plugin.

## Current status
ACCURARE Hypothesis Studio and the ACCURARE MCP service are planned. No production MCP connection is declared by this file.
