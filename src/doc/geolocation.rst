.. _javascript.geolocation:

Geolocation - Get the user's location wiith OpenLayers
======================================================

Modern browsers can get the user's location if the user agrees. See the `GeoLocation <http://ol3js.org/en/master/examples/geolocation.html>`_ example of OpenLayers 3 to get an idea of how powerful this is. As you can see, the Geolocation API even allows to get altitude, heading and speed - depending on which sensors are available on the device.

Just the location
-----------------

For this introduction, we really only want to know the user's coordinates. Instead of dealing with the browser's native Geolocation API, we'll use OpenLayer. So let's load OpenLayers first (usually you just add a script tag to your html page, but here we do it programmatically so we can work on the console):

.. code-block:: javascript

    var script = document.createElement('script');
    script.src = 'http://ol3js.org/en/master/build/ol.js';
    document.head.appendChild(script);

After a few seconds, we will have OpenLayers available. Now let's configure a Geolocation instance:

.. code-block:: javascript

    var geolocation = new ol.Geolocation();
    geolocation.setTracking(true); // here the browser may ask for confirmation
    geolocation.on('change:position', function() {
      console.log(geolocation.getPosition());
    });

As you can see, we display the position whenever it has changed. This is done in an asynchronous manner through an event handler (``geolocation.on(...)``).