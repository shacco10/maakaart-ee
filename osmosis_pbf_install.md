  * Uusim osmosis http://bretth.dev.openstreetmap.org/osmosis-build/
  * Geofabriku eksport pbf failid http://download.geofabrik.de/osm/
  * su - postgres
  * createdb osm
  * createlang plpgsql osm
  * createuser **username**
  * psql -d osm -f /usr/share/postgresql/contrib/postgis-1.5/postgis.sql
  * psql -d osm -f /usr/share/postgresql/contrib/postgis-1.5/spatial\_ref\_sys.sql
  * psql -d osm -f /usr/share/postgresql/contrib/hstore.sql
  * psql -d osm -f /usr/local/osmosis/script/pgsql\_simple\_schema\_0.6.sql
  * echo "alter role **username** password 'osm';" | psql -d osm
  * export JAVACMD\_OPTIONS="-Djava.io.tmpdir=/my/pretty/little/tempdir"
  * /usr/local/osmosis-0.38/bin/osmosis --read-pbf file="**file**.osm.pbf" --write-pgsql user=**username** database=osm password=osm