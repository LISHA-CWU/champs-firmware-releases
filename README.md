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

## Installing a release

### Over the air (normal path)

Any device already running an MCUboot image can take a release without a
debug probe:

1. Install Nordic's **nRF Connect Device Manager** app (iOS/Android), or use
   the NexGen Dashboard iOS app which drives the same MCUmgr flow.
2. Download the `dfu_<board>_vX.Y.Z.zip` asset for the board you have (check the device's
   Device Information Service model string first) and get it onto the phone.
3. Stop streaming, make sure the device is charging and above ~50% battery.
4. In Device Manager: connect → **Image** tab → select the zip → **Test and
   Confirm** (or **Test** + **Reset** to keep MCUboot's automatic revert).
5. Verify the running image's hash against `image_hash` in `manifest.json`
   via MCUmgr image state.

### Over SWD (first flash or recovery)

This repo does not publish a merged hex for CHAMPS/HRPC. For a unit with no bootloader, build `merged.hex` from source (see below) and flash it as described in the source repo, or flash it here with the same tools:

- Install the **SEGGER J-Link Software and Documentation Pack**
  (<https://www.segger.com/downloads/jlink/>) and **nRF Connect for
  Desktop** (<https://www.nordicsemi.com/Products/Development-tools/nRF-Connect-for-Desktop>).
- Wire a J-Link (or an nRF52840 DK) to the board's SWDIO, SWDCLK, GND and
  VTref pads with the board powered.
- In nRF Connect for Desktop open **Programmer**, select the J-Link, **Add
  file** the merged hex, and **Erase & write**. Or from a shell:

  ```sh
  nrfutil install device
  nrfutil device program --firmware merged_<board>_vX.Y.Z.hex --options chip_erase_mode=ERASE_ALL
  nrfutil device reset
  ```

## Building from source

Source lives in [`champs-firmware`](https://github.com/LISHA-CWU/champs-firmware). Building it
requires **nRF Connect SDK v3.1.1** and its toolchain:

1. Install [VS Code](https://code.visualstudio.com/) and the **nRF Connect
   for VS Code Extension Pack** (`nordic-semiconductor.nrf-connect-extension-pack`).
2. From the extension's Welcome page, **Install Toolchain → v3.1.1** and
   **Install SDK → v3.1.1** (or `nrfutil sdk-manager toolchain install
   --ncs-version v3.1.1` and `nrfutil sdk-manager install v3.1.1`).
3. Follow the **Getting started**, **Building** and **Flashing** sections of
   that repo's README for the board target (`hrpc_v2/nrf52840`, `hrpc_v3/nrf52840`) and the exact `west` /
   VS Code steps.
