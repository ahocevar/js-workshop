.. _geoext.javascript.concepts:

Core Concepts of Ext JS, OpenLayers and GeoExt
==============================================

In this chapter, we will get familiar with some basic concepts. We will do this
by looking at code snippets. To try out the code instantly, these snippets
either have a jsFiddle link above the snippet, or this `generic jsFiddle
<http://jsfiddle.net/m7fJN/1/>`_ which includes Ext JS, OpenLayers and GeoExt can
be used to paste code and run it.


Documentation and Examples
--------------------------

In addition to this workshop, the best way to learn Ext JS, OpenLayers and
GeoExt is to start with the tutorials. Once you have understood the basic
concepts, the example collections and the API reference will accompany you in
your coding endeavors. For OpenLayers and GeoExt, OpenGeo offers additional
workshop materials at http://workshops.opengeo.org/.

Ext JS
~~~~~~

* Tutorials: http://www.sencha.com/learn/extjs/#3x/tutorial/
* Examples: http://dev.sencha.com/deploy/ext-3.4.0/examples/
* API Reference: http://docs.sencha.com/ext-js/3-4/

OpenLayers
~~~~~~~~~~

* Tutorials: http://docs.openlayers.org/
* Examples: http://openlayers.org/dev/examples/
* API Reference: http://dev.openlayers.org/apidocs/files/OpenLayers-js.html

GeoExt
~~~~~~

* Tutorials: http://geoext.org/tutorials/
* Examples: http://geoext.org/examples.html
* API Reference: http://geoext.org/lib/


Integration with (other) JavaScript Frameworks
----------------------------------------------

There are many all-purpose JavaScript frameworks for making DOM manipulation
and other common tasks easier. Both
`Ext JS <http://www.sencha.com/products/extjs/>`_ and OpenLayers can co-exist
with such frameworks.

The default core framework for Ext JS is
`Ext Core <http://www.sencha.com/products/extcore/>`_. When using Ext JS in a
web page, two script resources have to be included: an adapter and the main
library:

.. code-block:: html

    <script src="http://extjs.cachefly.net/ext-3.4.0/adapter/ext/ext-base.js"></script>
    <script src="http://extjs.cachefly.net/ext-3.4.0/ext-all.js"></script>

When working with another all-purpose JavaScript framework (e.g.
`JQuery <http://jquery.org/>`_ ), the adapter needs to be replaced:

.. code-block:: html

    <script src="http://extjs.cachefly.net/ext-3.4.0/adapter/jquery/ext-jquery-adapter.js"></script>
    <script src="http://extjs.cachefly.net/ext-3.4.0/ext-all.js"></script>

`OpenLayers <http://openlayers.org/>`_ originally had basic convenience
functionality from the `Prototype <http://prototypejs.org/>`_ framework
included. Today, only the `$ <http://api.prototypejs.org/dom/dollar/>`_
function is left in the global scope, and it won't overwrite an existing ``$``
function (e.g. the one from JQuery). Other functions have moved from native
type overrides to the ``OpenLayers`` namespace, e.g.
``OpenLayers.Function.bind`` instead of ``Function.prototype.bind``. This makes
OpenLayers framework agnostic, so combining it with popular frameworks like
JQuery or Ext JS works flawlessly.

`GeoExt <http://geoext.org>`_ requires Ext JS and OpenLayers and is not
negatively affected by other JavaScript frameworks.


High Level Events
-----------------

Both Ext JS and OpenLayers come with their own event handling components.
Depending on the context, these components provide access to low level browser
events, and add high level events that are documented along with the API
documentation.

It is good practice to listen to high level events wherever possible. When
working with Ext JS and OpenLayers, listening to low level browser events is
usually not necessary.

In Ext JS, all components that fire events inherit from `Ext.util.Observable
<http://docs.sencha.com/ext-js/3-4/#!/api/Ext.util.Observable>`_. Let's look
at a combo box with an event handler for its ``change`` event:

.. code-block:: javascript

    new Ext.form.ComboBox({
        renderTo: document.body,
        store: ["one", "two"],
        triggerAction: "all",
        listeners: {
            "select": function(combo) {
                alert("You have selected " + combo.getValue());
            }
        }
    });

OpenLayers is a bit different. To enable a component to fire events, an
`OpenLayers.Event
<http://dev.openlayers.org/apidocs/files/OpenLayers/Events-js.html>`_ instance
is added to it. Let's create a map that fires an event whenever the zoom level
is changed:

.. code-block:: html

    <div id="map" style="width:256px;height:256px"></div>
    <script type="text/javascript">
        new OpenLayers.Map({
            div: "map",
            layers: [new OpenLayers.Layer.OSM()],
            eventListeners: {
                "zoomend": function() {
                    alert("The zoom level is now " + this.getZoom());
                },
                scope: this
            },
            zoom: 0,
            center: [0,0]
        });
    </script>

Note the optional :obj:`scope` option in the :obj:`eventListeners` definition:
this is the execution context (scope) for the handler function. The same works
in Ext JS. The default execution context is the component that fires the event.
In OpenLayers, this is the component that holds the :obj:`OpenLayers.Event`
instance - the map in the above snippet.

Event listeners in Ext JS are called with an arbitrary number of arguments,
whereas OpenLayers listeners are always called with a single object literal as
argument. The arguments are documented in the API reference.


DOM vs. JavaScript Centric Design
---------------------------------

There are different paradigms for developing web applications, which can
roughly be characterized on DOM centric or JavaScript centric. To understand
the difference, let's look at a snippet that generates a drop-down form field
using a DOM centric approach (`jsFiddle <http://jsfiddle.net/qT4Sq/>`_):

.. code-block:: html

    <select id="dropdown">
        <option value="1">one</option>
        <option value="2" selected="selected">two</option>
        <option value="3">three</option>
    </select>
    <button id="getresult">Get Result</button>
    <script type="text/javascript">
        document.getElementById("getresult").onclick = function() {
            var dropdown = document.getElementById("dropdown");
            alert(dropdown.options[dropdown.selectedIndex].value);
        }
    </script>

Now a JavaScript centric approach for the same drop-down could look like this
(`jsFiddle <http://jsfiddle.net/zyEaj/>`_):

.. _geoext.javascript.concepts.dropdown:

.. code-block:: html

    <select id="dropdown"></select>
    <button id="getresult">Get Result</button>
    <script type="text/javascript">
        var data = [[1, "one"], [2, "two"], [3, "three"]],
            result = "2";
        var dropdown = document.getElementById("dropdown");
        for (var i=0, ii=data.length; i<ii; ++i) {
            var option = document.createElement("option");
            option.value = data[i][0];
            option.text = data[i][1];
            option.selected = (option.value == result);
            dropdown.appendChild(option);
        }
        dropdown.onchange = function() {
            result = this.options[this.selectedIndex].value;
        };
        document.getElementById("getresult").onclick = function() {
            alert(result);
        };
    </script>

The main difference between the two approaches is where the data is stored.
The example uses data that is presented to the user (the options of the
drop-down), and deals with the current selection (the result).

The DOM centric version stores all the data in the DOM: the ``select`` element
with its options represents the available options, and the current result is
stored implicitly by the ``selected`` attribute which is set on the 2nd option.

The JavaScript centric version uses an initial DOM that only contains the
elements that are relevant for the presentation (i.e. the ``select`` element
and the "Get Result" button). Everything that is data related is added and
managed by JavaScript.

In both approaches, behavior is added by assigning event handlers through
JavaScript. Note that this could also be done through the DOM by setting e.g.
an ``onclick`` attribute with inline JavaScript as value, but this is not
considered good practice.

Now which approach is the better one? This depends on the context. If the whole
web page is generated by a template engine that has all data available on the
server during the template rendering pass, the DOM centric approach makes sense
and saves extra server requests. If the web page is a static page that pulls
data from an external resource (e.g. JSON data or data from OGC services), the
JavaScript centric approach is the preferred one.

Ext JS supports both DOM centric and JavaScript centric designs. As an example
for the former, let's assume we have a form with fields of different data
types, one being a date field. In the DOM, we could give all date fields a
"datefield" class, and let Ext JS turn all DOM elements with that class into
nice date fields with a calendar widget
(`jsFiddle <http://jsfiddle.net/5RDkA/>`_):

.. code-block:: html

    <input name="date" class="datefield" value="2011-01-09">
    <input name="name">
    <script type="text/javascript">
        Ext.each(Ext.query(".datefield"), function(el) {
            new Ext.form.DateField({applyTo: el})
        }
    </script>   

To wrap this up, let's have a look at the JavaScript centric approach for the
:ref:`drop-down example <geoext.javascript.concepts.dropdown>` above when we
add Ext JS into the mix and do everything in a single script block
(`jsFiddle <http://jsfiddle.net/Yqptq/>`_):

.. code-block:: javascript

    var data = [[1, "one"], [2, "two"], [3, "three"]],
        result = "2";
    var combo = new Ext.form.ComboBox({
        store: data,
        value: result,
        forceSelection: true,
        triggerAction: "all",
        renderTo: document.body
    });
    new Ext.Button({
        text: "Get Result",
        handler: function() { alert(combo.getValue()); },
        renderTo: document.body
    });

Now we don't need to create any DOM elements through HTML - Ext JS does it all
for us. And we don't need to find elements or data in the DOM through ids.


Getting Remote Geospatial Data with GeoExt
------------------------------------------

In the previous snippet, we see that the ``ComboBox`` in Ext JS has a ``store``
config option. The items of the combo box come from an ``Ext.data.Store``,
which is a powerful data component. `GeoExt <http://geoext.org/>`_ uses
subclasses of that component heavily for making data from OGC services easy to
use in Ext JS - in grids, combo boxes, trees and custom data views. Let's try
that by populating the above combo box with a list of US states from OpenGeo's
demo GeoServer, and change the "Get Result" button so it shows the selected
state on an OpenLayers map in a ``GeoExt.MapPanel`` (`jsFiddle
<http://jsfiddle.net/ACrcn/1/>`_):

.. code-block:: javascript

    var data = new GeoExt.data.FeatureStore({
        fields: ["STATE_NAME", "STATE_ABBR"],
        proxy: new GeoExt.data.ProtocolProxy({
            protocol: new OpenLayers.Protocol.Script({
                callbackKey: "format_options",
                callbackPrefix: "callback:",
                url: "http://demo.opengeo.org/geoserver/wfs",
                params: {
                    service: "WFS",
                    version: "1.1.0",
                    request: "GetFeature",
                    typeName: "topp:states",
                    srsName: "EPSG:3857",
                    outputFormat: "json"
                }
            })
        }),
        autoLoad: true
    });
    var combo = new Ext.form.ComboBox({
        store: data,
        displayField: "STATE_NAME",
        valueField: "feature",
        emptyText: "Select a state",
        mode: "local",
        triggerAction: "all",
        renderTo: document.body
    });
    new Ext.Button({
        text: "Show on map",
        handler: function() {
            result.removeAllFeatures();
            result.addFeatures([combo.getValue()]);
            result.map.zoomToExtent(result.getDataExtent());
        },
        renderTo: document.body
    });
    var result = new OpenLayers.Layer.Vector();
    new GeoExt.MapPanel({
        layers: [new OpenLayers.Layer.OSM(), result],
        center: [0,0],
        width: 256,
        height: 256,
        renderTo: document.body
    });

I think at this point it is obvious that when working with OGC services (WFS in
this case), a JavaScript centric approach makes sense - or how would you have
created a DOM with the geometries of the states?
