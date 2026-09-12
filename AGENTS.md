# AGENTS.md

本文件面向在此仓库中工作的 AI 代理与人类贡献者。**只记录已在本机验证过的事实**；
未验证的内容一律不写，因为错误的指引比没有指引更有害。

## 这是什么仓库

`CCBlueX/LiquidBounce`（水影）的**中文汉化派生仓库**，不是 GitHub fork。

- 上游基线：`8fc1f12b34c50f483c3b3446c45e8bc1de58e20c`（2026-08-09，`mod_version=0.39.1`）
- 本仓库历史：**单次 squash 提交**（`64243a1`），与上游**没有共同祖先**
- 平台：Fabric · Minecraft `26.2` · JDK `25`

### 同步上游时的关键约束

因为与上游无共同祖先，`git merge upstream/main` 会直接失败：

```
fatal: refusing to merge unrelated histories
```

即使加上 `--allow-unrelated-histories`，git 也会把全部约 2294 个文件视为 add/add 冲突——
**`git merge` 在这个仓库里不是正确的同步手段。**

正确做法是三方应用（3-way apply）：把本仓库相对基线的改动面（约 19 个文件）重新应用到新的上游基线上。
识别基线的方法：用未被汉化改动的文件（如 `build.gradle.kts`、`settings.gradle.kts`、`flake.nix`）的
blob hash 去上游历史中反查。

另外注意：上游已把旧的命令系统整体重写为 Brigadier DSL
（`features/command/Command.kt`、`Parameter.kt`、`builder/`、`dsl/` 均已被删除）。
**`zh_cn.json` 中带 `.subcommand.` / `.result.` / `.parameter.` 段的键在新命令系统下不会命中**，
同步上游时必须按新格式重新生成这些键。

## 构建与测试

```bash
export JAVA_HOME=/Library/Java/JavaVirtualMachines/zulu-25.jdk/Contents/Home   # 必须 JDK 25

./gradlew test                 # 254 个测试，本机实测全绿
./gradlew build                # 完整构建，产物 build/libs/liquidbounce-0.39.1.jar
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
- **`svelte-check` 当前有 18 个既有报错**，全部在 `src-theme/src/routes/hud/elements/**`
  （`Hud*Settings` 类型未生成）。这些文件与上游基线逐字节一致，属上游既有问题，
  **不影响 `vite build`**（构建成功）。不要把它们误判为本次改动引入。
- `./gradlew test` 走的是 Fabric Knot classloader 的无头测试环境，**不需要启动 Minecraft 客户端**。
- **CI 不跑测试。** `.github/workflows/build.yml` 的构建步骤是 `./gradlew build -x test -x detekt`
  （且 workflow 只在 `nextgen` 分支的 push/PR 上触发）。**测试全绿必须本地验证**，不能依赖 CI。
- `./gradlew test` 在源码未变时会报 `UP-TO-DATE` 而跳过实际执行；需要真实重跑时用
  `./gradlew test --rerun-tasks`。核对是否真跑过，看 `build/test-results/test/*.xml` 的时间戳。

## 硬性约定

1. **绝不翻译数据值。** 只翻译**显示层**文本。
   - 反例（会引入 BUG）：`SwitchBindAction.svelte` 的 `choices={["切换","按住","智能"]}`。
     该数组是 `BindAction`（`"Toggle" | "Hold" | "Smart"`）类型，值会被写入配置文件；
     翻译它会破坏类型契约，并使已保存的按键绑定失效。
   - 正例：值保持英文，在渲染处映射，见同文件的 `actionLabels`。
   - 同理适用于设置项名（`Scale`、`GridSize`）、命令名与配置标识符。
2. **汉化只改显示层文件。** `localization.ts` 是模块名/分类名的唯一映射源；
   新增模块时必须同步补进 `moduleNames`，否则该模块在 ClickGUI 中显示英文原名。
3. **`zh_cn.json` 与 `en_us.json` 的键必须一一对应。** 新增语言键时两个文件都要加。
   当前 zh_cn 对 en_us 的覆盖率为 100%（796/796）。
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
| `src-theme/src/routes/clickgui/localization.ts` | 模块名/分类名中文映射（唯一来源） |
| `src/test/kotlin/` | 测试（254 个用例） |
| `gradle/libs.versions.toml` | 依赖版本目录 |
| `buildSrc/` | 构建逻辑 |
