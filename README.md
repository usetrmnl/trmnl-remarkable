
Readme · MD
# TRMNL for reMarkable

Run a [TRMNL](https://trmnl.com) dashboard on a reMarkable device. The
tablet fetches your dashboard on a schedule, keeps the last one cached for when
the network drops, and sleeps in between.

[![CI](https://github.com/usetrmnl/trmnl-remarkable/actions/workflows/ci.yml/badge.svg)](https://github.com/usetrmnl/trmnl-remarkable/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/license-MIT-222222.svg)](LICENSE)

<p align="center">
  <img src="docs/images/remarkable-trmnl-weather.jpeg" alt="A TRMNL weather dashboard on a reMarkable Paper Pro" width="49%">
  <img src="docs/images/remarkable-trmnl-mondrian.jpeg" alt="The Mondrian generative art plugin on a reMarkable Paper Pro" width="49%">
</p>
This is a community project. It is not affiliated with or supported by
reMarkable, and a reMarkable firmware update can break it.

> [!CAUTION]
> **Enabling Developer Mode factory-resets the device.** Sync or export
> everything you care about first. Developer Mode also lowers the tablet's
> security, and uninstalling this app does not turn it back off. Leaving it
> requires reMarkable's [software recovery](https://support.remarkable.com/s/article/Software-recovery),
> which erases local data again. Read reMarkable's
> [Developer Mode notes](https://support.remarkable.com/s/article/Developer-mode)
> before you start.

## Supported models

I own a Paper Pro and nothing else, so the other models are tested by the
community. Rather than guess, the installer checks the model, architecture, and
firmware, and stops if they don't match.

**Tablet**

| Device | Status |
|---|---|
| reMarkable Paper Pro (`Ferrari`, ARM64) | **Supported.** Developed and tested on this device |
| reMarkable 2 (ARM32) | **Supported since 2.3.0.** Confirmed on hardware in daily use (OS 3.27.3.0); see the note below |
| reMarkable 1 (ARM32) | **Supported since 2.3.0.** Installs, but not yet confirmed on hardware. Testers wanted |
| Any other reMarkable model | Not supported |

A community tester runs the app daily on a reMarkable 2 (OS 3.27.3.0), where it
fetches from the Device API and renders real dashboards on the greyscale panel.
That covers install and everyday refreshing only. The rest of the hardware
checklist in [docs/compatibility.md](docs/compatibility.md) — offline restart
from cache, suspend and resume, reboot, uninstall, and stock recovery — is still
unconfirmed on this model, as is the reMarkable 1 entirely.

The release carries a complete build for each architecture. The installer
identifies the tablet first and sends only the half that matches it.

**reMarkable OS**

| Device | Version | Status |
|---|---|---|
| Paper Pro, reMarkable 2 | 3.26.x, 3.27.x | Supported |
| reMarkable 1 | 3.20.x to 3.27.x | Supported; reMarkable no longer ships releases for this device |
| Any device | Anything older or newer | The installer refuses to run until the version is validated |

**Computer running the installer**

| Platform | Status |
|---|---|
| Windows 10 / 11, x64 | Supported |
| macOS | Not yet. Planned, and the most requested gap [contributions welcome](.github/CONTRIBUTING.md) |
| Linux | Not yet |

**Running it on an rM1 or rM2?** Nothing after the installer's own checks has
been confirmed on that hardware yet. Back the tablet up first, and open an issue
with what you see either way; the installer and **Diagnostics** both print the
model and firmware they detected. Don't force anything past a check that
refuses; that is how tablets get bricked.

If you are on a device or firmware this doesn't cover, TRMNL documents a
[KOReader-based approach](https://help.trmnl.com/en/articles/16206611-trmnl-remarkable)
that works more broadly.

Full details: [compatibility](docs/compatibility.md).

## What you need

| | |
|---|---|
| Tablet | reMarkable Paper Pro, reMarkable 2, or reMarkable 1, on a supported firmware (see Supported models) |
| Computer | Windows 10 or 11, x64, for the installer |
| Connection | USB cable (Wi-Fi also works) |
| TRMNL account | A claimed BYOD device and its Device API key, or your own BYOS server over HTTPS |

## Install

1. On the tablet: enable Developer Mode, complete the reset, and note the SSH
   password it shows you. Charge above 20% and connect the USB cable.
2. Download the release ZIP and `SHA256SUMS.txt`, then check the hash matches:
```powershell
   Get-FileHash .\TRMNL-for-reMarkable-2.4.0-Windows-x64.zip -Algorithm SHA256
```

3. Extract the whole ZIP. Run **TRMNL Installer.exe** with the `payload` folder
   next to it.
4. Leave the address at `10.11.99.1`, paste the SSH password, and click **Find
   my tablet**.
5. Check the model, firmware, and SSH key, then click **Install TRMNL**.
6. On the tablet, open **AppLoad** and tap **TRMNL**.
The installer is code-signed through Microsoft Trusted Signing, so Windows
should not report an unknown publisher. SmartScreen may still show a caution
until a release builds download reputation. Verify the checksum and read
[code signing](docs/code-signing.md) either way.

Your SSH password stays in the browser tab and the local installer process. It
is not written to disk or logs, and the installer only listens on `127.0.0.1`.
Longer instructions: [install guide](docs/install.md).

## Connect it to TRMNL

Use these steps to connect the app to the hosted TRMNL cloud. If you run a fully
self-hosted BYOS server, the hosted BYOD license and TRMNL Device API key are not
required. Choose the custom server option and enter its HTTPS origin and device
identity instead. Plain HTTP is only accepted for a loopback mock on the tablet.

### 1. Obtain and claim a BYOD license

The hosted TRMNL service requires one BYOD license per third-party device.

**[Buy TRMNL, BYOD $10 off with `paperpro10`](https://trmnl.com/?ref=paperpro10)**

The link applies the discount automatically. If you would rather enter it
yourself, the code is `paperpro10` at checkout.

> **Disclosure:** that is a referral link, set up with TRMNL. It takes $10 off
> your license and pays this project a referral amount. The installer and the app
> are free and MIT licensed regardless, and a fully self-hosted BYOS server needs
> no license at all, see the custom server option below.

After purchase, sign in to TRMNL and use the order number to claim or add the
BYOD device. TRMNL's help center explains the claim and Friendly ID flow:
[Find your Friendly ID](https://help.trmnl.com/en/articles/12632379-find-your-friendly-id).

### 2. Configure the device model

In the TRMNL device settings, choose or create the closest custom model. The
tablet's panel decides the values:

| Tablet | Resolution | Color capability |
|---|---|---|
| Paper Pro | **1620 x 2160** | **full color / 16.7M** when the model editor offers it |
| reMarkable 2 and 1 | **1404 x 1872** | **grayscale**; the panel shows 16 greys |

Orientation is **portrait** and the image format is **PNG** on every model.

Use fit mode in the tablet app if a plugin sends a different aspect ratio. The
Paper Pro can display color, but a plugin or template must also render color.
On the reMarkable 1 and 2, turn on **Smooth gradients** in Settings if a colour
dashboard arrives flat; it converts to the panel's greys by diffusing the error
rather than dropping it. The app reports the panel it detected in Settings and
in Diagnostics.

### 3. Get the correct Device API key

1. Sign in at [trmnl.com](https://trmnl.com/).
2. Open **Devices** and select or edit the claimed BYOD device.
3. Open **Developer Perks**. The label may be under a gear or edit view.
4. Copy that device's **Device API Key**.
Do not use the Account API token from account settings. Account tokens begin
with `user_`, use bearer authentication, and are for a different API. This app
needs the device-scoped key used as the `access-token` header. Treat it as a
password and never paste it into an issue, screenshot, log, or diagnostics post.

### 4. Enter it on the tablet

1. Open TRMNL in AppLoad and tap the upper-right corner of the screen.
2. Choose **Settings** and leave the server on **TRMNL cloud**.
3. Paste the Device API Key.
4. Confirm the pre-filled **Device ID / MAC address** matches the Wi-Fi MAC in
   the TRMNL BYOD device record. Correct it if the account uses another value.
5. Tap **Test connection**, then **Save**.
The key is stored at `/home/root/.config/trmnl-remarkable/config.json` with mode
`0600`. It is masked in the UI after save and redacted from diagnostics.

### 5. Confirm operation

Tap **Next screen** and confirm a playlist image appears. In diagnostics, check
that the last refresh succeeded and that the next refresh time is present.

An HTTP 401 or 403 usually means the key is wrong or the device is unclaimed. A
`user_...` value is definitely the wrong key. An HTTP 429 means the service is
rate limiting requests. The client honors the server's `Retry-After` value
instead of repeatedly requesting.

## Using it

| What | Where |
|---|---|
| Controls and Settings | Tap the upper-right corner |
| Refresh, next screen, previous screen | Buttons in the controls panel |
| Front light (Paper Pro only) | Slider, or follow the system brightness |
| Weekly front-light schedule (Paper Pro only) | **Brightness schedule** in the controls panel; drag over Monday-first half-hour cells |
| Refresh history | **History** in the controls panel |
| Diagnostics | **Diagnostics** (secrets are redacted) |
| Back to reMarkable | AppLoad's downward swipe from centre-top, the on-screen button, or hold the upper-left corner for two seconds |

Opening and closing an overlay triggers a local e-ink cleanup. It does not spend
a TRMNL API request.

## Battery

Battery life comes down to how often the tablet wakes, how much it uses Wi-Fi,
and the front light where there is one. A static e-ink image costs nothing to
keep on screen.

On a Paper Pro with the front light off, the recommended settings below, and a
dashboard refreshing on the usual server interval, that works out to roughly
nine days on a charge. Your own figure will differ with refresh rate, Wi-Fi
quality, and front-light use; measure it with the built-in battery test rather
than assuming this one.

Recommended: leave reMarkable's **Auto-sleep** and **Light sleep** on, turn
**Auto power-off** off, keep TRMNL's **Wake for refresh** on, and use the
longest refresh interval you can live with.

Three settings help further:

- **Refresh less often below 20% battery** stretches the interval up to 4× (max
  6 hours) once the battery is low and the charger is unplugged.
- **Pause scheduled refreshes overnight** stops wakeups inside a window you set.
  The dashboard stays on screen; manual refresh still works.
- **Smooth gradients** dithers images before display, which removes the banding
  that dashboards drawn for bright screens produce. It targets the panel that is
  present: the Paper Pro's colours, or the 16 greys of the reMarkable 1 and 2.
  Text and flat colour are left alone.
For a real measurement, charge to full, unplug, start the battery test in
Settings, and let it discharge at least 10%. Plugging in invalidates the
estimate rather than skewing it quietly.

## Removing it

Rebooting the tablet always returns to the stock reMarkable interface. Run the
installer again, find your tablet, and pick an action:

| Action | What it does |
|---|---|
| **Reactivate after reboot** | Starts XOVI/AppLoad again without reinstalling |
| **Restore stock interface** | Stops the runtime injection, keeps your data |
| **Uninstall TRMNL** | Removes the app, keeps settings and cache |
| **Uninstall and erase data** | Also removes settings, cache, history, and logs |

None of these turn off Developer Mode. TRMNL only writes under `/home/root` and
never touches your notebooks, documents, or boot partitions. See
[privacy](docs/privacy.md) for the exact paths.

## If something goes wrong

| Problem | Try |
|---|---|
| Tablet not found | Wake and unlock it, plug in directly without a hub, confirm Developer Mode, copy the SSH password again |
| Address won't connect | Use `10.11.99.1` over USB, or the tablet's local IP over Wi-Fi |
| SSH key changed | Don't click past it. Reconnect over USB and confirm it is your tablet before accepting |
| HTTP 401 or 403 | Use the Device API Key from a claimed BYOD device, not a `user_...` token |
| HTTP 429 | Wait; the app honours `Retry-After` |
| Dashboard is monochrome | On a Paper Pro, check the plugin renders colour; the tablet can't add colour that isn't in the image. The reMarkable 1 and 2 have no colour panel |
| Colour dashboard looks flat on an rM1 or rM2 | Turn on **Smooth gradients** in Settings so colours are diffused into the panel's greys |
| Gone after reboot | Use **Reactivate after reboot**. Returning to stock on reboot is deliberate |
| Battery drains fast | Longer refresh interval, lower front light, and the battery settings above |

Read [support](.github/SUPPORT.md) before opening an issue, and never paste an API key,
SSH password, or unredacted diagnostic into one.

## Security

The installer binds to localhost only, checks the model, firmware, architecture,
free space, SSH key, payload paths, and checksums before it changes anything,
and remembers the tablet's SSH key so a change is caught on later runs. Your API
key is stored in an owner-only `0600` file, masked in the UI, kept out of logs,
and never sent back to the interface after saving. Remote servers and images
must use HTTPS, credential-bearing cross-origin redirects are refused, and
downloaded images are size- and dimension-checked before decoding. The app
ignores firmware and reset directives from the server.

Report vulnerabilities privately. See [SECURITY.md](.github/SECURITY.md), not a public
issue.

## Documentation

| Topic | |
|---|---|
| Install walkthrough | [docs/install.md](docs/install.md) |
| Supported devices and firmware | [docs/compatibility.md](docs/compatibility.md) |
| Privacy and stored data | [docs/privacy.md](docs/privacy.md) |
| Windows code signing | [docs/code-signing.md](docs/code-signing.md) |
| Release process | [docs/releasing.md](docs/releasing.md) |
| Validation records | [docs/validation/](docs/validation/) |
| Changes | [CHANGELOG.md](CHANGELOG.md) |
| Third-party components | [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md) |

## Building

Needs Go 1.25.12+, Qt 6.8.2 (`rcc` and `qmllint`), PowerShell 5.1+, Python 3,
and ShellCheck.

```powershell
./scripts/build.ps1
./scripts/build-release.ps1 -Version 2.4.0
```

`build.ps1` runs formatting, tests, `go vet`, both device cross-builds (ARM64
for the Paper Pro and ARMv7 for the reMarkable 1 and 2), QML resource
compilation, and bundle validation. `build-release.ps1` also verifies the
pinned runtime hashes for both architectures, builds the installer, and writes
the release ZIP, SBOM, and checksums under `release/`.

Layout:

- `app/ui/TRMNL.qml`: the AppLoad frontend
- `backend/cmd/trmnl-remarkable`: the device backend (Device API, scheduling, rendering), built for ARM64 and ARMv7
- `installer/`: the localhost Windows installer
- `device/install.sh`, `device/uninstall.sh`, `device/recover-stock.sh`: on-device operations
## Contributing and licence

Issues and pull requests are welcome. Start with
[CONTRIBUTING.md](.github/CONTRIBUTING.md) and the
[code of conduct](.github/CODE_OF_CONDUCT.md).

The project is MIT licensed. The installer also redistributes checksum-pinned
XOVI and AppLoad components under their own LGPL/GPL terms; see
[THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md).
