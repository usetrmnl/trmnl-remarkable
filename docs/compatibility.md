# Compatibility

| Component | Status |
|---|---|
| reMarkable Paper Pro (`reMarkable Ferrari`, aarch64) | Supported; developed and validated on this device |
| reMarkable 2 (`reMarkable 2.0`, armv7l) | Supported; install and everyday Device API refreshing exercised on hardware (OS 3.27.3.0). The full checklist below is not complete for this model |
| reMarkable 1 (`reMarkable 1.0` or `reMarkable Prototype 1`, armv7l) | Supported; not yet exercised on hardware |
| Other reMarkable models (Paper Pro Move, Paper Pure) | Blocked |
| reMarkable OS 3.27.x | Supported; 3.27.3.0 exercised on Paper Pro hardware |
| reMarkable OS 3.26.x | Allowed by the installer; not exercised on hardware |
| reMarkable OS 3.20.x–3.25.x | Allowed on the reMarkable 1 only, which no longer receives releases |
| OS 3.28 and later, or below the windows above | Blocked until validated |
| XOVI 0.3.3 (aarch64 and arm32) | Pinned |
| rm-xovi-extensions release 19 (aarch64 and arm32) | Pinned |
| AppLoad 0.5.3 (aarch64 and arm32) | Pinned |
| Windows 10/11 x64 | Installer platform |

## Firmware windows

The window is per device because the devices are not on the same release line:

| Device | Accepted firmware | Why |
|---|---|---|
| Paper Pro | 3.26.x, 3.27.x | The range validated against pinned XOVI/AppLoad |
| reMarkable 2 | 3.26.x, 3.27.x | Same release line as the Paper Pro |
| reMarkable 1 | 3.20.x – 3.27.x | reMarkable stopped shipping releases for it; a window ending at 3.26 would exclude every rM1 in use |

## Panels

| Device | Resolution | Colour | Front light |
|---|---|---|---|
| Paper Pro | 1620 × 2160 | Yes | Yes |
| reMarkable 2 | 1404 × 1872 | No, 16 greys | No |
| reMarkable 1 | 1404 × 1872 | No, 16 greys | No |

The app reads `/sys/devices/soc0/machine` to decide, falling back to
`/proc/device-tree/model`. Front-light controls are hidden where there is no
front light, and dithering targets the panel that is present.

Do not bypass the model or firmware checks. reMarkable updates can change
private APIs, framebuffer behaviour, runtime compatibility, or SSH access.
Before updating the tablet, return to the stock interface, check this page and
the open issues, and keep a recovery path available.

Adding a firmware version to this list requires all of the following on physical
hardware, against the exact release archive: clean install, launch, a real
Device API refresh, cache and offline restart, overlay cleanup, front-light
restore where the device has one, suspend and resume, reboot to stock,
reactivation, uninstall, purge, and stock recovery. Record the model, the OS
build, and the archive's SHA-256.
