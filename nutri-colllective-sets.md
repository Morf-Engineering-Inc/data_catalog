data-catalog/registry/datasets.yaml — 52 datasets, split in_house (40) vs third_party (12), each with path, size, format, license, and provenance notes. Covers everything from the evidence-platform registers and Postgres corpora to the big downloads (FDC, FooDB, Open Food Facts, VMH/AGORA/Recon3D, the Harvard Dataverse food dataset).
data-catalog/README.md — quickstart explaining the schema and what's not built yet (the data/ and scripts/ automation from the original plan).
The new predictor_ledger / precursor-register dataset (your Sept 3 in-house build — 71 targets, an availability ledger of nutrient-standardization precursor chains) is catalogued as the newest in-house entry.
vmh_gene_harvest's

# Data Catalog

A pointer index of every dataset behind NUTRI-COLLECTIVE_0 — where it lives, how
big it is, and whether it's something the user built (`in_house`) or downloaded
from elsewhere (`third_party`). Built out from the plan in `../data_catalog_layout.md`.

**Nothing is copied here.** Per that plan's "reference, don't copy" rule,
`registry/datasets.yaml` just points at the real absolute paths — the 2GB+ of
third-party downloads and the multi-GB Postgres corpora stay exactly where they
are.

## Using it

Open `registry/datasets.yaml`. Each entry has:

- `category` — `in_house` or `third_party`
- `local_path` — where the data actually is right now
- `source_url` — where to re-download it (third-party) or a one-line note on
  how it was built (in-house)
- `license` — best known; several third-party sources have terms that block
  redistribution, check before shipping anything derived from them
- `notes` — anything that would make you misuse the number: superseded stores,
  generated-vs-authored, known contradictions with older notes, etc.

## Newest dataset

`precursor_register` (`01_nutri-collective/predictor_ledger/precursor-register/`)
— the availability ledger of dated precursor chains behind nutrient-standardization
milestones, created 2026-09-03. Read its own README before touching it: it
explicitly forbids using prize-year dates as discovery dates and forbids
`caused_by` edges — it's an availability ledger, not a causal-claim dataset.

## Known issue (confirmed)

`vmh_gene_harvest` really is truncated: its own metadata fields (`count`,
`n_results`, `results`) all claim 30,000, but the actual data ends
alphabetically at gene symbol "SIX6" with zero SLC25* entries anywhere in the
file. Don't trust the count field — use NCBI Entrez for anything past "SIX6".

## Not built yet

The original plan (`../data_catalog_layout.md`) also sketched `data/raw/`,
`data/processed/`, and `scripts/{download_source,update_registry}.py` for
automating re-downloads and size checks. Those don't exist yet — this pass
only populated the registry itself. Build them if/when actually re-downloading
something becomes a recurring need.


# Master dataset registry for NUTRI-COLLECTIVE_0.
#
# Schema per data_catalog_layout.md, extended with:
#   category: in_house  -> authored/curated/harvested by the user
#             third_party -> downloaded from an external source, re-downloadable
#   license: best-known licence/terms; "unclear" where the source itself is ambiguous
#   verified: date this entry's numbers were last checked against the live filesystem/db
#   notes: anything a future reader needs to not misuse the number (superseded stores,
#          contradictions with older notes, generated-vs-authored, etc.)
#
# "local_path" is the real absolute path where the data actually lives today.
# Per the layout doc's "Reference, Don't Copy" principle, nothing has been moved
# or copied into data-catalog/data/ — this registry is a pointer index only.

datasets:

  # ============================================================
  # IN-HOUSE — authored, curated, or harvested by the user
  # ============================================================

  - id: precursor_register
    name: Precursor Register (predictor_ledger)
    category: in_house
    description: >
      Availability ledger of dated precursor chains behind nutrient-standardization
      milestones (vitamin/mineral discovery, L8 fortification statutes, L9 metrology
      standards). Explicitly NOT a causal-claim dataset — schema forbids prize-year
      dates as discovery dates and forbids "caused_by" edges.
    source_url: internal (authored from LLM research passes; raw transcripts kept alongside)
    local_path: 01_nutri-collective/predictor_ledger/precursor-register/
    format: "JSON (33 files: all_targets.json, catalog.json, set_a/b/c/d.json, SCHEMA.json, absences.json, ID_MAP.json, UNIVERSE_PARTITION.json, l8-*/l9-* milestone files, LAYER-*.md, README.md)"
    size: 71 targets, 160 precursor_index entries, 29 shared_precursors
    update_frequency: manual, active development (created 2026-09-03)
    projects_using_this: [nutri-collective]
    tags: [nutrition, standardization, ledger, authored, NEW]
    license: internal / unpublished
    verified: 2026-09-04
    notes: >
      New as of 2026-09-03. README states explicitly: "Availability ledger only.
      No prize years as discovery_year. No averaging. No caused_by." Raw research
      transcripts that fed it (research_beta.md, research_beta2.gemini.md,
      researc_beta3.md) are kept in the same directory as provenance record, not
      as separate datasets.

  - id: standardization_ledger
    name: Standardization Ledger
    category: in_house
    description: Adoption-tracker rows — orgs, L0-L9 layer scores, events, and predictions with a meter-history log.
    source_url: internal
    local_path: 01_nutri-collective/predictor_ledger/data-set/standardization-ledger.v1.json
    format: JSON
    size: 95 KB / 2655 lines
    update_frequency: manual, --stamp after every edit
    projects_using_this: [nutri-collective]
    tags: [standardization, ledger, authored]
    license: internal / unpublished
    verified: 2026-09-04
    notes: >
      "Authored, except the three stamped blocks (snapshot, scoreboard,
      meter_history)" per its own README. Open rows overdue 14 days after
      resolves_by fail `make test`.

  - id: completeness_ledger
    name: Completeness Ledger
    category: in_house
    description: Dashboard-facing completeness rollup of the standardization/precursor ledgers.
    source_url: internal
    local_path: 01_nutri-collective/predictor_ledger/completeness-ledger-v1.json
    format: JSON
    size: 3.4 KB
    update_frequency: manual
    projects_using_this: [nutri-collective]
    tags: [standardization, dashboard, authored]
    license: internal / unpublished
    verified: 2026-09-04
    notes: co-located with completeness-dashboard-v1.html which renders it.

  - id: nutrient_register
    name: Nutrient Register
    category: in_house
    description: Curated nutrient rows (ids, sources, Upper Limits, envelope data) for all 169 nutrients.
    source_url: internal
    local_path: 01_nutri-collective/evidence-platform/site/nutrient-register.v1.json
    format: JSON
    size: 444 KB, 169 rows
    update_frequency: regenerated via `make nutrients`
    projects_using_this: [evidence-platform, mcp]
    tags: [nutrition, generated, published]
    license: see licence block in file
    verified: 2026-09-04
    notes: >
      GENERATED, not hand-edited — source of truth is nutrient_provenance_curated
      below; edit that, then `make nutrients`. ODS-sourced UL blocks are behind a
      bot wall; verify via Wayback Machine, not a live fetch.

  - id: nutrient_provenance_curated
    name: Nutrient Provenance (curated source)
    category: in_house
    description: Hand-curated input policy file that generates nutrient_register.
    source_url: internal
    local_path: 01_nutri-collective/evidence-platform/policy/nutrient-provenance.curated.json
    format: JSON
    size: 121 KB
    update_frequency: manual, edit this not the generated register
    projects_using_this: [evidence-platform]
    tags: [nutrition, authored, policy]
    license: internal / unpublished
    verified: 2026-09-04

  - id: food_fdc_curated
    name: Food-to-FDC Curated Join
    category: in_house
    description: >
      93 in-repo foods -> 177 matched FDC records -> 7,976 components,
      joined down to 80 nutrient rows. Signed policy file.
    source_url: internal (join built over USDA FoodData Central, see fdc_food_data_central)
    local_path: 01_nutri-collective/evidence-platform/policy/food-fdc.curated.json
    format: JSON
    size: 46 KB
    update_frequency: manual
    projects_using_this: [evidence-platform]
    tags: [nutrition, food, join, authored, signed]
    license: internal / unpublished (built over USDA public-domain data)
    verified: 2026-09-04
    notes: backend Postgres seed built from this is untested per earlier notes.

  - id: standardization_taxonomy_curated
    name: Standardization Taxonomy (curated source)
    category: in_house
    description: Curated source feeding the in-repo taxonomy ledger; layer IDENTITY gated by a fingerprint.
    source_url: internal
    local_path: 01_nutri-collective/evidence-platform/policy/standardization-taxonomy.curated.json
    format: JSON
    size: 15 KB
    update_frequency: manual
    projects_using_this: [evidence-platform]
    tags: [standardization, authored, policy]
    license: internal / unpublished
    verified: 2026-09-04
    notes: rescores live in a separate rescore_log, not in this file.

  - id: government_curated
    name: Government Sources (curated)
    category: in_house
    description: Curated register of government-sourced data feeding the evidence platform.
    source_url: internal
    local_path: 01_nutri-collective/evidence-platform/policy/government.curated.json
    format: JSON
    size: 38 KB
    update_frequency: manual
    projects_using_this: [evidence-platform]
    tags: [authored, policy, government]
    license: internal / unpublished
    verified: 2026-09-04

  - id: brands_register
    name: Brands Register
    category: in_house
    description: Per-brand processing-definition lanes.
    source_url: internal
    local_path: 01_nutri-collective/evidence-platform/site/brands.v1.json
    format: JSON
    size: 143 KB
    update_frequency: manual
    projects_using_this: [evidence-platform]
    tags: [brands, authored, published]
    license: see licence block in file
    verified: 2026-09-04
    notes: default_definition in processing_definitions_register is the only switch that changes scoring.

  - id: processing_definitions_register
    name: Processing Definitions Register
    category: in_house
    description: Instrument registry for food-processing definitions (e.g. NOVA variants).
    source_url: internal
    local_path: 01_nutri-collective/evidence-platform/site/processing-definitions.v1.json
    format: JSON
    size: 25 KB
    update_frequency: manual
    projects_using_this: [evidence-platform]
    tags: [processing, authored, published]
    license: see licence block in file
    verified: 2026-09-04

  - id: disease_causes_register
    name: Disease-Causes Register
    category: in_house
    description: Causal aliases (narrower/exact relations) linking disease topics; 17 topics linked.
    source_url: internal
    local_path: 01_nutri-collective/evidence-platform/site/disease-causes.v1.json
    format: JSON
    size: 103 KB
    update_frequency: manual
    projects_using_this: [evidence-platform]
    tags: [disease, causal, authored, published]
    license: see licence block in file
    verified: 2026-09-04
    notes: CVD/LDL-cholesterol misattribution was fixed in this register; scope is never "class".

  - id: cause_exposure_crosswalk
    name: Cause-Exposure Crosswalk
    category: in_house
    description: "Authored join register linking causes to exposures: 21 joined, 12 gaps, 24 out of scope."
    source_url: internal
    local_path: 01_nutri-collective/evidence-platform/site/cause-exposure-crosswalk.v1.json
    format: JSON
    size: 34 KB
    update_frequency: manual (authored, not computed)
    projects_using_this: [evidence-platform]
    tags: [crosswalk, authored, published]
    license: see licence block in file
    verified: 2026-09-04
    notes: '"OPEN" rows are missing rows, not empty scope.'

  - id: standardization_chart
    name: Standardization Chart (9-layer canonical scores)
    category: in_house
    description: The ONLY place the 9-layer standardization scores actually live and get plotted.
    source_url: internal
    local_path: 01_nutri-collective/evidence-platform/site/standardization-chart.v1.json
    format: JSON
    size: 36 KB
    update_frequency: manual
    projects_using_this: [evidence-platform]
    tags: [standardization, canonical, authored, published]
    license: see licence block in file
    verified: 2026-09-04
    notes: an older 55/60/33/30/12/35/60/8/8 audit set is superseded by this file — do not resurrect it.

  - id: standardization_sources_register
    name: Standardization Sources Register
    category: in_house
    description: 103 sources backing the standardization chart's underlying claims (never the scores themselves).
    source_url: internal
    local_path: 01_nutri-collective/evidence-platform/site/standardization-sources.v1.json
    format: JSON
    size: 90 KB, 103 sources
    update_frequency: manual
    projects_using_this: [evidence-platform]
    tags: [standardization, sources, authored, published]
    license: see licence block in file
    verified: 2026-09-04
    notes: '"crossref" status != verified; a bot wall on a source is not the same as unverifiable.'

  - id: nutrition_vocab
    name: Nutrition Controlled Vocabulary (SKOS)
    category: in_house
    description: SKOS concept layer mirroring lexicon.py's corpus-scoring vocabulary.
    source_url: internal
    local_path: 01_nutri-collective/evidence-platform/site/nutrition-vocab.v1.json
    format: JSON
    size: 40 KB
    update_frequency: manual, gated to reproduce lexicon.py exactly
    projects_using_this: [evidence-platform]
    tags: [vocabulary, SKOS, authored, published]
    license: see licence block in file
    verified: 2026-09-04
    notes: SEARCH_ONLY synonyms in build_search_index.py are NOT lexicon edits and don't move scores.

  - id: id_crosswalk
    name: ID Crosswalk Register
    category: in_house
    description: Cross-registry identifier crosswalk.
    source_url: internal
    local_path: 01_nutri-collective/evidence-platform/site/id-crosswalk.v1.json
    format: JSON
    size: 353 KB
    update_frequency: manual
    projects_using_this: [evidence-platform]
    tags: [crosswalk, authored, published, CC-BY-4.0]
    license: CC BY 4.0 (embedded CDNO portion is CC BY 3.0 — read dcterms:license per sub-source, don't assume one licence for the whole file)
    verified: 2026-09-04

  - id: lane_disagreement_register
    name: Lane Disagreement Register
    category: in_house
    description: Per-outcome disagreement flag register drawn on by the Health Outcomes tab.
    source_url: internal
    local_path: 01_nutri-collective/evidence-platform/site/lane-disagreement.v1.json
    format: JSON
    size: 67 KB
    update_frequency: generated via `make lane-disagreement`
    projects_using_this: [evidence-platform]
    tags: [generated, review-flag]
    license: internal / unpublished
    verified: 2026-09-04
    notes: a flag is a review item, never a grade; numbers are cap-8 until the corpus re-run.

  - id: food_claims_index
    name: Food Claims Index
    category: in_house
    description: "Direct vs via_component food-claim facts (>=5% of reference; energy/water excluded; omega-6 != omega-3)."
    source_url: internal
    local_path: 01_nutri-collective/evidence-platform/site/food-claims.v1.json
    format: JSON
    size: 380 KB
    update_frequency: manual
    projects_using_this: [evidence-platform]
    tags: [claims, food, authored, published]
    license: see licence block in file
    verified: 2026-09-04
    notes: backs check_article.py's IN LEDGER / VIA COMPONENT / NOT IN LEDGER verdicts (no LLM involved).

  - id: food_components_register
    name: Food Components Register
    category: in_house
    description: Food-to-component join data; the largest register file in evidence-platform.
    source_url: internal
    local_path: 01_nutri-collective/evidence-platform/site/food-components.v1.json
    format: JSON
    size: 1.88 MB
    update_frequency: manual/generated (verify against generated_by field before editing)
    projects_using_this: [evidence-platform]
    tags: [food, components, largest-register]
    license: see licence block in file
    verified: 2026-09-04

  - id: upf_markers_register
    name: UPF Markers Register
    category: in_house
    description: >
      Ingredient-level ultra-processed-food marker substances — not a NOVA score,
      the actual substances, each row quoted from one Monteiro 2019 sentence.
    source_url: internal
    local_path: 01_nutri-collective/evidence-platform/site/upf-markers.v1.json
    format: JSON
    size: 16 KB
    update_frequency: manual
    projects_using_this: [evidence-platform]
    tags: [UPF, ingredients, authored, published]
    license: see licence block in file
    verified: 2026-09-04
    notes: 8 of 17 markers are restricted by anyone (regulatory status).

  - id: upf_tracker_register
    name: UPF Definition Tracker
    category: in_house
    description: Adoption tracker for competing UPF/processing definitions.
    source_url: internal
    local_path: 01_nutri-collective/evidence-platform/site/upf-tracker.v1.json
    format: JSON
    size: 53 KB
    update_frequency: manual
    projects_using_this: [evidence-platform]
    tags: [UPF, tracker, authored, published]
    license: see licence block in file
    verified: 2026-09-04

  - id: corpus_history_register
    name: Corpus History Series
    category: in_house
    description: Generated trend series describing corpus growth over time.
    source_url: internal
    local_path: 01_nutri-collective/evidence-platform/site/corpus-history.v1.json
    format: JSON
    size: 8.5 KB
    update_frequency: generated
    projects_using_this: [evidence-platform]
    tags: [generated, corpus, trend]
    license: internal / unpublished
    verified: 2026-09-04

  - id: decision_ledger
    name: Decision Ledger (MCP + assay)
    category: in_house
    description: One knob per gauntlet/scoring decision; rubrics pre-registered before results are known.
    source_url: internal
    local_path: 01_nutri-collective/decisions/decision-ledger.v1.json
    format: JSON
    size: 519 lines
    update_frequency: manual, checked by check_decisions.py in `make test`
    projects_using_this: [nutri-collective, assay]
    tags: [decisions, ledger, authored]
    license: internal / unpublished
    verified: 2026-09-04
    notes: one deployed row per lane; v12 prediction is currently a draft, not yet decided.

  - id: funder_gate_ledger
    name: Funder-Gate / COI Assessability Ledger
    category: in_house
    description: >
      Conflict-of-interest assessability cut for funding questions, 2017+ only,
      PMC lane. Largest ledger by line count.
    source_url: internal
    local_path: 01_nutri-collective/study-records/funder-gate-ledger.v1.json
    format: JSON
    size: 12,103 lines
    update_frequency: manual; verified 2026-08-27
    projects_using_this: [nutri-collective, paper-2]
    tags: [COI, funding, ledger, authored, paper-2]
    license: internal / unpublished, pending Zenodo deposit
    verified: 2026-09-04
    notes: >
      This is the L8 adoption instrument for paper 2 (funder gate, not a statute) —
      untracked by design, corrected 2026-08-31. Human sign-off + deposit remain open.

  - id: food_sponsorship_cut_deposit
    name: Food Sponsorship Cut (Zenodo deposit package)
    category: in_house
    description: Deposit-ready package (food-sponsors.v1.json, coi-cut.v1.json) backing the Big Food paper.
    source_url: internal, awaiting Zenodo upload (see zenodo_deposits_registry)
    local_path: 01_nutri-collective/deposits/food-sponsorship-cut-v1/
    format: JSON
    size: 2 files
    update_frequency: manual
    projects_using_this: [big-food-paper]
    tags: [deposit, COI, authored, pending-upload]
    license: intended CC BY 4.0 on deposit (see zenodo_deposits_registry pattern)
    verified: 2026-09-04
    notes: sign-off and web upload are the user's remaining manual steps, not automatable.

  - id: zenodo_deposits_registry
    name: Zenodo Deposits Registry
    category: in_house
    description: Master index of every Zenodo deposit the user has published (DOIs, versions, licences).
    source_url: generated from the Zenodo REST API (refresh-zenodo-registry.py)
    local_path: "01_nutri-collective/tools pubmed/zenodo-deposits.v1.json"
    format: JSON
    size: 11 works, 17 version records
    update_frequency: refresh script; live API is authoritative, this is a cached index
    projects_using_this: [nutri-collective]
    tags: [zenodo, deposits, generated, meta-registry]
    license: n/a (index of licences, not itself licensed content)
    verified: 2026-09-04
    notes: >
      Concept DOI for the flagship deposit "biology-as-code" is 10.5281/zenodo.21536448.
      Bare `/versions` endpoint has a known gotcha — see refresh script comments.

  - id: claims_ledger_backend
    name: Claims Ledger (backend)
    category: in_house
    description: Hand-written health-claim rows with grades and review metadata.
    source_url: internal
    local_path: 01_nutri-collective/backend/claims.json
    format: JSON
    size: 126 claims, 5 grades, last reviewed 2026-07-12
    update_frequency: manual, via backend/draft_claim.py
    projects_using_this: [backend, mcp]
    tags: [claims, ledger, authored, curated]
    license: internal / unpublished
    verified: 2026-09-04
    notes: >
      110 of the 125-126 rows arrived in one undocumented commit with no recorded
      inclusion criterion — treat provenance of those rows as unverified even
      though the grades themselves are curated. draft_claim.py --check re-verifies
      pillars on NCBI but does not check citation formatting (verify_citations.py
      is separate and does not run on this file automatically).

  - id: assay_claim_corpus
    name: Assay Claim Corpus (gold/silver/bronze/x-intake)
    category: in_house
    description: >
      Graded and intake nutrition-claims corpus. The largest body of graded
      nutrition claims on this machine.
    source_url: internal
    local_path: 01_nutri-collective/services/assay/.assay-data/
    format: JSON files, layered gold/silver/bronze/x-intake
    size: "73 MB on disk; ~6,208 files / ~11,229 records per docs/LOCAL-DATA.md (978 silver + 3,689 quarantine files directly enumerated; remaining gold/bronze/x-intake not separately re-counted this pass)"
    update_frequency: manual + pipeline-graded
    projects_using_this: [assay, mcp]
    tags: [claims, corpus, gitignored, largest-corpus]
    license: internal / unpublished, gitignored
    verified: 2026-09-04
    notes: >
      Of the "gold" 2,009-record run, only 13 are actually grounded with a PMID —
      the real graded set is the 158-claim "grounded" tag (36/52/70 split, 132
      with PMIDs); stance labels are pending human review. Not derived data —
      losing this directory loses irreplaceable grading work; see local_data_backup_surface.

  - id: assay_vocab_registers
    name: Assay Vocab + ID Registers
    category: in_house
    description: Curated vocab plus generated ID registers for the assay/MCP standards layer.
    source_url: internal
    local_path: 01_nutri-collective/services/assay/data/
    format: JSON (subject/outcome/food-vocab.curated.json + matching -ids.v1.json)
    size: small (<1 MB combined)
    update_frequency: curated files manual, -ids files generated
    projects_using_this: [assay, mcp]
    tags: [vocab, authored, generated]
    license: internal / unpublished
    verified: 2026-09-04
    notes: no aggregate adoption score exists by design; don't compute one.

  - id: assay_fixtures
    name: Assay Benchmark/Parity Fixtures
    category: in_house
    description: Benchmark and scorer-parity gold fixtures (e.g. nutrimedia-30).
    source_url: internal
    local_path: 01_nutri-collective/services/assay/fixtures/
    format: JSON
    size: small
    update_frequency: manual
    projects_using_this: [assay]
    tags: [fixtures, benchmark, authored]
    license: internal / unpublished
    verified: 2026-09-04

  - id: evidence_value_gold_corpus
    name: Evidence-Value Gold Corpus
    category: in_house
    description: Gold-graded evidence corpus used by builders that resolve _EV references.
    source_url: internal
    local_path: 01_nutri-collective/evidence-value/
    format: JSONL + JSON, 18 files
    size: 94 MB total; premium_gold_all.jsonl = 2,735 records
    update_frequency: manual
    projects_using_this: [evidence-platform, mcp]
    tags: [gold-corpus, evidence, authored, tracked-in-git]
    license: internal / unpublished
    verified: 2026-09-04
    notes: workbench.html counts registers only; the hub Roadmap card is separate and forward-looking, don't conflate the two.

  - id: sweeteners_register
    name: Sweeteners Register
    category: in_house
    description: Nutrient-node register specific to sweeteners.
    source_url: internal
    local_path: 01_nutri-collective/nutrient-nodes/sweeteners/sweeteners.v1.json
    format: JSON
    size: 442 KB
    update_frequency: manual
    projects_using_this: [nutri-collective]
    tags: [sweeteners, authored]
    license: internal / unpublished
    verified: 2026-09-04

  - id: prospective_laws_register
    name: Prospective Laws Register
    category: in_house
    description: Tracks candidate/killed/errata food-law items with an evidence-level and ref-level rubric.
    source_url: internal
    local_path: 01_nutri-collective/prospective-laws/
    format: JSON (candidates, errata, killed, sources, identifiers, laws.graph, plus foodome/ subregisters)
    size: 8.4 MB total
    update_frequency: manual
    projects_using_this: [nutri-collective]
    tags: [law, tracker, authored]
    license: internal / unpublished
    verified: 2026-09-04
    notes: has its own REF-LEVELS.md / EVIDENCE-LEVELS.md rubric docs alongside it.

  - id: mechanism_ontology_db
    name: Mechanism Ontology (SQLite)
    category: in_house
    description: Mechanism/BFO-stack term ontology.
    source_url: internal
    local_path: 01_nutri-collective/backend/mechanism_ontology.db
    format: SQLite
    size: "68 KB; mechanism_term 109 rows, principle_mechanism 94 rows, bfo_stack_term 51 rows"
    update_frequency: manual
    projects_using_this: [backend]
    tags: [ontology, sqlite, authored]
    license: internal / unpublished
    verified: 2026-09-04
    notes: documented in docs/MECHANISM_ONTOLOGY.md.

  - id: funding_db
    name: Funding DB (NIH RePORTER cut)
    category: in_house
    description: NIH RePORTER-derived funding data (project, pub_project, pub_seen tables).
    source_url: derived from NIH RePORTER API (third-party source, in-house cut)
    local_path: 01_nutri-collective/evidence-platform/build/funding.db
    format: SQLite
    size: 272 KB
    update_frequency: rebuildable but slow
    projects_using_this: [evidence-platform]
    tags: [funding, derived, sqlite]
    license: derived from public NIH data
    verified: 2026-09-04
    notes: derived, but a slow rebuild — treat as worth keeping despite being reproducible in principle.

  - id: master_crosswalk
    name: MASTER_CROSSWALK (VMH/HMDB/KEGG/ChEBI/PubChem)
    category: in_house
    description: >
      2,797-row curated crosswalk joining VMH/HMDB/KEGG/ChEBI/PubChem identifiers.
      The curatorial join is in-house work; the underlying identifiers/data are
      third-party (VMH/Recon3D-sourced) — dual-listed, see licence note.
    source_url: internal join over vmh.life source data
    local_path: biology_as_code_PUBLIC/MASTER_CROSSWALK.tsv (mirrored at 01_nutri-collective/working_map_nutrition/MASTER_CROSSWALK.tsv)
    format: TSV
    size: 2,798 lines (2,797 rows)
    update_frequency: manual
    projects_using_this: [biology_as_code]
    tags: [crosswalk, authored, third-party-derived, licence-flagged]
    license: >
      OPEN but narrow — academic/research use + citation required; commercial use
      blocked. See THIRD-PARTY-DATA.json for the full analysis. Read dcterms:license
      off the source OWL files directly; CDNO and FOBI licences are recorded
      incorrectly (in opposite directions) in their own registries and repos.
    verified: 2026-09-04

  - id: pubmed_corpus_postgres
    name: PubMed Nutrition-Slice Corpus (Postgres)
    category: in_house
    description: The canonical PubMed nutrition-slice corpus, 1900-2027, with iCite and MeSH-topic joins.
    source_url: harvested from NCBI E-utilities / iCite (third-party source, in-house ingest)
    local_path: "Docker Postgres, 127.0.0.1:5433, db nutricollective"
    format: PostgreSQL tables (articles, icite, mesh_topics, ingest_windows)
    size: "articles 2,022,437 rows (4.2 GB); icite 2,022,437; mesh_topics 27,005; ingest_windows 444"
    update_frequency: incremental ingest, watermark-gated (see `pubmed status` / `pubmed repair`)
    projects_using_this: [nutri-collective, assay, evidence-platform]
    tags: [pubmed, corpus, irreplaceable, postgres]
    license: derived from public NCBI data
    verified: 2026-09-04
    notes: >
      THE canonical corpus — "the only truly irreplaceable store... assembled
      across July-August 2026 and repaired once" per docs/LOCAL-DATA.md. The
      ingester silently skipped ~82k records historically; `pubmed status` /
      `pubmed repair` exist for this reason. Do not confuse with
      pubmed_slice_db_superseded below. Requires benchmark/.venv (the only
      interpreter with psycopg) for the preflight gate — any other interpreter
      silently degrades the corpus check.

  - id: pubmed_slice_db_superseded
    name: pubmed_slice.db (SUPERSEDED)
    category: in_house
    description: Early SQLite predecessor of the Postgres PubMed corpus. Do not use.
    source_url: n/a — superseded
    local_path: 01_nutri-collective/backend/pubmed_slice.db
    format: SQLite
    size: 44 MB, articles table 28,554 rows
    update_frequency: frozen / dead
    projects_using_this: []
    tags: [superseded, decoy, do-not-use]
    license: n/a
    verified: 2026-09-04
    notes: >
      Explicitly labeled "superseded... not the corpus" in docs/LOCAL-DATA.md.
      The LAN-IP port-collision workaround some old scripts reference is dead;
      the real corpus is on 127.0.0.1:5433 (pubmed_corpus_postgres).

  - id: kibo_db_postgres
    name: kibo_db (Postgres, port 5432)
    category: in_house
    description: >
      A second, easy-to-forget local Postgres server (native homebrew install,
      not the docker one) holding an Open Food Facts import plus legacy "kibo"
      food data.
    source_url: Open Food Facts import (third-party source) + legacy in-house food rows
    local_path: "postgresql://127.0.0.1:5432, db kibo_db (homebrew postgresql@14)"
    format: PostgreSQL tables (open_food_facts, nutrients, foods)
    size: "open_food_facts 3,609,037 rows; nutrients 14,111; foods 316"
    update_frequency: static import, not actively refreshed
    projects_using_this: [legacy]
    tags: [postgres, off-import, legacy, easy-to-forget]
    license: Open Food Facts data is ODbL
    verified: 2026-09-04
    notes: "this server is the reason the real corpus was moved to port 5433 — see pubmed_corpus_postgres."

  - id: vmh_gene_harvest
    name: VMH SPARQL Gene Harvest
    category: in_house
    description: In-house SPARQL harvest of the gene list from the VMH endpoint (source data is third-party).
    source_url: harvested from vmh.life SPARQL endpoint
    local_path: 01_nutri-collective/working_map_nutrition/vmh_snapshots/sparql/gene.json
    format: JSON
    size: "count/n_results/results all report 30,000"
    update_frequency: manual re-harvest
    projects_using_this: [biology_as_code]
    tags: [vmh, gene, harvest, truncated, KNOWN-ISSUE]
    license: VMH terms (see THIRD-PARTY-DATA.json)
    verified: 2026-09-04
    notes: >
      CONFIRMED truncated, resolving an earlier apparent contradiction: the
      file's own count/n_results/results metadata fields all claim 30,000, but
      the actual data (149,854 lines) ends alphabetically at gene symbol
      "SIX6" (last "symbol" entry in the file) with zero SLC25* entries
      anywhere in it. The 30,000 count field is not a reliable indicator of
      completeness — it likely reflects a requested page size or upstream
      cursor limit, not actual unique results returned. Every SLC25* gene
      (and everything alphabetically after SIX6) is still missing. Use NCBI
      Entrez to resolve Entrez ID -> gene symbol for anything past this point
      rather than trusting this file.

  # ============================================================
  # THIRD-PARTY / FOUND — downloaded, re-downloadable from source
  # ============================================================

  - id: harvard_dataverse_periodic_food
    name: Harvard Dataverse — Periodic Table of Food (+ threatened-species foods)
    category: third_party
    description: >
      Food-product list, food categories, and an IUCN CR/EN/VU threatened-species
      foods table, with codebooks and references.
    source_url: "Harvard Dataverse (exact dataset page not recorded locally — search by codebook/file names, e.g. 'Periodic Table of Food')"
    local_path: _downloads_NOTinGIT/dataverse_file_harvard_periodicTfood/
    format: .xls / .tab (6 files)
    size: 692 KB
    update_frequency: static download (2026-08-29)
    projects_using_this: [biology_as_code]
    tags: [harvard, dataverse, food, biodiversity, third-party]
    license: check Dataverse dataset page terms before redistribution
    verified: 2026-09-04

  - id: fdc_food_data_central
    name: USDA FoodData Central
    category: third_party
    description: USDA nutrition database — Foundation, Branded, and FNDDS food data, multiple vintage snapshots.
    source_url: "https://fdc.nal.usda.gov/download-datasets.html"
    local_path: "LIVE/BACK_END_RN_APP/data-source-files/FoodDataCentral /  (note: trailing space in directory name is real, not a typo)"
    format: JSON/CSV, multiple vintage folders (2024_FDC_ARCHIVE, 2025_FDC, 2026_FDC_APRIL, oct25-2025-4mainfilesjson)
    size: 14 GB (foundationDownload.json alone is 6.3 MB)
    update_frequency: USDA releases periodic vintages; re-download from source
    projects_using_this: [nutri-collective, evidence-platform]
    tags: [usda, food, nutrition, public-domain, third-party]
    license: US public domain
    verified: 2026-09-04
    notes: path confirmed live via root .env `path_to_data_files`; matches prior notes exactly.

  - id: foodb
    name: FooDB
    category: third_party
    description: Curated food-composition database (compounds, concentrations).
    source_url: "https://foodb.ca/downloads"
    local_path: data-source-files/FoodB-ca/
    format: CSV / JSON / MySQL dump variants
    size: 2.1 GB
    update_frequency: static download
    projects_using_this: [biology_as_code]
    tags: [food, composition, third-party]
    license: check foodb.ca terms of use before redistribution
    verified: 2026-09-04
    notes: kept under build/ in biology_as_code specifically because of licence constraints — never ship it in the public repo.

  - id: open_food_facts
    name: Open Food Facts
    category: third_party
    description: Crowdsourced global food-product database.
    source_url: "https://world.openfoodfacts.org/data"
    local_path: data-source-files/OpenFoodFacts/
    format: parquet / jsonl.gz / csv.gz
    size: "15 GB total (food.parquet 5.7 GB, products.jsonl.gz 9.0 GB, products.csv.gz 1.06 GB)"
    update_frequency: OFF publishes rolling dumps; re-download from source
    projects_using_this: [biology_as_code, kibo_db_postgres]
    tags: [food, crowdsourced, ODbL, third-party]
    license: ODbL 1.0 (share-alike; attribution required — was missing from NOTICE until 2026-08-29, now fixed)
    verified: 2026-09-04
    notes: also imported wholesale into kibo_db_postgres (3.6M rows).

  - id: grocerydb_truefood
    name: GroceryDB (truefood.tech)
    category: third_party
    description: Grocery product / degree-of-processing dataset.
    source_url: "truefood.tech"
    local_path: "data-source-files/grocerydb(truefood.tech)/"
    format: CSV (+ source zip)
    size: ~46 MB
    update_frequency: static download
    projects_using_this: [nutri-collective]
    tags: [grocery, processing, third-party]
    license: check truefood.tech terms before redistribution
    verified: 2026-09-04

  - id: misc_kibo_food_data
    name: Misc third-party nutrient databases (AI4Food-NutritionDB, USDA Flavonoid/Isoflavone DBs, ComprehensiveFoodDatabase)
    category: third_party
    description: Grab-bag of smaller third-party nutrient/flavonoid databases collected during early kibo work.
    source_url: "AI4Food-NutritionDB via GitHub (owner/org not recorded locally); USDA Flavonoid/Isoflavone Access databases via USDA ARS"
    local_path: data-source-files/misc_kibo_food_data/
    format: CSV / Access (.accdb) / PDF documentation
    size: ~40 MB combined
    update_frequency: static download
    projects_using_this: [legacy]
    tags: [misc, flavonoids, third-party, low-priority]
    license: mixed — USDA ARS data is public domain; AI4Food-NutritionDB licence not recorded locally, check its GitHub repo
    verified: 2026-09-04

  - id: wholefoods_colab_dump
    name: Wholefoods Retailer Data (Colab-sourced)
    category: third_party
    description: Retailer ingredient rows originally filtered via a Colab notebook (cosmetics/body-care filter).
    source_url: "unknown original retailer source — filtered through a Colab notebook kept in-repo"
    local_path: "data-source-files/data-copy-wholefoods-*"
    format: CSV/JSON (unspecified in this pass)
    size: small
    update_frequency: static
    projects_using_this: [evidence-platform]
    tags: [retailer, ingredients, third-party, colab]
    license: unclear — origin of underlying retailer data not documented locally
    verified: 2026-09-04
    notes: "224 of 229 retailer rows trace to this Colab dump; the FDA lane (20/20) is separately clean and public-domain. Keep the notebook, it's the only record of the filtering logic."

  - id: vmh_agora_recon_downloads
    name: "VMH / AGORA / Recon3D / ReconMap downloads (bundle)"
    category: third_party
    description: >
      Genome-scale metabolic models and reconstructions: AGORA gut-microbe models
      (base + mucins + genomes), Recon3D human model, ReconMap 2.01 (+ SBML3
      layout), plus ancillary exports (SEED2VMH crosswalk, recon-store TSVs,
      flux exports, atom-mapped reactions, metabolite .mol files) and a MATLAB
      Recon3D patch script.
    source_url: "vmh.life (VMH downloads page)"
    local_path: _downloads_NOTinGIT/vhm_related/
    format: "mixed — .zip (models), .tsv/.csv (tables), .pdf (docs), .m/.txt (script)"
    size: 1.5 GB
    update_frequency: static download (re-downloadable from vmh.life)
    projects_using_this: [biology_as_code]
    tags: [vmh, agora, recon3d, gem, third-party, re-downloadable]
    license: check vmh.life terms before redistribution
    verified: 2026-09-04
    notes: "flux exports (fluxes.tsv + fluxes (1..9).tsv) are browser re-download duplicates — likely only the newest matters."

  - id: pyadm1
    name: PyADM1
    category: third_party
    description: Anaerobic Digestion Model No. 1 implemented in Python — gut-fermentation modeling reference.
    source_url: "https://github.com/CaptainFerMag/PyADM1"
    local_path: "_downloads_NOTinGIT/PyADM1-master.zip"
    format: zip (Python source)
    size: 18 MB
    update_frequency: static download
    projects_using_this: [biology_as_code]
    tags: [modeling, fermentation, github, third-party]
    license: check repo licence file
    verified: 2026-09-04

  - id: pycomo
    name: PyCoMo
    category: third_party
    description: Community metabolic modeling package.
    source_url: "https://github.com/univieCUBE/PyCoMo"
    local_path: "_downloads_NOTinGIT/PyCoMo-main.zip"
    format: zip (Python source)
    size: 7 MB
    update_frequency: static download
    projects_using_this: [biology_as_code]
    tags: [modeling, github, third-party]
    license: check repo licence file
    verified: 2026-09-04

  - id: fda_ingredient_inventories
    name: FDA Ingredient / GRAS / Food-Contact Inventories
    category: third_party
    description: US public-domain FDA ingredient safety and food-contact inventories.
    source_url: "FDA public data (specific pages not recorded locally)"
    local_path: biology_as_code_PUBLIC/FDA_ingredients/
    format: unspecified in this pass
    size: not sized this pass
    update_frequency: static download
    projects_using_this: [biology_as_code]
    tags: [fda, public-domain, third-party]
    license: "US public domain (17 U.S.C. 105)"
    verified: 2026-09-04

  - id: off_fixtures_biology_as_code
    name: Open Food Facts Fixtures (biology_as_code macro fallbacks)
    category: third_party
    description: Small fixture files sourced from Open Food Facts used as macro-nutrient fallback data.
    source_url: "https://world.openfoodfacts.org/data (subset)"
    local_path: biology_as_code_PUBLIC/src/biology_as_code/data/fixtures/
    format: JSON, 67 fixture files
    size: small
    update_frequency: static
    projects_using_this: [biology_as_code]
    tags: [off, fixtures, third-party, ODbL]
    license: "ODbL 1.0, share-alike; attribution was missing from NOTICE until 2026-08-29, now corrected"
    verified: 2026-09-04
