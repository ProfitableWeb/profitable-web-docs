# Инструкция по настройке среды разработки

## 📋 Предварительные требования

### Системные требования
- **OS**: Windows 10/11, macOS, или Linux
- **Node.js**: 18.x или новее
- **Python**: 3.11 или новее
- **PostgreSQL**: 14 или новее
- **Redis**: 6.x или новее
- **Git**: Последняя версия

### Инструменты разработки
- **IDE**: VS Code/Qoder/Cursor 
- **Package Manager**: npm/yarn для Node.js, pip для Python
- **Container**: Docker (опционально, но рекомендуется)

## 🚀 Быстрая настройка

### 1. Клонирование репозиториев

```bash
# Создание рабочей директории
mkdir ProfitableWeb
cd ProfitableWeb

# Клонирование всех репозиториев
git clone https://github.com/ProfitableWeb/profitable-web-backend.git
git clone https://github.com/ProfitableWeb/profitable-web-frontend.git
git clone https://github.com/ProfitableWeb/profitable-web-admin.git
git clone https://github.com/ProfitableWeb/profitable-web-docs.git
```

### 2. Backend Setup

```bash
cd profitable-web-backend

# Установка Poetry (если не установлен)
curl -sSL https://install.python-poetry.org | python3 -

# Инициализация проекта
poetry init
poetry install

# Настройка pre-commit hooks
poetry run pre-commit install

# Настройка переменных окружения
cp .env.example .env
# Отредактировать .env файл с вашими настройками

# Миграции базы данных
poetry run alembic upgrade head

# Запуск сервера разработки
poetry run uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

### 3. Frontend Setup

```bash
cd ../profitable-web-frontend

# Установка зависимостей
npm install

# Настройка pre-commit hooks
npx husky install

# Настройка переменных окружения
cp .env.local.example .env.local
# Отредактировать .env.local с вашими настройками

# Запуск сервера разработки
npm run dev
```

### 4. Admin Panel Setup

```bash
cd ../profitable-web-admin

# Установка зависимостей
npm install

# Настройка переменных окружения
cp .env.local.example .env.local
# Отредактировать .env.local с настройками админки

# Запуск сервера разработки
npm run dev
```

## 🐳 Docker Setup (Альтернативный вариант)

### Полный запуск через Docker Compose

```bash
# В корневой директории проекта
docker-compose up -d

# Проверка статуса сервисов
docker-compose ps

# Просмотр логов
docker-compose logs -f [service-name]
```

### Отдельные сервисы

```bash
# Только база данных и Redis
docker-compose up -d postgres redis

# Backend сервис
docker-compose up -d backend

# Frontend сервисы
docker-compose up -d frontend admin
```

## ⚙️ Переменные окружения

### Backend (.env)
```env
# Database
DATABASE_URL=postgresql://user:password@localhost:5432/profitableweb
REDIS_URL=redis://localhost:6379

# Security
JWT_SECRET=your-super-secret-jwt-key
CORS_ORIGINS=http://localhost:3000,http://localhost:3001

# External APIs
OPENAI_API_KEY=your-openai-key
SENDGRID_API_KEY=your-sendgrid-key

# File Storage
UPLOAD_DIR=./uploads
MAX_FILE_SIZE=10485760  # 10MB
```

### Frontend (.env.local)
```env
# API Configuration
NEXT_PUBLIC_API_URL=http://localhost:8000
NEXT_PUBLIC_SITE_URL=http://localhost:3000

# Analytics
NEXT_PUBLIC_GA_ID=your-google-analytics-id

# Feature Flags
NEXT_PUBLIC_ENABLE_ANALYTICS=true
NEXT_PUBLIC_ENABLE_COMMENTS=false
```

### Admin Panel (.env.local)
```env
# API Configuration
NEXT_PUBLIC_API_URL=http://localhost:8000
NEXT_PUBLIC_ADMIN_URL=http://localhost:3001

# Authentication
NEXT_PUBLIC_JWT_EXPIRY=24h

# Features
NEXT_PUBLIC_ENABLE_DRAFT_MODE=true
NEXT_PUBLIC_ENABLE_PREVIEW=true
```

## 🔧 Настройка базы данных

### PostgreSQL Setup

```sql
-- Создание пользователя и базы данных
CREATE USER profitableweb WITH PASSWORD 'your-password';
CREATE DATABASE profitableweb OWNER profitableweb;
GRANT ALL PRIVILEGES ON DATABASE profitableweb TO profitableweb;
```

### Миграции

```bash
# Создание новой миграции
alembic revision --autogenerate -m "Description of changes"

# Применение миграций
alembic upgrade head

# Откат миграций
alembic downgrade -1
```

## 🧪 Тестирование

### Backend Tests

```bash
cd profitable-web-backend

# Установка тестовых зависимостей
pip install -r requirements-dev.txt

# Запуск тестов
pytest

# Запуск с покрытием кода
pytest --cov=app tests/
```

### Frontend Tests

```bash
cd profitable-web-frontend

# Запуск unit тестов
npm run test

# Запуск e2e тестов
npm run test:e2e

# Запуск с watch mode
npm run test:watch
```

## 🔍 Полезные команды

### Общие команды
```bash
# Проверка всех сервисов
make health-check

# Полная очистка и пересборка
make clean && make setup

# Backup базы данных
make db-backup

# Restore базы данных
make db-restore
```

### Разработка
```bash
# Форматирование кода
make format

# Линтинг
make lint

# Типы проверка (TypeScript)
make type-check

# Сборка для продакшена
make build
```

## 🚨 Troubleshooting

### Частые проблемы

**Проблема**: Backend не запускается
**Решение**: Проверьте переменные окружения и доступность PostgreSQL

**Проблема**: Frontend не может подключиться к API
**Решение**: Убедитесь, что CORS настроен правильно в backend

**Проблема**: Миграции не применяются
**Решение**: Проверьте права доступа к базе данных

### Логи и отладка

```bash
# Backend логи
tail -f logs/app.log

# Database логи
sudo tail -f /var/log/postgresql/postgresql-14-main.log

# Docker логи
docker-compose logs -f backend
```

---

*При возникновении проблем создавайте issue в соответствующем репозитории*