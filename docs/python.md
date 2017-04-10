# Cairn GIS Python Documentation

## Installation

If you are running a fairly modern version of Python, you should be able to install the Cairn GIS client with

    pip install cairn-gis

Alternatively, if you are an Anaconda user, you can install with

    conda install cairn-gis


## Authentication

To use Cairn GIS, you will first need an **API key** to identify your client to the central geographic server.
You can create one of these for free at [www.cairngis.com](www.cairngis.com). Once you have created one,
store it somewhere safe, as it is your password to Cairn GIS. It will be in a format that looks like
`CGIS-80c6d145-22ec-45da-a0fc-9f7e8666f062`. The rest of this page assumes you have an API key, and refers
to it as `API_KEY`.


## Quick Start

Cairn GIS includes a library of simple geographic objects such as `Location` (a point on the Earth, i.e. a
latitude and longitude) or `Region` (a region of the earth, such as a country, state, or county.) These
objects are used to construct queries that can then be evaluated by the Cairn GIS server. Here's how to
run a simple query to find the state that contains the location with latitude *40.76* and longitude *-111.89*:

```python
>>> from cairn_gis import Connection, Location
>>> conn = Connection(API_KEY)
>>> query = Location(40.76, -111.89).region('us_state')
>>> result = conn.run(query)
>>> result
Region(type="us_state", key="UT", name="Utah")
>>> result.name
"Utah"
```

More complex queries are also possible by combining several objects and function calls. The following
example shows how to compute driving distance from the centroid (middle) of Oregon to the centroid of
Washington State:

```python
>>> from cairn_gis import Connection, Region
>>> conn = Connection(API_KEY)
>>> washington_center = Region("us_state", "WA").centroid()
>>> oregon_center = Region("us_state", "OR").centroid()
>>> query = oregon_center.driving_distance(washington_center)
>>> conn.run(query)
Measure(value=301.90, unit="mi")
```
