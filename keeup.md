# keepup.sh #
Skript baasi uuendamiseks
```
#!/bin/bash
cd /var/lib/postgresql/up2date
intf=$(sed '6q;d' configuration.txt)
interval=${intf#*=}
cdate=$(sed '3q;d' state.txt)
chd=${cdate#*=}
algus=$(date +%s)

if [ -f keepup.lock ]
then
        exit
else
        echo $$ >> keepup.lock
        ../osmosis/bin/osmosis --read-replication-interval workingDirectory=/var/lib/postgresql/up2date --simplify-change --write-xml-change changes.osc.gz
        osm2pgsql --append --slim -d gis -S /var/lib/postgresql/estonia.style -C16000 changes.osc.gz
        lopp=$(date +%s)
        vahe=$((lopp-algus))
        diff=$((vahe-interval))
        min=$((vahe/60))
        echo $chd " Interval"$interval" Import time "$vahe" sek / "$min" min DIF:"$diff" (- is good)" >> log.txt
        rm keepup.lock
fi

```