Bus Stop Chooser
================

[![Build Status](https://travis-ci.com/SmartCambridge/BusStopChooser.svg?branch=master)](https://travis-ci.com/SmartCambridge/BusStopChooser)

An JavaScript input widget for selecting bus stops, using the [Smart Cambridge
bus stop API](http://smartcambridge.org/transport/api/docs/#stops-list) (itself based on data from [NaPTAN](https://data.gov.uk/dataset/ff93ffc1-6656-47d8-9155-85ea0b8f2251/national-public-transport-access-nodes-naptan)) and [Leaflet](http://leafletjs.com/).

This code has essentially no error checking, so if you call it in ways it's not expecting it is likely to respond with exciting error messages (or not respond at all).

Usage
-----

Include [Leaflet](http://leafletjs.com/) and the widget's JavaScript and CSS:

```html
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.3.1/dist/leaflet.css"
integrity="sha512-Rksm5RenBEKSKFjgI3a41vrjkw4EVPlJ3+OiI65vTjIdo9brlAacEuKOiQ5OFh7cOI1bkDwLqdLw3Zg0cRJAAQ=="
crossorigin=""/>

<script src="https://unpkg.com/leaflet@1.3.1/dist/leaflet.js"
integrity="sha512-/Nsx9X4HebavoBvEBuyp3I7od5tA0UzAxs+j83KgC8PU0kgB4XiK4Lfe4y4cgBtaRJQEIFCW+oC506aPT2L1zw=="
crossorigin=""></script>

<link rel="stylesheet" href="bus_stop_chooser.css"/>

<script src="bus_stop_chooser.js"></script>
```

Create a widget instance

```javascript
var chooser = BusStopChooser.create(options);
```

where `options` is a dictionary containing configuration options for
the widget:

Option|Description
------| -------
`api_endpoint` | The base URL of the API to use. Default `https://smartcambridge.org/api/v1/`.
`api_token` | Authorisation token to include in API requests. Default is not to include a token.
`lat`, `lng`, `zoom` | Default centre and zoom level of the initial map display. Ignored if `current.map` or `current.stops` are supplied to `render()` (see below), in which case the map is scaled and centred either based on `current.map` or to a box containing all `current.stops`. Defaults to Cambridge City Centre
`multi_select` | If present and 'true', multiple stops can be selected. If not, it's only possible to select a  single stop. Even with `multi_select=false`, `current.stops` passed to `render()` should be a list (containing at most one stop), and `getData()` still returns a list of stops (which will have at most one element). Defaults to `false`.
`zoom_threshold` | The zoom level below which un-selected stops will be hidden to avoid slowing down map rendering. Default 15.
`on_click_stop_callback` | Function. If present and defined, the function will be called every time a bus stop is clicked. It will be called with two arguments, first the marker corresponding to the bus stop that has been clicked, and secondly a boolean indicating if the action was to select (true) or deselect(false) the bus stop.
`popups` | If present and 'true', suppress the tooltips that are normally shown when hovering over a stop and instead show a popup containing the stop's name when it is clicked.
`location` | If present and 'true', include a button that will zoom the map so that it's centered on current position, providing the browser can do geolocation.
`stops_callback` | Function. If present and defined, the function will be called when the chooser is first rendered and once each time a bus stop is selected or de-selected. It will be called with a single argument which is the total number of currently selected stops.

Then render it into a DOM object (e.g. a \<div\>)

```javascript
chooser.render(parent_el, [current]);
```

where `parent_el` is a the DOM object into which the chooser will be
rendered, or the string ID of the object. Make sure the object has a
defined height and width, for example by setting it in CSS. `current`, if provided,
can contain the centre and zoom level of the map to display and/or a
list of existing stops to be edited in the format returned by
`getData()` (see below).

Subsequently, call

```javascript
data = chooser.getData();
```

to retrieve the chosen stops. The returned data is an object with properties
`map`, giving the final centre and zoom of the displayed map, and `stops`
containing a (possibly empty) list of bus stops.

```json
{
  "map": {
    "lng": 0.13309121131896975,
    "lat": 52.19267312526965,
    "zoom": 10
  },
  "stops": [
    {
      "id": "0500CCITY002",
      "stop_id": "0500CCITY002",
      "atco_code": "0500CCITY002",
      "naptan_code": "CMBDADAG",
      "common_name": "Great St Mary's Church",
      "indicator": "near",
      "locality_name": "Cambridge",
      "longitude": 0.11798986495,
      "latitude": 52.2056312981,
      "lng": 0.11798986495,
      "lat": 52.2056312981
    },
    ...
  ]
}
```

Stops, both in the returned data and in `default_data` are represented by
an object containing location and other data about the stop derived directly
from the API. To be usable in `current.stops`, a stop must have at least
`stop_id`, `lng`, and `lat` properties.

`choser.value()` is an alias for `chooser.getData()`. `chooser.valid()` is a stub
routine that curently always returns `True`.