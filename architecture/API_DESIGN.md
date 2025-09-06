# API Design - ProfitableWeb Backend

## 🎯 Принципы дизайна API

### RESTful Architecture
- Четкая структура ресурсов
- HTTP методы по назначению (GET, POST, PUT, DELETE)
- Статусные коды для индикации результата
- Консистентное именование эндпоинтов

### Версионирование
- API версии в URL: `/api/v1/articles`
- Обратная совместимость
- Graceful deprecation старых версий

## 📋 Основные эндпоинты

### Статьи (Articles)
```
GET    /api/v1/articles           # Список статей с пагинацией
GET    /api/v1/articles/{id}      # Детальная статья
POST   /api/v1/articles           # Создание статьи
PUT    /api/v1/articles/{id}      # Обновление статьи
DELETE /api/v1/articles/{id}      # Удаление статьи
```

### Категории (Categories)
```
GET    /api/v1/categories         # Список категорий
GET    /api/v1/categories/{id}    # Детальная категория
POST   /api/v1/categories         # Создание категории
PUT    /api/v1/categories/{id}    # Обновление категории
```

### Аналитика (Analytics)
```
GET    /api/v1/analytics/overview    # Общая статистика
GET    /api/v1/analytics/articles    # Аналитика по статьям
GET    /api/v1/analytics/hobbies     # Метрики монетизации хобби
```

### Аутентификация (Authentication)
```
POST   /api/v1/auth/login         # Вход
POST   /api/v1/auth/logout        # Выход
POST   /api/v1/auth/refresh       # Обновление токена
GET    /api/v1/auth/profile       # Профиль пользователя
```

## 📊 Схемы данных

### Article Schema
```json
{
  "id": "uuid",
  "title": "string",
  "slug": "string",
  "content": "text",
  "excerpt": "string",
  "published": "boolean",
  "publishedAt": "datetime",
  "createdAt": "datetime",
  "updatedAt": "datetime",
  "category": {
    "id": "uuid",
    "name": "string",
    "slug": "string"
  },
  "tags": ["string"],
  "metadata": {
    "seoTitle": "string",
    "seoDescription": "string",
    "featuredImage": "string"
  },
  "analytics": {
    "views": "integer",
    "engagementRate": "float",
    "monetizationScore": "float"
  }
}
```

### Category Schema
```json
{
  "id": "uuid",
  "name": "string",
  "slug": "string",
  "description": "text",
  "articlesCount": "integer",
  "createdAt": "datetime"
}
```

### Analytics Schema
```json
{
  "overview": {
    "totalArticles": "integer",
    "totalViews": "integer",
    "totalRevenue": "float",
    "activeHobbies": "integer"
  },
  "articleMetrics": [
    {
      "articleId": "uuid",
      "title": "string",
      "views": "integer",
      "engagementRate": "float",
      "conversionRate": "float"
    }
  ],
  "hobbyMetrics": [
    {
      "hobbyName": "string",
      "timeInvested": "integer",
      "moneySpent": "float",
      "revenueGenerated": "float",
      "roi": "float"
    }
  ]
}
```

## 🔐 Безопасность

### JWT Authentication
- Access tokens (15 минут)
- Refresh tokens (7 дней)
- Role-based access control

### Rate Limiting
- Public API: 100 запросов/минуту
- Authenticated API: 1000 запросов/минуту
- Admin API: без лимитов

### Валидация данных
- Input sanitization
- Schema validation
- SQL injection protection

## 📈 Пагинация и фильтрация

### Пагинация
```json
{
  "data": [...],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 150,
    "totalPages": 8,
    "hasNext": true,
    "hasPrev": false
  }
}
```

### Фильтрация и сортировка
```
GET /api/v1/articles?category=ai&published=true&sort=-publishedAt&limit=10
```

## ⚡ Производительность

### Кэширование
- Redis для частых запросов
- ETags для условных запросов
- Cache headers для статического контента

### Оптимизация запросов
- Lazy loading связанных данных
- Database indexing
- Query optimization

---

*API документация регулярно обновляется*