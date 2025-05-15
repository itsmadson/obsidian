

## Table of Contents

1. [Introduction to PostGIS](#introduction-to-postgis)
2. [Setting Up Your Environment](#setting-up-your-environment)
3. [PostGIS Basics](#postgis-basics)
4. [Spatial Data Types](#spatial-data-types)
5. [Creating and Importing Spatial Data](#creating-and-importing-spatial-data)
6. [Spatial Queries](#spatial-queries)
7. [Spatial Relationships](#spatial-relationships)
8. [Spatial Analysis Functions](#spatial-analysis-functions)
9. [Spatial Indexing](#spatial-indexing)
10. [Raster Data in PostGIS](#raster-data-in-postgis)
11. [Working with Projections](#working-with-projections)
12. [PostGIS with DBeaver](#postgis-with-dbeaver)
13. [Best Practices](#best-practices)
14. [Advanced Topics](#advanced-topics)
15. [Resources](#resources)

## Introduction to PostGIS

### What is PostGIS?

PostGIS is a spatial database extender for PostgreSQL. It adds support for geographic objects allowing location queries to be run in SQL. PostGIS follows the Simple Features for SQL specification from the Open Geospatial Consortium (OGC).

### Why Use PostGIS?

- **Powerful Spatial Functions**: Hundreds of functions for spatial operations
- **Integration with PostgreSQL**: Use all PostgreSQL features with spatial data
- **Standards Compliance**: Implements OGC standards
- **Performance**: Spatial indexing for fast queries
- **Open Source**: Free and community-supported
- **Mature Technology**: Stable and well-documented

## Setting Up Your Environment

Since you've already set up PostgreSQL and PostGIS with Docker and are using DBeaver, I'll focus on verifying your setup is working correctly.

### Verifying PostGIS Installation

Connect to your database in DBeaver and run the following SQL to verify PostGIS is installed:

```sql
SELECT PostGIS_version();
```

This should return the version of PostGIS you have installed.

### Creating a Spatial Database

If you haven't already created a spatial database:

```sql
CREATE DATABASE gis_db;
\c gis_db

CREATE EXTENSION postgis;
CREATE EXTENSION postgis_topology;
```

## PostGIS Basics

### Key Concepts

1. **Geometries**: Mathematical representations of spatial features
2. **Features**: Real-world entities with spatial attributes
3. **Spatial Reference Systems (SRS)**: Coordinate systems for positioning data
4. **Topology**: Relationships between spatial features

### Checking Available Reference Systems

PostGIS stores information about coordinate systems in the `spatial_ref_sys` table:

```sql
SELECT srid, auth_name, auth_srid, proj4text, srtext
FROM spatial_ref_sys
LIMIT 5;
```

Common SRIDs (Spatial Reference Identifiers):

- 4326: WGS84 (GPS coordinates - longitude/latitude)
- 3857: Web Mercator (used by Google Maps, OpenStreetMap)
- 2263: New York State Plane (feet)

## Spatial Data Types

PostGIS provides several spatial data types:

### Geometry Types

- **POINT**: A single location (x, y)
- **LINESTRING**: A sequence of points connected by straight lines
- **POLYGON**: A closed area defined by an outer ring and optional inner rings (holes)
- **MULTIPOINT**: A collection of points
- **MULTILINESTRING**: A collection of linestrings
- **MULTIPOLYGON**: A collection of polygons
- **GEOMETRYCOLLECTION**: A collection of geometries of any type

### Creating a Table with Spatial Data

```sql
CREATE TABLE cities (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50),
    geom GEOMETRY(POINT, 4326)
);
```

## Creating and Importing Spatial Data

### Manually Creating Geometries

```sql
-- Insert a point (New York City)
INSERT INTO cities (name, geom)
VALUES ('New York', ST_SetSRID(ST_MakePoint(-74.0060, 40.7128), 4326));

-- Insert a point (Los Angeles)
INSERT INTO cities (name, geom)
VALUES ('Los Angeles', ST_SetSRID(ST_MakePoint(-118.2437, 34.0522), 4326));
```

### Well-Known Text (WKT)

WKT is a text markup language for representing vector geometry:

```sql
-- Insert using WKT
INSERT INTO cities (name, geom)
VALUES ('Chicago', ST_GeomFromText('POINT(-87.6298 41.8781)', 4326));
```

### Well-Known Binary (WKB)

WKB is a binary representation of geometry:

```sql
-- Insert using WKB (less common in direct SQL)
INSERT INTO cities (name, geom)
VALUES ('Houston', ST_GeomFromWKB(E'\\001\\001\\000\\000\\000\\000\\000\\000\\000\\256\\263\\330\\301\\024\\064\\000\\000\\000\\000\\000\\300\\072\\064', 4326));
```

### GeoJSON

PostGIS can import GeoJSON data:

```sql
-- Insert using GeoJSON
INSERT INTO cities (name, geom)
VALUES ('Miami', ST_GeomFromGeoJSON('{"type":"Point","coordinates":[-80.1918,25.7617]}'));
```

### Importing Shapefiles and Other Formats

For shapefiles and other spatial data formats, you have several options:

1. **Using shp2pgsql command line tool**:

```bash
shp2pgsql -s 4326 input.shp schema.table_name > table.sql
psql -d database_name -f table.sql
```

2. **Using ogr2ogr utility**:

```bash
ogr2ogr -f "PostgreSQL" PG:"dbname=gis_db user=postgres" input.shp -nln schema.table_name
```

3. **Using QGIS Database Manager**
    
4. **Using DBeaver's import functionality**
    

## Spatial Queries

### Querying Geometries

```sql
-- Get all cities
SELECT name, ST_AsText(geom) FROM cities;

-- Get a specific city
SELECT name, ST_X(geom) as longitude, ST_Y(geom) as latitude 
FROM cities 
WHERE name = 'New York';
```

### Spatial Metadata Functions

```sql
-- Get geometry type
SELECT ST_GeometryType(geom) FROM cities LIMIT 1;

-- Get SRID
SELECT ST_SRID(geom) FROM cities LIMIT 1;

-- Get number of points in a geometry
SELECT ST_NPoints(geom) FROM cities LIMIT 1;
```

## Spatial Relationships

PostGIS provides functions to test spatial relationships between geometries:

### Distance

```sql
-- Distance between New York and Los Angeles (in degrees)
SELECT ST_Distance(
    (SELECT geom FROM cities WHERE name = 'New York'),
    (SELECT geom FROM cities WHERE name = 'Los Angeles')
) AS distance_degrees;

-- Distance in meters (using geography type)
SELECT ST_Distance(
    (SELECT geom::geography FROM cities WHERE name = 'New York'),
    (SELECT geom::geography FROM cities WHERE name = 'Los Angeles')
) AS distance_meters;
```

### Proximity Queries

```sql
-- Find cities within 500km of New York
SELECT name
FROM cities
WHERE ST_DWithin(
    geom::geography,
    (SELECT geom::geography FROM cities WHERE name = 'New York'),
    500000  -- 500km in meters
);
```

### Containment and Intersection

```sql
-- Create a table with polygons (US states)
CREATE TABLE states (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50),
    geom GEOMETRY(POLYGON, 4326)
);

-- Insert some example states
INSERT INTO states (name, geom)
VALUES ('New York State', ST_GeomFromText('POLYGON((-74.25909 40.49168, -73.89847 40.50995, ... -74.25909 40.49168))', 4326));

-- Find which state contains New York City
SELECT s.name
FROM states s, cities c
WHERE c.name = 'New York' AND ST_Contains(s.geom, c.geom);

-- Find states that intersect a line
SELECT name
FROM states
WHERE ST_Intersects(
    geom,
    ST_GeomFromText('LINESTRING(-100 40, -70 40)', 4326)
);
```

## Common Spatial Relationship Functions

- **ST_Contains**: Returns true if geometry A contains geometry B
- **ST_Intersects**: Returns true if geometries share any space
- **ST_Crosses**: Returns true if geometries cross
- **ST_Disjoint**: Returns true if geometries don't share any space
- **ST_Equals**: Returns true if geometries are exactly the same
- **ST_Touches**: Returns true if geometries touch at their boundaries
- **ST_Within**: Returns true if geometry A is completely inside geometry 

## Spatial Analysis Functions

### Buffers

```sql
-- Create a 10km buffer around New York City
SELECT ST_Buffer(
    (SELECT geom::geography FROM cities WHERE name = 'New York'),
    10000  -- 10km in meters
)::geometry AS buffer_geom;

-- Find cities within a buffer
SELECT c.name
FROM cities c
WHERE ST_Intersects(
    c.geom,
    ST_Buffer(
        (SELECT geom::geography FROM cities WHERE name = 'New York'),
        100000  -- 100km in meters
    )::geometry
) AND c.name != 'New York';
```

### Unions

```sql
-- Create a union of all state geometries
SELECT ST_Union(geom) AS usa_geom
FROM states;
```

### Common Analysis Functions

- **ST_Area**: Calculate area of a geometry
- **ST_Length**: Calculate length of a linestring
- **ST_Perimeter**: Calculate perimeter of a polygon
- **ST_Centroid**: Calculate the geometric center of a geometry
- **ST_ConvexHull**: Calculate the convex hull of a geometry
- **ST_Difference**: Returns the part of geometry A that does not intersect with geometry B
- **ST_Intersection**: Returns the shared portion of two geometries
- **ST_SymDifference**: Returns the parts of two geometries that don't intersect

## Spatial Indexing

### Creating a Spatial Index

```sql
-- Create a spatial index on the geom column
CREATE INDEX cities_geom_idx ON cities USING GIST (geom);
```

### When to Use Spatial Indexes

- When performing spatial queries on large datasets
- When using spatial relationship functions (ST_Contains, ST_Intersects, etc.)
- When using distance calculations

### Analyzing Index Performance

```sql
-- See if the index is being used
EXPLAIN ANALYZE
SELECT name
FROM cities
WHERE ST_DWithin(
    geom::geography,
    ST_SetSRID(ST_MakePoint(-74.0060, 40.7128), 4326)::geography,
    100000  -- 100km
);
```

## Raster Data in PostGIS

PostGIS supports raster data types for representing grid or image data:

```sql
-- Create a table for raster data
CREATE TABLE elevation (
    rid SERIAL PRIMARY KEY,
    rast raster,
    name text
);

-- Load raster data (example using raster2pgsql command)
-- raster2pgsql -s 4326 -I -C -M elevation.tif public.elevation > elevation.sql
-- psql -d gis_db -f elevation.sql

-- Query raster metadata
SELECT rid, name, ST_Width(rast), ST_Height(rast), ST_BandCount(rast)
FROM elevation;

-- Extract values at specific points
SELECT ST_Value(e.rast, 1, ST_Transform(c.geom, ST_SRID(e.rast)))
FROM elevation e, cities c
WHERE c.name = 'New York';
```

## Working with Projections

### Transforming Geometries Between Projections

```sql
-- Transform from WGS84 (4326) to Web Mercator (3857)
SELECT ST_Transform(geom, 3857) AS transformed_geom
FROM cities;

-- Calculate true distances with projected data
SELECT ST_Distance(
    ST_Transform((SELECT geom FROM cities WHERE name = 'New York'), 3857),
    ST_Transform((SELECT geom FROM cities WHERE name = 'Los Angeles'), 3857)
) / 1000 AS distance_km;
```

### Creating a Custom Projection

```sql
-- Insert a custom spatial reference system
INSERT INTO spatial_ref_sys (srid, auth_name, auth_srid, proj4text, srtext)
VALUES (
    100001, 'custom', 100001,
    '+proj=lcc +lat_1=33 +lat_2=45 +lat_0=39 +lon_0=-96 +x_0=0 +y_0=0 +datum=NAD83 +units=m +no_defs',
    'PROJCS["Custom USA Projection",GEOGCS["GCS_North_American_1983",DATUM["North_American_Datum_1983",SPHEROID["GRS_1980",6378137,298.257222101]],PRIMEM["Greenwich",0],UNIT["Degree",0.017453292519943295]],PROJECTION["Lambert_Conformal_Conic_2SP"],PARAMETER["False_Easting",0],PARAMETER["False_Northing",0],PARAMETER["Central_Meridian",-96],PARAMETER["Standard_Parallel_1",33],PARAMETER["Standard_Parallel_2",45],PARAMETER["Latitude_Of_Origin",39],UNIT["Meter",1]]'
);
```

## PostGIS with DBeaver

DBeaver provides several features for working with PostGIS:

### Viewing Spatial Data in DBeaver

1. Open the table with spatial data
2. Go to "Spatial" tab to see data visualized on a map
3. Use the map controls to pan and zoom

### Using Spatial Filters in DBeaver

1. In the "Spatial" tab, use the filtering tools to draw shapes
2. Apply spatial filters to see only data within the drawn area

### Exporting Spatial Data from DBeaver

1. Right-click on a table with spatial data
2. Select "Export Data"
3. Choose a format (shapefile, GeoJSON, etc.)
4. Configure export options

## Best Practices

### Performance Optimization

1. **Use Spatial Indexes**: Always create spatial indexes on geometry columns
2. **Simplify Complex Geometries**: Use ST_Simplify to reduce vertices for faster processing
3. **Choose Appropriate SRIDs**: Use projected coordinates for distance/area calculations
4. **Use the Geography Type**: For global data where distance calculations must account for Earth's curvature
5. **Optimize Queries**: Use EXPLAIN ANALYZE to identify slow queries

```sql
-- Simplify complex geometries
UPDATE states
SET geom = ST_Simplify(geom, 0.01)
WHERE ST_NPoints(geom) > 1000;
```

### Data Organization

1. **Schema Design**: Organize tables into schemas based on themes or projects
2. **Consistent SRIDs**: Standardize on a small set of SRIDs for your project
3. **Metadata Table**: Create a metadata table to track data sources and processing

### Error Prevention

1. **Validate Geometries**: Use ST_IsValid to check geometries before operations
2. **Handle Empty Geometries**: Check for empty geometries with ST_IsEmpty
3. **Trap Topological Exceptions**: Use PL/pgSQL exception handling for topological errors

```sql
-- Validate geometries
SELECT name, ST_IsValid(geom) AS is_valid
FROM states
WHERE NOT ST_IsValid(geom);

-- Fix invalid geometries
UPDATE states
SET geom = ST_MakeValid(geom)
WHERE NOT ST_IsValid(geom);
```

## Advanced Topics

### Topology

PostGIS Topology provides a model for managing features that share geometry:

```sql
-- Create a topology schema
SELECT CreateTopology('city_topo', 4326);

-- Add a layer to the topology
SELECT AddTopoGeometryColumn('city_topo', 'public', 'city_districts', 'topo', 'POLYGON');

-- Create features using topology
INSERT INTO city_districts (name, topo)
VALUES ('Downtown', CreateTopoGeom('city_topo', 3, 1, TopoElementArray_Agg(TopoElementArray(1, 1))));
```

### 3D Geometries

PostGIS supports 3D geometries with Z coordinates:

```sql
-- Create a table with 3D points
CREATE TABLE buildings (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50),
    geom GEOMETRY(POINTZ, 4326)
);

-- Add a 3D point
INSERT INTO buildings (name, geom)
VALUES ('Empire State Building', ST_SetSRID(ST_MakePoint(-73.9857, 40.7484, 381), 4326));

-- Get Z value
SELECT name, ST_Z(geom) AS height
FROM buildings;
```

### Temporal Data

Combining spatial and temporal data:

```sql
-- Create a spatio-temporal table
CREATE TABLE vehicle_positions (
    id SERIAL PRIMARY KEY,
    vehicle_id INTEGER,
    geom GEOMETRY(POINT, 4326),
    timestamp TIMESTAMP
);

-- Query data for a specific time range
SELECT vehicle_id, geom, timestamp
FROM vehicle_positions
WHERE vehicle_id = 1
AND timestamp BETWEEN '2023-01-01' AND '2023-01-02'
ORDER BY timestamp;

-- Construct trajectory
SELECT vehicle_id, ST_MakeLine(geom ORDER BY timestamp) AS trajectory
FROM vehicle_positions
WHERE vehicle_id = 1
GROUP BY vehicle_id;
```

### PostGIS with pgRouting

pgRouting extends PostGIS with routing functionality:

```sql
-- Create extension
CREATE EXTENSION pgrouting;

-- Create a road network table
CREATE TABLE roads (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    geom GEOMETRY(LINESTRING, 4326),
    source INTEGER,
    target INTEGER,
    cost FLOAT
);

-- Prepare topology
SELECT pgr_createTopology('roads', 0.001, 'geom', 'id', 'source', 'target');

-- Find shortest path
SELECT *
FROM pgr_dijkstra(
    'SELECT id, source, target, cost FROM roads',
    1, 5, -- source and target nodes
    false -- directed=false
);
```

## Resources

### Documentation

- [Official PostGIS Documentation](https://postgis.net/docs/)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [PostGIS Cookbook](https://postgis.net/workshops/)

### Tools

- [QGIS](https://qgis.org/) - Open-source GIS desktop application
- [pgAdmin](https://www.pgadmin.org/) - PostgreSQL administration and development platform
- [DBeaver](https://dbeaver.io/) - Universal database tool with PostGIS support
- [ogr2ogr](https://gdal.org/programs/ogr2ogr.html) - Command-line tool for data conversion

### Sample Data

- [Natural Earth](https://www.naturalearthdata.com/) - Public domain map dataset
- [OpenStreetMap Data Extracts](https://download.geofabrik.de/) - OpenStreetMap data by region
- [TIGER/Line Shapefiles](https://www.census.gov/geographies/mapping-files/time-series/geo/tiger-line-file.html) - US Census Bureau geographic data

### Practice Exercises

1. **Basic**: Create a table of points and query them by distance
2. **Intermediate**: Import a shapefile of countries and find area and perimeter
3. **Advanced**: Create a routing network and calculate shortest paths

### Staying Updated

- [PostGIS Mailing List](https://lists.osgeo.org/mailman/listinfo/postgis-users)
- [GIS Stack Exchange](https://gis.stackexchange.com/)
- [Planet PostGIS](https://planet.postgis.net/) - Blog aggregator