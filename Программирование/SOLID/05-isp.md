# Глава 4: Interface Segregation Principle (ISP) - Принцип разделения интерфейса

## Оглавление

- [Теоретические основы](#теоретические-основы)
- [Почему ISP важен?](#почему-isp-важен)
- [Распознавание нарушений ISP](#распознавание-нарушений-isp)
- [Пример нарушения ISP](#пример-нарушения-isp)
- [Рефакторинг согласно ISP](#рефакторинг-согласно-isp)
- [Более сложный пример: Система платежей](#более-сложный-пример-система-платежей)
- [Практическое применение ISP с паттернами](#практическое-применение-isp-с-паттернами)
- [Распространенные ошибки при применении ISP](#распространенные-ошибки-при-применении-isp)
- [Практические советы по применению ISP](#практические-советы-по-применению-isp)
- [Упражнения для практики](#упражнения-для-практики)
- [Заключение по ISP](#заключение-по-isp)

## Теоретические основы

**Принцип разделения интерфейса (ISP)** формулируется так:

> Клиенты не должны быть вынуждены зависеть от методов, которые они не используют.

Проще говоря, лучше иметь много небольших, специализированных интерфейсов, чем один большой, универсальный. Каждый интерфейс должен быть сфокусирован на одной конкретной задаче.

### Исторический контекст

Принцип ISP был сформулирован Робертом Мартином как часть SOLID принципов. Он подчеркивает важность создания небольших, сфокусированных интерфейсов вместо больших, "толстых" интерфейсов, которые пытаются сделать слишком много.

### Формальное определение

Согласно Роберту Мартину, ISP рекомендует: "Создавайте мелкозернистые интерфейсы, специфичные для клиента. Клиенты не должны зависеть от интерфейсов, которые они не используют."

### Ключевые концепции ISP

1. **Интерфейс должен быть небольшим**
   - Каждый интерфейс должен решать одну конкретную задачу
   - Избегайте создания интерфейсов с множеством несвязанных методов

2. **Интерфейсы должны быть специфичными для клиента**
   - Проектируйте интерфейсы с учетом конкретных потребностей клиентов
   - Разные клиенты могут использовать разные подмножества функциональности

3. **Композиция интерфейсов**
   - Используйте множественное наследование интерфейсов для создания сложных объектов
   - Комбинируйте небольшие интерфейсы для получения нужной функциональности

### ISP в контексте Python

В Python интерфейсы обычно реализуются через абстрактные базовые классы (ABC) или протоколы. Python поддерживает множественное наследование, что делает реализацию ISP особенно удобной.

## Почему ISP важен?

### Снижение связанности

- **Компоненты становятся менее зависимыми друг от друга**
  - Каждый компонент зависит только от нужных ему методов
  - Изменения в одной части системы меньше влияют на другие
  - Улучшается модульность системы

- **Уменьшение "ripple effect"**
  - Изменение интерфейса влияет только на его реальных пользователей
  - Не затрагиваются компоненты, которые не используют измененную часть
  - Упрощается рефакторинг и поддержка

### Улучшение гибкости

- **Легче изменять интерфейсы без влияния на несвязанный код**
  - Маленькие интерфейсы проще изменять
  - Изменения локализованы и предсказуемы
  - Уменьшается риск сломать существующий функционал

- **Упрощается добавление новой функциональности**
  - Новые методы добавляются в специализированные интерфейсы
  - 
- **Легко создавать специализированные реализации**
  - Можно реализовать только нужные интерфейсы
  - Избегаем пустых или выбрасывающих исключения реализаций
  - Улучшается переиспользование кода

### Уменьшение побочных эффектов

- **Изменения в одном интерфейсе не влияют на другие**
  - Каждый интерфейс независим
  - Изменения локализованы
  - Уменьшается количество регрессионных ошибок

- **Четкое разделение ответственности**
  - Каждый интерфейс имеет одну четкую цель
  - Легко понять назначение интерфейса
  - Улучшается документирование кода

### Улучшение читаемости

- **Небольшие интерфейсы легче понять**
  - Легко увидеть все методы интерфейса
  - Понятна сфера ответственности
  - Уменьшается когнитивная нагрузка

- **Лучше документация**
  - Каждый интерфейс имеет четкое назначение
  - Легко документировать функциональность
  - Улучшается понимание системы

### Повышение тестируемости

- **Легче создавать моки и тесты**
  - Моки реализуют только нужные интерфейсы
  - Тесты становятся более сфокусированными
  - Упрощается модульное тестирование

- **Уменьшается сложность тестов**
  - Не нужно реализовывать неиспользуемые методы
  - Тесты становятся более читаемыми
  - Улучшается покрытие тестами

## Распознавание нарушений ISP

### Признаки нарушения ISP

1. **Большие интерфейсы с множеством методов**
   - Интерфейсы с более чем 5-7 методами
   - Методы решают разные задачи
   - Нет четкой сфокусированности

2. **Классы реализуют методы как пустые или выбрасывающие исключения**
   ```python
   class MyInterface(ABC):
       @abstractmethod
       def method1(self): pass
       @abstractmethod
       def method2(self): pass
       @abstractmethod
       def method3(self): pass

   class MyClass(MyInterface):
       def method1(self): pass
       def method2(self): pass
       def method3(self):
           raise NotImplementedError("Not supported")
   ```

3. **Клиенты используют только часть методов интерфейса**
   - Большинство методов интерфейса не используются некоторыми клиентами
   - Клиенты вынуждены зависеть от ненужных методов
   - Интерфейс пытается обслужить слишком много сценариев

4. **Комментарии вроде "Этот метод не поддерживается в данной реализации"**
   - Явные указания на то, что часть функциональности не реализована
   - Признак того, что интерфейс слишком большой
   - Нарушение принципа разделения интерфейса

### Примеры кода с нарушениями ISP

```python
# Плохо: нарушение ISP
class Worker(ABC):
    @abstractmethod
    def work(self): pass
    
    @abstractmethod
    def eat(self): pass
    
    @abstractmethod
    def sleep(self): pass

class HumanWorker(Worker):
    def work(self): print("Working")
    def eat(self): print("Eating")
    def sleep(self): print("Sleeping")

class RobotWorker(Worker):
    def work(self): print("Working")
    def eat(self):
        # Роботы не едят - нарушение ISP
        raise NotImplementedError("Robots don't eat")
    def sleep(self):
        # Роботы не спят - нарушение ISP
        raise NotImplementedError("Robots don't sleep")
```

В этом примере RobotWorker вынужден реализовывать методы, которые ему не нужны.

## Пример нарушения ISP

Рассмотрим пример с интерфейсом для работы с мультимедийными устройствами:

```python
from abc import ABC, abstractmethod

class MultimediaDevice(ABC):
    """Интерфейс для мультимедийных устройств"""
    
    @abstractmethod
    def play_audio(self):
        pass
    
    @abstractmethod
    def play_video(self):
        pass
    
    @abstractmethod
    def print_document(self):
        pass
    
    @abstractmethod
    def scan_document(self):
        pass

class SmartTV(MultimediaDevice):
    """Умный телевизор"""
    
    def play_audio(self):
        print("Playing audio on TV")
    
    def play_video(self):
        print("Playing video on TV")
    
    def print_document(self):
        # Телевизор не может печатать документы
        raise NotImplementedError("TV cannot print documents")
    
    def scan_document(self):
        # Телевизор не может сканировать документы
        raise NotImplementedError("TV cannot scan documents")

class Printer(MultimediaDevice):
    """Принтер"""
    
    def play_audio(self):
        # Принтер не воспроизводит аудио
        raise NotImplementedError("Printer cannot play audio")
    
    def play_video(self):
        # Принтер не воспроизводит видео
        raise NotImplementedError("Printer cannot play video")
    
    def print_document(self):
        print("Printing document")
    
    def scan_document(self):
        print("Scanning document")
```

### Проблемы этого кода:

1. **SmartTV вынужден реализовывать методы print_document() и scan_document()**, которые ему не нужны
2. **Printer вынужден реализовывать методы play_audio() и play_video()**, которые ему не нужны
3. **Нарушение ISP** - клиенты зависят от методов, которые они не используют
4. **Код становится громоздким и трудно поддерживаемым**
5. **Появляется много исключений NotImplementedError**, что ухудшает дизайн

### Анализ нарушений ISP

1. **Интерфейс слишком большой**
   - MultimediaDevice пытается охватить все возможные функции
   - Методы не связаны между собой по смыслу
   - Нет единой сферы ответственности

2. **Клиенты вынуждены реализовывать ненужные методы**
   - SmartTV не должен уметь печатать и сканировать
   - Printer не должен воспроизводить аудио и видео
   - Нарушение принципа разделения интерфейса

3. **Нарушение других принципов SOLID**
   - SRP: интерфейс имеет несколько ответственностей
   - LSP: подклассы не могут безопасно заменить базовый класс
   - DIP: жесткая зависимость от большого интерфейса

## Рефакторинг согласно ISP

Разделим большой интерфейс на несколько специализированных:

```python
from abc import ABC, abstractmethod

class AudioPlayer(ABC):
    """Интерфейс для воспроизведения аудио"""
    
    @abstractmethod
    def play_audio(self):
        pass

class VideoPlayer(ABC):
    """Интерфейс для воспроизведения видео"""
    
    @abstractmethod
    def play_video(self):
        pass

class Printer(ABC):
    """Интерфейс для печати документов"""
    
    @abstractmethod
    def print_document(self):
        pass

class Scanner(ABC):
    """Интерфейс для сканирования документов"""
    
    @abstractmethod
    def scan_document(self):
        pass

class SmartTV(AudioPlayer, VideoPlayer):
    """Умный телевизор - реализует только нужные интерфейсы"""
    
    def play_audio(self):
        print("Playing audio on TV")
    
    def play_video(self):
        print("Playing video on TV")

class SimplePrinter(Printer):
    """Простой принтер - только печать"""
    
    def print_document(self):
        print("Printing document")

class AllInOnePrinter(AudioPlayer, VideoPlayer, Printer, Scanner):
    """Многофункциональное устройство - реализует все интерфейсы"""
    
    def play_audio(self):
        print("Playing audio on all-in-one device")
    
    def play_video(self):
        print("Playing video on all-in-one device")
    
    def print_document(self):
        print("Printing document on all-in-one device")
    
    def scan_document(self):
        print("Scanning document on all-in-one device")
```

### Преимущества этого подхода:

1. **Каждый класс реализует только те интерфейсы, которые ему действительно нужны**
   - SmartTV реализует только AudioPlayer и VideoPlayer
   - SimplePrinter реализует только Printer
   - AllInOnePrinter реализует все интерфейсы, потому что это его назначение

2. **Легко добавлять новые типы устройств с разными комбинациями возможностей**
   - Можно создать устройство, которое только сканирует
   - Можно создать устройство, которое воспроизводит только аудио
   - Гибкость системы значительно возрастает

3. **Код стал более чистым и понятным**
   - Каждый интерфейс имеет четкую сферу ответственности
   - Легко понять, что умеет делать каждое устройство
   - Уменьшилось количество исключений NotImplementedError

4. **Уменьшилась связанность между классами**
   - Классы зависят только от нужных им интерфейсов
   - Изменение одного интерфейса не влияет на другие
   - Улучшилась модульность системы

5. **Соблюдение ISP и других принципов SOLID**
   - ISP: клиенты зависят только от нужных методов
   - SRP: каждый интерфейс имеет одну ответственность
   - LSP: подклассы безопасно заменяют базовые классы
   - DIP: зависимость от абстракций, а не от конкретных реализаций

## Более сложный пример: Система платежей

Рассмотрим систему обработки платежей с нарушением ISP:

```python
from abc import ABC, abstractmethod

class PaymentProcessor(ABC):
    """Интерфейс для обработки платежей"""
    
    @abstractmethod
    def process_payment(self, amount):
        pass
    
    @abstractmethod
    def refund_payment(self, amount):
        pass
    
    @abstractmethod
    def validate_card(self, card_number):
        pass
    
    @abstractmethod
    def send_receipt(self, email):
        pass

class CreditCardProcessor(PaymentProcessor):
    """Обработка кредитных карт"""
    
    def process_payment(self, amount):
        print(f"Processing credit card payment: ${amount}")
    
    def refund_payment(self, amount):
        print(f"Refunding credit card payment: ${amount}")
    
    def validate_card(self, card_number):
        print(f"Validating credit card: {card_number}")
    
    def send_receipt(self, email):
        print(f"Sending receipt to: {email}")

class PayPalProcessor(PaymentProcessor):
    """Обработка PayPal"""
    
    def process_payment(self, amount):
        print(f"Processing PayPal payment: ${amount}")
    
    def refund_payment(self, amount):
        print(f"Refunding PayPal payment: ${amount}")
    
    def validate_card(self, card_number):
        # PayPal не требует валидации карты
        raise NotImplementedError("PayPal doesn't use card validation")
    
    def send_receipt(self, email):
        print(f"Sending receipt to: {email}")

class CryptoProcessor(PaymentProcessor):
    """Обработка криптовалюты"""
    
    def process_payment(self, amount):
        print(f"Processing cryptocurrency payment: ${amount}")
    
    def refund_payment(self, amount):
        # Криптовалютные платежи обычно нельзя отменить
        raise NotImplementedError("Cryptocurrency payments cannot be refunded")
    
    def validate_card(self, card_number):
        # Криптовалюта не использует карты
        raise NotImplementedError("Cryptocurrency doesn't use cards")
    
    def send_receipt(self, email):
        # Криптоплатежи могут не отправлять receipt на email
        raise NotImplementedError("Cryptocurrency doesn't send email receipts")
```

### Проблемы этого кода:

1. **PayPalProcessor не требует валидации карт**, но вынужден реализовывать validate_card()
2. **CryptoProcessor не поддерживает возвраты, валидацию карт и отправку receipt**, но вынужден реализовывать все методы
3. **Много исключений NotImplementedError**, что указывает на плохой дизайн
4. **Интерфейс слишком большой и пытается охватить все возможные сценарии**

### Рефакторинг системы платежей

Разделим интерфейс на специализированные:

```python
from abc import ABC, abstractmethod

class PaymentProcessor(ABC):
    """Базовый интерфейс для обработки платежей"""
    
    @abstractmethod
    def process_payment(self, amount):
        pass

class RefundableProcessor(PaymentProcessor):
    """Интерфейс для процессоров с возможностью возврата"""
    
    @abstractmethod
    def refund_payment(self, amount):
        pass

class CardValidator(ABC):
    """Интерфейс для валидации карт"""
    
    @abstractmethod
    def validate_card(self, card_number):
        pass

class ReceiptSender(ABC):
    """Интерфейс для отправки чеков"""
    
    @abstractmethod
    def send_receipt(self, email):
        pass

class CreditCardProcessor(PaymentProcessor, RefundableProcessor, CardValidator, ReceiptSender):
    """Обработка кредитных карт - реализует все возможности"""
    
    def process_payment(self, amount):
        print(f"Processing credit card payment: ${amount}")
    
    def refund_payment(self, amount):
        print(f"Refunding credit card payment: ${amount}")
    
    def validate_card(self, card_number):
        print(f"Validating credit card: {card_number}")
    
    def send_receipt(self, email):
        print(f"Sending receipt to: {email}")

class PayPalProcessor(PaymentProcessor, RefundableProcessor, ReceiptSender):
    """PayPal - не требует валидации карт"""
    
    def process_payment(self, amount):
        print(f"Processing PayPal payment: ${amount}")
    
    def refund_payment(self, amount):
        print(f"Refunding PayPal payment: ${amount}")
    
    def send_receipt(self, email):
        print(f"Sending receipt to: {email}")

class CryptoProcessor(PaymentProcessor):
    """Криптовалюта - только обработка платежей"""
    
    def process_payment(self, amount):
        print(f"Processing cryptocurrency payment: ${amount}")
```

### Преимущества этого подхода:

1. **Каждый процессор реализует только нужные интерфейсы**
   - CreditCardProcessor реализует все интерфейсы, потому что поддерживает все операции
   - PayPalProcessor не реализует CardValidator, потому что не работает с картами
   - CryptoProcessor реализует только PaymentProcessor, потому что поддерживает только базовые платежи

2. **Нет исключений NotImplementedError**
   - Каждый метод в каждом классе имеет осмысленную реализацию
   - Улучшается качество кода
   - Уменьшается количество ошибок

3. **Легко добавлять новые типы процессоров**
   - Можно создать процессор, который только обрабатывает платежи и отправляет receipt
   - Можно создать процессор, который обрабатывает платежи и валидирует карты
   - Гибкость системы возрастает

4. **Четкое разделение ответственности**
   - Каждый интерфейс имеет одну четкую задачу
   - Легко понять, что умеет делать каждый процессор
   - Улучшается документирование кода

## Практическое применение ISP с паттернами

### 1. Паттерн Adapter

Паттерн Adapter помогает реализовать ISP, когда нужно адаптировать существующий класс к новому интерфейсу:

```python
from abc import ABC, abstractmethod

class SimpleReader(ABC):
    """Простой интерфейс для чтения данных"""
    
    @abstractmethod
    def read_data(self):
        pass

class AdvancedReader(ABC):
    """Расширенный интерфейс для чтения данных"""
    
    @abstractmethod
    def read_data(self):
        pass
    
    @abstractmethod
    def read_metadata(self):
        pass
    
    @abstractmethod
    def validate_data(self):
        pass

class FileAdvancedReader(AdvancedReader):
    """Класс, реализующий расширенный интерфейс"""
    
    def read_data(self):
        print("Reading data from file")
        return "data"
    
    def read_metadata(self):
        print("Reading metadata from file")
        return "metadata"
    
    def validate_data(self):
        print("Validating file data")
        return True

class AdvancedToSimpleAdapter(SimpleReader):
    """Адаптер, который преобразует AdvancedReader в SimpleReader"""
    
    def __init__(self, advanced_reader: AdvancedReader):
        self.advanced_reader = advanced_reader
    
    def read_data(self):
        # Используем только нужный метод из расширенного интерфейса
        return self.advanced_reader.read_data()

# Использование
advanced_reader = FileAdvancedReader()
simple_reader = AdvancedToSimpleAdapter(advanced_reader)
data = simple_reader.read_data()  # Используем только простой интерфейс
```

### 2. Композиция интерфейсов

ISP часто проявляется через композицию небольших интерфейсов:

```python
from abc import ABC, abstractmethod

class Writable(ABC):
    @abstractmethod
    def write(self, data):
        pass

class Readable(ABC):
    @abstractmethod
    def read(self):
        pass

class Closable(ABC):
    @abstractmethod
    def close(self):
        pass

class Flushable(ABC):
    @abstractmethod
    def flush(self):
        pass

class FileStream(Writable, Readable, Closable, Flushable):
    """Файловый поток - реализует все интерфейсы"""
    
    def write(self, data):
        print(f"Writing to file: {data}")
    
    def read(self):
        print("Reading from file")
        return "data"
    
    def close(self):
        print("Closing file")
    
    def flush(self):
        print("Flushing file")

class NetworkStream(Writable, Readable, Closable):
    """Сетевой поток - не поддерживает flush"""
    
    def write(self, data):
        print(f"Writing to network: {data}")
    
    def read(self):
        print("Reading from network")
        return "data"
    
    def close(self):
        print("Closing network connection")

class MemoryStream(Writable, Readable):
    """Поток памяти - не поддерживает close и flush"""
    
    def write(self, data):
        print(f"Writing to memory: {data}")
    
    def read(self):
        print("Reading from memory")
        return "data"
```

### 3. Паттерн Strategy с ISP

Паттерн Strategy можно комбинировать с ISP для создания гибких систем:

```python
from abc import ABC, abstractmethod

class CompressionStrategy(ABC):
    @abstractmethod
    def compress(self, data):
        pass

class EncryptionStrategy(ABC):
    @abstractmethod
    def encrypt(self, data):
        pass

class ValidationStrategy(ABC):
    @abstractmethod
    def validate(self, data):
        pass

class ZIPCompression(CompressionStrategy):
    def compress(self, data):
        print(f"Compressing {data} with ZIP")
        return f"ZIP:{data}"

class AES256Encryption(EncryptionStrategy):
    def encrypt(self, data):
        print(f"Encrypting {data} with AES-256")
        return f"AES:{data}"

class JSONValidation(ValidationStrategy):
    def validate(self, data):
        print(f"Validating {data} as JSON")
        return True

class DataProcessor:
    def __init__(self, compression: CompressionStrategy, 
                 encryption: EncryptionStrategy,
                 validation: ValidationStrategy):
        self.compression = compression
        self.encryption = encryption
        self.validation = validation
    
    def process(self, data):
        if self.validation.validate(data):
            compressed = self.compression.compress(data)
            encrypted = self.encryption.encrypt(compressed)
            return encrypted
        return None

# Использование с разными стратегиями
processor1 = DataProcessor(ZIPCompression(), AES256Encryption(), JSONValidation())
processor2 = DataProcessor(ZIPCompression(), AES256Encryption(), JSONValidation())
```

## Распространенные ошибки при применении ISP

### 1. Слишком мелкое разделение интерфейсов

```python
# Плохо: излишнее разделение
class Adder(ABC):
    @abstractmethod
    def add(self, a, b):
        pass

class Subtractor(ABC):
    @abstractmethod
    def subtract(self, a, b):
        pass

class Multiplier(ABC):
    @abstractmethod
    def multiply(self, a, b):
        pass

class Divider(ABC):
    @abstractmethod
    def divide(self, a, b):
        pass

class Calculator(Adder, Subtractor, Multiplier, Divider):
    def add(self, a, b): return a + b
    def subtract(self, a, b): return a - b
    def multiply(self, a, b): return a * b
    def divide(self, a, b): return a / b
```

Это создает ненужную сложность без реальных преимуществ. Лучше создать один интерфейс Calculator с основными арифметическими операциями.

### 2. Неправильное понимание "клиента"

ISP говорит о клиентах интерфейса, но не всегда понятно, кто является клиентом:

```python
# Плохо: неправильное понимание клиента
class UserInterface(ABC):
    @abstractmethod
    def display_data(self): pass
    
    @abstractmethod
    def handle_input(self): pass
    
    @abstractmethod
    def validate_input(self): pass

class WebInterface(UserInterface):
    def display_data(self): pass
    def handle_input(self): pass
    def validate_input(self): pass

class MobileInterface(UserInterface):
    def display_data(self): pass
    def handle_input(self): pass
    def validate_input(self): pass
```

Здесь "клиент" - это не конечный пользователь, а код, который использует эти интерфейсы. Если все реализации используют все методы, то ISP не нарушен.

### 3. Игнорирование контекста

В небольших проектах строгое следование ISP может быть избыточным:

```python
# Для небольшого скрипта это может быть приемлемо
class DataHandler:
    def save_data(self, data): pass
    def load_data(self): pass
    def validate_data(self, data): pass
    def transform_data(self, data): pass
```

Не всегда нужно разделять интерфейсы, если:
- Проект небольшой
- Все реализации используют все методы
- Нет планов на расширение функциональности

### 4. Создание интерфейсов без реализации

Иногда разработчики создают интерфейсы, которые никогда не будут использованы:

```python
# Плохо: ненужные интерфейсы
class FutureFeature(ABC):
    @abstractmethod
    def future_method(self):
        pass

class CurrentClass(FutureFeature):
    def future_method(self):
        raise NotImplementedError("Not implemented yet")
```

Лучше добавлять интерфейсы тогда, когда они действительно нужны.

## Практические советы по применению ISP

### 1. Проектируйте интерфейсы для конкретных клиентов

Подумайте, кто будет использовать интерфейс:
- Какие методы действительно нужны каждому клиенту?
- Какие группы методов используются вместе?
- Можно ли разделить интерфейс на логические части?

```python
# Хорошо: интерфейсы спроектированы для клиентов
class FileReader:
    def read_file(self, filename): pass

class FileWriter:
    def write_file(self, filename, data): pass

class FileValidator:
    def validate_file(self, filename): pass

class FullFileHandler(FileReader, FileWriter, FileValidator):
    def read_file(self, filename): pass
    def write_file(self, filename, data): pass
    def validate_file(self, filename): pass
```

### 2. Используйте множественное наследование интерфейсов

Python поддерживает множественное наследование, что делает реализацию ISP особенно удобной:

```python
# Хорошо: множественное наследование интерфейсов
class Drawable(ABC):
    @abstractmethod
    def draw(self): pass

class Clickable(ABC):
    @abstractmethod
    def click(self): pass

class Draggable(ABC):
    @abstractmethod
    def drag(self): pass

class Button(Drawable, Clickable):
    def draw(self): print("Drawing button")
    def click(self): print("Button clicked")

class Slider(Drawable, Clickable, Draggable):
    def draw(self): print("Drawing slider")
    def click(self): print("Slider clicked")
    def drag(self): print("Slider dragged")
```

### 3. Следите за когезией

Методы в интерфейсе должны быть логически связаны:
- Все методы должны служить одной цели
- Не смешивайте разные уровни абстракции
- Интерфейс должен иметь высокую внутреннюю связность

```python
# Хорошо: высокая когезия
class DatabaseConnection(ABC):
    @abstractmethod
    def connect(self): pass
    
    @abstractmethod
    def disconnect(self): pass
    
    @abstractmethod
    def execute_query(self, query): pass
    
    @abstractmethod
    def begin_transaction(self): pass
    
    @abstractmethod
    def commit_transaction(self): pass
```

### 4. Используйте абстрактные базовые классы

ABC помогают определить четкие контракты и предотвращают создание неполных реализаций:

```python
# Хорошо: использование ABC
from abc import ABC, abstractmethod

class NotificationService(ABC):
    @abstractmethod
    def send_notification(self, recipient, message):
        pass

class EmailNotificationService(NotificationService):
    def send_notification(self, recipient, message):
        print(f"Sending email to {recipient}: {message}")

# Нельзя создать экземпляр без реализации всех методов
# Это предотвратит ошибки во время выполнения
```

### 5. Рефакторите существующие интерфейсы

Если вы обнаружили большой интерфейс, разделите его:
1. Выделите группы связанных методов
2. Создайте отдельные интерфейсы для каждой группы
3. Обновите реализации для использования новых интерфейсов
4. Тестируйте, чтобы ничего не сломалось

## Упражнения для практики

### Упражнение 1: Рефакторинг интерфейса работника

Дан интерфейс, который нарушает ISP:

```python
from abc import ABC, abstractmethod

class Employee(ABC):
    @abstractmethod
    def work(self):
        pass
    
    @abstractmethod
    def attend_meeting(self):
        pass
    
    @abstractmethod
    def submit_report(self):
        pass
    
    @abstractmethod
    def manage_team(self):
        pass
    
    @abstractmethod
    def code_review(self):
        pass
```

**Задание:** Разделите этот интерфейс на несколько специализированных интерфейсов и создайте классы для разных типов работников (Manager, Developer, Intern), которые реализуют только нужные интерфейсы.

**Требования:**
1. Создайте интерфейсы: Worker, MeetingAttendee, ReportSubmitter, TeamManager, CodeReviewer
2. Реализуйте классы:
   - Manager: Worker, MeetingAttendee, ReportSubmitter, TeamManager
   - Developer: Worker, MeetingAttendee, CodeReviewer
   - Intern: Worker, MeetingAttendee
3. Покажите, как используются эти классы

### Упражнение 2: Создание системы плагинов

Создайте систему плагинов для текстового редактора, которая позволяет:
- Добавлять новые форматы файлов для сохранения
- Добавлять новые форматы файлов для загрузки
- Добавлять новые функции обработки текста
- Добавлять новые функции экспорта

Используйте ISP для создания гибкой системы плагинов.

**Требования:**
1. Создайте интерфейсы:
   - FileSaver (для сохранения файлов)
   - FileLoader (для загрузки файлов)
   - TextProcessor (для обработки текста)
   - Exporter (для экспорта)
2. Реализуйте несколько плагинов для каждого интерфейса
3. Создайте менеджер плагинов, который работает с интерфейсами
4. Покажите, как добавлять новые плагины без изменения существующего кода

### Упражнение 3: Анализ и рефакторинг

Проанализируйте следующий код и определите, нарушает ли он ISP. Если да, предложите рефакторинг:

```python
class SmartDevice(ABC):
    @abstractmethod
    def turn_on(self): pass
    
    @abstractmethod
    def turn_off(self): pass
    
    @abstractmethod
    def connect_to_wifi(self): pass
    
    @abstractmethod
    def make_call(self): pass
    
    @abstractmethod
    def send_message(self): pass
    
    @abstractmethod
    def take_photo(self): pass
    
    @abstractmethod
    def play_music(self): pass

class Smartphone(SmartDevice):
    def turn_on(self): print("Phone on")
    def turn_off(self): print("Phone off")
    def connect_to_wifi(self): print("Connected to WiFi")
    def make_call(self): print("Making call")
    def send_message(self): print("Sending message")
    def take_photo(self): print("Taking photo")
    def play_music(self): print("Playing music")

class SmartSpeaker(SmartDevice):
    def turn_on(self): print("Speaker on")
    def turn_off(self): print("Speaker off")
    def connect_to_wifi(self): print("Connected to WiFi")
    def make_call(self): raise NotImplementedError("Cannot make calls")
    def send_message(self): raise NotImplementedError("Cannot send messages")
    def take_photo(self): raise NotImplementedError("Cannot take photos")
    def play_music(self): print("Playing music")

class SmartLight(SmartDevice):
    def turn_on(self): print("Light on")
    def turn_off(self): print("Light off")
    def connect_to_wifi(self): print("Connected to WiFi")
    def make_call(self): raise NotImplementedError("Cannot make calls")
    def send_message(self): raise NotImplementedError("Cannot send messages")
    def take_photo(self): raise NotImplementedError("Cannot take photos")
    def play_music(self): raise NotImplementedError("Cannot play music")
```

**Задание:**
1. Определите нарушения ISP в этом коде
2. Предложите и реализуйте рефакторинг
3. Создайте новые интерфейсы и обновите классы
4. Покажите, как улучшенный код решает проблемы ISP

## Заключение по ISP

Принцип разделения интерфейса помогает создавать гибкие, слабо связанные системы, где каждый компонент зависит только от того, что ему действительно нужно.

### Ключевые моменты, которые нужно запомнить:

1. **Клиенты не должны зависеть от методов, которые они не используют**
   - Создавайте небольшие, специализированные интерфейсы вместо больших универсальных
   - Используйте множественное наследование для комбинирования интерфейсов
   - Следите за когезией методов в интерфейсе

2. **ISP улучшает гибкость и снижает связанность**
   - Маленькие интерфейсы легче изменять
   - Изменения локализованы и не влияют на несвязанный код
   - Система становится более модульной

3. **ISP требует баланса**
   - Не создавайте слишком мелкие интерфейсы без необходимости
   - Учитывайте контекст и масштаб проекта
   - Иногда можно нарушить ISP ради простоты

4. **ISP работает в связке с другими принципами SOLID**
   - SRP: каждый интерфейс имеет одну ответственность
   - OCP: небольшие интерфейсы легче расширять
   - LSP: специализированные интерфейсы легче реализовывать
   - DIP: зависимость от абстракций обеспечивает гибкость

### Практические шаги для применения ISP:

1. **Анализируйте существующие интерфейсы**
   - Ищите большие интерфейсы с множеством методов
   - Определите, какие методы используются вместе
   - Выделите группы связанных методов

2. **Разделяйте большие интерфейсы**
   - Создайте специализированные интерфейсы для каждой группы
   - Используйте множественное наследование для комбинаций
   - Обновите реализации для использования новых интерфейсов

3. **Тестируйте разделенные интерфейсы**
   - Убедитесь, что функциональность не пострадала
   - Проверьте, что реализации используют только нужные методы
   - Убедитесь, что система стала более гибкой

4. **Проектируйте новые интерфейсы с учетом ISP**
   - Думайте о клиентах интерфейса
   - Создавайте небольшие, сфокусированные интерфейсы
   - Используйте композицию интерфейсов для сложных объектов

В [следующей главе](06-dip) мы изучим последний принцип SOLID - Принцип инверсии зависимостей (DIP), который завершает наше понимание создания гибких и поддерживаемых систем.