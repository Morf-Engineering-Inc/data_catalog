# Inventory — `_downloads_NOTinGIT/`

**What this folder is:** third-party downloads that feed the GEM/VMH modeling work
(Recon3D, AGORA, ReconMap) plus one Harvard Dataverse food dataset. Everything here
is **re-downloadable from public sources** — nothing is original work, which is why
it is not in git and not in the off-site backup (see `docs/LOCAL-DATA.md` in
nutri-collective for the backup surface; the vmh-sparql snapshots backed up there
are a different, separate artifact).

**Total: ~2.1 GB.** Inventoried 2026-09-01.

## Top level

| item | size | what it is | source / re-download |
|---|---|---|---|
| `vhm_related/` | 1.5 GB | VMH downloads (below) | vmh.life → Downloads |
| `extracted/` | 555 MB | unzipped copies of the archives (below) | re-inflatable from the zips |
| `PyADM1-master.zip` | 18 MB | PyADM1 — Anaerobic Digestion Model No. 1 in Python (gut-fermentation modeling reference), July 20 | github.com/CaptainFerMag/PyADM1 |
| `PyCoMo-main.zip` | 7 MB | PyCoMo — community metabolic modeling package, July 20 | github.com/univieCUBE/PyCoMo |
| `dataverse_file_harvard_periodicTfood/` | 692 KB | Harvard Dataverse dataset, downloaded Aug 29: food-product list + food categories + IUCN CR/EN/VU (threatened-species) foods + codebooks + references (`.xls`/`.tab`) | Harvard Dataverse (search the dataset by the codebook/file names) |

## `vhm_related/` (all from vmh.life unless noted)

| item | size | what it is |
|---|---|---|
| `AGORA-Genomes.zip` | 677 MB | genomes behind the AGORA gut-microbe reconstructions |
| `Atom-Mapping-RXN.zip` | 425 MB | atom-mapped reaction files |
| `AGORA-1.03-With-Mucins.zip` | 225 MB | AGORA 1.03 microbe models, mucin variant |
| `AGORA-1.03.zip` | 214 MB | AGORA 1.03 microbe models, base |
| `mol.zip` | 4.3 MB | metabolite structure files (.mol) |
| `ReconMap-2.01-SBML3-Layout-Render.zip` | 3.7 MB | ReconMap with SBML3 layout/render |
| `Recon3D_301.zip` | 3.1 MB | **Recon3D 3.01 — the human GEM the bac ontology cites** |
| `ReconMap-2.01.zip` | 1.7 MB | ReconMap 2.01 map |
| `Summary of update to AGORA reconstruction.pdf` | 956 KB | AGORA update notes |
| `Supplementary_table_1.xlsx` | 612 KB | paper supplement accompanying the above |
| `SEED2VMH_translation.csv` | 36 KB | ModelSEED→VMH identifier crosswalk |
| `recon-store-{metabolites,reactions,genes,diseases}-*.tsv` | ~80 KB | VMH recon-store table exports |
| `fluxes.tsv` + `fluxes (1..9).tsv` | ~40 KB | VMH flux exports — **browser re-download duplicates**, likely only the newest matters |
| `patchRecon3D_01.m.txt` | 8 KB | MATLAB patch script for Recon3D |

## `extracted/`

Unzipped working copies: `Recon3D` (3.4 MB), `ReconMap-2.01` (47 MB),
`ReconMap-2.01-SBML3-Layout-Render` (65 MB), `AGORA-base` (111 MB),
`AGORA-With-Mucins` (117 MB), `mol` (34 MB), `PyADM1-master` (43 MB),
`PyCoMo-main` (135 MB). Pure duplication of the archives above.

## If space is ever needed

Safe deletions in order of payoff, all recoverable: `extracted/` (555 MB — re-unzip),
`AGORA-Genomes.zip` (677 MB — only needed for genome-level work, re-downloadable),
`Atom-Mapping-RXN.zip` (425 MB — same), the eight `fluxes (n).tsv` duplicates.
Keep `Recon3D_301.zip`, the recon-store TSVs, and `SEED2VMH_translation.csv` — small,
and they are the files the crosswalk/ontology work actually touched.
