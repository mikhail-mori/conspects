# Установка и настройка окружения для разработки

## Требования к системе
- Python 3.8+
- Операционная система: Windows/macOS/Linux
- 2 ГБ свободного места на диске
- Доступ к интернету

## Шаг 1: Установка Python
```bash
# Проверка установленной версии
python --version

# Установка через пакетный менеджер
# Ubuntu/Debian:
sudo apt update
sudo apt install python3.8 python3-pip

# macOS (через Homebrew):
brew install python3

# Windows: Скачайте установщик с python.org
```
## Шаг 2: Создание виртуального окружения
```bash
# Создание директории проекта
mkdir telegram_bot
cd telegram_bot

# Создание виртуального окружения
python -m venv venv

# Активация
# Linux/macOS:
source venv/bin/activate

# Windows:
venv\Scripts\activate
```
## Шаг 3: Установка aiogram 3.x
```bash
# Установка последней версии
pip install aiogram==3.0.0b7

# Установка дополнительных зависимостей
pip install aiosqlite
pip install python-dotenv
pip install pydantic
```
## Шаг 4: Настройка IDE

Рекомендуемые инструменты:

- **VS Code** с расширениями:
    
    - Python
    - Pylance
    - Docker
    - GitLens
- **PyCharm Professional** (для продвинутой отладки)
    

## Шаг 5: Настройка Git
```bash
# Инициализация репозитория
git init

# Настройка пользователя
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Создание .gitignore
echo "venv/" >> .gitignore
echo "__pycache__/" >> .gitignore
echo ".env" >> .gitignore
```
## Шаг 6: Структура проекта
```
telegram_bot/
├── venv/
├── src/
│   ├── __init__.py
│   ├── handlers/
│   ├── keyboards/
│   ├── middlewares/
│   └── utils/
├── data/
│   ├── database.db
│   └── config.py
├── logs/
├── tests/
├── .env
├── requirements.txt
└── bot.py
```
## Шаг 7: Создание файла .env
```ini
# .env
BOT_TOKEN=ваш_токен_здесь
ADMIN_ID=123456789
DB_URL=sqlite+aiosqlite:///data/database.db
LOG_LEVEL=INFO
```
## Шаг 8: Проверка установки

Создайте файл `test.py`:
```python
import asyncio
from aiogram import Bot, Dispatcher

async def main():
    bot = Bot(token="ВАШ_ТОКЕН")
    dispatcher = Dispatcher()
    print("Бот успешно запущен!")
    await bot.session.close()

if __name__ == "__main__":
    asyncio.run(main())
```
Запустите тест:
```bash
python test.py
```
## Возможные проблемы и решения

1. **Ошибка импорта aiogram**:
    
    ```bash
    pip install --upgrade pip
    pip install --force-reinstall aiogram
    ```
2. **Проблемы с виртуальным окружением**:
    
    - Удалите и пересоздайте venv
    - Проверьте активацию окружения
3. **Ошибки токена**:
    
    - Получите токен у @BotFather
    - Проверьте правильность копирования

## Рекомендуемые расширения для VS Code

1. Python - Microsoft
2. Pylance - Microsoft
3. Python Test Explorer - LittleFoxTeam
4. Docker - Microsoft
5. GitLens - GitKraken