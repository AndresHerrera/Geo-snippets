# Geo-snippets

**This repository contains a collection of code snippets for geo developers**

* [GPS](#gps)
	* [Export a Garmin fit activity to gpx](#export-a-garmin-fit-activity-to-gpx)
	
* [PostgreSQL](#postgresql)
	* [Create PostgreSQL database](#create-postgresql-database)
	* [Create PostGIS and PgRouting extensions in PostgreSQL](#create-postgis-and-pgrouting-extensions-in-postgresql)
	* [Add PostgreSQL extensions](#add-postgresql-extensions)
	* [Import to PostgreSQL from SQL file](#import-to-postgresql-from-sql-file)
	* [Import CSV file to a PostgreSQL table](#import-csv-file-to-a-postgresql-table)
	* [Exports table as a JSON array](#exports-table-as-a-json-array)
	
* [PostGIS](#postgis)
	* [Get the geometry type](#get-the-geometry-type)
	* [Find a table SRID](#find-a-table-srid)
	
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


* [gdal](#gdal)

* [Python](#python)

* [DOS or UNIX Bash routines](#dos-or-unix-bash-routines)
	* [Load all shapefiles into Postgres](#load-all-shapefiles-into-postgres)
	* [Convert all Garmin Fit to GPX](#convert-all-garmin-fit-to-gpx)




	
	
Snippets
=======


GPS
----------

### Export a Garmin fit activity to gpx
```
gpsbabel -t -i garmin_fit -f input.FIT -o gpx -F output.gpx
```

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
```
COPY table_name FROM 'data.csv' WITH DELIMITER ',' CSV HEADER;
```

### Exports table as a JSON array
```
COPY (SELECT array_to_json(array_agg(t)) FROM schema.table as t) to '/output.json'
```

PostGIS
----------

### Get the geometry type
```sql
 SELECT st_geometrytype(the_geom) FROM  table;
```

### Find a table SRID
```sql
SELECT Find_SRID('public', 'table', 'the_geom');
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

gdal
----------


Python
----------


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

## Author: Andres Herrera &copy; 2020

* [@andresherrera](https://twitter.com/andresherrera)

License: MIT - do anything with the code, but don't blame me if it does not work.

Spread the word: tweet, star on github, etc.