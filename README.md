# Mixed Coding Workspace: Koochin Report Tool and Shiftery Store Ops

A desktop-sized working folder where several unrelated threads land rather than one project: a TSETMC
stock-report generator with a packaged Windows dashboard, the live-site engineering folder for the
`shifteryadak.ir` WooCommerce store, a batch of Word/HTML-to-PDF rendering tests, a single-file
portfolio page, and a one-shot API probe client. Nothing here is packaged or published as a unit.

**Suggested repo name:** `koochin-shifter-workspace`
**Stack:** Python 3.12 (openpyxl, requests, BeautifulSoup, Playwright, paramiko, PyInstaller), PHP/WooCommerce and a WordPress mu-plugin, Node with jsdom for DOM tests, HTML/CSS
**Status:** active
**Last modified:** 2026-09-20

## What it does

- `koochin-report/koochin-report/` - the substantial piece: a TSETMC (Tehran Stock Exchange) daily
  report generator for the symbol کوچین. `koochin_report.py` emits the client's RTL Excel format
  (YekanWeb font, autofilter, Jalali date column) for 30-second trade windows above a value threshold
  with buyer power; `main.py` + `app/index.html` wrap it in a Persian dashboard served locally and
  packaged as `dist/BourseFilter.exe` via `BourseFilter.spec`. Has its own README, tests in `tests/`,
  and jsdom-based DOM tests (`test_basket_dom.js`, `test_onedate.js`).
- `shifter_work/` - engineering and ops for the live WooCommerce store. `live/shifter-smart-search/`
  is a WordPress mu-plugin (loader + `includes/class-sss-{data,db,api,frontend}.php`, `templates/`,
  `assets/`, Tailwind build inputs) providing an AJAX search panel; `fixed/` and `Final_Files_bugfix/`
  hold the corrected cookma/others price-sync plugins and Python scrapers; `release_zips/` the four
  client deliverable zips. Around them: `deploy_sss_ftp.py`, `do_ftp_upload.py`, `list_ftp.py`,
  `gateway_helper.py`, `run_purge.py`, `purge_rocket_cache.py` (WP Rocket disk cache),
  `probe_sss_features.py` / `probe_sss_followup.py` (black-box probes of the live search endpoint),
  and pulled HTML/JS/CSS captures (`search_desktop.html`, `js_*.js`, `ri.css`, `extract_*.txt`).
- Loose root artifacts - a Word/HTML → PDF rendering investigation with no surviving driver script:
  `test_perfect_word.{docx,pdf,png}`, `test_table.{docx,html,pdf}`, `pdf_structure.json`, and the
  `page_*` / `verify_page_*` / `word_*` PNGs comparing renders.
- `index.html` with `logo_*_b64.txt` and `polaris_logo_transparent.png` - a single-file
  "Polaris Swords | Portfolio" page, logo inlined as base64.
- `typesafe_client.py` - minimal stdlib client for the TypeSafe `systemone` endpoint.

## Layout

```
koochin-report/koochin-report/   report CLI + BourseFilter desktop app (own README)
shifter_work/                    live store: mu-plugin, fixes, release zips, deploy + probe scripts
index.html, logo_*_b64.txt       Polaris Swords portfolio page and inlined logo
test_*.docx/.pdf/.png, pdf_structure.json, page_*.png, verify_*.png   rendering-test residue
typesafe_client.py               TypeSafe API probe client
```

## Running it

Koochin report (its own README has the full flag set):

```bash
cd koochin-report/koochin-report
py koochin_report.py --symbol کوچین --out koochin.xlsx
py main.py                    # local dashboard on the default browser
```

API probe (needs a real key in `TYPESAFE_API_KEY`): `python typesafe_client.py "your message"`.

## Notes

- Treat `shifter_work/` as production-touching: the deploy, upload and purge scripts connect to the
  live host, and `probe_sss_features.py` drives the public production search endpoint. None of them
  belong in a "try the README" run.
- Committed credentials (values not reproduced; all must be removed before any publish). FTPS account
  and password: `shifter_work/deploy_sss_ftp.py`, `shifter_work/do_ftp_upload.py`,
  `shifter_work/list_ftp.py`, `shifter_work/purge_rocket_cache.py`. SSH host/user/password:
  `shifter_work/gateway_helper.py`, `shifter_work/run_purge.py`. API key: `typesafe_client.py`.
  Tokens/cookies: `shifter_work/live/shifter-smart-search/shifter-smart-search.php`
  (`SSS_TRIGGER_TOKEN`), `shifter_work/fixed/cookma-woo/cookma-woo.php` and
  `shifter_work/fixed/others/others.php` (default `csv_sync_secret_token`),
  `shifter_work/fixed/scrapers/price.py` (cookma session id).
- Not publishable as one repo. The clean split is `koochin-report` as its own repository (after
  dropping `build/`, `dist/`, `.pytest_cache/`), `shifter_work/live/shifter-smart-search` as a private
  plugin repo, and the rest as personal scratch.
