# Rights review — decide before this folder is pushed

Access is not permission. Every file here was publicly downloadable, but that says nothing about whether
it may be committed to a public Git repository. This file lists the decision each source needs, and where
to read the terms. It is not legal advice and it makes no claim about any licence.

`files.csv` carries the same decision per file in `redistribution_status`:

| Value | Files | Meaning |
| --- | --- | --- |
| `public_redistribution_ok` | 7 | A captured public licence statement supports redistribution with attribution |
| `unclear_review_required` | 128 | Publicly downloadable, but no licence statement could be captured or verified — **review before pushing** |
| `local_only` | 4 | Deliberately excluded from the tracked area by `.gitignore` |

## Sources cleared on a captured statement

| Source | Basis | Captured evidence |
| --- | --- | --- |
| `OSM11` (Geofabrik / OpenStreetMap) | ODbL 1.0 with attribution | `documentation/osm-geofabrik/opendatacommons_odbl-1.0.html`, `geofabrik_imprint_legal-notice.html`, ODbL statement inside the Indonesia index page |
| `EPA15` (US EPA) | US federal government work | The assessment PDF and programme page as published |

## Sources needing a rights decision

| Source | What to check | Rights URL recorded in `sources.csv` | Note |
| --- | --- | --- | --- |
| `PW1` IMF PortWatch | Whether the IMF terms permit republication of the queried records | `https://www.imf.org/external/terms.htm` | The provider names this URL as the layer's `licenseInfo`, but the page returned **HTTP 403** to automated access, so its text was never read. Highest-volume source in the folder (25.3 MB); decide this one first. |
| `WB2` World Bank CPPI | Report PDFs usually carry a CC BY notice on the imprint page | `https://www.worldbank.org/en/about/legal/terms-and-conditions` | Not verified in this run. 46.4 MB. |
| `BPS3` BPS | BPS terms of use for republication of publication PDFs | `https://www.bps.go.id/id/pages/term-of-use.html` | 88.6 MB — the single largest source. |
| `KH4` Kemenhub / Satu Data | Provider terms | `https://hubnet.kemenhub.go.id/` | `hubnet.kemenhub.go.id/terms` timed out, nothing captured. |
| `KD6` Kemendag | Portal terms | `https://satudata.kemendag.go.id/` | None captured. |
| `PLD8` Pelindo | Corporate-report reuse terms | `https://pelindo.co.id/` | 101.7 MB of annual and sustainability reports. |
| `JICT7`, `KAI9`, `JICA10`, `ESDM15`, `IOC13`, `NASA14`, `BI16` | Each publisher's own terms | see each row in `sources.csv` | Small files; a blanket decision per publisher is fine. |
| `UN5`, `ERA512`, `GFW17` | — | — | No data files stored, only documentation captures. GFW's terms are explicitly **noncommercial only (CC BY-NC 4.0)**: do not rely on that page as a data licence. |

## Deliberately local-only

All four files were moved here unchanged — no bytes were edited — and all four were found by a scan of the
tracked area for token-, key- and JWT-shaped strings.

| File | Why |
| --- | --- |
| `_local_only/bps-maritime/bps_publication_page_statistik-transportasi-laut-2024.html` | The captured page embeds two live one-time `download.php?f=…` tokens. Bytes preserved unchanged; signed URLs never enter the tracked area. |
| `_local_only/kemendag/ekspor-non-migas-pelabuhan-muat.html` | The site's shared page chrome embeds six JWT-shaped signed support-ticket links. The published trade table inside the page is clean — only the surrounding machinery carries the strings. |
| `_local_only/kemendag/impor-non-migas-pelabuhan-bongkar.html` | Same page chrome, same six JWT-shaped links. |
| `_local_only/worldbank-cppi/cppi_comparative_annex_document_page.html` | The document-detail shell embeds the publisher's client-side API key. The annex PDF itself is in `data/raw/worldbank-cppi/`. |

These are `_local_only` because of accidental secret-shaped strings, not because of restrictive terms: the
Kemendag data and the World Bank annex remain publicly downloadable on their own terms. If the owner judges
the strings harmless, each file can be moved back into the tracked area with its bytes untouched (see
`deferred.md` items 13 and 14). Do not "fix" them by editing the bytes — that would turn a source-native
capture into a modified one.

The brief for the case, and any terminal-level operator records, are local-only by default and are not
present in this folder. If the brief is added later it belongs in `_local_only/` unless the owner
explicitly authorises publication.

## Standing rules for anyone extending this folder

1. Do not add a file to `data/raw/` or `documentation/` until its rights row exists in `sources.csv`.
2. Never record a token-bearing or signed URL in a manifest, filename or log — record the stable landing
   page instead.
3. Never overwrite a byte-distinct snapshot. Re-running a query that returns identical bytes should be
   logged as a skip, not stored twice.
