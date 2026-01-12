# gdrive-deduper

Find duplicate files in your Google Drive using MD5 checksums. Fast, read-only, outputs a CSV report.

## Setup

### 1. Install dependencies

```bash
uv sync
```

### 2. Get Google OAuth credentials (one-time)

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project (or select existing)
3. Enable the **Google Drive API**:
   - Go to "APIs & Services" → "Library"
   - Search "Google Drive API" → Enable
4. Create OAuth credentials:
   - Go to "APIs & Services" → "Credentials"
   - Click "Create Credentials" → "OAuth client ID"
   - Select "Desktop app"
   - Download the JSON file
5. Save the file as `credentials.json` in this folder

> **Note:** If your Cloud project is in "Testing" mode, add your Google account as a test user under "OAuth consent screen" → "Test users".

## Usage

```bash
# Scan entire Google Drive
uv run main.py

# Scan specific folder (recursive)
uv run main.py --path "/Photos"

# Custom output file
uv run main.py --output my_report.csv
```

On first run, a browser window opens for Google login. Your token is cached in `token.json` for future runs.

## Output

Results are saved to `.output/duplicates.csv` with columns:

| Column | Description |
|--------|-------------|
| filename | Name of the file |
| path1 | Full path of first copy |
| path2 | Full path of second copy |
| date1 | Modified date of first copy |
| date2 | Modified date of second copy |
| md5 | MD5 checksum |
| size | File size in bytes |
| status | `duplicate` or `uncertain` |

**Status values:**
- `duplicate` - Confirmed duplicate (same MD5 and size)
- `uncertain` - Same MD5 but different size (rare, worth investigating)

At the end, the script shows potential space savings if duplicates were removed.

## Limitations

- Google Workspace files (Docs, Sheets, Slides) don't have MD5 checksums and are skipped
- Shared drives are excluded (only scans "My Drive")
- Read-only: this tool does not delete anything
