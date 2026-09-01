# AI PPT Generator

[English](README.md) | [中文](README_zh.md)

Build a controllable, editable, model-configurable AI PPT workbench inspired by NotebookLM. AIPPT converts papers, documents, and other materials into presentation decks, then exports them as PDF, raster PPTX, or editable PPTX with editable text, native lines/table borders, and separated image assets.

![AIPPT workbench demo](docs/assets/aippt-demo.gif)

[Watch the HD demo video](docs/assets/aippt-demo.webm)

The demo covers uploading `doc/L9.md`, entering custom requirements, generating and editing the design outline, confirming page designs, generating a 6-slide deck, editing one slide, confirming the replacement, and exporting PDF/PPTX. Model waiting time is fast-forwarded.

## Latest Update: Editable PPTX Export

Editable PPTX export is now supported. Generated slide images can be reconstructed into PowerPoint-editable pages: OCR text becomes editable text boxes, simple lines and table borders are rebuilt as native shapes, and backgrounds, icons, illustrations, and visual components are separated into movable image assets instead of remaining one full-page bitmap.

| Generated PPT image                                      | After conversion to editable PPTX                                                     |
| :--------------------------------------------------------: | :------------------------------------------------------------------------------------: |
| ![Generated PPT image](docs/assets/gen_ppt_pic.png)      | ![Converted editable PPTX with separated elements](docs/assets/gen_ppt_edit.png)      |

## More Than A Clone

NotebookLM's PPT feature is closer to a one-click result generator, with limited visibility into the design process and limited per-slide control. This project turns the workflow into an understandable, editable workbench:

- **Visible process**: Review the deck outline and page-by-page design notes before slide generation
- **Per-slide control**: Edit any slide independently, generate new versions, revert history, and confirm replacements
- **Model control**: Configure separate OpenAI-compatible models for text planning, visual understanding, OCR, image generation, and image editing
- **Local-first config**: Manage model connections through local `config.yaml` or WebUI local API configuration; saved projects and exported files do not include API keys
- **Export-ready output**: Export generated decks to PDF, raster PPTX, or editable PPTX for presentation and downstream editing
- **Editable PPTX reconstruction**: Rebuild slide images into editable PowerPoint text boxes, native line/table-border shapes, and positioned bitmap assets through a provider-gated reconstruction path

## ✨ Features

- 🎨 **Per-slide generation**: Create an editable outline and page designs before generating polished PPT pages
- 🌐 **PPT Workbench**: Upload sources, configure model roles, preview slides, edit pages, track history, and export PDF, raster PPTX, or editable PPTX
- 📝 **Multi-format parsing**: Supports `.md/.txt/.pdf/.docx/.pptx` input and converts content to Markdown
- ✏️ **Full-page image editing**: Edit each generated slide independently, revert history, and confirm replacements
- 🔀 **Multi-model roles**: Configure `text_model`/`prompt_model`, `vlm_model`, `ocr_model`, `image_model`, and `edit_model` separately as needed
- 🖼️ **Image result compatibility**: Accepts URLs, Markdown image links, data URLs, `b64_json`, and raw base64
- 💾 **Local multi-project persistence**: Save multiple PPT projects in the browser, including source content, outline, page designs, generated slides, and per-slide edit history
- 📤 **Editable PPTX reconstruction**: Export editable PPTX with editable text, separated image assets, and native simple shapes after provider verification; see [Generative Editable PPTX Export](docs/generative-editable-pptx.md)

## 🚀 Quick Start

### 1. Installation & Configuration

```bash
# Clone the project
git clone <repository-url>
cd OpenNotebookLM-AIPPT

# Configure API keys
cp config.example.yaml config.yaml
# Edit config.yaml and fill in your API keys
```

### 2. Start Services

**Option 1: WebUI Interface (Recommended)**

```bash
# One-click start for both frontend and backend
./start.sh
```

After startup, visit:
- 🎨 Frontend: http://localhost:5173
- 📚 API Docs: http://localhost:8000/docs

**Option 2: Start Frontend and Backend Separately**

```bash
# Terminal 1: Start backend
./start-api.sh

# Terminal 2: Start frontend
cd web && npm install && npm run dev
```

**Option 3: Command Line Usage**

```bash
# Install dependencies
pip install -r requirements.txt

# Basic usage
python main.py -i doc/L9.md -n 5

# Generate prompts only
python main.py -i doc/L9.md -n 5 --prompt-only -o prompts.json

# Generate from prompt file
python main.py --from-prompt prompts.json
```

### Local Project Persistence

AIPPT stores project content and image assets in the current browser profile's IndexedDB, and uses localStorage for the active project id, UI preferences, and local API configuration. Saved project data includes uploaded sources, content settings, design outlines, page designs, generated pages, edited versions, and image assets needed for export.

Notes:
- Clearing browser site data removes local projects.
- Projects do not automatically sync across browsers or devices.
- API keys belong to local API configuration; they are not written into saved project records and are not included in exported PDF/PPTX files.

### 3. WebUI Usage Flow

1. **Upload Document**: Drag and drop or click to upload a source file in the left panel
2. **Configure Models**: Configure text, visual understanding/OCR, image generation, and image editing model roles as needed
3. **Set Parameters & Requirements**: Choose page count, resolution, aspect ratio, language, style, audience, and custom requirements
4. **Confirm Design**: Generate an editable outline, confirm it, then review the generated page designs
5. **Generate PPT**: Generate slide pages after page-design confirmation and watch real-time progress
6. **Preview & Edit**: Preview generated slides in the right panel and edit a single page when needed
7. **Export**: Export to PDF, raster PPTX, or editable PPTX

The export menu keeps the existing raster PPTX option and adds a separate editable PPTX option. Editable mode uses VLM/OCR plus image editing/image generation providers to reconstruct slides into editable text boxes, native simple shapes, and separated bitmap assets. It is quality-gated by default and fails rather than silently returning a low-fidelity deck unless an explicit fallback policy is requested. See [Generative Editable PPTX Export](docs/generative-editable-pptx.md).

The built-in demo source is `doc/L9.md`. This is a repository-relative path, so a fresh clone can use it directly in the WebUI or CLI examples.

## Agent Skills

AIPPT includes ready-to-use Agent Skills for running presentation generation directly from an AI coding agent without deploying the WebUI.

| Format | Directory | Intended use |
| --- | --- | --- |
| Universal Agent Skill | `skills/universal/aippt/` | Agent Skills-compatible tools |
| Claude Code | `skills/claude-code/aippt/` | Project or personal Claude Code skills |
| Codex | `skills/codex/aippt/` | Project or personal Codex skills |

Install the format used by your agent:

```bash
# Claude Code, project-local
mkdir -p .claude/skills
cp -R skills/claude-code/aippt .claude/skills/aippt

# Codex, project-local
mkdir -p .agents/skills
cp -R skills/codex/aippt .agents/skills/aippt

# Codex, personal alternative
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
cp -R skills/codex/aippt "${CODEX_HOME:-$HOME/.codex}/skills/aippt"
```

Then ask the agent to generate a deck from a repository-local Markdown or text file, for example: `Use AIPPT to turn doc/L9.md into an 8-slide Chinese presentation.`

The skills use the existing `main.py` workflow, validate generated artifacts, and require credentials to remain in the ignored local `config.yaml`. They never require an API key in the prompt or command line.

## 📁 Project Structure

```
OpenNotebookLM-AIPPT/
├── src/                    # Core logic
├── api/                    # FastAPI backend
├── web/                    # React frontend
├── skills/                 # Universal, Claude Code, and Codex Agent Skills
├── tests/                  # Tests
├── doc/                    # Input documents directory
│   └── L9.md               # Default demo source
├── config.yaml             # Configuration file
├── start.sh                # One-click startup script
└── main.py                 # CLI entry point
```

## ⚙️ Configuration

All configurations are managed in `config.yaml`, including:
- API configuration (`text_model`/`prompt_model`, `vlm_model`, `ocr_model`, `image_model`, `edit_model`)
- Generative editable PPTX provider roles and quality gates (visual analysis, OCR, cleanup, asset generation, repair, validation)
- PPT default settings (language, style, page count)
- Timeout and retry settings

See `config.example.yaml` for detailed configuration examples.

### Using OpenAI Compatible API

The current protocol is OpenAI-compatible `/chat/completions`: text profiles use
chat completions, and image/edit profiles use multimodal chat completions that
return an image URL, data URL, or base64 payload.

```yaml
api:
  models:
    # `prompt_model` is still accepted as a legacy alias for `text_model`.
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

## 📤 Output Structure

```
output/ppt_20241201_123456/
├── source_material.txt      # Original input material
├── prompts.json             # Generated prompts
├── result.json              # Generation result
├── presentation.pdf         # Exported PDF
├── presentation.pptx        # Exported raster or editable PPTX
└── images/                  # Generated slide images and assets
```

## 🧪 Development Checks

Recommended local runtime:
- Python 3.11 or 3.12
- Node.js 20

Backend checks:

```bash
python -m pip install -r requirements-dev.txt
ruff check api src tests main.py
ruff format --check api src tests main.py
python -m pytest --cov=api --cov=src --cov-report=term-missing
```

Frontend checks:

```bash
cd web
npm ci
npm run lint
npm run test
npm run build
```

GitHub Actions runs the same default checks on `main` and `dev` pull requests and pushes. Real model API calls and desktop PowerPoint/WPS rendering are intentionally not part of the default CI because they require private keys or host applications that are not available in GitHub runners. CI still validates package-level PPTX structure, object manifests, renderer contracts, and fake-provider reconstruction paths.

## 📋 TODO

- [ ] Support region selection for partial slide editing
- [ ] Improve editable PPTX element grouping, layer ordering, and native-shape coverage for complex slides
- [ ] Add more VLM, OCR, image generation, and image editing provider presets

## Acknowledgements

The editable PPTX reconstruction design references ideas from:
- [slide-alchemy](https://github.com/CodingFeng101/slide-alchemy)
- [image-to-editable-ppt-skill](https://github.com/ningzimu/image-to-editable-ppt-skill)

## 📄 License

Apache License 2.0
