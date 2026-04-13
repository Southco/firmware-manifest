# Southco Firmware Manifest

## Project Overview
This repository contains the firmware update manifest for Southco devices. The manifests define available firmware updates, version compatibility, and download URLs for OTA (Over-The-Air) updates. Firmware binaries are hosted via GitHub Releases.

## Manifest Structure

### Split-file layout
```
manifests/
  index.json              # Device catalog — mobile app fetches this first
  EA-PCA-018-10-2.json    # Per-device update manifest
```

### `index.json`
- **manifest_version**: Schema version (currently "2.0")
- **devices**: Array of device entries with `model_number`, `display_name`, and `manifest_url`

### Per-device manifest (e.g., `EA-PCA-018-10-2.json`)
- **model_number**: Device identifier
- **manifest_version**: Schema version
- **firmware_groups**: Groups of update rules
  - **valid_current_versions**: Which installed firmware versions qualify for the updates in this group
  - **available_updates**: List of firmware updates, each with:
    - `target_version`, `release_date`
    - `app_file`: Application firmware (`filename`, `url`, `sha256`, `size_bytes`)
    - `dfu_file`: DFU firmware (same fields, nullable)

## Key Details
- Firmware binaries are hosted via GitHub Releases as release assets
- Download URL pattern: `https://github.com/Southco/firmware-manifest/releases/download/<tag>/<filename>`
- Tag naming convention: `<model>-<version>` (e.g., `EA-PCA-018-10-2-revE.9`)
- Files are `.zip` archives containing `.OTA` firmware images
- A device's current version determines which update group applies
- DFU files handle bootloader/radio updates and may be null if not needed
- `sha256` and `size_bytes` fields provide integrity checking and download progress

## Conventions
- Keep the JSON well-formatted and valid
- Versions follow a "Rev X.Y" naming scheme
- Release dates use ISO 8601 format (YYYY-MM-DD)
- Adding a new device = one new JSON file in `manifests/` + one entry in `index.json`
