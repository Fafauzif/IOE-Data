SOURCE 12 / ERA512 - Copernicus ERA5 hourly time-series on single levels
ACCESS STATUS: blocked_access

Requested landing page
  https://cds.climate.copernicus.eu/datasets/reanalysis-era5-single-levels-timeseries
  HTTP 200 (after one 307 redirect to ?tab=overview), Content-Type: text/html; charset=utf-8,
  128,312 bytes, <title>ERA5 hourly time-series data on single levels from 1940 to present</title>

What was stored: reanalysis-era5-single-levels-timeseries.html (this directory) - the landing page as
returned, source-native, unmodified. This is a documentation/evidence capture, NOT ERA5 data.

WHY THE DATA IS BLOCKED - exact wording carried by the captured page

1) Login gate. The page header of the anonymous response reads, verbatim:
     "Login - Register"
     "Login ‒ Register"
   i.e. the visitor is not signed in, and the Download tab's request form is served only to a signed-in
   session. The response body exposes a component named "loginLabel", confirming the login-gated flow.

2) Licence / terms-of-use acceptance. Embedded JSON-LD of the same page, field conditionsOfAccess:
     "Free access upon acceptance of applicable licences and terms of use"
   The licence widget text of the same page, verbatim:
     "To seamlessly retrieve data via API from any of the ECMWF-hosted Data Stores (using the single
      shared API endpoint https://datastores.ecmwf.int/api ), users must accept Terms & Conditions of
      applicable (or all) data stores as shown below (acceptance of relevant dataset licences also
      applies)."
   The dataset's own licence as stated on the page: "Licence CC-BY licence" (spdx: CC-BY-4.0),
   DOI 10.24381/1cf1ad76, publication date 2025-03-17, update date 2026-11-27.

3) API credential. The page's Zarr/API access instructions, verbatim:
     "You will need your CDS API key, available on [your profile page](../profile)."
   A CDS API key exists only inside an authenticated ECMWF/CDS user profile.

ACTION TAKEN: none of the above was satisfied. Per the collection rules, no account was created, no
licence or Terms & Conditions were accepted, no API key or token was obtained or used, and no anonymous
or token-bearing request was sent to the CDS/ECMWF download endpoints (https://cds.climate.copernicus.eu/api,
https://datastores.ecmwf.int/api, https://arco.datastores.ecmwf.int). The HTTP request count for this source
is exactly 1: the public landing page above.

WHAT WOULD UNBLOCK IT: a CDS account with the ECMWF Terms & Conditions and the CC-BY dataset licence
accepted in the profile, then either the cdsapi client or the ARCO/Zarr store with that API key. That is a
credential-holding decision for a human, outside this collection run.

Fallback considered and NOT taken: the ERA Explorer / STAC / CSW links on the page lead to the same
authenticated CDS retrieval. No unauthenticated bulk copy of ERA5 hourly series was found, and brute-forcing
or scraping around the login would breach the no-login / no-bypass rule.

Related local note: NASA POWER (source 14, data/raw/nasa-power/hourly-point/) is independent of CDS and was
collected successfully for the same three ports and year, so the atmospheric variables are not missing from
the corpus - only the ERA5 reanalysis replication of them is.
