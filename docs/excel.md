# Cairn Geographics Excel Documentation

## Installation

Download the Excel add-in from [the downloads page](http://www.cairngeographics.com/my_account).
You can install it temporarily by simply double clicking on the **CairnGeographics-AddIn.xll** file
in your downloads; if you install it this way then it will be removed when you close Microsoft Excel.

If you'd like to install it permanently, then go to **File** -> **Options**, and click on the
**Add-ins** category. In the Add-ins panel that appears, choose the **Manage** box, click
**Excel Add-ins**, and then click Go. Now choose **Browse** and navigate to the
**CairnGeographics-AddIn.xll** file that you downloaded. If you have any trouble, the
[Microsoft support page on Excel Add-ins](https://support.office.com/en-us/article/Add-or-remove-add-ins-0af570c4-5cf3-4fa9-9b88-403625a0b460) can be helpful.

## Authentication

To use Cairn Geographics, you will first need an **API key** to identify your client to the central
geographic server. You can retrieve your API key by signing into [www.cairngeographics.com](http://www.cairngeographics.com),
or ask your account manager to provide it to you. Store it somewhere safe,
as it is your password to Cairn Geographics. It will be in a format that looks like
`CGIS-80c6d145-22ec-45da-a0fc-9f7e8666f062`.

If the Cairn Geographics Add-in has been installed correctly, there will be a new tab at the far right
of Excel's top ribbon called **Cairn Geographics**. Click on this tab, and there will be an input box
where you can paste your API Key. Paste your API Key into the box, and if it is accepted then the icon
will turn green and your remaining monthly queries will be displayed.

## Quick Start

Cairn Geographics provides a number of new functions that you can use in your Excel formulas in the
same way as you use built-in functions like `SUM`. These functions are listed below, along with examples:

### Geocoding
| Function | Arguments | Description                                                                                       | Example                                       |
|----------|-----------|---------------------------------------------------------------------------------------------------|-----------------------------------------------|
| Geocode  | address   | Given a US postal address, returns the nearest latitude and longitude as a comma separated string | `=Geocode("947 S 700 E, Salt Lake City, UT")` |

### Driving Distance/Time
| Function         | Arguments                                | Description                                                                         | Example                                         |
|------------------|------------------------------------------|-------------------------------------------------------------------------------------|-------------------------------------------------|
| DrivingDistance  | start_lat, start_lon, end_lat, end_lon   | Compute the length of the shortest driving route (in meters) between two US points. | `=DrivingDistance(34.03,-118.49,34.06,-118.36)` |
| DrivingTime      | start_lat, start_lon, end_lat, end_lon   | Estimate the driving time of the shortest route (in seconds) between two US points. | `=DrivingTime(34.03,-118.49,34.06,-118.36)`     |

### Geographic Boundaries
| Function         | Arguments  | Description                                                                                          | Example                            |
|------------------|------------|------------------------------------------------------------------------------------------------------|------------------------------------|
| EnclosingState   | lat, lon   | Get the postal code (e.g. CA, MA) of the state containing the input point.                           | `=EnclosingState(34.03,-118.49)`   |
| EnclosingZipCode | lat, lon   | Get the five-digit zip code (e.g. 90034) of the zip code tabulation area containing the input point. | `=EnclosingZipCode(34.03,-118.49)` |
| EnclosingMetro   | lat, lon   | Get the name (e.g. "Salt Lake City, UT") of the metro area (CBSA) containing the input point.        | `=EnclosingMetro(34.03,-118.49)`   |
| EnclosingCounty  | lat, lon   | Get the name (e.g. "Los Angeles County") of the US county containing the input point.                | `=EnclosingCounty(34.03,-118.49)`  |

### Demographics

The functions below all include the argument `demographics_key`, which tells Cairn Geographics
what information to extract for the relevant geographic area. All information comes from the
most recent American Community Survey (ACS). Demographics keys can either be named data (e.g.
"population" or "median_household_income") or expressions made up of ACS columns (e.g.
"B05001002/B05001001").

| Function            | Arguments                     | Description                                                       | Example                                                |
|---------------------|-------------------------------|-------------------------------------------------------------------|--------------------------------------------------------|
| ZipCodeDemographics | zip_code, demographics_key    | Look up demographic information for a given five-digit zip code.  | `=ZipCodeDemographics("02138", "population")`          |
| StateDemographics   | state_code, demographics_key  | Look up demographic information for a given US state.             | `=StateDemographics("GA", "B01001A003 + B01001B003")`  |
