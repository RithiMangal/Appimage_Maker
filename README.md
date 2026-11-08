<div align="center">

# 📦 AppImage Maker

### *It Makes Any Executables To Appimage — In Offline.*

[![Rust](https://img.shields.io/badge/Rust-1.75%2B-orange?style=flat-square)](https://www.rust-lang.org)
[![GUI](https://img.shields.io/badge/GUI-egui%20%2F%20eframe-0.29-9b59b6?style=flat-square)](https://www.egui.rs)
[![Platform](https://img.shields.io/badge/Platform-Linux%20x86_64-0366d6?style=flat-square)](#requirements)
[![Offline](https://img.shields.io/badge/100%25-Offline-green?style=flat-square)](#features)
[![License](https://img.shields.io/badge/License-MIT-blue?style=flat-square)](#license)

</div>
• Strategic Recommendation: Frames Lubuntu as the primary native development environment for guaranteed stability.
• Technical Breakdown: Bullet points highlighting the streamlined configuration, low resource footprint, and its ability to revive older hardware with zero lag.
• Veteran Insight: Concludes with an engaging nod to the fun of managing multiple Linux environments.
---


## 📖 Overview

**AppImage Maker** is a lightweight, fully offline desktop application written in **Rust** that turns any executable or binary into a portable **AppImage** — with maximum compression, built-in security auditing, and zero internet dependency.

It ships with `appimagetool` bundled right inside the application, so you can produce AppImages on any Linux machine, in any environment, without installing anything extra. Just point it at an executable, give it a name, and hit **Create**.

> **Design goal:** everything you need, nothing you don't — a fast, native GUI that feels right at home on any Linux desktop (X11 & Wayland, Plasma included).

---

## ✨ Features

| Category | Details |
|---|---|
| 🎨 **Dark neon UI** | Deep-space dark palette with luminous neon glow title & subtitle, metallic 3D-style buttons |
| 🪟 **Custom window controls** | Minimize (`-`), Maximize/Restore (`□`), Close (`●`) — top-left, fully functional |
| 🖱️ **Smooth title-bar drag** | Native window-manager dragging (flicker-free, pointer-safe, stays on-screen) |
| 🔒 **Security audit** | Runs `cargo audit` before every build; aborts on detected vulnerabilities |
| 🗜️ **Maximum compression** | SquashFS `-comp xz -Xbcj x86 -b 1024k -no-xattrs` — minimal footprint |
| 📴 **100% offline** | `appimagetool` bundled in `bin/` — no downloads, no network calls |
| 🖼️ **Custom icon support** | Optional `.png` / `.svg` / `.xpm` app icon (falls back to the built-in neon icon) |
| 🌐 **Contact link** | Top-right **Contact** button opens the author's GitHub repositories |
| 🐧 **Broad compatibility** | x86_64 Linux, all major distros, KDE Plasma, X11 & Wayland, UEFI/EFI-safe packaging |
| ⚡ **Lean binary** | Release build (`opt-level = 3`, thin LTO, stripped) — the app itself is only ~8 MB |

---

## 🖥️ The Interface

```
┌──────────────────────────────────────────────────────────────────────┐
│ [−] [□] [●]   Appimage Maker                        [  Contact  ]  │
│               It Makes Any Executables To Appimage In Offline        │
├──────────────────────────────────────────────────────────────────────┤
│  Executable File / Binary:                                           │
│  [/path/to/your/binary                            ] [ Browse... ]    │
│                                                                      │
│  App Name:                                                           │
│  [ MyApp                                                 ]           │
│                                                                      │
│  App Icon (.png - Optional):                                         │
│  [/path/to/icon.png                               ] [ Browse... ]    │
│                                                                      │
│  Output Directory:                                                   │
│  [/home/user/Desktop                              ] [ Browse... ]    │
│                                                                      │
│  [          Create AppImage (Max Compression)          ]             │
│                                                                      │
│  Status / Log:                                                       │
│  Ready. Select executable and build offline AppImage.                │
└──────────────────────────────────────────────────────────────────────┘
```

### Window controls (top-left)

| Button | Color | Action |
|---|---|---|
| `−` | Yellow glow | Minimize to taskbar |
| `□` | Green glow | Maximize / Restore |
| `●` | Red glow | Close application |

> **Tip:** drag the window by the title bar — it follows your mouse smoothly via the native desktop drag.

---

## 🚀 Quick Start

### Option A — Run the prebuilt AppImage

```bash
chmod +x Appimage_Maker-x86_64.AppImage
./Appimage_Maker-x86_64.AppImage
```

That's it. The app is fully self-contained (bundled `appimagetool` included).

### Option B — Build from source

**Requirements:** Rust toolchain (Cargo), which is all you need — everything else is vendored/bundled.

```bash
cd appimage_maker

# 1. Build the release binary
cargo build --release

# 2. Stage the AppDir
rm -rf AppDir && mkdir -p AppDir/usr/bin AppDir/bin AppDir/assets
cp target/release/appimage_maker AppDir/usr/bin/
cp bin/appimagetool AppDir/bin/
cp assets/appimage_maker.png AppDir/appimage_maker.png
cp assets/appimage_maker.png AppDir/.DirIcon

# 3. Add desktop entry + AppRun launcher
echo '[Desktop Entry]'        >  AppDir/.desktop
echo 'Type=Application'       >> AppDir/.desktop
echo 'Name=AppImage Maker'    >> AppDir/.desktop
echo 'Exec=appimage_maker'    >> AppDir/.desktop
echo 'Icon=appimage_maker'    >> AppDir/.desktop
echo 'Categories=Utility;'    >> AppDir/.desktop
printf '#!/bin/sh\nHERE="$(dirname "$(readlink -f "$0")")"\nexec "$HERE/usr/bin/appimage_maker" "$@"\n' > AppDir/AppRun
chmod +x AppDir/usr/bin/appimage_maker AppDir/bin/appimagetool AppDir/AppRun

# 4. Package the AppImage (maximum compression)
MKSQUASHFS_OPTS="-comp xz -Xbcj x86 -b 1024k -no-xattrs" \
  ./bin/appimagetool AppDir ../Appimage_Maker-x86_64.AppImage
```

---

## 🛠️ Usage

1. **Executable File / Binary** — click *Browse...* and pick the binary you want to package. The **App Name** is auto-filled from the file name.
2. **App Name** — the name used for the `.desktop` entry, icon, and output file.
3. **App Icon (.png - Optional)** — pick a custom icon, or leave it blank to use the built-in neon AppImage Maker icon.
4. **Output Directory** — defaults to `~/Desktop`. Pick any folder.
5. Hit **Create AppImage (Max Compression)**.

The status log reports progress in real time:

```
Running security checks (cargo audit)...
Cargo audit passed. Packaging AppImage offline...
Success! AppImage created at: /home/user/Desktop/MyApp-x86_64.AppImage (2.41 MB)
```

---

## ⚙️ How It Works

AppImage Maker assembles a standard **AppDir** and hands it to the bundled `appimagetool`:

```
┌──────────────────────────────┐
│ 1. Cargo audit (security)    │  cargo audit — abort on vulnerabilities
└──────────────┬───────────────┘
               ▼
┌──────────────────────────────┐
│ 2. Stage AppDir              │  /usr/bin/<app> · AppRun · .desktop ·
│                              │  <app>.png · .DirIcon
└──────────────┬───────────────┘
               ▼
┌──────────────────────────────┐
│ 3. appimagetool (bundled)    │  MKSQUASHFS_OPTS="-comp xz -Xbcj x86
│                              │    -b 1024k -no-xattrs"  (max compression)
└──────────────┬───────────────┘
               ▼
   <AppName>-x86_64.AppImage   (portable, offline, no install needed)
```

### Security — `cargo audit` gating

Before packaging, the app runs **`cargo audit`** against the current project. If vulnerabilities are found, the build **aborts immediately** before any AppImage is produced — so you never ship a compromised tool. If `cargo audit` isn't installed, the app warns and proceeds (fully offline mode is never blocked).

### Compression — maximum, every time

SquashFS is invoked with aggressive settings:

```
MKSQUASHFS_OPTS="-comp xz -Xbcj x86 -b 1024k -no-xattrs"
```

- `-comp xz` — LZMA-based XZ compression (best ratio)
- `-Xbcj x86` — BCJ filter tuned for x86 binaries
- `-b 1024k` — 1 MB block size (better ratio for large files)
- `-no-xattrs` — strips extended attributes (smaller, cleaner image)

### `appimagetool` resolution (offline-first)

The app locates the bundled tool in this order, so it works both as a standalone binary and as an AppImage:

1. `$APPDIR/bin/appimagetool` (when running from an extracted AppImage)
2. Relative to the current executable (`bin/appimagetool`, parent dir)
3. Working-directory `bin/appimagetool`
4. `./bin/appimagetool` fallback

---

## 📁 Project Structure

```
appimage_maker/
├── Cargo.toml                 # Package manifest (Rust 2021, release optimizations)
├── Cargo.lock
├── src/
│   └── main.rs                # Full application (UI, build pipeline, audit, packaging)
├── bin/
│   └── appimagetool           # Bundled AppImage builder (8.5 MB, statically compiled)
├── assets/
│   └── appimage_maker.png     # Built-in neon AppImage Maker icon
├── AppDir/                    # AppImage staging tree (AppRun, .desktop, usr/bin, bin/)
└── target/                    # Cargo build output
```

---

## 🧰 Tech Stack

| Layer | Choice | Why |
|---|---|---|
| Language | **Rust** (edition 2021) | Speed, safety, static binaries |
| GUI | **eframe / egui 0.29** | Immediate-mode, GPU-accelerated, native look & feel |
| File dialogs | **rfd 0.14** | Native OS dialogs (X11/Wayland safe) |
| Browser | **webbrowser 1.2** | Cross-platform `xdg-open` for the Contact link |
| Images | **image 0.25** (PNG only) | Lean icon pipeline |
| Packaging | **appimagetool** (bundled) + SquashFS | Industry-standard AppImage toolchain |

**Release profile:** `opt-level = 3`, thin LTO, single codegen unit, stripped symbols, `panic = "abort"` — small and fast.

---

## ❓ FAQ / Troubleshooting

**Q: The app says `Bundled appimagetool not found in bin/!` — why?**
Run the binary from the project root or from inside the AppImage (so `bin/appimagetool` is reachable). The packaged AppImage always includes it.

**Q: Do I need internet to create an AppImage?**
No. Everything runs locally: `cargo audit` reads the local lockfile and `appimagetool` is bundled. 100% offline.

**Q: My executable is huge — will the AppImage be small?**
With `-comp xz -Xbcj x86` you get the best available SquashFS ratio. The final size mostly depends on your binary and its shared-library dependencies.

**Q: Can I use it on KDE Plasma / Wayland?**
Yes. The GUI is built on `eframe` with both X11 and Wayland backends, and the AppImage is a standard, desktop-agnostic artifact.

**Q: `cargo audit` isn't installed — is that a problem?**
No. The app warns and continues; the audit is a safety gate, not a hard dependency.

---

## 🤝 Contributing

Issues and pull requests are welcome! If you're improving the build pipeline, the UI, or icon handling, keep the offline-first philosophy intact.

---

## 📄 License

Released under the **MIT License**.

---

<div align="center">

Built with Rust 🦀 · Package with AppImage · Works 100% offline

**[Contact the author →](https://github.com/RithiMangal?tab=repositories)**

</div>
