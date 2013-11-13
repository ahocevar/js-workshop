.. _javascript.ajax:

AJAX - Working with remote data
===============================

AJAX is short for "Asynchronous JavaScript and XML". It is a way for a web application to load data through the browser's XMLHttpRequest API. The original idea of the XMLHttpRequest API was to load XML documents, but with JSON (JavaScript Object Notation) becoming popular, JSON is now the #1 payload handled by XMLHttpRequest.

Let's look at the parts of this concept in more detail, using an example.

The Example
-----------

OpenStreetMap offers the Nominatim API for geocoding. Let's use the API to find the coordinates of the Vienna University of Technology. This is the request url:

.. code-block:: javascript

    var url = 'http://nominatim.openstreetmap.org/search?q=Vienna+University+of+Technology&format=json';

XMLHttpRequest (XHR)
--------------------

This is the API we use to send the request to the server:

.. code-block:: javascript

    var xhr = new XMLHttpRequest();
    xhr.open("GET", url, true);

Look at the ``xhr.open("GET", url, true)`` line. "GET" is the HTTP verb to use (could also be "POST", "PUT" or "DELETE"). "GET" is the kind of request that your browser also sends when you type a URL in the address bar. And the ``true`` argument after the url means that we want the request to be sent asynchronously.

Asynchronous
------------

This is important, because using XHR with synchronous request freezes the browser until the response is available. When communicating with slow servers or loading lots of data, this can be a long time. So never use synchronous requests!

However, dealing with asynchronous requests means that we do not have a continuos flow of operation any more. Let's look at what this means:

.. code-block:: javascript

    var result;
    xhr.onload = function() {
      result = JSON.parse(xhr.responseText);
      console.log(result[0].lat, result[0].lon);
    };
    xhr.send();
    console.log(result);

Look at the last line of this snippet. What will be the value of the ``result`` here? Right. It will be ``undefined``, because the response from the server is not here yet. So whatever we want to do with the response, we have to do it in the ``onload`` handler of the XHR.