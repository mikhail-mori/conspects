# Обработчики команд, сообщений и колбэков

## Основные типы обработчиков в aiogram 3.x

### 1. Обработчики команд
```python
from aiogram.filters import Command
from aiogram.types import Message

@dp.message(Command("start"))
async def cmd_start(message: Message):
    await message.answer("Привет! Я бот для записи на занятия.")

@dp.message(Command("help"))
async def cmd_help(message: Message):
    help_text = (
        "/start - Начать работу\n"
        "/help - Показать помощь\n"
        "/book - Записаться на занятие"
    )
    await message.answer(help_text)

# Команда с аргументами
@dp.message(Command("set", magic=F.args))
async def cmd_set(message: Message):
    args = message.text.split()
    if len(args) < 2:
        await message.answer("Использование: /set <значение>")
        return
    
    value = args[1]
    await message.answer(f"Установлено значение: {value}")
```

### 2. Обработчики текстовых сообщений
```python
from aiogram import F

# Обработчик всех текстовых сообщений
@dp.message(F.text)
async def text_handler(message: Message):
    await message.answer(f"Вы написали: {message.text}")

# Обработчик конкретного текста
@dp.message(F.text == "Привет")
async def hello_handler(message: Message):
    await message.answer("И вам привет!")

# Обработчик с регулярным выражением
@dp.message(F.text.regexp(r'^\d+$'))
async def number_handler(message: Message):
    await message.answer(f"Вы ввели число: {message.text}")
```

### 3. Обработчики колбэков
```python
from aiogram.types import CallbackQuery

# Обработчик всех колбэков
@dp.callback_query()
async def callback_handler(callback: CallbackQuery):
    await callback.answer("Колбэк получен")
    await callback.message.answer(f"Вы нажали кнопку: {callback.data}")

# Обработчик конкретного колбэка
@dp.callback_query(F.data == "show_menu")
async def show_menu_callback(callback: CallbackQuery):
    await callback.message.edit_text("Меню:", reply_markup=menu_keyboard())

# Обработчик с префиксом
@dp.callback_query(F.data.startswith("book_"))
async def book_callback(callback: CallbackQuery):
    service_id = callback.data.split("_")[1]
    await callback.answer(f"Выбрана услуга: {service_id}")
```

## Продвинутые техники работы с обработчиками

### 1. Комбинированные фильтры
```python
from aiogram.filters import CommandStart, CommandObject

# Команда /start с аргументами
@dp.message(CommandStart(deep_link=True))
async def cmd_start_deep(message: Message, command: CommandObject):
    ref = command.args
    await message.answer(f"Вы пришли по ссылке с параметром: {ref}")

# Только для админов
@dp.message(Command("admin"), F.from_user.id.in_(ADMIN_IDS))
async def admin_command(message: Message):
    await message.answer("Панель администратора")

# Только в личных сообщениях
@dp.message(F.chat.type == "private")
async def private_handler(message: Message):
    await message.answer("Это личное сообщение")
```

### 2. Группировка обработчиков в роутеры
```python
from aiogram import Router

user_router = Router()
admin_router = Router()

# Обработчики для пользователей
@user_router.message(Command("start"))
async def user_start(message: Message):
    await message.answer("Пользовательское меню")

# Обработчики для админов
@admin_router.message(Command("admin"))
async def admin_panel(message: Message):
    await message.answer("Админ-панель")

# Подключение роутеров
dp.include_router(user_router)
dp.include_router(admin_router)
```

### 3. Асинхронные обработчики с состоянием
```python
from aiogram.fsm.context import FSMContext
from aiogram.fsm.state import State, StatesGroup

class BookingState(StatesGroup):
    choosing_service = State()
    entering_date = State()

@dp.message(Command("book"))
async def book_start(message: Message, state: FSMContext):
    await message.answer("Выберите услугу:", reply_markup=services_keyboard())
    await state.set_state(BookingState.choosing_service)

@dp.callback_query(BookingState.choosing_service)
async def service_chosen(callback: CallbackQuery, state: FSMContext):
    service = callback.data
    await state.update_data(service=service)
    await callback.message.answer("Введите дату:")
    await state.set_state(BookingState.entering_date)

@dp.message(BookingState.entering_date)
async def date_entered(message: Message, state: FSMContext):
    data = await state.get_data()
    service = data["service"]
    date = message.text
    await message.answer(f"Вы записаны на {service} {date}")
    await state.clear()
```

### 4. Middleware для обработчиков
```python
from aiogram.types import Message
from aiogram.types import TelegramObject
from aiogram.types.base import Middleware

class AuthMiddleware(Middleware):
    async def __call__(
        self,
        handler: Callable[[TelegramObject, Dict[str, Any]], Awaitable[Any]],
        event: TelegramObject,
        data: Dict[str, Any]
    ) -> Any:
        # Проверка авторизации
        if event.from_user.id not in AUTHORIZED_USERS:
            await event.answer("Доступ запрещен")
            return
        
        return await handler(event, data)

# Регистрация middleware
dp.update.middleware(AuthMiddleware())
```

## Практические примеры

### 1. Многоуровневое меню
```python
@dp.message(Command("menu"))
async def show_main_menu(message: Message):
    keyboard = InlineKeyboardBuilder()
    keyboard.button(text="Услуги", callback_data="services")
    keyboard.button(text="О нас", callback_data="about")
    keyboard.button(text="Контакты", callback_data="contacts")
    await message.answer("Главное меню:", reply_markup=keyboard.as_markup())

@dp.callback_query(F.data == "services")
async def services_menu(callback: CallbackQuery):
    keyboard = InlineKeyboardBuilder()
    keyboard.button(text="Консультация", callback_data="book_consultation")
    keyboard.button(text="Мастер-класс", callback_data="book_workshop")
    keyboard.button(text="⬅️ Назад", callback_data="main_menu")
    await callback.message.edit_text("Наши услуги:", reply_markup=keyboard.as_markup())

@dp.callback_query(F.data == "main_menu")
async def back_to_main(callback: CallbackQuery):
    await show_main_menu(callback.message)
```

### 2. Обработка ошибок в обработчиках
```python
from aiogram.exceptions import TelegramAPIError

@dp.message()
async def safe_handler(message: Message):
    try:
        # Рискованная операция
        await message.answer("Обработка...")
    except TelegramAPIError as e:
        logging.error(f"Ошибка Telegram API: {e}")
        await message.answer("Произошла ошибка, попробуйте позже")
    except Exception as e:
        logging.error(f"Неизвестная ошибка: {e}")
        await message.answer("Что-то пошло не так")
```

### 3. Динамические обработчики
```python
# Регистрация обработчиков на лету
async def register_dynamic_handlers():
    # Получаем список услуг из БД
    services = await get_services_from_db()
    
    for service in services:
        @dp.callback_query(F.data == f"book_{service['id']}")
        async def book_service(callback: CallbackQuery, service=service):
            await callback.answer(f"Запись на {service['name']}")
            await process_booking(callback.from_user.id, service['id'])
```

## Лучшие практики
1. **Разделяйте обработчики** по типам и функциям
2. **Используйте роутеры** для организации кода
3. **Применяйте middleware** для кросс-функциональности
4. **Обрабатывайте ошибки** в каждом обработчике
5. **Используйте FSM** для многошаговых процессов
6. **Комбинируйте фильтры** для точной маршрутизации
7. **Логируйте действия** для отладки
8. **Документируйте обработчики** для поддержки кода