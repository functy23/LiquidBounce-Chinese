<div align="center">
<p>
    <img width="200" src="https://raw.githubusercontent.com/CCBlueX/LiquidCloud/master/LiquidBounce/liquidbounceLogo.svg">
</p>

# 🀄 LiquidBounce 中文汉化版

**LiquidBounce（水影）的非官方中文汉化版 —— ClickGUI 与模块名已汉化，Minecraft 26.2 / Fabric。**

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

[English](../README.md) | **简体中文**
</div>

---

## 概述

> 基于 [CCBlueX/LiquidBounce](https://github.com/CCBlueX/LiquidBounce) 的**中文汉化修改版**，非官方构建。
>
> 分支版本：`0.40.1` · 支持 Minecraft `26.2` · 平台：Fabric

## ⚠️ 免责声明

- 本仓库为**本地化/汉化分支**，代码版权归原作者 **CCBlueX** 所有，遵循 **GNU GPL v3.0** 协议。
- LiquidBounce 为作弊客户端，**仅建议在单机或允许的环境中使用**；在服务器上使用可能被反作弊检测并导致封号，请自行承担风险。

## ✨ 本分支的改动

> 上游基线：`CCBlueX/LiquidBounce` 提交 `adfddc078`（2026-09-23，`mod_version=0.40.1`，`nextgen` 分支）。

1. **ClickGUI 界面汉化**
   - 新增 `src-theme/src/routes/clickgui/localization.ts`：**240 个模块名 + 8 个分类名**的中文显示映射（仅显示层，不影响命令/配置标识符）。
     0.40.1 中声明的 **240 个模块已全部覆盖（覆盖率 100%）**，且没有多余条目：其中 4 个（`AutoDeposit`、`PotionFX`、`TotemEffect`、`TridentBoost`）是本次上游新增，另 2 个（`AutoBuff`、`BetterTitle`）早就在上游存在、只是此前漏译。
     该文件由 `Module.svelte`、`Search.svelte`、`Panel.svelte` 引用；未命中的模块名回退显示原名。
   - 汉化 ClickGUI 界面文案：搜索框占位符、空状态（没有找到模块/组件）、以及设置项与 HUD 编辑器面板中的按钮/提示文字。
     **共 16 个 `.svelte` 文件含有中文文案**；第 17 个（`Panel.svelte`）只增加了分类名的显示层映射调用，本身不含文案。
   - **绑定动作显示**：`SwitchBindAction.svelte` 的 `Toggle`/`Hold`/`Smart` 在显示层映射为「切换/按住/智能」；
     其**存储值仍为英文**，因为该值是写入配置文件的持久化枚举（`BindAction`），翻译它会破坏类型契约并使已保存的绑定失效。
2. **语言文件重做** `src/main/resources/resources/liquidbounce/lang/zh_cn.json`
   - 上游在 0.40.1 把命令系统整体重写为 Brigadier DSL，语言键格式随之改变：旧键带 `.subcommand.` / `.result.` / `.parameter.` 段，新键去掉了这些段
     （`.friend list result noFriends` → `.friend.list.noFriends`）。因此旧的 `zh_cn.json` 里有 **370 个再也匹配不到的孤儿命令键**。
   - 本次**清掉了全部 370 个孤儿键，并补齐了 382 个上游新键**，现在 `zh_cn.json` 与 `en_us.json` 的键**完全相同（各 808 个）且顺序一致**（覆盖率 100%）。
     译文记忆被保留：425 个值沿用本分支原有译文，222 个由旧格式重新映射而来，70 个为新增翻译（插件系统、市场/配置系统，以及上游自己也没译的若干条目），3 个是本分支更清楚的措辞，88 个取自上游 `zh_cn.json`。
   - 上游自带校验脚本 `scripts/verify-i18n.mjs`，以上游 `en_us.json` 为基准检查缺失键、额外键与占位符数量。`node scripts/verify-i18n.mjs` 对 zh_cn 的输出为 **`OK zh_cn 0 issue(s)`**。
3. **说明**：设置项的值名（如 `Scale`、`GridSize`）以及命令/配置文件中的标识符**有意保留英文**，改动它们会破坏命令与配置文件。
   这条规则同样适用于上文的绑定动作值，是本分支的一条硬性约定。

## 🚀 构建

环境要求：

- **JDK 25**（Temurin 25+，项目使用 Java 25 工具链；`gradle/libs.versions.toml` 中 `jdk = "25"`）
- **Node.js**（含 npm，用于构建 Web 主题；`src-theme/package.json` 未声明 `engines` 约束）

```bash
./gradlew build      # 完整构建
./gradlew test       # 仅跑测试（394 个用例）
```

构建产物：`build/libs/liquidbounce-0.40.1.jar`

注意事项：

- 构建任务会读取 git 提交信息（`generateGitProperties`），请确保目录是 git 仓库；
- ClickGUI 是 Web 前端（Vite + Svelte），首次构建会自动执行 `npm ci` + `npm run build`，需要联网；
- 想跳过主题重新构建时，可仅修改 `src-theme/src` 后重新执行 `./gradlew build`；
- 首次构建需要下载 Minecraft 26.2、映射与完整依赖图，耗时较长（本机实测约 17 分钟）；
- 前端类型检查 `npx svelte-check`（在 `src-theme/` 下运行）**当前在 7 个文件中报 21 个错误**（0 个 warning）：
  其中 18 个既有报错位于 `src-theme/src/routes/hud/elements/**`（`Hud*Settings` 类型未生成，与上游一致），
  另 3 个位于 `src-theme/src/routes/menu/common/modal/Tabs.svelte`，是上游 0.40.1 自己引入的（该文件与本仓库上游逐字节一致）。
  两类都属上游既有问题，不影响 `vite build`。

## 📦 安装与使用

1. 将 `liquidbounce-0.40.1.jar` 放入 Fabric 客户端的 `mods` 文件夹（MC 26.2，需安装 Fabric API 与 fabric-language-kotlin）；
2. 进游戏后执行 `/client language set zh_cn`，或直接把游戏语言设为简体中文（AUTO 会自动跟随）；
3. 按 **右 Shift** 打开 ClickGUI，分类、模块名与界面文字均为中文。

## ❓ 常见问题

- **为什么设置项名字还是英文（Scale、GridSize…）？**
  这些是配置/命令的标识符，改动会导致命令与存档配置失效，因此汉化版刻意保留英文。
- **想恢复原版？**
  直接使用官方原版 jar，或还原本分支修改过的文件（改动清单见上方「本分支的改动」）。
- **为什么升级到 0.40.1 后旧命令汉化不生效了？**
  上游 0.40.1 重写了命令系统，语言键格式随之变化；本分支已全部重新映射，直接使用本仓库的 `zh_cn.json` 即可，不要用旧文件覆盖它。
- **如何关闭 ClientChat（客户端聊天/IRC）？**
  ClickGUI → 设置 → ClientChat → 关闭 Enabled；或执行 `.value set ClientChat.Enabled false`。

## 📄 许可证

本项目遵循 [GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.en.html)（LICENSE 文件）。版权归原作者 **CCBlueX** 所有，本分支保留其全部版权声明，仅作中文本地化。

上游项目：[CCBlueX/LiquidBounce](https://github.com/CCBlueX/LiquidBounce) · 官网：[liquidbounce.net](https://liquidbounce.net)