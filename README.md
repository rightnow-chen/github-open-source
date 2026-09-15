# GitHub 开源助手 · github-open-source

> 把本地项目（Skill / 工具 / 代码库）开源到 GitHub 的标准化流程，一条龙搞定。

适配 **Windows + WorkBuddy 沙箱环境**，内置国内环境高频坑的排错手册。

这是一个 **WorkBuddy / Claude Code 等 Agent 可调用的 Skill**。

---

## 它能做什么

| 能力 | 说明 |
|---|---|
| 🛠 环境准备 | 装 gh CLI、配 git 身份、补 README/LICENSE/.gitignore |
| 🌐 网络诊断 | 识别并解决 sing-box/Clash 代理半死状态（Fake-IP 劫持）问题 |
| 🔐 gh 登录 | 设备码登录，含沙箱拦截 `hosts.yml` 的绕过方案 |
| 🚀 一键发布 | `gh repo create` 建库 + 推送，输出仓库 URL |

---

## 快速开始

### 作为 Skill 调用

```bash
cp -r github-open-source ~/.workbuddy/skills/   # WorkBuddy
# 或 ~/.claude/skills/  # Claude Code
```

对话中说：
> 用 github-open-source 把 XX 项目开源到 GitHub

### 核心流程（6 步）

1. 确认项目信息（路径 / 仓库名 / 可见性 / 许可证）
2. 环境准备（gh CLI + git 身份 + 必备文件）
3. `git init` + 首次提交
4. 网络诊断（国内环境）
5. gh 登录（沙箱配置目录）
6. `gh repo create` 一键建库推送 + 安全收尾

---

## ⚠️ 两个关键坑（已内置排错）

1. **sing-box/Clash TUN 模式半死**：DNS 解析到 Fake-IP 段（`198.18.x.x`）但流量不通，表现为 HTTP 000 / SSL 握手失败。解法：重启代理或彻底退出。
2. **WorkBuddy 沙箱拦截 `~/.config/gh/hosts.yml`**：授权成功但凭证存不下。解法：`export GH_CONFIG_DIR=<可写目录>`。

完整排错手册见 `references/troubleshooting.md`。

---

## 目录结构

```
github-open-source/
├── SKILL.md                          # 主入口（6 步流程 + 约束）
├── README.md                         # 本文件
├── LICENSE                           # MIT
└── references/
    ├── license-templates.md          # MIT/Apache-2.0/GPL-3.0 许可证模板 + .gitignore
    └── troubleshooting.md            # 8 类高频报错排错手册
```

---

## 依赖

- git（Git for Windows）
- gh CLI（GitHub 官方命令行工具）

## License

[MIT](./LICENSE)
