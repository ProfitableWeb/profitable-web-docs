# Pre-commit Configuration - ProfitableWeb

## 🎯 Цель

Pre-commit hooks обеспечивают качество кода на этапе коммита, автоматически запуская проверки и форматирование перед сохранением изменений в git.

## 🔧 Backend (Python + Poetry)

### Установка pre-commit

```bash
# В корне backend проекта
poetry add --group dev pre-commit black isort flake8 mypy
poetry run pre-commit install
```

### Конфигурация `.pre-commit-config.yaml`

```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-added-large-files
      - id: check-json
      - id: check-toml

  - repo: https://github.com/psf/black
    rev: 23.12.1
    hooks:
      - id: black
        language_version: python3
        args: [--line-length=88]

  - repo: https://github.com/pycqa/isort
    rev: 5.13.2
    hooks:
      - id: isort
        args: [--profile=black, --line-length=88]

  - repo: https://github.com/pycqa/flake8
    rev: 7.0.0
    hooks:
      - id: flake8
        args: [--max-line-length=88, --extend-ignore=E203,W503]

  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.8.0
    hooks:
      - id: mypy
        additional_dependencies: [types-all]
        args: [--ignore-missing-imports]
```

### pyproject.toml конфигурация

```toml
[tool.black]
line-length = 88
target-version = ['py311']
include = '\.pyi?$'

[tool.isort]
profile = "black"
line_length = 88
multi_line_output = 3

[tool.mypy]
python_version = "3.11"
warn_return_any = true
warn_unused_configs = true
disallow_untyped_defs = true
ignore_missing_imports = true

[tool.flake8]
max-line-length = 88
extend-ignore = ["E203", "W503"]
```

## 🎨 Frontend (Next.js + TypeScript)

### Установка pre-commit

```bash
# В корне frontend проекта
npm install --save-dev @pre-commit/cli husky lint-staged
npm install --save-dev eslint prettier @typescript-eslint/parser @typescript-eslint/eslint-plugin
npx husky install
```

### Конфигурация `.pre-commit-config.yaml`

```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-json
      - id: check-added-large-files

  - repo: local
    hooks:
      - id: eslint
        name: eslint
        entry: npx eslint --fix
        language: node
        types: [javascript, jsx, ts, tsx]
        exclude: node_modules

      - id: prettier
        name: prettier
        entry: npx prettier --write
        language: node
        types: [javascript, jsx, ts, tsx, json, yaml, md]
        exclude: node_modules

      - id: typescript
        name: typescript
        entry: npx tsc --noEmit
        language: node
        types: [ts, tsx]
        exclude: node_modules
```

### Package.json scripts

```json
{
  "scripts": {
    "lint": "eslint . --ext .ts,.tsx,.js,.jsx",
    "lint:fix": "eslint . --ext .ts,.tsx,.js,.jsx --fix",
    "format": "prettier --write .",
    "format:check": "prettier --check .",
    "type-check": "tsc --noEmit",
    "pre-commit": "npm run lint:fix && npm run format && npm run type-check"
  },
  "lint-staged": {
    "*.{ts,tsx,js,jsx}": [
      "eslint --fix",
      "prettier --write"
    ],
    "*.{json,md,yml,yaml}": [
      "prettier --write"
    ]
  }
}
```

### Husky setup

```bash
# Создание pre-commit hook
npx husky add .husky/pre-commit "npx lint-staged"

# Создание commit-msg hook для conventional commits
npx husky add .husky/commit-msg "npx commitizen --hook || true"
```

## 🔧 Admin Panel

Аналогично frontend конфигурации, но с дополнительными правилами для админ-интерфейса:

### ESLint дополнительные правила

```json
{
  "extends": [
    "next/core-web-vitals",
    "@typescript-eslint/recommended"
  ],
  "rules": {
    "react-hooks/exhaustive-deps": "error",
    "@typescript-eslint/no-unused-vars": "error",
    "@typescript-eslint/explicit-function-return-type": "warn",
    "react/prop-types": "off"
  }
}
```

## 🎯 Общие правила

### Conventional Commits с номерами задач

**Для JIRA:**
```bash
# Установка commitizen с JIRA плагином
npm install -g commitizen cz-conventional-changelog @commitlint/cli @commitlint/config-conventional

# Конфигурация commitlint.config.js
module.exports = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    'subject-case': [2, 'never', ['start-case', 'pascal-case', 'upper-case']],
    'header-max-length': [2, 'always', 100],
    'references-empty': [2, 'never']
  },
  parserPreset: {
    parserOpts: {
      // Парсинг JIRA номеров типа [PW-123]
      referenceActions: null,
      issuePrefixes: ['PW-', 'BLOG-', 'API-']
    }
  }
}
```

**Для GitHub Issues:**
```bash
# Commit с ссылкой на issue
git commit -m "feat: add search functionality\n\nCloses #42"

# Или связанный с issue
git commit -m "fix: resolve login bug\n\nRefs #15"
```

**Для внутренних задач (profitable-web-docs планирование):**
```bash
# Ссылка на задачу в DEVELOPMENT_ROADMAP.md
git commit -m "feat: implement article API\n\nRef: ROADMAP.md#Backend-API-endpoints"

# Или с номером задачи из плана
git commit -m "fix: resolve SCSS compilation\n\nTask: Frontend-1.3-SCSS-configuration"
```

### Типы коммитов с задачами:
- `feat(PW-123): add user authentication` - новая функциональность
- `fix(API-45): resolve database connection` - исправление бага  
- `docs(BLOG-12): update setup guide` - обновление документации
- `style: format code with prettier` - форматирование кода
- `refactor(PW-89): optimize API queries` - рефакторинг
- `test(PW-56): add integration tests` - добавление тестов
- `chore: update dependencies` - обновление сборки, зависимостей

### Автоматизация CI/CD

```yaml
# .github/workflows/pre-commit.yml
name: Pre-commit checks
on: [push, pull_request]

jobs:
  pre-commit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v4
        with:
          python-version: 3.11
      - uses: pre-commit/action@v3.0.0
```

## 🔗 Интеграция с Task Managers

### Git hooks для автоматического обновления задач

**prepare-commit-msg hook:**
```bash
#!/bin/sh
# .git/hooks/prepare-commit-msg

# Получаем номер задачи из названия бранча
BRANCH_NAME=$(git symbolic-ref --short HEAD 2>/dev/null)
TICKET=$(echo $BRANCH_NAME | grep -o -E '(PW|API|BLOG)-[0-9]+' | head -1)

if [ -n "$TICKET" ]; then
    # Если номер задачи еще не в коммите, добавляем
    if ! grep -q "$TICKET" "$1"; then
        sed -i "1s/^/($TICKET) /" "$1"
    fi
fi
```

**Пример workflow:**
```bash
# Создаем бранч с номером задачи
git checkout -b feature/PW-123-add-authentication

# Делаем изменения
git add .
git commit -m "feat: add user authentication"
# Автоматически становится: "(PW-123) feat: add user authentication"
```

## ✅ Development Workflow

1. **Создание ветки с номером задачи**
   ```bash
   git checkout -b feature/PW-123-implement-search
   ```

2. **Разработка с pre-commit проверками**
   ```bash
   # Делаем изменения
   git add .
   git commit -m "feat: implement search functionality"
   # Автоматически: (PW-123) feat: implement search functionality
   ```

3. **Автоматически выполняется:**
   - Форматирование кода (black/prettier)
   - Проверка линтером (flake8/eslint)
   - Проверка типов (mypy/tsc)
   - Добавление номера задачи в коммит
   - Общие проверки (trailing spaces, file size)

4. **Коммит проходит только при успешных проверках**

5. **Push и создание PR с автоматической связью задачи**

## 🚫 Обход проверок (не рекомендуется)

```bash
# Только в крайних случаях
git commit --no-verify -m "emergency fix"
```

---

*Pre-commit hooks обеспечивают высокое качество кода и единообразие стиля*