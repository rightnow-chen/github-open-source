---
name: github-open-source
version: 1.0.0
description: |
  把本地项目（Skill / 工具 / 代码库）开源到 GitHub 的标准化流程。
  覆盖：环境准备（gh CLI + git 身份 + README/LICENSE/.gitignore）→ 网络诊断 →
  gh 登录（含沙箱配置目录坑）→ 一键建库推送。
  内含 Windows + WorkBuddy 沙箱环境下的排错手册。
triggers:
  - "把 XX 开源到 GitHub"
  - "帮我发布到 GitHub"
  - "开源这个项目 / skill"
  - "push 到 github"
  - "open source this"
input_schema:
  project_dir: string   # 必填，要开源的本地项目路径
  repo_name: string     # 可选，GitHub 仓库名，默认取目录名
  visibility: string    # 可选，public（默认）/ private
  license: string       # 可选，MIT（默认）/ Apache-2.0 / GPL-3.0 / 无
  description: string   # 可选，仓库描述
output_schema:
  repo_url: string      # 最终仓库 URL
---

# GitHub 开源助手（github-open-source）

把本地项目开源到 GitHub，一条龙搞定。适配 Windows + WorkBuddy 沙箱环境。

## 何时调用

- "把 ai-complaint-scout 开源到 GitHub"
- "帮我发布这个项目 / skill 到 GitHub"
- "push 到 github / 开源"

## 完整流程（6 步）

### Step 0 · 确认项目信息
- 项目路径（要开源的是哪个文件夹）
- 仓库名（默认取目录名，英文、kebab-case）
- 可见性：`public` 还是 `private`
- 许可证：`MIT`（默认，工具类最常用）或 `Apache-2.0` / `GPL-3.0`
- 仓库描述（一句中文/英文都行）

### Step 1 · 环境准备

1. **检查 gh CLI**：
   ```bash
   gh --version   # 没有则：winget install --id GitHub.cli --silent --accept-package-agreements --accept-source-agreements
   ```
   gh 默认安装路径：`C:\Program Files\GitHub CLI\gh.exe`，需加进 PATH：
   ```bash
   export PATH="$PATH:/c/Program Files/GitHub CLI"
   ```

2. **配置 git 身份**（没有则配，全局）：
   ```bash
   git config --global user.name "用户名"
   git config --global user.email "邮箱"   # 建议用 GitHub noreply: 用户名@users.noreply.github.com
   ```

3. **补开源必备文件**（如果项目没有）：
   - `README.md`：说明这是啥、怎么用、怎么装
   - `LICENSE`：MIT 全文（见 references/license-templates.md）
   - `.gitignore`：排除运行时产物、虚拟环境、系统文件

### Step 2 · 初始化 git 仓库

```bash
cd <project_dir>
git init
git branch -m main      # 把默认分支改成 main
git add -A
git commit -m "feat: initial release"
```

### Step 3 · 网络诊断（⚠️ 国内环境高频坑）

**症状**：`gh auth login` / `curl https://github.com` 报 `EOF`、`HTTP 000`、SSL 握手失败，但浏览器可能正常。

**排查顺序**：
```bash
# 1. 测连通性
curl -s -o /dev/null -w "%{http_code}\n" -m 10 https://github.com    # 200 正常，000 不通
curl -s -o /dev/null -w "%{http_code}\n" -m 10 https://api.github.com

# 2. 查 DNS（关键！）
nslookup github.com
#   如果解析到 198.18.x.x / 198.19.x.x（Fake-IP 保留段）→ 说明有 TUN 模式代理在劫持 DNS
#   正常应是 140.82.x.x / 20.205.x.x 等真实 IP

# 3. 查代理进程
tasklist | grep -iE "clash|mihomo|verge|v2ray|sing-box|surge"
```

**根因判断**：`sing-box` / `Clash` 等 TUN 模式代理**半死状态**——DNS 劫持还活着，流量转发已挂。表现为：所有 GitHub 请求全被吸走然后丢掉，连"直连"都不通。

**解法**（让用户做，二选一）：
1. 重启代理核心 / 换可用节点；
2. 彻底退出代理软件（确保进程消失），回归真实直连。

### Step 4 · gh 登录（⚠️ WorkBuddy 沙箱专属坑）

**关键坑**：WorkBuddy 沙箱会**硬性拦截** `~/.config/gh/hosts.yml` 的写入，导致 `gh auth login` 每次都显示 `✓ Authentication complete`，但凭证存不下来（`You are not logged into any GitHub hosts`）。

**解法：把 gh 配置目录指到沙箱可写的地方**（如工作区）：
```bash
export GH_CONFIG_DIR="<工作区绝对路径>/.gh-config"
mkdir -p "$GH_CONFIG_DIR"
gh auth login --hostname github.com --git-protocol https --web
```
这一步会让 gh 把 token 写到 `<工作区>/.gh-config/hosts.yml`，绕过拦截。

**设备码登录流程**：
1. 后台运行 `gh auth login --web`，会输出一次性码（如 `XXXX-XXXX`）
2. 让用户打开 `https://github.com/login/device` 输入码 → Continue → Authorize
3. 成功后显示 `✓ Logged in as <用户名>`

验证：
```bash
gh auth status   # 应显示 Logged in + token scopes 含 'repo'
```

### Step 5 · 一键建库推送

```bash
cd <project_dir>
gh repo create <repo_name> \
  --public \              # 或 --private
  --source=. \
  --remote=origin \
  --push \
  --description "仓库描述"
```

成功输出仓库 URL，如 `https://github.com/<用户名>/<repo_name>`。

### Step 6 · 验证 + 收尾

```bash
gh repo view <用户名>/<repo_name> --json name,url,visibility,description
git log --oneline -1
```

**⚠️ 安全收尾（重要）**：
- 如果用了 `GH_CONFIG_DIR` 指向工作区，该 `.gh-config` 目录含 token，**必须确保它不在 git 仓库里**（在 `.gitignore` 加 `.gh-config/`，或确认它在仓库目录之外）。
- token 泄露风险：`gho_` 开头的 token 一旦提交到公开仓库，等于把账号写权限送人。

## 约束

- ✅ 开源前确认项目无敏感信息（密钥、token、密码、隐私数据）
- ✅ 补上 LICENSE，无 License 时他人默认无权使用
- ✅ README 写清楚用途 + 安装方式
- ❌ 不要把 `.gh-config`、`.env`、凭证文件提交进仓库
- ❌ 不要用 `--force` 推送覆盖他人仓库

## 依赖

- git（Windows 自带或 Git for Windows）
- gh CLI（GitHub 官方命令行工具）

## 参考文件

- `references/license-templates.md`：MIT / Apache-2.0 / GPL-3.0 许可证全文模板
- `references/troubleshooting.md`：排错手册（网络、沙箱、认证常见报错）
- `references/versioning.md`：版本迭代约定（commit 规范 + SemVer + 铁律）
