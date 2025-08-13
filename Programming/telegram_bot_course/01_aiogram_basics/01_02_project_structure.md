# Структура проекта на aiogram 3.x

## Оптимальная структура для коммерческих проектов
```
project_name/
├── src/                          # Основной код
│   ├── __init__.py
│   ├── handlers/                 # Обработчики сообщений
│   │   ├── __init__.py
│   │   ├── user_handlers.py      # Обработчики для пользователей
│   │   ├── admin_handlers.py     # Обработчики для админов
│   │   └── callback_handlers.py # Обработчики колбэков
│   ├── keyboards/               # Клавиатуры
│   │   ├── __init__.py
│   │   ├── user_keyboards.py
│   │   └── admin_keyboards.py
│   ├── middlewares/             # Middleware
│   │   ├── __init__.py
│   │   ├── auth_middleware.py
│   │   └── logging_middleware.py
│   ├── filters/                 # Фильтры
│   │   ├── __init__.py
│   │   ├── admin_filters.py
│   │   └── user_filters.py
│   ├── utils/                   # Утилиты
│   │   ├── __init__.py
│   │   ├── database.py
│   │   ├── config.py
│   │   └── helper_functions.py
│   ├── services/                # Внешние сервисы
│   │   ├── __init__.py
│   │   ├── google_sheets.py
│   │   └── crm_integration.py
│   └── models/                  # Модели данных
│       ├── __init__.py
│       ├── user.py
│       └── booking.py
├── data/                        # Данные и конфиги
│   ├── database.db
│   ├── config.py
│   └── migrations/
├── logs/                        # Логи
│   ├── bot.log
│   └── errors.log
├── tests/                       # Тесты
│   ├── __init__.py
│   ├── test_handlers.py
│   └── test_services.py
├── media/                       # Медиафайлы
│   ├── images/
│   └── documents/
├── backups/                     # Резервные копии
├── .env                         # Переменные окружения
├── requirements.txt             # Зависимости
├── bot.py                       # Точка входа
├── docker-compose.yml           # Для Docker
└── Dockerfile                   # Конфиг Docker
```

## Пояснение ключевых директорий

### src/
Основная директория с исходным кодом:
- **handlers/**: Разделение обработчиков по типам пользователей
- **keyboards/**: Генерация клавиатур
- **middlewares/**: Промежуточные обработчики
- **filters/**: Кастомные фильтры
- **utils/**: Вспомогательные функции
- **services/**: Интеграции с внешними API
- **models/**: Описание моделей данных

### data/
Хранение данных:
- **database.db**: SQLite база данных
- **config.py**: Конфигурация приложения
- **migrations/**: Миграции базы данных

### logs/
Система логирования:
- **bot.log**: Основные логи
- **errors.log**: Логи ошибок

### tests/
Тестирование:
- Разделение тестов по модулям
- Использование pytest

## Пример файла bot.py
```python
import asyncio
import logging
from aiogram import Bot, Dispatcher
from aiogram.fsm.storage.memory import MemoryStorage

from src.utils.config import load_config
from src.handlers import user_handlers, admin_handlers
from src.middlewares.auth_middleware import AuthMiddleware
from src.middlewares.logging_middleware import LoggingMiddleware

async def main():
    config = load_config()
    
    bot = Bot(token=config.bot_token)
    storage = MemoryStorage()
    dp = Dispatcher(storage=storage)
    
    # Подключаем middleware
    dp.update.middleware(AuthMiddleware())
    dp.update.middleware(LoggingMiddleware())
    
    # Подключаем роутеры
    dp.include_router(user_handlers.router)
    dp.include_router(admin_handlers.router)
    
    # Запуск бота
    await bot.delete_webhook(drop_pending_updates=True)
    await dp.start_polling(bot)

if __name__ == "__main__":
    logging.basicConfig(level=logging.INFO)
    try:
        asyncio.run(main())
    except KeyboardInterrupt:
        print("Бот остановлен")
```

## Принципы организации кода
1. **Разделение ответственности**:
   - Каждый модуль выполняет одну функцию
   - Четкая иерархия зависимостей

2. **Масштабируемость**:
   - Легкое добавление новых обработчиков
   - Простая интеграция новых сервисов

3. **Тестируемость**:
   - Изолированные компоненты
   - Возможность мокирования зависимостей

4. **Безопасность**:
   - Токены и секреты в .env
   - Ограничение доступа к критичным функциям

## Рекомендации по именованию
- **Файлы**: snake_case (user_handlers.py)
- **Классы**: CamelCase (UserService)
- **Функции**: snake_case (get_user_data)
- **Переменные**: snake_case (user_id)
- **Константы**: UPPER_SNAKE_CASE (BOT_TOKEN)

## Инструменты для поддержки структуры
1. **pylint** или **flake8** для проверки кода
2. **black** для форматирования
3. **isort** для сортировки импортов
4. **mypy** для статической типизации

## Типичные ошибки новичков
1. Весь код в одном файле
2. Смешивание бизнес-логики и обработчиков
3. Хранение токенов в коде
4. Отсутствие структуры для тестов
5. Неправильная организация импортов