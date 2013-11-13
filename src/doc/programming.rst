.. _javascript.programming:

Programming basics explained with JavaScript
============================================

Programming is a skill that requires your brain to be tuned into a special mode. It is similar to sitting in front of a pile of toy blocks, trying to build an arch:

* Creativity to build something you want with what you have.
* Anylatic capabilities to examine the different blocks and see where they fit. * Patience to pursue your goal through experimenting with different approaches until you are satisfied.

.. figure:: arch.jpg

    Credit: `zenblocks <http://www.etsy.com/shop/zenblocks>`_

The building blocks of most programming languages are similar, and are different only in their syntax. Here we will get familiar with the building blocks in JavaScript.

Getting started
---------------

JavaScript is the language of the web, so every browser is able to execute it. Most browsers come with something called like "Develpment Tools". These tools include a console, and all snippets in this chapter can be tried out in the console by simply typing them at the '>' prompt.

.. code-block:: javascript

    console.log("Hello World"); // prints "Hello World"

This prints "Hello World" to the console. Congratulations, you just successfully executed your first line of JavaScript. Note the ``//``. This precedes comments - things that the machine does not read, but are important to
the developer. For comments that span more than a single line, use this:

.. code-block:: javascript

   /* This is a comment
      longer than one line */

Operators - Math, but not only
------------------------------

Basic operators
~~~~~~~~~~~~~~~

To add and to subtract are the most basic tasks that are expressed with operators:

.. code-block:: javascript

    console.log(1 + 2);
    console.log("Hello" + " " + "World");
    console.log(5 - 3);

Obviously the ``+`` operator is also used to concatenate strings (coding jargon for text).

Other math operators include ``*`` (multiplication), ``/`` (division) and ``%`` (modulo).

Comparison operators
~~~~~~~~~~~~~~~~~~~~

These include ``==`` (equals), ``===`` (identity), ``<`` (less than), ``>`` (greater than), ``<=`` (less or equal than), ``>=`` (greater or equal than).

Comparisons evaluate to `true` or `false`. These so-called boolean values are very important in programming, because they are used together with conditions that can control the flow of execution.

Variables
---------

Variables are key to programming. They are used to store values for later use. The ``=`` operator is used to assign values to variables.

.. code-block:: javascript

    var a = 1;
    var b = 2;
    console.log(a + b);
    a = a + 1; // can also be written as "a += 1" or "++a"
    console.log

Flow of execution
-----------------

Conditional execution
~~~~~~~~~~~~~~~~~~~~~

When something is to be executed conditionally, the ``if``-Statement is used. There is also an ``else`` to handle the case where the condition is not met.

.. code-block:: javascript

    var a = Math.random(); // generates a value between 0 and 1
    if (a > 0.5) { // curly braces wrap a block
      console.log("The random number is greater than 0.5");
    } else {
      console.log("The random number is 0.5 or less");
    }

The above code will examine the random number assigned to the variable a, and print a message saying whether the number was greater than 0.5 or not.

Iterate over a block
~~~~~~~~~~~~~~~~~~~~

The most commonly used iteration method is the ``for`` loop. It can create an
iterator, which is usually an increasing number:

.. code-block:: javascript

    for (var i = 1; i <= 5; ++i) {
      console.log(i);
    }

This prints the numbers from 1 to 5 to the console. We will see other flavours of the ``for`` loop below.

Data types
----------

JavaScript is very straightforward when it comes to data types. The most important ones are:

* Number: ``1``, ``2`` etc.
* String: ``"foo"``, ``'bar'`` etc. - strings are wrapped in ``'`` or ``"``.
* Boolean: ``true``, ``false``
* Object: Key-value pairs, e.g. ``{'foo': 'bar', 'n': 1}``
* Array: List of values, e.g. ``[1, "a", 2, "b"]``

Getting values from objects or arrays
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

There are different ways. Here is what we can do:

.. code-block:: javascript

    var a = {'foo': 'bar', 'n': 1};
    console.log(a.foo); // 'bar'
    console.log(a['n']); // 1
    for (var key in a) { console.log(a[key]); }; // Two lines: 'bar' and 1.
    
    var b = [1, "a", 2, "b"];
    console.log(b[0]); // 1
    console.log(b[3]); // "b"
    for (var i = b.length - 1; i >= 0; --i) { console.log(b[i]); }; // Guess it!

Functions
---------

Functions contain blocks of code, and can be passed arguments when they are called. We already saw a function built into JavaScript above (``Math.random()``), and here is a custom one with an argument:

.. code-block:: javascript

    function hello(name) {
      // name is the argument
      console.log('Hello ' + name);
    }
    hello('John'); // prints 'Hello John'

.. rubric:: Task

Now that you are familiar with the concepts, implement the "FizzBuzz" exercise:

Write a program that prints the numbers from 1 to 100. But for multiples of three print "Fizz" instead of the number and for the multiples of five print "Buzz". For numbers which are multiples of both three and five print "FizzBuzz". (Credit: `codinghorror.com <http://www.codinghorror.com/blog/2007/02/why-cant-programmers-program.html>`_)