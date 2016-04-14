# Tutvustus #

Aadressotsing võimaldab kiiresti leida Eesti aadresse. Üldine loogika:
  1. Otsi Eesti OSM andmetest kasutades [OSM Nominatim-i](http://wiki.openstreetmap.org/wiki/Nominatim). Täiendada seda (lisada sinna juurde Eesti tänavalühendid jms eripärad), kuni toimib piisavalt hästi OSM andmete seisukohast.
  1. Otsi ADS-i andmebaasist. See on SQL andmebaas (PostgreSQL ilmselt), kuhu on ADS tabelid sisse laetud. Alternatiiv oleks laadida OSM-i baasi aadresspunktid, siis eelmine punkt lahendaks ka selle asja.
  1. Otsi globaalsetest OSM andmetest. Suuresti sama mis eelnev.

# Üldnõuded #
  * JSON (so AJAX) API
  * Vastus <100 ms

# Testid #
Osalised aadressid, üks vaste (esimene kõige parem):
  * Kastani 16, Tartu
  * Tartu
  * Kastani 14/16, Tartu
  * Lõunakeskus
  * Pihkva

Täisaadress, üks vaste eeldatav (võetud Muuseumite tablist paljud näited, tabel on saadaval):
  * Kiltsi alevik Väike-Maarja vald 46201 Lääne-Virumaa
  * Tartu mnt 21, Ahja 63701, Põlvamaa
  * Tähtvere 56/62, 50050 Tartu
  * Ahja, Tartu 21
  * Vetepere küla Albu vald 73402 Järvamaa
  * Vetepere, Albu vald 73402 Järva maakond
  * Vetepere, Albu 73402 Järva maakond
  * Vetepere, Albu, Järvamaa
  * Vene 16 Tallinn 10123
  * Vene 16 Tallinn
  * Tina 23-13 10126 Tallinn
  * Tartu mnt 12, Elva
  * Elva 2, Tartu
  * Vallimaa 15 a, 93812 Kuressaare
  * Vabrikuväljak 8, 92411 Kärdla, Hiiumaa
  * Vabriku väljak 8, 92411 Kärdla Hiiumaa
  * Lubjassaare talu Suure-Jaani Vald 71305 Viljandimaa
  * Paide tee 3 Koeru 73001 Järvamaa
  * Kursi pastoraat Kursi küla Puurmani vald 49001 Jõgevamaa
  * Saare/Lyckholmi küla, Noarootsi vald 91220  Läänemaa


Mitu vastet:
  * Pappa Pizza
  * Vene 16

> Vaata [siin source all](http://code.google.com/p/maakaart-ee/source/browse/trunk/server/adb/) PHP-s tehtud prototüüpi, mille probleemiks on et on aeglane ja ei läbi osasid teste (leiab asukohti valest linnast)