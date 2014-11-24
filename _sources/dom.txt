.. _javascript.dom:

Interacting with the DOM
========================

In a web applications, we usually need to react to user input, and change the
content of the page dynamically. JavaScript provides access to the Document Object Model (DOM) of the web page. Let's see how we can write to the web page, and how we can get user input.

Writing to the DOM
------------------

Let's say we want to have an element on our page, where we provide dynamic information to the user. We can either create an element in HTML (e.g. ``<div id="myElement"></div>``) and access it from JavaScript using ``document.getElementById('myElement')``, or we can create it programmatically:

.. code-block:: javascript

    var output = document.createElement('div');
    document.body.appendChild(output);

Then we can easily change the content of the element:

.. code-block:: javascript

    output.innerHTML = "Hello World";

Reading from forms
------------------

When we have a form element on a web page, we have access to its value. Usually we want to get the value when the user submits the form. This is done by using an event handler. Let's create a form inside the div from the previous snippet (we could do that in the html too):

.. code-block:: javascript

    output.innerHTML = '<form id="form"><input type="text" name="text"></input></form>';

Now let's log the value of the "text" field whenever the form is submitted (i.e. the user presses ``Enter`` after entering a form value):

.. code-block:: javascript

    var form = document.getElementById('form');
    form.onsubmit = function(evt) {
      console.log(form.text.value);
      evt.preventDefault();
    };

``onsubmit`` is the event that occurs whenever the user submits the form. Like all DOM event handlers, the function is called with the event object as argument. If we want to prevent the form from being submitted to a server (the default behaviour), we have to call ``evt.preventDefault()`` to prevent that default behaviour.