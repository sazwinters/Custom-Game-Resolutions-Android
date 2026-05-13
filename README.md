# Custom Game Resolutions by saz

**Automatically apply custom downscale factors to hundreds of games – improve performance, reduce heat, and extend battery life on any Android 12+ device.**

---

## 📋 Overview

This Magisk module silently sets **optimized resolution downscaling** for over **300 popular games** directly via Android’s built-in Game Manager API. No third‑party apps or per‑game configuration required. Once installed, your games will render at a lower internal resolution, lowering GPU load while keeping the UI crisp.

**Designed for low‑end and mid‑range devices**, but applicable to any device running Android 12 or later.

**Key benefits:**
- Higher and more stable frame rates
- Less heat and thermal throttling
- Longer battery life during gaming sessions
- Works even on ROMs without a full Game Manager implementation

---

## 🔧 How It Works

The module uses two native Android mechanisms (both introduced in Android 12):

1. **`device_config put game_overlay`** – writes persistent per‑game downscale factors for all three performance modes (Standard, Performance, Battery).  
   *Even if your ROM lacks a Game Manager service, these settings are stored and respected by the system.*  
   See the official Android documentation on game mode interventions:  
   [developer.android.com/games/optimize/adpf/gamemode/gamemode-interventions](https://developer.android.com/games/optimize/adpf/gamemode/gamemode-interventions)

2. **`cmd game mode performance`** – activates Performance mode for each game, which may additionally boost CPU/GPU/touch responsiveness.  
   *If this command is unavailable (custom ROMs, etc.), the overlays still work – you only miss the mode switch.*

**Execution flow:**
- On every boot, a `service.sh` script runs 15 seconds after boot completes.
- **Phase 1:** Applies the downscale factors to all 300+ games via `device_config`.
- **Phase 2:** Attempts to set each game to Performance mode (skipped if unsupported).
- All actions are logged to `/data/adb/modules/custom_game_resolutions/custom_game_resolutions.log`.

---

## 📊 Downscale Tiers

All games are grouped into three **universal tiers** – the same factor is applied regardless of the in‑game performance mode selected by the user. This ensures a consistent visual experience.

| Tier | Downscale Factor | Use Case |
|------|------------------|----------|
| Heavy | 0.60 | Extremely demanding 3D titles (Warzone, Fortnite, Genshin Impact) |
| Medium | 0.70 | Most 3D games (PUBG, COD Mobile, Mobile Legends, Honkai: Star Rail) |
| Light | 0.80 | 2D / less demanding games (Hearthstone, Stardew Valley, Clash Royale) |

A full list of supported games with their assigned tier is available in the `service.sh` file inside the module.

---

## ✅ Requirements

- **Android 12 or higher** (API 31+)
- **Root access** with Magisk, KernelSU, or APatch
- No additional apps or tweaks needed

---

## 📦 Installation

1. Download the latest module zip from the [Releases](https://github.com/sazwinters/Custom-Game-Resolutions/releases) page.
2. Open your root manager (Magisk / KernelSU / APatch) and go to the Modules section.
3. Tap **Install from storage** and select the zip.
4. Reboot your device.

After reboot, the module will automatically apply the downscaling on every boot. You can verify by checking the log file:

```bash
cat /data/adb/modules/custom_game_resolutions/custom_game_resolutions.log
```

---

## 🔗 Perfect Pairings

- **[AsoulOpt](https://github.com/nakixii/Magisk_AsoulOpt)** – The game list in this module is **synced** with AsoulOpt.
- **[Uperf-Game-Turbo](https://github.com/yinwanxi/Uperf-Game-Turbo)** – Goated module.

---

## 🛠 Customization

You can freely edit the game list and tiers to suit your preferences:

1. Open the module’s `service.sh` file (located at `/data/adb/modules/custom_game_resolutions/service.sh`).
2. Look for the `GAMES_060`, `GAMES_070`, and `GAMES_080` variables.
3. Add, remove, or change `package.name|factor` entries.
4. Reboot for changes to take effect.

Example – adding a new game to the medium tier:
```bash
com.my.favorite.game|0.70
```

---

## 🗑️ Uninstallation

Disabling or removing the module will leave the downscale settings in place, but they will no longer be re‑applied on boot. To fully clean all applied overlays, flash the module zip again and choose “Uninstall,” or manually run the uninstall script (which also runs automatically when uninstalling via Magisk).  
Alternatively, you can clear individual overlays with:

```bash
device_config delete game_overlay <package.name>
```

---

## ❓ FAQ

**Does this override my in‑game graphics settings?**  
No. Downscaling happens at the system level *after* the game renders its frame. Your in‑game settings (quality, resolution, FPS) remain unchanged; the final output is simply scaled down before display. This means you still benefit from all the other performance boosts of Performance mode.

**Will it work on my non‑Pixel/custom ROM?**  
Yes. The `device_config` settings are a core Android feature and work on any device running Android 12+. Even if `cmd game` is missing, the overlays still apply. Tested on **Infinix Hot 11s NFC (Helio G88)** but should work on all Android 12+ devices.

**Why does the module apply the same factor for all modes?**  
Because users often switch between Standard/Performance/Battery modes, and keeping the same downscale factor avoids sudden visual quality jumps when toggling. The core goal – reduced GPU load – is achieved in every mode.

---

## 🧪 Design Rationale

- **No runtime daemon** – all settings are written once per boot, no background services or battery drain.
- **Single script** – easy to audit, modify, or troubleshoot.
- **Log rotation** – prevents log spam (max 64 KB, old logs rotated).
- **Universal downscale** – unlike per‑mode tweaking, this guarantees a consistent sharpness/fps tradeoff.
- **Fallback safe** – gracefully handles missing `cmd game` (Phase 2) without any errors.

---

## 📄 License

This project is licensed under the MIT License – see the [LICENSE](LICENSE) file for details.

---

## 👤 Credits

- **saz** – creator
- The Android Game Manager API and the `device_config` subsystem
- The Magisk community for inspiration

---

**If you find this module useful, please consider giving it a ⭐ on GitHub!**
