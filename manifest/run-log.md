# Run log — IOE 2027 raw port data collection

**Collection date:** 2026-11-27 (UTC). Single unattended run, executed as one orchestrator plus six
parallel workers, each worker holding its own share of the request and byte budget.

## Limits applied

| Limit | Value | Outcome |
| --- | --- | --- |
| Per-file size | strictly < 100,000,000 bytes, 90 MB target per part | respected — largest stored file 40,036,394 bytes |
| Newly stored bytes | ≤ 500 MB | **275.14 MB** stored (135 tracked files + 4 local-only files) |
| Transferred response bodies | ≤ 750 MB | approx. 276 MB of bodies plus metadata/HTML probes; no part discarded for size |
| HTTP requests | ≤ 240 | **~182** (orchestrator ~99, workers ~83) |
| Run duration | ≤ 45 min of unattended work | parallelised across six workers to stay inside it |
| Concurrency | one in-flight download at a time, ≥ 1 s between requests to a service | respected; each worker ran its own sequential queue |
| Timeouts | connect 15 s, read inactivity 30 s, single request 120 s | respected; two requests hit the 120 s ceiling and their partial bytes were discarded |
| Retries | ≤ 2 on transient errors, honour Retry-After | used on 3 URLs (all 4xx/size cases, no persistent denial) |

Free disk at start: 196 GB. No temporary part file remains in the folder.

## What was completed

| Source | Outcome |
| --- | --- |
| PW1 IMF PortWatch | 44,413 daily records across 46 native JSON pages: 2025 full year for all 101 IDN ports, 2025 + 2020-2024 + 2026-01-01..11-20 for Tanjung Priok, Surabaya, Semarang; plus layer metadata, service catalogue and port reference |
| WB2 World Bank CPPI | 4 edition report PDFs (2020-2023), 2023 guidance note, methodology note, 2020-2025 comparative annex, WDS document-index responses |
| BPS3 BPS | Statistik Transportasi Laut publication PDFs for data years 2020-2024 (5 editions), 4-major-port statistics table page |
| KH4 Kemenhub | Provider-native XLSX and JSON for the major container-port throughput table (2020-2025), catalog and provider pages |
| KD6 Kemendag | Export-by-loading-port and import-by-unloading-port native HTML tables, kept separate |
| PLD8 Pelindo / Pelindo TPK | Parent annual reports 2024-2025 and sustainability reports 2024-2025; Pelindo TPK annual reports 2024-2025, sustainability reports 2024-2025, 2025 financial statements, investor index |
| KAI9 KAI | 5 official container/freight press releases |
| OSM11 OSM/Geofabrik | Indonesia and Java index pages, native md5 of the nationwide extract, ODbL 1.0 text, legal notice |
| IOC13 IOC tide gauge | Station metadata for koli (Jakarta Port) + four 10-day 2025 water-level windows |
| NASA14 NASA POWER | Full 2025 hourly series, 6 parameters, at the three priority port points |
| ESDM15 / EPA15 | ESDM 2019 grid emission factor PDF; EPA shore power assessment PDF and programme page |
| BI16 Bank Indonesia | JISDOR USD/IDR raw XML for 2020-2025, plus page, ASMX and WSDL captures |
| JICT7 JICT | Facilities/operations, about, home and publication pages |
| UN5 / JICA10 / GFW17 / ERA512 | metadata-only or blocked; see `deferred.md` |

## Skips and blockers (exact)

- `services9.arcgis.com` ArcGIS `/query` — no geometry on the tabular layer; point coordinates were
  instead read from the provider's `PortWatch_ports` layer 1 attributes.
- `pelindo.id/investor/laporan-tahunan` → **HTTP 403**, body is a bot-challenge page
  (`<title>Security Verification | Pelabuhan Indonesia</title>`). Not bypassed; alternate host used.
- `ioc-sealevelmonitoring.org` 30-day window → **curl (28) timed out after 120014 ms** at ~24 KB/s;
  partial record discarded, replaced by four 10-day windows.
- `wskursbi.asmx` SOAP POST → **HTTP 200 but HTML** WAF rejection: `Akses Ditolak` /
  `The requested URL was rejected. Please consult with your IT Administrator.` — the documented HTTP GET
  binding of the same read-only operation was used instead.
- `unctadstat.unctad.org/EN/BulkDownload.html` → **404**. See `deferred.md`.
- `download.geofabrik.de/legal-notices.html` → **404**; licence text taken from the footer link instead.
- `hubnet.kemenhub.go.id/terms` → connect timeout, no licence statement captured.
- `www.imf.org/external/terms.htm` → **HTTP 403** to automated access by the orchestrator, so the
  PortWatch licence text could not be read even though the provider names that URL as its `licenseInfo`.

## Containment actions (secret scan)

A scan of the tracked area for token-, key- and JWT-shaped strings flagged three captures. Each was moved
unchanged into `_local_only/` and excluded from the Git-ready area; no byte was edited, so the original
provider response is still present locally.

| File | Finding | Quarantined to |
| --- | --- | --- |
| BPS 2024 publication landing page | two live one-time `webapi.bps.go.id/download.php?f=…` tokens | `_local_only/bps-maritime/` |
| Kemendag exports-by-loading-port capture | six JWT-shaped signed support-ticket links in the shared page chrome | `_local_only/kemendag/` |
| Kemendag imports-by-unloading-port capture | six JWT-shaped signed support-ticket links in the shared page chrome | `_local_only/kemendag/` |
| World Bank CPPI document-detail page | the publisher's client-side `ApiKey` | `_local_only/worldbank-cppi/` |

In all four cases the payload data itself is clean — it is the page machinery around it that carries the
string, so each file can be returned to the tracked area by the owner without re-downloading. No token or
key value appears in `sources.csv`, `files.csv`, `README.md`, this log or `rights-review.md`, and none was
copied into a filename.

## Stopping condition

Work stopped at the end of the 18-item source register with the core 2025 files in hand, not on a hard
limit: 275 MB of a 500 MB allowance and ~182 of 240 requests were used. The largest remaining pulls are
deferred deliberately (nationwide OSM extract, ERA5, UNCTAD PLSCI), and they are recorded with scope
boundaries in `deferred.md`.

## Collection QA only

Every stored file was hashed and byte-counted after the last write; `files.csv` is generated from the
files on disk, so its hashes describe the actual bytes. No cleaning, joining, field renaming,
deduplication, OCR, transcription, type conversion, calculation or synthesis was performed, and no
number in this folder is an interpretation of the data.
