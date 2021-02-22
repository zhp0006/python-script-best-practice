The goal of this exercise is to learn some best practices for writing Python
code.

# Contents

-   [Getting set up](#getting-set-up)
-   [Learning objective](#learning-objective)
-   [The goal](#the-goal)
-   [The script](#the-script)
-   [Best practice 1: Put code into functions](#best-practice-1-put-code-into-functions)
-   [Best practice 2: Write modules not scripts](#best-practice-2-write-modules-not-scripts)
-   [Best practice 3: Use docstrings to document your code](#best-practice-3-use-docstrings-to-document-your-code)
-   [Best practice 4: Add tests to your docstrings](#best-practice-4-add-tests-to-your-docstrings)
-   [Acknowledgments](#acknowledgments)
-   [License](#license)


# Getting set up

At this point, you should have
(1) an account on [Github](https://github.com/) and
(2) been introduced to the very basics of [Git](https://git-scm.com/).

1.  Login to your [Github](https://github.com/) account.

1.  Fork [this repository](https://github.com/joaks1/python-script-example), by
    clicking the 'Fork' button on the upper right of the page.

    After a few seconds, you should be looking at *your* 
    copy of the repo in your own Github account.

1.  Click the 'Clone or download' button, and copy the URL of the repo via the
    'copy to clipboard' button.

1.  In your terminal, navigate to where you want to keep this repo (you can
    always move it later, so just your home directory is fine). Then type:

        $ git clone the-url-you-just-copied

    and hit enter to clone the repository. Make sure you are cloning **your**
    fork of this repo.

1.  Next, `cd` into the directory:

        $ cd the-name-of-directory-you-just-cloned

1.  At this point, you should be in your own local copy of the repository.

1.  As you work on the exercise below, be sure to frequently `add` and `commit`
    your work and `push` changes to the *remote* copy of the repo hosted on
    GitHub. Don't enter these commands now; this is just to jog your memory:

        $ # Do some work
        $ git add file-you-worked-on.py
        $ git commit
        $ git push origin master

# Learning objective 

Learn some practices in organizing and documenting your code when writing
Python scripts.

# The goal

The goal of this exercise is for you to take a simple, working Python script
and transform it into a much more helpful and useful Python module.

# The script

Here is the code that is currently in the `smallest_factor.py` file in
this repository:

```python
#! /usr/bin/env python3

# A script for getting the smallest prime factor of an integer.

import sys

if len(sys.argv) != 2:
    sys.exit(sys.argv[0] + ": Expecting one command line argument -- the integer for which to get the smallest factor")
n = int(sys.argv[1])
if n < 1:
    sys.exit(sys.argv[0] + ": Expecting a positive integer")

smallest_prime_factor = None
for i in range(2, n):
    if (n % i) == 0:
        smallest_prime_factor = i
        break

if smallest_prime_factor is None:
    print(n)
else:
    print(smallest_prime_factor)
```

Let's run it as a script and see what happens:

    $ python3 smallest_factor.py
    smallest_factor.py: Expecting one command line argument -- the integer for which to get the smallest factor

We get a message that we need to provide an integer to factor, so let's try:

    $ python3 smallest_factor.py 9
    3

OK, the script seems to work, but it isn't all that it could be.
Let's modify the code in `smallest_factor.py` and make it better.


# Best practice 1: Put code into functions

Let's take this code:

```python
smallest_prime_factor = None
for i in range(2, n):
    if (n % i) == 0:
        smallest_prime_factor = i
        break
```

and turn it into a function:

```python
def get_smallest_prime_factor(n):
    for i in range(2, n):
        if (n % i) == 0:
            return i
    return None
```

Now, our script looks like:

```python
#! /usr/bin/env python3

# A script for getting the smallest prime factor of an integer.

import sys


def get_smallest_prime_factor(n):
    for i in range(2, n):
        if (n % i) == 0:
            return i
    return None

if len(sys.argv) != 2:
    sys.exit(sys.argv[0] + ": Expecting one command line argument -- the integer for which to get the smallest factor")
n = int(sys.argv[1])
if n < 1:
    sys.exit(sys.argv[0] + ": Expecting a positive integer")

smallest_prime_factor = get_smallest_prime_factor(n)

if smallest_prime_factor is None:
    print(n)
else:
    print(smallest_prime_factor)
```

Let's make sure the script still works:

    $ python3 smallest_factor.py 9
    3

Are you getting errors? Try to fix the bugs in your code.
The error messages that Python reports take some getting use to,
but they are very helpful and informative, so read them carefully
and try to figure out what is wrong.


# Best practice 2: Write modules not scripts

What if we want to use the `get_smallest_prime_factor` function in other 
scripts we are writing?
We don't want to re-write this function in every script where
we want to use it.
Rather, we want to be able to import the function as part of a module.
Let's try importing our code now:

    $ python3
    >>> import smallest_factor

What happened? Well, the code in `smallest_factor.py` ran as a script and
closed the Python interpreter when finished.
That means we cannot use our shiny new function outside of the script
it currently lives in. Not cool! Let's fix this problem.

All of the code after our new function is simply the "command-line interface,"
or CLI, of our script.
This code simply processes the arguments from the command line and reports the
result to standard output.
Let's use the conditional `if __name__ == '__main__':` so that this code
only gets run when the file is executed as a script.
After all, that is the only time it needs to run!

```python
if __name__ == '__main__':
    if len(sys.argv) != 2:
        sys.exit(sys.argv[0] + ": Expecting one command line argument -- the integer for which to get the smallest factor")
    n = int(sys.argv[1])
    if n < 1:
        sys.exit(sys.argv[0] + ": Expecting a positive integer")
    
    smallest_prime_factor = get_smallest_prime_factor(n)
    
    if smallest_prime_factor is None:
        print(n)
    else:
        print(smallest_prime_factor)
```

Don't worry about what `__name__` is. It's just a variable to which Python
assigns the value `'__main__'` when the code is being run as script.

Now our script looks like:

```python
#! /usr/bin/env python3

# A script for getting the smallest prime factor of an integer.

import sys


def get_smallest_prime_factor(n):
    for i in range(2, n):
        if (n % i) == 0:
            return i
    return None

if __name__ == '__main__':
    if len(sys.argv) != 2:
        sys.exit(sys.argv[0] + ": Expecting one command line argument -- the integer for which to get the smallest factor")
    n = int(sys.argv[1])
    if n < 1:
        sys.exit(sys.argv[0] + ": Expecting a positive integer")
    
    smallest_prime_factor = get_smallest_prime_factor(n)
    
    if smallest_prime_factor is None:
        print(n)
    else:
        print(smallest_prime_factor)
```

Try running it:

    $ python3 smallest_factor.py 9
    3

Now try importing it as a module:

    $ python3
    >>> import smallest_factor

Notice that our code nested in the `if __name__ == '__main__'`
statement no longer runs when we import our file as a module!
Now we can use our `get_smallest_prime_factor` function outside
of the file it lives in. Try it:

    >>> smallest_factor.get_smallest_prime_factor(9)
    3

Also, we can use the `help` function, just like anything else in Python:

    >>> help(smallest_factor)
    >>> help(smallest_factor.get_smallest_prime_factor)

**NOTE:** `help` displays the help menu using the program `less`.
Hit the `q` key to exit the help menu.

Hmmm... our help messages are not very ... helpful. Let's fix that next.


# Best practice 3: Use docstrings to document your code

First, let's change our comment at the top of the script to a docstring.

Change

```python
#! /usr/bin/env python3

# A script for getting the smallest prime factor of an integer.
```

to

```python
#! /usr/bin/env python3

"A module for getting the smallest prime factor of an integer."
```

Next, let's add a docstring to our function:

```python
def get_smallest_prime_factor(n):
    """
    Returns the smallest integer that is a factor of `n`.
    
    If `n` is a prime number `None` is returned.

    Parameters
    ----------
    n : int
        The integer to be factored.

    Returns
    -------
    int or None
        The smallest integer that is a factor of `n`
        or None if `n` is a prime.
    """
    for i in range(2, n):
        if (n % i) == 0:
            return i
    return None
```

This docstring is a bit overkill for such a simple function, but
it demonstrates some good practices.

Now, let's check our help messages again:

    $ python3
    >>> import smallest_factor
    >>> help(smallest_factor)
    >>> help(smallest_factor.get_smallest_prime_factor)

Much better.


# Best practice 4: Add tests to your docstrings

We can even add examples to docstrings that can be run
as tests!!

```python
def get_smallest_prime_factor(n):
    """
    Returns the smallest integer that is a factor of `n`.
    
    If `n` is a prime number `None` is returned.

    Parameters
    ----------
    n : int
        The integer to be factored.

    Returns
    -------
    int or None
        The smallest integer that is a factor of `n`
        or None if `n` is a prime.

    Examples
    --------
    >>> get_smallest_prime_factor(7)
    >>> get_smallest_prime_factor(8)
    2
    >>> get_smallest_prime_factor(9)
    3
    """
    for i in range(2, n):
        if (n % i) == 0:
            return i
    return None
```

Now, you can run the examples as tests using the `doctest` module of Python:

    $ python3 -m doctest -v smallest_factor.py
    Trying:
        get_smallest_prime_factor(7)
    Expecting nothing
    ok
    Trying:
        get_smallest_prime_factor(8)
    Expecting:
        2
    ok
    Trying:
        get_smallest_prime_factor(9)
    Expecting:
        3
    ok
    1 items had no tests:
        tmp
    1 items passed all tests:
       3 tests in tmp.get_smallest_prime_factor
    3 tests in 2 items.
    3 passed and 0 failed.
    Test passed.
    
Nice! The examples provide useful information in the help menu for someone
(e.g., you 6 months from now) learning how to use your function.
Running the examples as tests help confirm the function is working
as expected.

Here's our `smallest_factor.py` code in its final form:

```python
#! /usr/bin/env python3

"A module for getting the smallest prime factor of an integer."

import sys


def get_smallest_prime_factor(n):
    """
    Returns the smallest integer that is a factor of `n`.
    
    If `n` is a prime number `None` is returned.

    Parameters
    ----------
    n : int
        The integer to be factored.

    Returns
    -------
    int or None
        The smallest integer that is a factor of `n`
        or None if `n` is a prime.

    Examples
    --------
    >>> get_smallest_prime_factor(7)
    >>> get_smallest_prime_factor(8)
    2
    >>> get_smallest_prime_factor(9)
    3
    """
    for i in range(2, n):
        if (n % i) == 0:
            return i
    return None

if __name__ == '__main__':
    if len(sys.argv) != 2:
        sys.exit(sys.argv[0] + ": Expecting one command line argument -- the integer for which to get the smallest factor")
    n = int(sys.argv[1])
    if n < 1:
        sys.exit(sys.argv[0] + ": Expecting a positive integer")
    
    smallest_prime_factor = get_smallest_prime_factor(n)
    
    if smallest_prime_factor is None:
        print(n)
    else:
        print(smallest_prime_factor)
```


# Extra challenges (i.e., not required)

-   The code in our `get_smallest_prime_factor` function is not very efficient.
    Can you improve it?
-   Write another script (module) that calculates and returns a list of prime
    numbers that are factors of an integer provided at the command line
    -   Make sure to `import smallest_factor` and use the
        `get_smallest_prime_factor` function in your new script.
    -   Make sure to follow the best practices above!


# Acknowledgments

## Support
This work was made possible by funding provided to [Jamie
Oaks](http://phyletica.org) from the National Science Foundation (DEB 1656004).


# License

<a rel="license" href="http://creativecommons.org/licenses/by/4.0/deed.en_US"><img alt="Creative Commons License" style="border-width:0" src="http://i.creativecommons.org/l/by/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by/4.0/deed.en_US">Creative Commons Attribution 4.0 International License</a>.
