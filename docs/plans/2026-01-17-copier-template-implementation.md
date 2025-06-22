# Copier Template Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Transform this repo into a Copier template that generates standardized Python projects with optional fullstack frontend.

**Architecture:** Template files live in `template/` subdirectory with `.jinja` suffix for files needing variable substitution. Copier config in root `copier.yml`. Conditional frontend via Jinja `{% if fullstack %}` blocks.

**Tech Stack:** Copier 9.4+, Jinja2 templating, uv, ruff, pytest, pre-commit, GitHub Actions, React 19, Vite 7, Tailwind v4, shadcn/ui, Biome

---

## Phase 1: Setup Copier Structure

### Task 1: Create copier.yml

**Files:**
- Create: `copier.yml`

**Step 1: Create the Copier configuration file**

```yaml
_min_copier_version: "9.4.0"
_subdirectory: template
_templates_suffix: .jinja

project_name:
  type: str
  help: "Project name (e.g., my-awesome-project)"
  validator: "{% if not project_name %}Project name is required{% endif %}"

fullstack:
  type: bool
  default: false
  help: "Include fullstack frontend (React + TypeScript + Vite + Tailwind + shadcn/ui)?"

package_name:
  type: str
  default: "{{ project_name | lower | replace('-', '_') | replace(' ', '_') | replace('.', '_') }}"
  when: false

_year:
  type: str
  default: "{{ '%Y' | strftime }}"
  when: false
```

**Step 2: Commit**

```bash
git add copier.yml
git commit -m "feat: add Copier configuration"
```

---

### Task 2: Create template directory structure

**Files:**
- Create: `template/` directory structure

**Step 1: Create all template directories**

```bash
mkdir -p template/.claude
mkdir -p template/.github/workflows
mkdir -p template/docs/plans
mkdir -p template/src
mkdir -p template/tests
```

**Step 2: Commit**

```bash
git add template/
git commit -m "feat: create template directory structure"
```

---

## Phase 2: Core Python Template Files

### Task 3: Create pyproject.toml.jinja

**Files:**
- Create: `template/pyproject.toml.jinja`

**Step 1: Create the pyproject.toml template**

```toml
[project]
name = "{{ project_name }}"
version = "0.1.0"
description = ""
requires-python = ">=3.13"
dependencies = []

[dependency-groups]
dev = [
    "jupyter>=1.1.1",
    "pre-commit>=4.2.0",
    "pytest>=8.4.1",
    "pytest-asyncio>=1.3.0",
    "pytest-cov>=7.0.0",
    "ruff>=0.12.0",
]

[tool.ruff]
line-length = 120
target-version = "py313"
src = ["src"]

[tool.ruff.lint]
select = ["E", "F", "W", "I", "B", "N", "UP", "ASYNC", "S", "PTH"]

[tool.ruff.lint.per-file-ignores]
"tests/**/*.py" = ["S101"]

[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = "test_*.py"
python_functions = "test_*"
asyncio_mode = "auto"
addopts = "-ra -q"

[build-system]
requires = ["uv_build>=0.9.25,<0.10.0"]
build-backend = "uv_build"
```

**Step 2: Commit**

```bash
git add template/pyproject.toml.jinja
git commit -m "feat: add pyproject.toml template"
```

---

### Task 4: Create .python-version

**Files:**
- Create: `template/.python-version`

**Step 1: Create the Python version file**

```
3.13
```

**Step 2: Commit**

```bash
git add template/.python-version
git commit -m "feat: add .python-version template"
```

---

### Task 5: Create .pre-commit-config.yaml

**Files:**
- Create: `template/.pre-commit-config.yaml`

**Step 1: Create the pre-commit config**

```yaml
repos:
  # Ruff - Python linting and formatting
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.14.13
    hooks:
      - id: ruff-check
        args: [--fix]
        types_or: [python, pyi, jupyter]
      - id: ruff-format
        types_or: [python, pyi, jupyter]

  # Markdown formatting
  - repo: https://github.com/executablebooks/mdformat
    rev: 1.0.0
    hooks:
      - id: mdformat
        additional_dependencies:
          - mdformat-gfm
          - mdformat-frontmatter

  # General file hygiene
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v6.0.0
    hooks:
      - id: end-of-file-fixer
      - id: trailing-whitespace
      - id: mixed-line-ending
        args: [--fix=lf]

  # Secret scanning
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.30.0
    hooks:
      - id: gitleaks
```

**Step 2: Commit**

```bash
git add template/.pre-commit-config.yaml
git commit -m "feat: add pre-commit config template"
```

---

### Task 6: Create .gitignore

**Files:**
- Create: `template/.gitignore`

**Step 1: Create comprehensive .gitignore**

```gitignore
# ===== Python =====
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
*.egg-info/
.installed.cfg
*.egg
MANIFEST
*.manifest
*.spec
pip-log.txt
pip-delete-this-directory.txt

# Virtual environments
.venv/
venv/
ENV/
env/
.conda/

# Type checkers / linters
.mypy_cache/
.dmypy.json
.pytype/
.pyre/
.ruff_cache/

# Testing / Coverage
htmlcov/
.tox/
.nox/
.coverage
.coverage.*
coverage.xml
*.cover
*.py,cover
.hypothesis/
.pytest_cache/
nosetests.xml

# Jupyter
.ipynb_checkpoints/
*.ipynb_metadata/

# ===== JavaScript / TypeScript / Node =====
node_modules/
jspm_packages/
bower_components/
.npm/
.pnpm-store/
.yarn/
*.tsbuildinfo
.eslintcache
.stylelintcache
.parcel-cache/
.next/
.nuxt/
.output/
dist/
out/

# ===== macOS =====
.DS_Store
.AppleDouble
.LSOverride
._*
.DocumentRevisions-V100
.fseventsd
.Spotlight-V100
.TemporaryItems
.Trashes
.VolumeIcon.icns
.com.apple.timemachine.donotpresent
.AppleDB
.AppleDesktop
Network Trash Folder
Temporary Items
.apdisk

# ===== IDEs =====
# VSCode
.vscode/*
!.vscode/settings.json
!.vscode/tasks.json
!.vscode/launch.json
!.vscode/extensions.json
.history/
*.vsix

# JetBrains
.idea/**/workspace.xml
.idea/**/tasks.xml
.idea/**/usage.statistics.xml
.idea/**/dictionaries
.idea/**/shelf
.idea/**/aws.xml
.idea/**/contentModel.xml
.idea/**/dataSources/
.idea/**/dataSources*.xml
.idea/**/sqlDataSources.xml
.idea/**/dynamic.xml
.idea/**/uiDesigner.xml
.idea/**/dbnavigator.xml
.idea/**/gradle.xml
.idea/**/libraries
.idea/**/mongoSettings.xml
.idea/**/replstate.xml
.idea/**/sonarlint/
.idea/**/httpRequests/
.idea/**/caches/
*.iws
out/

# ===== Terraform / Pulumi / IaC =====
.terraform/
.terraform.lock.hcl
*.tfstate
*.tfstate.*
*.tfvars
*.tfvars.json
*.auto.tfvars
*.auto.tfvars.json
crash.log
crash.*.log
override.tf
override.tf.json
*_override.tf
*_override.tf.json
.terraformrc
terraform.rc

# Pulumi
.pulumi/
Pulumi.*.yaml
!Pulumi.yaml

# ===== Secrets / Keys / Credentials =====
# Environment files
.env
.env.*
!.env.example
!.env.template
*.local

# Private keys and certificates
*.pem
*.key
*.p12
*.pfx
*.jks
*.keystore
*.crt
*.cer
*.der
*.pub
id_rsa*
id_dsa*
id_ecdsa*
id_ed25519*

# Credential files
*credentials*
*credential*
*secret*
*secrets*
*.secrets.*
*password*
*passwords*
*token*
*tokens*
*apikey*
*api_key*
*api-key*

# Cloud provider credentials
.aws/credentials
.azure/
gcloud/
serviceaccount*.json
*-service-account*.json
*-key.json
*-credentials.json
application_default_credentials.json

# OAuth / Auth files
oauth*.json
client_secret*.json
token.json
refresh_token*

# Config files that often contain secrets
config.local.*
settings.local.*
local_settings.py
*.local.yaml
*.local.yml
*.local.json
*.local.toml

# ===== Logs =====
logs/
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*
lerna-debug.log*
.pnpm-debug.log*

# ===== Misc =====
*.bak
*.swp
*.swo
*~
.cache/
tmp/
temp/
*.tmp
*.temp
```

**Step 2: Commit**

```bash
git add template/.gitignore
git commit -m "feat: add comprehensive .gitignore template"
```

---

### Task 7: Create .gitattributes

**Files:**
- Create: `template/.gitattributes`

**Step 1: Create .gitattributes for line ending normalization**

```gitattributes
# Auto-detect text files and normalize to LF on commit
* text=auto eol=lf

# Explicit text files
*.py text eol=lf
*.pyi text eol=lf
*.md text eol=lf
*.txt text eol=lf
*.json text eol=lf
*.yaml text eol=lf
*.yml text eol=lf
*.toml text eol=lf
*.cfg text eol=lf
*.ini text eol=lf
*.sh text eol=lf
*.html text eol=lf
*.css text eol=lf
*.js text eol=lf
*.ts text eol=lf
*.tsx text eol=lf
*.jsx text eol=lf

# Binary files (no conversion)
*.png binary
*.jpg binary
*.jpeg binary
*.gif binary
*.ico binary
*.pdf binary
*.woff binary
*.woff2 binary
*.ttf binary
*.eot binary
*.zip binary
*.gz binary
*.tar binary
```

**Step 2: Commit**

```bash
git add template/.gitattributes
git commit -m "feat: add .gitattributes for line ending normalization"
```

---

### Task 8: Create .editorconfig

**Files:**
- Create: `template/.editorconfig`

**Step 1: Create .editorconfig**

```ini
root = true

[*]
charset = utf-8
end_of_line = lf
indent_style = space
indent_size = 4
insert_final_newline = true
trim_trailing_whitespace = true

[*.{js,ts,jsx,tsx,json,yaml,yml,css,html}]
indent_size = 2

[Makefile]
indent_style = tab
```

**Step 2: Commit**

```bash
git add template/.editorconfig
git commit -m "feat: add .editorconfig template"
```

---

### Task 9: Create LICENSE.txt

**Files:**
- Create: `template/LICENSE.txt`

**Step 1: Create MIT license**

```
MIT License

Copyright (c) {{ _year }}

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

**Step 2: Commit**

```bash
git add template/LICENSE.txt
git commit -m "feat: add LICENSE.txt template"
```

---

## Phase 3: Source and Test Files

### Task 10: Create src package structure

**Files:**
- Create: `template/src/{{ package_name }}/__init__.py`
- Create: `template/src/{{ package_name }}/py.typed`

**Step 1: Create __init__.py**

```python
"""{{ project_name }} package."""
```

**Step 2: Create py.typed marker**

Create empty file `template/src/{{ package_name }}/py.typed` (PEP 561 marker for typed package)

**Step 3: Commit**

```bash
git add template/src/
git commit -m "feat: add src package template"
```

---

### Task 11: Create test files

**Files:**
- Create: `template/tests/__init__.py`
- Create: `template/tests/conftest.py`
- Create: `template/tests/test_example.py`

**Step 1: Create tests/__init__.py**

```python
"""Test suite for {{ project_name }}."""
```

**Step 2: Create tests/conftest.py**

```python
"""Pytest configuration and fixtures."""

import pytest


@pytest.fixture
def sample_fixture():
    """Example fixture that can be used across tests."""
    return {"key": "value"}
```

**Step 3: Create tests/test_example.py**

```python
"""Example test file."""


def test_example():
    """Example test that always passes."""
    assert True


def test_import():
    """Test that the package can be imported."""
    import {{ package_name }}  # noqa: F401
```

**Step 4: Commit**

```bash
git add template/tests/
git commit -m "feat: add test files template"
```

---

## Phase 4: GitHub Actions

### Task 12: Create CI workflow

**Files:**
- Create: `template/.github/workflows/ci.yml`

**Step 1: Create the CI workflow**

```yaml
name: CI

on:
  pull_request:
    branches: [main, dev]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Install uv
        uses: astral-sh/setup-uv@v5

      - name: Set up Python
        run: uv python install 3.13

      - name: Install dependencies
        run: uv sync --dev

      - name: Run ruff check
        run: uv run ruff check .

      - name: Run ruff format check
        run: uv run ruff format --check .

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Install uv
        uses: astral-sh/setup-uv@v5

      - name: Set up Python
        run: uv python install 3.13

      - name: Install dependencies
        run: uv sync --dev

      - name: Run tests
        run: uv run pytest

  secrets:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Run gitleaks
        uses: gitleaks/gitleaks-action@v2
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

**Step 2: Commit**

```bash
git add template/.github/
git commit -m "feat: add GitHub Actions CI workflow"
```

---

## Phase 5: Claude Code Configuration

### Task 13: Create .claude/settings.json.jinja

**Files:**
- Create: `template/.claude/settings.json.jinja`

**Step 1: Create the Claude settings template**

```json
{
  "enabledPlugins": {
    "superpowers@superpowers-marketplace": true,
    "double-shot-latte@superpowers-marketplace": true,
    "episodic-memory@superpowers-marketplace": true,
    "feature-dev@claude-plugins-official": true,
    "code-simplifier@claude-plugins-official": true,
    "code-review@claude-plugins-official": true,
    "pyright-lsp@claude-plugins-official": true,
    "explanatory-output-style@claude-plugins-official": true,
    "ralph-loop@claude-plugins-official": true{% if fullstack %},
    "typescript-lsp@claude-plugins-official": true,
    "frontend-design@claude-plugins-official": true,
    "playwright@claude-plugins-official": true{% endif %}
  }
}
```

**Step 2: Commit**

```bash
git add template/.claude/settings.json.jinja
git commit -m "feat: add Claude Code settings template"
```

---

### Task 14: Create .claude/README.md.jinja

**Files:**
- Create: `template/.claude/README.md.jinja`

**Step 1: Create Claude setup instructions**

```markdown
# Claude Code Setup

This project uses Claude Code with the following plugins.

## Required Plugins

**From superpowers-marketplace:**

- **superpowers** - Skills and workflows for development
- **double-shot-latte** - Enhanced development workflows
- **episodic-memory** - Conversation memory across sessions

**From claude-plugins-official:**

- **feature-dev** - Guided feature development
- **code-simplifier** - Code refactoring assistance
- **code-review** - PR review via `/code-review`
- **pyright-lsp** - Python type checking and intelligence
- **explanatory-output-style** - Educational code explanations
- **ralph-loop** - Iterative development loops
{% if fullstack %}

## Fullstack Plugins

- **typescript-lsp** - TypeScript language support
- **frontend-design** - Frontend UI development
- **playwright** - Browser automation and testing
{% endif %}

## Installation

1. Open Claude Code
2. Run `/plugins` to open plugin manager
3. Install the plugins listed above from the marketplace
```

**Step 2: Commit**

```bash
git add template/.claude/README.md.jinja
git commit -m "feat: add Claude Code README template"
```

---

### Task 15: Create CLAUDE.md.jinja

**Files:**
- Create: `template/CLAUDE.md.jinja`

**Step 1: Create project-level Claude instructions**

```markdown
# {{ project_name }}

## Tech Stack

- Python 3.13
- Package manager: uv
- Linting/Formatting: ruff
- Testing: pytest
{% if fullstack %}
- Frontend: React 19 + TypeScript + Vite 7 + Tailwind v4 + shadcn/ui
{% endif %}

## Project Structure

- `src/{{ package_name }}/` - Main package code
- `tests/` - Test files
- `docs/` - Documentation
- `docs/plans/` - Implementation plans
{% if fullstack %}- `frontend/` - React frontend{% endif %}

## Configuration

All tool configuration is in `pyproject.toml`. Check there for:

- Ruff lint rules and settings
- Pytest configuration
- Dependencies

## Documentation

- `docs/product-design.md` - Product requirements and design
- `docs/architecture.md` - Technical design decisions
```

**Step 2: Commit**

```bash
git add template/CLAUDE.md.jinja
git commit -m "feat: add CLAUDE.md template"
```

---

## Phase 6: Documentation

### Task 16: Create README.md.jinja

**Files:**
- Create: `template/README.md.jinja`

**Step 1: Create project README template**

```markdown
# {{ project_name }}

<!-- Brief one-line description of the project -->

## Installation

```bash
# Clone the repository
git clone <repository-url>
cd {{ project_name }}

# Install uv (if not already installed)
curl -LsSf https://astral.sh/uv/install.sh | sh

# Install dependencies
uv sync --dev --upgrade

# Install pre-commit hooks
uv run pre-commit install
```
{% if fullstack %}

### Frontend Setup

```bash
cd frontend
npm install
npx shadcn@latest init
```
{% endif %}

## Development

### Running Tests

```bash
uv run pytest
```

### Linting & Formatting

```bash
# Check for issues
uv run ruff check .
uv run ruff format --check .

# Auto-fix issues
uv run ruff check --fix .
uv run ruff format .
```

### Pre-commit Hooks

Pre-commit hooks run automatically on commit. To run manually:

```bash
uv run pre-commit run --all-files
```
{% if fullstack %}

### Frontend Development

```bash
cd frontend
npm run dev      # Start dev server
npm run test     # Run tests
npm run check    # Lint & format
```
{% endif %}

## Project Structure

```
{{ project_name }}/
├── src/{{ package_name }}/   # Main package code
├── tests/                    # Test files
├── docs/                     # Documentation
│   ├── architecture.md       # Technical architecture
│   ├── product-design.md     # Product requirements
│   └── plans/                # Implementation plans
{% if fullstack %}├── frontend/                 # React frontend
{% endif %}├── pyproject.toml            # Project configuration
└── README.md                 # This file
```

## Documentation

- [Product Design](docs/product-design.md) - Product vision and requirements
- [Architecture](docs/architecture.md) - Technical design decisions

## License

MIT
```

**Step 2: Commit**

```bash
git add template/README.md.jinja
git commit -m "feat: add README.md template"
```

---

### Task 17: Create docs/product-design.md

**Files:**
- Create: `template/docs/product-design.md`

**Step 1: Create product design placeholder**

```markdown
# Product Design

> This document describes the product vision, user needs, and feature requirements.
> Update this as the product evolves.

## Overview

<!-- What is this product? Who is it for? What problem does it solve? -->

## User Personas

<!-- Who are the target users? What are their goals and pain points? -->

## Features

<!-- List of features with descriptions and priorities -->

## User Flows

<!-- Key user journeys through the product -->

## Success Metrics

<!-- How do we measure if the product is successful? -->
```

**Step 2: Commit**

```bash
git add template/docs/product-design.md
git commit -m "feat: add product-design.md template"
```

---

### Task 18: Create docs/architecture.md.jinja

**Files:**
- Create: `template/docs/architecture.md.jinja`

**Step 1: Create architecture placeholder**

```markdown
# Architecture

> This document describes the technical architecture, design decisions, and system structure.
> Update this as the architecture evolves.

## Overview

<!-- High-level system architecture diagram or description -->

## Components

<!-- Key components/modules and their responsibilities -->

## Data Flow

<!-- How data moves through the system -->

## Dependencies

<!-- External services, APIs, libraries this system depends on -->

## Design Decisions

<!-- Key technical decisions and their rationale (ADRs) -->

| Decision | Choice | Rationale | Date |
|----------|--------|-----------|------|
| Package manager | uv | Fast, modern, handles Python versions | |
| Linting | ruff | All-in-one, fast, replaces multiple tools | |

## Security Considerations

<!-- Security-relevant architecture decisions -->
{% if fullstack %}

## Frontend Architecture

| Technology | Purpose |
|------------|---------|
| React 19.2 | UI framework with Server Components support |
| TypeScript 5.7 | Type safety |
| Vite 7 | Build tool, dev server, HMR |
| Tailwind CSS v4 | Utility-first styling (CSS-first config) |
| shadcn/ui | Accessible, customizable components |
| Biome | Linting + formatting (20x faster than ESLint) |
| Vitest | Unit testing (native Vite integration) |

### Frontend Structure

```
frontend/
├── src/
│   ├── components/    # Reusable UI components
│   │   └── ui/        # shadcn/ui components
│   ├── lib/           # Utilities (cn helper, etc.)
│   ├── test/          # Test setup
│   ├── App.tsx        # Root component
│   └── main.tsx       # Entry point
├── biome.json         # Linting/formatting config
├── vite.config.ts     # Vite configuration
└── vitest.config.ts   # Test configuration
```
{% endif %}
```

**Step 2: Commit**

```bash
git add template/docs/architecture.md.jinja
git commit -m "feat: add architecture.md template"
```

---

### Task 19: Create docs/plans/.gitkeep

**Files:**
- Create: `template/docs/plans/.gitkeep`

**Step 1: Create .gitkeep with explanation**

```
# This directory holds implementation plans created during development.
# Plans are typically created by Claude Code's brainstorming skill.
# Format: YYYY-MM-DD-<topic>-design.md
```

**Step 2: Commit**

```bash
git add template/docs/plans/.gitkeep
git commit -m "feat: add docs/plans directory"
```

---

## Phase 7: Fullstack Frontend (Conditional)

### Task 20: Create frontend directory structure

**Files:**
- Create: `template/{% if fullstack %}frontend{% endif %}/` structure

**Step 1: Create frontend directories**

```bash
mkdir -p "template/{% if fullstack %}frontend{% endif %}/public"
mkdir -p "template/{% if fullstack %}frontend{% endif %}/src/components/ui"
mkdir -p "template/{% if fullstack %}frontend{% endif %}/src/lib"
mkdir -p "template/{% if fullstack %}frontend{% endif %}/src/test"
mkdir -p "template/{% if fullstack %}frontend{% endif %}/src/assets"
```

**Step 2: Commit**

```bash
git add "template/{% if fullstack %}frontend{% endif %}/"
git commit -m "feat: create frontend directory structure"
```

---

### Task 21: Create frontend/package.json.jinja

**Files:**
- Create: `template/{% if fullstack %}frontend{% endif %}/package.json.jinja`

**Step 1: Create package.json**

```json
{
  "name": "{{ project_name }}-frontend",
  "private": true,
  "version": "0.1.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc -b && vite build",
    "preview": "vite preview",
    "test": "vitest",
    "test:ui": "vitest --ui",
    "check": "biome check .",
    "check:fix": "biome check --write ."
  },
  "dependencies": {
    "react": "^19.2.0",
    "react-dom": "^19.2.0",
    "class-variance-authority": "^0.7.0",
    "clsx": "^2.1.0",
    "tailwind-merge": "^3.0.0",
    "lucide-react": "^0.475.0"
  },
  "devDependencies": {
    "@biomejs/biome": "^2.0.0",
    "@tailwindcss/vite": "^4.0.0",
    "@testing-library/jest-dom": "^6.6.0",
    "@testing-library/react": "^16.2.0",
    "@testing-library/user-event": "^14.6.0",
    "@types/react": "^19.0.0",
    "@types/react-dom": "^19.0.0",
    "@vitejs/plugin-react": "^4.4.0",
    "jsdom": "^26.0.0",
    "tailwindcss": "^4.0.0",
    "typescript": "~5.7.0",
    "vite": "^7.0.0",
    "vitest": "^4.0.0"
  }
}
```

**Step 2: Commit**

```bash
git add "template/{% if fullstack %}frontend{% endif %}/package.json.jinja"
git commit -m "feat: add frontend package.json template"
```

---

### Task 22: Create frontend config files

**Files:**
- Create: `template/{% if fullstack %}frontend{% endif %}/vite.config.ts`
- Create: `template/{% if fullstack %}frontend{% endif %}/vitest.config.ts`
- Create: `template/{% if fullstack %}frontend{% endif %}/biome.json`
- Create: `template/{% if fullstack %}frontend{% endif %}/tsconfig.json`
- Create: `template/{% if fullstack %}frontend{% endif %}/tsconfig.node.json`

**Step 1: Create vite.config.ts**

```typescript
import path from "path"
import tailwindcss from "@tailwindcss/vite"
import react from "@vitejs/plugin-react"
import { defineConfig } from "vite"

export default defineConfig({
  plugins: [react(), tailwindcss()],
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./src"),
    },
  },
  server: {
    port: 3000,
  },
})
```

**Step 2: Create vitest.config.ts**

```typescript
import path from "path"
import react from "@vitejs/plugin-react"
import { defineConfig } from "vitest/config"

export default defineConfig({
  plugins: [react()],
  test: {
    environment: "jsdom",
    globals: true,
    setupFiles: "./src/test/setup.ts",
  },
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./src"),
    },
  },
})
```

**Step 3: Create biome.json**

```json
{
  "$schema": "https://biomejs.dev/schemas/2.0.0/schema.json",
  "organizeImports": { "enabled": true },
  "linter": {
    "enabled": true,
    "rules": { "recommended": true }
  },
  "formatter": {
    "enabled": true,
    "indentStyle": "space",
    "indentWidth": 2
  }
}
```

**Step 4: Create tsconfig.json**

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "skipLibCheck": true,
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["src"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

**Step 5: Create tsconfig.node.json**

```json
{
  "compilerOptions": {
    "composite": true,
    "skipLibCheck": true,
    "module": "ESNext",
    "moduleResolution": "bundler",
    "allowSyntheticDefaultImports": true,
    "strict": true
  },
  "include": ["vite.config.ts", "vitest.config.ts"]
}
```

**Step 6: Commit**

```bash
git add "template/{% if fullstack %}frontend{% endif %}/"
git commit -m "feat: add frontend config files"
```

---

### Task 23: Create frontend source files

**Files:**
- Create: `template/{% if fullstack %}frontend{% endif %}/index.html.jinja`
- Create: `template/{% if fullstack %}frontend{% endif %}/src/main.tsx`
- Create: `template/{% if fullstack %}frontend{% endif %}/src/App.tsx`
- Create: `template/{% if fullstack %}frontend{% endif %}/src/App.css`
- Create: `template/{% if fullstack %}frontend{% endif %}/src/index.css`
- Create: `template/{% if fullstack %}frontend{% endif %}/src/vite-env.d.ts`

**Step 1: Create index.html.jinja**

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>{{ project_name }}</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>
```

**Step 2: Create src/main.tsx**

```tsx
import { StrictMode } from "react"
import { createRoot } from "react-dom/client"
import "./index.css"
import App from "./App.tsx"

createRoot(document.getElementById("root")!).render(
  <StrictMode>
    <App />
  </StrictMode>,
)
```

**Step 3: Create src/App.tsx**

```tsx
import "./App.css"

function App() {
  return (
    <div className="min-h-screen flex items-center justify-center bg-gray-50">
      <div className="text-center">
        <h1 className="text-4xl font-bold text-gray-900 mb-4">
          Welcome to {{ project_name }}
        </h1>
        <p className="text-gray-600">
          Edit <code className="bg-gray-100 px-2 py-1 rounded">src/App.tsx</code> to get started
        </p>
      </div>
    </div>
  )
}

export default App
```

**Step 4: Create src/App.css**

```css
#root {
  max-width: 1280px;
  margin: 0 auto;
  padding: 2rem;
}
```

**Step 5: Create src/index.css**

```css
@import "tailwindcss";

@theme {
  --font-sans: "Inter", sans-serif;
  --radius-lg: 0.5rem;
  --radius-md: 0.375rem;
  --radius-sm: 0.25rem;
}
```

**Step 6: Create src/vite-env.d.ts**

```typescript
/// <reference types="vite/client" />
```

**Step 7: Commit**

```bash
git add "template/{% if fullstack %}frontend{% endif %}/"
git commit -m "feat: add frontend source files"
```

---

### Task 24: Create frontend utility and test files

**Files:**
- Create: `template/{% if fullstack %}frontend{% endif %}/src/lib/utils.ts`
- Create: `template/{% if fullstack %}frontend{% endif %}/src/test/setup.ts`
- Create: `template/{% if fullstack %}frontend{% endif %}/src/components/.gitkeep`
- Create: `template/{% if fullstack %}frontend{% endif %}/src/components/ui/.gitkeep`
- Create: `template/{% if fullstack %}frontend{% endif %}/src/assets/.gitkeep`
- Create: `template/{% if fullstack %}frontend{% endif %}/public/vite.svg`

**Step 1: Create src/lib/utils.ts**

```typescript
import { clsx, type ClassValue } from "clsx"
import { twMerge } from "tailwind-merge"

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}
```

**Step 2: Create src/test/setup.ts**

```typescript
import "@testing-library/jest-dom/vitest"
```

**Step 3: Create .gitkeep files**

Create empty files:
- `template/{% if fullstack %}frontend{% endif %}/src/components/.gitkeep`
- `template/{% if fullstack %}frontend{% endif %}/src/components/ui/.gitkeep`
- `template/{% if fullstack %}frontend{% endif %}/src/assets/.gitkeep`

**Step 4: Create public/vite.svg**

```svg
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" aria-hidden="true" role="img" class="iconify iconify--logos" width="31.88" height="32" preserveAspectRatio="xMidYMid meet" viewBox="0 0 256 257"><defs><linearGradient id="IconifyId1813088fe1fbc01fb466" x1="-.828%" x2="57.636%" y1="7.652%" y2="78.411%"><stop offset="0%" stop-color="#41D1FF"></stop><stop offset="100%" stop-color="#BD34FE"></stop></linearGradient><linearGradient id="IconifyId1813088fe1fbc01fb467" x1="43.376%" x2="50.316%" y1="2.242%" y2="89.03%"><stop offset="0%" stop-color="#FFBD4F"></stop><stop offset="100%" stop-color="#FF980E"></stop></linearGradient></defs><path fill="url(#IconifyId1813088fe1fbc01fb466)" d="M255.153 37.938L134.897 252.976c-2.483 4.44-8.862 4.466-11.382.048L.875 37.958c-2.746-4.814 1.371-10.646 6.827-9.67l120.385 21.517a6.537 6.537 0 0 0 2.322-.004l117.867-21.483c5.438-.991 9.574 4.796 6.877 9.62Z"></path><path fill="url(#IconifyId1813088fe1fbc01fb467)" d="M185.432.063L96.44 17.501a3.268 3.268 0 0 0-2.634 3.014l-5.474 92.456a3.268 3.268 0 0 0 3.997 3.378l24.777-5.718c2.318-.535 4.413 1.507 3.936 3.838l-7.361 36.047c-.495 2.426 1.782 4.5 4.151 3.78l15.304-4.649c2.372-.72 4.652 1.36 4.15 3.788l-11.698 56.621c-.732 3.542 3.979 5.473 5.943 2.437l1.313-2.028l72.516-144.72c1.215-2.423-.88-5.186-3.54-4.672l-25.505 4.922c-2.396.462-4.435-1.77-3.759-4.114l16.646-57.705c.677-2.35-1.37-4.583-3.769-4.113Z"></path></svg>
```

**Step 5: Commit**

```bash
git add "template/{% if fullstack %}frontend{% endif %}/"
git commit -m "feat: add frontend utility and test files"
```

---

### Task 25: Create frontend README.md.jinja

**Files:**
- Create: `template/{% if fullstack %}frontend{% endif %}/README.md.jinja`

**Step 1: Create frontend README**

```markdown
# {{ project_name }} Frontend

React 19 + TypeScript + Vite 7 + Tailwind CSS v4 + shadcn/ui

## Setup

```bash
cd frontend

# Install dependencies
npm install

# Initialize shadcn/ui (first time only)
npx shadcn@latest init

# Add components as needed
npx shadcn@latest add button
npx shadcn@latest add card
npx shadcn@latest add input
```

## Development

```bash
npm run dev        # Start dev server at http://localhost:3000
npm run build      # Production build
npm run preview    # Preview production build
```

## Testing

```bash
npm run test       # Run tests in watch mode
npm run test:ui    # Run tests with UI
```

## Linting & Formatting

```bash
npm run check      # Check for issues
npm run check:fix  # Auto-fix issues
```

## Tech Stack

- **React 19.2** - UI framework
- **TypeScript 5.7** - Type safety
- **Vite 7** - Build tool with HMR
- **Tailwind CSS v4** - Utility-first CSS
- **shadcn/ui** - Accessible component library
- **Biome** - Linting and formatting (replaces ESLint + Prettier)
- **Vitest** - Unit testing
```

**Step 2: Commit**

```bash
git add "template/{% if fullstack %}frontend{% endif %}/README.md.jinja"
git commit -m "feat: add frontend README template"
```

---

## Phase 8: Template Repo README

### Task 26: Create template repo README

**Files:**
- Modify: `README.md` (root level, not in template/)

**Step 1: Replace root README with template usage instructions**

```markdown
# Standard Python Template

A Copier template for Python projects with modern tooling.

## Features

- **Python 3.13** with uv package manager
- **src layout** with proper build backend (uv_build)
- **ruff** for linting and formatting
- **pytest** for testing
- **pre-commit** hooks (ruff, mdformat, gitleaks)
- **Claude Code** configuration with recommended plugins
- **GitHub Actions** CI (lint, test, secret scanning)
- **Optional fullstack**: React 19 + TypeScript + Vite 7 + Tailwind v4 + shadcn/ui

## Usage

```bash
uvx copier copy gh:your-org/standard-template-python ./my-project
```

You'll be prompted for:

- **Project name** - Used for package name and directory
- **Include fullstack?** - Adds React + TypeScript + Vite + Tailwind + shadcn/ui

## After Creation

```bash
cd my-project

# Initialize git
git init

# Install dependencies
uv sync --dev --upgrade

# Install pre-commit hooks
uv run pre-commit install

# Run tests
uv run pytest
```

### Fullstack Setup (if enabled)

```bash
cd frontend
npm install
npx shadcn@latest init
npm run dev
```

## Template Development

To test the template locally:

```bash
# Test basic Python project
copier copy . /tmp/test-project

# Test with fullstack
copier copy . /tmp/test-fullstack --data fullstack=true
```
```

**Step 2: Commit**

```bash
git add README.md
git commit -m "docs: update README with template usage instructions"
```

---

## Phase 9: Cleanup and Testing

### Task 27: Remove old project files

**Files:**
- Delete: Old project files that shouldn't be in the template repo

**Step 1: Remove files that are now in template/**

```bash
# Remove old src/ and tests/ (now in template/)
rm -rf src/ tests/

# Remove old config files (now in template/)
rm -f .pre-commit-config.yaml
rm -f .python-version
rm -f pyproject.toml
rm -f LICENSE.txt

# Keep: .gitignore (for template repo itself)
# Keep: docs/plans/ (design documents)
# Keep: .git/
# Keep: .idea/ (if you want IDE settings)
```

**Step 2: Create minimal .gitignore for template repo**

```gitignore
# Template repo ignores
.DS_Store
.idea/
*.pyc
__pycache__/
.venv/
```

**Step 3: Commit**

```bash
git add -A
git commit -m "chore: clean up old project files, keep template structure"
```

---

### Task 28: Test the template

**Step 1: Test basic Python project generation**

```bash
copier copy . /tmp/test-python-project --data project_name=my-test-project
cd /tmp/test-python-project
uv sync --dev
uv run pytest
uv run ruff check .
```

Expected: All commands pass

**Step 2: Test fullstack project generation**

```bash
copier copy . /tmp/test-fullstack-project --data project_name=my-fullstack-app --data fullstack=true
cd /tmp/test-fullstack-project
uv sync --dev
uv run pytest
cd frontend
npm install
npm run check
npm run build
```

Expected: All commands pass

**Step 3: Verify file structure**

```bash
ls -la /tmp/test-python-project/
ls -la /tmp/test-fullstack-project/
ls -la /tmp/test-fullstack-project/frontend/
```

---

### Task 29: Final commit

**Step 1: Tag the release**

```bash
git tag -a v1.0.0 -m "Initial Copier template release"
```

**Step 2: Push to remote (when ready)**

```bash
git push origin main --tags
```

---

## Summary

Total tasks: 29

**Phase 1:** Setup Copier Structure (2 tasks)
**Phase 2:** Core Python Template Files (7 tasks)
**Phase 3:** Source and Test Files (2 tasks)
**Phase 4:** GitHub Actions (1 task)
**Phase 5:** Claude Code Configuration (3 tasks)
**Phase 6:** Documentation (4 tasks)
**Phase 7:** Fullstack Frontend (6 tasks)
**Phase 8:** Template Repo README (1 task)
**Phase 9:** Cleanup and Testing (3 tasks)
