# kms-hdr-panel

libcosmic settings panel for [kms-hdr](https://github.com/Sigmachan/kms-hdr) — HDR colour pipeline control and NVIDIA gaming configuration for Linux.

---

## What it does

Provides a GUI for everything `kms-hdr` exposes, with full hardware auto-detection on launch:

- **EDID badges** — HDR10, HLG, HDR10+, Dolby Vision, BT.2020, DCI-P3, DSC, HDMI-CEC, OLED — read directly from `/sys/class/drm/*/edid`
- **GPU badge** — auto-detects AMD / Intel / NVIDIA and labels which pipeline features are available
- **HDR toggle** — enables or resets via `pkexec kms-hdr` (no password prompt, polkit `allow_active = yes`)
- **SDR white point** and **display peak nits** sliders
- **Colour gamut** — BT.2020 / DCI-P3 D65 / sRGB via 3×3 CTM (AMD/Intel only; silently skipped on NVIDIA)
- **Color Intensity** — BT.709 saturation matrix (AMD/Intel only)
- **Output bit depth** — 8 / 10 / 12 bpc
- **OLED Care** _(shown only when OLED detected)_
  - Longevity Preset: SDR 150 nit / peak 600 nit
  - Auto-dim slider: writes a `swayidle` systemd user service that dims to 50 nit after N minutes idle
  - Pixel shift note (handled by the compositor)
- **NVIDIA Gaming** _(shown only when NVIDIA GPU detected)_
  - RTX Smooth Motion toggle (`NVPRESENT_ENABLE_SMOOTH_MOTION`, driver 575+)
  - Reflex toggle (`PROTON_ENABLE_NVAPI` + `DXVK_ENABLE_NVAPI`)
  - DLDSR toggle (`__GL_DLDSR_MULTIPLIER=2.25`)
  - Digital Vibrance slider (nvibrant ioctl, −1024 to 1023)
  - Gamescope output resolution dropdown (1080p–8K)
  - Upscaling mode (FSR / NIS / integer / nearest)
  - Frame rate cap slider
  - Saves to `/etc/hdr-game.conf` — read by the `hdr-game` launcher
- **HDR Calibration** — opens `hdr-cal.py` interactive overlay + 8 full-screen test patterns

All privilege-requiring operations run through `pkexec` — no daemon, no persistent root process.

---

## Dependencies

| Package | Purpose |
|---------|---------|
| `kms-hdr` | Required — provides the `kms-hdr` binary and polkit policy |
| `libxkbcommon`, `wayland` | libcosmic runtime |
| `swayidle` | Optional — OLED auto-dim |
| `python-gobject` | Optional — calibration overlay (`hdr-cal.py`) |
| `nvibrant` | Optional — digital vibrance on NVIDIA |
| `gamescope` | Optional — NVIDIA HDR gaming (`hdr-game`) |

---

## Build

Requires a working [libcosmic](https://github.com/pop-os/libcosmic) checkout and Rust nightly.

```bash
git clone https://github.com/Sigmachan/kms-hdr-panel
cd kms-hdr-panel
cargo build --release
sudo install -Dm755 target/release/kms-hdr-panel /usr/local/bin/kms-hdr-panel
```

### Arch Linux

Build via the PKGBUILD in the `kms-hdr` repo (builds both packages in one `makepkg -si`):

```bash
git clone https://github.com/Sigmachan/kms-hdr
cd kms-hdr
makepkg -si
```

---

## Launch

```bash
kms-hdr-panel
```

Or integrate into COSMIC Settings via the `Sigmachan/cosmic-settings` fork — HDR appears as a section inside the Display page.

---

## Config files

| File | Managed by |
|------|-----------|
| `/etc/kms-hdr.conf` | HDR pipeline settings (SDR nits, peak nits, gamut, BPC, saturation, OLED dim timeout) |
| `/etc/hdr-game.conf` | NVIDIA gaming settings (Smooth Motion, Reflex, vibrance, Gamescope resolution/fps/upscale) |
| `~/.config/systemd/user/kms-hdr-dim.service` | OLED auto-dim swayidle unit (written by the panel) |

---

## License

GPL-3.0-only.

Maintainer: Kira Keller \<senedato@gmail.com\>
