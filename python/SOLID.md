Принципы проектирования программного обеспечения — это набор рекомендаций и лучших практик, которые помогают программистам создавать высококачественный, удобный в обслуживании и расширяемый код. Вот несколько ключевых принципов:

1. **Принцип единственной ответственности (Single Responsibility Principle, SRP)**: Каждый класс или модуль должен иметь только одну причину для изменения. Это означает, что класс должен выполнять только одну задачу или иметь только одну область ответственности.

2. **Принцип открытости/закрытости (Open/Closed Principle, OCP)**: Программные сущности должны быть открыты для расширения, но закрыты для изменения. Это означает, что можно добавлять новые функциональные возможности без изменения существующего кода.
   
3. **Принцип подстановки Барбары Лисков (Liskov Substitution Principle, LSP)**: Объекты в программе можно заменять их подтипами без изменения правильности выполнения программы. Это принцип обеспечивает совместимость интерфейсов при наследовании.
   
4. **Принцип разделения интерфейса (Interface Segregation Principle, ISP)**: Не заставляйте клиентов зависеть от методов, которые они не используют. Это означает создание специализированных интерфейсов вместо одного универсального.
   
5. **Принцип инверсии зависимостей (Dependency Inversion Principle, DIP)**: Модули высокого уровня не должны зависеть от модулей низкого уровня. Обе стороны должны зависеть от абстракций, а не от конкретики.   

Эти принципы часто используются в рамках объектно-ориентированного программирования, но могут быть применимы и в других парадигмах. Применение этих принципов помогает создавать более гибкое, поддерживаемое и масштабируемое программное обеспечение.

## Примеры кода:

1. **Принцип единственной ответственности (SRP)**

 В этом примере у нас есть класс `Report`, который отвечает только за хранение информации о отчете, и класс `ReportPrinter`, который отвечает за печать отчета.

```python
class Report:
    def __init__(self, content):
        self.content = content

class ReportPrinter:
    def print_report(self, report):
        print(report.content)
```

2. **Принцип открытости/закрытости (OCP)**

Здесь класс `Shape` является базовым классом, а `Circle` и `Rectangle` расширяют его. Мы можем добавить новые формы, не изменяя существующий код.

```python
class Shape:
    def draw(self):
        pass

class Circle(Shape):
    def draw(self):
        print("Drawing a circle")

class Rectangle(Shape):
    def draw(self):
        print("Drawing a rectangle")
```

3. **Принцип подстановки Барбары Лисков (LSP)**

Подкласс `Square` может заменить `Rectangle` без влияния на правильность программы.

```python
class Rectangle:
    def set_width(self, width):
        self.width = width

    def set_height(self, height):
        self.height = height

class Square(Rectangle):
    def set_width(self, width):
        self.width = self.height = width

    def set_height(self, height):
        self.width = self.height = height
```

4. **Принцип разделения интерфейса (ISP)**

Интерфейсы `Printer` и `Scanner` разделены, так что классы могут реализовывать только те, которые им нужны.

```python
class Printer:
    def print_document(self, document):
        pass

class Scanner:
    def scan_document(self, document):
        pass

class Photocopier(Printer, Scanner):
    def print_document(self, document):
        print("Print", document)

    def scan_document(self, document):
        print("Scan", document)
```

5. **Принцип инверсии зависимостей (DIP)**

Здесь классы `Button` и `Lamp` зависят от абстракции (`Switchable`), а не от конкретных реализаций.

```python
class Switchable:
    def turn_on(self):
        pass

    def turn_off(self):
        pass

class Lamp(Switchable):
    def turn_on(self):
        print("Lamp turned on")

    def turn_off(self):
        print("Lamp turned off")

class Button:
    def __init__(self, lamp):
        self.lamp = lamp

    def press(self):
        if is_on:
            self.lamp.turn_off()
        else:
            self.lamp.turn_on()
```

