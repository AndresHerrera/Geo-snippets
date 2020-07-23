# Geo-snippets

**This repository contains a collection of code snippets for geo developers**
	
* [PostgreSQL](#postgresql)
	* [Create PostgreSQL database](#create-postgresql-database)
	* [Create PostGIS and PgRouting extensions in PostgreSQL](#create-postgis-and-pgrouting-extensions-in-postgresql)
	* [Add PostgreSQL extensions](#add-postgresql-extensions)
	* [Import to PostgreSQL from SQL file](#import-to-postgresql-from-sql-file)
	* [Import CSV file to a PostgreSQL table](#import-csv-file-to-a-postgresql-table)
	* [Exports table as a JSON array](#exports-table-as-a-json-array)
	* [Create an unique id](#create-an-unique-id)
	* [Size of database](#size-of-database)
	* [Size of schema](#size-of-schema)
	* [Find duplicates](#find-duplicates)

	
	
* [PostGIS](#postgis)
	* [Get the geometry type](#get-the-geometry-type)
	* [Find a table SRID](#find-a-table-srid)
	* [Change table SRID](#change-table-srid)
	* [Get tables with wrong SRID](#get-tables-with-wrong-srid)
	* [Spatial join point in polygon](#spatial-join-point-in-polygon)
	* [Check validity of geometries](#check-validity-of-geometries)
	* [Get extent of table](#get-extent-of-table)
	* [Change projection of data](#change-projection-of-data)
	
* [shp2pgsql](#shp2pgsql)
	* [Export a shapefile to SQL](#export-a-shapefile-to-sql)

* [ogr2ogr](#ogr2ogr)
	* [Export a new shapefile filtered by criteria](#export-a-new-shapefile-filtered-by-criteria)
	* [Export a shapefile to GML](#export-a-shapefile-to-gml)
	* [Export a shapefile to GeoJSON](#export-a-shapefile-to-geojson)
	* [Export a shapefile to KML](#export-a-shapefile-to-kml)
	* [Export dbf table to PostgreSQL](#export-dbf-table-to-postgresql)
	* [Import to PostgreSQL table from csv and Virtual File Format](#import-to-postgresql-table-from-csv-and-virtual-file-format)
	* [Export a csv file to dbf](#export-a-csv-file-to-dbf)
	* [Export a csv file to shapefile](#export-a-csv-file-to-shapefile)
	* [Export a Postgres table to GeoPackage](#export-a-postgres-table-to-geopackage)
	* [Export many Postgres tables to GeoPackage](#export-many-postgres-tables-to-geopackage)
	* [Export a whole Postgres database to GeoPackage](#export-a-whole-postgres-database-to-geopackage)
	* [Load a single layer GeoPackage into Postgres](#load-a-single-layer-geopackage-into-postgres)
	* [PostGIS to SQLite](#postgis-to-sqlite)
	* [Import Geodatabase to PostGIS](#import-geodatabase-to-postgis)
	* [Reading the meta data using ogrinfo](#reading-the-meta-data-using-ogrinfo)


* [gdal](#gdal)
	* [Create virtual raster from a folder containing Geotiff files](#create-virtual-raster-from-a-folder-containing-geotiff-files)
	* [Create tiles to disk from GeoTiff file](#create-tiles-to-disk-from-geotiff-file)
	* [Create a hillsahe from a dem Geotiff](#create-a-hillsahe-from-a-dem-geotiff)
	* [Crop a raster file according to a shapefile](#crop-a-raster-file-according-to-a-shapefile)
	* [Merge bands into a raster file](#merge-bands-into-a-raster-file)
	* [Merge bands into a raster file spatially](#merge-bands-into-a-raster-file-spatially)
	* [Resample a raster file](#resample-a-raster-file)
	

* [Python](#python)

* [GPS](#gps)
	* [Export a Garmin fit activity to gpx](#export-a-garmin-fit-activity-to-gpx)

* [DOS or UNIX Bash routines](#dos-or-unix-bash-routines)
	* [Load all shapefiles into Postgres](#load-all-shapefiles-into-postgres)
	* [Convert all Garmin Fit to GPX](#convert-all-garmin-fit-to-gpx)

* [PoTree](#potree)
	* [convert las to potree](#convert-las-to-potree)
	
Snippets
=======

PostgreSQL
----------

### Create PostgreSQL database
```
createdb -U user -p 5432 -h localhost -E UTF8 -e databasename 
```

### Create PostGIS and PgRouting extensions in PostgreSQL
```
psql -U user -p 5432 -h localhost -d databasename -c "CREATE EXTENSION postgis;"
psql -U user -p 5432 -h localhost -d databasename -c "CREATE EXTENSION pgrouting;"
```

### Add PostgreSQL extensions
```sql
CREATE EXTENSION IF NOT EXISTS postgis; 
```

### Import to PostgreSQL from SQL file
```
psql -U user -d databasename -h localhost -p 5432 -f input_file.sql
```

### Import CSV file to a PostgreSQL table
```sql
COPY table_name FROM 'data.csv' WITH DELIMITER ',' CSV HEADER;
```

### Exports table as a JSON array
```sql
COPY (SELECT array_to_json(array_agg(t)) 
FROM schema.table as t) to '/output.json'
```

### Create an unique id
```sql
SELECT ROW_NUMBER() OVER (ORDER BY column ) as id  
FROM schema.table;
```

### Size of database
```sql
SELECT pg_size_pretty(pg_database_size('databasename')) as dbsize;
```

### Size of schema
```sql
select pg_size_pretty(CAST((SELECT
SUM(pg_total_relation_size(table_schema || '.' || table_name) )
FROM information_schema.tables
WHERE table_schema = 'schema') as bigint) )  as schema_size;
```

### Find duplicates
```sql
SELECT column, COUNT(column) AS numocurrences
FROM schema.table
GROUP BY column
HAVING ( COUNT(column) > 1 );
```


PostGIS
----------

### Get the geometry type
```sql
 SELECT st_geometrytype(the_geom) FROM  schema.table;
```

### Get tables with wrong SRID
```sql
SELECT t.f_table_catalog, t.f_table_schema, t.f_table_name, t.f_geometry_column, t.coord_dimension, t.srid, t.type
FROM geometry_columns t 
WHERE t.srid <> 4326 ORDER BY t.f_table_schema, t.f_table_name;
```
### Find a table SRID
```sql
SELECT Find_SRID('schema', 'table', 'the_geom');
```

### Change table SRID
```sql
SELECT UpdateGeometrySRID('schema', 'table', 'the_geom',4326);
```

### Spatial join point in polygon
```sql
SELECT *
FROM table1 a, table2 b
WHERE ST_Within(a.the_geom, b.the_geom);
```

### Check validity of geometries
```sql
SELECT *
FROM schema.table
WHERE ST_IsValid(the_geom) = true;
```

### Get extent of table
```sql
SELECT ST_XMax(r) AS xmax, ST_XMin(r) AS xmin, ST_YMax(r) AS ymax, ST_YMin(r) AS ymin 
FROM (SELECT ST_Collect(the_geom) AS r FROM schema.table) AS foo;
```

### Change projection of data
```sql
ALTER TABLE schema.table ALTER COLUMN the_geom TYPE Geometry(MultiLineString, 3115) 
USING ST_Transform(the_geom, 3115);
```

shp2pgsql
----------

### Export a shapefile to SQL
```
shp2pgsql -g the_geom -s 4326 -W latin1 shapefile table_name > output.sql
```

ogr2ogr
----------

### Export a new shapefile filtered by criteria
```
ogr2ogr -sql "SELECT * FROM input WHERE criteria=1" output.shp input.shp
```

### Export a shapefile to GML
```
ogr2ogr -f GML -t_srs crs:84 output.gml input.shp
```

### Export a shapefile to GeoJSON
```
ogr2ogr -f GeoJSON -t_srs crs:84 output.geojson input.shp
```

### Export a shapefile to KML
```
ogr2ogr -f KML -t_srs crs:84 output.KML input.shp
```

### Export dbf table to PostgreSQL
```
ogr2ogr -f "PostgreSQL" PG:"dbname=database host=localhost user=postgres password=postgres port=5432" table_dbf.dbf -nln "newtable"
```

### Import to PostgreSQL table from csv and Virtual File Format 
```
ogr2ogr -a_srs epsg:4326 -f "PostgreSQL" PG:"dbname=database host=localhost user=postgres password=postgres port=5432" file.vrt
```
* [Example data](ogr2ogr/pg_from_csv)

### Export a csv file to dbf
```
ogr2ogr -f "ESRI Shapefile" output.dbf input.csv
```

### Export a csv file to shapefile 
```
ogr2ogr -f "ESRI Shapefile" output_shp_xy data.vrt
```
* [Example data](ogr2ogr/csv_to_shp)

### Export a Postgres table to GeoPackage
```
ogr2ogr -f GPKG output.gpkg PG:"dbname=database host=localhost user=postgres password=postgres port=5432" "table"
```

### Export many Postgres tables to GeoPackage
```
ogr2ogr -f GPKG output.gpkg PG:"dbname=database host=localhost user=postgres password=postgres port=5432 tables=table1,table2,table3"
```

### Export a whole Postgres database to GeoPackage
```
ogr2ogr -f GPKG output.gpkg PG:"dbname=database host=localhost user=postgres password=postgres port=5432"
```

### Load a single layer GeoPackage into Postgres
```
ogr2ogr -f "PostgreSQL" PG:"dbname=database host=localhost user=postgres password=postgres port=5432" input.gpkg -nln "new_table"
```

### PostGIS to SQLite
```
ogr2ogr -progress -gt 65536 -f "SQLite" data.sqlite PG:"dbname=database host=localhost user=postgres password=postgres port=5432" -a_srs "EPSG:4326"
```

### Import Geodatabase to PostGIS
```
ogr2ogr -f "PostgreSQL" PG:"dbname=database host=localhost user=postgres password=postgres port=5432" data.gdb -a_srs EPSG:4326
```

## Reading the meta data using ogrinfo
```
ogrinfo -ro data.shp
```

gdal
----------

## Create virtual raster from a folder containing Geotiff files
```
gdalbuildvrt file.vrt folder_name/*.tif
```

## Create tiles to disk from GeoTiff file
```
gdal2tiles.py input.tif output_folder  -z 6-18 -r antialias
```
*Zoom level 6 to 18

## Create a hillsahe from a dem Geotiff
```
gdaldem hillshade inputdem.tif  outputhillshade.tif
```

## Crop a raster file according to a shapefile
```
gdalwarp -overwrite -s_srs EPSG:3115 -q -cutline shapefile.shp -of GTiff input.tiff output.tiff
```

## Merge bands into a raster file
```
gdal_merge.py -o output.tiff -of GTiff -ps 10 10 -separate band1.tiff band2.tiff band3.tiff
```
*10 x 10 meter pixel

## Merge bands into a raster file spatially
```
gdal_merge.py -o output.tiff -of GTiff band1.tiff band2.tiff band3.tiff
```

## Resample a raster file
```
gdal_translate -outsize 50% 50% input.tif output.tif
```
*Resample to 50%


Python
----------

GPS
----------

### Export a Garmin fit activity to gpx
```
gpsbabel -t -i garmin_fit -f input.FIT -o gpx -F output.gpx
```


DOS or UNIX Bash routines
----------

### Load all shapefiles into Postgres
```
@echo allshp2dbpgsql.bat
@echo off
color 20
for %%x in (*.shp) do ogr2ogr -f "PostgreSQL" PG:"host=localhost user=postgres dbname=database password=12345 port:5432" %%~nx.shp
pause
exit
```

### Convert all shapefiles into Postgres
```
@echo allshapes2pgsql.bat
@echo off
color 20
for %%x in (*.shp) do shp2pgsql -g the_geom -s 4326 -W latin1 %%~nx %%~nx > %%~nx.sql
pause
exit
```

### Convert all Garmin Fit to GPX
```
@echo allfit2gpx.bar
@echo off
color 20
for %%x in (*.fit) do gpsbabel -t -i garmin_fit -f %%~nx.fit -o gpx -F %%~nx.gpx
pause
exit
```

Potree
----------

### Convert las to potree

```
PotreeConverter.exe C:/temporal/input.las -o C:/temporal/data_converted
```



## Author: Andres Herrera &copy; 2020

* [@andresherrera](https://twitter.com/andresherrera)

License: MIT - do anything with the code, but don't blame me if it does not work.

Spread the word: tweet, star on github, etc.