# Probleemid Mapnik vaikimisi kaardikujundusega #
  * Üldiseid: http://www.41latitude.com/post/1310985699/openstreetmap-critique

# Eesti-spetsiifilisi täiendusideid #
Allolevad on jaakl subjektiivsed ettepanekud, mille üle võib alati diskuteerida. Üldine eesmärk on teha eelkõige võimalikult neutraalne kaardikujundus, mis näeks välja professionaalne, ei torkaks liialt silma nii kaardina (kus on oluline inforikkus) kui taustakaardina (mille peal näidatakse muud infot pealiskihina) kasutades.

  * Kohanimed kohalikus keeles -> muuta eesti(kui pole siis inglise) keelseks.
  * Külanimed on halvasti nähtavad, võiksid olla väiksema suurendusega välja tulla.
  * Teed on metsadeinfoga koos halvasti nähtavad. Eriti zoom=9,8
  * Metsaliigid ei eristu metsakihis. Praegu on kõik kuusekestena - sega ja lehtmetsal võiks olla pisut teine märk
  * Põllud tuleks teha valgeks, nähtamatuks, praeguse pruuni asemel
  * Osad suurendused on väikese infoga. Näiteks zoom 12-13 külanimed, zoom=11 võiks valla (küla?) nimed olla, zoom=10 vallanimed, zoom=8 linnad ja asulad. Zoom 14 tertiary tee nimed (suured tänavad linnades)
  * POIsid võiks rohkem olla. Ilmselt on vajalik eraldi kujundusvarianti POIdega (täiskaart) ja ilma nendeta (taustakaardiks)
  * Põllud (roheline muster) võiks 1 tase väiksema zoomiga välja tulla (zoom=7). Katsetada vähemalt.
  * Soine mets ei tule välja, võiks olla roheline märgala triipudega näiteks
  * Linnas võikisid majade polügonid ja hoonestusalad olla vähemvärvilised, hallid (nagu googles)?
  * POI ikoonide täiendus - kas päris oma komplekt või mõned täiendavad? Suuremad ikoonid, mida saab klikitavaks teha

# Tehnilised vahendid/juhendid #
  * Esmaseks katsetuseks sobib maps.cloudmade.com Style Editor, sama seal saab muuta üsna vähe  asju.
  * Lõpptulemus tuleb vormistada Mapnik XML-kujundusena (Mapnik XML stylesheet). Juhendeid (tõsi üsna napilt) ja viite praegusele default kujundusele leiab http://wiki.openstreetmap.org/wiki/Mapnik#Customizing_the_rendering .
  * Mapnik kujundusfaili ametlik kirjeldus on http://trac.mapnik.org/wiki/XMLConfigReference

# 30-sek juhend Mapnik stylesheeti #
Mapnik stylesheet on XML fail, mis koosneb järgnevatest sektsioonidest:
  * 

&lt;Map&gt;

 - ei ole vaja muuta
  * 

&lt;Style&gt;

 - stiilid. Igal stiilil on unikaalne nimi ja seal sees on hulk  joonistamise reeglid (Rule). Reeglil on tavaliselt kolm osa:
    1. suurendusvahemiku määramine, kus stiil toimib. Näiteks suurendus (zoom) 10-12.
    1. filter, mis määrab andme-tag-d mille kohta stiil kehtib. Näiteks [highway](highway.md)='residental' tähendab tänavaid
    1. symboli definitsioon, varieerub olenevalt kas tegu on punktide, aladega, tekstidega või joontega. Siin pannaksegi paika värvid, laiused, fondid, markerid jms
  * 

&lt;Layer&gt;

 - kaardikihid. Style veel üksi ei joonista kaardile midagi, see määrab vaid kujunduse sisu, info lisamiseks peab olemas olema ka vastav Layer. Igal kihil on
    1. Üldandmed - unikaalne nimi, projektsioon
    1. Stiil, millega seda joonistatakse - viide _Style_- elemendi nimele
    1. Datasource - kust andmed loetakse: SQL ja viide baasile. SQL kirjutamiseks võib olla vaja baasieksperdi abi, aga sageli peaks saama olemasolevatest päris hea idee.
    1. Kihid joonistatakse alt-üles, st kõige viimane failis jääb kaardipildil "kõige peale"
  * Kui uurid OSM peamist kujundust (http://trac.openstreetmap.org/browser/applications/rendering/mapnik/osm.xml), siis näed et seal pole tegu ühe XML-failiga, vaid see on jagatud mitmetesse failidesse: eraldi failides on tehnilised parameetrid (nt andmebaasiühendus) ja mitmed kihtide grupid (nt veed). Lihtsalt üks fail läheks liiga suureks. Sisse loetakse kõik http://trac.openstreetmap.org/browser/applications/rendering/mapnik/inc failid. Sümbolipildid on http://trac.openstreetmap.org/browser/applications/rendering/mapnik/symbols kataloogis