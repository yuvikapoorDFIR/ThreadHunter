<div align="center">

<img src="https://github.com/user-attachments/assets/0623cfa5-39f2-4605-8992-145afa502e6d" alt="ThreadHunter" width="420"/>

# ThreadHunter

**Forensic-grade BEC investigation platform for Microsoft 365 Unified Audit Log analysis**

[![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=flat-square&logo=python&logoColor=white)](https://python.org)
[![License](https://img.shields.io/badge/License-MIT-blue?style=flat-square)](LICENSE)
[![Platform](https://img.shields.io/badge/Platform-Windows%20%7C%20macOS%20%7C%20Linux-lightgrey?style=flat-square)]()
[![DFIR](https://img.shields.io/badge/Use%20Case-DFIR%20%7C%20BEC-E95555?style=flat-square)]()
[![Author](https://img.shields.io/badge/Author-Yuvi%20Kapoor-2EAD7A?style=flat-square)](https://linkedin.com/in/yuvi-kapoor-5a38521a5)

</div>

---

## Overview

**ThreadHunter** is a desktop DFIR platform built for investigating Microsoft 365 Unified Audit Log data during Business Email Compromise engagements. It enriches raw UAL exports with GeoIP data, surfaces impossible travel, detects IOCs, and generates self-contained investigation reports — entirely on your local machine.

Built by [Yuvi Kapoor](https://linkedin.com/in/yuvi-kapoor-5a38521a5) at **CyberClan DFIR**.

---

## Features

- **UAL ingestion** — load raw M365 UAL exports (CSV or XLSX)
- **AuditData parsing** — extracts ClientIP, AppId, SessionId, UserAgent, and UniqueTokenId from nested JSON with parallel processing
- **GeoIP enrichment** — Country / City / ASN / Organisation via MaxMind GeoLite2 — fully offline
- **Live tile map** — scrollable world map with clickable markers, cluster counts, and location detail panel
- **Impossible travel detection** — flags same-account logins from different countries within a configurable time window
- **IOC filter** — match across IP, SessionId, UserAgent, and UniqueTokenId simultaneously
- **Summary dashboard** — top operations, IPs, users, and countries with flag images and bar charts
- **Data table** — sortable columns, full-text search, double-click row detail with raw AuditData JSON
- **HTML report export** — self-contained investigation report with impossible travel, IOCs, and analyst notes
- **Case management** — SQLite-backed `.threadhunter` case files; metadata, events, IOCs, and notes in one file
- **Dark / Light theme** — toggle at any time without restarting
- **Export** — save filtered or full data to CSV or Excel

---

## Screenshots

> _Add screenshots of the application here_

---

## Downloads

> No installer required — unzip and run.

| Platform | Download | Requirements |
|---|---|---|
| **Windows 10/11** | [`ThreadHunter_Windows_v1.0.zip`](../../releases/latest) | Python 3.10+ |
| **macOS 12+** (Intel & Apple Silicon) | [`ThreadHunter_Mac_v1.0.zip`](../../releases/latest) | Python 3.10+ from python.org |

---

## Installation

### 1. Install dependencies

```bash
pip install -r requirements.txt
```

### 2. Get GeoLite2 databases (free, one-time)

Register at [maxmind.com](https://www.maxmind.com/en/geolite2/signup) and download:
- `GeoLite2-City.mmdb`
- `GeoLite2-ASN.mmdb`

Place them anywhere — ThreadHunter lets you browse to them at runtime and remembers the paths between sessions.

> GeoLite2 databases cannot be bundled in this repository under MaxMind's licence. All lookups happen locally — no IP data is ever sent to MaxMind. If MaxMind registration is not possible, [DB-IP Lite](https://db-ip.com/db/lite.php) databases are compatible and available under Creative Commons with no registration.

### 3. Export a UAL from Microsoft 365

1. Go to [Microsoft Purview compliance portal](https://compliance.microsoft.com) → **Audit**
2. Set your date range and click **Search**
3. Once complete → **Export** → **Download all results**
4. Load the resulting `.csv` into ThreadHunter via **OPEN UAL FILE**

📖 [Full export instructions — Microsoft documentation](https://learn.microsoft.com/en-us/purview/audit-log-export-records)

### 4. Run

```bash
python main.py
```

Or open a case file directly:

```bash
python main.py path/to/MyCase.threadhunter
```

---

## Dependencies

| Package | Purpose |
|---|---|
| `customtkinter` | Modern themed GUI framework |
| `polars` | High-performance DataFrame operations |
| `Pillow` | Logo, icon, and flag image rendering |
| `maxminddb` | GeoLite2 database lookups |
| `tldextract` | Domain extraction from URLs and email addresses |

---

## Usage

### Platform Notes

**Windows** — works out of the box with standard Python from [python.org](https://www.python.org/downloads/).

**macOS** — install Python from [python.org](https://www.python.org/downloads/), not Homebrew. The python.org installer bundles a more reliable version of Tk. If the app opens behind other windows on first launch, click the ThreadHunter icon in the Dock.

**Linux:**
```bash
sudo apt install python3-tk   # Ubuntu/Debian
pip install -r requirements.txt
python main.py
```

### Keyboard Shortcuts

| Shortcut | Action |
|---|---|
| `Ctrl+R` | Run Enrichment |
| `Ctrl+F` | Focus search in Data Table |
| `Ctrl+E` | Export current view |
| `Ctrl+M` | Map View |
| `Ctrl+I` | IOC Filter |
| `Ctrl+U` | Summary |

### Map Tile Servers

| Option | Internet required | Notes |
|---|---|---|
| OpenStreetMap | Yes | Default |
| Google Maps | Yes | |
| Google Satellite | Yes | |
| CartoDB Dark | Yes | |
| Self-hosted (localhost) | No | Use with a local OSM tile server |

**Running fully offline:** Use [OpenFreeMap](https://github.com/hyperknot/openfreemap) — a self-contained Docker OSM tile server. Once running, select **Self-hosted (localhost)** in the dropdown.

> When using external tile servers, requests include the lat/lon bounding box of the area being viewed. On sensitive engagements, use a local tile server.

---

## Case Files

Each investigation is a single `.threadhunter` file — a SQLite database containing case metadata, all enriched events with GeoIP data, an IOC watchlist, and analyst notes.

> ⚠️ **Case files contain PII** from audit logs. Store them on encrypted volumes and handle in accordance with your engagement data handling policy. The in-app delete function overwrites the file with zeros before removal.

---

## Data & Privacy

### What leaves your workstation

| Component | External contact | Data sent |
|---|---|---|
| GeoIP enrichment | None — fully local | Nothing |
| Map tiles (external server) | Tile provider CDN | Lat/lon bounding box of viewed area |
| Map tiles (self-hosted) | None | Nothing |
| Case file | None | Nothing |
| HTML report | None (opens in local browser) | Nothing unless you send the file |

### Privacy considerations

UAL exports contain personal information about every user whose activity was logged — names, email addresses, IP addresses, session identifiers, file access records, and authentication events.

| Jurisdiction | Instrument | Key consideration |
|---|---|---|
| Australia | Privacy Act 1988 / APPs | Collect only what is necessary; destroy when no longer required |
| European Union | GDPR | Data minimisation and purpose limitation apply |
| United Kingdom | UK GDPR / DPA 2018 | Same as EU GDPR in practice |
| United States (healthcare) | HIPAA | UAL logs from healthcare environments may touch PHI-adjacent systems |
| United States (finance) | SEC / FINRA | Retention obligations may conflict with data minimisation |

> This is general guidance, not legal advice. Consult your firm's legal team for jurisdiction-specific obligations.

---

## File Structure

```
threadhunter/
├── main.py                  # Entry point
├── threadhunter.py          # Main investigation window
├── launcher.py              # Case launcher / recent cases
├── requirements.txt
├── appid_lookup.csv         # M365 Application ID → Display Name mapping
├── operations_config.yaml   # Per-operation field extraction config
├── flags/                   # Pre-rendered country flag PNGs (253 flags)
├── tkintermapview/          # Bundled map widget
└── core/
    ├── case.py              # Case model and SQLite schema
    └── database.py          # SQLite connection wrapper
```

---

## Attribution

- Map tiles: © [OpenStreetMap](https://www.openstreetmap.org/copyright) contributors
- GeoIP data: This product includes GeoLite2 data created by MaxMind — [maxmind.com](https://www.maxmind.com)
- Country flags: [iso-country-flags-svg-collection](https://github.com/joielechong/iso-country-flags-svg-collection)

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).

- Do not attach real UAL exports or `.threadhunter` case files to issues or pull requests
- Do not commit `*.mmdb` GeoIP databases to the repository

---

## Author

**Yuvi Kapoor** — Senior DFIR Analyst, CyberClan (APAC)

Specialising in ransomware and BEC incident response engagements.

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Yuvi%20Kapoor-0A66C2?style=flat-square&logo=linkedin)](https://linkedin.com/in/yuvi-kapoor-5a38521a5)

---

## Licence

MIT — see [LICENSE](LICENSE)

---

## Disclaimer

This tool is intended for use by authorised security professionals conducting legitimate incident response and forensic investigations. Ensure all M365 queries and data handling are performed in accordance with applicable laws and your organisation's policies.

---

<div align="center">

Built for the DFIR community · CyberClan APAC

</div>
