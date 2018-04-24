Bus Stop Chooser
================

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
`lat`, `lng`, `zoom` | Centre and zoom level of the initial map display. Not used if `default_data` is supplied (see below), in which case the map is scaled and centred to show the existing stops. Defaults to Cambridge City Centre
`multi_select` | If present and 'true', multiple stops can be selected. If not, it's only possible to select a  single stop. With `multi_select=false` any `default_data` passed to `render()` should be a list containing at most one stop, and `getData()` still returns a list of stops (which will have at most one element). Defaults to `false`.
`zoom_threshold` | The zoom level below which un-selected stops will be hidden to avoid slowing down map rendering. Default 15.

Then render it into a DOM object (e.g. a \<div\>)

```javascript
chooser.render(container, [default_data]);
```

where `container` is a the DOM object into which the chooser will be
rendered, or the string ID of the object. Make sure the container has a defined height, for example by setting it in CSS. `default_data`, if provided, contains
a list of existing stops to be edited.

Subsequently, call

```javascript
data = chooser.getData()
```

to retrieve the chosen stops. The returned data is an object with keys
`map`, giving the final centre and zoom of the displayed map, and `stops`
containing a (possibly empty) list of bus stops.

```json
{
  "map": {
    "lng": 0.030394660080013132,
    "lat": 52.21211418451629,
    "zoom": 11
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
      "latitude": 52.2056312981
    },
    ...
  ]
}
```

Stops, both in the returned data and in `default_data` are represented by
an object containing location and other data about the stop derived directly
from the API. To be usable as `default_data`, a stop must have at least
`atco_code`, `longitude`, and `latitude` keys.

