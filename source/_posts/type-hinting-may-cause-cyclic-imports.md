---
title: 'Type hinting may cause cyclic imports'
date: 2021-07-05 00:00:00
tags: [Python]
published: true
hideInList: false
feature: 
isTop: false
---

Recently I was working on rss3 SDK. In order to facilitate development, I just make a python version of the reference [JavaScript  SDK](https://github.com/NaturalSelectionLabs/RSS3-SDK-for-JavaScript), which means the usage should be pretty similar between both.

## Make a Python version

What's  more, to make a clear code,I use type hinting in the new project. However, there is a code snippet in JavaScript version:

```jsx
# index.ts
class RSS3 {
    options: IOptions;
    persona: Persona;
    file: File;
    profile: Profile;
    items: Items;
    item: Item;
    links: Links;

    constructor(options: IOptions) {
        this.options = options;

        this.file = new File(this);
        this.persona = new Persona(this);
        this.profile = new Profile(this);
        this.items = new Items(this);
        this.item = new Item(this);
        this.links = new Links(this);
    }
}
# file.ts
import Main from './index';
class File {
    private main: Main;
    private list: {
        [key: string]: RSS3IContent;
    } = {};
    private dirtyList: {
        [key: string]: number;
    } = {};

    constructor(main: Main) {
        this.main = main;
    }
...
}
```

Yes, the two files refer to each other. Actually, it happens on many other files. When I turned this into Python version:

```python
# index.py
class RSS3:
		...
    persona: Persona
    file: File_
    profile: Profile
    items: Items
    items: Item
    links: Links

# file.py
from .index import RSS3

class File:
    rss3: RSS3

def __init__(self, rss3: RSS3):
        self.rss3 = rss3
```

## circular import

It didn't seem any errors, but when I started testing the problems appeared.

```python
E   ImportError: cannot import name 'RSS3' from partially initialized module 'rss3.src.index' (most likely due to a circular import) 
```

how to solve this problem ? Don't worry, [PEP 484](https://www.python.org/dev/peps/pep-0484/#runtime-or-type-checking) has given a solution.

## Solutions

> When a type hint contains names that have not been defined yet, that definition may be expressed as a string literal, to be resolved later.

So we can modify our code like the following:

```python
class File:
    rss3: 'RSS3'

def __init__(self, rss3: 'RSS3'):
        self.rss3 = rss3
```

That's okay already.

> Sometimes there's code that must be seen by a type checker (or other static analysis tools) but should not be executed. For such situations the typing module defines a constant, TYPE_CHECKING, that is considered True during type checking (or other static analysis) but False at runtime.

Modify out code again:

```python
if TYPE_CHECKING:
    from .index import RSS3

class File:
    rss3: 'RSS3'

	def __init__(self, rss3: 'RSS3'):
	        self.rss3 = rss3
```

## What's more

if we are using Python 3.7+, we can at least skip having to provide an explicit string annotation by taking advantage of [PEP 563](https://www.python.org/dev/peps/pep-0563/):

```python
from __future__ import annotations
from typing import TYPE_CHECKING
if TYPE_CHECKING:
    from .index import RSS3

class File:
    rss3: 'RSS3'

	def __init__(self, rss3: 'RSS3'):
	        self.rss3 = rss3
```

The `from __future__  import annotations` import will make all type hints be strings and skip evaluating them.

# References

[PEP 484 -- Type Hints](https://www.python.org/dev/peps/pep-0484/#forward-references)

[Python type hinting without cyclic imports](https://stackoverflow.com/questions/39740632/python-type-hinting-without-cyclic-imports)