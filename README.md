# clean-code-python

[![Build Status](https://travis-ci.com/zedr/clean-code-python.svg?branch=master)](https://travis-ci.com/zedr/clean-code-python)
[![](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/download/releases/3.8.3/)

Di-fork dari [clean-code-python](https://github.com/zedr/clean-code-python), dengan referensi penerjemahan dari [clean-code-javascript](https://github.com/andirkh/clean-code-javascript).

## Table of Contents
  1. [Kata Pengantar](#kata-pengantar)
  2. [Variabel](#variabel)
  3. [Fungsi](#fungsi)
  4. [Objects and Data Structures](#objects-and-data-structures)
  5. [Classes](#classes)
     1. [S: Single Responsibility Principle (SRP)](#single-responsibility-principle-srp)
     2. [O: Open/Closed Principle (OCP)](#openclosed-principle-ocp)
     3. [L: Liskov Substitution Principle (LSP)](#liskov-substitution-principle-lsp)
     4. [I: Interface Segregation Principle (ISP)](#interface-segregation-principle-isp)
     5. [D: Dependency Inversion Principle (DIP)](#dependency-inversion-principle-dip)
  6. [Don"t repeat yourself (DRY)](#dont-repeat-yourself-dry)

## Kata Pengantar
![Gambar lucu dari estimasi kualitas perangkat lunak sebagai hitungan berapa
banyak umpatan yang keluar saat membaca kode](http://www.osnews.com/images/comics/wtfm.jpg)


Prinsip Rekayasa Perangkat Lunak, oleh Robert C. Buku
[*Clean Code*](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882) oleh Martin,
diadaptasi untuk Python. Perlu diingat bahwa panduan ini bukan tentang gaya
penulisan kode. Panduan ini lebih tentang membuat perangkat lunak yg [mudah dibaca,
dapat digunakan kembali, dan mudah direfaktor kembali](https://github.com/ryanmcdermott/3rs-of-software-architecture)
dalam bahasa Python.


Tidak semua prinsip disini harus diikuti secara ketat, dan bahkan lebih sedikit
akan lebih mudah disepakati bersama. Ini hanyalah panduan-panduan saja dan tidak
lebih, tetapi panduan-panduan ini dikodifikiasi selama bertahun-tahun dari
pengalaman kolektif penulis buku *Clean Code*.

Terinspirasi dari [clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript)

Untuk Python3.7+

## **Variabel**
### Gunakan nama-nama variabel yang bermakna dan mudah diucapkan

**Buruk:**
```python
import datetime


ymdstr = datetime.date.today().strftime("%y-%m-%d")
```

**Baik**:
```python
import datetime


current_date: str = datetime.date.today().strftime("%y-%m-%d")
```
**[⬆ kembali ke atas](#table-of-contents)**

### Gunakan kosakata yang sama untuk jenis variabel yang sama

**Buruk:**
Di sini kita menggunakan tiga nama berbeda untuk entitas dasar yang sama:
```python
def get_user_info(): pass
def get_client_data(): pass
def get_customer_record(): pass
```

**Baik**:
Jika entitasnya sama, Anda harus menggunakan rujukan yang konsisten dalam fungsi Anda:
```python
def get_user_info(): pass
def get_user_data(): pass
def get_user_record(): pass
```

**Lebih Baik:**
Python is (also) an object oriented programming language. If it makes sense, package the functions together with the concrete implementation
of the entity in your code, as instance attributes, property methods, or methods:

```python
from typing import Union, Dict, Text


class Record:
    pass


class User:
    info : str

    @property
    def data(self) -> Dict[Text, Text]:
        return {}

    def get_record(self) -> Union[Record, None]:
        return Record()
        
```

**[⬆ back to top](#table-of-contents)**

### Gunakan nama yg mudah dicari kembali
Kita akan lebih banyak membaca kode daripada menulisnya. Maka penting hukumnya
menulis kode untuk mudah dibaca dan mudah dicari. Dengan *tidak* memberi nama
variabel yang bermakna untuk memahami program kita, kita menyakiti pembaca kode
kita. Buat nama variabel lebih mudah dicari.

**Buruk:**
```python
import time


# Angka 86400 itu untuk apa?
time.sleep(86400)
```

**Baik**:
```python
import time


# Declare them in the global namespace for the module.
SECONDS_IN_A_DAY = 60 * 60 * 24
time.sleep(SECONDS_IN_A_DAY)
```
**[⬆ back to top](#table-of-contents)**

### Gunakan variabel penjelas
**Buruk:**
```python
import re


address = "One Infinite Loop, Cupertino 95014"
city_zip_code_regex = r"^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$"

matches = re.match(city_zip_code_regex, address)
if matches:
    print(f"{matches[1]}: {matches[2]}")
```

**Tidak buruk**:

Ini lebih baik, tapi kita masih sangat bergantung pada regex.

```python
import re


address = "One Infinite Loop, Cupertino 95014"
city_zip_code_regex = r"^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$"
matches = re.match(city_zip_code_regex, address)

if matches:
    city, zip_code = matches.groups()
    print(f"{city}: {zip_code}")
```

**Baik**:

Kurangi ketergantungan pada regex dengan memberi nama pada subpola.
```python
import re


address = "One Infinite Loop, Cupertino 95014"
city_zip_code_regex = r"^[^,\\]+[,\\\s]+(?P<city>.+?)\s*(?P<zip_code>\d{5})?$"

matches = re.match(city_zip_code_regex, address)
if matches:
    print(f"{matches['city']}, {matches['zip_code']}")
```
**[⬆ back to top](#table-of-contents)**

### Hindari Mental Mapping
Jangan paksa pembaca kode Anda untuk menerjemahkan arti dari sebuah variabel.
Eksplisit lebih baik daripada implisit.

**Buruk:**
```python
seq = ("Austin", "New York", "San Francisco")

for item in seq:
    #do_stuff()
    #do_some_other_stuff()

    # Sebentar, `item` itu untuk apa tadi?
    print(item)
```

**Baik**:
```python
locations = ("Austin", "New York", "San Francisco")

for location in locations:
    #do_stuff()
    #do_some_other_stuff()
    # ...
    print(location)
```
**[⬆ back to top](#table-of-contents)**


### Jangan menambahkan konteks yang tidak dibutuhkan

Jika nama class/objek kamu memiliki arti tertentu, jangan mengulanginya di dalam
nama variabel.

**Buruk:**

```python
class Car:
    car_make: str
    car_model: str
    car_color: str
```

**Baik**:

```python
class Car:
    make: str
    model: str
    color: str
```

**[⬆ back to top](#table-of-contents)**

### Gunakan argumen default daripada `short circuiting` dan `kondisional`

**Rumit**

Kenapa menulis:

```python
import hashlib


def create_micro_brewery(name):
    name = "Hipster Brew Co." if name is None else name
    slug = hashlib.sha1(name.encode()).hexdigest()
    # etc.
```

... padahal Anda bisa menentukan argumen default? Ini juga menjelaskan bahwa Anda mengharapkan string sebagai nilai argumennya.

**Baik**:

```python
from typing import Text
import hashlib


def create_micro_brewery(name: Text = "Hipster Brew Co."):
    slug = hashlib.sha1(name.encode()).hexdigest()
    # etc.
```

**[⬆ back to top](#table-of-contents)**
## **Fungsi**
### Function arguments (2 or fewer ideally)
Limiting the amount of function parameters is incredibly important because it makes 
testing your function easier. Having more than three leads to a combinatorial explosion 
where you have to test tons of different cases with each separate argument.

Zero arguments is the ideal case. One or two arguments is ok, and three should be avoided. 
Anything more than that should be consolidated. Usually, if you have more than two 
arguments then your function is trying to do too much. In cases where it"s not, most 
of the time a higher-level object will suffice as an argument.

**Bad:**
```python
def create_menu(title, body, button_text, cancellable):
    pass
```

**Java-esque**:
```python
class Menu:
    def __init__(self, config: dict):
        self.title = config["title"]
        self.body = config["body"]
        # ...

menu = Menu(
    {
        "title": "My Menu",
        "body": "Something about my menu",
        "button_text": "OK",
        "cancellable": False
    }
)
```

**Also good**
```python
from typing import Text


class MenuConfig:
    """A configuration for the Menu.

    Attributes:
        title: The title of the Menu.
        body: The body of the Menu.
        button_text: The text for the button label.
        cancellable: Can it be cancelled?
    """
    title: Text
    body: Text
    button_text: Text
    cancellable: bool = False


def create_menu(config: MenuConfig) -> None:
    title = config.title
    body = config.body
    # ...


config = MenuConfig()
config.title = "My delicious menu"
config.body = "A description of the various items on the menu"
config.button_text = "Order now!"
# The instance attribute overrides the default class attribute.
config.cancellable = True

create_menu(config)
```

**Fancy**
```python
from typing import NamedTuple


class MenuConfig(NamedTuple):
    """A configuration for the Menu.

    Attributes:
        title: The title of the Menu.
        body: The body of the Menu.
        button_text: The text for the button label.
        cancellable: Can it be cancelled?
    """
    title: str
    body: str
    button_text: str
    cancellable: bool = False


def create_menu(config: MenuConfig):
    title, body, button_text, cancellable = config
    # ...


create_menu(
    MenuConfig(
        title="My delicious menu",
        body="A description of the various items on the menu",
        button_text="Order now!"
    )
)
```

**Even fancier**
```python
from typing import Text
from dataclasses import astuple, dataclass


@dataclass
class MenuConfig:
    """A configuration for the Menu.

    Attributes:
        title: The title of the Menu.
        body: The body of the Menu.
        button_text: The text for the button label.
        cancellable: Can it be cancelled?
    """
    title: Text
    body: Text
    button_text: Text
    cancellable: bool = False

def create_menu(config: MenuConfig):
    title, body, button_text, cancellable = astuple(config)
    # ...


create_menu(
    MenuConfig(
        title="My delicious menu",
        body="A description of the various items on the menu",
        button_text="Order now!"
    )
)
```

**Even fancier, Python3.8+ only**
```python
from typing import TypedDict, Text


class MenuConfig(TypedDict):
    """A configuration for the Menu.

    Attributes:
        title: The title of the Menu.
        body: The body of the Menu.
        button_text: The text for the button label.
        cancellable: Can it be cancelled?
    """
    title: Text
    body: Text
    button_text: Text
    cancellable: bool


def create_menu(config: MenuConfig):
    title = config["title"]
    # ...


create_menu(
    # You need to supply all the parameters
    MenuConfig(
        title="My delicious menu", 
        body="A description of the various items on the menu",
        button_text="Order now!",
        cancellable=True
    )
)
```
**[⬆ back to top](#table-of-contents)**

### Functions should do one thing
This is by far the most important rule in software engineering. When functions do more 
than one thing, they are harder to compose, test, and reason about. When you can isolate 
a function to just one action, they can be refactored easily and your code will read much 
cleaner. If you take nothing else away from this guide other than this, you"ll be ahead 
of many developers.

**Bad:**
```python
from typing import List


class Client:
    active: bool


def email(client: Client) -> None:
    pass


def email_clients(clients: List[Client]) -> None:
    """Filter active clients and send them an email.
    """
    for client in clients:
        if client.active:
            email(client)
```

**Good**:
```python
from typing import List


class Client:
    active: bool


def email(client: Client) -> None:
    pass


def get_active_clients(clients: List[Client]) -> List[Client]:
    """Filter active clients.
    """
    return [client for client in clients if client.active]


def email_clients(clients: List[Client]) -> None:
    """Send an email to a given list of clients.
    """
    for client in get_active_clients(clients):
        email(client)
```

Do you see an opportunity for using generators now?

**Even better**
```python
from typing import Generator, Iterator


class Client:
    active: bool


def email(client: Client):
    pass


def active_clients(clients: Iterator[Client]) -> Generator[Client, None, None]:
    """Only active clients"""
    return (client for client in clients if client.active)


def email_client(clients: Iterator[Client]) -> None:
    """Send an email to a given list of clients.
    """
    for client in active_clients(clients):
        email(client)
```


**[⬆ back to top](#table-of-contents)**

### Function names should say what they do

**Bad:**

```python
class Email:
    def handle(self) -> None:
        pass

message = Email()
# What is this supposed to do again?
message.handle()
```

**Good:**

```python
class Email:
    def send(self) -> None:
        """Send this message"""

message = Email()
message.send()
```

**[⬆ back to top](#table-of-contents)**

### Functions should only be one level of abstraction

When you have more than one level of abstraction, your function is usually doing too 
much. Splitting up functions leads to reusability and easier testing.

**Bad:**

```python
# type: ignore

def parse_better_js_alternative(code: str) -> None:
    regexes = [
        # ...
    ]

    statements = code.split('\n')
    tokens = []
    for regex in regexes:
        for statement in statements:
            pass

    ast = []
    for token in tokens:
        pass

    for node in ast:
        pass
```

**Good:**

```python
from typing import Tuple, List, Text, Dict


REGEXES: Tuple = (
   # ...
)


def parse_better_js_alternative(code: Text) -> None:
    tokens: List = tokenize(code)
    syntax_tree: List = parse(tokens)

    for node in syntax_tree:
        pass


def tokenize(code: Text) -> List:
    statements = code.split()
    tokens: List[Dict] = []
    for regex in REGEXES:
        for statement in statements:
            pass

    return tokens


def parse(tokens: List) -> List:
    syntax_tree: List[Dict] = []
    for token in tokens:
        pass

    return syntax_tree
```

**[⬆ back to top](#table-of-contents)**

### Don"t use flags as function parameters

Flags tell your user that this function does more than one thing. Functions 
should do one thing. Split your functions if they are following different code 
paths based on a boolean.

**Bad:**

```python
from typing import Text
from tempfile import gettempdir
from pathlib import Path


def create_file(name: Text, temp: bool) -> None:
    if temp:
        (Path(gettempdir()) / name).touch()
    else:
        Path(name).touch()
```

**Good:**

```python
from typing import Text
from tempfile import gettempdir
from pathlib import Path


def create_file(name: Text) -> None:
    Path(name).touch()


def create_temp_file(name: Text) -> None:
    (Path(gettempdir()) / name).touch()
```

**[⬆ back to top](#table-of-contents)**

### Avoid side effects

A function produces a side effect if it does anything other than take a value in 
and return another value or values. For example, a side effect could be writing 
to a file, modifying some global variable, or accidentally wiring all your money
to a stranger.

Now, you do need to have side effects in a program on occasion - for example, like
in the previous example, you might need to write to a file. In these cases, you
should centralize and indicate where you are incorporating side effects. Don"t have
several functions and classes that write to a particular file - rather, have one
(and only one) service that does it.

The main point is to avoid common pitfalls like sharing state between objects
without any structure, using mutable data types that can be written to by anything,
or using an instance of a class, and not centralizing where your side effects occur.
If you can do this, you will be happier than the vast majority of other programmers.

**Bad:**

```python
# type: ignore

# This is a module-level name.
# It"s good practice to define these as immutable values, such as a string.
# However...
fullname = "Ryan McDermott"

def split_into_first_and_last_name() -> None:
    # The use of the global keyword here is changing the meaning of the
    # the following line. This function is now mutating the module-level
    # state and introducing a side-effect!
    global fullname
    fullname = fullname.split()

split_into_first_and_last_name()

# MyPy will spot the problem, complaining about 'Incompatible types in 
# assignment: (expression has type "List[str]", variable has type "str")'
print(fullname)  # ["Ryan", "McDermott"]

# OK. It worked the first time, but what will happen if we call the
# function again?
```

**Good:**
```python
from typing import List, AnyStr


def split_into_first_and_last_name(name: AnyStr) -> List[AnyStr]:
    return name.split()

fullname = "Ryan McDermott"
name, surname = split_into_first_and_last_name(fullname)

print(name, surname)  # => Ryan McDermott
```

**Also good**
```python
from typing import Text
from dataclasses import dataclass


@dataclass
class Person:
    name: Text

    @property
    def name_as_first_and_last(self) -> list:
        return self.name.split() 


# The reason why we create instances of classes is to manage state!
person = Person("Ryan McDermott")
print(person.name)  # => "Ryan McDermott"
print(person.name_as_first_and_last)  # => ["Ryan", "McDermott"]
```

**[⬆ back to top](#table-of-contents)**

## **Objects and Data Structures**

*Coming soon*

**[⬆ back to top](#table-of-contents)**

## **Classes**

### **Single Responsibility Principle (SRP)**
### **Open/Closed Principle (OCP)**
### **Liskov Substitution Principle (LSP)**
### **Interface Segregation Principle (ISP)**
### **Dependency Inversion Principle (DIP)**

*Coming soon*

**[⬆ back to top](#table-of-contents)**

## **Don"t repeat yourself (DRY)**

*Coming soon*

**[⬆ back to top](#table-of-contents)**

