# Geo-snippets

This repository contains a collection of code snippets for geo developers

## Snippets

### GPS

Garmin fit activity  to gpx
```
gpsbabel -t -i garmin_fit -f input.FIT -o gpx -F output.gpx
```

### PostgreSQL

Create database
```
createdb -U user -p 5432 -h localhost -E UTF8 -e databasename 
```

Create extension
```
psql -U user -p 5432 -h localhost -d databasename -c "CREATE EXTENSION postgis;"
```

Import from SQL file
```
psql -U user -d databasename -h localhost -p 5432 -f input_file.sql
```

Import CSV file
```
COPY table_name FROM 'data.csv' WITH DELIMITER ',' CSV HEADER;
```

### PostGIS



### shp2pgsql

Shapefile to SQL
```
shp2pgsql -g the_geom -s 4326 -W latin1 shapefile table_name > output.sql
```

### ogr2ogr

New shapefile filtered by criteria
```
ogr2ogr -sql "SELECT * FROM input WHERE criteria=1" output.shp input.shp
```

Shapefile to GML
```
ogr2ogr -f GML -t_srs crs:84 output.gml input.shp
```

Shapefile to GeoJSON
```
ogr2ogr -f GeoJSON -t_srs crs:84 output.geojson input.shp
```

Shapefile to kml
```
ogr2ogr -f KML -t_srs crs:84 output.KML input.shp
```

Import to database from csv and Virtual File Format (vrt)  - [Example data](ogr2ogr/pg_from_csv)
```
ogr2ogr -a_srs epsg:4326 -f "PostgreSQL" PG:"dbname=database host=localhost user=postgres password=postgres port=5432" file.vrt
```

csv to dbf 
```
ogr2ogr -f "ESRI Shapefile" output.dbf input.csv
```

csv to shapefile - [Example data](ogr2ogr/csv_to_shp)
```
ogr2ogr -f "ESRI Shapefile" output_shp_xy data.vrt
```

### gdal


### python


## Author: Andres Herrera &copy; 2020

* [@andresherrera](https://twitter.com/andresherrera)

License: MIT - do anything with the code, but don't blame me if it does not work.

Spread the word: tweet, star on github, etc.