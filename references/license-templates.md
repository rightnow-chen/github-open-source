# 开源许可证模板

## MIT License（默认，工具类最常用）

```
MIT License

Copyright (c) 2026 <你的名字或项目名>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

**特点**：最宽松。允许任何人使用、修改、分发、商用，只需保留版权声明。

---

## Apache-2.0（含专利授权条款）

完整文本：https://www.apache.org/licenses/LICENSE-2.0.txt

**特点**：宽松 + 明确的专利授权，适合有一定规模、关注专利的项目。

---

## GPL-3.0（传染性强）

完整文本：https://www.gnu.org/licenses/gpl-3.0.txt

**特点**：copyleft，衍生作品也必须开源。适合想强制下游开源的场景。

---

## 选择建议

| 场景 | 推荐 |
|---|---|
| 工具 / 脚本 / Skill | MIT |
| 库 / 框架（希望广泛传播） | MIT 或 Apache-2.0 |
| 想强制衍生品开源 | GPL-3.0 |
| 暂不确定 | MIT（最安全、最常用） |

---

## .gitignore 通用模板

```
# 运行时中间产物
raw/
build/
dist/
*.log
__pycache__/
*.pyc

# 虚拟环境 / 依赖
.venv/
venv/
node_modules/

# 环境变量 / 凭证（千万别提交）
.env
.env.*
.gh-config/
*.pem
*.key

# 系统文件
.DS_Store
Thumbs.db
```
