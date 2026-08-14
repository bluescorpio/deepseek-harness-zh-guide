# DeepSeek Harness 中文速查手册（v0.1 预览版）

> 发布 2026-08-13 深夜 ｜ 本文写于发布后 24h 内，官方文档尚未补全（文档真空期）
> 信息核对自 npm 官方包 [`@deepseek-ai/dsh`](https://www.npmjs.com/package/@deepseek-ai/dsh) 与 GitHub 仓库提交记录
> English TL;DR: DeepSeek open-sourced an MIT-licensed agent runtime ("Everything is a Plugin") that rivals Claude Code / Codex. Verified install + commands below.

---

## 这是什么（一句话）

DeepSeek 于 **2026-08-13 深夜**发布的**开源 Agent 运行时框架**，口号 *"Everything is a Plugin / 一切皆插件"*，直接对标 Claude Code 与 OpenAI Codex，MIT 协议全开源。官方公式：**Model + Harness = Agent**——模型负责推理，Harness 负责工具调用、任务规划、上下文管理等全部工程化执行。

**为什么现在写**：npm 包 14 小时前才公开，GitHub README 正文目前还是空的文件树。本文在发布后 24–72h 的文档真空期内，先把**已确认的命令和踩坑**列出来，凡是官方没写清的都标「待确认」，不编假配置。

---

## 已确认事实（来源：npm + GitHub commits）

| 项 | 内容 |
|---|---|
| 包名 | `@deepseek-ai/dsh`（**注意带 `@` 作用域**，别写成 `deepseek-ai/dsh`） |
| 版本 | `0.1.0-rc.6`，Public，发布约 14 小时前 |
| 协议 | MIT |
| 仓库 | `github.com/deepseek-ai/deepseek-harness` |
| 核心理念 | Everything is a Plugin，基于 **Cordis** 插件运行时 |
| 内置 bundle | `@deepseek-ai/dsh-base`、`@deepseek-ai/dsh-web-app`、`@deepseek-ai/dsh-headless` |

---

## 五分钟上手（已验证命令）

前置：装好 Node.js（LTS 即可）。

```bash
# 方式 A：免安装直接跑（推荐先试）
npx @deepseek-ai/dsh web

# 方式 B：全局安装后用 dsh 命令
npm i -g @deepseek-ai/dsh
dsh web
```

`dsh web` 是 `--profile web` 的别名。首次启动会从内置模板**自动初始化**，终端会打印本地访问地址。浏览器打开后三步：

1. 填入 DeepSeek API Key（去 `platform.deepseek.com` 创建，`sk-` 开头，**只显示一次，先复制**）；
2. 若环境变量已有 `DEEPSEEK_API_KEY` 可跳过这步；
3. **必须选一个工作区（workspace）目录**，否则输入框发不出消息——用空目录练手最安全，别直接挂真实项目。

---

## 全部已确认命令（来自 npm 官方说明）

| 命令 | 作用 |
|---|---|
| `dsh web` | 启动 Web UI（= `--profile web`），默认上手路径 |
| `dsh --profile <name>` | 启动 `$DSH_HOME/profiles/<name>` 下的命名 profile |
| `dsh --profile headless "任务"` | 跑一次无头会话，打印最终答案后退出，适合脚本 / CI |
| `dsh plugin --profile <name> <pnpm参数>` | 用 pnpm 管理某 profile 的插件 |
| `dsh --profile web --port 8080` | 给 Web App 传参（launcher 只解析自己的 flag，其余转交 profile） |
| `dsh --dump-default-config` / `--dump-config` | 不启动，直接查看组合后的配置树 |

> 调用目录默认就是 workspace 根目录。除 `web` / `headless` 外，其他 profile 必须通过 `dsh plugin` 创建。

---

## 架构速记（已确认）

- **Profile = 有序的插件包 patch 层叠加**：
  `dsh.profile.bundles`（按序）→ profile 的 `cordis.patch.yml` → 家目录 `$DSH_HOME/cordis.patch.yml` → `--patch` 覆盖层。
- 一个 profile 目录含三件套：`package.json`（外加插件依赖）+ `dsh.profile`（manifest，含 `bundles` 列表）+ `cordis.patch.yml`（用户自己的 patch 层）。
- 想看最终组合结果，用 `--dump-default-config` / `--dump-config`，不用真启动。

---

## 踩坑清单（重点）

1. **v0.1 预览版，破坏性更新随时来**——官方明说后续可能改到不兼容，别上生产。
2. **包名带 `@`**：`npx @deepseek-ai/dsh web`，写成 `npx deepseek-ai/dsh web` 会找不到包。
3. **必须先选 workspace**，否则输入框发不出消息、发送键是灰的。
4. **API Key 用环境变量最省事**：`export DEEPSEEK_API_KEY=sk-...`，免去网页配置。
5. **沙箱 / 远程访问 / 消息平台接入还不完善**，云端部署 + 多平台等稳定再迁。
6. 中文文档在 `apps/cli/README.zh.md`，但截至发稿 README 正文还是空的——**这就是真空期本身**。

---

## 社区流传、待官方确认的点（先标着，别当真）

- 四种运行模式「**标准 / PTC（程序化工具调用，可用 TS 写流程）/ 极简 / 创造**」—— 官方文档未列，可能对应不同 profile / preset。
- 支持约 **40 家第三方模型**（OpenAI / Anthropic / Google 等），不强绑 DeepSeek。
- 内测 **769 名开发者、约 300 个社区插件**。
- 负责人崔添翼（崔哥），6 枚 ACM 金牌、前 Jane Street，2026-03 加入 DeepSeek。

---

## 战略一句话

DeepSeek 不满足于只当"被调用的模型"，要抢模型之上的**开发者入口**。MIT 全开源是在对开发者喊"别活在 Anthropic 统治下"。窗口期内容红利就在眼前——但官方文档补全后，这类速查帖的 SEO 价值会快速衰减，**今天发最划算**。

---

*由 Edy 整理 · 更多 AI 工具出海实战见 [howdoaiagentswork.com](https://howdoaiagentswork.com) 与 [xiaohongshudownloader.com](https://xiaohongshudownloader.com)*
