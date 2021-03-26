#### Import OpenStreetMap data in a postGIS geodatabase
This is loosely inspored from a [post on r-spatial.org](https://www.r-spatial.org/2017/07/14/large_scale_osm_in_r) by Konstantin Klemmer.

You should already have a working postgres/postGIS server. Refer to `postGIS install.md` for the details.

## Create a database
Using **pgAdmin4**, connect to the database server, and create a database to store OSM data (`Object` - `Create` - `Database`). Remember to assign a dedicated non-superuser `gisuser` as a database owner.

On the newly created database, create the `postgis` and `hstore` estensions: open a SQL script and execute:
```
CREATE EXTENSION postgis;
CREATE EXTRENSION hstore;
```

Check if all extensions are in place using pgAdmin4 `Extensions` path in the Browser.

## Enable postGIS to use osmosis
Open a SQL editor in pgAdmin4 and execute two SQL scripts that are stored in the osmosis directory:
```
psql -U <geodatabase postgres user> -d <geodatabase name> -a -f [PATH_TO_OSMOSIS]\script\pgsnapshot_schema_0.6.sql
psql -U <geodatabase postgres user> -d <geodatabase name> -a -f [PATH_TO_OSMOSIS]\script\pgsnapshot_schema_0.6_linestring.sql
```

In our case, since osmosis has been installed under `/usr/local/share/osmosis` (see `postGIS install.md`), the two command, to be issued as `postgres` UNIX user in the postgres server console will be:
```
psql -U postgres -d OSM_Europe -a -f /usr/local/share/osmosis/script/pgsnapshot_schema_0.6.sql
psql -U postgres -d OSM_Europe -a -f /usr/local/share/osmosis/script/pgsnapshot_schema_0.6_linestring.sql
```

Mind that you need a `.pgpass` file to handle `psql` login.
This can be created in a cinch:
```
echo "localhost:5432:OSMEurope:gisuser:secret" > .pgpass
chmod 0600 .pgpass
```

As an alternative (and this worked better!) just open a "query tool" in pgAdmin4 and copy-paste the two scripts cited above.

With pgAdmin4 check that all the schemata have been created indeed, look in the browser under `Schemas` - `public` - `Tables` in your geodatabase.

Add indices to the database, in an SQL  editor run: 
```
CREATE INDEX idx_nodes_tags ON nodes USING GIN(tags);
CREATE INDEX idx_ways_tags ON ways USING GIN(tags);
CREATE INDEX idx_relations_tags ON relations USING GIN(tags);
```

## Read in data with osmosis
As for any piece of crap in Java, you can set some fancy options to better use RAM and whatnot: edit `~/.osmosis` and add:
```
JAVACMD_OPTIONS=-server 
JAVACMD_OPTIONS=-Xmx17G
```
in this example you tell osmosis to use 17GB of RAM. Adapt to your situation, of course.

Time to read in the `.pbf` file:
```
osmosis --read-pbf file="europe-latest.osm_20210226.pbf" --log-progress --write-psql host="localhost" database="OSMEurope" user="gisuser" password="secret"
```


#### Summing up
Here's all the code that could be run, as `postgres` user on the host running postgresql (loosely based on a snippet left around on github at `https://gist.github.com/TimSC/f5d36a302cf2311b7bdc`):

```
sudo -u postgres createuser gisuser
sudo -u postgres dropdb OSMEurope
sudo -u postgres createdb --encoding=UTF8 --owner=gisuser OSMEurope

sudo -u postgres psql --dbname=OSMEurope -c "CREATE EXTENSION postgis;"
sudo -u postgres psql --dbname=OSMEurope -c "CREATE EXTENSION hstore;"

sudo -u postgres psql --dbname=OSMEurope -c "ALTER USER gisuser WITH PASSWORD 'secret';"

psql --user=gisuser --password --dbname=OSMEurope -f usr/local/share/osmosis/script/pgsnapshot_schema_0.6.sql
psql --user=gisuser --password --dbname=OSMEurope -f usr/local/share/osmosis/script/pgsnapshot_schema_0.6_linestring.sql

psql --user=gisuser --password --dbname=OSMEurope -c "CREATE INDEX idx_nodes_tags ON nodes USING GIN(tags);"
psql --user=gisuser --password --dbname=OSMEurope -c "CREATE INDEX idx_ways_tags ON ways USING GIN(tags);"
psql --user=gisuser --password --dbname=OSMEurope -c "CREATE INDEX idx_relations_tags ON relations USING GIN(tags);"

osmosis --read-pbf file="europe-latest.osm_20210226.pbf" --log-progress --write-psql host="localhost" database="OSMEurope" user="gisuser" password="secret"
```




/home/tim/osm/osmosis/bin/osmosis --read-pgsql database=egypt-osm user=gisuser password='test0199' outPipe.0=pg --dataset-bounding-box inPipe.0=pg top=30.1332509 left=31.1400604 bottom=29.9400604 right=31.3220215 outPipe.0=dd --write-xml inPipe.0=dd file=- | bzip2 > cairo.osm.bz2

/home/tim/osm/osmosis/bin/osmosis --read-pgsql database=egypt-osm user=gisuser password='test0199' outPipe.0=pg --dd inPipe.0=pg outPipe.0=dd --write-xml inPipe.0=dd file=- | bzip2 > everything.osm.bz2




