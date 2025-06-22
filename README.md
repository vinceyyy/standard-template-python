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
uvx copier copy https://github.com/vinceyyy/standard-template-python ./my-project
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
