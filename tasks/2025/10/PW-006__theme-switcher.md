# PW-006: Theme Switcher

## 📋 Информация о задаче

- **ID**: PW-006
- **Статус**: ✅ РЕАЛИЗОВАНО
- **Создано**: 2025-10-26
- **Завершено**: 2025-10-28
- **Приоритет**: High ⚡
- **Компонент**: ⚛️ Frontend
- **Теги**: #frontend #animations #theme #ui-components

## Обзор задачи

Реализовать переключатель темы (светлая/тёмная) с анимированной SVG-иконкой солнышко-луна, поддержкой сохранения выбора пользователя и интеграцией с системой тем приложения.

**Статус:** ✅ РЕАЛИЗОВАНО  
**Приоритет:** Высокий  
**Реализация:** `src/components/common/theme-toggle/`

---

## Технический стек

- **Framework:** Next.js 14+ (App Router)
- **Анимации:** CSS transitions + SVG morphing
- **Стилизация:** SCSS с CSS-переменными
- **TypeScript:** Обязательно
- **Context API:** React Context для управления состоянием темы

---

## Требования к функциональности

### 1. Структура компонентов (РЕАЛИЗОВАНО)

```
src/components/common/theme-toggle/
├── ThemeToggle.tsx               # Основной компонент переключателя ✅
├── SunMoonIcon.tsx               # SVG иконка с анимацией ✅
├── ThemeToggle.scss              # Стили переключателя ✅
├── SunMoonIcon.scss              # Стили иконки с анимациями ✅
├── index.ts                      # Публичный API модуля ✅
└── README.md                     # Документация компонента ✅

src/contexts/
└── ThemeContext.tsx              # React Context для управления темой ✅

src/types/
└── index.ts                      # TypeScript типы (Theme) ✅
```

### 2. Типы данных

**Theme Type:**

```typescript
export type Theme = "light" | "dark";

export interface ThemeContextType {
  theme: Theme;
  toggleTheme: () => void;
  setTheme: (theme: Theme) => void;
}
```

### 3. Функциональность

#### ✅ Основные возможности

- **Переключение тем**: Плавный переход между светлой и тёмной темой
- **Сохранение выбора**: Автоматическое сохранение предпочтений в localStorage
- **Продвинутая анимация**: Элегантная трансформация солнца в луну с elastic эффектами
- **SVG морфинг**: Использование SVG mask для плавной трансформации
- **Accessibility**: ARIA-метки и поддержка `prefers-reduced-motion`
- **Адаптивный**: Корректно отображается на всех размерах экрана
- **SSR-friendly**: Предотвращение hydration mismatch

#### ✅ Технические особенности

- **Hydration Mismatch Prevention**: Используется `isMounted` state для предотвращения несоответствий между SSR и CSR
- **Script в head**: Инициализация темы до гидратации для предотвращения мигания
- **CSS Custom Properties**: Использование CSS-переменных для динамического изменения цветов
- **SVG Mask Animation**: Продвинутая анимация с использованием SVG mask для морфинга иконки
- **Elastic Easing**: Использование cubic-bezier функций для естественных анимаций

### 4. Анимации

#### ✅ SVG Анимация солнышко-луна

**Основано на:** [Building a theme switch component - web.dev](https://web.dev/articles/building/a-theme-switch-component)

**Принцип работы:**

- Используется SVG mask для создания эффекта "вырезания" луны из солнца
- Солнце масштабируется и поворачивается
- Лучи солнца исчезают с поворотом
- Маска луны сдвигается для создания эффекта фазы

**SVG код иконки:**

```tsx
<svg
  className="sun-moon-icon"
  aria-hidden="true"
  width={size}
  height={size}
  viewBox="0 0 24 24"
>
  <mask className="sun-moon-icon__moon-mask" id={maskId}>
    <rect x="0" y="0" width="100%" height="100%" fill="white" />
    <circle
      className="sun-moon-icon__moon-mask-circle"
      cx="24"
      cy="12"
      r="6"
      fill="black"
    />
  </mask>
  <circle
    className="sun-moon-icon__sun"
    cx="12"
    cy="12"
    r="6"
    mask={`url(#${maskId})`}
    fill="currentColor"
  />
  <g className="sun-moon-icon__sun-beams" stroke="currentColor">
    <line x1="12" y1="1" x2="12" y2="3" />
    <line x1="12" y1="21" x2="12" y2="23" />
    <line x1="4.22" y1="4.22" x2="5.64" y2="5.64" />
    <line x1="18.36" y1="18.36" x2="19.78" y2="19.78" />
    <line x1="1" y1="12" x2="3" y2="12" />
    <line x1="21" y1="12" x2="23" y2="12" />
    <line x1="4.22" y1="19.78" x2="5.64" y2="18.36" />
    <line x1="18.36" y1="5.64" x2="19.78" y2="4.22" />
  </g>
</svg>
```

**CSS анимации:**

```scss
// Базовые стили
.sun-moon-icon {
  --ease-3: cubic-bezier(0.25, 0, 0.3, 1);
  --ease-out-5: cubic-bezier(0.23, 1, 0.32, 1);
  --ease-elastic-3: cubic-bezier(0.5, 1.25, 0.75, 1.25);
  --ease-elastic-4: cubic-bezier(0.5, 1.5, 0.75, 1.25);

  display: block;
  transform-origin: center;

  &__sun,
  &__moon-mask-circle {
    transform-origin: center;
  }

  &__sun {
    fill: currentColor;
  }

  &__sun-beams {
    stroke: currentColor;
    stroke-width: 2px;
    stroke-linecap: round;
    transform-origin: center;
  }
}

// Состояние тёмной темы
.sun-moon-icon--dark {
  .sun-moon-icon__sun {
    transform: scale(1.75);
  }

  .sun-moon-icon__sun-beams {
    opacity: 0;
    transform: rotateZ(-25deg);
  }

  .sun-moon-icon__moon-mask-circle {
    transform: translateX(-7px);
  }
}

// Анимации (только если нет предпочтения к сниженному движению)
@media (prefers-reduced-motion: no-preference) {
  .sun-moon-icon:not(.sun-moon-icon--no-animation) {
    .sun-moon-icon__sun {
      transition: transform 0.5s var(--ease-elastic-3);
    }

    .sun-moon-icon__sun-beams {
      transition: transform 0.5s var(--ease-elastic-4), opacity 0.5s var(--ease-3);
    }

    .sun-moon-icon__moon-mask-circle {
      transition-property: transform;
      transition-duration: 0.25s;
      transition-timing-function: var(--ease-out-5);
      transition-delay: 0s;
    }
  }

  // Переопределение для тёмной темы
  .sun-moon-icon--dark:not(.sun-moon-icon--no-animation) {
    .sun-moon-icon__sun {
      transition-timing-function: var(--ease-3);
      transition-duration: 0.25s;
    }

    .sun-moon-icon__sun-beams {
      transition-duration: 0.15s;
    }

    .sun-moon-icon__moon-mask-circle {
      transition-property: transform;
      transition-duration: 0.5s;
      transition-timing-function: var(--ease-out-5);
      transition-delay: 0.05s;
    }
  }
}
```

**Параметры анимации:**

- **Длительность**: 0.5s для основных элементов, 0.25s для маски
- **Easing**: Elastic cubic-bezier для естественного движения
- **Задержки**: Разные задержки для создания волнового эффекта
- **SVG Mask**: Уникальный ID для избежания конфликтов при нескольких иконках

### 5. Accessibility

#### ✅ ARIA поддержка

- `aria-label`: Описательный текст для скрин-ридеров
- `aria-pressed`: Индикация активного состояния
- `title`: Подсказка при наведении
- `aria-hidden="true"` для декоративной SVG иконки

#### ✅ Поддержка prefers-reduced-motion

- Автоматическое отключение анимаций при системном предпочтении
- Класс `.sun-moon-icon--no-animation` для принудительного отключения

### 6. Интеграция

#### ✅ ThemeContext

```tsx
// В root layout
import { ThemeProvider } from "@/contexts/ThemeContext";

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        <ThemeProvider defaultTheme="light">{children}</ThemeProvider>
      </body>
    </html>
  );
}
```

#### ✅ Использование в компонентах

```tsx
import { useTheme } from "@/contexts/ThemeContext";

function MyComponent() {
  const { theme, toggleTheme, setTheme } = useTheme();

  return (
    <div>
      <p>Текущая тема: {theme}</p>
      <button onClick={toggleTheme}>Переключить</button>
    </div>
  );
}
```

#### ✅ Интеграция в AppBar

```tsx
import ThemeToggle from "@/components/common/theme-toggle";

const AppBarActions = () => {
  return (
    <div className="action-buttons">
      <ThemeToggle />
      {/* другие кнопки */}
    </div>
  );
};
```

---

## Критерии приёмки

### ✅ Функциональность

- ✅ Переключение между светлой и тёмной темой работает корректно
- ✅ Выбор темы сохраняется в localStorage и восстанавливается при перезагрузке
- ✅ Анимация иконки плавная и естественная (elastic easing)
- ✅ SVG морфинг работает без артефактов
- ✅ Hydration mismatch отсутствует

### ✅ Accessibility

- ✅ ARIA-атрибуты корректно настроены
- ✅ Поддержка `prefers-reduced-motion` работает
- ✅ Keyboard navigation поддерживается
- ✅ Screen readers корректно объявляют состояние

### ✅ Performance

- ✅ Анимации используют GPU acceleration (transform, opacity)
- ✅ Нет layout thrashing при переключении темы
- ✅ SSR работает без мигания контента
- ✅ Script в head предотвращает FOUC (Flash of Unstyled Content)

### ✅ Интеграция

- ✅ ThemeContext интегрирован в root layout
- ✅ Компонент используется в AppBar
- ✅ CSS-переменные корректно обновляются при смене темы
- ✅ Все компоненты приложения реагируют на смену темы

---

## ✅ Фактическая реализация

### Структура файлов

```
src/components/common/theme-toggle/
├── ThemeToggle.tsx               # Основной компонент переключателя
├── SunMoonIcon.tsx               # SVG иконка с анимацией
├── ThemeToggle.scss              # Стили переключателя
├── SunMoonIcon.scss              # Стили иконки с анимациями
├── index.ts                      # Публичный API модуля
└── README.md                     # Документация компонента

src/contexts/
└── ThemeContext.tsx              # React Context для управления темой

src/types/
└── index.ts                      # TypeScript типы (Theme)
```

### Ключевые особенности реализации

1. **Hydration Mismatch Prevention**: Используется `isMounted` state и script в head для предотвращения несоответствий между SSR и CSR
2. **SVG Mask Animation**: Продвинутая анимация с использованием SVG mask для создания эффекта морфинга солнца в луну
3. **Elastic Easing**: Использование cubic-bezier функций для естественных анимаций
4. **Theme Persistence**: Автоматическое сохранение выбора пользователя в localStorage
5. **Accessibility**: Полная поддержка ARIA-атрибутов и `prefers-reduced-motion`
6. **CSS Custom Properties**: Динамическое изменение цветов через CSS-переменные
7. **SSR Optimization**: Script в head предотвращает FOUC при загрузке страницы

### Использование

```tsx
import ThemeToggle from "@/components/common/theme-toggle";

function Header() {
  return (
    <header>
      <ThemeToggle />
    </header>
  );
}
```

### Интеграция в приложение

- **Root Layout**: `src/app/layout.tsx` с script в head для инициализации темы
- **Providers**: `src/components/providers/Providers.tsx` с ThemeProvider
- **AppBar**: `src/components/app-layout/app-bar/app-bar-right/app-bar-actions/AppBarActions.tsx`
- **Context**: `src/contexts/ThemeContext.tsx` для управления состоянием темы
- **Типы**: `src/types/index.ts` с определением типа Theme

---

## 📝 Примечания

- **SSR Optimization**: Script в head критически важен для предотвращения мигания контента
- **SVG Mask**: Продвинутая техника анимации, основанная на [GUI Challenges](https://web.dev/articles/building/a-theme-switch-component)
- **Elastic Easing**: Создаёт естественное ощущение движения
- **Hydration Safety**: `isMounted` state предотвращает несоответствия между сервером и клиентом
- **Accessibility First**: Полная поддержка screen readers и keyboard navigation
- **Performance**: GPU-ускоренные анимации для плавности на всех устройствах

---

## 🔗 Ссылки и ресурсы

- **[Building a theme switch component - web.dev](https://web.dev/articles/building/a-theme-switch-component)** - Исходный урок с детальным объяснением SVG mask анимации
- **[GUI Challenges](https://github.com/argyleink/gui-challenges)** - Коллекция продвинутых UI компонентов
- **[SVG Mask Documentation - MDN](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/mask)** - Официальная документация по SVG mask
- **[CSS Custom Properties - MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)** - Документация по CSS-переменным
- **[prefers-reduced-motion - MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion)** - Accessibility для анимаций
