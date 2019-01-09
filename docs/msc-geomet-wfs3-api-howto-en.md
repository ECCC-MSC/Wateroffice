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

https://geo.weather.gc.ca/geomet-beta/features

### OpenAPI 3.0 Document

https://geo.weather.gc.ca/geomet-beta/features/api

## Feature Collections

List all feature collections available:

https://geo.weather.gc.ca/geomet-beta/features/collections

The response provides a list of feature collections with associated metadata (title, description, links, extent, CRS).

## Feature Collection 

List a single feature collection:

https://geo.weather.gc.ca/geomet-beta/features/collections/hydrometric-stations

## Inspecting Feature Collection Schema

Issue a query returning a single feature to inspect geometry and properties:

https://geo.weather.gc.ca/geomet-beta/features/collections/hydrometric-daily-mean/items?limit=1

## Querying

Querying feature collections allows for spatial, temporal and property filtering.  Filter parameters
can be combined to formulate an exclusive ('and') search.

The examples that follow use the [hydrometric daily mean](https://geo.weather.gc.ca/geomet-beta/features/collections/hydrometric-daily-mean) feature collection.

Default query, no filters:

https://geo.weather.gc.ca/geomet-beta/features/collections/hydrometric-daily-mean/items

### Spatial

Query by bounding box (minx, miny, maxx, maxy)

https://geo.weather.gc.ca/geomet-beta/features/collections/hydrometric-daily-mean/items?bbox=-140,43.2,-65,67

### Temporal

Query by a single time instant:

https://geo.weather.gc.ca/geomet-beta/features/collections/hydrometric-daily-mean/items?time=1972-10-30

Query by a time extent:

https://geo.weather.gc.ca/geomet-beta/features/collections/hydrometric-daily-mean/items?time=1972-10-30/2010-07-31

### Property

Query by a feature collection property:

https://geo.weather.gc.ca/geomet-beta/features/collections/hydrometric-daily-mean/items?STATION_NUMBER=10CD001

### Paging

The default response size is 10 features, starting with the first feature.  To adjust/page through query results.

Query and limit to features 1-2:

https://geo.weather.gc.ca/geomet-beta/features/collections/hydrometric-daily-mean/items?STATION_NUMBER=10CD001&limit=2

Query and limit to features 1-100:

https://geo.weather.gc.ca/geomet-beta/features/collections/hydrometric-daily-mean/items?STATION_NUMBER=10CD001&limit=100

Query and limit to features 101-200:

https://geo.weather.gc.ca/geomet-beta/features/collections/hydrometric-daily-mean/items?STATION_NUMBER=10CD001&startindex=101&limit=100

### Combining Filter Parameters

Query all daily mean data from a single station between 2001 and 2010:

https://geo.weather.gc.ca/geomet-beta/features/collections/hydrometric-daily-mean/items?STATION_NUMBER=10CD001&time=2001-01-01/2010-12-31

### Exporting to CSV format

Any query can be exported to CSV by adding `f=csv` to the request.

### Sorting

Any query can be sorted by adding `sortby=PROPERTY:X`, where `PROPERTY` is the sort property and `X` is the sort order (`A` is ascending, `D` is descending).  Sort order is optional.  Sorting by multiple properties is supported by providing a comma-separated list to the `sortby` parameter.

## Access by Identifier

Fetch a single feature by identifier:

https://geo.weather.gc.ca/geomet-beta/features/collections/hydrometric-daily-mean/items?f=csv

## Tools and Implementations

### GDAL

Using [GDAL](http://www.gdal.org/drv_wfs3.html)'s WFS 3 support:

```bash
# list datasets
ogrinfo WFS3:https://geo.weather.gc.ca/geomet-beta/features
# describe a single dataset
ogrinfo WFS3:https://geo.weather.gc.ca/geomet-beta/features hydrometric-daily-mean -al -so
# perform spatial query
ogrinfo WFS3:https://geo.weather.gc.ca/geomet-beta/features hydrometric-daily-mean -spat -100 50 -90 55
```

### geojson.io

Using [geojson.io](https://geojson.io), copy/paste any query result to visualize and point/click query.
