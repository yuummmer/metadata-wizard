# Kata: Art Collections (TidyTuesday 2021-01-12)

## Goal
Practice a specimen-like rulepack with referential integrity + basic policy checks for a small museum-style dataset (artworks + artists).

## Files
- `fixtures/artworks.csv`
  Columns (subset): `id`, `artistId`, `title`, `acquisitionYear`, `url`, `medium`, …

- `fixtures/artists.csv`  (artistId, artist)
  Columns (subset): `id`, `name`, `gender`, `dates`, `yearOfBirth`, `yearOfDeath`, `placeOfBirth`, `placeOfDeath`, `url`
---

## Current implementation: ingest rulepack (multi-input)

The file:

- `rules/tate-art-collections-ingest@0.1.0.yaml`

defines a simple **ingestion rulepack** that runs against both `artworks.csv` and `artists.csv` in a single FAIRy run.

Implemented checks:

- **artworks-required-core** (`required`, fail)  
  - `artworks.csv` must have: `id`, `artistId`, `title`, `acquisitionYear`, `url`
- **artists-required-core** (`required`, fail)  
  - `artists.csv` must have: `id`, `name`
- **artworks-id-unique** (`unique`, fail)  
  - `artworks.id` has no duplicates
- **artists-id-unique** (`unique`, fail)  
  - `artists.id` has no duplicates

Run it with:

```bash
fairy validate \
  --rulepack katas/art-collections/rules/tate-art-collections-ingest@0.1.0.yaml \
  --inputs artworks=katas/art-collections/fixtures/artworks.csv \
  --inputs artists=katas/art-collections/fixtures/artists.csv \
  --report-md out/art_ingest_report.md

This kata is meant as a museum-shaped example for multi-table ingest checks that can be adapted by collection / museum data curators.

## Checks to implement (5–7)
- ✅ **required-columns (artworks)**: id, artistId, title, acquisitionYear, url
- ✅ **required-columns (artists)**: artistId, artist
- ✅ **id-unique (artworks)**: `id` has no duplicates
- ⏳ **artist-foreign-key**: every `artworks.artistId` exists in `artists.artistId`
- ⏳ **year-range**: `acquisitionYear` numeric + within [min_year, max_year]
- ⏳ **url-format**: `url` is valid http(s)
- ⏳ **title-trimmed** (optional warn): non-empty after trim
- ⏳ **medium-policy** (optional warn): flag if `medium` contains any tokens in `medium_policy`

(✅ = implemented in tate-art-collections-ingest@0.1.0.yaml, ⏳ = planned/next.)

## Parameters (tune during practice)
```yaml
min_year: 1600
max_year: 2025
medium_policy: ["unknown", "photograph"]  # example only; tweak
```
## Done when
- Rulepack runs locally and produces a report
- At least 1 structural, 1 semantic, 1 policy check implemented
- Golden report comparison passes (expected/report.json)