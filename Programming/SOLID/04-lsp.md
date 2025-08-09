# Глава 3: Liskov Substitution Principle (LSP) - Принцип подстановки Лисков

## Оглавление

- [Теоретические основы](#теоретические-основы)
- [Почему LSP важен?](#почему-lsp-важен)
- [Условия соблюдения LSP](#условия-соблюдения-lsp)
- [Распознавание нарушений LSP](#распознавание-нарушений-lsp)
- [Пример нарушения LSP: Классический пример с Rectangle и Square](#пример-нарушения-lsp-классический-пример-с-rectangle-и-square)
- [Решение проблемы LSP](#решение-проблемы-lsp)
- [Еще один пример: Иерархия банковских счетов](#еще-один-пример-иерархия-банковских-счетов)
- [Практические примеры нарушений LSP](#практические-примеры-нарушений-lsp)
- [Практические советы по применению LSP](#практические-советы-по-применению-lsp)
- [Распространенные ошибки при применении LSP](#распространенные-ошибки-при-применении-lsp)
- [Упражнения для практики](#упражнения-для-практики)
- [Заключение по LSP](#заключение-по-lsp)

## Теоретические основы

**Принцип подстановки Лисков (LSP)** был введен Барбарой Лисков в 1987 году на конференции OOPSLA и формулируется так:

> Объекты в программе должны быть заменяемы на экземпляры их подтипов без изменения правильности выполнения программы.

Проще говоря, если у вас есть класс B, который является подклассом класса A, то вы должны иметь возможность использовать объект B вместо объекта A в любом месте программы, и программа должна продолжать работать корректно.

### Исторический контекст

Барбара Лисков представила этот принцип в своей работе "Data Abstraction and Hierarchy". Позже принцип был популяризирован Робертом Мартином как часть SOLID принципов.

### Формальное определение

Формально LSP можно определить так: "Пусть S(x) является свойством, верным для объектов x типа T. Тогда S(y) также должно быть верным для объектов y типа S, где S является подтипом T".

Это означает, что подтипы должны вести себя так, чтобы пользователь не мог отличить их от базового типа.

### Поведенческая подтипизация

LSP вводит понятие "поведенческой подтипизации", которая означает, что подтип не только должен поддерживать все операции базового типа, но и должен вести себя ожидаемо с точки зрения пользователя.

## Почему LSP важен?

### Гарантия корректности полиморфизма

- **Уверенность, что подклассы ведут себя ожидаемо**
  - Полиморфизм работает только если подклассы соответствуют контракту
  - Пользователи могут безопасно использовать подклассы через интерфейс базового класса
  - Снижается риск неожиданного поведения

- **Надежное использование наследования**
  - Наследование становится предсказуемым
  - Подклассы действительно являются "видами" базовых классов
  - Улучшается понимание иерархии классов

### Улучшение повторного использования кода

- **Можно безопасно использовать подклассы**
  - Клиентский код не нужно изменять при замене реализации
  - Компоненты становятся более универсальными
  - Уменьшается дублирование кода

- **Легко создавать библиотеки и фреймворки**
  - Пользователи могут расширять функциональность через наследование
  - Гарантируется совместимость расширений
  - Улучшается экосистема продукта

### Снижение ошибок

- **Предотвращает неожиданное поведение при замене объектов**
  - Подклассы не нарушают ожидания пользователей
  - Снижается количество багов, связанных с наследованием
  - Упрощается отладка

- **Улучшает качество кода**
  - Заставляет разработчиков тщательно проектировать иерархии
  - Предотвращает "неправильное" наследование
  - Способствует созданию более надежных систем

### Улучшение тестируемости

- **Легче тестировать иерархии классов**
  - Можно использовать одни и те же тесты для базовых классов и подклассов
  - Упрощается создание тестовых сценариев
  - Улучшается покрытие тестами

- **Подклассы проходят те же тесты, что и базовые классы**
  - Гарантируется соответствие поведению
  - Тесты становятся более надежными
  - Уменьшается количество специфичных тестов

### Повышение надежности

- **Система становится более предсказуемой**
  - Поведение подклассов ожидаемо
  - Уменьшается количество "сюрпризов" при замене компонентов
  - Улучшается доверие к системе

- **Улучшается документация и понимание**
  - Четкие контракты между классами
  - Легче понимать, как использовать подклассы
  - Улучшается коммуникация в команде

## Условия соблюдения LSP

Для соблюдения LSP подкласс должен соответствовать нескольким ключевым условиям:

### 1. Сохранение инвариантов

**Инварианты** - это условия, которые всегда должны быть истинными для объекта. Подклассы должны сохранять все инварианты базового класса.

```python
# Плохо: нарушение инвариантов
class Rectangle:
    def __init__(self, width, height):
        self.width = width
        self.height = height
    
    @property
    def area(self):
        return self.width * self.height

class Square(Rectangle):
    def __init__(self, side):
        super().__init__(side, side)
    
    @property
    def width(self):
        return self._width
    
    @width.setter
    def width(self, value):
        self._width = value
        self._height = value  # Нарушение инварианта!
    
    @property
    def height(self):
        return self._height
    
    @height.setter
    def height(self, value):
        self._height = value
        self._width = value  # Нарушение инварианта!
```
В этом примере Square нарушает инвариант Rectangle, где ширина и высота независимы.

### 2. Не усиление предусловий

**Предусловия** - это условия, которые должны быть истинными перед вызовом метода. Подклассы не должны делать предусловия строже, чем в базовом классе.
```python
# Плохо: усиление предусловий
class BaseProcessor:
    def process(self, data):
        # Принимает любые данные
        return data.upper()

class StrictProcessor(BaseProcessor):
    def process(self, data):
        # Усиливает предусловие - теперь требует строку
        if not isinstance(data, str):
            raise TypeError("Data must be string")
        return data.upper()
```
StrictProcessor нарушает LSP, так как он не может обработать данные, которые мог бы обработать BaseProcessor.

### 3. Не ослабление постусловий

**Постусловия** - это условия, которые гарантируются после выполнения метода. Подклассы не должны делать постусловия слабее, чем в базовом классе.
```python
# Плохо: ослабление постусловий
class DataValidator:
    def validate(self, data):
        # Гарантирует, что возвращает bool
        return isinstance(data, str)

class LazyValidator(DataValidator):
    def validate(self, data):
        # Ослабляет постусловие - может вернуть None
        if data is None:
            return None
        return isinstance(data, str)
```
LazyValidator нарушает LSP, так как он не гарантирует тот же результат, что и DataValidator.

### 4. Сохранение истории

**История** - это свойства объекта, которые не должны меняться. Подклассы не должны изменять свойства, которые базовый класс не изменяет.
```python
# Плохо: изменение истории
class ImmutablePoint:
    def __init__(self, x, y):
        self._x = x
        self._y = y
    
    @property
    def x(self):
        return self._x
    
    @property
    def y(self):
        return self._y

class MutablePoint(ImmutablePoint):
    def __init__(self, x, y):
        super().__init__(x, y)
    
    @property
    def x(self):
        return self._x
    
    @x.setter
    def x(self, value):
        self._x = value  # Изменяет неизменяемое свойство!
    
    @property
    def y(self):
        return self._y
    
    @y.setter
    def y(self, value):
        self._y = value  # Изменяет неизменяемое свойство!
```
MutablePoint нарушает LSP, так как он изменяет свойства, которые ImmutablePoint обещал не изменять.

## Распознавание нарушений LSP

### Признаки нарушения LSP

1. **Проверка типа объекта (isinstance) перед использованием**
	```python
	def process_shape(shape):
		if isinstance(shape, Circle):
			shape.draw_circle()
		elif isinstance(shape, Rectangle):
			shape.draw_rectangle()
	```
	 Это указывает на то, что подклассы ведут себя по-разному.

2. **Подкласс выбрасывает исключения, которые не выбрасывает базовый класс**
	```python
	class Bird:
		def fly(self):
			print("Flying")

	class Penguin(Bird):
		def fly(self):
		    raise NotImplementedError("Penguins can't fly")
	```
3. **Подкласс возвращает значения другого типа, чем базовый класс**
	```python
	class DataProcessor:
	    def process(self, data):
	        return str(data)

	class NumberProcessor(DataProcessor):
	    def process(self, data):
	        return int(data)  # Другой тип!
	```
4. **Подкласс имеет пустые реализации методов базового класса**
	```python
	class Vehicle:
		def start_engine(self):
		    pass
		def stop_engine(self):
		    pass

	class Bicycle(Vehicle):
	    def start_engine(self):
	        pass  # Пустая реализация
	    def stop_engine(self):
	        pass  # Пустая реализация
	```
5. **Подкласс изменяет состояние, которое базовый класс не изменяет**
	```python
	class ReadOnlyFile:
	    def __init__(self, filename):
	        self.filename = filename
	        self._content = None
	    
	    def read(self):
	        if self._content is None:
	            with open(self.filename, 'r') as f:
	                self._content = f.read()
	        return self._content
	
	class WritableFile(ReadOnlyFile):
	    def write(self, content):
	        with open(self.filename, 'w') as f:
	            f.write(content)
	        self._content = content  # Изменяет состояние!
	```
### Примеры кода с нарушениями LSP
```python
# Плохо: нарушение LSP
class DatabaseConnection:
    def connect(self):
        pass
    
    def execute_query(self, query):
        pass

class NoSQLDatabaseConnection(DatabaseConnection):
    def execute_query(self, query):
        # NoSQL базы данных могут не поддерживать SQL запросы
        raise NotImplementedError("NoSQL databases don't support SQL queries")
```
В этом примере NoSQLDatabaseConnection не может заменить DatabaseConnection, так как он не поддерживает те же операции.

## Пример нарушения LSP: Классический пример с Rectangle и Square

Рассмотрим классический пример нарушения LSP:
```python
class Rectangle:
    def __init__(self, width, height):
        self.width = width
        self.height = height
    
    def set_width(self, width):
        self.width = width
    
    def set_height(self, height):
        self.height = height
    
    def area(self):
        return self.width * self.height

class Square(Rectangle):
    def __init__(self, side):
        super().__init__(side, side)
    
    def set_width(self, width):
        self.width = width
        self.height = width  # Нарушение LSP!
    
    def set_height(self, height):
        self.height = height
        self.width = height  # Нарушение LSP!
```
### Проблемы этого кода:

1. **Нарушение инвариантов:** В Rectangle ширина и высота независимы, но в Square они всегда равны
2. **Неожиданное поведение:** Изменение ширины квадрата автоматически изменяет высоту
3. **Нарушение принципа подстановки:** Код, ожидающий Rectangle, может работать некорректно с Square

Давайте посмотрим, как это проявляется на практике:
```python
def process_rectangle(rect):
    """Функция, которая работает с прямоугольником"""
    rect.set_width(5)
    rect.set_height(10)
    print(f"Expected area: 50, Actual area: {rect.area()}")

# С обычным прямоугольником работает корректно
rect = Rectangle(2, 3)
process_rectangle(rect)  # Вывод: Expected area: 50, Actual area: 50

# С квадратом работает некорректно
square = Square(2)
process_rectangle(square)  # Вывод: Expected area: 50, Actual area: 100
```
### Анализ нарушения LSP

1. **Нарушение инвариантов**
    
    - Rectangle: width и height независимы
    - Square: width и height всегда равны
    - Square нарушает инвариант Rectangle
2. **Изменение постусловий**
    
    - Rectangle.set_width() изменяет только width
    - Square.set_width() изменяет и width, и height
    - Постусловия метода изменены
3. **Неправильное использование наследования**
    
    - Square не является "видом" Rectangle в поведенческом смысле
    - Square не может заменить Rectangle без изменения поведения
    - Наследование используется неправильно

## Решение проблемы LSP

Есть несколько способов решения этой проблемы:

### Вариант 1: Отказ от наследования
```python
class Rectangle:
    def __init__(self, width, height):
        self.width = width
        self.height = height
    
    def set_width(self, width):
        self.width = width
    
    def set_height(self, height):
        self.height = height
    
    def area(self):
        return self.width * self.height

class Square:
    def __init__(self, side):
        self.side = side
    
    def set_side(self, side):
        self.side = side
    
    def area(self):
        return self.side * self.side
```
**Преимущества:**

- Каждая фигура имеет свое собственное поведение
- Нет нарушения LSP
- Код становится более понятным

**Недостатки:**

- Нет общего интерфейса для работы с фигурами
- Дублирование кода (метод area())
- Трудно создать полиморфный код

### Вариант 2: Использование абстрактного базового класса
```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height
    
    def set_width(self, width):
        self.width = width
    
    def set_height(self, height):
        self.height = height
    
    def area(self):
        return self.width * self.height

class Square(Shape):
    def __init__(self, side):
        self.side = side
    
    def set_side(self, side):
        self.side = side
    
    def area(self):
        return self.side * self.side
```
**Преимущества:**

- Есть общий интерфейс для работы с фигурами
- Нет нарушения LSP
- Можно создавать полиморфный код

**Недостатки:**

- Rectangle и Square имеют разные интерфейсы (set_width/set_height vs set_side)
- Нет общего способа изменения размеров

### Вариант 3: Использование неизменяемых объектов
```python
class Rectangle:
    def __init__(self, width, height):
        self._width = width
        self._height = height
    
    @property
    def width(self):
        return self._width
    
    @property
    def height(self):
        return self._height
    
    def area(self):
        return self.width * self.height

class Square(Rectangle):
    def __init__(self, side):
        super().__init__(side, side)
    
    @property
    def side(self):
        return self.width
```
**Преимущества:**

- Объекты неизменяемы, что предотвращает нарушение инвариантов
- Square является подтипом Rectangle
- Нет нарушения LSP

**Недостатки:**

- Ограниченная гибкость (нельзя изменить размеры)
- Не подходит для всех сценариев

### Вариант 4: Использование композиции
```python
class Rectangle:
    def __init__(self, width, height):
        self.width = width
        self.height = height
    
    def set_width(self, width):
        self.width = width
    
    def set_height(self, height):
        self.height = height
    
    def area(self):
        return self.width * self.height

class Square:
    def __init__(self, side):
        self._rectangle = Rectangle(side, side)
    
    @property
    def side(self):
        return self._rectangle.width
    
    @side.setter
    def side(self, value):
        self._rectangle.width = value
        self._rectangle.height = value
    
    def area(self):
        return self._rectangle.area()
```
**Преимущества:**

- Square использует Rectangle как внутреннюю реализацию
- Нет нарушения LSP
- Сохраняется инкапсуляция

**Недостатки:**

- Square не является подтипом Rectangle
- Нет общего интерфейса для полиморфной работы

## Еще один пример: Иерархия банковских счетов

Рассмотрим пример с банковскими счетами:
```python
class BankAccount:
    def __init__(self, balance):
        self.balance = balance
    
    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("Amount must be positive")
        self.balance += amount
    
    def withdraw(self, amount):
        if amount <= 0:
            raise ValueError("Amount must be positive")
        if amount > self.balance:
            raise ValueError("Insufficient funds")
        self.balance -= amount

class FixedTermDepositAccount(BankAccount):
    """Срочный вклад - нельзя снимать деньги до окончания срока"""
    
    def __init__(self, balance, term_months):
        super().__init__(balance)
        self.term_months = term_months
        self.months_passed = 0
    
    def withdraw(self, amount):
        # Нарушение LSP: изменяем поведение базового метода
        if self.months_passed < self.term_months:
            raise ValueError("Cannot withdraw before term ends")
        super().withdraw(amount)
    
    def pass_month(self):
        self.months_passed += 1
```
### Проблемы:

1. Метод withdraw() в подклассе имеет дополнительные условия
2. Код, ожидающий обычный BankAccount, может сломаться с FixedTermDepositAccount
3. Нарушение принципа подстановки

### Решение для банковских счетов
```python
from abc import ABC, abstractmethod

class Account(ABC):
    def __init__(self, balance):
        self.balance = balance
    
    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("Amount must be positive")
        self.balance += amount
    
    @abstractmethod
    def withdraw(self, amount):
        pass

class CheckingAccount(Account):
    """Текущий счет - можно снимать деньги в любое время"""
    
    def withdraw(self, amount):
        if amount <= 0:
            raise ValueError("Amount must be positive")
        if amount > self.balance:
            raise ValueError("Insufficient funds")
        self.balance -= amount

class FixedTermDepositAccount(Account):
    """Срочный вклад - нельзя снимать деньги до окончания срока"""
    
    def __init__(self, balance, term_months):
        super().__init__(balance)
        self.term_months = term_months
        self.months_passed = 0
    
    def withdraw(self, amount):
        # Этот метод не нарушает LSP, так как базовый класс абстрактный
        if self.months_passed < self.term_months:
            raise ValueError("Cannot withdraw before term ends")
        if amount <= 0:
            raise ValueError("Amount must be positive")
        if amount > self.balance:
            raise ValueError("Insufficient funds")
        self.balance -= amount
    
    def pass_month(self):
        self.months_passed += 1
```
**Преимущества этого подхода:**

1. Базовый класс абстрактный, поэтому нет нарушения LSP
2. Каждый подкласс реализует withdraw() согласно своей логике
3. Клиенты работают с конкретными типами счетов
4. Четкое разделение ответственности

## Практические примеры нарушений LSP

### 1. Подкласс с пустыми реализациями
```python
# Плохо: нарушение LSP
class Bird:
    def fly(self):
        print("Flying")
    
    def make_sound(self):
        print("Chirp")

class Penguin(Bird):
    def fly(self):
        # Пингвины не летают - нарушение LSP
        pass
    
    def make_sound(self):
        print("Honk")
```
**Проблемы:**

- Penguin не может заменить Bird без изменения поведения
- Метод fly() имеет пустую реализацию
- Нарушение принципа подстановки

**Решение:**
```python
# Хорошо: соблюдение LSP
from abc import ABC, abstractmethod

class Bird(ABC):
    @abstractmethod
    def make_sound(self):
        pass

class FlyingBird(Bird):
    @abstractmethod
    def fly(self):
        pass

class Sparrow(FlyingBird):
    def fly(self):
        print("Flying")
    
    def make_sound(self):
        print("Chirp")

class Penguin(Bird):
    def make_sound(self):
        print("Honk")
```
### 2. Подкласс, выбрасывающий новые исключения
```python
# Плохо: нарушение LSP
class DatabaseConnection:
    def connect(self):
        pass
    
    def execute_query(self, query):
        pass

class NoSQLDatabaseConnection(DatabaseConnection):
    def execute_query(self, query):
        # NoSQL базы данных могут не поддерживать SQL запросы
        raise NotImplementedError("NoSQL databases don't support SQL queries")
```
**Проблемы:**

- NoSQLDatabaseConnection не может заменить DatabaseConnection
- Выбрасывает исключение, которое не выбрасывает базовый класс
- Нарушение контракта

**Решение:**
```python
# Хорошо: соблюдение LSP
from abc import ABC, abstractmethod

class DataConnection(ABC):
    @abstractmethod
    def connect(self):
        pass
    
    @abstractmethod
    def execute(self, operation):
        pass

class SQLDatabaseConnection(DataConnection):
    def connect(self):
        print("Connecting to SQL database")
    
    def execute(self, operation):
        print(f"Executing SQL query: {operation}")

class NoSQLDatabaseConnection(DataConnection):
    def connect(self):
        print("Connecting to NoSQL database")
    
    def execute(self, operation):
        print(f"Executing NoSQL operation: {operation}")
```
### 3. Подкласс, изменяющий тип возвращаемого значения
```python
# Плохо: нарушение LSP
class DataProcessor:
    def process(self, data):
        return str(data)

class NumberProcessor(DataProcessor):
    def process(self, data):
        return int(data)  # Другой тип!
```
**Проблемы:**

- NumberProcessor возвращает другой тип, чем DataProcessor
- Клиентский код может ожидать строку и получить число
- Нарушение постусловий

**Решение:**
```python
# Хорошо: соблюдение LSP
from abc import ABC, abstractmethod

class DataProcessor(ABC):
    @abstractmethod
    def process(self, data):
        pass

class StringProcessor(DataProcessor):
    def process(self, data):
        return str(data)

class NumberProcessor(DataProcessor):
    def process(self, data):
        # Приводим к строке для соответствия контракту
        return str(int(data))
```
## Практические советы по применению LSP

### 1. Следите за контрактом методов

**Предусловия не должны усиливаться в подклассах**

- Подклассы должны принимать как минимум те же входные данные, что и базовый класс
- Не добавляйте дополнительные ограничения на входные параметры

**Постусловия не должны ослабляться в подклассах**

- Подклассы должны возвращать как минимум тот же результат, что и базовый класс
- Не уменьшайте гарантии выходных данных

**Инварианты должны сохраняться**

- Все условия, которые истинны для базового класса, должны оставаться истинными для подкласса
- Не нарушайте ожидания относительно состояния объекта

### 2. Избегайте проверок типов
```python
# Плохо
def process_shape(shape):
    if isinstance(shape, Circle):
        # обработка круга
    elif isinstance(shape, Rectangle):
        # обработка прямоугольника
```
Вместо этого используйте полиморфизм:
```python
# Хорошо
def process_shape(shape):
    shape.process()  # Полиморфный вызов
```
### 3. Тестируйте подстановку

Пишите тесты, которые проверяют, что подкласс может заменить базовый класс:
```python
def test_rectangle_substitution():
    # Тест, который работает с любым прямоугольником
    def test_rectangle_behavior(rect):
        rect.set_width(5)
        rect.set_height(10)
        assert rect.area() == 50
    
    # Должен работать с обычным прямоугольником
    rect = Rectangle(2, 3)
    test_rectangle_behavior(rect)
    
    # Должен работать с квадратом, если он является подтипом
    square = Square(2)
    test_rectangle_behavior(square)  # Этот тест провалится!
```
### 4. Используйте абстрактные классы

Абстрактные базовые классы помогают определить четкие контракты:
```python
from abc import ABC, abstractmethod

class PaymentProcessor(ABC):
    @abstractmethod
    def process_payment(self, amount):
        pass
    
    @abstractmethod
    def refund_payment(self, amount):
        pass

class CreditCardProcessor(PaymentProcessor):
    def process_payment(self, amount):
        # Реализация
        pass
    
    def refund_payment(self, amount):
        # Реализация
        pass
```
### 5. Проектируйте иерархии внимательно

Не все отношения "является" подходят для наследования. Задавайте вопросы:

- Действительно ли подкласс является "видом" базового класса?
- Сохраняет ли подкласс все инварианты базового класса?
- Может ли подкласс безопасно заменить базовый класс?

## Распространенные ошибки при применении LSP

### 1. Неправильное понимание наследования

**Наследование - это не просто переиспользование кода**

- Наследование означает "является" (is-a) в поведенческом смысле
- Подкласс должен вести себя как базовый класс
- Не используйте наследование только для переиспользования кода
```python
# Плохо: наследование для переиспользования кода
class LinkedList:
    def __init__(self):
        self.head = None
    
    def append(self, value):
        # Реализация добавления
        pass

class Stack(LinkedList):
    def push(self, value):
        self.append(value)
    
    def pop(self):
        # Реализация извлечения
        pass
```
Stack не является "видом" LinkedList в поведенческом смысле.

### 2. Слишком общие базовые классы
```python
# Плохо
class Processor:
    def process(self, data):
        pass

class ImageProcessor(Processor):
    def process(self, image_data):
        if not isinstance(image_data, Image):
            raise TypeError("Expected Image data")
```
Базовый класс слишком общий и не определяет четкий контракт.

### 3. Игнорирование бизнес-логики

Иногда подклассы должны иметь разное поведение, но это не всегда нарушение LSP:
```python
# Хорошо: различное поведение, но соответствие контракту
class Employee:
    def calculate_salary(self):
        pass

class HourlyEmployee(Employee):
    def calculate_salary(self):
        # Расчет почасовой оплаты
        pass

class SalariedEmployee(Employee):
    def calculate_salary(self):
        # Расчет оклада
        pass
```
Это не нарушение LSP, так как оба метода возвращают зарплату, соответствуя контракту.

### 4. Чрезмерное использование абстрактных классов
```python
# Плохо: излишняя абстракция
class Animal(ABC):
    @abstractmethod
    def make_sound(self):
        pass
    
    @abstractmethod
    def move(self):
        pass
    
    @abstractmethod
    def eat(self):
        pass
    
    @abstractmethod
    def sleep(self):
        pass
```
Не все животные могут иметь все эти методы. Лучше использовать композицию или более мелкие интерфейсы.

## Упражнения для практики

### Упражнение 1: Анализ иерархии классов

Проанализируйте следующую иерархию классов и определите, нарушает ли она LSP:
```python
class Vehicle:
    def __init__(self, fuel_capacity):
        self.fuel_capacity = fuel_capacity
        self.fuel_level = 0
    
    def refuel(self, amount):
        if amount <= 0:
            raise ValueError("Amount must be positive")
        self.fuel_level = min(self.fuel_level + amount, self.fuel_capacity)
    
    def start_engine(self):
        if self.fuel_level <= 0:
            raise ValueError("No fuel")
        print("Engine started")

class ElectricVehicle(Vehicle):
    def __init__(self, battery_capacity):
        super().__init__(battery_capacity)
        self.battery_level = 0
    
    def refuel(self, amount):
        # Электромобили не заправляются бензином
        raise NotImplementedError("Electric vehicles don't use fuel")
    
    def charge(self, amount):
        if amount <= 0:
            raise ValueError("Amount must be positive")
        self.battery_level = min(self.battery_level + amount, self.fuel_capacity)
    
    def start_engine(self):
        if self.battery_level <= 0:
            raise ValueError("No battery charge")
        print("Electric engine started")
```
**Задание:**

1. Определите нарушения LSP в этой иерархии
2. Объясните, почему эти нарушения происходят
3. Предложите рефакторинг для устранения нарушений
4. Покажите, как будет выглядеть исправленный код

### Упражнение 2: Создание LSP-совместимой иерархии

Создайте иерархию классов для различных типов работников в компании:

- Employee (базовый класс)
- Manager
- Developer
- Intern

Каждый работник должен иметь метод work(), но разные типы работников работают по-разному. Убедитесь, что иерархия соответствует LSP.

**Требования:**

1. Определите общий интерфейс для всех работников
2. Реализуйте каждый тип работника
3. Убедитесь, что подклассы могут безопасно заменять базовый класс
4. Напишите тесты для проверки соответствия LSP

### Упражнение 3: Рефакторинг нарушений LSP

Дан код с нарушениями LSP:
```python
class Database:
    def __init__(self, connection_string):
        self.connection_string = connection_string
    
    def connect(self):
        print(f"Connecting to {self.connection_string}")
    
    def execute(self, query):
        print(f"Executing query: {query}")
        return f"Result of {query}"
    
    def disconnect(self):
        print("Disconnecting from database")

class NoSQLDatabase(Database):
    def __init__(self, connection_string):
        super().__init__(connection_string)
        self.is_connected = False
    
    def connect(self):
        print(f"Connecting to NoSQL {self.connection_string}")
        self.is_connected = True
    
    def execute(self, query):
        if not self.is_connected:
            raise ConnectionError("Not connected to database")
        print(f"Executing NoSQL operation: {query}")
        return f"NoSQL result of {query}"
    
    def disconnect(self):
        print("Disconnecting from NoSQL database")
        self.is_connected = False
```
**Задание:**

1. Определите нарушения LSP в этом коде
2. Объясните, почему эти нарушения problematic
3. Предложите и реализуйте рефакторинг
4. Напишите тесты для проверки соответствия LSP после рефакторинга

## Заключение по LSP

Принцип подстановки Лисков - это критически важный принцип для создания надежных и предсказуемых иерархий классов. Он гарантирует, что полиморфизм работает корректно и что подклассы действительно являются "видами" своих базовых классов.

### Ключевые моменты, которые нужно запомнить:

1. **Подклассы должны быть заменяемы на свои базовые классы без изменения корректности программы**
    
    - Сохраняйте инварианты, предусловия и постусловия
    - Избегайте проверок типов и пустых реализаций
    - Используйте абстрактные классы для определения четких контрактов
2. **LSP требует тщательного проектирования иерархий**
    
    - Не все отношения "является" подходят для наследования
    - Иногда лучше использовать композицию вместо наследования
    - Проектируйте контракты между классами внимательно
3. **Наследование - это поведенческое отношение**
    
    - Подкласс должен вести себя как базовый класс
    - Не используйте наследование только для переиспользования кода
    - Убедитесь, что подклассы соответствуют ожиданиям пользователей
4. **Тестирование - ключ к соблюдению LSP**
    
    - Пишите тесты, которые проверяют подстановку
    - Используйте одни и те же тесты для базовых классов и подклассов
    - Тестируйте инварианты, предусловия и постусловия

### Практические шаги для применения LSP:

1. **Анализируйте существующие иерархии**
    
    - Проверяйте, могут ли подклассы безопасно заменять базовые классы
    - Ищите проверки типов и пустые реализации
    - Убедитесь, что инварианты сохраняются
2. **Проектируйте новые иерархии с учетом LSP**
    
    - Определяйте четкие контракты между классами
    - Используйте абстрактные классы для интерфейсов
    - Тщательно продумывайте, какие классы должны наследовать друг от друга
3. **Тестируйте соответствие LSP**
    
    - Пишите полиморфные тесты
    - Проверяйте, что подклассы проходят те же тесты, что и базовые классы
    - Убедитесь, что замена подклассов не ломает код
4. **Используйте композицию, когда наследование не подходит**
    
    - Не все отношения "является" должны реализовываться через наследование
    - Композиция часто обеспечивает большую гибкость
    - Используйте паттерны проектирования для сложных отношений

В [следующей главе](05-isp.md) мы изучим Принцип разделения интерфейса (ISP), который помогает создавать мелкозернистые и сфокусированные интерфейсы.