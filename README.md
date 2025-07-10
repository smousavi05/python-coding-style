
***

## Python Style Guide

This guide outlines code style and best practices for writing Python code, particularly suited for open-source machine learning projects on GitHub. It is intended as a reference for both writing and reviewing Python code.

While specific rules can be subjective, this guide is based on widely accepted authorities in the Python community, including PEP8, principles from "Clean Code". Remember to use your best judgment, as there are sometimes good reasons for breaking rules. Consistency is paramount: if you're editing existing code, adopt its style, but also consider converging on newer, beneficial styles over time.

### 1. General Principles

*   **Consistency:** Be consistent in your code style within a file and project.
    *   **Good:** `user_emails = [user.email for user in users]`
    *   **Bad:** Mixing different string quote styles: `Python("Why are you hiding your eyes?")` and `Gollum('The lint. It burns. It burns us.')`
*   **Readability:** Prioritize clear, understandable code over extreme conciseness.
*   **Linters:** Use linters like `flake8` or `pylint` to ensure code follows style guidelines and to catch common errors.
    *   **Description:** `flake8` helps ensure PEP8 compliance, and `pylint` finds bugs and style issues. Consider running them as part of your CI chain.
    *   **Good:** Running `flake8` in your editor or CI to check for PEP8 compliance.
    *   **Bad:** Ignoring linter warnings without suppression or explanation.

### 2. Commenting

*   **Purpose:** Keep comments to a minimum, generally only necessary for understanding complex or non-obvious code. If you feel the urge to comment, consider if the code can be written more clearly instead.
    *   **Good:**
        ```python
        # We use a weighted dictionary search to find out where i is in
        # the array. We extrapolate position based on the largest num
        # in the array and the array size and then do binary search to
        # get the exact number.
        if i & (i - 1) == 0:  # True if i is 0 or a power of 2.
        ```
       
    *   **Bad:**
        ```python
        # Get all frobs for users <-- Redundant, stating the obvious
        user_frobs = get_user_frobs(user_id)
        ```
       
*   **Avoid Commented-Out/Dead Code:** Never commit commented-out code or dead code. Delete it; Git remembers.
    *   **Good:** Deleting unused functions or lines of code immediately when identified as dead.
    *   **Bad:** Leaving code commented out with a note "just in case we need it".
*   **Formatting:** Comments should start at least 2 spaces away from the code with `#`, followed by at least one space before the text.
*   **TODO Comments:** Use `TODO` comments for temporary code or short-term solutions. They should begin with `TODO:`, a colon, a link to context (like a bug reference), and an explanatory string introduced with a hyphen.
    *   **Good:** `# TODO: crbug.com/192795 - Investigate cpufreq optimizations.`
    *   **Bad:** `# TODO(youremailaddress): Use a "* " here for concatenation operator.`

### 3. Docstrings

*   **Format:** Use three-double-quotes `"""` for all docstrings. They should start with a one-line summary (under 80 characters) followed by a blank line, then the rest of the description.
*   **Modules:** Every file should have a docstring describing its contents and usage.
    *   **Good:**
        ```python
        """A one-line summary of the module or program, terminated by a period.

        Leave one blank line. The rest of this docstring should contain an
        overall description of the module or program. Optionally, it may
        also contain a brief description of exported classes and functions
        and/or usage examples.
        """
        ```
       
    *   **Bad:**
        ```python
        """Tests for foo.bar."""
        ```
        (for test modules, only if no additional information is provided)
*   **Functions/Methods:** A docstring is mandatory for public API functions, nontrivial functions, or those with non-obvious logic. It should describe the function's calling syntax, semantics, and any side effects, not implementation details irrelevant to the caller.
    *   **Sections:** Include `Args:`, `Returns:` (or `Yields:` for generators), and `Raises:` sections as appropriate. Use a hanging indent for descriptions.
    *   **Good:**
        ```python
        def fetch_smalltable_rows(
            table_handle: smalltable.Table,
            keys: Sequence[bytes | str],
            require_all_keys: bool = False,
        ) -> Mapping[bytes, tuple[str, ...]]:
            """Fetches rows from a Smalltable.

            Retrieves rows pertaining to the given keys from the Table instance
            represented by table_handle. String keys will be UTF-8 encoded.

            Args:
                table_handle: An open smalltable.Table instance.
                keys: A sequence of strings representing the key of each table row to fetch.
                    String keys will be UTF-8 encoded.
                require_all_keys: If True only rows with values set for all keys will be returned.

            Returns:
                A dict mapping keys to the corresponding table row data fetched.
                Each row is represented as a tuple of strings.
            Raises:
                IOError: An error occurred accessing the smalltable.
            """
        ```
       
    *   **Bad:**
        ```python
        def my_function(arg1, arg2):
            """Does something.""" # Too vague, lacks Args/Returns/Raises details
            # ...
        ```
       
*   **Classes:** Classes should have a docstring below the definition, including a one-line summary and an `Attributes` section for public attributes.
    *   **Good:**
        ```python
        class SampleClass:
            """Summary of class here.
            Longer class information...
            Longer class information...

            Attributes:
                likes_spam: A boolean indicating if we like SPAM or not.
                eggs: An integer count of the eggs we have laid.
            """
        ```
       
    *   **Bad:**
        ```python
        class CheeseShopAddress:
            """Class that describes the address of a cheese shop. ...""" # Redundant "Class that describes"
        ```
       

### 4. Type Checking (Type Hints)

*   **Benefits:** Type annotations improve readability and maintainability, catching many runtime errors at build time. Use a static type checker like `mypy` or `pytype`.
*   **Usage:** Annotate function arguments, return values, and variables using syntax like `def func(a: int) -> list[int]:` or `a: SomeType = some_func()`.
*   **Generality:** Prefer generic abstract container types like `collections.abc.Sequence` or `typing.Iterable` over concrete types like `list` to retain duck-typing flexibility.
    *   **Good:** `def find_squanched_frob(frobs: Iterable[Frob]) -> Frob:`
    *   **Bad:** `def find_squanched_frob(frobs: List[Frob]) -> Frob:`
*   **`None` Values:** If an argument can legally be `None`, it must be explicitly typed as `Optional[X]` (from `typing`) or `X | None` (Python 3.10+ recommended).
    *   **Good:** `def modern_or_union(a: str | int | None, b: str | None = None) -> str:`
    *   **Bad:** `def implicit_optional(a: str = None) -> str:`
*   **Avoid `Any`:** Don't use the `Any` type unless absolutely necessary, as it defeats the purpose of type checking. If the best type parameter for a generic is `Any`, make it explicit, or prefer `TypeVar`.
*   **Line Breaking:** For long function signatures, prefer one parameter per line, aligning the closing parenthesis with the `def` keyword. Break lines between variables, not within type annotations.
    *   **Good:**
        ```python
        def my_method(
            self,
            first_var: int,
            second_var: Foo,
            third_var: Bar | None,
        ) -> int:
            # ...
        ```
       
    *   **Bad:**
        ```python
        def my_method( self, other_arg: MyLongType | None,) -> dict[OtherLongType, MyLongType]:
            # ...
        ```
        (closing parenthesis on new line, not aligned with `def`)
*   **Imports for Typing:** For symbols from `typing` or `collections.abc` used for type checking, always import the symbol itself directly (e.g., `from typing import Any, Iterable`). Place all imports at the top of the file, after module docstrings, grouped from most generic to least generic (future, standard library, third-party, local package) and sorted lexicographically.

### 5. Unit Tests

*   **Importance:** Proper test coverage is crucial, especially in dynamically typed languages like Python, where type errors might only appear at runtime.
*   **Properties of Good Tests:**
    *   **Small:** Test only one thing per test.
    *   **Thorough:** Cover all code branches.
    *   **Independent:** Do not rely on other tests' existence or execution order.
    *   **Fast:** Use small, realistic sample data for tests.
*   **Frameworks:** `pytest` is a popular and recommended testing framework.
*   **Mocking:** When interacting with external services (like databases or cloud APIs), use mocking to isolate your code during tests.
    *   **Good:** Using a mock library (e.g., `moto3` for AWS `boto3`) to simulate external service behavior without actual calls.
    *   **Bad:** Writing unit tests that make real API calls to external databases or cloud services.
*   **Developer Responsibility:** The developer implementing a feature is responsible for adequate test coverage of new and changed code.
*   **CI Integration:** Integrate test coverage services (like `codecov` or `coveralls`) into your CI chain.
*   **Naming:** For new unit test files, follow PEP 8 compliant `lower_with_under` method names, e.g., `test_<method_under_test>_<state>`.

### 6. Other Best Practices

*   **Line Length:** Maximum line length is 80 characters, with explicit exceptions for long imports, URLs, or pylint disable comments. Use Python’s implicit line joining with parentheses, brackets, or braces instead of backslashes for continuation.
    *   **Good:** `if (width == 0 and height == 0 and color == 'red' and emphasis == 'strong'):`
    *   **Bad:** `if width == 0 and height == 0 and \ color == 'red' and emphasis == 'strong':`
*   **Naming Conventions:** Names should be descriptive but concise.
    *   **Modules/Packages:** `lower_with_under`.
    *   **Classes/Exceptions:** `CapWords`.
    *   **Functions/Methods/Variables:** `lower_with_under()`.
    *   **Global/Class Constants:** `CAPS_WITH_UNDER`.
    *   **Internal Symbols:** Prefix with a single underscore (`_`).
    *   **Good:** `days_between_events` instead of `number_of_days_between_events` (too long) or `dbe` (not descriptive).
    *   **Bad:** `models.UserModel` (redundant naming if the module is `models`).
*   **Exceptions and Error Handling:**
    *   **Use Exceptions:** Python's error handling is built around exceptions; use them to indicate invalid states that cannot be recovered from.
    *   **Custom Exceptions:** Define your own exceptions by inheriting from existing ones when useful, especially to abstract low-level errors.
    *   **Avoid Returning `None` for Errors:** Never return `None` as an error indicator. Raise an exception instead.
        *   **Good:**
            ```python
            def get_persons_by_age(age):
                if age < 0:
                    raise ValueError('age cannot be negative')
            ```
           
        *   **Bad:**
            ```python
            def get_user(user_id):
                if user_id_not_found:
                    return None # Error indicated by None
                # ...
            ```
           
    *   **Specific Exceptions:** Only catch exceptions you know how to handle. Avoid bare `except:` or `except Exception:` unless re-raising or at an isolation point.
        *   **Good:**
            ```python
            try:
                conf_file = open(conf_path)
            except IOError:
                print('Could not load config, loading default config')
            ```
           
        *   **Bad:**
            ```python
            try:
                user = get_user(user_id)
            except Exception: # Catches all exceptions, including bugs or KeyboardInterrupt
                # Oh no, something bad happened
                pass
            ```
           
*   **Abstractions:** Don't be afraid to use Python's high-level abstractions like list comprehensions, generators, iterators, and context managers. They can make code cleaner and less complex.
    *   **Good (List Comprehension):** `user_emails = [user.email for user in users]`
    *   **Bad (Loop for list building):**
        ```python
        user_emails = []
        for user in users:
            user_emails.append(user.email)
        ```
       
    *   **Good (Context Manager):**
        ```python
        with open('studios.csv') as f:
            studios_data = f.read()
        ```
       
    *   **Bad (Manual file closing):**
        ```python
        f = open('studios.csv')
        studios_data = f.read()
        f.close()
        ```
       
*   **Mutability and Pure Functions:** Avoid mutable global state. Prefer pure functions (no side effects, deterministic) when implementing logic. Functions should generally do only one thing.
    *   **Good (Pure function):** `def squanch_frobs(frobs): return [frob.squanch() for frob in frobs]`
    *   **Bad (Mutates external state):** `def squanch_frobs(): for frob in frobs: frob.squanch()`
*   **Object-Oriented Programming (OOP):** Use OOP when appropriate (e.g., storing related data, encapsulating stateful concepts, polymorphism).
    *   **Getters/Setters:** Access attributes directly. Don't create getters and setters unless necessary. Use `@property` decorator when logic is needed for access.
    *   **Good:** `my_object.attribute = new_value`
    *   **Bad:** `my_object.set_attribute(new_value)` (if it's just setting an internal attribute without complex logic)
*   **Default Arguments:** Avoid using mutable types (e.g., lists, dictionaries) as default arguments, as they are evaluated once at module load time and become mutable global state.
    *   **Good:**
        ```python
        def squanch_frobs(frobs, excluded_frobs=None):
            if excluded_frobs is None:
                excluded_frobs = []
            # ...
        ```
       
    *   **Bad:** `def squanch_frobs(frobs, excluded_frobs=[]):`
*   **String Formatting:** Use f-strings (Python 3.6+), the `%` operator, or the `.format()` method. Avoid accumulating strings in a loop using `+` or `+=`; instead, use `''.join()` on a list of substrings.
    *   **Good (f-string):** `x = f'name: {name}; score: {n}'`
    *   **Bad (Concatenation in loop):**
        ```python
        employee_table = '<table>'
        for last_name, first_name in employee_list:
            employee_table += '<tr><td>%s, %s</td></tr>' % (last_name, first_name)
        employee_table += '</table>'
        ```
       
*   **Virtual Environments:** Use virtual environments (e.g., `venv`, `virtualenv`, `Poetry`, `Pipenv`) to isolate project dependencies. `Poetry` and `Pipenv` offer virtual environment support and deterministic dependency management, which is beneficial for reproducible ML environments.
*   **Packaging:** Consider packaging your project as an installable Python package if it's a library. Use `pyproject.toml` for modern packaging. Use semantic versioning (`MAJOR.MINOR.PATCH`) for libraries.
*   **Function Length:** Prefer small, focused functions. If a function exceeds about 40 lines, consider breaking it into smaller, more manageable pieces.
*   **`if __name__ == '__main__':`:** Ensure your main functionality is in a `main()` function and executed only when the script is run directly, not when imported as a module.
    *   **Good:**
        ```python
        def main():
            # ... main program logic ...

        if __name__ == '__main__':
            main()
        ```
       
    *   **Bad:** Top-level code that executes immediately upon import, potentially causing side effects when the module is imported by other scripts or tools.


*    **Comprehensions & Generator Expressions:** Okay to use for simple cases. List, Dict, and Set comprehensions as well as generator expressions provide a concise and efficient way to create container types and iterators without resorting to the use of traditional loops, map(), filter(), or lambda.
    *   **Good:**
       ```result = [mapping_expr for value in iterable if filter_expr]

        result = [
         is_valid(metric={'key': value})
         for value in interesting_iterable
         if a_longer_filter_expression(value)
        ]
   
        descriptive_name = [
         transform({'key': key, 'value': value}, color='black')
         for key, value in generate_iterable(some_input)
         if complicated_condition_is_met(key, value)
        ]
   
        result = []
        for x in range(10):
          for y in range(5):
            if x * y > 10:
              result.append((x, y))
      
        return {
            x: complicated_transform(x)
            for x in long_generator_function(parameter)
            if x is not None
        }
   
        return (x**2 for x in range(10))
      
        unique_names = {user.name for user in users if user is not None}
     ```
       
    *   **Bad:**
      ```result = [(x, y) for x in range(10) for y in range(5) if x * y > 10]

        return (
            (x, y, z)
            for x in range(5)
            for y in range(5)
            if x != y
            for z in range(5)
            if y != z
        )
      ```

*   **Default Iterators and Operators:** Use default iterators and operators for types that support them, like lists, dictionaries, and files. The built-in types define iterator methods, too. Prefer these methods to methods that return lists, except that you should not mutate a container while iterating over it.
    *   **Good:**
         ```for key in adict: ...
            if obj in alist: ...
            for line in afile: ...
            for k, v in adict.items(): ...
        ```    
    *   **Bad:**
        ```for key in adict.keys(): ...
           for line in afile.readlines(): ...
        ```

*   **True/False Evaluations:** Use the "implicit" false if possible, e.g., if foo: rather than if foo != []:. There are a few caveats that you should keep in mind though:

      Always use if foo is None: (or is not None) to check for a None value. E.g., when testing whether a variable or argument that defaults to None was set to some other value. The other value might be a value that's false in a boolean context!
      
      Never compare a boolean variable to False using ==. Use if not x: instead. If you need to distinguish False from None then chain the expressions, such as if not x and x is not None:.
      
      For sequences (strings, lists, tuples), use the fact that empty sequences are false, so if seq: and if not seq: are preferable to if len(seq): and if not len(seq): respectively.
      
      When handling integers, implicit false may involve more risk than benefit (i.e., accidentally handling None as 0). You may compare a value which is known to be an integer (and is not the result of len()) against the integer 0.

    *   **Good:**
         ```if not users:
               print('no users')
         
            if i % 10 == 0:
               self.handle_multiple_of_ten()
         
            def f(x=None):
               if x is None:
                  x = []
        ```    
    *   **Bad:**
        ```if len(users) == 0:
             print('no users')
      
           if not i % 10:
             self.handle_multiple_of_ten()
      
           def f(x=None):
             x = x or []
        ```

