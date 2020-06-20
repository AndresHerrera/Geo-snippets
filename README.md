# Geo-snippets

**This repository contains a collection of code snippets for geo developers**

## Snippets

### GPS

* Export a Garmin fit activity to gpx:
```
gpsbabel -t -i garmin_fit -f input.FIT -o gpx -F output.gpx
```

### PostgreSQL

* Create PostgreSQL database:
```
createdb -U user -p 5432 -h localhost -E UTF8 -e databasename 
```

* Create PostGIS and PgRouting extensions in PostgreSQL:
```
psql -U user -p 5432 -h localhost -d databasename -c "CREATE EXTENSION postgis;"
psql -U user -p 5432 -h localhost -d databasename -c "CREATE EXTENSION pgrouting;"
```

* Import to PostgreSQL from SQL file:
```
psql -U user -d databasename -h localhost -p 5432 -f input_file.sql
```

* Import CSV file to a PostgreSQL table:
```
COPY table_name FROM 'data.csv' WITH DELIMITER ',' CSV HEADER;
```

### PostGIS

* Get the geometry type:
```sql
 SELECT st_geometrytype(the_geom) FROM  table;
```

* Find a table SRID:
```sql
SELECT Find_SRID('public', 'table', 'the_geom');
```


### shp2pgsql

* Export a shapefile to SQL:
```
shp2pgsql -g the_geom -s 4326 -W latin1 shapefile table_name > output.sql
```

### ogr2ogr

* Export a new shapefile filtered by criteria:
```
ogr2ogr -sql "SELECT * FROM input WHERE criteria=1" output.shp input.shp
```

* Export a shapefile to GML:
```
ogr2ogr -f GML -t_srs crs:84 output.gml input.shp
```

* Export a shapefile to GeoJSON:
```
ogr2ogr -f GeoJSON -t_srs crs:84 output.geojson input.shp
```

* Export a shapefile to KML:
```
ogr2ogr -f KML -t_srs crs:84 output.KML input.shp
```

* Export dbf table to PostgreSQL:
```
ogr2ogr -f "PostgreSQL" PG:"dbname=database host=localhost user=postgres password=postgres port=5432" table_dbf.dbf -nln "newtable"
```

* Import to PostgreSQL table from csv and Virtual File Format (vrt) - [Example data](ogr2ogr/pg_from_csv)
```
ogr2ogr -a_srs epsg:4326 -f "PostgreSQL" PG:"dbname=database host=localhost user=postgres password=postgres port=5432" file.vrt
```

* Export a csv file to dbf:
```
ogr2ogr -f "ESRI Shapefile" output.dbf input.csv
```

* Export a csv file to shapefile:- [Example data](ogr2ogr/csv_to_shp)
```
ogr2ogr -f "ESRI Shapefile" output_shp_xy data.vrt
```

* Export a Postgres table to GeoPackage:
```
ogr2ogr -f GPKG output.gpkg PG:"dbname=database host=localhost user=postgres password=postgres port=5432" "table"
```

* Export many Postgres tables to GeoPackage:
```
ogr2ogr -f GPKG output.gpkg PG:"dbname=database host=localhost user=postgres password=postgres port=5432 tables=table1,table2,table3"
```

* Export a whole Postgres database to GeoPackage:
```
ogr2ogr -f GPKG output.gpkg PG:"dbname=database host=localhost user=postgres password=postgres port=5432"
```

* Load a single layer GeoPackage into Postgres:
```
ogr2ogr -f "PostgreSQL" PG:"dbname=database host=localhost user=postgres password=postgres port=5432" input.gpkg -nln "new_table"
```

### gdal


### python

### DOS or UNIX Bash Commands

* Load all shapefiles into Postgres (allshp2dbpgsql):
```
@echo allshp2dbpgsql
@echo off
color 20
for %%x in (*.shp) do ogr2ogr -f "PostgreSQL" PG:"host=localhost user=postgres dbname=database password=12345 port:5432" %%~nx.shp
pause
exit
```

* Convert all shapefiles into Postgres (allshapes2pgsql):
```
@echo allshapes2pgsql
@echo off
color 20
for %%x in (*.shp) do shp2pgsql -g the_geom -s 4326 -W latin1 %%~nx %%~nx > %%~nx.sql
pause
exit
```

## Author: Andres Herrera &copy; 2020

* [@andresherrera](https://twitter.com/andresherrera)

License: MIT - do anything with the code, but don't blame me if it does not work.

Spread the word: tweet, star on github, etc.