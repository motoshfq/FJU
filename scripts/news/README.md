# Система Новин ФДУ

Повнофункціональна система управління новинами для сайту Федерації Дзюдо України.

## Структура файлів

```
scripts/news/
├── newsData.js      # Дані новин
├── newsManager.js   # Основний менеджер новин
├── newsUtils.js     # Утилітні функції
├── newsModal.css    # Стилі модального вікна
└── README.md        # Документація
```

## Основні компоненти

### 1. NewsData (newsData.js)
Містить масив об'єктів новин з наступними полями:
- `id` - унікальний ідентифікатор
- `title` - заголовок новини
- `excerpt` - короткий опис
- `content` - повний текст (HTML)
- `category` - категорія (achievements, competitions, events, announcements)
- `date` - дата публікації
- `author` - автор
- `image` - шлях до зображення
- `featured` - чи є новина головною
- `tags` - масив тегів
- `views` - кількість переглядів
- `likes` - кількість лайків

### 2. NewsManager (newsManager.js)
Основний клас для управління новинами:

#### Методи:
- `filterByCategory(category)` - фільтрація за категорією
- `search(query)` - пошук новин
- `goToPage(page)` - пагінація
- `renderNews()` - відображення новин
- `openNewsModal(newsId)` - відкриття модального вікна
- `renderRecentNews()` - відображення останніх новин

#### Використання:
```javascript
// Ініціалізація автоматична при завантаженні DOM
const newsManager = window.newsManager;

// Фільтрація
newsManager.filterByCategory('achievements');

// Пошук
newsManager.search('дзюдо');

// Отримання новини за ID
const news = newsManager.getNewsById(1);
```

### 3. NewsUtils (newsUtils.js)
Утилітні функції:

#### Форматування дат:
```javascript
NewsUtils.formatDate('2024-12-15'); // "15 грудня 2024"
NewsUtils.formatDate('2024-12-15', 'short'); // "15 гру"
NewsUtils.timeAgo('2024-12-15'); // "3 дні тому"
```

#### Робота з категоріями:
```javascript
const categoryInfo = NewsUtils.getCategoryInfo('achievements');
// { name: 'Досягнення', icon: '🏆', color: '#28a745' }
```

#### Локальне сховище:
```javascript
NewsUtils.saveToLocalStorage('key', data);
const data = NewsUtils.getFromLocalStorage('key', defaultValue);
```

#### Аналітика:
```javascript
NewsUtils.trackNewsView(newsId);
NewsUtils.trackNewsLike(newsId);
const isLiked = NewsUtils.isNewsLiked(newsId);
```

## Інтеграція з HTML

### Підключення файлів:
```html
<link rel="stylesheet" href="scripts/news/newsModal.css">
<script src="scripts/news/newsData.js"></script>
<script src="scripts/news/newsManager.js"></script>
```

### Необхідні HTML елементи:
```html
<!-- Фільтри -->
<div class="news-filter-tabs">
    <button class="news-filter-tab active" data-category="all">Усі новини</button>
    <button class="news-filter-tab" data-category="achievements">Досягнення</button>
    <!-- ... інші категорії -->
</div>

<!-- Контейнер новин -->
<div class="main-articles">
    <!-- Новини будуть згенеровані автоматично -->
</div>

<!-- Пагінація -->
<div class="pagination">
    <!-- Пагінація буде згенерована автоматично -->
</div>

<!-- Останні новини (сайдбар) -->
<div class="recent-articles">
    <!-- Останні новини будуть згенеровані автоматично -->
</div>
```

## Категорії новин

1. **achievements** - Досягнення (🏆)
2. **competitions** - Змагання (🥋)
3. **events** - Події (📅)
4. **announcements** - Анонси (📢)

## Функціональність

### ✅ Реалізовано:
- Фільтрація новин за категоріями
- Пагінація
- Модальне вікно для повного перегляду
- Відображення останніх новин
- Підрахунок переглядів
- Адаптивний дизайн
- Анімації та переходи

### 🔄 Можливі розширення:
- Пошук по тексту
- Сортування за датою/популярністю
- Коментарі до новин
- Соціальні мережі (поділитися)
- RSS фід
- Адмін-панель для додавання новин

## Додавання нових новин

Для додавання нової новини, додайте об'єкт до масиву `newsData`:

```javascript
{
    id: 7, // унікальний ID
    title: "Заголовок новини",
    excerpt: "Короткий опис...",
    content: `<p>Повний текст новини...</p>`,
    category: "achievements",
    date: "2024-12-20",
    author: "Автор",
    image: "assets/news/image.jpg",
    featured: false,
    tags: ["тег1", "тег2"],
    views: 0,
    likes: 0
}
```

## Стилізація

Основні CSS класи для кастомізації:
- `.news-filter-tab` - кнопки фільтрів
- `.featured-article` - головна новина
- `.regular-article` - звичайні новини
- `.news-modal` - модальне вікно
- `.article-category` - категорія новини
- `.pagination` - пагінація

## Підтримка браузерів

- Chrome 60+
- Firefox 55+
- Safari 12+
- Edge 79+

## Продуктивність

- Ледача загрузка зображень
- Дебаунс для пошуку
- Віртуалізація для великих списків (при потребі)
- Кешування в localStorage 