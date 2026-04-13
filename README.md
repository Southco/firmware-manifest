# Southco Firmware Manifest

This repository hosts the firmware update manifest and OTA binaries for Southco devices.

## Structure

```
manifests/
  index.json              # Device catalog — entry point for the mobile app
  EA-PCA-018-10-2.json    # Per-device firmware update manifest
```

- **`index.json`** — Lists all supported devices with links to their individual manifests.
- **Per-device manifest** — Contains firmware groups, version compatibility rules, and download URLs for each device.

## Firmware Hosting

Firmware binaries (`.zip` archives containing `.OTA` images) are hosted as **GitHub Release assets**, not committed to the repo.

Each release is tagged with the convention `<model>-<version>` (e.g., `EA-PCA-018-10-2-revE.9`).

## Adding a New Firmware Version

1. Create a GitHub Release with the tag `<model>-<version>`
2. Upload the `.zip` firmware files as release assets
3. Update the device's manifest JSON with the new entry (including `sha256` and `size_bytes`)
4. Commit and push

## Adding a New Device

1. Create a new `manifests/<model>.json` file
2. Add an entry to `manifests/index.json`
3. Create GitHub Releases for the device's firmware files

## License

Copyright © Southco, Inc. All rights reserved. Unauthorized use, distribution, or modification is prohibited.
