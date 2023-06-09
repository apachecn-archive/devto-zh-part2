# Python Peeps:对于单元测试，你什么时候更喜欢使用 Unittest、Doctest、Pytest？

> 原文：<https://dev.to/nemessisc/python-peeps-unit-testing-when-do-you-prefer-to-use-unittest-doctest-pytest--2ma7>

下面分别列出了 Unittest、Doctest 和 Pytest 的例子，如果你不熟悉它们的话。

```
# Testing Framework:
# Unittest supports test automation by writing classes. 
import unittest

def fib(n):
    """Return the n-th number in the fibonacci series."""
    if n <= 0:
        return 0
    elif n == 1:
        return 1
    return fib(n - 1) + fib(n - 2)

class TestFib(unittest.TestCase):

    def test_fib_series(self):
        self.assertEqual(fib(0), 0)
        self.assertEqual(fib(1), 1)
        self.assertEqual(fib(2), 1)
        self.assertEqual(fib(3), 2)
        self.assertEqual(fib(4), 3)
        self.assertEqual(fib(5), 5)

if __name__ == "__main__":
    # normally this is unittest.main()...this is for jupyter
    unittest.main(argv=['first-arg-is-ignored'], exit=False, verbosity=2) 
```

Enter fullscreen mode Exit fullscreen mode

```
# Testing Framework:
# Doctest allows you to write tests in the docstrings 
def fib(n):
    """Return the n-th number in the fibonacci series.

    >>> fib(0)
    0
    >>> fib(1)
    1
    >>> fib(29)
    514229
    """
    if n <= 0:
        return 0
    elif n == 1:
        return 1
    return fib(n - 1) + fib(n - 2)

if __name__ == "__main__":
    import doctest

    # doctest.testmod()
    doctest.testmod(verbose=True) 
```

Enter fullscreen mode Exit fullscreen mode

```
#Testing framework pytest
# Test discovery: for doctests, unittests and pytests. 
import pytest

%%run_pytest[clean] -vvv

def fib(n):
    """Return the n-th number in the fibonacci series."""
    if n <= 0:
        return 0
    elif n == 1:
        return 1
    return fib(n-1) + fib(n-2)

def test_fib_series():
    assert fib(0) == 0
    assert fib(1) == 1
    assert fib(2) == 1
    assert fib(3) == 2
    assert fib(4) == 3
    assert fib(5) == 5 
```

Enter fullscreen mode Exit fullscreen mode