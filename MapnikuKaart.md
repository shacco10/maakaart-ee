**Uuendus 2014 - kaart.maakaart.ee ei kasuta seda enam, kaarti teeb MapServer ja andmeid impordib imposm. Probleem oli uuenduste lugemisel - kogu maailma kohta nõudsid liialt ressursse.**

## osm2pgsql ##
  * Loo uus postgis baas '**gis**' 5433 pordis olevasse baasi (/suur/imposmdata kataloogis cluster). Vt eespool käske
  * aktiveeri seal ka hstore extension, PostGres 9.0+ käib see SQL asemel järgnevalt:
```
createdb gis -p 5433
psql -d gis -p 5433 -f /usr/share/postgresql/9.1/contrib/postgis-1.5/postgis.sql
psql -d gis -p 5433 -f /usr/share/postgresql/9.1/contrib/postgis-1.5/spatial_ref_sys.sql
psql -p 5433 gis
create extension hstore;
```
  * kompileeri osm2pgsql - viimane svn versioon
```
mkdir /opt/osm2pgsql/src
cd /opt/osm2pgsql/src
svn co http://svn.openstreetmap.org/applications/utils/export/osm2pgsql/
cd osm2pgsql/
./autogen.sh
./configure
sed -i 's/-g -O2/-O2 -march=native -fomit-frame-pointer/' Makefile
make
```
  * Käivita import kasutades kiiret lahtipakkimise meetodit. Täisplaneedi kogu import võttis ikkagi vähemalt 5 ööpäeva.
```
cd  /opt/osm2pgsql/src/osm2pgsql
bunzip2 -c /home/jaakl/planet-111123.osm.bz2 |./osm2pgsql --slim -d gis -U postgres --hstore -C 10000 --number-processes=4 -P 5433 /dev/stdin
```

## Mapnik ja Mod\_tile ##
Kasutan https://launchpad.net/~kakrueger/+ppa-packages ubuntu pakette.
```
sudo add-apt-repository ppa:kakrueger/openstreetmap
sudo apt-get update
sudo apt-get install apache2-dev
sudo apt-get install renderd
sudo apt-get install libapache2-mod-tile
sudo apt-get install unifont
```
Tulemus:
  1. paketiga tulev layout ja shapefailid on koondatud **/etc/mapnik-osm-data/osm.xml**
  1. see on libmapnik 0.7.1, mis töötab Mapnik kujundusega ilma seda konvertimata

Lisasammud:
  1. kustuta pakitud failid /etc/mapnik-osm-data/ kataloogist, vabastad sadu megabaite:
```
cd /etc/mapnik-osm-data/
sudo rm *bz2
sudo rm *tgz
sudo rm *zip
```
  1. tõsta failid suuremale storagele, tee symlink tagasi
```
sudo cp /etc/mapnik-osm-data /suur/mapnik-osm-data/
sudo rm -rf /etc/mapnik-osm-data
sudo ln -s /suur/mapnik-osm-data /etc
```
  1. kombineeri apache konfides default ja tileserver\_site konfid, kontrolli ega path-is konflikte pole
  1. suurenda apache konfifailis aegu mis on tile genereerimiseks lubatud, muidu tuleb vähemalt esialgu liiga palju 404 not found.
  1. konfi /etc/renderd.conf, lisa est töötav kujundus, muuda fontide otsingut et unifont leitaks, määra path-id
```
[renderd]
stats_file=/var/run/renderd/renderd.stats
socketname=/var/run/renderd/renderd.sock
num_threads=4
tile_dir=/var/lib/mod_tile ; DOES NOT WORK YET

[mapnik]
plugins_dir=/usr/lib/mapnik/0.7/input
font_dir=/usr/share/fonts/truetype/
font_dir_recurse=true

[default]
URI=/modtile/default/
XML=/etc/mapnik-osm-data/osm.xml
;HOST=tile.openstreetmap.org
;HTCPHOST=proxy.openstreetmap.org

[est]
URI=/modtile/est/
XML=/home/mapnik/mapnik/osm_et.xml
;HOST=tile.openstreetmap.org
;HTCPHOST=proxy.openstreetmap.org
```
  1. unifont (hiina tähed jms) jaoks modi /etc/mapnik-osm-data/inc/fontset-settings.inc faili
  1. Konfi datasource-settings.xml.inc baasi ühendus (port 5433, user postgres). Kõikidel kujundustel.
  1. Eestikeelsete kohanimede jaoks inc/layer-placenames\_et.xml.inc failis on "name" päritud hstore veerust: _...,COALESCE(tags -> 'name:et',tags -> 'name:en', "name") AS name ..._
```
sudo sed -i 's/,name,/,COALESCE(tags -> '\''name:et'\'',tags -> '\''name:en'\'', "name") AS name,/' layer-placenames_ee.xml.inc
```
  1. muuda tilekataloog suuremale storagele:
```
sudo service renderd stop
sudo cp -r /var/lib/mod_tile /suur
sudo chown -R www-data /suur/mod_tile
sudo rm -rf /var/lib/mod_tile
sudo ln -s /suur/mod_tile /var/lib
sudo service renderd start

```
  1. iga kujunduse, renderd.conf ja osm.xml-ide muutmine nõuab nii apache2 kui renderd restarti:
```
sudo service renderd restart
sudo service apache2 restart
```
  1. renderd logi (veateated jms) leiab: /var/log/syslog.1
  1. eelrenderda madalad zoomid, eestikeelne Eesti ala detailsemalt:
```
render_list -s /var/run/renderd/renderd.sock --all -z 0 -Z 10 -n 4 -m est
render_list -s /var/run/renderd/renderd.sock --all -z 0 -Z 10 -n 4 -m default
render_list -s /var/run/renderd/renderd.sock --all -y 598 -Y 621 -x 1148 -X 1184 -z 11 -Z 11 -n 4 -m est
render_list -s /var/run/renderd/renderd.sock --all -y 1196 -Y 1242 -x 2296 -X 2368 -z 12 -Z 12  -n 4 -m est
render_list -s /var/run/renderd/renderd.sock --all -y 2392 -Y 2484 -x 4592 -X 4736 -z 13 -Z 13  -n 4 -m est
render_list -s /var/run/renderd/renderd.sock --all -y 4784 -Y 4968 -x 9184 -X 9472 -z 14 -Z 14  -n 4 -m est
render_list -s /var/run/renderd/renderd.sock --all -y 9568 -Y 9936 -x 18368 -X 18944 -z 15 -Z 15  -n 4 -m est
render_list -s /var/run/renderd/renderd.sock --all -y 19136 -Y 19872 -x 36736 -X 37888 -z 16 -Z 16  -n 4 -m est
render_list -s /var/run/renderd/renderd.sock --all -y 38272 -Y 39744 -x 73472 -X 75776 -z 17 -Z 17  -n 4 -m est
render_list -s /var/run/renderd/renderd.sock --all -y 76544 -Y 79488 -x 146944 -X 151552 -z 18 -Z 18  -n 4 -m est
```

## Mapnik baasi pidev uuendus (minutely mapnik) ##
Note: Imposm/Mapserver-i baasi uuendamiseks on täna veel ainus võimalus uus täisimport.

Allolev on peamiselt http://wiki.openstreetmap.org/wiki/DE:Minutely_Mapnik baasil.
  1. Installi ja kohanda load-next skript. Siin on minu modifitseeritud [load-next](LoadNext.md) täiskujul.
```
cd /opt
svn co https://svn.toolserver.org/svnroot/mazder/diff-import/ diffs
cd diffs
edit load-next
 # set database connections, directories for tools
 # osm2pgsql command modify to add port there, remove unneeded parameters (style, host)
 # tile_exipry/expire.rb (aeglane!) asemel kasutada 10x kiiremat mod_tile-ga tulevat render_expired skripti, ja seda kahe tileseti peal.
```
  1. Genereeri state.txt, muutes URL-is kuupäev vastavalt viimasele imporditud planeedi ajale.
```
wget "http://toolserver.org/~mazder/replicate-sequences/?Y=2011&m=11&d=23&H=01&i=00&s=00&stream=minute" -O state.txt
```
  1. Esialgu, kui planeet on päevi vanu (minul suisa 30 pv) 1-tunnise intervalliga jooksev uuendus, igas uuenduses on 6 tundi andmeid. Kontrolli konfi ja lisa crontab-i load-next käivitus:
```
nano configuration.txt
# 6 hours
maxInterval = 21600

sudo -u jaakl crontab -e
@hourly /opt/diffs/load-next
```
  1. TODO: kui andmed hakkavad jõudma suhteliselt värskeks (< 1 pv vanaks), siis tuleks vähenda samm-sammult eriti crontabis (maxInterval võib jääda?) uuenduste perioodi, et kiiremad uuendused oleks, kuni 1 minutini välja.
  1. load-next toimimist jälgi logidest, peamiselt neist failidest. Enamus aega võtab osm2pgsql baasi lugemine:
```
tail /opt/diffs/logs/run.log
tail -f /opt/diffs/logs/osm2pgsql.log
```
  1. Tekitada webi skript mis näitab uuenduste seisu http://kaart.maakaart.ee/planetstate.php:
```
nano /var/www/planetstate.php
<?php
echo "Mapnik database is currently (more than) ";
echo `/opt/diffs/replag -h`;
echo " old.<br/>";
$filename = '/suur/mod_tile/planet-import-complete';
if (file_exists($filename)) {
    echo "$filename was last modified: " . date ("F d Y H:i:s.", filemtime($filename));
}
echo "<br/>";

$statefile = '/opt/diffs/state.txt';
echo $statefile." :<br/><pre>";
echo file_get_contents($statefile);
echo "</pre>";
?>
```

Kontrolli kui palju oleme ajast maas:
```
jaakl@kaart:/opt/diffs$ sudo -u www-data osmosis -q --read-replication-lag humanReadable=yes workingDirectory=/opt/diffs
```