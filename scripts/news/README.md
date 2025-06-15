# 📰 Система новостей FJU - Минимальная реализация

## 📁 Структура (после реорганизации)

```
api/
├── articles/           # JSON статьи (перенесено из scripts/news/articles)
│   ├── 1.json         # Статья 1  
│   ├── 2.json         # Статья 2
│   └── ...
├── news.php           # API endpoint
└── .htaccess          # Защита

scripts/news/
├── news.js            # 🔥 ЕДИНЫЙ файл - вся логика
├── newsUtils.js       # Legacy поддержка (при необходимости)
└── README.md          # Эта документация

security-config.js     # Конфигурация безопасности
```

## 🎯 Что изменилось

### ✅ МИНИМИЗАЦИЯ:
- **5 файлов → 1 файл** (`news.js`)
- Все классы в одном месте
- Автоматическое определение типа страницы
- Единая инициализация

### 🚀 ОПТИМИЗАЦИЯ:
- Статьи в `api/articles/` вместо `scripts/news/articles/`
- Меньше HTTP запросов
- Упрощенная структура

## 📄 news.js - Единый файл

Содержит все в одном:

```javascript
// Классы:
NewsLoader      # Загрузка через API + fallback
NewsManager     # Управление списком новостей  
NewsArticle     # Управление отдельной статьей

// Автоинициализация:
DOMContentLoaded → определяет тип страницы → запускает нужный класс
```

## 🔧 Использование

### Для страницы новостей (`news.html`):
```html
<script src="scripts/news/news.js"></script>
```
Автоматически создает `window.newsManager`

### Для страницы статьи (`news-article.html`):
```html  
<script src="scripts/news/news.js"></script>
```
Автоматически создает `window.newsArticle`

### Для главной страницы (`index.html`):
```html
<script src="scripts/news/news.js"></script>
```
Экспортирует `window.newsLoader` для использования в `home.js`

## 🔄 Обратная совместимость

- `window.newsLoader` - доступен везде
- `window.newsManager` - на странице новостей
- `window.newsArticle` - на странице статьи
- `NewsUtils` - сохранен для legacy кода

## 📊 Производительность

**До:**
- 5 файлов JS (newsLoader, newsManager, newsArticle, newsUtils, newsModal.css)
- 5 HTTP запросов
- ~15KB общий размер

**После:**
- 1 файл JS (news.js)
- 1 HTTP запрос  
- ~8KB размер

**Улучшение: 50% меньше размер, 80% меньше запросов!** 🚀

## 🔒 Безопасность

Сохранены все улучшения безопасности:
- POST API запросы
- Токены времени
- Rate limiting
- Fallback механизм

## 🛠️ Разработка

Для добавления новых функций - редактируйте `news.js`:
- Все в одном месте
- Четкое разделение на классы
- Комментарии для навигации 