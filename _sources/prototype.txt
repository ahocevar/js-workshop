.. _javascript.prototype:

JavaScript, a Prototype-based Language
======================================

JavaScript, although the name might make us think so, is not a scripting flavor
of Java. It is influenced by Java, and became an industry standard under the
name of
`ECMAScript <http://www.ecma-international.org/publications/standards/Ecma-262.htm>`_
soon after its creation in 1995.

JavaScript can be characterized as a prototype-based language that is weakly
typed and dynamic. In addition to object or prototype-based programming, it
supports the scripting, imperative and functional paradigms.


Variables and Data Types
------------------------

JavaScript is a dynamic language, and any variable can hold any data type. In
other words: JavaScript uses typed values, not typed variables.

What's more important about variables is that they are simply defined using the
:token:`var` keyword. If this keyword is omitted, the variable will be global.
It is strongly recommended to always define a variable using the var keyword.
JavaScript also has a concept of inner functions - functions can be nested.
Using the :token:`var` keyword in an inner function will create the variable in
the scope of the inner function. Outer functions won't have access to that
variable, but the inner function will have access to variables of all its outer
functions. Here is some code to show what this means:

.. code-block:: javascript

    var a;
    var outer = function() {
        a = 5; // global
        function inner() {
            console.log(window.a); // 5 - access global through root object
            console.log(a); // undefined, global unavailable because of local a
            var a = "foo"; // local variable a - does not affect global a
            b = 10; // no var keyword - global variable
            var c = true; // local variable c - only available inside this function
            console.log(a); // "foo"
            console.log(b); // 10
            console.log(c); // true
        }
        inner(); // execute the inner function
        console.log(a); // 5
        console.log(b); // 10
        console.log(c); // reference error - c is not defined
        console.log(window.c); // undefined. In a browser, window is the global scope.
    };
    outer(); // execute the outer function
    console.log(a); // displays 5

Wonder why the first :samp:`console.log(a)` in the inner function returns
:token:`undefined`? When a variable a is defined in the scope of a function,
any outer variable with the same name won't be available, to protect it.

In the above code snippet, we have already used all the 5 data types:

* :token:`Number`: A number, no matter if e.g. integer or float. The global
  variable :samp:`a` and :samp:`b`.

* :token:`String`: The inner function's :samp:`a`.

* :token:`Boolean`: :samp:`c`.
 
* :token:`Object`: The root object. All other objects inherit from
  :token:`Object`. :samp:`window`.

* :token:`Function`: First-class object, directly inheriting form the Object
  object. :samp:`inner` and :samp:`outer`. Functions are declared with the
  :token:`function` keyword. If followed by a name, the function will be
  assigned to a local variable of that name (see :samp:`inner` above). If
  directly followed by the implementation, it will be an anonymous function,
  which can be assigned to a variable (see :samp:`outer` above). Functions have
  an execution context (scope), which can be referenced inside the function
  using the :samp:`this` keyword.

JavaScript makes a difference between properties or variables that are
uninitialized (:token:`undefined`) or intentionally set to :token:`null`.


Truth, Equality and Type Casting
--------------------------------

As a weakly typed language, JavaScript has the advantage that you can check
apples and oranges for equality, using the :token:`==` operator (:token:`===`
does a strict check for equality). But there are also common pitfalls with
this. See the following examples:

.. code-block:: javascript

    console.log(0 == false); // true
    console.log("0" == false); // true
    console.log("" == false); // true
    console.log(1 == "1"); // true
    console.log(1 == true); // true
    console.log("1" == true); // true
    console.log("2" == true); // false

The above examples are all checks for equality, with implicit type casting to
make the values comparable. Wonder why the last one is not true? Because "2" is
not cast to :token:`Boolean` to make it comparable with :token:`true`. Instead,
both sides must be either cast to :token:`Number` (:samp:`2 == 1`) or
:token:`String` (:samp:`"2" == "true"`). This is why I recommend to use the
strict :token:`===` comparison operator.

Type casting to :token:`Boolean`, which happens e.g. in :token:`if` statements
without comparison operators (:samp:`if ("foo") { /* do something */ }`), has
some surprises. When something evaluates to false, we talk about falsey,
otherwise about truthy. There are five values in addition to :token:`false`
that are falsey:

.. code-block:: javascript

    console.log(Boolean(undefined));
    console.log(Boolean(null));
    console.log(Boolean(NaN));
    console.log(Boolean(0));
    console.log(Boolean(""));

A typical mistake is to do a truthy check to see whether an argument was
provided:

.. code-block:: javascript

    function doIfSet(value) {
        if (value) {
            console.log("doing something");
        }
    }
    doIfSet(0);

If our intention was to see "doing something" on the console when doIfSet was
called with an argument, then we'll be disappointed, because the
:command:`console.log` will not be executed. Instead, we should have done the
following:

.. code-block:: javascript

    function doIfSet(value) {
        if (value !== undefined) {
            console.log("doing something");
        }
    }
    doIfSet(0);
    

Closures
--------

When a reference to an inner function is kept beyond its outer function, we
talk about a closure. Accidental closures can cause memory leaks when they are
created too often. But on the other hand, closures are a very powerful feature
of JavaScript:

.. code-block:: javascript

    function outer(name) {
        var count = 0;
        return function() {
            count++;
            console.log(name + " called " + count + " times.");
        }
    }
    var john = outer("John");
    var jeff = outer("Jeff");
    john(); // "John called 1 times."
    jeff(); // "Jeff called 1 times."
    john(); // "John called 2 times."

In this example, the function that :samp:`outer` returns is a closure. Memory
will be allocated any time :samp:`outer` is called, and a new function with a
private variable :samp:`count` and an implicit private variable :samp:`name`
from the argument of the outer function will be returned. These private
variables cannot be accessed from outside the outer function, but are exposed
through the closure function.

Let's look at an example for an accidental closure that can cause memory leaks:

.. code-block:: javascript

    function addClickHandler(el) {
        el.onclick = function() {
            console.log(el.textContent + " clicked.");
        };
    }

    var listItems = document.getElementsByTagName("li");
    for (var i=0, ii=listItems.length; i<ii; ++i) {
        addClickHandler(listItems[i]);
    }

The intention of the above was to add behavior to all :token:`li` (list item)
elements of a web page, while keeping the handler function private. Now if a
page has hundreds of list elements, the click handler function that
:token:`addClickHandler` returns will be generated hundreds of times. Memory
needs to be allocated for each. To avoid this, the above example should be
modified to look like this:

.. code-block:: javascript

    var addClickHandler = (function() {
        function clickHandler() {
            console.log(this.textContent + " clicked.");
        }
        return function(el) {
            el.onclick = clickHandler;
        }
    })();

    var listItems = document.getElementsByTagName("li");
    for (var i=0, ii=listItems.length; i<ii; ++i) {
        addClickHandler(listItems[i]);
    }

Instead of creating a closure with every call of :token:`addClickHandler`, we
now create a single closure - the :token:`addClickHandler` function - by
executing an anonymous function
(:samp:`(function() { /* do something */ })()`). Since event handlers for DOM
elements are called in the scope of the DOM element, we can use the
:samp:`this` keyword to access the element inside the handler function.


Inheritance
-----------

JavaScript frameworks sometimes provide easy ways to create classes. But to understand how inheritance works, we'll take a glance at how it is handled in pure JavaScript:

.. code-block:: javascript

    function Creature(name) {
        this.name = name;
        this.alive = false;
    }
    Creature.prototype.live = function() {
        this.alive = true;
    };
    Creature.prototype.die = function() {
        this.alive = false;
    };
    
    function Animal(name) {
        this.hungry = true;
        // call the constructor of the superclass
        Creature.prototype.constructor.apply(this, arguments);
    };
    Animal.prototype = new Creature; // inherit from Creature
    Animal.prototype.constructor = Animal; // don't use Creature's constructor
    Animal.prototype.eat = function() {
        if (this.alive) {
            this.hungry = false;
        }
    };
    Animal.prototype.die = function() {
        this.hungry = false;
        Creature.prototype.die.apply(this, arguments);
    };

The above creates a class :samp:`Creature`. A creature has a name, can live and
die, and we can query the creature for its alive status. :samp:`Animal` is a
subclass of :samp:`Creature`, which is hungry and can eat if it is alive.

As we can see, inheritance in JavaScript works by making the prototype of the
subclass an instance of its superclass. To make sure that we have a correct
inheritance chain, we can use the :token:`instanceof` keyword:

.. code-block:: javascript

    var creature = new Creature("myCreature");
    console.log(creature instancoef Creature); // true
    console.log(creature instanceof Animal); // false
    var animal = new Animal("myAnimal");
    console.log(animal instanceof Animal); // true
    console.log(animal instanceof Creature); // true