<div align="center">
<p>
    <img width="200" src="https://raw.githubusercontent.com/CCBlueX/LiquidCloud/master/LiquidBounce/liquidbounceLogo.svg">
</p>
</div>

# LiquidBounce 中文汉化版（水影汉化分支）

> 基于 [CCBlueX/LiquidBounce](https://github.com/CCBlueX/LiquidBounce) 的**中文汉化修改版**，非官方构建。
>
> 分支版本：`0.39.1` · 支持 Minecraft `26.2` · 平台：Fabric

## ⚠️ 免责声明

- 本仓库为**本地化/汉化分支**，代码版权归原作者 **CCBlueX** 所有，遵循 **GNU GPL v3.0** 协议。
- LiquidBounce 为作弊客户端，**仅建议在单机或允许的环境中使用**；在服务器上使用可能被反作弊检测并导致封号，请自行承担风险。

## ✨ 本分支的改动

> 上游基线：`CCBlueX/LiquidBounce` 提交 `8fc1f12b`（2026-08-09，`mod_version=0.39.1`）。
> 本仓库为单次提交的汉化派生仓库，**不是 GitHub fork**，与上游没有共同祖先，因此不能直接用 `git merge` 同步。

1. **ClickGUI 界面汉化**
   - 新增 `src-theme/src/routes/clickgui/localization.ts`：**234 个模块名 + 8 个分类名**的中文显示映射（仅显示层，不影响命令/配置标识符）。
     其中 232 个覆盖 `ModuleManager` 注册的全部模块（覆盖率 100%），另 2 个为上游遗留条目。
     该文件由 `Module.svelte`、`Search.svelte`、`Panel.svelte` 引用；未命中的模块名回退显示原名。
   - 汉化 ClickGUI 界面文案：搜索框占位符、空状态（没有找到模块/组件）、以及设置项与 HUD 编辑器面板中的按钮/提示文字，共涉及 16 个 `.svelte` 文件。
   - **绑定动作显示**：`SwitchBindAction.svelte` 的 `Toggle`/`Hold`/`Smart` 在显示层映射为「切换/按住/智能」；
     其**存储值仍为英文**，因为该值是写入配置文件的持久化枚举（`BindAction`），翻译它会破坏类型契约并使已保存的绑定失效。
2. **语言文件补全** `src/main/resources/resources/liquidbounce/lang/zh_cn.json`
   - 相对上游基线补全 2 个缺失键，另翻译 9 个原本与 `en_us.json` 完全相同（即未翻译）的值；
   - 本次维护补齐 `liquidbounce.module.spearKill.description`，至此 **zh_cn 对 en_us 的键覆盖率为 100%**（796 / 796）。
   - 仍保留 5 个刻意为英文的值（如 `ID: %s`、`TPS: %s`、`#%s %s [%s] %s`），它们只含占位符或通用缩写。
3. **说明**：设置项的值名（如 `Scale`、`GridSize`）以及命令/配置文件中的标识符**有意保留英文**，改动它们会破坏命令与配置文件。
   这条规则同样适用于上文的绑定动作值，是本分支的一条硬性约定。

## 🚀 构建

环境要求：

- **JDK 25**（Temurin 25+，项目使用 Java 25 工具链；`gradle/libs.versions.toml` 中 `jdk = "25"`）
- **Node.js**（含 npm，用于构建 Web 主题；`src-theme/package.json` 未声明 `engines` 约束）

```bash
./gradlew build      # 完整构建
./gradlew test       # 仅跑测试（254 个用例）
```

构建产物：`build/libs/liquidbounce-0.39.1.jar`

注意事项：

- 构建任务会读取 git 提交信息（`generateGitProperties`），请确保目录是 git 仓库；
- ClickGUI 是 Web 前端（Vite + Svelte），首次构建会自动执行 `npm ci` + `npm run build`，需要联网；
- 想跳过主题重新构建时，可仅修改 `src-theme/src` 后重新执行 `./gradlew build`；
- 首次构建需要下载 Minecraft 26.2、映射与完整依赖图，耗时较长（本机实测约 17 分钟）；
- 前端类型检查 `npx svelte-check`（在 `src-theme/` 下运行）**当前有 18 个既有报错**，全部位于
  `src-theme/src/routes/hud/elements/**`（`Hud*Settings` 类型未生成）。这些文件与上游基线完全一致，
  属上游既有问题，不影响 `vite build`。

## 📦 安装与使用

1. 将 `liquidbounce-0.39.1.jar` 放入 Fabric 客户端的 `mods` 文件夹（MC 26.2，需安装 Fabric API 与 fabric-language-kotlin）；
2. 进游戏后执行 `/client language set zh_cn`，或直接把游戏语言设为简体中文（AUTO 会自动跟随）；
3. 按 **右 Shift** 打开 ClickGUI，分类、模块名与界面文字均为中文。

## ❓ 常见问题

- **为什么设置项名字还是英文（Scale、GridSize…）？**
  这些是配置/命令的标识符，改动会导致命令与存档配置失效，因此汉化版刻意保留英文。
- **想恢复原版？**
  直接使用官方原版 jar，或还原本分支修改过的文件（改动清单见上方「本分支的改动」）。
- **如何关闭 ClientChat（客户端聊天/IRC）？**
  ClickGUI → 设置 → ClientChat → 关闭 Enabled；或执行 `.value set ClientChat.Enabled false`。

## 📄 许可证

本项目遵循 [GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.en.html)（LICENSE 文件）。版权归原作者 **CCBlueX** 所有，本分支保留其全部版权声明，仅作中文本地化。

上游项目：[CCBlueX/LiquidBounce](https://github.com/CCBlueX/LiquidBounce) · 官网：[liquidbounce.net](https://liquidbounce.net)
