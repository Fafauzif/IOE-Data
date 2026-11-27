# ioe-port-raw-data

Source-native raw files and collection metadata for the **Indonesia Ocean Expo 2027 preliminary business
case** (client: Samudra Nusantara Container Terminal — a fictional Java container gateway; 2025 is the case
baseline). The real ports in this folder are comparators, not the client.

This folder holds **raw provider responses and the metadata needed to replay them. Nothing else.** No
cleaning, no joins, no field renaming, no deduplication of source records, no OCR, no transcription, no
type conversion, no calculations, no charts, no synthesis. Every file is the bytes the provider returned.

Collected 2026-11-27. 135 tracked files, 273.55 MB, plus 4 local-only files (1.59 MB) that are gitignored.

---

## Navigating the folder

```text
ioe-port-raw-data/
  README.md                  this file
  .gitignore                 excludes _local_only/, _scratch/ and transient files
  manifest/
    sources.csv              one row per source-register item: status, scope, rights link
    files.csv                one row per stored file: bytes, sha256, endpoint, replay steps
    run-log.md               limits applied, what completed, exact blockers
    deferred.md              what was not retrieved, in priority order, with the safe next step
    rights-review.md         the redistribution decision each source still needs
  data/raw/<source-slug>/<dataset-slug>/<year-or-edition>/<source-native-filename>
  documentation/<source-slug>/<public-metadata-or-licence-capture>
  _local_only/               gitignored; material that must not be published
  _scratch/                  gitignored; collection logs and the working helper scripts
```

Start with `manifest/sources.csv` for "what is here and how complete is it", then `manifest/files.csv` to
go from a row to a file on disk.

### Source slugs

| Slug | Source |
| --- | --- |
| `imf-portwatch` | IMF PortWatch daily port activity (ArcGIS) |
| `worldbank-cppi` | World Bank / S&P Container Port Performance Index |
| `bps-maritime` | BPS Statistik Transportasi Laut |
| `kemenhub-hubnet` | Kemenhub / Satu Data Indonesia container-port throughput |
| `kemendag` | Kemendag non-oil trade by port (documentation placement) |
| `jict` | Jakarta International Container Terminal |
| `pelindo`, `pelindo-tpk` | PT Pelabuhan Indonesia (Persero) and PT Pelindo Terminal Petikemas |
| `kai-petikemas` | KAI container and freight releases |
| `jica-patimban` | JICA Patimban material |
| `osm-geofabrik` | OpenStreetMap via Geofabrik |
| `nasa-power` | NASA POWER point meteorology |
| `ioc-sealevel` | IOC Kolinamil tide gauge |
| `bi-jisdor` | Bank Indonesia JISDOR reference rate |
| `esdm-epa` | ESDM grid factors and EPA shore-power method |

## What is in `data/raw/` and what it looks like

Every file is stored in the format the provider actually served. Nothing was transcoded.

| Format | Where | How to read it |
| --- | --- | --- |
| `.json` | IMF PortWatch pages, Kemenhub resource, NASA POWER, IOC observations, World Bank WDS index | Plain JSON. PortWatch pages are ArcGIS `FeatureCollection` objects: `features[].attributes` holds the record, `exceededTransferLimit` tells whether more pages existed. NASA POWER is a `properties.parameter` object of hourly values. |
| `.xlsx` | Kemenhub resource | Open in any spreadsheet tool; unmodified provider workbook. |
| `.pdf` | CPPI editions, BPS editions, Pelindo reports, ESDM factor table, EPA assessment | As published, with the publisher's own pagination. |
| `.html` | Kemendag tables, JICT, KAI, Pelindo index pages, NASA/GFW/UNCTAD documentation captures | The page as served. Where a publisher provides no file export, the published HTML table **is** the source-native form — the values sit in the `<table>` markup, not in a separate download. |
| `.xml` | Bank Indonesia JISDOR | Raw `DataSet` XML from the ASMX GET binding; provider element names and `+07:00` dates untouched. |
| `.md5` | Geofabrik | Native checksum of the nationwide extract that was **not** downloaded. |

## Reconstructing partitions

Three sources were retrieved in parts. Reassembly is documented here; **no part file was renamed, trimmed
or merged on disk.**

1. **IMF PortWatch — paged API results.** 46 page files across four partitions:
   `2025-port514-port1237-port1179/`, `2020-2024-port514-port1237-port1179/`, `2026ytd-…/`,
   `2025-all-indonesia-ports/`. Within a partition, files are ordered by page number and offset
   (`page_001_offset_0.json`, `page_002_offset_1000.json`, …), and the whole partition is the ordered
   concatenation of `features[]`. The query was stably ordered by `portid,date`, so the order is
   reproducible. Record counts per partition match the provider's own `returnCountOnly` count exactly
   (36,865 for the IDN 2025 partition, 7,548 for the three priority ports across 2020-2026), which is the
   completeness check — not an assumption. If you re-request a page, `files.csv` has the exact `sha256`
   to compare against.
2. **BPS publications.** One PDF per data year, each a complete published edition — no partitioning.
3. **IOC tide gauge.** Four non-contiguous 10-day windows in `water-level-prs/2025/`; the date range is in
   each filename. They are **not** a continuous year series — see coverage gaps.

## Metadata fields

`sources.csv` — `source_id, provider, dataset, landing_url, rights_url, access_status, requested_scope,
actual_scope, notes`. `access_status` is one of `downloaded`, `partial`, `metadata_only`, `blocked_access`,
`deferred_size`, `deferred_budget`, `not_supplied`.

`files.csv` — `source_id, relative_path, original_filename, public_endpoint,
replayable_query_or_export_steps, downloaded_utc, period_or_edition, geography, media_type, bytes, sha256,
page_or_byte_range, redistribution_status, status`.

- `relative_path` is from the folder root and is the authoritative pointer to the bytes.
- `original_filename` keeps the server's own name where it differed from the local name.
- `public_endpoint` is the stable public URL or query. One-time signed URLs are deliberately **not** recorded;
  where a token was used, the stable landing page is recorded and the token is described only as a mechanism.
- `sha256` was computed from the files on disk after the final write, so it describes the bytes that are
  actually here. Re-running a request and getting the same hash means nothing should be re-stored.
- `status` describes the retrieval of *that file*, not the source. A source can be `partial` while every
  individual file is `downloaded`.

### Naming

Local names are lowercase, ISO-dated, path-safe. Server filenames are kept when meaningful (for example the
Pelindo and BPS PDFs, which carry the publisher's own naming); generic provider filenames are replaced by
`<slug>_<period>` plus the real extension. An existing byte-distinct file is never overwritten: a repeated
identical request is logged as a skip instead.

## Public redistribution vs local-only

- **`data/raw/` and `documentation/` are the Git-ready area.** They are intended for a public repository,
  but *only* after the rights decisions in `manifest/rights-review.md` are settled. 128 of 135 files are
  flagged `unclear_review_required` — publicly downloadable is not the same as republishable. Read that
  file before pushing.
- **`_local_only/` is gitignored.** It holds bytes that are fine to keep locally but must not be published.
  It holds four files, each moved there unchanged (no bytes altered):
  the BPS 2024 publication landing page (embeds live one-time download tokens), the two Kemendag
  trade-by-port captures (their shared page chrome embeds JWT-shaped signed support-ticket links), and the
  World Bank CPPI document-detail page (embeds the publisher's client-side ApiKey). In all four cases the
  **data** carries no secret — it is the surrounding page machinery that does — so each can be returned to
  the tracked area once the owner decides, without re-downloading.
- **`_scratch/` is gitignored.** Collection logs, the byte-bounded fetch helper and the manifest generators.
  Useful for an audit; not part of the published dataset.
- No credentials, cookies, signed URLs, account identifiers or private paths are recorded anywhere in the
  tracked area. The case brief and any terminal-level operator records are local-only by default.

## Coverage: what this folder does **not** contain

Read this before treating 2025 as complete.

- **No terminal-level operational records.** Berth, pilot, tug, work order, yard, gate, meter, injury,
  contract and tariff records are `not_supplied` (`CASE18`). No public source can substitute for them, no
  operator was contacted, and no non-public endpoint was probed.
- **ERA5 (Copernicus) is absent** — `blocked_access`, it needs an authenticated CDS account and accepted
  licences. Only its landing page is stored.
- **UNCTAD Port Liner Shipping Connectivity Index is absent as data** — `metadata_only`; the data centre
  publishes no documented native export.
- **Global Fishing Watch is absent** — `blocked_access` (personal token) and its terms are noncommercial
  only, so it cannot enter a redistributable folder.
- **No OSM extract is stored** — every published Indonesia/Java extract is 704 MB-1.6 GB, over the per-file
  cap; only indexes, the native md5 and the ODbL text are here.
- **IMF PortWatch covers 2025 in full for all 101 IDN ports, but 2020-2024 and 2026 only for the three
  priority ports.** The depressed history for the other ports is `deferred_budget`.
- **BPS covers data years 2020-2024.** No 2025 edition was published at collection time.
- **The Kemendag port-level trade tables are stored but not tracked.** They are in `_local_only/kemendag/`
  pending a scan decision, so the Git-ready area currently has no `data/raw/kemendag/` directory.
- **Tide gauge coverage is four 10-day windows in 2025**, not a year series.
- **JICT publishes no throughput series and no annual report**; the publication page renders
  `Belum ada publikasi`.
- **Patimban (JICA) is narrative only** — no development document or operational statistics file was
  published on the linked source.
- Provider-side limits observed at collection time (recorded, never estimated): PortWatch serves 2026 data
  through 2026-11-20; the IDN port reference lists 101 ports including 15 offshore oil terminals (`fso*`).

## How to resume without overwriting

1. Read `manifest/deferred.md` — it is in priority order and states the safe retrieval for each item.
2. Before a request, check `files.csv` for the same `public_endpoint` and scope. If the response hash
   matches, log the skip and move on; do not store a second copy.
3. New files go to `data/raw/<source-slug>/<dataset-slug>/<year-or-edition>/` with a predictable name.
   Byte-distinct snapshots of the same scope get a date or request-id suffix; nothing existing is replaced.
4. Append a row to `manifest/files.csv` (bytes and `sha256` measured from the file you just wrote) and
   update `manifest/sources.csv` for the source's `actual_scope` and `access_status`.
5. Keep the per-run limits: < 100,000,000 bytes per file (90 MB target), one download at a time, ≥ 1 s
   between requests to the same service, and no parallel workers inside a single run.
6. Add the source's rights decision to `manifest/rights-review.md` before the file enters the tracked area.

## Reproducing a file

`files.csv` gives the endpoint and the replay steps for every file, for example:

```text
source_id            PW1
relative_path        data/raw/imf-portwatch/daily-ports-data/2025-all-indonesia-ports/page_001_offset_0.json
replayable_query     ArcGIS REST /query: f=json, where=ISO3='IDN' AND date >= '2025-01-01' AND
                     date <= '2025-12-31', outFields=*, returnGeometry=false,
                     orderByFields=portid,date, resultRecordCount=1000, resultOffset paging
sha256               (in files.csv)
```

The same request should reproduce the same bytes; compare against `sha256` before re-storing anything.
