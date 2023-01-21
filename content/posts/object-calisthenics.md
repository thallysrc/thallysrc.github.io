---
title: "Objects Calisthenics em python"
# description: "sadasdasd"
date: 2023-01-21T11:30:03+00:00

# weight: 1
# showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false

disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
appendFilePath: true # to append file path to Edit link
---


Eu decidi começar a escrever posts para ajudar outros desenvolvedores a evoluir na carreira, começando por Object Calisthenics em python. Ele foi introduzido por Jeff Bay no seu livro "The ThoughtWorks Anthology", embora muitos desenvolvedores conheçam essas práticas ou já aplicam ser conhecer por esse nome.

Alguns pontos sobre o post:

1. Object Calisthenics seria uma maneira de exercitar os princípios SOLID, pois basicamente, todas as regras levam aos mesmos princípios de SOLID, mas de uma forma diferente.
2. Eu escolhi somente 5 princípios porque eles são os mais relevantes para serem exemplificados com código, embora existam outros.
3. A ideia desse post é ser um conteudo bem resumido

Então, vamos aos princípios:

## **Um nível de identação por método**

Esse princípio é bem claro e direto. O propósito dele, que eu vejo como algo útil, é que ele te força a manter métodos menores, fazendo somente uma coisa, respeitando também o princípio de responsabilidade única, e também evita uma complexidade cognitiva alta no código.

```python
# incorrect
class Terminal:

    @staticmethod
    def show_matrix():
        for i in range(10):
            print(i)
            for j in range(10):
                print(j)

# correct
class Terminal:

    def show_matrix(self):
        return self.print_rows()

    def print_rows(self):
        for i in range(10):
            print(i)
            self.print_row()

    @staticmethod
    def print_row():
        for i in range(10):
            print(i)
```

## **Não use ELSE**

Usar `else` na maioria dos casos pode ser substituído por um “early return” ou também conhecido como “handle exception first”. A prática de evitar o uso de `else` pode tornar o código mais fácil de ler e, na maioria das vezes, não é necessário utilizar `else`.

Por exemplo:

```python
# incorrect
if response:
    return response
else:
    return "deu problema"

# correct
if not response:
    return "deu problema"
return response
```

Pode parecer que não faz muita diferença, mas imagine isso em um código grande, cheio de if else um dentro do outro. Dessa forma, evitando o uso de else, você também exerce o princípio "Um nível de indentação por método.”

## **Encapsule os tipos primitivos**

Esse princípio contém poucos exemplos na internet. Mas esse princípio é sobre não ter tipos primitivos soltos no código(int, str, float..), especialmente se eles têm algum comportamento.

Exemplo sem encapsular

```python
@dataclass
class Person:
    name: str
    zip_code: int

    def validate_zip_code(self):
        if not self.zip_code:
            print("invalid")

@dataclass
class Supplier:
    company: str
    zip_code: int

    def validate_zip_code(self):
        if not self.zip_code:
            print("invalid")
```

Exemplo encapsulando:

```python
@dataclass
class ZipCode:
    code: int

    def __init__(self, code: int):
        self.validate(code)
        self.code = code

    @staticmethod
    def validate(code):
        if not code:
            print("invalid")

@dataclass
class Person:
    name: str
    zip_code: ZipCode

@dataclass
class Supplier:
    name: str
    zip_code: ZipCode

supplier = Supplier("Jaskier codes", ZipCode(10))
person = Person("Dudu", ZipCode(None))
```

Dessa forma, o comportamento do `zip_code` fica restrito à classe ZipCode, evitando a necessidade de duplicar código em dois locais diferentes, como no exemplo mencionado anteriormente.

## Usar First Class Collection

A definição de "first class collection" seria que qualquer classe que contém uma collection não deve ter nenhuma outra variável nela. E cada collection deve ser encapsulada em sua própria classe, assim somente ela deve alterar seu comportamento, como adicionar métodos de filtragem, deleção, etc.

```python
from dataclasses import dataclass
from typing import List

@dataclass
class Payment:
    mode: int
    value: int

class PaymentCollection:
    def __init__(self):
        self._payments: List[Payment] = []

    def add(self, payment: Payment) -> None:
        if payment.mode == 3:
            raise Exception("Not supportted payment mode")

        self._payments.append(payment)

    def get_all(self) -> List[Payment]:
        return self._payments

    def total(self) -> int:
        return sum([payment.value for payment in self._payments])

class Bill:
    def __init__(self, collection):
        self.value = 100
        self._payment_collection: PaymentCollection = collection

    def add_payment(self, payment) -> None:
        self._payment_collection.add(payment)

    def total_payment(self) -> int:
        return self._payment_collection.total()

    def get_all_payments(self) -> List[Payment]:
        return self._payment_collection.get_all()

bill = Bill(collection=PaymentCollection())
```

## Um ponto por linha

Esse princípio serve basicamente para garantir que um objeto não tenha interação direta com um objeto de outro objeto. No entanto, isso não se aplica para `fluent interfaces` e `method chaining`

```python
from dataclasses import dataclass

@dataclass
class Stock:
    location: str
    quantity: int

@dataclass
class Product:
    stock: Stock

    def formatted_quantity(self):
        return f"{self.stock.quantity} N"

class Shelf:

    @staticmethod
    def show_stock():
        product = Product(stock=Stock(location="Joinville", quantity=3))

        # correct
        print(product.formatted_quantity())

        # incorrect
        print(f"{product.stock.quantity} N")

shelf = Shelf()

shelf.show_stock()
```

## Conclusão

Esses princípios são bons de conhecer, o princípio "first class collection" talvez seja o mais raro de todos de ser visto em uma aplicação, embora só de aplicar esses outros você verá uma melhoria significativa na qualidade do código. Se você começar a implementar, verá que tudo é sobre "clean code." Se tiver alguma dúvida, estou à disposição e também aberto a sugestões. Valeu!

### Link do repositorio com o codigo

[https://github.com/thallysrc/talks/tree/main/object-calisthenics](https://github.com/thallysrc/talks/tree/main/object-calisthenics)
