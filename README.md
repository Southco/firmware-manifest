# Southco Firmware Manifest

This repository hosts the firmware update manifest and OTA binaries for Southco devices.

## Structure

```
manifests/
  index.json              # Production device catalog — entry point for the mobile app
  index-dev.json          # (Optional) Development/beta device catalog
  <model>.json            # Per-device production firmware manifest
  <model>-dev.json        # (Optional) Per-device development/beta firmware manifest
```

- **`index.json`** — Lists all supported devices with links to their individual manifests. The mobile app fetches this first.
- **Per-device manifest** — Contains firmware groups, version compatibility rules, and download URLs for each device.
- **Development/beta files** — `index-dev.json` and `<model>-dev.json` follow the same schema as their production counterparts. They allow shipping pre-release firmware to beta testers without exposing it to production users. See [Development / Beta Channel](#development--beta-channel) below.

## Firmware Hosting

Firmware binaries (`.zip` archives containing `.OTA` images) are hosted as **GitHub Release assets**, not committed to the repo.

Each release is tagged with the convention `<model>-<version>` (e.g., `EA-PCA-018-10-2-revE.9`).

## Adding a New Firmware Version

### Step 1: Prepare your firmware files

Place your `.zip` firmware files in a local folder. You will need:
- The **APP** firmware zip (required)
- The **DFU** firmware zip (optional, only if a bootloader/radio update is needed)

### Step 2: Compute sha256 and file size

Run these commands for each zip file to get the values you'll need for the manifest:

```bash
# On macOS
shasum -a 256 <filename>.zip
wc -c < <filename>.zip

# On Linux / WSL
sha256sum <filename>.zip
wc -c < <filename>.zip
```

### Step 3: Create a GitHub Release (web UI)

1. Go to https://github.com/Southco/firmware-manifest
2. Click **Releases** (right sidebar) → **Draft a new release**
3. Click **Choose a tag** → type your tag name and click **Create new tag**
   - Tag format: `<model>-<version>` (e.g., `EA-PCA-018-10-2-revE.10`)
   - Target branch: `main`
4. Set the **Release title** (e.g., `EA-PCA-018-10-2 Rev E.10`)
5. In the description, note what's included (e.g., "App firmware Rev E.10 + DFU Rev A→C")
6. Drag and drop your `.zip` files into the **Attach binaries** area at the bottom
7. Click **Publish release**

### Step 3 (alternative): Create a GitHub Release (CLI)

If you have the GitHub CLI (`gh`) installed:

```bash
gh release create EA-PCA-018-10-2-revE.10 \
  --repo Southco/firmware-manifest \
  --title "EA-PCA-018-10-2 Rev E.10" \
  --notes "App firmware Rev E.10 + DFU Rev A→C" \
  EA-PCA-018-10-2_APP_revE.10.zip \
  EA-PCA-018-10-2_DFU_revAtoC.zip
```

### Step 4: Get the download URLs

After publishing, each uploaded file gets a permanent URL:

```
https://github.com/Southco/firmware-manifest/releases/download/<tag>/<filename>
```

For example:
```
https://github.com/Southco/firmware-manifest/releases/download/EA-PCA-018-10-2-revE.10/EA-PCA-018-10-2_APP_revE.10.zip
```

You can verify by clicking the file on the release page — the URL in your browser is the download URL.

### Step 5: Update the device manifest

Edit the device's JSON file in `manifests/` (e.g., `manifests/EA-PCA-018-10-2.json`). Add a new entry under the appropriate `firmware_group` → `available_updates`:

```json
{
  "target_version": "Rev E.10",
  "release_date": "2026-04-13",
  "app_file": {
    "filename": "EA-PCA-018-10-2_APP_revE.10.zip",
    "url": "https://github.com/Southco/firmware-manifest/releases/download/EA-PCA-018-10-2-revE.10/EA-PCA-018-10-2_APP_revE.10.zip",
    "sha256": "<value from step 2>",
    "size_bytes": <value from step 2>
  },
  "dfu_file": null
}
```

### Step 6: Commit and push

```bash
git add manifests/EA-PCA-018-10-2.json
git commit -m "Add Rev E.10 firmware for EA-PCA-018-10-2"
git push
```

## Adding a New Device

1. Create a new `manifests/<model>.json` file (use an existing device file as a template)
2. Add an entry to `manifests/index.json`:
   ```json
   {
     "model_number": "<model>",
     "display_name": "<model>",
     "manifest_url": "https://raw.githubusercontent.com/Southco/firmware-manifest/main/manifests/<model>.json"
   }
   ```
3. Create GitHub Releases for the device's firmware files (follow the steps above)
4. Commit and push both files

## Development / Beta Channel

Two parallel sets of manifests may exist in `manifests/`:

| Channel | Index file | Per-device file |
|---------|------------|-----------------|
| Production | `index.json` | `<model>.json` |
| Development / Beta | `index-dev.json` | `<model>-dev.json` |

The two sets use the **same schema**; only the file names differ. The mobile app selects the channel by choosing which index URL to fetch — production builds resolve `index.json`, beta builds resolve `index-dev.json`. A device on a production-only build will never see any dev URLs.

**Conventions for dev firmware:**
- Use a distinct GitHub Release tag suffix to keep dev assets separate (e.g., `EA-PCA-018-10-revE.13-beta`). Do not mix dev and production assets in the same release.
- Dev manifests should never reference production release assets, and vice versa.
- When promoting a dev firmware to production, copy the entry from `<model>-dev.json` into `<model>.json` and re-publish the assets under a production tag.

## License

Copyright © Southco, Inc. All rights reserved. Unauthorized use, distribution, or modification is prohibited.
