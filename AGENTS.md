# AGENTS.md

本文件面向在此仓库中工作的 AI 代理与人类贡献者。**只记录已在本机验证过的事实**；
未验证的内容一律不写，因为错误的指引比没有指引更有害。

## 文档约定（双语 + 徽章）

README 为**英文主文档**（`README.md`）+ **中文全量翻译**（`doc/README_zh-CN.md`），
两份内容一一对应，**改一边必须同步另一边**。两份文件顶部是同一组 shields.io 徽章
（语言/平台/CI/License/Release/Downloads/Stars/Repo Size/Contributors 按仓库实际能力裁剪，
没有的能力不放，避免死链），徽章下面一行语言切换：
`README.md` 用 `**English** | [简体中文](doc/README_zh-CN.md)`，
中文版用 `[English](../README.md) | **简体中文**`。增删徽章时两份一起改。

## 这是什么仓库

`CCBlueX/LiquidBounce`（水影）的**中文汉化派生仓库**，不是 GitHub fork。

- 上游基线：`adfddc0781d056201037ac16b216cfd3e2df4e4a`（2026-09-23，`mod_version=0.40.1`，上游 `nextgen` 分支 tip）
- 上一轮基线：`8fc1f12b34c50f483c3b3446c45e8bc1de58e20c`（2026-08-09，`mod_version=0.39.1`）
- 本仓库历史：第一轮为**单次 squash 提交**（`64243a1`），与上游**没有共同祖先**；
  当前同步分支 `sync/0.40.1` 改为在上游 tip 之上叠加改动，因此它**是**上游的后代
- 平台：Fabric · Minecraft `26.2` · JDK `25`

### 同步上游时的关键约束

旧分支（`maintenance/2026-09-12-localization-and-docs`、`origin/main`）与上游**没有共同祖先**，
`git merge upstream/nextgen` 会直接失败：

```
fatal: refusing to merge unrelated histories
```

即使加上 `--allow-unrelated-histories`，git 也会把全部约 2300+ 个文件视为 add/add 冲突
（上一轮基线 `8fc1f12` 有 2293 个文件，上游 tip `adfddc078` 有 2377 个）——
**`git merge` 在这个仓库里不是正确的同步手段。**

正确做法是三方应用（3-way apply）：把本仓库相对基线的改动面重新应用到新的上游基线上。

```bash
git diff <上一轮基线> <汉化分支> > /tmp/l10n.patch
git checkout -b sync/<新版本> upstream/nextgen   # 基于上游 tip，历史干净
git apply -3 /tmp/l10n.patch                     # 用 blob 做三方合并
```

`git apply -3` 只在真正重叠处留冲突标记（本轮 24 个文件里只有 2 个留标记：
`gradlew.bat` 与 `zh_cn.json`），其余自动合并。冲突逐个手工解决后 `git add`。
识别基线的方法：用未被汉化改动的文件（如 `build.gradle.kts`、`settings.gradle.kts`、`flake.nix`）的
blob hash 去上游历史中反查。

从 0.40.1 起本分支以 `sync/<版本>` 命名，且**以 `upstream/nextgen` 为父提交**，
因此下次同步既可以直接 `git merge`（若不再需要重写历史），也可以继续沿用 3-way apply。

### 命令系统与语言键（0.40.1 起）

上游把旧的命令系统整体重写为 Brigadier DSL
（`features/command/Command.kt`、`Parameter.kt`、`builder/`、`dsl/` 均已被删除），
语言键格式随之改变：

- 旧格式：`liquidbounce.command.<cmd>.subcommand.<sub>.result.<msg>`、`...parameter.<p>.description`
- 新格式：去掉 `.subcommand.` / `.result.` 段，即 `liquidbounce.command.<cmd>.<sub>.<msg>`
  （见 `features/command/brigadier/CommandDsl.kt` 的 `translation("liquidbounce.command.${path.substringBefore('.')}.$key")`）

**`zh_cn.json` 中带 `.subcommand.` / `.result.` / `.parameter.` 段的键在新命令系统下不会命中**，
同步上游时必须按新格式重新生成。本轮即删除了 370 个此类孤儿键（`.subcommand.` 264、
`.result.` 234、`.parameter.` 60，同一键可同时命中多个模式），并把 382 个上游新键补齐。

上游自带校验脚本 `scripts/verify-i18n.mjs`（本仓库直接保留上游版本，未改动）：

```bash
node scripts/verify-i18n.mjs            # 额外键只警告
node scripts/verify-i18n.mjs --strict   # 额外键也算失败
node scripts/verify-i18n.mjs --json     # 机器可读
```

它以上游自带的 `en_us.json` 为基准检查三件事：缺失键、额外键、占位符（`%s`/`%d`/`%1$s`）数量不一致。
**注意该脚本对上游其余语言也报大量 FAIL**（上游 `de_de`/`ja_jp`/`zh_tw` 等本身就没跟上新键），
本仓库只要求 `zh_cn` 一行是 `OK`。

已知上游自身缺陷：`liquidbounce.module.noFall.messages.spoofLanding` 被上游 Kotlin 代码
（`.../player/nofall/modes/NoFallSpoofLanding.kt`）引用，但**上游 `en_us.json` 里没有这个键**。
因为本仓库要求 zh_cn 与 en_us 严格 1:1，这个键无法出现在 zh_cn 中，该模式下会直接显示原始键名。
不要为了它破坏 1:1（`verify-i18n.mjs` 会把额外键算作 issue）。

**翻译值时不要动占位符数量。** 上游重写命令系统时改了若干字符串的占位符
（例：`liquidbounce.command.client.config.backup.failedToBackup` 由 `Backup failed: %s`
变成只剩 `%s`，`...value.set.success` 由 2 个参数变成 1 个），沿用旧译文的占位符会渲染出多余/缺失的参数。

## 构建与测试

```bash
export JAVA_HOME=/Library/Java/JavaVirtualMachines/zulu-25.jdk/Contents/Home   # 必须 JDK 25

./gradlew test                 # 394 个测试（0.39.1 时代为 254），本机实测全绿
./gradlew build                # 完整构建，产物 build/libs/liquidbounce-0.40.1.jar
```

前端主题（`src-theme/`）：

```bash
cd src-theme
npm ci                         # 首次需要，需要联网
npm run build                  # vite build
npx svelte-check --tsconfig ./tsconfig.json   # 类型检查
```

### 实测踩坑

- **`JAVA_HOME` 必须显式指定 JDK 25。** 本机默认 `java` 恰好是 25，但 `JAVA_HOME` 为空；
  若环境里存在其他 JDK（本机装有 17/21/25），不显式设置会在构建中报出难以定位的错误。
- **首次构建耗时很长（本机约 17 分钟）**：Gradle wrapper 9.6.1 需要下载，之后 Fabric Loom
  还要拉取 Minecraft 26.2、映射与完整依赖图（`~/.gradle/caches/fabric-loom/26.2/`）。
  构建「卡住」时先看 `~/.gradle/caches` 是否有新文件，再判断是否真的挂死。
- **`svelte-check` 当前有 21 个报错**（327 个文件，0 warning）：
  - 18 个既有报错，全部在 `src-theme/src/routes/hud/elements/**`（`Hud*Settings` 类型未生成）；
  - 3 个是上游 0.40.1 在 `src-theme/src/routes/menu/common/modal/Tabs.svelte` 引入的
    （该文件本仓库逐字节等于上游，改动来自上游 `64f9c02c`，与本仓库无关）。
  - 两类都**不影响 `vite build`**（构建成功）。不要把它们误判为本次改动引入，也不要顺手「修」。
- `./gradlew test` 走的是 Fabric Knot classloader 的无头测试环境，**不需要启动 Minecraft 客户端**。
- **CI 不跑测试。** `.github/workflows/build.yml` 的构建步骤是 `./gradlew build -x test -x detekt`
  （且 workflow 只在 `nextgen` 分支的 push/PR 上触发）。**测试全绿必须本地验证**，不能依赖 CI。
- `./gradlew test` 在源码未变时会报 `UP-TO-DATE` 而跳过实际执行；需要真实重跑时用
  `./gradlew test --rerun-tasks`。核对是否真跑过，看 `build/test-results/test/*.xml` 的时间戳。
- **0.40.1 实测基线**：`./gradlew test --rerun-tasks`（JDK 25）`BUILD SUCCESSFUL in 13m`，
  **394 个用例 / 0 失败 / 0 错误 / 0 跳过**（64 个测试类；数字取自 `build/test-results/test/*.xml`
  与 `build/reports/tests/test/index.html`）。`src/test` 里的 `@Test` 计数同样是 394。
- `build.gradle.kts` 会调用 `node --version` 与 `npm --version` 来决定 node/npm 版本输入，
  所以 `node`/`npm` 必须在 `PATH` 里，否则配置阶段就失败。

## 硬性约定

1. **绝不翻译数据值。** 只翻译**显示层**文本。
   - 反例（会引入 BUG）：`SwitchBindAction.svelte` 的 `choices={["切换","按住","智能"]}`。
     该数组是 `BindAction`（`"Toggle" | "Hold" | "Smart"`）类型，值会被写入配置文件；
     翻译它会破坏类型契约，并使已保存的按键绑定失效。
   - 正例：值保持英文，在渲染处映射，见同文件的 `actionLabels`（`SwitchBindAction.svelte`）。
   - 同理适用于设置项名（`Scale`、`GridSize`）、命令名与配置标识符。
2. **汉化只改显示层文件。** `localization.ts` 是模块名/分类名的唯一映射源；
   新增模块时必须同步补进 `moduleNames`，否则该模块在 ClickGUI 中显示英文原名。
3. **`zh_cn.json` 与 `en_us.json` 的键必须一一对应。** 新增语言键时两个文件都要加。
   当前 zh_cn 对 en_us 的覆盖率为 100%（808/808），且键顺序与 en_us 完全一致。
   核对命令：`node scripts/verify-i18n.mjs`（`zh_cn` 行必须是 `OK`）。
4. **改 README 前先数一遍。** 本仓库的 README 曾出现「翻译 14 个值」（实际 9 个）、
   「汉化 16 处文案」但列举了从未改动的「窗口标题」「打开」等不实声明。
   任何数量、路径、命令都必须实测后再写。

## 目录速览

| 路径 | 作用 |
|---|---|
| `src/main/kotlin/` | 客户端主体（Kotlin） |
| `src/main/java/` | Mixin 注入 |
| `src/main/resources/resources/liquidbounce/lang/` | 各语言文件，含 `zh_cn.json` |
| `src-theme/src/routes/clickgui/` | ClickGUI 前端（Svelte），汉化主要集中在此 |
| `src-theme/src/routes/clickgui/localization.ts` | 模块名/分类名中文映射（唯一来源，240 个模块 + 8 个分类） |
| `src/test/kotlin/` | 测试（394 个用例；0.40.1 随命令系统重写新增了一批 Brigadier DSL 测试） |
| `scripts/verify-i18n.mjs` | 上游语言文件校验脚本（与上游一致，未改动） |
| `gradle/libs.versions.toml` | 依赖版本目录 |
| `buildSrc/` | 构建逻辑 |
