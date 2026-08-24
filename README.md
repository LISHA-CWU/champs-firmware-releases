# champs-firmware-releases

Public firmware releases for CHAMPS/HRPC devices (`hrpc_v2`, `hrpc_v3`).

This repo holds only **build outputs** - signed DFU packages, per-release
test specs, and a version manifest. It is published automatically by CI in
the private source repo, [`LISHA-CWU/champs-firmware`](https://github.com/LISHA-CWU/champs-firmware),
on every merge to `main` that bumps `VERSION`. No firmware source code lives
here.

## manifest.json

The registry every update client polls, keyed by board:

```json
{
  "schema": 1,
  "boards": {
    "hrpc_v3": {
      "latest": "0.2.0",
      "versions": {
        "0.2.0": {
          "zip_url": "https://github.com/LISHA-CWU/champs-firmware-releases/releases/download/v0.2.0/dfu_hrpc_v3_v0.2.0.zip",
          "zip_sha256": "...",
          "image_hash": "...",
          "tests_url": "https://github.com/LISHA-CWU/champs-firmware-releases/releases/download/v0.2.0/tests_v0.2.0.json",
          "released_at": "2026-08-24T12:00:00Z",
          "notes": "..."
        }
      }
    }
  }
}
```

Stable URL: `https://raw.githubusercontent.com/LISHA-CWU/champs-firmware-releases/main/manifest.json`
- no authentication required.

## Releases

Each GitHub Release (tagged `vX.Y.Z`) carries three assets:

- `dfu_hrpc_v2_vX.Y.Z.zip`, `dfu_hrpc_v3_vX.Y.Z.zip` - signed, nRF Connect
  Device Manager compatible DFU packages, one per board.
- `tests_vX.Y.Z.json` - the post-update sampling-rate test spec for that
  release.

Anyone can install these directly with Nordic's **nRF Connect Device
Manager** app, independent of any custom client.
