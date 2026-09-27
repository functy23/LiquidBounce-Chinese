<div align="center">
<p>
    <img width="200" src="https://raw.githubusercontent.com/CCBlueX/LiquidCloud/master/LiquidBounce/liquidbounceLogo.svg">
</p>

# 🀄 LiquidBounce 中文汉化版

**An unofficial Chinese localization build of LiquidBounce (水影) — ClickGUI and module names translated, Minecraft 26.3 / Fabric.**

[![LiquidBounce-Chinese](https://img.shields.io/badge/LiquidBounce-Chinese-LBC-orange.svg)](https://github.com/functy23/LiquidBounce-Chinese)
[![Kotlin](https://img.shields.io/badge/Kotlin-2.0%2B-purple.svg?logo=kotlin&logoColor=white)](https://kotlinlang.org/)
[![Top Language](https://img.shields.io/github/languages/top/functy23/LiquidBounce-Chinese?style=flat)](https://github.com/functy23/LiquidBounce-Chinese)
[![Platform](https://img.shields.io/badge/platform-Fabric%20%7C%20Minecraft%2026.3-lightgrey.svg?logo=minecraft&logoColor=white)](https://github.com/functy23/LiquidBounce-Chinese)

[![License: GPL-3.0](https://img.shields.io/badge/License-GPL--3.0-blue.svg?logo=opensourceinitiative&logoColor=white)](https://opensource.org/licenses/GPL-3.0)

[![Release](https://img.shields.io/github/v/release/functy23/LiquidBounce-Chinese?style=flat&logo=github)](https://github.com/functy23/LiquidBounce-Chinese/releases/latest)
[![Downloads](https://img.shields.io/github/downloads/functy23/LiquidBounce-Chinese/total?label=Downloads&logo=github)](https://github.com/functy23/LiquidBounce-Chinese/releases)
[![Stars](https://img.shields.io/github/stars/functy23/LiquidBounce-Chinese?style=flat&logo=github)](https://github.com/functy23/LiquidBounce-Chinese/stargazers)
[![Repo Size](https://img.shields.io/github/repo-size/functy23/LiquidBounce-Chinese?style=flat&logo=github)](https://github.com/functy23/LiquidBounce-Chinese)
[![Contributors](https://img.shields.io/github/contributors/functy23/LiquidBounce-Chinese?color=ee8449&logo=githubsponsors)](https://github.com/functy23/LiquidBounce-Chinese/graphs/contributors)

[Issues](https://github.com/functy23/LiquidBounce-Chinese/issues) • [AGENTS.md](AGENTS.md) • [Releases](https://github.com/functy23/LiquidBounce-Chinese/releases)

**English** | [简体中文](doc/README_zh-CN.md)
</div>

---

## Overview

> A **Chinese-localized build** of [CCBlueX/LiquidBounce](https://github.com/CCBlueX/LiquidBounce) — an unofficial build.
>
> Branch version: `0.40.2` · Built for Minecraft `26.3` (the mod declares `>26.2 <26.4`) · Platform: Fabric · JDK `25`

## ⚠️ Disclaimer

- This repository is a **localization / Chinese-translation branch**; all code copyright belongs to the original author **CCBlueX**, under the **GNU GPL v3.0** license.
- LiquidBounce is a cheat client and is **only recommended for singleplayer or environments where it is allowed**; using it on a server may be detected by anti-cheat and result in a ban — you bear the risk yourself.

## ✨ Changes in This Branch

> Upstream baseline: `CCBlueX/LiquidBounce` commit `f37f07f8` (2026-09-27, `nextgen` branch tip); previous baseline: `adfddc078` (2026-09-23, the `0.40.1` line).

1. **ClickGUI interface localization**
   - Added `src-theme/src/routes/clickgui/localization.ts`: Chinese display mappings for **240 module names + 8 category names** (display layer only; commands and configuration identifiers are unaffected).
     All **241 module names declared by the current upstream are covered (100%)**, with no stale entries: `AutoMobHeal` (added by upstream `284a05a2`) was missing until 0.40.2 and is now translated as well.
     The file is referenced by `Module.svelte`, `Search.svelte` and `Panel.svelte`; module names with no match fall back to the original name.
   - Localized the ClickGUI interface strings — the search box placeholder, the empty states (no module / component found), and the button and hint texts in the settings and HUD editor panels.
     **16 `.svelte` files contain Chinese literals**; a 17th (`Panel.svelte`) only adds a display-layer category mapping call and contains no literal text itself.
   - **Bind action display**: `Toggle` / `Hold` / `Smart` in `SwitchBindAction.svelte` are mapped at the display layer to `切换` / `按住` / `智能` (Switch / Hold / Smart);
     their **stored values remain English**, because those values are persistent enums (`BindAction`) written to the config file — translating them would break the type contract and invalidate already-saved binds.
2. **Language file rebuild** — `src/main/resources/resources/liquidbounce/lang/zh_cn.json`
   - Upstream 0.40.1 rewrote the whole command system (Brigadier DSL) and changed the language key format accordingly: old keys carried explicit `.subcommand.` / `.result.` / `.parameter.` segments, new keys drop them
     (`.friend list result noFriends` → `.friend.list.noFriends`). The previous `zh_cn.json` therefore had **370 orphaned command keys** that could no longer match anything.
   - The 0.40.1 round **removed all 370 orphan keys and added all 382 upstream keys the file did not cover**, and preserved the translation memory (425 values kept the branch's own wording, 222 were re-keyed from the old format, 70 were newly translated — the Add-on and Marketplace/Config surfaces plus a few strings upstream itself left in English — 3 are the branch's own clearer phrasings and 88 come from upstream's `zh_cn.json`).
   - 0.40.2 re-ran the same procedure on the newest upstream: it **added the 15 keys upstream introduced since** (the map-image command, the name-based marketplace commands, local-config tracking) and **kept this branch's own wording wherever upstream `zh_cn.json` reads differently** (13 keys).
     `zh_cn.json` now holds exactly the same **818 keys as `en_us.json`, in the same order** — 100% coverage, `OK zh_cn 0 issue(s)`, and 0 entries left untranslated except 6 that are language-neutral (`ID: %s`, `TPS: %s`, `Guilded`, …).
   - Upstream ships `scripts/verify-i18n.mjs` to check a translation against `en_us.json` (missing keys, extra keys, placeholder-count mismatches). `node scripts/verify-i18n.mjs` reports **`OK zh_cn 0 issue(s)`**.
3. **Note**: setting value names (such as `Scale`, `GridSize`) and identifiers in commands/config files **are intentionally kept in English** — changing them would break commands and config files.
   The same rule applies to the bind action values above, and it is a hard convention of this branch.

## 🚀 Building

Requirements:

- **JDK 25** (Temurin 25+; the project uses a Java 25 toolchain, `jdk = "25"` in `gradle/libs.versions.toml`)
- **Node.js** (with npm, for building the web theme; `src-theme/package.json` declares no `engines` constraint)

```bash
./gradlew build      # full build
./gradlew test       # tests only (394 test cases)
```

Build artifact: `build/libs/liquidbounce-0.40.2.jar`

Notes:

- The build task reads git commit information (`generateGitProperties`), so make sure the directory is a git repository;
- ClickGUI is a web frontend (Vite + Svelte); the first build runs `npm ci` + `npm run build` automatically and requires network access;
- To skip rebuilding the theme, you can change only `src-theme/src` and then run `./gradlew build` again;
- The first build needs to download Minecraft 26.3, mappings and the full dependency graph, which takes a long time (about 17 minutes measured locally);
- The frontend type check `npx svelte-check` (run under `src-theme/`) **currently reports 21 errors in 7 files** (0 warnings):
  18 pre-existing ones under `src-theme/src/routes/hud/elements/**` (`Hud*Settings` types not generated, identical to upstream), plus
  3 in `src-theme/src/routes/menu/common/modal/Tabs.svelte` introduced by upstream itself (that file is byte-identical to upstream).
  Both groups are pre-existing upstream issues and do not affect `vite build`.

## 📦 Installation & Usage

1. Put `liquidbounce.jar` (the release asset; the local build is named `liquidbounce-0.40.2.jar`) into the `mods` folder of your Fabric client (MC 26.3; Fabric API and fabric-language-kotlin are required);
2. In game, run `/client language set zh_cn`, or simply set the game language to Simplified Chinese (AUTO follows it automatically);
3. Press **Right Shift** to open the ClickGUI — categories, module names and interface texts are all in Chinese.

## ❓ FAQ

- **Why are setting names still in English (Scale, GridSize…)?**
  These are config/command identifiers; changing them would break commands and saved configs, so the localized build deliberately keeps them in English.
- **Want to go back to vanilla?**
  Use the official upstream jar directly, or restore the files this branch modified (the change list is in "Changes in This Branch" above).
- **Why did my old command translations stop working after 0.40.1?**
  Upstream rewrote the command system in 0.40.1 and the language key format changed; this branch re-keyed all of them, so simply use the `zh_cn.json` shipped here and do not copy an old one over it.
- **How do I turn off ClientChat (client chat / IRC)?**
  ClickGUI → Settings → ClientChat → turn Enabled off; or run `.value set ClientChat.Enabled false`.

## 📄 License

This project is licensed under the [GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.en.html) (LICENSE file). Copyright belongs to the original author **CCBlueX**; this branch retains all of their copyright notices and only adds Chinese localization.

Upstream project: [CCBlueX/LiquidBounce](https://github.com/CCBlueX/LiquidBounce) · Website: [liquidbounce.net](https://liquidbounce.net)