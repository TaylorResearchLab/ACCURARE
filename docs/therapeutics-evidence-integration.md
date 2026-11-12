# ACCURARE Therapeutics Evidence Integration (proposal v0.1)

Status: **design proposal; no graph import performed**. This document extends the ACCURARE U24/DDKG foundation to drug, intervention, treatment, and therapeutic hypothesis evidence.

## Scientific purpose

Link childhood cancer and rare/developmental disease genetics to drug targets, molecular mechanisms, treatment regimens, trials, genomic response biomarkers, and human developmental expression across tissues and stages. Provide a researcher-facing Therapeutics Explorer and governed read-only MCP tools; retain a private copy of the DDKG as the principal scientific knowledge infrastructure.

### Existing Taylor Lab work to reuse

1. `TaylorResearchLab/PMTL_paper`: FDA Pediatric Molecular Target List (PMTL), mapped childhood and adult cancer types, ClinicalTrials.gov drug interventions, Open Targets drug-target and disease relationships.
2. `TaylorResearchLab/CANTOS`: cancer trial terminology standardization across ClinicalTrials.gov, WHO cancer naming, and NCIt. Preserve existing decisions to use exact/substring matching before applying fuzzy matching to unresolved interventions and to split combination therapies into components while retaining the original regimen.
3. `TaylorResearchLab/DDKG_OpenPedCan_Integration`: existing entry point for integrating childhood cancer analyses into DDKG; inspect implementation before deciding schema mutations.
4. Existing DDKG datasets: IDG, LINCS perturbation and compound bioactivity evidence, and any current MED-RT/NCIt/DrugBank vocabulary relationships. The actual graph must be inventoried before claiming coverage.
5. U24OD038422: original childhood cancer and structural birth defect knowledge graph and analysis objectives.

## Initial public source candidates

| Source | Contribution | Publication conditions |
| --- | --- | --- |
| CIViC | Curated variant/cancer/therapy response evidence with evidence levels and assertions | CC0; retain version and citations |
| Open Targets | Drug, target, indication, clinical stage and evidence links; aggregated data | Platform CC0; audit individual source terms and avoid laundering proprietary upstream data |
| ChEMBL | Chemical bioactivity, target, assay, mechanism, clinical compounds | Review CC BY-SA 3.0 and original assay publications |
| ClinicalTrials.gov | NCT studies, intervention names, conditions, trial phase and status | Follow ClinicalTrials.gov terms, revisions, and source attribution |
| NCIt | Antineoplastic and treatment terminology, names, synonyms, biological concepts | Review NCIt source license and third-party content |
| DGIdb | Aggregated drug-gene interactions with component source attribution | Review constituent source rights and relationship evidence |
| openFDA | FDA drug labeling and selected public drug information | Preserve label identifier and revision; distinguish labeled indication from clinical efficacy evidence |

DrugBank remains a **restricted/provenance-sensitive optional input**. Its drug content and derivatives must not be redistributed publicly or included in commercial products without an appropriate agreement. Maintain license boundaries for source-specific relations.

## Model design: preserve identity and evidence

### Entity classes

- `DrugMolecule`, `DrugProduct`, `Biologic`, `CellTherapy`, `TreatmentRegimen`, `Intervention`: distinguish compounds, brand/formulation, biologics and cells, combination therapies, non-drug interventions.
- `Gene` (HGNC), `GeneProduct` (UniProt), `Pathway`, `MolecularAlteration`, `ExpressionProgram`.
- `CancerType` and `Disease` (NCIt, MONDO, other established identifiers), `Phenotype` (HPO).
- `Study` (NCT), `Publication` (PMID/DOI), `SourceRecord` (native assertion), `Evidence` / `PortableAssertionRecord` as available.

### Source-native relations / context

- Drug acts on gene product / has tested bioactivity / changes expression signature / has regulatory indication.
- Variant predicts sensitivity, resistance or toxicity for a named therapy in a named cancer context.
- Intervention studied in a specified clinical trial, with status, phase, arm, and cancer cohort.
- Combination regimen contains intervention components, with original free-text intervention retained.
- Disease has a curated therapy association, with its original evidence scope.
- Gene exhibits a developmental expression pattern in species, tissue, stage and assay (expression observation, not a drug-target link).

Graph edges carry source-native predicate, normalized predicate, evidence class, original and normalized identifiers, provenance, release/version, references, applicability, license policy, and optional derived inference identifiers. Avoid assuming target gene expression implies therapeutic sensitivity.

### Evidence classes to keep separate

1. Regulatory indication and actual approved label.
2. Curated predictive biomarker: sensitivity, resistance, toxicity, with cancer type and alteration scope.
3. Clinical intervention/trial: tested, recruiting, completed, etc.; **trial enrollment is not efficacy evidence**.
4. Pharmacology: direct binding/target engagement, inhibition/activation or other mechanism.
5. Experimental functional assay or LINCS perturbation signature.
6. Computational target or repurposing hypothesis.

A ranking system should report evidence by class and source before any combined score. Do not collapse clinical trial enrollment, in vitro activity, and approval into an undifferentiated `TREATS` edge.

### Identifier policy

- Retain native codes and source strings; normalize drugs via NCIt/RxNorm/ChEMBL/other crosswalks where rights permit.
- Keep molecular target identity distinct: HGNC gene versus UniProt protein target.
- Maintain NCT study IDs, MONDO/NCIt disease IDs, and PMID/DOI for source evidence.
- Preserve multi-component regimens and distinguish molecular complexes from single-gene targets.
- Do not use fuzzy or embedding similarities as verified drug identity matches without review.
- Preserve explicit `no match` and ambiguous outcomes.

## First biological demonstration

Start with two U24-aligned indication groups:
1. Neuroblastoma and hematological malignancies with congenital heart defect gene overlap.
2. Pediatric brain tumors with genes associated with congenital CNS abnormalities.

For each cohort or cancer subgroup:
1. Obtain a versioned gene/variant evidence set from OpenPedCan, Kids First or the original NCI Molecular Targets Project.
2. Join to DevMap and curated disease gene claims, without conflating membership with causation.
3. Add developmental tissue expression and human/NHP comparisons.
4. Retrieve drugs and clinical interventions by validated protein targets, alteration-specific sensitivity evidence, or curated cancer indications.
5. Return evidence grouped by class, indication, sample type and developmental context; generate testable candidate mechanisms for expert review.

Example hypothesis (not result): a developmental gene involved in a childhood cancer pathway may provide a context-dependent candidate treatment target, conditional on pathway activity, alteration effect and tissue expression.

## Architecture and safety

- Import/update research DDKG on restricted infrastructure after source and schema audits.
- Produce an independently versioned, rights-cleared **ACCURARE publication graph** for server-side API/MCP access.
- Serve only approved, bounded public JSON summaries at `accurare.org`; no raw graph dumps or direct Neo4j drivers.
- Authenticate restricted MCP sessions; use parameterized queries, fixed query shapes, rate limits, audit logs, scoped access and per-source data rules.
- Public therapeutic outputs must be labeled with source evidence and analytical context; research hypotheses are distinguished from validated treatment indications.

## Proposed MCP tools (not yet implemented)

- `get_drug(drug_id)`
- `get_drug_targets(drug_id, evidence_class, limit)`
- `get_therapies_for_cancer(cancer_id, evidence_class, limit)`
- `get_variant_therapy_evidence(variant_id, cancer_id, limit)`
- `get_intervention_trials(intervention_id, cancer_id, limit)`
- `get_target_developmental_context(gene_id, species, tissue, stage, limit)`
- `compare_therapeutic_hypotheses(hypothesis_ids)` (precomputed analytical results only)

## Verification before implementation

1. Inventory existing drug, gene product, trial, treatment, NCIt, MED-RT, IDG, LINCS and crosswalk nodes/edges in the current DDKG copy.
2. Trace and verify the latest PMTL and CANTOS outputs and source licenses; document exact match counts instead of carrying over preliminary totals.
3. Identify license-restricted relations and isolate them from public exports.
4. Freeze an initial source manifest, integration schema, and benchmark set of manually reviewed therapeutic associations.
5. Implement a small versioned pilot before bulk import or public therapeutic search.

## Source links

- https://github.com/TaylorResearchLab/PMTL_paper
- https://github.com/TaylorResearchLab/CANTOS
- https://civicdb.org/releases/licensing
- https://platform-docs.opentargets.org/licence
- https://chembl.gitbook.io/chembl-interface-documentation/web-services/chembl-data-web-services
- https://clinicaltrials.gov/data-api/how-download-study-records
- https://dgidb.org
- https://open.fda.gov/apis/
- https://evs.nci.nih.gov/thesaurus-downloads
