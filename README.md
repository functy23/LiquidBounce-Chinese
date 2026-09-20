<div align="center">
<p>
    <img width="200" src="https://raw.githubusercontent.com/CCBlueX/LiquidCloud/master/LiquidBounce/liquidbounceLogo.svg">
</p>

# 🀄 LiquidBounce 中文汉化版

**An unofficial Chinese localization build of LiquidBounce (水影) — ClickGUI and module names translated, Minecraft 26.2 / Fabric.**

[![LiquidBounce-Chinese](https://img.shields.io/badge/LiquidBounce-Chinese-LBC-orange.svg)](https://github.com/functy23/LiquidBounce-Chinese)
[![Kotlin](https://img.shields.io/badge/Kotlin-2.0%2B-purple.svg?logo=kotlin&logoColor=white)](https://kotlinlang.org/)
[![Top Language](https://img.shields.io/github/languages/top/functy23/LiquidBounce-Chinese?style=flat)](https://github.com/functy23/LiquidBounce-Chinese)
[![Platform](https://img.shields.io/badge/platform-Fabric%20%7C%20Minecraft%2026.2-lightgrey.svg?logo=minecraft&logoColor=white)](https://github.com/functy23/LiquidBounce-Chinese)

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

> A **Chinese-localized fork** of [CCBlueX/LiquidBounce](https://github.com/CCBlueX/LiquidBounce) — an unofficial build.
>
> Branch version: `0.39.1` · Supports Minecraft `26.2` · Platform: Fabric

## ⚠️ Disclaimer

- This repository is a **localization / Chinese-translation branch**; all code copyright belongs to the original author **CCBlueX**, under the **GNU GPL v3.0** license.
- LiquidBounce is a cheat client and is **only recommended for singleplayer or environments where it is allowed**; using it on a server may be detected by anti-cheat and result in a ban — you bear the risk yourself.

## ✨ Changes in This Branch

> Upstream baseline: `CCBlueX/LiquidBounce` commit `8fc1f12b` (2026-08-09, `mod_version=0.39.1`).
> This repository is a single-commit localization derivative and **is not a GitHub fork**; it has no common ancestor with upstream, so it cannot be synced with a plain `git merge`.

1. **ClickGUI interface localization**
   - Added `src-theme/src/routes/clickgui/localization.ts`: Chinese display mappings for **234 module names + 8 category names** (display layer only; commands and configuration identifiers are unaffected).
     232 of them cover every module registered by `ModuleManager` (100% coverage); the remaining 2 are legacy upstream entries.
     The file is referenced by `Module.svelte`, `Search.svelte` and `Panel.svelte`; module names with no match fall back to the original name.
   - Localized the ClickGUI interface strings: the search box placeholder, the empty states (no module / component found), and the button and hint texts in the settings and HUD editor panels — 16 `.svelte` files in total.
   - **Bind action display**: `Toggle` / `Hold` / `Smart` in `SwitchBindAction.svelte` are mapped at the display layer to `切换` / `按住` / `智能` (Switch / Hold / Smart);
     their **stored values remain English**, because those values are persistent enums (`BindAction`) written to the config file — translating them would break the type contract and invalidate already-saved binds.
2. **Language file completion** — `src/main/resources/resources/liquidbounce/lang/zh_cn.json`
   - Compared with the upstream baseline, 2 missing keys were filled in, and 9 values that were identical to `en_us.json` (i.e. untranslated) were translated;
   - This maintenance round filled in `liquidbounce.module.spearKill.description`, bringing **zh_cn key coverage of en_us to 100%** (796 / 796).
   - 5 values are still deliberately kept in English (such as `ID: %s`, `TPS: %s`, `#%s %s [%s] %s`); they contain only placeholders or generic abbreviations.
3. **Note**: setting value names (such as `Scale`, `GridSize`) and identifiers in commands/config files **are intentionally kept in English** — changing them would break commands and config files.
   The same rule applies to the bind action values above, and it is a hard convention of this branch.

## 🚀 Building

Requirements:

- **JDK 25** (Temurin 25+; the project uses a Java 25 toolchain, `jdk = "25"` in `gradle/libs.versions.toml`)
- **Node.js** (with npm, for building the web theme; `src-theme/package.json` declares no `engines` constraint)

```bash
./gradlew build      # full build
./gradlew test       # tests only (254 test cases)
```

Build artifact: `build/libs/liquidbounce-0.39.1.jar`

Notes:

- The build task reads git commit information (`generateGitProperties`), so make sure the directory is a git repository;
- ClickGUI is a web frontend (Vite + Svelte); the first build runs `npm ci` + `npm run build` automatically and requires network access;
- To skip rebuilding the theme, you can change only `src-theme/src` and then run `./gradlew build` again;
- The first build needs to download Minecraft 26.2, mappings and the full dependency graph, which takes a long time (about 17 minutes measured locally);
- The frontend type check `npx svelte-check` (run under `src-theme/`) **currently reports 18 pre-existing errors**, all under
  `src-theme/src/routes/hud/elements/**` (`Hud*Settings` types not generated). These files are identical to the upstream baseline
  and are pre-existing upstream issues; they do not affect `vite build`.

## 📦 Installation & Usage

1. Put `liquidbounce-0.39.1.jar` into the `mods` folder of your Fabric client (MC 26.2; Fabric API and fabric-language-kotlin are required);
2. In game, run `/client language set zh_cn`, or simply set the game language to Simplified Chinese (AUTO follows it automatically);
3. Press **Right Shift** to open the ClickGUI — categories, module names and interface texts are all in Chinese.

## ❓ FAQ

- **Why are setting names still in English (Scale, GridSize…)?**
  These are config/command identifiers; changing them would break commands and saved configs, so the localized build deliberately keeps them in English.
- **Want to go back to vanilla?**
  Use the official upstream jar directly, or restore the files this branch modified (the change list is in "Changes in This Branch" above).
- **How do I turn off ClientChat (client chat / IRC)?**
  ClickGUI → Settings → ClientChat → turn Enabled off; or run `.value set ClientChat.Enabled false`.

## 📄 License

This project is licensed under the [GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.en.html) (LICENSE file). Copyright belongs to the original author **CCBlueX**; this branch retains all of their copyright notices and only adds Chinese localization.

Upstream project: [CCBlueX/LiquidBounce](https://github.com/CCBlueX/LiquidBounce) · Website: [liquidbounce.net](https://liquidbounce.net)
