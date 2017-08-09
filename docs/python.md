# Cairn Geographics Python Documentation

## Installation

If you are running a fairly modern version of Python 2 or 3, you should be able to install the Cairn
Geographics client with

```bash
pip install cairn-geographics
```

## Authentication

To use Cairn Geographics, you will first need an **API key** to identify your client to the central geographic
server. You can create one of these for free at [www.cairngeographics.com](www.cairngeographics.com).
Once you have created one, store it somewhere safe, as it is your password to Cairn Geographics. It will be
in a format that looks like`CGIS-80c6d145-22ec-45da-a0fc-9f7e8666f062`. The rest of this page assumes you have
an API key, and refers to it as `API_KEY`.

## Quick Start

Cairn Geographics includes a library of simple geographic objects such as `Location` (a point on the Earth, i.e. a
latitude and longitude) or `Region` (a region of the earth, such as a country, state, or county.) These
objects are used to construct queries that can then be evaluated by the Cairn Geographics server. Here's how to
run a simple query to find the state that contains the location with latitude *40.76* and longitude *-111.89*:

```python
>>> from cairn_geographics import Connection, Location
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
>>> from cairn_geographics import Connection, Region
>>> conn = Connection(API_KEY)
>>> washington_center = Region("us_state", "WA").centroid()
>>> oregon_center = Region("us_state", "OR").centroid()
>>> query = oregon_center.driving_distance(washington_center)
>>> conn.run(query)
Measure(value=301.90, unit="mi")
```

## API Reference

### Location

Location objects can be created either from a latitude and longitude pair, or from a US address string.
Both of the following forms are acceptable:

  * `Location(40.7551,-111.8881)`
  * `Location("150 S State St, Salt Lake City, UT 84111")`

To geocode a location (i.e. turn an address into a latitude/longitude), simply run a `Location` object
with an address as a query. The result will also be a `Location` object, this time with `lat` and `lon`
fields added:

```python
>>> from cairn_geographics import Location
>>> conn.run(Location("150 S State St, Salt Lake City, UT 84111"))
Location(lat=40.7551277, lon=-111.8881408, address=150 S State St, Salt Lake City, UT 84111)
>>> (_.lat, _.lon)
(40.7551277, -111.8881408)
```

Location objects support the following class methods:
<hr>
*Location.* **region(region_type: str)**

Returns the US political region that contains the given location, where
`region_type` is currently one of the strings "us_county", "us_state", "metro_area", or "zip_code". Examples:

```python
>>> conn.run(Location("30 Dunster Street. Cambridge, MA 02138").region("metro_area"))
Region(region_type=metro_area, key=14460, name=Boston-Cambridge-Newton, MA-NH)
>>> conn.run(Location(43.6187, -116.2146).region("us_state"))
Region(region_type=us_state, key=ID, name=Idaho)
```
<hr>

*Location.* **driving_distance(end_location: Location)**

Given another `Location` object, computes the driving distance to that location. Returns a `Measure`
object in meters. If either the start or end point do not fall on the road network, then the distance
includes the shortest possible straight line route to get from the start/end point to the nearest road.
Example:

```python
>>> conn.run(Location(43.6187, -116.2146).driving_distance(Location(40.7551,-111.8881)))
Measure(value=548728.9, unit='m')
```
<hr>

*Location.* **driving_time(end_location: Location)**

Similar to `driving_distance` above, but returns a
`Measure` object in seconds instead, representing the average drivetime between the two points.
Example:

```python
>>> conn.run(Location(43.6187, -116.2146).driving_time(Location(40.7551,-111.8881)))
Measure(value=21539, unit='s')
```

### Region

Region objects are created with a **region type** and a **region key**; for example:

* `Region('us_state', 'WA')`
* `Region('zip_code', '90210')`

The region types that can currently be created in this way are "us_state" and "zip_code",
since these have well-defined unique IDs. Other types of regions can be created from an
interior point using the `Location.region` function described above.

Region objects of type "us_state" or "zip_code" can also be created using the `State`
and `ZipCode` aliases, e.g. `State("AK")` or `ZipCode("30094")`.


Region objects support the following class methods:

<hr>
*Region.* **centroid()**

Returns a `Location` object containing the geographic centroid of the region. Example:

```python
>>> conn.run(Region("zip_code", "90210").centroid())
Location(lat=34.10105584880364, lon=-118.4147335422377)
```

<hr>
*Region.* **area()**

Returns a `Measure` object containing the total area of the region in square meters. Example:

```python
>>> conn.run(Region("zip_code", "90210").area())
Measure(value=26376181.913986266, unit='m^2')
```

<hr>
*Region.* **demographics(demographics_type)**

Queries the American Community Survey for demographics information about the region. Returns
a `Measure` object containing the result. `demographics_type` can either be a
predefined query like "population" or "median_household_income", or an arbitrary
expression combining ACS columns (e.g. "B05001002/B05001001"). Examples:

```python
>>> conn.run(Region("zip_code", "90210").demographics("median_household_income"))
Measure(value=92052, unit='USD')
conn.run(Region("zip_code", "90210").demographics("B05001002/B05001001"))
Measure(value=0.6092871394884818, unit='units')
```


Available demographics keys currently include:

| Key                     |  Description                                                                                                               |
|-------------------------|----------------------------------------------------------------------------------------------------------------------------|
| population              | Total 2015 population                                                                                                      |
| pop_white               | Total population identifying as white alone ([more info](https://www.socialexplorer.com/data/ACS2015/metadata/?ds=ACS15&var=B02001002))                                                               |
| pop_black               | Total population identifying as black alone or in combination with other races ([more info](https://www.socialexplorer.com/data/ACS2015/metadata/?ds=ACS15&var=B02001002))                            |
| pop_asian               | Total population identifying as Asian alone or in combination with other races ([more info](https://www.socialexplorer.com/data/ACS2015/metadata/?ds=ACS15&var=B02001002))                            |
| pop_native_american     | Total population identifying as Native American/Pacific Islander alone or in combination with other races ([more info](https://www.socialexplorer.com/data/ACS2015/metadata/?ds=ACS15&var=B02001002)) |
| pop_hispanic            | Total population identifying as Hispanic or Latino ([more info](https://www.socialexplorer.com/data/ACS2015/metadata/?ds=ACS15&var=b03001003))                                                        |
| median_household_income | Median household income in 2015 inflation-adjusted dollars ([more info](https://www.socialexplorer.com/data/ACS2015/metadata/?ds=ACS15&var=b19013001))                                                |


Note that Cairn Geographics is aware of the correct result unit for predefined queries, but cannot infer
units for arbitrary ACS column expressions.

### Measure

The `Measure` type is used to pass numeric results back from Cairn Geographics, and to do a limited set
of numeric calculations on the Geographics server. Each `Measure` object has a numeric `value` field in
addition to a string `unit` field. Examples include:

* `Measure(value=761321, unit='m^2')`
* `Measure(value=1200.5, unit='s')`
* `Measure(value=110000, unit='dollars')`

The Cairn Geographics query evaluator is unit-aware, so you can pass in queries like this:

```python
>>> beverly_hills = Region("zip_code", "90210")
>>> conn.run(beverly_hills.demographics("population"))
Measure(value=22052, unit='people')
>>> conn.run(beverly_hills.area())
Measure(value=26376181.913986266, unit='m^2')
>>> conn.run(beverly_hills.demographics("population")/beverly_hills.area())
Measure(value=0.0008360573214088534, unit='people/m^2')
```
