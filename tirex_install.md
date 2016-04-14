# Install #
  * Tirex svn
```
 svn co http://svn.openstreetmap.org/applications/utils/tirex/ tirex
```
  * Nõutavad pakid
```
 sudo apt-get install perl-IPC-ShareLite perl-JSON perl-GD perl-libwww-perl
```
  * Build
```
 make; make install
```

# Conf #
  * Nõutavad katloogid. Kasutajal, kes käivitab tirex -i peab olema õigus sinna kataloogidesse kirjutada.
```
/var/run/tirex
/var/lib/tirex
/var/log/tirex
/var/lib/mod_tile
```
  * Linkida kataloog
```
ln -s /storage/tiles /var/lib/mod_tile
ln -s /storage/tiles /var/lib/tirex/tiles
```
  * /etc/tirex/tirex.conf
```
stats_dir=/var/lib/tirex/stats
modtile_socket_name=/var/lib/tirex/modtile.sock
socket_dir=/var/run/tirex
master_pidfile=/var/run/tirex/tirex-master.pid
master_syslog_facility=daemon
master_rendering_timeout=10
bucket name=live       minprio=1  maxproc=4 maxload=20
bucket name=important  minprio=10 maxproc=3 maxload=8
bucket name=background minprio=20 maxproc=2 maxload=4
```

  * Tirexi mapniku renderdaja conf /etc/tirex/renderer/mapnik/mapnik.conf
```
name=mapnik
tiledir=/var/lib/tirex/tiles/mapnik/
minz=0
maxz=19
mapfile=/home/mapnik/mapnik/osm.xml
```

  * /etc/apache2/mods-enabled/mod\_tile.conf
```
ModTileRenderdSocketName /var/lib/tirex/modtile.sock
ModTileTileDir  /var/lib/mod_tile
AddTileConfig   /tiles/test test
AddTileConfig   /tiles/mapnik mapnik
```

  * Näidis kaart kasutades mapniku renderdajat
```
<html>
  <head>
    <title>OpenLayers Demo</title>
    <style type="text/css">
      html, body, #basicMap {
          width: 100%;
          height: 100%;
          margin: 0;
      }
    </style>
    <script src="http://www.openlayers.org/api/OpenLayers.js"></script>
    <script>
      function init() {
        map = new OpenLayers.Map("basicMap");
        var newL = new OpenLayers.Layer.OSM("MAP", "tiles/mapnik/${z}/${x}/${y}.png", {numZoomLevels: 19});
        map.addLayer(newL);
        map.setCenter(new OpenLayers.LonLat(26.1234,59.1234) // Center of the map
          .transform(
            new OpenLayers.Projection("EPSG:4326"), // transform from WGS 1984
            new OpenLayers.Projection("EPSG:900913") // to Spherical Mercator Projection
          ), 14 // Zoom level
        );
      }
    </script>
  </head>
  <body onload="init();">
    <div id="basicMap"></div>
  </body>
</html>
```
# Run #
Mitte root kasutajaga (mapnik)
```
tirex-master &
```

```
tirex-backend-manager &
```

Et näha tirexi tegemisi
```
tirex-status
```