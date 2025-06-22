# Python Project Template Design

A Copier template for standardized Python projects across the company.

## Overview

Transform the existing `standard-template-python` repo into a proper Copier template that generates consistent, modern Python projects with optional fullstack frontend support.

## Usage

```bash
uvx copier copy gh:your-org/standard-template-python ./my-project
```

Prompts:
- **Project name** - Used for package name and directory
- **Include fullstack?** - Adds React + TypeScript + Vite + Tailwind + shadcn/ui

## Template Variables

| Variable | Type | Description |
|----------|------|-------------|
| `project_name` | string | Project name (required) |
| `fullstack` | boolean | Include frontend (default: false) |
| `package_name` | computed | Auto-derived: lowercase, hyphens→underscores |

---

## Project Structure (Generated)

```
{{ project_name }}/
├── .claude/
│   ├── settings.json          # Plugin configuration
│   └── README.md              # Claude Code setup instructions
├── .github/
│   └── workflows/
│       └── ci.yml             # Lint + test + secret scanning on PRs
├── docs/
│   ├── plans/                 # Coding assistant plans
│   ├── architecture.md        # Technical architecture
│   └── product-design.md      # Product requirements
├── src/
│   └── {{ package_name }}/
│       ├── __init__.py
│       └── py.typed           # PEP 561 marker
├── tests/
│   ├── __init__.py
│   ├── conftest.py
│   └── test_example.py
├── frontend/                  # (fullstack only)
│   ├── src/
│   ├── public/
│   ├── package.json
│   ├── vite.config.ts
│   └── ...
├── .editorconfig
├── .gitattributes
├── .gitignore
├── .pre-commit-config.yaml
├── .python-version            # 3.13
├── CLAUDE.md
├── LICENSE.txt
├── README.md
└── pyproject.toml
```

---

## Configuration Files

### pyproject.toml

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

### .pre-commit-config.yaml

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

### .gitattributes

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

### .editorconfig

```ini
root = true

[*]
charset = utf-8
end_of_line = lf
indent_style = space
indent_size = 4
insert_final_newline = true
trim_trailing_whitespace = true

[*.{js,ts,jsx,tsx,json,yaml,yml}]
indent_size = 2

[Makefile]
indent_style = tab
```

### .gitignore

Comprehensive ignore rules covering:
- Python (pycache, venv, coverage, type checkers)
- JavaScript/TypeScript/Node (node_modules, build artifacts)
- macOS (.DS_Store, etc.)
- IDEs (VSCode, JetBrains)
- Terraform/Pulumi (state files, tfvars)
- **Secrets/Keys** (*.pem, *.key, credentials, tokens, API keys, cloud provider configs)

Key secret patterns:
```gitignore
# Private keys and certificates
*.pem
*.key
*.p12
*.pfx
*.jks

# Credential files
*credentials*
*secret*
*password*
*token*
*apikey*

# Cloud provider credentials
.aws/credentials
serviceaccount*.json
*-key.json

# Environment files
.env
.env.*
!.env.example
```

---

## GitHub Actions CI

**.github/workflows/ci.yml**

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

---

## Claude Code Configuration

### .claude/settings.json

**Base plugins:**
- superpowers@superpowers-marketplace
- double-shot-latte@superpowers-marketplace
- episodic-memory@superpowers-marketplace
- feature-dev@claude-plugins-official
- code-simplifier@claude-plugins-official
- code-review@claude-plugins-official
- pyright-lsp@claude-plugins-official
- explanatory-output-style@claude-plugins-official
- ralph-loop@claude-plugins-official

**Fullstack adds:**
- typescript-lsp@claude-plugins-official
- frontend-design@claude-plugins-official
- playwright@claude-plugins-official

### CLAUDE.md

Minimal, stable content:
- Tech stack summary
- Project structure
- Pointer to pyproject.toml for commands
- Pointer to docs/ for details

---

## Documentation Structure

### docs/product-design.md
Placeholder with sections:
- Overview
- User Personas
- Features
- User Flows
- Success Metrics

### docs/architecture.md
Placeholder with sections:
- Overview
- Components
- Data Flow
- Dependencies
- Design Decisions (ADR table)
- Security Considerations
- Frontend Architecture (fullstack only)

### docs/plans/
Empty directory for implementation plans created during development.

---

## Fullstack Frontend (Optional)

When `fullstack: true`, includes `frontend/` with:

### Tech Stack
- **React 19.2** - UI framework
- **TypeScript 5.7** - Type safety
- **Vite 7** - Build tool with HMR
- **Tailwind CSS v4** - CSS-first utility styling
- **shadcn/ui** - Accessible component library
- **Biome** - Linting + formatting (replaces ESLint + Prettier)
- **Vitest 4** - Unit testing

### frontend/package.json

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

### frontend/vite.config.ts

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

### frontend/biome.json

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

### frontend/src/index.css (Tailwind v4)

```css
@import "tailwindcss";

@theme {
  --font-sans: "Inter", sans-serif;
  --radius-lg: 0.5rem;
  --radius-md: 0.375rem;
  --radius-sm: 0.25rem;
}
```

### frontend/src/lib/utils.ts (shadcn helper)

```typescript
import { clsx, type ClassValue } from "clsx"
import { twMerge } from "tailwind-merge"

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}
```

---

## Copier Configuration

### copier.yml

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

### Template Repo Structure

```
standard-template-python/
├── copier.yml
├── README.md                          # How to use this template
└── template/
    ├── .claude/
    │   ├── README.md.jinja
    │   └── settings.json.jinja
    ├── .github/
    │   └── workflows/
    │       └── ci.yml
    ├── docs/
    │   ├── plans/
    │   │   └── .gitkeep
    │   ├── architecture.md.jinja
    │   └── product-design.md
    ├── src/
    │   └── {{ package_name }}/
    │       ├── __init__.py
    │       └── py.typed
    ├── tests/
    │   ├── __init__.py
    │   ├── conftest.py
    │   └── test_example.py
    ├── {% if fullstack %}frontend{% endif %}/
    │   └── ... (React + Vite setup)
    ├── .editorconfig
    ├── .gitattributes
    ├── .gitignore
    ├── .pre-commit-config.yaml
    ├── .python-version
    ├── CLAUDE.md.jinja
    ├── LICENSE.txt
    ├── pyproject.toml.jinja
    └── README.md.jinja
```

---

## Template Repo README

```markdown
# Standard Python Template

A Copier template for Python projects with modern tooling.

## Features

- **Python 3.13** with uv package manager
- **src layout** with proper build backend
- **ruff** for linting and formatting
- **pytest** for testing
- **pre-commit** hooks (ruff, mdformat, gitleaks)
- **Claude Code** configuration with recommended plugins
- **GitHub Actions** CI (lint, test, secret scanning)
- **Optional fullstack**: React 19 + TypeScript + Vite 7 + Tailwind v4 + shadcn/ui

## Usage

\```bash
uvx copier copy gh:your-org/standard-template-python ./my-project
\```

You'll be prompted for:
- **Project name** - Used for package name and directory
- **Include fullstack?** - Adds React + TypeScript + Vite + Tailwind + shadcn/ui

## After Creation

\```bash
cd my-project

# Initialize git
git init

# Install dependencies
uv sync --dev --upgrade

# Install pre-commit hooks
uv run pre-commit install

# Run tests
uv run pytest
\```
```

---

## Implementation Checklist

- [ ] Create `copier.yml` configuration
- [ ] Create `template/` directory structure
- [ ] Create `template/pyproject.toml.jinja`
- [ ] Create `template/.pre-commit-config.yaml`
- [ ] Create `template/.gitignore` (comprehensive)
- [ ] Create `template/.gitattributes`
- [ ] Create `template/.editorconfig`
- [ ] Create `template/.python-version`
- [ ] Create `template/src/{{ package_name }}/__init__.py`
- [ ] Create `template/src/{{ package_name }}/py.typed`
- [ ] Create `template/tests/__init__.py`
- [ ] Create `template/tests/conftest.py`
- [ ] Create `template/tests/test_example.py`
- [ ] Create `template/.github/workflows/ci.yml`
- [ ] Create `template/.claude/settings.json.jinja`
- [ ] Create `template/.claude/README.md.jinja`
- [ ] Create `template/CLAUDE.md.jinja`
- [ ] Create `template/README.md.jinja`
- [ ] Create `template/LICENSE.txt`
- [ ] Create `template/docs/product-design.md`
- [ ] Create `template/docs/architecture.md.jinja`
- [ ] Create `template/docs/plans/.gitkeep`
- [ ] Create `template/frontend/` (conditional fullstack)
- [ ] Create template repo README.md
- [ ] Test with `copier copy . /tmp/test-project`
- [ ] Test fullstack with `copier copy . /tmp/test-fullstack --data fullstack=true`
