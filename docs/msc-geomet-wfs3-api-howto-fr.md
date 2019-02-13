# MSC GeoMet Web Feature Service (WFS) 3.0 API HowTo

## OGC Web Feature Service 3.0

## MSC GeoMet WFS 3.0 API Overview

The [OGC WFS 3.0 API](https://rawgit.com/opengeospatial/WFS_FES/master/docs/17-069.html) provides
a specification to querying geospatial data on the web.

The service operates over HTTP.

Requests are made via HTTP GET requests.

Responses are JSON/[GeoJSON](http://geojson.org/) by default.

No HTTP authentication is required.

## Service Endpoints

### WFS 3

https://geo.weather.gc.ca/geomet/features

### OpenAPI 3.0 Document

https://geo.weather.gc.ca/geomet/features/api

## Feature Collections

List all feature collections available:

https://geo.weather.gc.ca/geomet/features/collections

The response provides a list of feature collections with associated metadata (title, description, links, extent, CRS).

## Feature Collection 

List a single feature collection:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-stations

## Inspecting Feature Collection Schema

Issue a query returning a single feature to inspect geometry and properties:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items?limit=1

## Querying

Querying feature collections allows for spatial, temporal and property filtering.  Filter parameters
can be combined to formulate an exclusive ('and') search.

The examples that follow use the [hydrometric daily mean](https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean) feature collection.

Default query, no filters:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items

### Spatial

Query by bounding box (minx, miny, maxx, maxy)

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items?bbox=-140,43.2,-65,67

### Temporal

Query by a single time instant:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items?time=1972-10-30

Query by a time extent:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items?time=1972-10-30/2010-07-31

### Property

Query by a feature collection property:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items?STATION_NUMBER=10CD001

### Paging

#### Startindex
The `startindex` parameter can be used to specify the record to start at when extracting
features.  The default value is 0 (first feature).

#### Limit
The `limit` parameter is used to define the maximum records to return as part of querying
for features.  The default response size is 500 features.  Setting a `limit` of 0 results
in returning only the number of features found (without the actual features returned).

#### Paging through results

The `startindex` and `limit` parameters can be used in tandem to cycle through feature collections.  The examples below demonstrate how to adjust and page through query results.

Query and limit to features 1-2:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items?STATION_NUMBER=10CD001&limit=2

Query and limit to features 1-100:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items?STATION_NUMBER=10CD001&limit=100

Query and limit to features 101-200:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items?STATION_NUMBER=10CD001&startindex=101&limit=100

## Strategies for paging

The paging strategy is commonly used in support of performance when returning large data extractions.  For example, a client could page by 1000 features to cycle through an entire station record:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items?STATION_NUMBER=01AP004&startindex=0&limit=1000

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items?STATION_NUMBER=01AP004&startindex=10&limit=1000

The client can then simply cycle through all items until there no longer any records.  This would constitute the entire record.

Another strategy that can be used is to query for all data without returning any records:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items?STATION_NUMBER=01AP004&limit=0

...and then inspecting the response (see `numberMatched`) to assess the size of the entire record.  The client can then decide how or whether to page accordingly.

### Combining Filter Parameters

Query all daily mean data from a single station between 2001 and 2010:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items?STATION_NUMBER=10CD001&time=2001-01-01/2010-12-31

### Exporting to CSV format

Any query can be exported to CSV by adding `f=csv` to the request.

## Access by Identifier

Fetch a single feature by identifier:

https://geo.weather.gc.ca/geomet/features/collections/hydrometric-daily-mean/items?f=csv

## Tools and Implementations

### GDAL

Using [GDAL](http://www.gdal.org/drv_wfs3.html)'s WFS 3 support:

```bash
# list datasets
ogrinfo WFS3:https://geo.weather.gc.ca/geomet/features
# describe a single dataset
ogrinfo WFS3:https://geo.weather.gc.ca/geomet/features hydrometric-daily-mean -al -so
# perform spatial query
ogrinfo WFS3:https://geo.weather.gc.ca/geomet/features hydrometric-daily-mean -spat -100 50 -90 55
```

### geojson.io

Using [geojson.io](https://geojson.io), copy/paste any query result to visualize and point/click query.
