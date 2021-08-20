---
category: [programming, python, reading]
tags: [pytest, test-driven, domain-driven]
title: "Reading notes for Architecture Patterns with Python by Harry Percival, Bob Gregory"
---

Notes, references and codes I wrote while reading [`Architecture Patterns with Python` by Harry Percival, Bob Gregory - O'Reilly](https://www.oreilly.com/library/view/architecture-patterns-with/9781492052197/).

<!--more-->

## Coding along

Minimal working examples following the book content at [github.com/bmaingret/architecture-patterns-code-along](https://github.com/bmaingret/architecture-patterns-code-along).

The full code by the authors is also available, as well as their book at [github.com/cosmicpython](https://github.com/cosmicpython/)

## Chapter 1 - Domain model

> The domain is a fancy way of saying the problem you’re trying to solve

> The domain model is the mental map that business owners have of their businesses

### Diving in the domain model

* Understand the business jargon and keeps a glossary
* Get concrete examples of the rules defining the domain model
* TDD: translates those rules into unit tests

### Value Object Pattern

> any domain object that is uniquely identified by the data it holds; we usually make them immutable

Which in Python can easily be converted to frozen dataclasses, offering hash for free.

```python
@dataclass(frozen=True)
class OrderLine:
    order_reference: str
    sku: str
    quantity: int

```

### Domain entity

> domain object that has long-lived identity

Usually mutable and have a fixed identity not depending on their values.

> We usually make this explicit in code by implementing equality operators on entities:

Python -> Defining the `__eq__` operator

Careful when defining hash, which basically means identifying what uniquely defines an entity along its life.

### Not everything must be in a class

Domain Service Function: in Python we can put the function in the module without making it more than that.

### Exceptions as domain concepts

Business errors/exceptions can be nicely represented by exceptions

```python
class OutOfStock(Exception):
    pass
```



## Chapter 2 - Repository Pattern

### Port and Adapter

Port usually is some interface, and adapter its implementation. In Python, this usually translates to some abstract base class and its implementation, but it can also be an implicit duck type port.

### ORM

ORM can lead to high dependency towards the ORM framework, and one must be careful to invert the dependency and makes the ORM depends on the domain models instead.

### Repository pattern

Make an abstraction around the storage. Looks like everything is stored in-memory and allows for 