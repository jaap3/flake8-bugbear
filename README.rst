==============
flake8-bugbear
==============

.. image:: https://travis-ci.org/PyCQA/flake8-bugbear.svg?branch=master
    :target: https://travis-ci.org/PyCQA/flake8-bugbear

.. image:: https://img.shields.io/badge/code%20style-black-000000.svg
    :target: https://github.com/psf/black

A plugin for Flake8 finding likely bugs and design problems in your
program.  Contains warnings that don't belong in pyflakes and
pycodestyle::

    bug·bear  (bŭg′bâr′)
    n.
    1. A cause of fear, anxiety, or irritation: *Overcrowding is often
       a bugbear for train commuters.*
    2. A difficult or persistent problem: *"One of the major bugbears of
       traditional AI is the difficulty of programming computers to
       recognize that different but similar objects are instances of the
       same type of thing" (Jack Copeland).*
    3. A fearsome imaginary creature, especially one evoked to frighten
       children.

It is felt that these lints don't belong in the main Python tools as they
are very opinionated and do not have a PEP or standard behind them. Due to
``flake8`` being designed to be extensible, the original creators of these lints
believed that a plugin was the best route. This has resulted in better development
velocity for contributors and adaptive deployment for ``flake8`` users.

Installation
------------

Install from ``pip`` with:

.. code-block:: sh

     pip install flake8-bugbear

It will then automatically be run as part of ``flake8``; you can check it has
been picked up with:

.. code-block:: sh

    $ flake8 --version
    3.5.0 (assertive: 1.0.1, flake8-bugbear: 18.2.0, flake8-comprehensions: 1.4.1, mccabe: 0.6.1, pycodestyle: 2.3.1, pyflakes: 1.6.0) CPython 3.7.0 on Darwin

Development
-----------

If you'd like to do a PR we have development instructions `here <https://github.com/PyCQA/flake8-bugbear/blob/master/DEVELOPMENT.md>`_.

List of warnings
----------------

**B001**: Do not use bare ``except:``, it also catches unexpected events
like memory errors, interrupts, system exit, and so on.  Prefer ``except
Exception:``.  If you're sure what you're doing, be explicit and write
``except BaseException:``.  Disable E722 to avoid duplicate warnings.

**B002**: Python does not support the unary prefix increment. Writing
``++n`` is equivalent to ``+(+(n))``, which equals ``n``. You meant ``n
+= 1``.

**B003**: Assigning to ``os.environ`` doesn't clear the
environment.  Subprocesses are going to see outdated
variables, in disagreement with the current process.  Use
``os.environ.clear()`` or the ``env=``  argument to Popen.

**B004**: Using ``hasattr(x, '__call__')`` to test if ``x`` is callable
is unreliable.  If ``x`` implements custom ``__getattr__`` or its
``__call__`` is itself not callable, you might get misleading
results.  Use ``callable(x)`` for consistent results.

**B005**: Using ``.strip()`` with multi-character strings is misleading
the reader. It looks like stripping a substring. Move your
character set to a constant if this is deliberate. Use
``.replace()`` or regular expressions to remove string fragments.

**B006**: Do not use mutable data structures for argument defaults.  They
are created during function definition time. All calls to the function
reuse this one instance of that data structure, persisting changes
between them.

**B007**: Loop control variable not used within the loop body.  If this is
intended, start the name with an underscore.

**B008**: Do not perform function calls in argument defaults.  The call is
performed only once at function definition time. All calls to your
function will reuse the result of that definition-time function call.  If
this is intended, assign the function call to a module-level variable and
use that variable as a default value.

**B009**: Do not call ``getattr(x, 'attr')``, instead use normal
property access: ``x.attr``. Missing a default to ``getattr`` will cause
an ``AttributeError`` to be raised for non-existent properties. There is
no additional safety in using ``getattr`` if you know the attribute name
ahead of time.

**B010**: Do not call ``setattr(x, 'attr', val)``, instead use normal
property access: ``x.attr = val``. There is no additional safety in
using ``setattr`` if you know the attribute name ahead of time.

**B011**: Do not call ``assert False`` since ``python -O`` removes these calls.
Instead callers should ``raise AssertionError()``.

**B012**: Use of ``break``, ``continue`` or ``return`` inside ``finally`` blocks will
silence exceptions or override return values from the ``try`` or ``except`` blocks.
To silence an exception, do it explicitly in the ``except`` block. To properly use
a ``break``, ``continue`` or ``return`` refactor your code so these statements are not
in the ``finally`` block.

**B013**: A length-one tuple literal is redundant.  Write ``except SomeError:``
instead of ``except (SomeError,):``.

**B014**: Redundant exception types in ``except (Exception, TypeError):``.
Write ``except Exception:``, which catches exactly the same exceptions.

**B015**: Pointless comparison. This comparison does nothing but
waste CPU instructions. Either prepend ``assert`` or remove it.

**B016**: Cannot raise a literal. Did you intend to return it or raise
an Exception?

**B017**: ``self.assertRaises(Exception):`` should be considered evil. It can lead
to your test passing even if the code being tested is never executed due to a typo.
Either assert for a more specific exception (builtin or custom), use
``assertRaisesRegex``, or use the context manager form of assertRaises
(``with self.assertRaises(Exception) as ex:``) with an assertion against the
data available in ``ex``.


Opinionated warnings
~~~~~~~~~~~~~~~~~~~~

The following warnings are disabled by default because they are
controversial.  They may or may not apply to you, enable them explicitly
in your configuration if you find them useful.  Read below on how to
enable.

**B901**: Using ``return x`` in a generator function used to be
syntactically invalid in Python 2. In Python 3 ``return x`` can be used
in a generator as a return value in conjunction with ``yield from``.
Users coming from Python 2 may expect the old behavior which might lead
to bugs.  Use native ``async def`` coroutines or mark intentional
``return x`` usage with ``# noqa`` on the same line.

**B902**: Invalid first argument used for method. Use ``self`` for
instance methods, and ``cls`` for class methods (which includes ``__new__``
and ``__init_subclass__``) or instance methods of metaclasses (detected as
classes directly inheriting from ``type``).

**B903**: Use ``collections.namedtuple`` (or ``typing.NamedTuple``) for
data classes that only set attributes in an ``__init__`` method, and do
nothing else. If the attributes should be mutable, define the attributes
in ``__slots__`` to save per-instance memory and to prevent accidentally
creating additional attributes on instances.

**B904**: Within an ``except`` clause, raise exceptions with ``raise ... from err``
or ``raise ... from None`` to distinguish them from errors in exception handling.
See [the exception chaining tutorial](https://docs.python.org/3/tutorial/errors.html#exception-chaining)
for details.

**B950**: Line too long. This is a pragmatic equivalent of
``pycodestyle``'s E501: it considers "max-line-length" but only triggers
when the value has been exceeded by **more than 10%**. You will no
longer be forced to reformat code due to the closing parenthesis being
one character too far to satisfy the linter. At the same time, if you do
significantly violate the line length, you will receive a message that
states what the actual limit is. This is inspired by Raymond Hettinger's
`"Beyond PEP 8" talk <https://www.youtube.com/watch?v=wf-BqAjZb8M>`_ and
highway patrol not stopping you if you drive < 5mph too fast. Disable
E501 to avoid duplicate warnings.


How to enable opinionated warnings
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To enable these checks, specify a ``--select`` command-line option or
``select=`` option in your config file.  As of Flake8 3.0, this option
is a whitelist (checks not listed are being implicitly disabled), so you
have to explicitly specify all checks you want enabled. For example::

	[flake8]
	max-line-length = 80
	max-complexity = 12
	...
	ignore = E501
	select = C,E,F,W,B,B901

Note that we're enabling the complexity checks, the PEP8 ``pycodestyle``
errors and warnings, the pyflakes fatals and all default Bugbear checks.
Finally, we're also specifying B901 as a check that we want enabled.
Some checks might need other flake8 checks disabled - e.g. E501 must be
disabled for B950 to be hit.

If you'd like all optional warnings to be enabled for you (future proof
your config!), say ``B9`` instead of ``B901``. You will need Flake8 3.2+
for this feature.

Note that ``pycodestyle`` also has a bunch of warnings that are disabled
by default.  Those get enabled as soon as there is an ``ignore =`` line
in your configuration.  I think this behavior is surprising so Bugbear's
opinionated warnings require explicit selection.


Tests
-----

Just run::

    coverage run tests/test_bugbear.py


License
-------

MIT


Change Log
----------

21.9.1
~~~~~~

* Update B008: Whitelist more immutable function calls (#173)
* Remove Python Compatibility Warnings (#182)
* Add B904: check for ``raise`` without ``from`` in an ``except`` clause (#181)
* Add Python 3.10 tests to ensure we pass (#183)

21.4.3
~~~~~~

* Verify the element in item_context.args is of type ast.Name for b017

21.4.2
~~~~~~

* Add another hasattr() check to b017 visit for .func

21.4.1
~~~~~~

* Add B017: check for gotta-catch-em-all assertRaises(Exception)

21.3.2
~~~~~~

* Fix crash on tuple expansion in try/except block (#161)

21.3.1
~~~~~~

* Fix grammar in B015 (#150)
* Make sure float infinity/NaN does not trigger B008 (#155)
* Handle positional-only args in class methods (#158)

20.11.1
~~~~~~~~~~~~

* Support exception aliases properly in B014 (#129)
* Add B015: Pointless comparison (#130)
* Remove check for # noqa comments (#134)
* Ignore exception classes which are not types (#135)
* Introduce B016 to check for raising a literal. (#141)
* Exclude types.MappingProxyType() from B008. (#144)

20.1.4
~~~~~~

* Ignore keywords for B009/B010

20.1.3
~~~~~~

* Silence B009/B010 for non-identifiers
* State an ignore might be needed for optional B9x checks

20.1.2
~~~~~~

* Fix error on attributes-of-attributes in `except (...):` clauses

20.1.1
~~~~~~

* Allow continue/break within loops in finally clauses for B012
* For B001, also check for ``except ():``
* Introduce B013 and B014 to check tuples in ``except (..., ):`` statements

20.1.0
~~~~~~

* Warn about continue/return/break in finally block (#100)
* Removed a colon from the descriptive message in B008. (#96)

19.8.0
~~~~~~

* Fix .travis.yml syntax + add Python 3.8 + nightly tests
* Fix `black` formatting + enforce via CI
* Make B901 not apply to __await__ methods

19.3.0
~~~~~~

* allow 'mcs' for metaclass classmethod first arg (PyCharm default)
* Introduce B011
* Introduce B009 and B010
* Exclude immutable calls like tuple() and frozenset() from B008
* For B902, the first argument for metaclass class methods can be
  "mcs", matching the name preferred by PyCharm.

18.8.0
~~~~~~

* black format all .py files
* Examine kw-only args for mutable defaults
* Test for Python 3.7

18.2.0
~~~~~~

* packaging fixes


17.12.0
~~~~~~~

* graduated to Production/Stable in trove classifiers

* introduced B008

17.4.0
~~~~~~

* bugfix: Also check async functions for B006 + B902

17.3.0
~~~~~~

* introduced B903 (patch contributed by Martijn Pieters)

* bugfix: B902 now enforces `cls` for instance methods on metaclasses
  and `metacls` for class methods on metaclasses

17.2.0
~~~~~~

* introduced B902

* bugfix: opinionated warnings no longer invisible in Syntastic

* bugfix: opinionated warnings stay visible when --select on the
  command-line is used with full three-digit error codes

16.12.2
~~~~~~~

* bugfix: opinionated warnings no longer get enabled when user specifies
  ``ignore =`` in the configuration.  Now they require explicit
  selection as documented above also in this case.

16.12.1
~~~~~~~

* bugfix: B007 no longer crashes on tuple unpacking in for-loops

16.12.0
~~~~~~~

* introduced B007

* bugfix: remove an extra colon in error formatting that was making Bugbear
  errors invisible in Syntastic

* marked as "Beta" in trove classifiers, it's been used in production
  for 8+ months

16.11.1
~~~~~~~

* introduced B005

* introduced B006

* introduced B950

16.11.0
~~~~~~~

* bugfix: don't raise false positives in B901 on closures within
  generators

* gracefully fail on Python 2 in setup.py

16.10.0
~~~~~~~

* introduced B004

* introduced B901, thanks Markus!

* update ``flake8`` constraint to at least 3.0.0

16.9.0
~~~~~~

* introduced B003

16.7.1
~~~~~~

* bugfix: don't omit message code in B306's warning

* change dependency on ``pep8`` to dependency on ``pycodestyle``, update
  ``flake8`` constraint to at least 2.6.2

16.7.0
~~~~~~

* introduced B306

16.6.1
~~~~~~

* bugfix: don't crash on files with tuple unpacking in class bodies

16.6.0
~~~~~~

* introduced B002, B301, B302, B303, B304, and B305

16.4.2
~~~~~~

* packaging herp derp

16.4.1
~~~~~~

* bugfix: include tests in the source package (to make ``setup.py test``
  work for everyone)

* bugfix: explicitly open README.rst in UTF-8 in setup.py for systems
  with other default encodings

16.4.0
~~~~~~

* first published version

* date-versioned


Authors
-------

Glued together by `Łukasz Langa <mailto:lukasz@langa.pl>`_. Multiple
improvements by `Markus Unterwaditzer <mailto:markus@unterwaditzer.net>`_,
`Martijn Pieters <mailto:github.com@zopatista.com>`_,
`Cooper Lees <mailto:me@cooperlees.com>`_, and `Ryan May <mailto:rmay31@gmail.com>`_.
