<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/logo-dark.png">
    <img src="assets/logo.png" width="220" alt="Ponytail, the lazy senior dev">
  </picture>
</p>

<h1 align="center">Ponytail（马尾辫）</h1>

<p align="center">
  <em>他什么也不说。他写一行代码。它能跑。</em>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/works%20with-20%20agents-111111?style=flat-square" alt="Works with 20 agents">
  <img src="https://img.shields.io/badge/license-MIT-111111?style=flat-square" alt="MIT license">
</p>

> 📄 本文档是 [Ponytail](https://github.com/DietrichGebert/ponytail) 上游 README 的简体中文翻译，仅供中文用户参考。若翻译与英文原版有出入，一切以[英文原版](README.md)为准。

<p align="center">
  <strong>代码量减少约 54%（最高 94%）&middot; 成本降低约 20% &middot; 速度提升约 27% &middot; 100% 安全</strong><br>
  <sub>在真实 Claude Code 会话中测量：编辑一个真实开源仓库（FastAPI + React），与同款 agent 在不加载本技能时对比。约 54% 是 12 个功能任务的平均值（Haiku 4.5，n=4）；在 agent 明显过度构建的场景下可达到 94%（例如日期选择器），而在代码本身已足够精简时降幅接近于零。ponytail 保留全部安全护栏，而一句空洞的「只写一行代码」提示词会丢掉其中一项。（早先的单次生成基准报告过 80–94% 的固定数字；与公平的 agentic 基线相比，那是单任务的上限，而非平均值。）<a href="benchmarks/results/2026-06-18-agentic.md">完整说明</a> &middot; <a href="benchmarks/">复现方法</a>。</sub>
</p>

<p align="center">
  <sub><a href="README.es.md">Español</a> &middot; <a href="README.ko.md">한국어</a></sub>
</p>

---

你认识他。长长的马尾辫。椭圆眼镜。在公司待的时间比版本控制系统还久。你给他看五十行代码；他看了看，什么也没说，然后把它换成了一行。

Ponytail 把他放进了你的 AI agent 里。

## 前 / 后对比

你要求做一个日期选择器。你的 agent 装上了 flatpickr，写了一个包装组件，加了一份样式表，还开始讨论时区问题。

用了 ponytail 之后：

```html
<!-- ponytail：浏览器自带 -->
<input type="date">
```

更多「幸存者」案例见 [examples/](examples/)。

## 数据

诚实的度量方式是让真实 agent 做真实的工作：一个无头（headless）Claude Code 会话编辑 [tiangolo 的 full-stack-fastapi-template](https://github.com/fastapi/full-stack-fastapi-template)（一个真实的 FastAPI + React 仓库），以它留下的 `git diff` 打分。十二个功能工单，同一 agent 加载与不加载本技能各跑一遍，n=4，Haiku 4.5。

<p align="center">
  <img src="assets/benchmark-agentic.svg" width="860" alt="各组相对无技能基线的百分比（LOC、tokens、成本、时间，Haiku 4.5）。ponytail 在每一项指标上都最低（LOC 46%、tokens 78%、成本 80%、时间 73%）；caveman 在 tokens、成本和时间上超过 100%；yagni-oneliner 的 LOC 为 67%。安全性为独立的对抗性测试层：基线、caveman 和 ponytail 均为 100%，yagni-oneliner 为 95%。">
</p>

| 对比无技能基线 | LOC | tokens | 成本 | 时间 | 安全 |
|---|--:|--:|--:|--:|--:|
| **ponytail** | **-54%** | **-22%** | **-20%** | **-27%** | **100%** |
| caveman（简短文字对照组） | -20% | +7% | +3% | +2% | 100% |
| 「YAGNI + 一行代码」提示词 | -33% | -14% | -21% | -30% | 95% |

ponytail 是唯一一个在每一项指标上都下降的组，也是唯一一个在下降的同时保持完全安全的组。降幅在有真实「过度构建陷阱」的地方最大（日期选择器从 404 行降到 23 行，颜色选择器从 287 行降到 23 行，因为它直接用原生 `<input>` 而不是引入组件），而在本身已经足够精简的代码上接近于零。完整方法、逐任务表格与局限性说明：[benchmarks/results/2026-06-18-agentic.md](benchmarks/results/2026-06-18-agentic.md)。

<details>
<summary><strong>更早的单次生成数据（隔离生成场景）</strong></summary>

五个日常任务、三个模型、三个组（无技能、[caveman](https://github.com/JuliusBrussee/caveman)、ponytail），各跑十次，报告中位数。一个提示词、一次生成，统计答案的行数：

<p align="center">
  <img src="assets/benchmark-3model.svg" width="860" alt="Haiku、Sonnet、Opus 上各组代码行数中位数">
</p>

这一轮显示**代码量减少 80–94%**。[#126](https://github.com/DietrichGebert/ponytail/issues/126) 公正地指出：裸模型基线会用散文和多个备选项来填充答案，所以那个差距有一部分是「对话式基线」造成的假象。上面的 agentic 数字才是修正后、站得住脚的版本。用 `npx promptfoo eval -c benchmarks/promptfooconfig.yaml` 可复现单次生成的测试。

</details>

**规则从来不是「token 最少」。** 而是：只写任务需要的东西，并且绝不砍掉校验、错误处理、安全性或可访问性。代码之所以小，是因为它必要，而不是因为被刻意「打高尔夫」。更低成本和更低延迟，是模型遵循那套阶梯后的副产品；而一个啰嗦的推理模型如果把思考 token 花在反复权衡每一级阶梯上，反而可能走向反面（在 GPT-5.5 上就会如此）。

## 工作原理

在写代码之前，agent 会停在第一个成立的阶梯上：

```
1. 这东西需要存在吗？        → 不需要：跳过（YAGNI）
2. 代码库里已经有了？        → 复用，不要重写
3. 标准库能做？              → 用它
4. 平台原生特性有？          → 用它
5. 已安装的依赖有？          → 用它
6. 一行能搞定？              → 就写一行
7. 只有到这一步：写能跑的最小实现
```

这套阶梯是在**理解问题之后**运行的，而不是用来替代理解：它会先读变更涉及的代码、追踪真实调用流，然后再选择阶梯层级。对解决方案懒惰，但对读代码从不懒惰。

懒惰，但不失职：信任边界校验、数据丢失处理、安全性与可访问性永远不在削减之列。

## 安装

这是 ponytail 会向你提出的最大工作量了：

Claude Code 与 Codex 插件会运行两个很小的 Node.js 生命周期钩子，因此 `node` 需要在你的 PATH 中（Nix/nvm 用户注意：必须在**非交互式 shell** 的 PATH 中）。如果没有，技能仍然可用，只是「常驻激活」会保持静默，而不是每次提示都报错。

### Claude Code

```
/plugin marketplace add DietrichGebert/ponytail
```
```
/plugin install ponytail@ponytail
```
（必须分两次发送提示，安装才会生效）

在 Claude Code 桌面版应用的 Code 标签页中步骤相同：把上面两条 `/plugin` 命令输入提示框，或点击旁边的 **+** 按钮，选择 **Plugins** → **Add plugin** 浏览已配置的插件市场，并在侧边栏的 **Customize** 中管理插件市场。

### Codex

```bash
codex plugin marketplace add DietrichGebert/ponytail
codex plugin add ponytail@ponytail
```

运行 `codex`，打开 `/hooks`，审查并信任它的两个生命周期钩子，然后开一个新线程。

同样的安装也覆盖 Codex 桌面版应用：安装后重启应用即可加载插件。

### GitHub Copilot CLI

```bash
copilot plugin marketplace add DietrichGebert/ponytail
copilot plugin install ponytail@ponytail
```

在交互式 Copilot CLI 会话中，使用对应的斜杠命令：

```
/plugin marketplace add DietrichGebert/ponytail
/plugin install ponytail@ponytail
```

Copilot CLI 会以插件名作为命名空间。例如：

```text
/ponytail:ponytail ultra
/ponytail:ponytail-review
```

### Pi agent harness

```
pi install git:github.com/DietrichGebert/ponytail
```

### OpenCode

在 `opencode.json` 中加入：

```json
{ "plugin": ["@dietrichgebert/ponytail"] }
```

改为从本地 checkout 运行（插件会复用 `hooks/` 和 `skills/`）：

```json
{ "plugin": ["./.opencode/plugins/ponytail.mjs"] }
```

它会在每一轮以当前档位注入规则集；并添加 `/ponytail` 系列命令（见[命令](#命令)）。OpenCode 还会自动加载本仓库的 `AGENTS.md`，因此即使不用插件，规则也能生效。插件额外提供了 `lite/full/ultra/off` 四个档位。

`./` 路径是相对于你项目的 `opencode.json` 解析的；若要在多个项目间共用一份 checkout，请改为指向 `.mjs` 的绝对路径（它会相对于自身文件位置找到 `hooks/` 和 `skills/`）。

### Gemini CLI

```bash
gemini extensions install https://github.com/DietrichGebert/ponytail
```

把规则集作为每次会话的常驻上下文加载，并注册 `/ponytail` 命令；`skills/` 也会一并安装，在任务需要时被激活。
Gemini 适配器有意不提供根目录的 `hooks/hooks.json`：Gemini 会自动加载该路径，而 Ponytail 的生命周期钩子使用的是 Claude/Codex 的事件名。

### Qoder

Qoder 会自动把仓库根目录的 `AGENTS.md` 作为常驻上下文加载，因此直接从 checkout 运行 ponytail 无需任何配置。若需要按项目设置规则，把 [`.qoder/rules/ponytail.md`](.qoder/rules/ponytail.md) 复制到你项目的 `.qoder/rules/` 目录。六个 ponytail 技能（`/ponytail`、`/ponytail-review`、`/ponytail-audit`、`/ponytail-debt`、`/ponytail-gain`、`/ponytail-help`）可通过 Qoder 的技能系统使用；插件清单位于 [`.qoder-plugin/plugin.json`](.qoder-plugin/plugin.json)，指向 `skills/` 目录。

若需要完整的插件级支持（自动激活档位 + 每次提示注入规则集），把 [`hooks/qoder-hooks.json`](hooks/qoder-hooks.json) 中的钩子加入你的 `.qoder/settings.json`。把 `PONYTAIL_DIR` 替换为你的 ponytail checkout 路径。Qoder 的 `UserPromptSubmit` 钩子会在首次提示时激活默认档位，并在每轮注入规则集；`PreToolUse` 配合 `task|Task` 匹配器会把规则集注入到子 agent。档位切换（`/ponytail lite|full|ultra|off`）会自动生效。

### Antigravity CLI

Google 正在把 Gemini CLI 更名为 Antigravity CLI（二进制名为 `agy`）；同一个扩展也能安装在那里：

```bash
agy plugin install https://github.com/DietrichGebert/ponytail
```

它复用本仓库的 `gemini-extension.json`。有一处差异：Antigravity 会把 `/ponytail` 系列命令转成技能，所以你是把命令作为消息输入聊天（例如发送 `/ponytail-review`），而不是从斜杠菜单里选。在迁移完成之前（大约 2026 年 6 月 18 日），`gemini extensions install` 也仍然可用。若想改为以常驻规则的方式运行，把规则集放进 `.agents/rules/`。

### Hermes Agent

```bash
hermes plugins install DietrichGebert/ponytail --enable
```

安装后重启 Hermes。插件会在每次 LLM 轮次前注入当前 Ponytail 档位，把内置技能注册为 `ponytail:<skill>`，并添加 `/ponytail`、`/ponytail-review`、`/ponytail-audit`、`/ponytail-debt`、`/ponytail-gain` 和 `/ponytail-help`。在共享网关中，请通过 Hermes 的斜杠命令访问控制把 `/ponytail` 限制为受信任用户；运行时档位是进程内隔离的。

### CodeWhale

读取项目根目录的 `AGENTS.md`，零配置。把 [`AGENTS.md`](AGENTS.md) 复制到你的项目，或在本仓库的 checkout 中运行 `codewhale`。就这样。

### Swival

先把整套内容暂存到你的库中，再添加你需要的技能：

```bash
swival skills add --global https://github.com/DietrichGebert/ponytail  # 暂存到 ~/.config/swival/library
swival skills add ponytail                                             # 安装到当前项目
swival skills add --global ponytail                                    # 或在所有项目中激活
```

Swival 也会读取项目根目录的 `AGENTS.md` 以及全局的 `~/.config/swival/AGENTS.md`，这是「纯指令」的兜底方式。

在命令行中，用 `$` 前缀显式激活某个技能。例如：`$ponytail-review`。

### Devin CLI

```bash
devin plugins install DietrichGebert/ponytail
```

把 ponytail 作为 Devin 插件安装；技能以 `/ponytail:ponytail`、`/ponytail:ponytail-review` 等形式提供。

### OpenClaw

```bash
clawhub install ponytail
```

从 ClawHub 把 ponytail 安装为 OpenClaw 技能；review、audit、debt、gain、help 技能同理（`clawhub install ponytail-review` 等）。OpenClaw 会在编码任务上自动应用它，同时提供一个 `/ponytail` 命令。如果没有 ClawHub，把 [`.openclaw/skills/ponytail`](.openclaw/skills/) 复制到 `~/.openclaw/skills/`。

### Grok Build

```bash
grok plugin install DietrichGebert/ponytail --trust
```

启用插件（默认关闭）：`/plugins` → Plugins → 在 `ponytail` 上按空格，或在 `~/.grok/config.toml` 中配置：

```toml
[plugins]
enabled = ["ponytail"]
```

开启新会话（或重载插件）。技能显示为 `/ponytail`、`/ponytail-review`、`/ponytail-audit`、`/ponytail-debt`、`/ponytail-gain`、`/ponytail-help`。用 `grok inspect` 验证。Grok 可以根据技能描述自动为编码任务调用 ponytail；当需要显式激活时，使用 `/ponytail`（或 `/ponytail lite`、`/ponytail full`、`/ponytail ultra`）。Grok 不使用生命周期钩子，因为其 SessionStart 输出无法注入指令。

`AGENTS.md` 仍然可以在没有插件的情况下，以纯指令方式从 checkout 生效。

就这样了。他会很骄傲。他不会说出来。

每个会话都处于激活状态，并带有少量命令（见[命令](#命令)）。`/ponytail ultra` 是为「这个代码库深深得罪了你」的时刻准备的。启动与档位切换时会显示当前档位。

用 `PONYTAIL_DEFAULT_MODE` 环境变量（`lite`/`full`/`ultra`/`off`）为每个新会话设置档位，或在 `~/.config/ponytail/config.json`（Windows 上是 `%APPDATA%\ponytail\config.json`）中设置 `defaultMode` 字段。默认值为 `full`。

激活状态下，规则集也会被注入到通过 Agent 工具派生出的每一个子 agent。若要把它限定到特定的 agent 类型（比如对只读搜索类 agent 关闭），把 `PONYTAIL_SUBAGENT_MATCHER` 环境变量设为一个针对子 agent `agent_type` 的正则。该正则不锚定、不区分大小写：`explore|general` 匹配两者，`^general$` 为精确匹配，插件 agent 类型形如 `plugin:name`。不设置表示注入到所有子 agent（默认行为）；正则非法、或平台未上报其类型的子 agent，也会回退为注入。

Cursor、Windsurf、Cline、GitHub Copilot Chat（VS Code、JetBrains、Visual Studio 的编辑器扩展，不是 [安装](#安装) 中那个独立的 Copilot CLI）、Aider、Kiro、Zed、CodeWhale、Swival、Qoder：从本仓库复制对应的规则文件（[`.cursor/rules/`](.cursor/rules/)、[`.windsurf/rules/`](.windsurf/rules/)、[`.clinerules/`](.clinerules/)、[`.github/copilot-instructions.md`](.github/copilot-instructions.md)、[`AGENTS.md`](AGENTS.md)、[`.kiro/steering/`](.kiro/steering/)、[`.qoder/rules/`](.qoder/rules/)）。

Kiro：把 `.kiro/steering/ponytail.md` 复制到 `~/.kiro/steering/`（全局）或你项目中的 `.kiro/steering/`。

GitHub Copilot CLI 兜底（纯指令模式）：它会读取项目中的 `AGENTS.md` 和 `.github/copilot-instructions.md`；也可以把规则复制到 `~/.copilot/copilot-instructions.md`，让 ponytail 在所有项目生效。这条路径保留常驻指导，但不提供插件档位切换或钩子。

带 Codex 扩展的 VS Code 会读取 `AGENTS.md`，本仓库已提供，因此从仓库根目录即可零配置生效（`~/.codex/AGENTS.md` 可让 Codex 全局生效）。

JetBrains Junie 需要你在 Settings → Tools → Junie → Project Settings → Guidelines Path 中指向它，才能读取 `AGENTS.md`（目前还不是自动的）。本仓库提供 `AGENTS.md`；`.junie/guidelines.md` 是 Junie 的旧路径。

Amp（Sourcegraph）会从工作目录及向上直到 `$HOME` 的父目录中读取 `AGENTS.md`，本仓库已提供，因此零配置生效（`~/.config/amp/AGENTS.md` 可全局生效）。

Jules（Google）会从仓库根目录读取 `AGENTS.md`，本仓库已提供，因此无需配置即可获得规则集。

哪个文件对应哪个 agent：[Agent portability](docs/agent-portability.md)。

### 卸载

| 宿主 | 命令 |
|------|---------|
| Claude Code | `/plugin remove ponytail` |
| Codex | `codex plugin remove ponytail` |
| Devin CLI | `devin plugins remove ponytail` |
| Grok Build | `grok plugin uninstall ponytail` |
| Pi agent | `pi uninstall ponytail` |
| Cursor / Windsurf / Cline / Qoder / 等 | 删除已复制的规则文件 |

这些命令只移除插件自身的文件。它们会残留少量 ponytail 写在插件目录外的状态：档位标记、`~/.config/ponytail/config.json`，以及（如果你接受了安装引导）`~/.claude/settings.json` 中的一条 `statusLine` 配置。运行 `node scripts/uninstall.js` 可以一并清理。**请在上面宿主卸载命令之前运行它**——该脚本本身也是插件文件，先移除插件会把它一起删掉（或者从本仓库的另一份 clone 中运行）。只有在 statusLine 指向 ponytail 自己的脚本时它才会移除该项，因此你自己设置的 statusline 不受影响。

## 命令

| 命令 | 作用 |
|---------|--------------|
| `/ponytail [lite \| full \| ultra \| off]` | 设置强度档位，或关闭它。不带参数则报告当前档位。 |
| `/ponytail-review` | 审查当前 diff 中的过度工程，交回一份「待删除清单」。 |
| `/ponytail-audit` | 审查整个仓库的过度工程，而不仅是 diff。 |
| `/ponytail-debt` | 把你推迟处理的 `ponytail:` 捷径收进一本台账，让「以后再说」不至于变成「永远不做」。 |
| `/ponytail-gain` | 展示基准测得的成效计分板（更少代码、更低成本、更快速度）。 |
| `/ponytail-help` | 上述命令的速查参考。 |

命令需要支持技能的宿主（Claude Code、Codex、Devin CLI、OpenCode、Gemini、pi、Swival、Hermes Agent、Qoder、Grok Build）。在 Codex 中它们是技能，用 `@` 调用（`@ponytail-review`）。纯指令适配器（Cursor、Windsurf、Cline、Copilot、Kiro、Antigravity）只加载常驻规则集，不提供命令。

## 开发

修改精简后的规则文本时，请保持各 agent 的副本一致：

```bash
node scripts/check-rule-copies.js
npm test
```

OpenClaw 技能包（`.openclaw/skills/`）由 `skills/` 生成；修改技能后需重新运行 `node scripts/build-openclaw-skills.js`，若产物过期，测试套件会失败。要把技能发布到 ClawHub，先运行一次 `clawhub login`，然后运行 `node scripts/publish-openclaw-skills.js`（它会以 `package.json` 中的版本发布全部六个，传 `--dry-run` 可预览）。

正确性基准测试会启动 Python 来做邮件与 CSV 校验；会优先尝试 `python3`，再尝试 `python`。CSV 校验需要本地已安装 `pandas`。

## 常见问题

**能和 [caveman](https://github.com/JuliusBrussee/caveman) 一起用吗？**
能，而且应该一起用。Caveman 缩减 agent **说的话**；ponytail 缩减它**构建的东西**。两者各管一半，毫不重叠：caveman 让代码逐字节保持精确，ponytail 不碰文字表述。用简短的话，谈最少的代码。

**需要配置文件吗？**
不需要。可选的 `~/.config/ponytail/config.json` 或 `PONYTAIL_DEFAULT_MODE` 环境变量可以设置默认档位，但没有任何必填项。

**可我真的需要那个 120 行的缓存类怎么办？**
你不需要。但你坚持的话，他会给你建。慢慢地。正确地。一边看着你。

**它能扩展吗？**
你从未写过的代码可以无限扩展。零 Bug、零 CVE、自开天辟地以来 100% 可用。

**为什么叫「ponytail」？**
你心里清楚为什么。

## 赞助方

<p align="center">
  <a href="https://greenpt.com/">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="assets/logo-greenpt-dark.svg">
      <img src="assets/logo-greenpt.svg" width="260" alt="GreenPT">
    </picture>
  </a>
</p>

## 许可证

[MIT](LICENSE)。能起作用的最短许可证。

## Star 历史

<a href="https://www.star-history.com/dietrichgebert/ponytail#history">
 <picture>
   <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/chart?repos=DietrichGebert/ponytail&type=Date&theme=dark" />
   <source media="(prefers-color-scheme: light)" srcset="https://api.star-history.com/chart?repos=DietrichGebert/ponytail&type=Date" />
   <img alt="Star History Chart" src="https://api.star-history.com/chart?repos=DietrichGebert/ponytail&type=Date" />
 </picture>
</a>
