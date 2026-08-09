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

1. **ClickGUI 界面汉化**
   - 新增 `src-theme/src/routes/clickgui/localization.ts`：**216 个模块名 + 8 个分类名**的中文显示映射（仅显示层，不影响命令/配置标识符）；
   - 汉化 16 处界面文案：窗口标题、顶部标签、搜索框、按钮/提示（定位、打开、移除组件、重置、添加项、添加值、取消、切换/按住/智能）、空状态（没有找到模块/组件）、按键提示等。
2. **语言文件补全** `src/main/resources/resources/liquidbounce/lang/zh_cn.json`：补全 2 个缺失键，翻译 14 个仍为英文的值。
3. **构建修复** `src-theme/package-lock.json`：将 lock 文件中 13 处 `registry.npmmirror.com` 镜像地址改回官方 npm 源（npm 12 默认拒绝拉取镜像远程 tarball，会导致 `npm ci` 失败）。
4. **说明**：设置项的值名（如 `Scale`、`GridSize`）以及命令/配置文件中的标识符**有意保留英文**，改动它们会破坏命令与配置文件。

## 🚀 构建

环境要求：

- **JDK 25**（Temurin 25+，项目使用 Java 25 工具链）
- **Node.js 18+**（含 npm，用于构建 Web 主题）

```bash
./gradlew build
```

构建产物：`build/libs/liquidbounce-0.39.1.jar`

注意事项：

- 构建任务会读取 git 提交信息（`generateGitProperties`），请确保目录是 git 仓库；
- ClickGUI 是 Web 前端（Vite + Svelte），首次构建会自动执行 `npm ci` + `npm run build`，需要联网；
- 想跳过主题重新构建时，可仅修改 `src-theme/src` 后重新执行 `./gradlew build`。

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
