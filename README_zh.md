# AI PPT 生成器

[English](README.md) | [中文](README_zh.md)

构建一个受 NotebookLM 启发的可控、可编辑、可自定义模型 AI PPT 工作台。AIPPT 支持将论文、文档等资料转换为演示文稿，并导出 PDF、栅格 PPTX 或可编辑 PPTX；可编辑 PPTX 中的文字可直接修改，线条/表格边框可作为原生形状编辑，背景、图标和图片元素也会拆分为可移动的独立资产。

![AIPPT 工作台演示](docs/assets/aippt-demo.gif)

[查看高清演示视频](docs/assets/aippt-demo.webm)

演示视频覆盖上传 `doc/L9.md`、填写用户要求、生成并编辑设计大纲、确认逐页设计、生成 6 页 PPT、单页编辑、确认替换、导出 PDF/PPTX；模型等待阶段已做快进剪辑。

## 最新更新：可编辑 PPTX 导出

现已支持可编辑 PPTX 导出。生成的幻灯片图片可以进一步重建为 PowerPoint/WPS 可编辑页面：OCR 文本会变成可编辑文本框，简单线条和表格边框会尽量转为 PPT 原生形状，背景、图标、插图和视觉组件会拆分为可移动、可缩放、可隐藏的独立图片资产，而不是整页保留为一张大图。

| 生成的 PPT 图片 | 转为可编辑 PPTX 后 |
| :---: | :---: |
| ![生成的 PPT 图片](docs/assets/gen_ppt_pic.png) | ![转为可编辑 PPTX 后的拆分元素效果](docs/assets/gen_ppt_edit.png) |

## 为什么不只是复刻

NotebookLM 的 PPT 能力更像“一键生成结果”，中间设计过程和单页调整空间相对有限。本项目把生成链路拆成用户可理解、可干预的工作台流程：

- **过程可见**：先展示 PPT 大纲和逐页设计说明，用户确认后再生成页面
- **逐页可改**：每一页都能单独编辑、生成新版本、回退历史并确认替换
- **模型可控**：文本规划、多模态理解、OCR、生图、图片编辑可分别配置不同 OpenAI-compatible 模型
- **本地可跑**：可使用本地 `config.yaml` 或 WebUI 本地 API 配置管理模型连接，项目记录和导出文件不包含 API Key
- **结果可导出**：生成后可直接导出 PDF、栅格 PPTX 或可编辑 PPTX，适合继续汇报或二次编辑
- **可编辑 PPTX 重建**：通过 provider 门禁的重建链路，把幻灯片图片重建为 PowerPoint 可编辑文本框、原生线条/表格边框和可移动图片资产

## ✨ 功能特性

- 🎨 **逐页生成**：先生成可编辑设计大纲和逐页设计，再使用 AI 模型生成精美 PPT 页面
- 🌐 **PPT 工作台**：支持资料上传、模型配置、当前页大预览、缩略图列表、编辑历史，以及 PDF、栅格 PPTX 或可编辑 PPTX 导出
- 📝 **多格式解析**：支持 `.md/.txt/.pdf/.docx/.pptx` 输入，统一转 Markdown 后生成
- ✏️ **整页图像编辑**：支持对每页幻灯片单独二次编辑、历史回退和确认替换
- 🔀 **多模型角色**：按需分别配置 `text_model`/`prompt_model`、`vlm_model`、`ocr_model`、`image_model`、`edit_model`
- 🖼️ **图像结果兼容**：兼容 URL、Markdown 图片链接、data URL、`b64_json` 和纯 base64
- 💾 **多项目本地留存**：支持在浏览器本地保存多个 PPT 项目，恢复资料、设计大纲、逐页设计、生成页面和单页编辑历史
- 📤 **可编辑 PPTX 重建**：通过 provider 验证后导出文字可编辑、图片资产可移动、简单形状可原生编辑的 PPTX，详见 [Generative Editable PPTX Export](docs/generative-editable-pptx.md)

## 🚀 快速开始

### 1. 安装配置

```bash
# 克隆项目
git clone <repository-url>
cd OpenNotebookLM-AIPPT

# 配置 API 密钥
cp config.example.yaml config.yaml
# 编辑 config.yaml，填入你的 API 密钥
```

### 2. 启动服务

**方式一：WebUI 界面（推荐）**

```bash
# 一键启动前后端
./start.sh
```

启动后访问：
- 🎨 前端界面: http://localhost:5173
- 📚 API 文档: http://localhost:8000/docs

**方式二：分别启动前后端**

```bash
# 终端 1：启动后端
./start-api.sh

# 终端 2：启动前端
cd web && npm install && npm run dev
```

**方式三：命令行使用**

```bash
# 安装依赖
pip install -r requirements.txt

# 基础用法
python main.py -i doc/L9.md -n 5

# 仅生成 Prompt
python main.py -i doc/L9.md -n 5 --prompt-only -o prompts.json

# 从 Prompt 文件生成
python main.py --from-prompt prompts.json
```

### 本地项目保存

AIPPT 会把项目内容和图片资源保存在当前浏览器 Profile 的 IndexedDB 中，并用 localStorage 保存当前打开的项目 ID、界面偏好和本地 API 配置。项目保存内容包括上传资料、PPT 内容设置、设计大纲、逐页设计、生成页面、编辑后的版本和导出所需图片资产。

注意：
- 清理浏览器站点数据会删除本地项目。
- 换浏览器或换设备不会自动同步项目。
- API Key 属于本地 API 配置，不会写入已保存的项目记录，也不会包含在导出的 PDF/PPTX 文件中。

### 3. WebUI 使用流程

1. **上传文档**：在左侧面板拖拽或点击上传资料文件
2. **配置模型**：在中间面板按需配置文本、多模态理解/OCR、生图和编辑模型
3. **设置参数与要求**：选择页数、清晰度、比例、语言、风格、受众，并填写用户定制要求
4. **确认设计**：先生成设计大纲，用户可编辑后确认，再生成逐页设计预览
5. **生成 PPT**：确认逐页设计后生成 PPT 页面，实时查看进度
6. **预览编辑**：在右侧面板预览生成的幻灯片，点击可进行单页编辑
7. **导出文件**：选择 PDF、栅格 PPTX 或可编辑 PPTX 格式导出

导出菜单会保留原有栅格 PPTX 选项，并新增独立的可编辑 PPTX 选项。可编辑模式依赖 VLM/OCR、图像编辑和图像生成 provider，将幻灯片重建为可编辑文本框、PPT 原生简单形状和拆分后的图片资产。该模式默认优先保证质量：验证失败时返回错误，不会悄悄降级为低保真 PPTX，除非请求显式允许 fallback。详见 [Generative Editable PPTX Export](docs/generative-editable-pptx.md)。

仓库内置演示资料为 `doc/L9.md`。该路径是仓库相对路径，clone 后可直接用于 WebUI 上传或命令行示例。

## Agent Skill 使用

AIPPT 提供三种可直接使用的 Agent Skill。无需部署 WebUI，即可让 AI 编程 Agent 调用现有命令行流程生成并检查演示文稿。

| 格式 | 目录 | 适用场景 |
| --- | --- | --- |
| 通用 Agent Skill | `skills/universal/aippt/` | 兼容 Agent Skills 规范的工具 |
| Claude Code | `skills/claude-code/aippt/` | Claude Code 项目级或个人 Skill |
| Codex | `skills/codex/aippt/` | Codex 项目级或个人 Skill |

按所用 Agent 安装对应版本：

```bash
# Claude Code：当前项目
mkdir -p .claude/skills
cp -R skills/claude-code/aippt .claude/skills/aippt

# Codex：当前项目
mkdir -p .agents/skills
cp -R skills/codex/aippt .agents/skills/aippt

# Codex：个人目录（可选）
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
cp -R skills/codex/aippt "${CODEX_HOME:-$HOME/.codex}/skills/aippt"
```

安装后可直接告诉 Agent：`使用 AIPPT 把 doc/L9.md 生成一份 8 页中文演示文稿。`

这些 Skill 会调用现有 `main.py` 流程，并在完成后检查输出文件。模型密钥只允许保存在已被 Git 忽略的本地 `config.yaml` 中，不需要也不应写入提示词或命令行。

## 📁 项目结构

```
OpenNotebookLM-AIPPT/
├── src/                    # 核心逻辑
├── api/                    # FastAPI 后端
├── web/                    # React 前端
├── skills/                 # 通用、Claude Code 与 Codex Agent Skill
├── tests/                  # 测试
├── doc/                    # 输入文档目录
│   └── L9.md               # 默认演示资料
├── config.yaml             # 配置文件
├── start.sh                # 一键启动脚本
└── main.py                 # 命令行入口
```

## ⚙️ 配置说明

所有配置统一在 `config.yaml` 中管理，包括：
- API 配置（`text_model`/`prompt_model`、`vlm_model`、`ocr_model`、`image_model`、`edit_model`）
- 生成式可编辑 PPTX provider 角色和质量门禁（视觉分析、OCR、清理、资产生成、修复、验证）
- PPT 默认配置（语言、风格、页数）
- 超时和重试配置

详细配置示例请参考 `config.example.yaml`。

### 使用 OpenAI 兼容 API

当前调用协议为 OpenAI-compatible `/chat/completions`：文本模型走 chat
completion，图像/编辑模型走多模态 chat completion，响应需返回图片 URL、data URL
或 base64。

```yaml
api:
  models:
    # `prompt_model` 仍可作为 `text_model` 的旧字段别名使用。
    text_model:
      model: "gpt-4o"
      base_url: "https://api.openai.com/v1"
      api_key: "sk-xxx"
    vlm_model:
      model: "gpt-4o"
      base_url: "https://api.openai.com/v1"
      api_key: "sk-xxx"
    ocr_model:
      model: "ocr-model"
      base_url: "https://api.example.com/v1"
      api_key: "sk-xxx"
    image_model:
      model: "gpt-image-2"
      base_url: "https://api.example.com/v1"
      api_key: "sk-xxx"
    edit_model:
      model: "gpt-image-2"
      base_url: "https://api.example.com/v1"
      api_key: "sk-xxx"
```

## 📤 输出结构

```
output/ppt_20241201_123456/
├── source_material.txt      # 原始输入资料
├── prompts.json             # 生成的 Prompt
├── result.json              # 生成结果
├── presentation.pdf         # 导出的 PDF
├── presentation.pptx        # 导出的栅格或可编辑 PPTX
└── images/                  # 生成页面图片和拆分资产
```

## 🧪 开发检查

推荐本地运行环境：
- Python 3.11 或 3.12
- Node.js 20

后端检查：

```bash
python -m pip install -r requirements-dev.txt
ruff check api src tests main.py
ruff format --check api src tests main.py
python -m pytest --cov=api --cov=src --cov-report=term-missing
```

前端检查：

```bash
cd web
npm ci
npm run lint
npm run test
npm run build
```

GitHub Actions 会在 `main` 和 `dev` 的 Pull Request 与 push 上运行这些默认检查。真实模型 API 调用和桌面 PowerPoint/WPS 渲染需要私有 Key 或 GitHub runner 中不存在的宿主应用，因此不放入默认 CI。CI 仍会验证包级 PPTX 结构、对象 manifest、渲染器 contract 和 fake-provider 重建路径。

## 📋 TODO

- [ ] 支持框选局部区域编辑
- [ ] 持续优化复杂页面的可编辑 PPTX 元素分组、层级顺序和原生形状覆盖率
- [ ] 增加更多 VLM、OCR、生图和图片编辑 provider 预设

## 致谢

可编辑 PPTX 重建设计参考了以下项目的思路：
- [slide-alchemy](https://github.com/CodingFeng101/slide-alchemy)
- [image-to-editable-ppt-skill](https://github.com/ningzimu/image-to-editable-ppt-skill)

## 📄 许可证

Apache License 2.0
