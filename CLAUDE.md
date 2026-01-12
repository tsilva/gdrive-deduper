# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
# Install dependencies
uv sync

# Run the deduplication scan
uv run main.py

# Scan specific folder
uv run main.py --path "/Photos"

# Custom output file
uv run main.py --output .output/custom.csv

# Use different credentials file
uv run main.py --credentials path/to/creds.json
```

## Architecture

Single-file Python tool (`main.py`) that uses Google Drive API v3 to find duplicate files.

**Flow:**
1. OAuth authentication (cached in `token.json`)
2. Single paginated API call fetches all files with MD5 metadata
3. Build in-memory path lookup from parent IDs
4. Group files by MD5 checksum
5. Output CSV with all duplicate pairs

**Key design decisions:**
- Uses `drive.metadata.readonly` scope (read-only, no file content access)
- Fetches all files in one query then filters locally (faster than recursive folder traversal)
- Path resolution uses memoization (`path_cache`) for efficiency
- Files with same MD5 but different size marked as "uncertain"
- Google Workspace files (Docs, Sheets) skipped (no MD5 available)

**Output:** `.output/duplicates.csv` - folder tracked by git, contents ignored.
