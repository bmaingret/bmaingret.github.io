---
category: [programming, python, reading]
tags: [pytest, test-driven, domain-driven]
title: "Reading notes for Architecture Patterns with Python by Harry Percival, Bob Gregory"
---

Notes, references and codes I wrote while reading [`Architecture Patterns with Python` by Harry Percival, Bob Gregory - O'Reilly](https://www.oreilly.com/library/view/architecture-patterns-with/9781492052197/).

**Work in progress**

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

Note however than because SQLAlchemy modifies the class at runtime, we have to use `unsafe_hash=True` instead...

### Domain entity

> Domain object that has long-lived identity

Usually mutable and have a fixed identity not depending on their values.

> We usually make this explicit in code by implementing equality operators on entities:

In Python that means defining the `__eq__` operator.

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

### Repository pattern

Make an abstraction around the storage. Looks like everything is stored in-memory and allows for 
### Port and Adapter

Port usually is some interface, and adapter its implementation. In Python, this usually translates to some abstract base class and its implementation, but it can also be an implicit duck type port.

### ORM

ORM can lead to high dependency towards the ORM framework, and one must be careful to invert the dependency and makes the ORM depends on the domain models instead.

## Interlude - Reproducibility and Continuous Integration

Although I had read a lot and knew this was the way to go, I never took the time to implement it in any of my project, so I thought this would be a good opportunity, even more since the original authors take this path as well (Makefile and Docker).

### Development and Production Environment - Python mess

> I mostly followed [https://ealizadeh.com/blog/guide-to-python-env-pkg-dependency-using-conda-poetry]()
> 
> I had so much trouble making everything work under plain Windows, that I moved all my Python dev to WSL2.

![Had to](https://imgs.xkcd.com/comics/python_environment.png)
> https://xkcd.com/1987

I had learned of Poetry a year ago, but still stuck to Conda. I never liked the Conda way of handling dependencies, but it is still of great help to install some data science tools that are not pure Python. 

* Install Conda (I prefer to use  the [Miniconda installer](https://docs.conda.io/en/latest/miniconda.html))
* Install Poetry (through the [install-poetry.py script](https://python-poetry.org/docs/master/#installation)). N.B. this requires a working installation of Python, so if you only installed it with Conda, install it through a shell with a Conda environment active.
* Create a minimal Conda environment (`conda create --name remote-work-env python=3.8.5` or `conda create --file environment.yml`)
* Set up a new project/Init an existing project using Poetry (e.g. `poetry init` from within an existing directory). N.B. Poetry should pick up the active Conda environment and not create a new one.
* Manage dependencies with Poetry (`poetry add sqlalchemy`, `poetry add --dev pytest`)

### Github Actions

Github Actions are a recent (and welcome) addition to Github, allowing CI/CD workflow right in Github. Although it is possible to stay in the Github ecosystem, some *Actions* still depends on external tools and API.

To get a good starting example, one can go to the Actions tab in a github repository, and at the *Choose a workflow template* select *Skip this and set up a workflow yourself*, an editable configuration template will be made available right from the browser.

![](/assets/2021-08-13-architecture-patterns-with-python/2021-08-13-architecture-patterns-with-python_github_actions.png)

For this repository, I have a single workflow containing the test passing, test coverage, linting and formatting. The Python setup is done thanks to an available Github Actions, dependency install thanks to Poetry, coverage through pytest-cov and [codecov](https://app.codecov.io/gh/bmaingret/architecture-patterns-code-along), and finally linting/formatting thanks to [pre-commit.ci](https://results.pre-commit.ci/repo/github/395353648). More on that in next part on pre-commits.

### Git hooks - Pre-commit

Instead (or in addition) of automating code quality checks before merging, we can take part of the git hook to check these before even committing. Thanks to some good people, we have an awesome Python tool for that: [pre-commit.com](https://pre-commit.com). Once installed, configuration can be made through a `.pre-commit-config.yml` file and then installed with `pre-commit install`. Once *installed* you can see the generated Python script file `.git/hooks/pre-commit`.

To configure hooks with pre-commit, you need to specify git repositories. Several hooks are available at the [pre-commit repository](https://github.com/pre-commit/pre-commit-hooks), among which I configured:
* check-yaml
* check-toml
* end-of-file-fixer
* trailing-whitespace

To this I added [Flake8](https://github.com/PyCQA/flake8) a Python linter,  and [black](https://github.com/psf/black) a code formatter (this will modify your code, although you can make it not, but that would go against the point).

Note that thanks to [pre-commit.ci](https://pre-commit.ci), the same configuration file can be used both to install the hooks locally, and to run checks in a Github Action.

## Chapter 3 - Coupling and Abstractions

> reduce the degree of coupling within a system by abstracting away the details

Some key takeaways:

* Abstractions and decoupling help for testing (c.f. the repository pattern).
* Separate the core logic code from external states.

This usually allows to do *edge-to-edge* testing, faking some details (quite often I/O). This requires some additional abstractions (around the filesystem for instance) and new explicit dependencies on this abstractions).
