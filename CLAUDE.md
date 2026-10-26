# Pathology Ontology Refinement Project

## Mission
Iteratively improve the existing merged pathology ontology. Do not rebuild it from scratch.

Before editing ontology files, read `ontology_master_instructions_v2.txt` in full. It is the detailed governing specification. Also read the relevant organ-system/site review file if one exists.

## Project layout
- `MergeFinalDraft2/`: default starting draft unless the user or `WORK_STATE.json` names a newer approved draft.
- `Yaml Files/`: original WHO and Pathology Outlines hierarchy references.
- organ-system JSON folders: source diagnoses, names, paths, URLs, IDs, and source metadata.
- `Metadata/`: structured terminology resources such as ICD-9, ICD-10, ICD-11, ICD-O3.2, ICD-O4, NCIt, and future resources.
- `Results/`: all new drafts, logs, summaries, and review outputs.

Never overwrite source files, `MergeFinalDraft2`, or the immediately previous working version.

## Scope
Only process the organ system explicitly requested by the user. Within it, identify the individual organs/sites, process one site at a time, finish the iterative loop for that site, then move to the next. After all requested sites are complete, perform an organ-system consistency pass.

Do not continue into another organ system unless asked.

### Current phase boundary
Deduplicate and merge synonyms only **within the same individual organ/site**.

Do not merge diagnoses across different organs, create cross-organ canonical entities, deduplicate identical names across sites, or force one site to match another structurally. Cross-organ harmonization is a later phase.

## Core reasoning rule
Treat every hierarchy edge as the claim:

> The child is a clinically and semantically valid member/type of the parent, and the parent adds useful classification information.

Verify that claim. Do not blindly preserve the current merged hierarchy, WHO organization, Pathology Outlines organization, or any source-derived category. Sources are evidence, not unquestionable templates.

## Manual review requirement
This is not an automatic string-matching cleanup. For every diagnosis or assignable concept, inspect its complete parent chain and determine whether it belongs to the site; whether it is a diagnosis, category, synonym, or source artifact; whether its neoplastic/non-neoplastic/tumor-like/precursor, behavior, and lineage classifications are accurate; and whether the current parent is both true and useful.

Use normalized strings, scripts, keywords, or fuzzy matching only to find candidates. Final identity and hierarchy decisions require semantic review. When uncertain, flag rather than force.

## Reconciling classification schemes
Merged branches may combine overlapping axes such as histogenesis (`epithelial`, `neuroendocrine`, `mesenchymal`, etc.), behavior (`benign`, `borderline`, `malignant`, etc.), and conceptual status (`neoplastic`, `non-neoplastic`, `tumor-like`, `precursor`).

Do not mechanically preserve parallel schemes when they create overlap, duplication, or contradictory placement. For each local branch, decide which organization is most informative. Check whether benign/malignant and histologic-family branches are splitting the same conceptual space, whether one axis belongs below another or in metadata, and whether every category label is actually true for every child.

There is no universal rule that histogenesis or behavior must always come first. Choose the most clinically coherent structure for the site.

## Source-artifact and name cleanup
Remove or flatten non-diagnostic source/navigation nodes such as `Introduction`, `Overview`, `General`, `General information`, and equivalent textbook/webpage headings.

If such wording is appended to a real diagnosis title, preserve the diagnosis and clean the preferred name. Example: `X tumor - general` may become `X tumor` if that page represents the diagnosis. Preserve the original source title as provenance when useful.

If normalization reveals a same-site diagnosis already present, investigate it as a duplicate rather than creating another leaf.

Normalize obvious punctuation, capitalization, spelling/OCR errors, redundant site wording, and tumor/tumour formatting without destroying meaningful terminology.

## Same-site synonym and duplicate review
Actively inspect within the current site for spelling variants, punctuation/capitalization/hyphenation variants, abbreviation vs full name, explicit aliases, historical vs current terminology, WHO vs Pathology Outlines alternate names, duplicate source-derived leaves, and `general`/`overview` titles that resolve to an existing diagnosis.

Confirm semantic identity before merging. Never merge merely because terms share words such as `carcinoma`, `serous`, `stromal`, `clear cell`, `squamous`, `sarcoma`, or `neuroendocrine`.

Do not merge family with subtype, parent with child, precursor with invasive disease, benign/borderline/malignant forms, primary with metastatic disease, or molecularly/behaviorally distinct diagnoses.

For a confirmed duplicate: choose one preferred name, preserve alternate names as aliases, combine non-conflicting metadata, preserve WHO/Pathology Outlines provenance and source paths/URLs/IDs, record conflicts, and remove only the redundant independent leaf.

## Redundant hierarchy
Inspect empty categories, repeated parent/child names, meaningless one-child chains, repeated site wording already implied by the root, and categories inherited only from source formatting.

A one-child category is not automatically wrong. Keep it if it adds real classification meaning; flatten it if it does not.

## Metadata enrichment
Only attach metadata after concept identity is understood. Use structured local files in `Metadata/` first.

Before using an unfamiliar metadata file, read its README/documentation, inspect workbook sheets or text structure, identify code/label/version fields, and determine whether it contains diagnoses, categories, morphology, topography, behavior, indexes, or annexes.

Expected resources may include ICD-9, ICD-10, ICD-11, ICD-O3.2, ICD-O4, and NCIt. Do not populate SNOMED unless authorized structured SNOMED data is later added.

For each resolved diagnosis: generate candidate mappings, manually verify semantic fit, attach only defensible mappings, record terminology system/version and source file/table, and classify the relationship when useful as `EXACT`, `SYNONYM`, `BROADER`, `NARROWER`, `APPROXIMATE`, `AMBIGUOUS`, or `NO_MATCH`.

Do not mark a broad ICD category `EXACT` merely because it contains the diagnosis. Multiple ontology diagnoses may legitimately share one ICD/ICD-O/NCIt code; never merge diagnoses because they share an external code. One diagnosis may also have multiple valid external mappings.

For ICD-O, preserve morphology, behavior, and topography separately when available. Missing metadata is preferable to a false mapping.

## New WHO material
Use newer WHO material when it clarifies existing terminology or classification, but do not perform wholesale new-WHO expansion during the first cleanup pass.

If a genuinely new WHO diagnosis appears absent, record it in `NEW WHO CANDIDATES` with its WHO name, source path, and useful metadata. Defer insertion until a dedicated later WHO-incorporation pass unless the user explicitly asks otherwise.

## Iterative site loop
For each site:

1. **Orient:** load the newest draft; read relevant WHO YAML, Pathology Outlines YAML, site JSON files, reviewer notes, and relevant metadata tables.
2. **Hierarchy pass:** manually inspect every diagnosis and parent chain; fix high-confidence semantic/classification errors; clean source artifacts; reconcile overlapping classification axes; remove/flatten redundant hierarchy only when justified.
3. **Identity pass:** find same-site duplicate/synonym candidates; verify identity manually; merge only confirmed same concepts; preserve aliases and provenance.
4. **Metadata pass:** map resolved concepts to structured terminology data; verify mappings semantically; preserve mapping relation, system/version, and source.
5. **Validation pass:** check duplicate IDs, same-site duplicates, broken/circular parents, false parent labels, empty/misleading categories, redundant chains, source artifacts, lost provenance, incorrect metadata, broad mappings marked exact, and unexplained count changes.
6. **Critical re-review:** re-read the modified site from the top. Reinspect every moved, merged, renamed, or flattened concept; every changed classification branch; every `EXACT` mapping; and every branch named in expert feedback.

If a complete re-review finds another substantive **high-confidence** correction, apply it, save a new version, and validate again. Stop iterating when a complete pass finds no substantial high-confidence correction remaining. Do not create cosmetic churn merely to produce another version.

## Versioning and state
Save every meaningful iteration as a new version in `Results/`, for example `FemaleGenital_uterine_corpus_v01`, `FemaleGenital_uterine_corpus_v02`, and `FemaleGenital_cervix_v01`.

If `WORK_STATE.json` exists, update it after every completed site/pass. If it does not exist and the task is long-running, create it with the requested organ system, current site, latest version, completed sites, passes completed, unresolved items, and next action.

Persist state before context compaction or ending a long session so work can resume from the correct draft and next action.

## Change log
Maintain an auditable log for meaningful changes. Record organ system/site, original concept/path, new concept/path, action (`KEEP`, `MOVE`, `RENAME`, `MERGE`, `FLATTEN`, `REMOVE`, `ADD`, or `FLAG FOR REVIEW`), reason/source evidence, aliases merged, provenance retained, external mappings changed, confidence, and unresolved questions.

Low-confidence destructive changes should normally be flagged rather than applied.

## Subagents
Subagents may gather independent evidence by inspecting WHO structure, Pathology Outlines structure, metadata tables, possible same-site duplicates, or mechanical integrity.

Do **not** let multiple subagents independently rewrite the same site. The main agent owns the current site and applies final hierarchy and identity decisions.

## Required output per site
Produce a versioned updated ontology, structured change log, unresolved review list, metadata mapping summary, `NEW WHO CANDIDATES` list if relevant, and a short summary of major structural/identity changes.

After all sites in the requested organ system are complete, perform a system-level consistency review **without cross-organ deduplication**.

## Final priority
Optimize in this order: diagnostic correctness; clinically meaningful hierarchy; correct same-site concept identity; preservation of source provenance; reliable metadata mappings; internal consistency; removal of redundant/source-artifact structure.

Do not optimize for the smallest tree, maximum symmetry, or maximum number of attached codes.
