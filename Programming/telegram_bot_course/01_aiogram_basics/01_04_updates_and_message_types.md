# Работа с обновлениями и типами сообщений

## Что такое обновления (Updates) в Telegram?
Обновления - это события, происходящие в чате с ботом:
- Новые сообщения
- Редактирование сообщений
- Нажатия на кнопки
- Изменения в чате
- И другие события

## Основные типы обновлений в aiogram
```python
from aiogram import types
from aiogram.enums import UpdateType

# Типы обновлений
UpdateType.MESSAGE          # Новое сообщение
UpdateType.EDITED_MESSAGE   # Отредактированное сообщение
UpdateType.CHANNEL_POST     # Пост в канале
UpdateType.EDITED_CHANNEL_POST # Отредактированный пост
UpdateType.INLINE_QUERY     # Инлайн-запрос
UpdateType.CHOSEN_INLINE_RESULT # Выбранный инлайн-результат
UpdateType.CALLBACK_QUERY   # Нажатие на инлайн-кнопку
UpdateType.SHIPPING_QUERY   # Запрос доставки
UpdateType.PRE_CHECKOUT_QUERY # Предпроверочный запрос
UpdateType.POLL             # Опрос
UpdateType.POLL_ANSWER      # Ответ на опрос
UpdateType.MY_CHAT_MEMBER   # Изменение статуса бота
UpdateType.CHAT_MEMBER      # Изменение участника чата
UpdateType.CHAT_JOIN_REQUEST # Запрос на вступление в чат
```

## Работа с сообщениями

### Основные типы сообщений
```python
@dp.message()
async def handle_message(message: types.Message):
    # Проверка типа сообщения
    if message.content_type == types.ContentType.TEXT:
        await message.answer("Это текстовое сообщение")
    
    elif message.content_type == types.ContentType.PHOTO:
        await message.answer("Это фото")
    
    elif message.content_type == types.ContentType.VOICE:
        await message.answer("Это голосовое сообщение")
    
    # И другие типы...
```

### Полный список типов контента
```python
types.ContentType.TEXT           # Текст
types.ContentType.PHOTO          # Фото
types.ContentType.AUDIO          # Аудио
types.ContentType.VIDEO          # Видео
types.ContentType.VOICE          # Голосовое
types.ContentType.DOCUMENT       # Документ
types.ContentType.STICKER        # Стикер
types.ContentType.VIDEO_NOTE     # Видео-кружок
types.ContentType.CONTACT        # Контакт
types.ContentType.LOCATION       # Локация
types.ContentType.VENUE          # Место
types.ContentType.ANIMATION      # Анимация (GIF)
types.ContentType.DICE           # Кости
types.ContentType.POLL           # Опрос
types.ContentType.WEB_PAGE_DATA  # Веб-страница
```

## Получение информации о сообщении

### Основные атрибуты сообщения
```python
@dp.message()
async def message_info(message: types.Message):
    # Информация о сообщении
    msg_id = message.message_id      # ID сообщения
    from_user = message.from_user    # Объект пользователя
    date = message.date              # Дата отправки
    chat = message.chat              # Объект чата
    content_type = message.content_type # Тип контента
    
    # Для текстовых сообщений
    text = message.text              # Текст сообщения
    entities = message.entities      # Форматирование (жирный, курсив и т.д.)
    
    # Для медиа
    photo = message.photo            # Список размеров фото
    document = message.document      # Документ
    audio = message.audio            # Аудио
    video = message.video            # Видео
    
    # Ответ на сообщение
    reply_to_message = message.reply_to_message # Ответ на сообщение
    
    # Пересланное сообщение
    forward_from = message.forward_from     # От кого переслано
    forward_date = message.forward_date     # Дата пересылки
```

### Пример обработчика с детальной информацией
```python
@dp.message()
async def detailed_message_handler(message: types.Message):
    info = (
        f"ID сообщения: {message.message_id}\n"
        f"ID пользователя: {message.from_user.id}\n"
        f"Имя пользователя: {message.from_user.full_name}\n"
        f"Тип контента: {message.content_type}\n"
        f"Дата: {message.date}\n"
    )
    
    if message.text:
        info += f"Текст: {message.text}\n"
    
    if message.photo:
        info += f"Фото: {len(message.photo)} размеров\n"
    
    if message.document:
        info += f"Документ: {message.document.file_name}\n"
    
    await message.answer(info)
```

## Работа с обновлениями

### Обработка разных типов обновлений
```python
# Обработка сообщений
@dp.message()
async def handle_messages(message: types.Message):
    await message.answer("Получено сообщение")

# Обработка колбэков
@dp.callback_query()
async def handle_callbacks(callback: types.CallbackQuery):
    await callback.answer("Колбэк получен")

# Обработка инлайн-запросов
@dp.inline_query()
async def handle_inline_queries(inline_query: types.InlineQuery):
    pass

# Обработка редактирования сообщений
@dp.edited_message()
async def handle_edited_messages(message: types.Message):
    await message.answer("Сообщение отредактировано")

# Обработка присоединения к чату
@dp.my_chat_member()
async def handle_chat_member_update(update: types.ChatMemberUpdated):
    await update.bot.send_message(
        update.chat.id,
        "Мои права в чате изменились"
    )
```

### Фильтрация обновлений по типу
```python
from aiogram import F
from aiogram.filters import Command

# Только текстовые сообщения
@dp.message(F.text)
async def text_handler(message: types.Message):
    await message.answer("Это текст")

# Только команды
@dp.message(Command())
async def command_handler(message: types.Message):
    await message.answer("Это команда")

# Только фото
@dp.message(F.photo)
async def photo_handler(message: types.Message):
    await message.answer("Это фото")

# Комбинация фильтров
@dp.message(F.text & ~Command())
async def text_not_command(message: types.Message):
    await message.answer("Текст, но не команда")
```

## Практические примеры

### 1. Обработка всех типов медиа
```python
@dp.message()
async def media_handler(message: types.Message):
    if message.photo:
        await message.answer_photo(
            message.photo[-1].file_id,
            caption="Ваше фото"
        )
    elif message.video:
        await message.answer_video(
            message.video.file_id,
            caption="Ваше видео"
        )
    elif message.audio:
        await message.answer_audio(
            message.audio.file_id,
            caption="Ваше аудио"
        )
    elif message.document:
        await message.answer_document(
            message.document.file_id,
            caption="Ваш документ"
        )
    else:
        await message.answer("Неизвестный тип медиа")
```

### 2. Логирование всех обновлений
```python
import logging

@dp.update()
async def log_updates(update: types.Update):
    logging.info(f"Получено обновление типа: {update.event_type}")
    
    if update.message:
        logging.info(f"Сообщение от {update.message.from_user.id}: {update.message.text}")
    
    elif update.callback_query:
        logging.info(f"Колбэк от {update.callback_query.from_user.id}")
```

### 3. Обработка ошибок в обновлениях
```python
from aiogram.exceptions import TelegramAPIError

@dp.message()
async def safe_handler(message: types.Message):
    try:
        # Ваш код обработки
        await message.answer("Обработка выполнена")
    except TelegramAPIError as e:
        logging.error(f"Ошибка Telegram API: {e}")
    except Exception as e:
        logging.error(f"Неизвестная ошибка: {e}")
```

## Советы по работе с обновлениями
1. **Всегда проверяйте тип обновления** перед обработкой
2. **Используйте фильтры** для разделения логики
3. **Логируйте все обновления** для отладки
4. **Обрабатывайте ошибки** для стабильной работы
5. **Используйте магические фильтры (F)** для сложных условий
6. **Разделяйте обработчики** по типам обновлений