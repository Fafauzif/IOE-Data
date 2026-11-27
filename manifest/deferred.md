# Deferred work — in priority order

Everything below was attempted or located during the run and then deliberately not retrieved. Each entry
gives the scope boundary and the safest next authorised retrieval. Nothing here is an error that a retry
would fix immediately; each needs either budget, credentials or a different retrieval mechanism.

## 1. PW1 — IDN-wide PortWatch history and 2026 year-to-date

- **Scope boundary:** 101 IDN ports × 2020-2024 = 184,527 records (~74 MB), and 101 IDN ports ×
  2026-01-01..11-20 = 32,724 records (~13 MB). The 2025 full year for the same 101 ports *is* already in
  `data/raw/imf-portwatch/daily-ports-data/2025-all-indonesia-ports/`.
- **Safest next retrieval:** repeat the existing paged `/query` per calendar year
  (`where=ISO3='IDN' AND date >= '<Y>-01-01' AND date <= '<Y>-12-31'`, `orderByFields=portid,date`,
  `resultOffset` 0,1000,…). ~37 pages per year. Run one year per session to stay inside the request ceiling.

## 2. ERA5 — Copernicus hourly reanalysis at the port points

- **Scope boundary:** 10 m U/V wind and precipitation, monthly chunks, for port514 Tanjung Priok,
  port1237 Surabaya and port1179 Semarang. The port coordinates are already resolved and audited in
  `documentation/imf-portwatch-ports/README-points.txt`.
- **Blocker:** the dataset requires an authenticated CDS account with accepted licences and an API key.
  No account was created and no licence accepted, by rule.
- **Safest next retrieval:** the user adds their own `~/.cdsapirc` and accepts the licences in the CDS web
  interface, then one `cdsapi` request per port per month. Do not share the key with this folder.

## 3. UNCTAD — quarterly Port Liner Shipping Connectivity Index

- **Scope boundary:** the quarterly PLSCI series and its published definitions, plus country-level LSCI
  kept separately.
- **Blocker:** no documented native export. `unctadstat.unctad.org/datacentre` is a single-page app;
  `/EN/BulkDownload.html` is 404; `unctadstat-api.unctad.org/datamart-api` returns 400 because the `path`
  parameter is undocumented; `/wds/TableViewer` and `/wds/ReportFolders` both 302 to the SPA shell.
- **Safest next retrieval:** export once by hand from the data-centre UI ("Download" on the PLSCI table),
  or ask UNCTAD's data team for the documented API path. A rendered-table scrape is deliberately avoided
  because it would create a derived table rather than source-native bytes.

## 4. OSM — nationwide and Java extracts

- **Scope boundary:** `indonesia-latest.osm.pbf` (~1.6 GB) and `java-latest.osm.pbf` (899,757,620 bytes),
  plus the 704-858 MB dated Java files and the 1.8-2.2 GB shapefile/GPKG variants.
- **Blocker:** `deferred_size` — every published extract is 8-25× the 90 MB per-file cap, and Geofabrik
  publishes no Jakarta-level extract.
- **Safest next retrieval:** take the extract outside this job folder (it is not a GitHub candidate), or
  use a published extract service that already serves small bbox files, and record the snapshot date plus
  the md5 already stored at `data/raw/osm-geofabrik/indonesia/2026-11-27/indonesia-latest.osm.pbf.md5`
  (958156b0447005ea6080acff68b6b6d6) so the snapshot is identifiable.

## 5. WB2 — CPPI 2024 and 2025 editions

- **Scope boundary:** the 2024 and 2025 edition reports and their data annexes. The 2020-2023 editions,
  methodology note, guidance note and comparative annex are already stored.
- **Blocker:** the two newer editions are not present in the World Bank WDS document index
  (`qterm=Container Port Performance Index` returns 113 documents, none dated 2025-2026).
- **Safest next retrieval:** re-query the WDS API later, or fetch the edition PDF from the CPPI landing
  page once its publication widget points at a stable `documents1.worldbank.org/curated/en/…` asset.

## 6. IOC13 — full-year 2025 tide-gauge coverage for Kolinamil

- **Scope boundary:** four 10-day windows (Jan/Apr/Jul/Oct 2025) are stored; the remaining ~330 days of
  2025 are not.
- **Blocker:** throughput of ~24 KB/s against the 120 s per-request ceiling, and a documented 30-day
  maximum per call that this link cannot serve inside that ceiling.
- **Safest next retrieval:** continue in 10-day windows (~1 MB each, ~33 requests for the year), one
  window per request with a 1 s gap. The station has no published datum, so keep it unstated.

## 7. PLD8 — remaining Pelindo documents

- **Scope boundary:** Pelindo parent financial-statement index (`pelindo.co.id/investor/laporan-keuangan`)
  and the Pelindo TPK quarterly management report (16.0 MB).
- **Blocker:** request cap spent, not a server restriction — both are plain GETs.
- **Safest next retrieval:** fetch the index page, then the linked PDFs directly.

## 8. JICT7 — official throughput release

- **Scope boundary:** any JICT publication carrying annual container throughput or berth productivity.
- **Blocker:** none published — `jict.co.id/sustainability-publication` renders `Belum ada publikasi`, and
  no annual report exists on the site.
- **Safest next retrieval:** the JICT or Pelindo annual report narrative for the terminal, or a Pelindo
  Terminal Petikemas disclosure. Do not substitute press estimates for a terminal record.

## 9. JICA10 — Patimban project documents

- **Scope boundary:** JICA Patimban phase III development/planning/progress files, and any Kemenhub
  operational statistics for Patimban.
- **Blocker:** the register-linked release is narrative only and links no file or Kemenhub page.
- **Safest next retrieval:** the JICA project page or an ODA loan agreement document for the phase, if a
  public one is published; keep projected and observed files separated by their published label.

## 10. GFW17 — cargo vessel presence

- **Scope boundary:** vessel-presence responses for small documented port geometries and short date windows.
- **Blocker:** `blocked_access` — personal Bearer token required, and the terms are noncommercial-only
  (CC BY-NC 4.0) with mandatory attribution, which is incompatible with a redistributable raw folder.
- **Safest next retrieval:** only if the case owner accepts a noncommercial, non-redistributed local copy;
  then the user's own token, small geometries, and the response kept in `_local_only/`. Note that these are
  AIS estimates, not terminal attendance logs.

## 11. CASE18 — the IOE 2027 brief and terminal records

- **Scope boundary:** the brief itself and any terminal-level berth, pilot, tug, work order, yard, gate,
  meter, injury, contract or tariff records.
- **Blocker:** `not_supplied` — the workspace holds the collection prompt, not the brief, and no terminal
  records were provided.
- **Safest next retrieval:** the user places the brief in `_local_only/` (it is local-only unless they
  authorise publication) and states which of the terminal record types can be shared with permitted access.

## 12. KD6 — Kemendag machine-readable export

- **Scope boundary:** a CSV/XLSX of exports by loading port and imports by unloading port.
- **Blocker:** the pages publish no export control that is not a form postback; the native HTML table is
  the only published form, and it is stored as such.
- **Safest next retrieval:** re-check the page later for a published download link; do not submit the site's
  form on the user's behalf.

## 13. KD6 — return the trade-by-port captures to the tracked area (owner decision)

- **Scope boundary:** `_local_only/kemendag/ekspor-non-migas-pelabuhan-muat.html` and
  `_local_only/kemendag/impor-non-migas-pelabuhan-bongkar.html` hold the published export-by-loading-port and
  import-by-unloading-port tables. They are quarantined because the site's shared page chrome embeds six
  JWT-shaped signed support-ticket links (payload is a small integer id, not a credential of yours).
- **Blocker:** policy, not access — the files download fine and the bytes are intact.
- **Safest next step:** the owner decides. If the strings are acceptable, move both files (unchanged) to
  `data/raw/kemendag/`, set the `KD6` row in `sources.csv` back to `downloaded`, and extend their
  `files.csv` rows. Do **not** edit the page bytes to strip the strings: that would replace a source-native
  capture with a modified one.

## 14. WB2 — return the CPPI document-detail capture (owner decision)

- **Scope boundary:** `_local_only/worldbank-cppi/cppi_comparative_annex_document_page.html` is the Angular
  shell of the annex's document-detail page; it embeds the publisher's client-side API key.
- **Blocker:** policy, not access. The annex PDF itself (`data/raw/worldbank-cppi/comparative-annex/`) is
  tracked and complete.
- **Safest next step:** leave it local-only; the shell page adds nothing the annex PDF does not already
  give you. Delete it if a clean tracked area matters more than the capture.
