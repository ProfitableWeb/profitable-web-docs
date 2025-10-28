# PW-005: Masonry Grid для статей

## 📋 Информация о задаче

- **ID**: PW-005
- **Статус**: ✅ РЕАЛИЗОВАНО
- **Создано**: 2025-10-25
- **Завершено**: 2025-10-26
- **Приоритет**: High ⚡
- **Компонент**: ⚛️ Frontend
- **Теги**: #frontend #masonry #staggered-animation #typography #animations #ui-components #responsive

## Обзор задачи

Реализовать адаптивную masonry-сетку для отображения карточек статей с поддержкой динамической загрузки контента, staggered-анимаций появления и SEO-оптимизации.

**Статус:** ✅ РЕАЛИЗОВАНО  
**Приоритет:** Высокий  
**Макет Figma:** [ProfitableWeb-UI (node-id: 32-623)](https://www.figma.com/design/bFcdFDewK5LSXgRXHVPCSh/ProfitableWeb-UI?node-id=32-623&m=dev)  
**Реализация:** `src/components/common/masonry/`

---

## Технический стек

- **Framework:** Next.js 14+ (App Router)
- **Анимации:** Framer Motion
- **Стилизация:** В соответствии с существующей системой проекта
- **TypeScript:** Обязательно

---

## Требования к функциональности

### 1. Структура компонентов

```
src/components/common/masonry/
├── MasonryGrid.tsx              # Server Component - обёртка для SEO ✅
├── ArticleList.tsx              # Client Component - с анимациями ✅
├── ArticleCard.tsx              # Карточка статьи с адаптивным поведением ✅
├── LoadMoreButton.tsx           # Кнопка "Загрузить ещё" ✅
├── types/
│   └── article.ts               # TypeScript типы ✅
│
├── hooks/
│   ├── useResponsiveColumns.ts  # Hook для определения количества колонок ✅
│   ├── useMediaQuery.ts         # Media query hook для адаптивности ✅
│   └── usePreferReducedMotion.ts # Hook для accessibility ✅
├── utils/
│   └── distribution.ts          # Алгоритм распределения по колонкам ✅
├── data/
│   └── mock-articles.ts         # Mock-данные (32 статьи) ✅
├── *.scss                       # Стили компонентов ✅
└── index.ts                     # Публичный API модуля ✅

src/utils/
├── breakpoints.ts               # Константы breakpoint'ов ✅
└── seo.ts                       # SEO утилиты (Schema.org, форматирование) ✅

app/ (или pages/)
├── page.tsx                     # Главная страница (Server Component)
├── sitemap.ts                   # Динамическая генерация sitemap
├── robots.ts                    # Robots.txt
└── layout.tsx                   # Root layout с metadata
```

### 2. Типы данных

**ArticleCard Interface:**

```typescript
interface Article {
  id: string;
  title: string;
  subtitle: string;
  createdAt: string; // ISO 8601 формат
  summary: string; // HTML-разметка для параграфов
  imageUrl?: string; // Опциональное изображение
  imageAlt?: string;
  slug: string; // URL-friendly идентификатор для SEO
  category?: string;
  readTime?: number; // Минуты
  blurDataURL?: string; // Blur placeholder для изображения
}
```

### 3. Адаптивный Layout

Масштабируемая система колонок от смартфонов до ultra-wide мониторов:

#### 📱 Mobile (< 768px) ✅

- **1 колонка**
- Gap между карточками: 24px (увеличен для лучшей читаемости)
- Ширина карточки: 100%
- Padding контейнера: 20px (горизонтально)
- **⚠️ ВАЖНО:** На мобильных карточки упрощены — показываются только: заголовок, подзаголовок и дата (БЕЗ изображения и аннотации)
- **Макет:** [Mobile version (node-id: 33-1145)](https://www.figma.com/design/bFcdFDewK5LSXgRXHVPCSh/ProfitableWeb-UI?node-id=33-1145&m=dev)

#### 💻 Tablet (768px - 1199px) ✅

- **2 колонки**
- Gap между колонками: 30px
- Gap между карточками: 10px
- Ширина карточки: динамическая (50% - gap)
- Padding контейнера: 40px (горизонтально)

#### 🖥️ Desktop (1200px - 1799px) ✅

- **3 колонки** (базовая версия из макета)
- Gap между колонками: 40px
- Gap между карточками: 10px
- Ширина карточки: 360px
- Padding контейнера: 60px (горизонтально)
- **Макет:** [Desktop version (node-id: 32-623)](https://www.figma.com/design/bFcdFDewK5LSXgRXHVPCSh/ProfitableWeb-UI?node-id=32-623&m=dev)

#### 🖥️ Large Desktop (1800px - 2399px) ✅

- **4 колонки**
- Gap между колонками: 40px
- Gap между карточками: 10px
- Ширина карточки: 360px
- Padding контейнера: 60px (горизонтально)
- Max-width контейнера: 2000px (центрирован)

#### 🖥️ XL Desktop (2400px - 3199px) ✅

- **5 колонок**
- Gap между колонками: 40px
- Gap между карточками: 10px
- Ширина карточки: 360px
- Padding контейнера: 80px (горизонтально)
- Max-width контейнера: 2600px (центрирован)

#### 🖥️ Ultra-Wide (≥ 3200px) ✅

- **6 колонок**
- Gap между колонками: 40px
- Gap между карточками: 10px
- Ширина карточки: 360px
- Padding контейнера: 100px (горизонтально)
- Max-width контейнера: 3200px (центрирован)

**Принцип масштабирования:**

```typescript
const getColumnCount = (width: number): number => {
  if (width < 768) return 1;
  if (width < 1200) return 2;
  if (width < 1800) return 3;
  if (width < 2400) return 4;
  if (width < 3200) return 5;
  return 6;
};
```

**Важно:**

- Карточки сохраняют фиксированную ширину 360px на всех разрешениях (кроме mobile/tablet)
- Используется max-width контейнера для предотвращения чрезмерного растяжения на огромных экранах
- На ultra-wide мониторах контейнер центрируется с увеличенными отступами

**Таблица breakpoint'ов:**

| Разрешение | Breakpoint  | Колонок | Gap  | Padding | Max-width | Примеры устройств          |
| ---------- | ----------- | ------- | ---- | ------- | --------- | -------------------------- |
| Mobile     | < 768px     | 1       | 24px | 20px    | -         | iPhone, Android            |
| Tablet     | 768-1199px  | 2       | 30px | 40px    | -         | iPad, Galaxy Tab           |
| Desktop    | 1200-1799px | 3       | 40px | 60px    | -         | Обычные мониторы 1920x1080 |
| Large      | 1800-2399px | 4       | 40px | 60px    | 2000px    | 2K мониторы                |
| XL         | 2400-3199px | 5       | 40px | 80px    | 2600px    | 4K мониторы                |
| Ultra-Wide | ≥ 3200px    | 6       | 40px | 100px   | 3200px    | 21:9, 32:9, 8K             |

### 4. Карточка статьи (ArticleCard)

#### Структура (сверху вниз):

1. **Заголовок (article-title)**

   - Шрифт: Inter Bold, 18px
   - Line-height: 1.2
   - Цвет: #000000
   - Padding-bottom: 8px

2. **Подзаголовок (article-sub-title)**

   - Шрифт: Inter Medium, 16px
   - Line-height: 20px (1.25)
   - Цвет: #464646

3. **Дата публикации (article-created-at)**

   - Шрифт: Inter Regular, 10px
   - Line-height: 16px
   - Цвет: #B8B8B8
   - Padding: 8px 0
   - Формат: DD.MM.YYYY

4. **Изображение (article-card-img-wrap)** [опционально]

   - Высота: 152px
   - Border-radius: 6px
   - Background placeholder: #EAEAEA
   - Margin-bottom: 16px
   - **Важно:** Используйте Next.js Image с priority для первых карточек

5. **Аннотация (article-summary-text)**
   - Шрифт: Inter Regular, 13px
   - Line-height: 1.5
   - Цвет: #2C2C2C
   - Поддержка HTML-параграфов с mb-2 (8px)

#### Стилизация карточки:

- Background: #FFFFFF
- Padding-bottom: 30px
- Width: 360px (desktop), 100% (адаптив)
- Overflow: clip

#### 🎯 Адаптивное поведение карточки:

**Desktop & Tablet (≥ 768px):**

- Показывать все элементы: заголовок + подзаголовок + дата + изображение (если есть) + аннотация

**Mobile (< 768px):**

- Показывать ТОЛЬКО: заголовок + подзаголовок + дата
- Скрывать: изображение + аннотацию (для экономии места и быстрого сканирования списка)
- Gap между карточками увеличить до 24px для лучшей читаемости

```typescript
// Вариант 1: Условный рендеринг (лучше для производительности, но хуже для SEO)
const isMobile = useMediaQuery("(max-width: 767px)");

return (
  <article>
    <h2>{title}</h2>
    <p>{subtitle}</p>
    <time>{date}</time>
    {!isMobile && (
      <>
        {imageUrl && <ArticleCardImage src={imageUrl} alt={imageAlt} />}
        <div dangerouslySetInnerHTML={{ __html: summary }} />
      </>
    )}
  </article>
);

// Вариант 2: CSS-скрытие (лучше для SEO, контент всегда в DOM)
return (
  <article>
    <h2>{title}</h2>
    <p>{subtitle}</p>
    <time>{date}</time>
    <div className="hidden md:block">
      {imageUrl && <ArticleCardImage src={imageUrl} alt={imageAlt} />}
      <div dangerouslySetInnerHTML={{ __html: summary }} />
    </div>
  </article>
);

// Рекомендация: используйте Вариант 1 (условный рендеринг)
// Причины:
// - Меньше DOM-элементов на мобильных = лучше производительность
// - Не загружаются изображения на мобильных = экономия трафика
// - SEO: карточка — это preview, полный контент на странице статьи
```

### 5. Алгоритм Masonry

**Подход:** CSS Grid с распределением по колонкам

**Важно для SEO:**

- Карточки должны идти в правильном порядке в HTML (для индексации)
- Использовать `grid-row` для позиционирования
- Избегать `position: absolute` для карточек

**Рекомендуемая реализация:**

1. Разбить статьи по колонкам на стороне клиента
2. Распределять карточки по колонкам с минимальной высотой (shortest column first)
3. Использовать CSS Grid с `grid-template-columns: repeat(auto-fit, minmax(360px, 1fr))`

### 6. Анимации (Framer Motion)

#### Staggered появление карточек:

```typescript
// Пример конфигурации
const containerVariants = {
  hidden: { opacity: 0 },
  visible: {
    opacity: 1,
    transition: {
      staggerChildren: 0.08, // Задержка между карточками
      delayChildren: 0.1,
    },
  },
};

const cardVariants = {
  hidden: {
    opacity: 0,
    y: 20,
    scale: 0.95,
  },
  visible: {
    opacity: 1,
    y: 0,
    scale: 1,
    transition: {
      duration: 0.4,
      ease: [0.25, 0.46, 0.45, 0.94], // easeOutQuad
    },
  },
};
```

#### Анимация кнопки "Загрузить ещё":

```typescript
const loadMoreVariants = {
  idle: { scale: 1 },
  hover: { scale: 1.02 },
  tap: { scale: 0.98 },
};
```

#### Анимация новых карточек при подгрузке:

- Те же варианты что и для initial load
- Плавное появление снизу (y: 20 → 0)
- Opacity: 0 → 1
- Duration: 0.4s

#### 🎬 Анимация при resize окна (перестройка колонок):

**Задача:** При изменении ширины окна браузера (например, 3 колонки → 2 колонки) карточки должны плавно перемещаться на новые позиции.

**Подход 1: Layout Animations (рекомендуется)**

```typescript
import { motion, LayoutGroup } from "framer-motion";

// Контейнер masonry
<LayoutGroup>
  <motion.div className="masonry-grid">
    {columns.map((column, colIndex) => (
      <motion.div key={colIndex} className="masonry-column" layout>
        {column.map((article) => (
          <motion.article
            key={article.id}
            layout // Автоматическая анимация позиции
            transition={{
              layout: {
                duration: 0.5,
                ease: [0.34, 1.56, 0.64, 1], // easeOutBack
              },
            }}
          >
            <ArticleCard article={article} />
          </motion.article>
        ))}
      </motion.div>
    ))}
  </motion.div>
</LayoutGroup>;
```

**Подход 2: AnimatePresence + Key Changes**

```typescript
import { AnimatePresence, motion } from "framer-motion";
import { useWindowSize } from "@/hooks/useWindowSize";
import { useMemo } from "react";

function MasonryGrid({ articles }) {
  const { width } = useWindowSize();
  const columnCount = useMemo(() => getColumnCount(width), [width]);

  // Пересчитываем колонки при изменении количества
  const columns = useMemo(
    () => distributeArticles(articles, columnCount),
    [articles, columnCount]
  );

  return (
    <motion.div
      key={columnCount} // Ключ меняется = триггер анимации
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      transition={{ duration: 0.3 }}
      className="masonry-grid"
    >
      {columns.map((column, colIndex) => (
        <motion.div
          key={colIndex}
          className="masonry-column"
          initial={{ x: -20, opacity: 0 }}
          animate={{ x: 0, opacity: 1 }}
          transition={{
            duration: 0.4,
            delay: colIndex * 0.05, // Stagger по колонкам
          }}
        >
          {column.map((article) => (
            <motion.article
              key={article.id}
              layout
              transition={{
                duration: 0.4,
                ease: "easeInOut",
              }}
            >
              <ArticleCard article={article} />
            </motion.article>
          ))}
        </motion.div>
      ))}
    </motion.div>
  );
}
```

**Оптимизация: Debounce resize events**

```typescript
import { useState, useEffect } from "react";
import { debounce } from "lodash";

function useResponsiveColumns() {
  const [columnCount, setColumnCount] = useState(3);

  useEffect(() => {
    const handleResize = debounce(() => {
      const width = window.innerWidth;
      setColumnCount(getColumnCount(width));
    }, 150); // 150ms задержка для плавности

    handleResize(); // Initial call
    window.addEventListener("resize", handleResize);

    return () => {
      handleResize.cancel();
      window.removeEventListener("resize", handleResize);
    };
  }, []);

  return columnCount;
}
```

**Рекомендации:**

1. **Используйте `layout` prop** от Framer Motion — это автоматически анимирует изменения позиции/размера
2. **Debounce обязателен** — без него будет слишком много перерасчётов при плавном resize
3. **LayoutGroup** для синхронизации анимаций между родственными элементами
4. **Оптимизация производительности:**
   - Не анимируйте resize на мобильных (там resize почти не бывает)
   - Используйте `will-change: transform` для GPU-ускорения
   - Отключайте анимации при первом рендере

**CSS для плавности:**

```css
.masonry-column {
  transition: width 0.4s ease-in-out;
}

@media (prefers-reduced-motion: reduce) {
  .masonry-column {
    transition: none;
  }
  /* Отключаем анимации для пользователей с настройками accessibility */
}
```

**Параметры анимации resize:**

- **Duration:** 0.4-0.5s (оптимальное восприятие)
- **Easing:** easeInOut или easeOutBack (для более живого эффекта)
- **Stagger между колонками:** 0.05s
- **Debounce delay:** 150ms

**Важно для UX:**

- Анимация должна быть **быстрее**, чем пользователь успевает отпустить мышь после resize
- На очень больших списках (100+ карточек) анимируйте только viewport
- Учитывайте `prefers-reduced-motion` для accessibility

**Готовые hooks для использования:**

```typescript
// hooks/useWindowSize.ts
import { useState, useEffect } from "react";

export function useWindowSize() {
  const [size, setSize] = useState({
    width: typeof window !== "undefined" ? window.innerWidth : 1200,
    height: typeof window !== "undefined" ? window.innerHeight : 800,
  });

  useEffect(() => {
    function handleResize() {
      setSize({
        width: window.innerWidth,
        height: window.innerHeight,
      });
    }

    window.addEventListener("resize", handleResize);
    return () => window.removeEventListener("resize", handleResize);
  }, []);

  return size;
}

// hooks/usePreferReducedMotion.ts
import { useEffect, useState } from "react";

export function usePreferReducedMotion() {
  const [prefersReducedMotion, setPrefersReducedMotion] = useState(false);

  useEffect(() => {
    const mediaQuery = window.matchMedia("(prefers-reduced-motion: reduce)");
    setPrefersReducedMotion(mediaQuery.matches);

    const handleChange = (e: MediaQueryListEvent) => {
      setPrefersReducedMotion(e.matches);
    };

    mediaQuery.addEventListener("change", handleChange);
    return () => mediaQuery.removeEventListener("change", handleChange);
  }, []);

  return prefersReducedMotion;
}

// hooks/useResponsiveColumns.ts
import { useState, useEffect } from "react";
import { debounce } from "lodash";
import { getColumnCount } from "@/utils/breakpoints";

export function useResponsiveColumns(debounceMs: number = 150) {
  const [columnCount, setColumnCount] = useState(() =>
    typeof window !== "undefined" ? getColumnCount(window.innerWidth) : 3
  );

  useEffect(() => {
    const handleResize = debounce(() => {
      setColumnCount(getColumnCount(window.innerWidth));
    }, debounceMs);

    handleResize(); // Initial call
    window.addEventListener("resize", handleResize);

    return () => {
      handleResize.cancel();
      window.removeEventListener("resize", handleResize);
    };
  }, [debounceMs]);

  return columnCount;
}

// utils/breakpoints.ts
export const BREAKPOINTS = {
  mobile: 768,
  tablet: 1200,
  desktop: 1800,
  large: 2400,
  xl: 3200,
} as const;

export function getColumnCount(width: number): number {
  if (width < BREAKPOINTS.mobile) return 1;
  if (width < BREAKPOINTS.tablet) return 2;
  if (width < BREAKPOINTS.desktop) return 3;
  if (width < BREAKPOINTS.large) return 4;
  if (width < BREAKPOINTS.xl) return 5;
  return 6;
}
```

### 7. Кнопка "Загрузить ещё"

**Расположение:** По центру под masonry-сеткой

**Стилизация:**

- Border: 2px solid rgba(0, 0, 0, 0.13)
- Border-radius: 12px
- Width: 232px
- Height: 68px
- Padding: 9px 37px
- Шрифт: Inter Bold, 16px
- Line-height: 1.5
- Цвет текста: #000000
- Background: transparent
- Hover: добавить легкое увеличение (scale: 1.02)

**Функциональность:**

- При клике загружать следующие N статей (например, 12)
- Показывать loader во время загрузки
- Скрывать кнопку, когда статей больше нет
- Сохранять состояние скролла после загрузки

#### Пагинация и URL-семантика (опционально вместо кнопки)

- Поддержать роуты `/page/[page]` для SEO-дружелюбной пагинации
- На странице указывать `rel="next"/"prev"` в `<link>` и в `<nav aria-label="Пагинация">`
- Серверная генерация страниц пагинации (SSG/ISR) для быстрого TTFB
- Кнопку "Загрузить ещё" можно оставить как прогрессивное улучшение (enhancement) поверх пагинации

### 8. SEO-оптимизация (Next.js специфика)

#### 🎯 Server-Side Rendering Strategy:

**Критически важно:** Весь контент должен быть доступен при SSR для индексации поисковыми роботами.

```typescript
// app/page.tsx или pages/index.tsx
import { MasonryGrid } from "@/components/masonry/MasonryGrid";
import { getArticles } from "@/lib/api"; // или import из mock-data

// Next.js 13+ App Router (Server Component по умолчанию)
export default async function HomePage() {
  // Данные загружаются на сервере
  const articles = await getArticles(); // или импорт mock-data

  return (
    <main>
      <MasonryGrid articles={articles} />
    </main>
  );
}

// Metadata для SEO
export const metadata = {
  title: "ProfitableWeb - Статьи о монетизации контента",
  description: "Исследовательский сайт о превращении хобби в доходную работу",
  openGraph: {
    title: "ProfitableWeb - Статьи",
    description: "Как превратить хобби в доходную работу",
    images: ["/og-image.jpg"],
  },
};
```

#### 🔄 Гибридная архитектура (Server + Client):

```typescript
// components/masonry/MasonryGrid.tsx
// Server Component (без 'use client')
import { ArticleList } from "./ArticleList";

export function MasonryGrid({ articles }: { articles: Article[] }) {
  // Рендерится на сервере - SEO-friendly
  return (
    <section aria-label="Статьи блога">
      {/* Все статьи в DOM для SEO */}
      <noscript>
        {/* Fallback для ботов без JS */}
        <div className="articles-grid-fallback">
          {articles.map((article) => (
            <ArticleCard key={article.id} article={article} isStatic />
          ))}
        </div>
      </noscript>

      {/* Интерактивная версия с анимациями */}
      <ArticleList articles={articles} />
    </section>
  );
}

// components/masonry/ArticleList.tsx
("use client"); // Client Component для анимаций

import { motion, LayoutGroup } from "framer-motion";
import { useResponsiveColumns } from "@/hooks/useResponsiveColumns";
import { distributeArticles } from "@/utils/masonry";

export function ArticleList({ articles }: { articles: Article[] }) {
  const columnCount = useResponsiveColumns();
  const columns = distributeArticles(articles, columnCount);

  return (
    <LayoutGroup>
      <div className="masonry-grid">{/* Клиентская логика с анимациями */}</div>
    </LayoutGroup>
  );
}
```

#### ✅ Семантическая разметка + Schema.org:

```tsx
// components/masonry/ArticleCard.tsx
import Link from "next/link";
import Image from "next/image";

export function ArticleCard({ article, isPriority = false }) {
  const jsonLd = {
    "@context": "https://schema.org",
    "@type": "BlogPosting",
    headline: article.title,
    description: article.subtitle,
    datePublished: article.createdAt,
    image: article.imageUrl,
    url: `https://profitableweb.ru/articles/${article.slug}`,
    author: {
      "@type": "Organization",
      name: "ProfitableWeb",
    },
  };

  return (
    <article
      itemScope
      itemType="https://schema.org/BlogPosting"
      className="article-card"
    >
      {/* JSON-LD для богатых сниппетов */}
      <script
        type="application/ld+json"
        dangerouslySetInnerHTML={{ __html: JSON.stringify(jsonLd) }}
      />

      <Link href={`/articles/${article.slug}`} className="article-link">
        <h2 itemProp="headline" className="article-title">
          {article.title}
        </h2>

        <p itemProp="description" className="article-subtitle">
          {article.subtitle}
        </p>

        <time
          itemProp="datePublished"
          dateTime={article.createdAt}
          className="article-date"
        >
          {formatDate(article.createdAt)}
        </time>

        {article.imageUrl && (
          <div className="article-image-wrapper">
            <Image
              src={article.imageUrl}
              alt={article.imageAlt || article.title}
              width={360}
              height={152}
              priority={isPriority} // Первые 6 карточек
              loading={isPriority ? "eager" : "lazy"}
              placeholder="blur"
              blurDataURL={article.blurDataURL}
              sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
              itemProp="image"
            />
          </div>
        )}

        <div
          itemProp="articleBody"
          className="article-summary"
          dangerouslySetInnerHTML={{ __html: article.summary }}
        />
      </Link>

      {/* SEO metadata */}
      <meta itemProp="url" content={`/articles/${article.slug}`} />
      <meta itemProp="author" content="ProfitableWeb" />
    </article>
  );
}
```

#### 📱 Mobile Content Hiding - SEO-safe подход:

```tsx
"use client";

import { useMediaQuery } from "@/hooks/useMediaQuery";

export function ArticleCard({ article }) {
  const isMobile = useMediaQuery("(max-width: 767px)");

  return (
    <article>
      <h2>{article.title}</h2>
      <p>{article.subtitle}</p>
      <time>{article.date}</time>

      {/* Вариант 1: CSS-скрытие (лучше для SEO, но хуже для performance) */}
      <div className={isMobile ? "hidden" : "block"}>
        {article.imageUrl && <ArticleImage {...} />}
        <div dangerouslySetInnerHTML={{ __html: article.summary }} />
      </div>

      {/* Вариант 2: Условный рендер (лучше performance, но контент в JS) */}
      {/* Рекомендация: используйте этот вариант */}
      {/* Почему: Google теперь индексирует контент после гидратации React */}
      {!isMobile && (
        <>
          {article.imageUrl && <ArticleImage {...} />}
          <div dangerouslySetInnerHTML={{ __html: article.summary }} />}
        </>
      )}
    </article>
  );
}
```

**Рекомендация:** Используйте условный рендеринг (Вариант 2) по следующим причинам:

- Google Crawler выполняет JavaScript и видит контент после гидратации
- Лучшая производительность на мобильных
- Экономия трафика пользователя
- Полный контент доступен на странице статьи

#### 🖼️ Next.js Image оптимизация:

```typescript
// Генерация blur placeholder для лучшего UX
import { getPlaiceholder } from "plaiceholder";

export async function generateBlurDataURL(imageUrl: string) {
  const buffer = await fetch(imageUrl).then((res) => res.arrayBuffer());
  const { base64 } = await getPlaiceholder(Buffer.from(buffer));
  return base64;
}

// В компоненте
<Image
  src={article.imageUrl}
  alt={article.imageAlt}
  width={360}
  height={152}
  priority={index < 6} // Первые 6 карточек
  loading={index < 6 ? "eager" : "lazy"}
  placeholder="blur"
  blurDataURL={article.blurDataURL}
  sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
  quality={85}
  // Автоматическая оптимизация форматов (WebP, AVIF)
/>;
```

#### 🔗 Internal Linking (Next.js Link):

```tsx
import Link from "next/link";

<Link
  href={`/articles/${article.slug}`}
  prefetch={true} // Prefetch при hover (App Router)
  className="article-card-link"
>
  {/* Контент карточки */}
</Link>;
```

#### 🚀 Performance & SEO метрики:

**Что измеряем:**

- **LCP (Largest Contentful Paint):** < 2.5s
- **FID (First Input Delay):** < 100ms
- **CLS (Cumulative Layout Shift):** < 0.1
- **Time to First Byte (TTFB):** < 600ms
- **Total Blocking Time (TBT):** < 300ms

**Next.js оптимизации:**

```typescript
// next.config.js
module.exports = {
  images: {
    formats: ["image/avif", "image/webp"],
    deviceSizes: [640, 750, 828, 1080, 1200, 1920, 2048, 3840],
    imageSizes: [16, 32, 48, 64, 96, 128, 256, 384],
    minimumCacheTTL: 60,
  },
  experimental: {
    optimizeCss: true, // Оптимизация CSS
  },
};
```

#### 🤖 Sitemap & Robots.txt:

```typescript
// app/sitemap.ts (Next.js 13+)
import { MetadataRoute } from "next";
import { getArticles } from "@/lib/api";

export default async function sitemap(): Promise<MetadataRoute.Sitemap> {
  const articles = await getArticles();

  const articleUrls = articles.map((article) => ({
    url: `https://profitableweb.ru/articles/${article.slug}`,
    lastModified: article.updatedAt || article.createdAt,
    changeFrequency: "weekly" as const,
    priority: 0.8,
  }));

  return [
    {
      url: "https://profitableweb.ru",
      lastModified: new Date(),
      changeFrequency: "daily",
      priority: 1,
    },
    ...articleUrls,
  ];
}

// app/robots.ts
import { MetadataRoute } from "next";

export default function robots(): MetadataRoute.Robots {
  return {
    rules: {
      userAgent: "*",
      allow: "/",
    },
    sitemap: "https://profitableweb.ru/sitemap.xml",
  };
}
```

#### 🎯 SEO Checklist для Masonry:

✅ **SSR-friendly:** Контент рендерится на сервере  
✅ **Schema.org:** JSON-LD для богатых сниппетов  
✅ **Semantic HTML:** `<article>`, `<time>`, правильные заголовки  
✅ **Next.js Image:** Автоматическая оптимизация форматов  
✅ **Internal Linking:** `<Link>` с prefetch  
✅ **Metadata API:** Open Graph, Twitter Cards  
✅ **Sitemap:** Динамическая генерация  
✅ **Mobile-first:** Responsive images с sizes  
✅ **Performance:** LCP < 2.5s, CLS < 0.1  
✅ **Accessibility:** ARIA-labels, semantic markup

#### 🧩 Состояния интерфейса

1. **Loading state**

- Скелетоны вместо карточек (скелетон высотой 152px под изображение + 2 строки текста)
- ARIA: `aria-busy="true"` на контейнере списка

2. **Empty state**

- Текст: "Материалы пока не найдены"
- Кнопка: "Сбросить фильтры" (если применены фильтры)
- Иллюстрация-плейсхолдер (SVG), скрыть от скринридеров `aria-hidden="true"`

3. **Error state**

- Сообщение об ошибке и кнопка "Повторить попытку"
- Логирование ошибки (console + отправка в мониторинг, если подключён)
- Для SEO: отдавать 200 с понятным контентом, а не пустую страницу

4. **No‑JS fallback**

- В `<noscript>` вывести статический список первых N статей (без анимаций)
- Ссылки должны вести на полноценные страницы статей
- Карточки в `<noscript>` включают заголовок, подзаголовок, дату и (если нужно) статичное изображение `<img>`

#### ⚠️ Важные замечания:

1. **Framer Motion должен быть в Client Component** (`'use client'`), но данные приходят из Server Component
2. **Все статьи должны быть в HTML при SSR** — Google индексирует серверный рендер в первую очередь
3. **useWindowSize и resize hooks** работают только на клиенте — используйте CSS media queries как fallback
4. **JSON-LD обязателен** для попадания в Rich Results Google
5. **Canonical URLs** для каждой статьи через Metadata API

### 9. Mock-данные

**Файл:** `data/mock-articles.ts`

Создать массив из 30+ статей с разнообразным контентом:

- 60% карточек с изображениями
- 40% карточек без изображений
- Разная длина аннотаций (50-500 слов)
- Реалистичные заголовки и даты

**Placeholder изображения:**

- Использовать локальные серые блоки (#EAEAEA)
- Или сервисы: `https://placehold.co/360x152/EAEAEA/EAEAEA`

### 10. Производительность

**Критерии:**

- First Contentful Paint < 1.5s (desktop) / < 2s (mobile 3G)
- Largest Contentful Paint < 2.5s (desktop) / < 3.5s (mobile 3G)
- Cumulative Layout Shift < 0.1
- Time to Interactive < 3.5s (desktop) / < 5s (mobile 3G)

**Оптимизации:**

- **Mobile-First Performance:** Условный рендеринг для исключения загрузки изображений и HTML-контента на мобильных
- Виртуализация для очень длинных списков (опционально, если > 100 статей)
- Ленивая загрузка изображений на desktop/tablet
- Мемоизация компонентов карточек (React.memo)
- useMemo для расчета распределения по колонкам
- Использовать `loading="lazy"` для изображений вне viewport
- Минимизировать JavaScript bundle через code splitting

**Ожидаемая экономия на Mobile:**

- ~60% меньше DOM-элементов (без изображений и текста аннотаций)
- ~80% меньше трафика (изображения не загружаются)
- ~40% быстрее Time to Interactive

### 11. Тестирование

**Unit tests:**

- Распределение статей по колонкам
- Форматирование дат
- Рендеринг карточек с/без изображений

**Integration tests:**

- Загрузка дополнительных статей
- Анимации появления (initial load + load more)
- Адаптивное изменение колонок (6 → 5 → 4 → 3 → 2 → 1)
- Адаптивное скрытие контента на мобильных (изображение + аннотация)
- Responsive behavior при resize окна
- Shortest column algorithm для всех количеств колонок
- **Пагинация (если включена):**
  - Навигация по `/page/2`, корректный `rel="next"/"prev"`
  - Сохранение и восстановление фильтров через query params
  - SSG/ISR страницы рендерятся без ошибок
- **Resize-анимации:**
  - Плавность перехода между количествами колонок
  - Debounce работает корректно (не тормозит при быстром resize)
  - Анимации не вызывают layout shifts
  - Layout animations от Framer Motion работают без ошибок

**Performance tests:**

- FPS анимаций resize > 50fps
- Не более 3 reflows при resize
- GPU-ускорение включено для анимаций
- Memory leaks отсутствуют при многократных resize
- **Loading/Empty/Error/No‑JS:**
  - Скелетоны отображаются < 200мс после старта загрузки
  - Empty state не влияет на CLS
  - Error state не блокирует навигацию/повторную загрузку
  - `<noscript>` содержит валидный HTML и корректные ссылки

**Visual regression:**

- Скриншоты для всех 6 breakpoint'ов (mobile, tablet, desktop, large, xl, ultra-wide)
- Проверка анимаций на разных разрешениях
- Проверка центрирования контейнера на больших экранах
- Проверка transition-states между breakpoint'ами
- Видео-тесты плавных переходов (записать Cypress/Playwright видео)

**Accessibility tests:**

- prefers-reduced-motion отключает анимации
- Keyboard navigation работает во время анимаций
- Screen readers не конфликтуют с layout changes

### 12. Фильтрация и сортировка

— Отложено. Реализуется в отдельной задаче после релиза базовой версии masonry.

---

## Этапы реализации

### Этап 1: Базовая структура (2-3 часа)

- [ ] Создать типы TypeScript
- [ ] Создать mock-данные (30+ статей)
- [ ] Реализовать `ArticleCard` компонент
- [ ] Базовый layout без masonry

### Этап 2: Masonry алгоритм (3-4 часа)

- [ ] Реализовать распределение по колонкам (shortest column first)
- [ ] Адаптивная логика для 6 breakpoint'ов (1/2/3/4/5/6 колонок)
- [ ] Реализовать динамическое определение количества колонок
- [ ] Тестирование на разных размерах экрана (включая ultra-wide)
- [ ] Max-width контейнера и центрирование на больших экранах

### Этап 3: Анимации (3-4 часа)

- [ ] Интеграция Framer Motion
- [ ] Staggered появление карточек (initial load)
- [ ] Анимация подгрузки новых статей
- [ ] Layout animations с LayoutGroup
- [ ] Resize-анимации при изменении количества колонок
- [ ] Debounce для resize events
- [ ] Hover-эффекты
- [ ] Поддержка prefers-reduced-motion

### Этап 4: Загрузка контента (1-2 часа)

- [ ] Реализовать кнопку "Загрузить ещё"
- [ ] Логика пагинации mock-данных
- [ ] Loading state
- [ ] End-of-list state

### Этап 5: SEO и доступность (2-3 часа)

- [ ] **Next.js Server Components:**
  - [ ] MasonryGrid как Server Component
  - [ ] ArticleList как Client Component
  - [ ] Правильная гидратация
- [ ] **Schema.org разметка:**
  - [ ] JSON-LD для каждой карточки
  - [ ] Microdata на элементах
- [ ] **Next.js SEO файлы:**
  - [ ] sitemap.ts - динамическая генерация
  - [ ] robots.ts
  - [ ] Metadata API в layout.tsx
- [ ] **Semantic HTML:**
  - [ ] `<article>`, `<time>`, правильные заголовки
  - [ ] ARIA-атрибуты
- [ ] **Next.js Image:**
  - [ ] Все изображения через next/image
  - [ ] Blur placeholders
  - [ ] Priority для первых 6
  - [ ] Sizes prop для responsive
- [ ] **Internal Linking:**
  - [ ] `<Link>` вместо `<a>`
  - [ ] Prefetch включен
- [ ] **Testing:**
  - [ ] Lighthouse SEO audit
  - [ ] Google Rich Results Test
  - [ ] Schema.org validator

### Этап 6: Тестирование и полировка (2-3 часа)

- [ ] Unit tests
- [ ] Визуальное тестирование
- [ ] Performance audit
- [ ] Accessibility audit (Lighthouse)
- [ ] Cross-browser testing

**Общая оценка времени:** 15-21 часов
_(+2 часа на ultra-wide мониторы, +2 часа на resize-анимации, +1 час на Next.js SEO-специфику)_

---

## Критерии приёмки

### Адаптивность

✅ Masonry-сетка адаптируется под 6 breakpoint'ов (1/2/3/4/5/6 колонок)  
✅ Mobile (<768px): 1 колонка, упрощённые карточки (только заголовок + подзаголовок + дата)  
✅ Tablet (768-1199px): 2 колонки, полный контент  
✅ Desktop (1200-1799px): 3 колонки, полный контент  
✅ Large Desktop (1800-2399px): 4 колонки, полный контент  
✅ XL Desktop (2400-3199px): 5 колонок, полный контент  
✅ Ultra-Wide (≥3200px): 6 колонок, полный контент  
✅ Плавные переходы при изменении размера окна (resize)

### Функциональность

✅ Staggered анимации работают плавно (60fps) на всех разрешениях  
✅ Resize-анимации при изменении ширины окна работают плавно (3→2 колонки, 4→5 колонок и т.д.)  
✅ Debounce для resize events реализован (150ms)  
✅ Кнопка "Загрузить ещё" корректно подгружает статьи  
✅ Shortest column first алгоритм работает для всех количеств колонок  
✅ Поддержка prefers-reduced-motion (отключение анимаций для accessibility)

### SEO & Performance

✅ **SSR:** Все статьи рендерятся на сервере (Server Components)  
✅ **Schema.org:** JSON-LD для каждой карточки статьи  
✅ **Metadata API:** Open Graph, Twitter Cards настроены  
✅ **Sitemap:** Динамическая генерация `sitemap.ts`  
✅ **Robots.txt:** Корректный `robots.ts`  
✅ **Next.js Image:** Все изображения через `next/image` с AVIF/WebP  
✅ **Internal Links:** Все ссылки через `<Link>` с prefetch  
✅ **Semantic HTML:** `<article>`, `<time>`, правильная иерархия заголовков  
✅ **Lighthouse Performance:** > 90 (desktop) / > 80 (mobile)  
✅ **Lighthouse SEO:** > 95  
✅ **Lighthouse Accessibility:** > 95  
✅ **Core Web Vitals:**

- LCP < 2.5s
- FID < 100ms
- CLS < 0.1
- TTFB < 600ms  
  ✅ **Google Rich Results Test:** Проходит валидацию  
  ✅ **Canonical URLs:** Корректные для всех страниц

### Качество кода

✅ Код покрыт тестами (coverage > 80%)  
✅ Работает во всех современных браузерах  
✅ TypeScript без ошибок

### UX на больших экранах

✅ Контейнер центрирован с max-width для предотвращения растяжения  
✅ На ultra-wide мониторах отступы увеличены для баланса композиции  
✅ Карточки сохраняют читаемость (360px) на всех разрешениях

---

## Дополнительные улучшения (опционально)

### Phase 2 (после основной реализации):

1. **Infinite scroll** как альтернатива кнопке

   - Intersection Observer API
   - Опция в настройках компонента

2. **Фильтрация по категориям**

   - Анимация перестроения сетки
   - URL query params для SEO

3. **Skeleton loaders**

   - Вместо пустого экрана при первой загрузке
   - Анимированные плейсхолдеры

4. **Hover preview**

   - Увеличение карточки при наведении
   - Показ дополнительной информации

5. **Share функциональность**

   - Кнопки соц. сетей на каждой карточке
   - Web Share API для мобильных

6. **Конфигурируемые breakpoint'ы**
   - Возможность кастомизации breakpoint'ов через props
   - Настройка max количества колонок (для экзотических сценариев)
   - Пример: `<MasonryGrid maxColumns={4} />` для ограничения 4 колонками даже на ultra-wide

---

## Ссылки и ресурсы

### Next.js & SEO

- [Next.js 14 App Router](https://nextjs.org/docs/app)
- [Next.js Server Components](https://nextjs.org/docs/app/building-your-application/rendering/server-components)
- [Next.js Image Optimization](https://nextjs.org/docs/app/building-your-application/optimizing/images)
- [Next.js Metadata API](https://nextjs.org/docs/app/building-your-application/optimizing/metadata)
- [Next.js Sitemap](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/sitemap)
- [Next.js Robots.txt](https://nextjs.org/docs/app/api-reference/file-conventions/metadata/robots)

### SEO & Schema.org

- [Schema.org BlogPosting](https://schema.org/BlogPosting)
- [Google Rich Results Test](https://search.google.com/test/rich-results)
- [Google Search Central - Schema.org](https://developers.google.com/search/docs/appearance/structured-data/intro-structured-data)
- [Web Vitals](https://web.dev/vitals/)
- [Core Web Vitals Tools](https://web.dev/vitals-tools/)

### Animations

- [Framer Motion Stagger Tutorial](https://www.framer.com/motion/animation/#orchestration)
- [Framer Motion Layout Animations](https://www.framer.com/motion/layout-animations/)
- [CSS Grid Masonry](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_grid_layout/Masonry_layout)

### Performance

- [Responsive Design Best Practices](https://web.dev/responsive-web-design-basics/)
- [React Window (Virtualization)](https://github.com/bvaughn/react-window)
- [TanStack Virtual](https://tanstack.com/virtual/latest)
- [Lighthouse CI](https://github.com/GoogleChrome/lighthouse-ci)

---

## Примечания

- Используйте существующие design tokens проекта для цветов и типографики
- Убедитесь, что компонент легко интегрируется с будущим API
- Код должен быть готов к замене mock-данных на real API endpoint
- Придерживайтесь naming conventions проекта
- Все тексты выводить через i18n (подготовка к мультиязычности)
- **Mobile-First:** Упрощение контента на мобильных не только экономит место, но и значительно улучшает производительность (меньше DOM-элементов, нет загрузки изображений)
- **SEO:** Несмотря на скрытие контента на мобильных, полная информация должна быть доступна в HTML для поисковых роботов (использовать CSS `display: none`, а не условный рендеринг, если SEO критично)
- **UX:** Клик по упрощённой карточке на мобильном должен вести на полную страницу статьи, где пользователь увидит изображение и полный текст
- **Ultra-Wide Support:** Современные пользователи с 21:9 и 32:9 мониторами получат оптимальное визуальное восприятие благодаря масштабированию до 6 колонок
- **Visual Balance:** Max-width контейнера предотвращает чрезмерное растяжение на огромных экранах (8K+), сохраняя читаемость и композицию
- **Performance на больших экранах:** При 6 колонках с 60+ карточками рекомендуется использовать виртуализацию (react-window или @tanstack/react-virtual)
- **Modern CSS Approach:** Рассмотрите использование CSS Container Queries для более гибкой адаптивности (если поддержка браузеров позволяет)
- **Testing on real devices:** Обязательно протестируйте на реальных ultra-wide мониторах (21:9, 32:9) — эмуляция в DevTools не всегда точна
- **Resize-анимации:** Layout animations от Framer Motion автоматически обрабатывают перемещение элементов — просто добавьте `layout` prop
- **Debounce критичен:** Без debounce на resize events производительность упадёт на 30-40% при активном изменении размера окна
- **GPU-acceleration:** Убедитесь, что анимации используют `transform` и `opacity` (не `left`, `top`, `width`, `height`) для hardware acceleration
- **Next.js SEO:** Используйте гибридную архитектуру — Server Components для данных + Client Components для интерактивности
- **SSR обязателен:** Google индексирует серверный рендер первым, клиентская гидратация — вторична
- **JSON-LD важен:** Для попадания в Rich Snippets Google требуется JSON-LD на каждой карточке
- **next/image критичен:** Автоматическая оптимизация в WebP/AVIF даёт прирост скорости ~40-60%
- **Lighthouse CI:** Интегрируйте в CI/CD для автоматической проверки Core Web Vitals при каждом PR

---

## ✅ Фактическая реализация

### Структура файлов

```
src/components/common/masonry/
├── MasonryGrid.tsx              # Server Component - обёртка для SEO
├── ArticleList.tsx              # Client Component - с анимациями
├── ArticleCard.tsx              # Карточка статьи с адаптивным поведением
├── LoadMoreButton.tsx           # Кнопка "Загрузить ещё"
├── types/
│   └── article.ts               # TypeScript типы
├── hooks/
│   ├── useResponsiveColumns.ts  # Hook для определения количества колонок
│   ├── useMediaQuery.ts         # Media query hook для адаптивности
│   └── usePreferReducedMotion.ts # Hook для accessibility
├── utils/
│   └── distribution.ts          # Алгоритм распределения по колонкам
├── data/
│   └── mock-articles.ts         # Mock-данные (32 статьи)
├── *.scss                       # Стили компонентов
└── index.ts                     # Публичный API модуля

src/utils/
├── breakpoints.ts               # Константы breakpoint'ов
└── seo.ts                       # SEO утилиты (Schema.org, форматирование)
```

### Ключевые особенности реализации

1. **Hydration Mismatch Prevention**: Используется `isMounted` state для предотвращения несоответствий между SSR и CSR
2. **Debounced Resize**: Resize events обрабатываются с задержкой 100ms для оптимизации производительности
3. **Shortest Column First**: Алгоритм распределения статей по колонкам для сбалансированной высоты
4. **Staggered Animations**: Framer Motion с задержкой 0.015s между карточками
5. **Mobile Adaptation**: На мобильных показываются только заголовок, подзаголовок и дата
6. **SEO Optimization**: JSON-LD разметка, семантический HTML, `<noscript>` fallback
7. **Theme Support**: Поддержка светлой и тёмной темы через CSS-переменные

### Использование

```tsx
import { MasonryGrid } from "@/components/common/masonry";
import { mockArticles } from "@/components/common/masonry/data";

export default function HomePage() {
  return <MasonryGrid articles={mockArticles} />;
}
```

### Интеграция в приложение

- **Главная страница**: `src/app/page.tsx` → `AppHomePage` → `MasonryGrid`
- **Стили темы**: `src/styles/themes/light/_masonry.scss` и `src/styles/themes/dark/_masonry.scss`
- **Breakpoints**: `src/utils/breakpoints.ts` с константами и функциями
- **SEO утилиты**: `src/utils/seo.ts` для форматирования дат и JSON-LD

---
