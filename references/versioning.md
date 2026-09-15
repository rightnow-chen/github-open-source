# 版本迭代约定

本仓库（及 `ai-complaint-scout`）的迭代遵循轻量规范，方便日后追溯和回滚。

## 迭代三件事

1. **改**：直接在 `~/.workbuddy/skills/<skill>/` 里改文件
2. **提交 + 推送**：说一句「把 XX 的改动推上去」，由 Agent 执行 `git add -A` + `git commit` + `git push origin main`
3. **拉取**：换电脑时 `git clone` 后 `cp -r` 到 skills 目录

## Commit Message 规范

采用 Conventional Commits 前缀：

| 前缀 | 用途 | 示例 |
|---|---|---|
| `feat:` | 新增功能 | `feat: 新增 App Store 评论数据源` |
| `fix:` | 修复 bug | `fix: 修复 gen_queries 不识别三级标题` |
| `docs:` | 文档/README 改动 | `docs: 更新排错手册` |
| `refactor:` | 重构（不改功能） | `refactor: 拆分抓取模块` |
| `chore:` | 杂项（依赖、配置） | `chore: 更新 .gitignore` |

## 版本号约定（SemVer）

`主版本.次版本.修订`：
- **修订**（1.0.x）：修 bug、小补丁
- **次版本**（1.x.0）：加新功能、加新数据源
- **主版本**（x.0.0）：颠覆性重构、不兼容改动

当前两个 skill 均为 `1.0.0`，版本号记在各自 `SKILL.md` 的 frontmatter `version:` 字段，改动时同步更新。

## 铁律

- **永远用 `main` 分支**，不在本地开 feature 分支（单人项目没必要）
- **不 `--force` 推送**，不覆盖远程历史
- **推送前 `git status` 自查**，确保没有敏感文件（token/密钥/.env）
- **凭证目录 `.gh-config/` 永不进仓库**（已在 .gitignore 中）
