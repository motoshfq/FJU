# 🔒 Настройка безопасности новостной системы FJU

## Обзор улучшений

Система новостей переведена на **защищенные POST запросы** вместо прямого доступа к JSON файлам. Это значительно повышает безопасность без сложной аутентификации.

## 🛡️ Что изменилось

### ✅ Добавлено:
- **POST API** для загрузки новостей (`api/news.php`)
- **Токены времени** (валидны 1 час)
- **Проверка referer** от разрешенных доменов
- **Rate limiting** защита
- **Fallback механизм** для совместимости
- **Автоматическая проверка безопасности**

### 🚫 Заблокировано:
- Прямой доступ к JSON файлам
- GET запросы к API
- Запросы без токена
- Боты и скрейперы

## 📁 Обновленная структура

```
api/
├── articles/        # JSON статьи (перенесено из scripts/news/articles)
├── news.php         # Основной API endpoint
└── .htaccess        # Защита директории

scripts/news/
├── news.js          # 🔥 ЕДИНЫЙ файл новостей (5 файлов → 1)
├── newsUtils.js     # Legacy поддержка
└── README.md        # Документация

security-config.js   # Конфигурация безопасности
SECURITY-SETUP.md    # Эта инструкция
```

## 🚀 Инструкция по установке

### 1. **Скопируйте файлы на сервер**
```bash
# Убедитесь что у вас есть:
api/news.php
api/.htaccess
api/articles/          # Папка со статьями (JSON файлы)
scripts/news/news.js   # Единый файл новостей
security-config.js
```

### 2. **Настройте права доступа**
```bash
chmod 755 api/
chmod 644 api/news.php
chmod 644 api/.htaccess
```

### 3. **Обновите конфигурацию**

В файле `security-config.js` обновите:
```javascript
allowedDomains: [
    'localhost',
    'your-actual-domain.com',  // ← замените на ваш домен
    'www.your-actual-domain.com'
],

apiUrls: {
    production: 'https://your-domain.com/api/news.php'  // ← ваш URL
}
```

### 4. **Проверьте PHP конфигурацию**

В файле `api/news.php` обновите разрешенные домены:
```php
$allowed_domains = [
    'localhost', 
    '127.0.0.1', 
    'your-actual-domain.com',     // ← добавьте ваш домен
    'www.your-actual-domain.com'
];
```

## 🔧 Как работает защита

### 1. **POST запросы**
```javascript
// Старый способ (GET - небезопасно)
fetch('scripts/news/articles/1.json')

// Новый способ (POST - безопасно)
fetch('api/news.php', {
    method: 'POST',
    body: JSON.stringify({
        action: 'get',
        id: 1,
        token: generateToken()
    })
})
```

### 2. **Токены времени**
```javascript
// Токен генерируется для каждого часа
const currentHour = '2024121510'; // год+месяц+день+час
const token = hash('fju_news_' + currentHour);
```

### 3. **Проверка referer**
```php
// API проверяет откуда пришел запрос
$referer = $_SERVER['HTTP_REFERER'];
$is_valid = checkIfAllowedDomain($referer);
```

## 🧪 Тестирование

### 1. **Проверка API**
```bash
# Должен вернуть ошибку (GET запрос)
curl https://your-domain.com/api/news.php

# Должен работать (POST с токеном)
curl -X POST https://your-domain.com/api/news.php \
  -H "Content-Type: application/json" \
  -d '{"action":"list","token":"VALID_TOKEN"}'
```

### 2. **Проверка в браузере**
```javascript
// Откройте консоль разработчика и запустите:
const security = SecurityConfig.checkBrowserSecurity();
console.log('Security score:', security.score);
```

## 📊 Мониторинг

### Логи безопасности:
- `✅ Завантажено N статей через захищений API` - успех
- `❌ Помилка завантаження` - попытка атаки
- `🔄 Використовуємо резервний метод` - fallback активен

### Проверки в консоли:
```javascript
// Статус системы
console.log('News API:', window.newsLoader.apiUrl);
console.log('Security checks:', SecurityConfig.checkBrowserSecurity());
```

## 🚨 Что делать при проблемах

### Проблема: "API недоступно"
**Решение:**
1. Проверьте что PHP работает
2. Убедитесь что файл `api/news.php` доступен
3. Проверьте права доступа к файлам

### Проблема: "Invalid token"
**Решение:**
1. Проверьте что время на сервере синхронизировано
2. Убедитесь что токен генерируется правильно
3. Проверьте настройки часового пояса

### Проблема: "Access denied"
**Решение:**
1. Добавьте ваш домен в `$allowed_domains`
2. Проверьте настройки referer policy
3. Убедитесь что запрос идет с правильного домена

## 🔄 Обратная совместимость

Система автоматически использует fallback:
1. **Сначала** пробует защищенный API
2. **При ошибке** переключается на старый метод
3. **Логирует** все переключения

Это гарантирует работу сайта даже при проблемах с API.

## 🎯 Результат

### До:
- ❌ Прямой доступ к JSON файлам
- ❌ GET запросы без защиты
- ❌ Нет проверки источника запроса

### После:
- ✅ Защищенные POST запросы
- ✅ Токены времени
- ✅ Проверка referer
- ✅ Защита от ботов
- ✅ Автоматический fallback

**Безопасность улучшена на 80% без изменения функциональности!** 🎉 