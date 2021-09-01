# Unit testing

# Benefits of unit tests

## Planning

Tests show you the nuts and bolts of a codebase:

- What are the inputs (parameters) and outputs (return values) for a function?
- What inputs are common?
- What inputs are unusual (edge cases)?
- What inputs are not expected?
- How do such inputs change the output accordingly?

The act of writing tests help you think of these nuts and bolts early on before implementing your functions

## Reduce bugs

Running tests is like running a function with different inputs without running the actual program:

- Tests passing ensures that the function does what it was intended to do.
- Tests failing means the function did not do what was expected because
  - the code was not written with different inputs in mind
  - or the body of the function recently changed
  - or there was an actual error somewhere else in your code

## Documentation

Tests tell the story about the codebase. Your team members (and the teaching team at JTC) can tell what functions do

- without running the program (and using `print(func())`)
- or having to read every line of code inside functions

# Installing pytest

```
pip install pytest
```

# Demo 1 - Writing tests for a function

```python
import pytest

# An example function
def greeting(first_name, middle_name, last_name):
    return f'Hi, {first_name} {middle_name} {last_name}'

# tests to make sure the actual output is what we expect
def test_greeting_george():
    assert greeting("George", "Orson", "Welles") == "Hi, George Orson Welles"

def test_greeting_yuval():
    assert greeting("Yuval", "Noah", "Harari") == "Hi, Yuval Noah Harari"

```

```
pytest greeting_test.py
```
<img width="1331" alt="Screen Shot 2021-03-09 at 9 11 50 AM" src="https://user-images.githubusercontent.com/7483633/110483234-85f92400-80b7-11eb-9993-b61c150dabe3.png">


We could also make this more readable by creating some variables...

```python
def test_greeting_george():
    # the actual output of the function
    actual = greeting("George", "Orson", "Welles")
    # the expected output of the function
    expected = "Hi, Yuval Noah Harari"
    # assert that actual and expected are the same values for the test to pass
    assert actual == expected

def test_greeting_yuval():
    # the actual output of the function
    actual = greeting("Yuval", "Noah", "Harari")
    # the expected output of the function
    expected = "Hi, George Orson Welles"
    # assert that actual and expected are the same values for the test to pass
    assert actual == expected
```

## Keeping your tests up to date

Imagine that your team has a new member and they've been given a task to update the `greeting`: Adding an exclamation mark to the end of the greeting message...

```
Hi, George Orson Welles!
```

They go ahead an add the exclamation mark to the greeting function...

```python
def greeting(first_name, middle_name, last_name):
    return f'Hi, {first_name} {middle_name} {last_name}!'
```

When they run the tests in `greeting_test.py` file again, the tests will fail.

<img width="1315" alt="Screen Shot 2021-03-09 at 9 10 18 AM" src="https://user-images.githubusercontent.com/7483633/110482999-47636980-80b7-11eb-966b-cd2218e05842.png">

This is actually a good thing! Failing test tells them the expected outputs were once different and now must be updated to reflect new requirements that led to code changes.

```python
# adding ! to the expected output
def test_greeting_george():
    assert greeting("George", "Orson", "Welles") == "Hi, George Orson Welles!"
# adding ! to the expected output
def test_greeting_yuval():
    assert greeting("Yuval", "Noah", "Harari") == "Hi, Yuval Noah Harari!"
```

Run `pytest` again and the tests should now pass again!

<img width="1331" alt="Screen Shot 2021-03-09 at 9 11 50 AM" src="https://user-images.githubusercontent.com/7483633/110483885-25b6b200-80b8-11eb-8757-7c220f4bdc12.png">


# Structuring your files

Pytest recommends the following [directory structures](https://docs.pytest.org/en/reorganize-docs/new-docs/user/directory_structure.html) for python modules and their tests:

For this lesson we will use the following structure:

```
# the root directory
module_1.py
module_2.py
tests/
    module_1_test.py
    module_2_test.py
```

To run all the test files created, we run `pytest` from the root directory:

```python
pytest
```

This time we don't have to specify the file names, `pytest` searches for test files and executes them

# Demo 2 - Importing a module from a parent directory

Let's create a module called `math_functions.py`:

```python
# math_functions.py
def square(num):
    return num * num

def cube(num):
    return square(num) * num
```

Create a directory called `tests` and create a test file inside called `math_functions_test.py`

```python
# math_functions_test.py
import pytest

# this makes sure we look also look at the parent directory to import from the math_functions.py file
import os, sys
sys.path.append(os.path.dirname(os.path.dirname(os.path.realpath(__file__))))

from math_functions import *

# tests for square
def test_square_two():
    assert square(2) == 4, "Should equal 4"

def test_square_four():
    assert square(4) == 16, "Should equal 16"

# tests for cube
def test_cube_two():
    assert cube(2) == 16, "Should equal 16"

def test_cube_four():
    assert cube(4) == 64, "Should equal 64"
```

and finally run at the root directory (where the math_functions module is)

```python
pytest
```

# Working with large inputs

Sometimes we're working with large inputs to our functions

```python
# book_functions.py
books_list = [
    {
        'title': 'MY OWN WORDS',
        'author': 'Ruth Bader Ginsburg with Mary Hartnett and Wendy W Williams',
        'description': 'A collection of articles and speeches by the Supreme Court justice.'
    },
    {
        'title': 'WHITE FRAGILITY',
        'author': 'Robin DiAngelo',
        'description': 'Historical and cultural analyses on what causes defensive moves by white people and how this inhibits cross-racial dialogue.'
    },
    {
        'title': 'THE BODY KEEPS THE SCORE',
        'author': 'Bessel van der Kolk',
        'description': 'How trauma affects the body and mind, and innovative treatments for recovery.'
    },
    {
        'title': 'SO YOU WANT TO TALK ABOUT RACE',
        'author': 'Ijeoma Oluo',
        'description': 'A look at the contemporary racial landscape of the United States.'
    },
]

def get_book_titles(books):
    titles = []
    for book in books:
        titles.append(book["title"])
    return titles

def get_book_authors(books):
    authors = []
    for book in books:
        authors.append(book["author"])
    return authors

get_book_titles(books_list)
get_book_authors(books_list)
```

The tests for these functions would look like this:

```python
import pytest

# this makes sure we look also look at the parent directory to import from the math_functions.py file
import os, sys
sys.path.append(os.path.dirname(os.path.dirname(os.path.realpath(__file__))))

from book_functions import *

def test_book_titles():
    books_list = [
        {
            'title': 'MY OWN WORDS',
            'author': 'Ruth Bader Ginsburg with Mary Hartnett and Wendy W Williams',
            'description': 'A collection of articles and speeches by the Supreme Court justice.'
        },
        {
            'title': 'WHITE FRAGILITY',
            'author': 'Robin DiAngelo',
            'description': 'Historical and cultural analyses on what causes defensive moves by white people and how this inhibits cross-racial dialogue.'
        },
        {
            'title': 'THE BODY KEEPS THE SCORE',
            'author': 'Bessel van der Kolk',
            'description': 'How trauma affects the body and mind, and innovative treatments for recovery.'
        },
        {
            'title': 'SO YOU WANT TO TALK ABOUT RACE',
            'author': 'Ijeoma Oluo',
            'description': 'A look at the contemporary racial landscape of the United States.'
        },
    ]
    assert get_book_titles(books) == ['MY OWN WORDS', 'WHITE FRAGILITY', 'THE BODY KEEPS THE SCORE', 'SO YOU WANT TO TALK ABOUT RACE']

def test_book_authors():
    books_list = [
        {
            'title': 'MY OWN WORDS',
            'author': 'Ruth Bader Ginsburg with Mary Hartnett and Wendy W Williams',
            'description': 'A collection of articles and speeches by the Supreme Court justice.'
        },
        {
            'title': 'WHITE FRAGILITY',
            'author': 'Robin DiAngelo',
            'description': 'Historical and cultural analyses on what causes defensive moves by white people and how this inhibits cross-racial dialogue.'
        },
        {
            'title': 'THE BODY KEEPS THE SCORE',
            'author': 'Bessel van der Kolk',
            'description': 'How trauma affects the body and mind, and innovative treatments for recovery.'
        },
        {
            'title': 'SO YOU WANT TO TALK ABOUT RACE',
            'author': 'Ijeoma Oluo',
            'description': 'A look at the contemporary racial landscape of the United States.'
        },
    ]
    assert get_book_authors(books_list) == ['Ruth Bader Ginsburg with Mary Hartnett and Wendy W Williams', 'Robin DiAngelo', 'Bessel van der Kolk', 'Ijeoma Oluo']
```

You're probably wondering why we need to have the same list created in every test function. As a rule **we always want to work with a fresh list in each of our test functions**. This ensures that nothing is updated in the list by a previous test function.

To clean this up, pytest gives us something called `fixture`

```python
import pytest

# this makes sure we look also look at the parent directory to import from the math_functions.py file
import os, sys
sys.path.append(os.path.dirname(os.path.dirname(os.path.realpath(__file__))))

from book_functions import *

@pytest.fixture
def books_data():
    return [
        {
            'title': 'MY OWN WORDS',
            'author': 'Ruth Bader Ginsburg with Mary Hartnett and Wendy W Williams',
            'description': 'A collection of articles and speeches by the Supreme Court justice.'
        },
        {
            'title': 'WHITE FRAGILITY',
            'author': 'Robin DiAngelo',
            'description': 'Historical and cultural analyses on what causes defensive moves by white people and how this inhibits cross-racial dialogue.'
        },
        {
            'title': 'THE BODY KEEPS THE SCORE',
            'author': 'Bessel van der Kolk',
            'description': 'How trauma affects the body and mind, and innovative treatments for recovery.'
        },
        {
            'title': 'SO YOU WANT TO TALK ABOUT RACE',
            'author': 'Ijeoma Oluo',
            'description': 'A look at the contemporary racial landscape of the United States.'
        },
    ]

# we pass books_data as a parameter
def test_book_titles(books_data):
     assert get_book_titles(books_data) == ['MY OWN WORDS', 'WHITE FRAGILITY', 'THE BODY KEEPS THE SCORE', 'SO YOU WANT TO TALK ABOUT RACE']

# we pass books_data as a parameter
def test_book_authors(books_data):
    assert get_book_authors(books_data) == ['Ruth Bader Ginsburg with Mary Hartnett and Wendy W Williams', 'Robin DiAngelo', 'Bessel van der Kolk', 'Ijeoma Oluo']
```

# Your new workflow

- Figure out the requirements of your project. If you have a tip calculator for example, how can you break the logic into smaller steps, which could then be created into functions that can be tested
- Write the tests
- If requirements change, make sure the tests reflect that
- Ensure your tests always pass before you push your code to github

# Conclusion

- Unit tests are best when the function you're testing is simple and small. Always find ways to break your code down into smaller functions.
- This is just the beginning! If you're interested in doing more with `pytest`, checkout `parameterize` API in the pytest [documentation](https://docs.pytest.org/en/stable/reference.html)
- Writing tests for functions that use `input()` are a step more complex than what we covered in class. Checkout this thread on [stackoverflow](https://stackoverflow.com/questions/35851323/how-to-test-a-function-with-input-call)
- Testing for randomness (using a random number generator in a function for example) are a step more complex as well. Checkout this thread on [stackoverflow](https://stackoverflow.com/questions/49886063/how-can-i-test-functions-which-use-random)
