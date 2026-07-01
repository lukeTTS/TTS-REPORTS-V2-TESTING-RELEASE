# TTS-REPORTS-V2-TESTING-RELEASE

Go to https://rigneyl.pythonanywhere.com/

Included test licence codes:
TTS-DEMO-2026
TTS-PILOT-2026


# TTS_REPORTS_V2_Testing_Release

PythonAnywhere-only internal testing release of TTS Reports.

This release removes the Windows build pathway and local desktop/command-line run instructions from the packaged app. It is intended to be uploaded to PythonAnywhere and used through the hosted Flask web app for internal testing only.

## Release status

- **Release name:** `TTS_REPORTS_V2_Testing_Release`
- **Release type:** Internal hosted testing build
- **Target host:** PythonAnywhere
- **App framework:** Flask
- **Customer upload mode:** aPAT PDF report export only
- **Licence gate:** Enabled
- **Terms acceptance gate:** Enabled on activation screen
- **Shop specials:** Enabled using local `sale_items.json`

## What changed in this revision

This revision was created from:

`TTS_REPORTS_v1_0_STABLE_BUILD_LABEL_ACCESS_TERMS_SHOP_SPECIALS`

Changes made for `TTS_REPORTS_V2_Testing_Release`:

1. **Removed Windows build option**
   - Removed `build_windows.bat` from the release package.
   - Removed README instructions for building a Windows executable.
   - Removed in-app Help references to opening a Windows executable.

2. **Removed local desktop run option from the packaged guidance**
   - Removed README instructions for `python app.py` local running.
   - Removed README references to `http://127.0.0.1:5000`.
   - Removed in-app Help references to running the app from a local command window.
   - Removed the `if __name__ == "__main__"` local app runner from `app.py` so the app is treated as a hosted WSGI app.

3. **Removed command-line report generation option from the release package**
   - Removed `cli.py` from the release package.
   - Removed README instructions for command-line PDF generation.

4. **Updated release labelling**
   - Updated the sidebar version label to `TTS Reports V2 Testing`.
   - Updated the activation screen label to `TTS Reports V2 Testing Release`.
   - Updated the Help page label to `TTS Reports V2 Testing Release`.

5. **Updated Help page for PythonAnywhere testing**
   - The Help page now tells testers to open the internal PythonAnywhere test URL.
   - The Help page now explains activation, terms acceptance, browser downloads, and hosted test storage.
   - The Help page no longer describes a local desktop workflow.

6. **Cleaned release package**
   - Removed Python cache files.
   - Cleared generated sample PDFs from the runtime `output` folder.
   - Added empty `uploads` and `output` folders with `.gitkeep` files so PythonAnywhere has the expected folder structure.

7. **Prepared Flask secret key for hosted testing**
   - `app.py` now reads `TTS_REPORTS_SECRET_KEY` from the environment if available.
   - A fallback testing key remains for internal testing convenience.

## Current implemented features retained from the previous build

The following features remain in this release:

- Licence activation page before the main app.
- SHA-256 licence-code comparison against `licence_codes.json`.
- Session-based unlock after valid activation.
- Terms acceptance checkbox on the activation page.
- Terms acceptance logging to `terms_acceptance_log.jsonl`.
- Upload, preview, and report generation blocked unless the current terms version has been accepted.
- aPAT PDF import workflow.
- Existing `.padfx`, `.apx`, and `.xlsx` parser support retained inside `report_core.py` for future/internal use, but not enabled in the hosted customer upload workflow.
- Asset Register Dashboard.
- Asset search and filtering.
- Asset detail popup.
- Asset tree view grouped by location with expand/collapse options.
- Basic Report.
- PRO Report.
- Upcoming Retest Report.
- Report preview before PDF export.
- Settings page.
- Business profile and branding fields.
- Latest-test-only customer report mode.
- Advanced/audit date filtering controls.
- Responsive/tablet improvements.
- Shop Specials ticker on the Home dashboard.
- Specials page using `sale_items.json`.
- `/sale_items` Flask endpoint.

## PythonAnywhere deployment notes

Use these notes when uploading this release to PythonAnywhere for internal testing.

### 1. Upload the release

Upload the contents of the `TTS_REPORTS_V2_Testing_Release` folder to your PythonAnywhere project folder, for example:

```text
/home/yourusername/mysite/
```

The folder should contain:

```text
app.py
report_core.py
requirements.txt
licence_codes.json
sale_items.json
static/
templates/
uploads/
output/
README.md
pythonanywhere_wsgi.py
```

### 2. Install requirements

In a PythonAnywhere Bash console, install the requirements into the Python environment used by the web app:

```bash
pip install --user -r requirements.txt
```

Required packages:

```text
Flask>=3.0
reportlab>=4.0
pymupdf>=1.23
```

### 3. Configure the PythonAnywhere WSGI file

Set the PythonAnywhere WSGI file to import the Flask app.

Example:

```python
import sys
from pathlib import Path

project_dir = Path('/home/yourusername/mysite')
if str(project_dir) not in sys.path:
    sys.path.insert(0, str(project_dir))

from app import app as application
```

A reference file named `pythonanywhere_wsgi.py` is included in this release. Update the username/path before using it.

### 4. Set the source code directory

In the PythonAnywhere **Web** tab, set the source code directory to the folder containing `app.py`, for example:

```text
/home/yourusername/mysite
```

### 5. Reload the web app

After uploading files or changing the WSGI configuration, reload the web app from the PythonAnywhere **Web** tab.

### 6. Open the internal test URL

Open the PythonAnywhere web app URL provided by the Web tab. The first screen should be the licence activation screen.

## Licence codes

Pilot/demo codes included in this release:

```text
TTS-DEMO-2026
TTS-PILOT-2026
```

The app does not need to store raw real customer codes. To add a new code, hash the normalised licence code with SHA-256 and add the hash to `licence_codes.json`.

Normalisation rule:

- Trim leading/trailing spaces.
- Convert to uppercase.
- Remove internal whitespace.

Example hash generator:

```python
import hashlib

code = "CUSTOMER-CODE-123"
normalised = "".join(code.strip().upper().split())
print(hashlib.sha256(normalised.encode("utf-8")).hexdigest())
```

## Terms acceptance

Current terms version:

```text
TTS_REPORTS_TERMS_2026_06
```

Terms acceptance is completed on the activation screen. Upload, report preview, and report generation are blocked unless the current terms version has been accepted in the active Flask session.

Acceptance events are logged to:

```text
terms_acceptance_log.jsonl
```

Each log entry records:

- UTC timestamp
- terms version
- action
- licence hash
- IP address / forwarded IP
- browser user agent

If `TERMS_VERSION` is changed in `app.py`, existing sessions must accept the new version before the app unlocks again.

## Upload behaviour for this testing release

The hosted customer upload workflow accepts:

```text
.pdf
```

This is intended for aPAT PDF report exports only.

The UI and Flask routes reject:

```text
.padfx
.apx
.xlsx
```

The parser code for these formats remains in `report_core.py` so it can be re-enabled later if required.

## Shop Specials data

Shop Specials are loaded from:

```text
sale_items.json
```

The app does not scrape Shopify and does not require Shopify API keys in this release.

To update sale items:

1. Edit `sale_items.json`.
2. Save the file.
3. Reload the PythonAnywhere web app if required.
4. Refresh the Specials page in the browser.

Each item supports:

```json
{
  "title": "Product name",
  "brand": "Brand",
  "category": "Category",
  "badge": "EOFY Sale",
  "regular_price": "$99.00 Ex GST",
  "sale_price": "$79.20 Ex GST",
  "summary": "Short product description.",
  "url": "https://testandtagsupplies.com.au/collections/sale"
}
```

## Internal testing checklist

Use this checklist for testing this release on PythonAnywhere:

- [ ] App opens to licence activation screen.
- [ ] Invalid licence code is rejected.
- [ ] Valid licence code unlocks the app only when terms are accepted.
- [ ] Main app loads after activation.
- [ ] Lock app button clears the session and returns to activation screen.
- [ ] aPAT PDF upload previews correctly.
- [ ] Non-PDF upload is rejected.
- [ ] Asset Register Dashboard populates after upload.
- [ ] Asset search works.
- [ ] Location/status filters work.
- [ ] Tree view groups assets by location.
- [ ] Asset detail popup opens and closes correctly.
- [ ] Basic Report preview works.
- [ ] Basic Report export downloads PDF.
- [ ] PRO Report preview works.
- [ ] PRO Report export downloads PDF.
- [ ] Upcoming Retest Report preview works.
- [ ] Upcoming Retest Report export downloads PDF.
- [ ] Settings save to browser localStorage.
- [ ] Specials ticker loads sale items.
- [ ] Specials page loads sale item cards.
- [ ] Terms acceptance log is created/updated.
- [ ] Uploaded files and generated reports are created in the expected hosted folders.

## Known limitations for this testing release

- This is an internal testing release, not a final production release.
- Uploaded files and generated reports are stored on the hosted app filesystem during testing.
- No user accounts or per-customer cloud storage are included.
- No database-backed licence administration screen is included.
- No Shopify live feed integration is included.
- Shop Specials are manually updated through `sale_items.json`.
- PDF import requires selectable embedded text. Scanned image PDFs are not supported.
- Hosted performance depends on the PythonAnywhere plan, worker availability, file size, report type, and PDF generation load.

## Revision history

### TTS_REPORTS_v1_0_STABLE_BUILD_LABEL_ACCESS_TERMS_SHOP_SPECIALS

Previous build used as the starting point for this release.

Included:

- V1.0 stable app label.
- Licence activation page.
- Terms acceptance gate.
- Terms acceptance moved to activation screen.
- PDF-only customer upload restriction.
- Mobile/tablet scrolling improvements.
- Shop Specials ticker.
- Specials page.
- Local `sale_items.json` sale data.
- `/sale_items` endpoint.

### TTS_REPORTS_V2_Testing_Release

Current build.

Changed:

- Removed Windows build file.
- Removed command-line script from release package.
- Removed local desktop run instructions.
- Removed local app runner from `app.py`.
- Updated in-app Help page for PythonAnywhere-hosted internal testing.
- Updated release labels to V2 Testing.
- Cleaned generated/cache files from the release.
- Added PythonAnywhere deployment notes to this README.

## Change log template for the next revision

Use this section when creating the next release from `TTS_REPORTS_V2_Testing_Release`.

### Next release name

```text
TTS_REPORTS_V2_1_[Short_Description]
```

### Starting build

```text
TTS_REPORTS_V2_Testing_Release
```

### Changes made

Document every change under these headings:

#### Added

- New feature 1
- New feature 2

#### Changed

- Existing behaviour changed 1
- Existing behaviour changed 2

#### Fixed

- Bug fix 1
- Bug fix 2

#### Removed

- Removed item 1
- Removed item 2

#### Testing notes

- What was tested
- What passed
- What failed
- Known issue carried forward

#### Deployment notes

- PythonAnywhere setup changes required
- New environment variables required
- New files/folders required
- Database/storage changes required

## Recommended next revision items

Suggested next items after internal PythonAnywhere testing:

1. Add report generation performance improvements.
2. Reduce PRO Report page count by placing multiple asset cards per page.
3. Add a simple admin method for managing licence hashes.
4. Add cleanup handling for old uploaded files and generated PDFs.
5. Add clearer test/non-production banners for internal testers.
6. Add database-backed audit logs if testing moves beyond a small pilot.
7. Add a controlled Shopify product feed if Specials need to update automatically.

