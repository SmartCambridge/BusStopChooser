Bus Stop Chooser
================

An JavaScript input widget for selecting bus stops, using the Smart Cambridge
bus stop API (itself based on data from NaPTAN).

Usage
-----

Include the javaScript and CSS:

    <link rel="stylesheet" href="bus_stop_chooser.css"/>
    <script src="bus_stop_chooser.js"></script>

Create a widget instance

    var chooser = BusStopChooser.create(options);

where `options` is a dictionary containing configuration options for
the widget:

Option|Description
------| -------
`lat`, `lng`, `zoom` | Centre and zoom level of the initial map display. Not used if `default_data` is supplied (see below), in which case the map is scaled and centred to show the existing stops. Defaults to Cambridge City Centre
`multi_select` | If present and 'true', multiple stops can be selected. If not, it's only possible to select a  single stop. With `multi_select=false`, `render()` expects a list containing at most one stop as `default_data` and `getData()` still returns a list of stops (which will have at most one element). Defaults to `false`.
`zoom_threshold` | The zoom level below which un-selected stops will be hidden to avoid slowing down map rendering. Default 15.

Then render it into a DOM object

    chooser.render(container, [default_data]);

where `container` is a the DOM object into which the chooser will be
rendered, or the string ID of the object. You will need to define at
least the height of this object. `default_data`, if provided, contains
a list of existing stops to be edited.

Subsequently, call

    choosed.getData()

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
an object containing loocation and other data about the stop derived directly
from the API. To be usable as `default_data`, a stop must have at least
`atco_code`, `longitude`, and `latitude` keys.

