
```
#!/bin/bash
# loads the diffs for the interval read from configuration.txt
# may be executed every 5 minutes or so

PIDFILE=`basename $0`.pid

OSMOSIS=/var/lib/postgresql/osmosis/bin/osmosis # $HOME/tools/osmosis/bin/osmosis
OSM2PGSQL=/opt/osm2pgsql/src/osm2pgsql/osm2pgsql #$HOME/bin/osm2pgsql
STYLE=/opt/osm2pgsql/src/osm2pgsql/default.style

# java proxy settings for osmosis
#JAVACMD_OPTIONS="-Dhttp.proxyHost=ha-proxy.esi -Dhttp.proxyPort=8080"
#export JAVACMD_OPTIONS

OSMOSISLOG=logs/osmosis.log
PSQLLOG=logs/osm2pgsql.log
EXPIRYLOG=logs/expiry.log
RUNLOG=logs/run.log

HOST=
DB=gis
PREFIX=planet
USER=postgres
PORT=5433

CURRENT=/tmp/osm-load-next.$$.osc
EXPIRE=1

m_info()
{
	echo "[`date +"%Y-%m-%d %H:%M:%S"`] $$ $1" >> "$RUNLOG"
}

m_error()
{
	echo "[`date +"%Y-%m-%d %H:%M:%S"`] $$ [error] $1" >> "$RUNLOG"
	
	m_info "resetting state"
	/bin/cp last.state.txt state.txt
	
	rm "$PIDFILE"
	exit 1
}

m_ok()
{
	echo "[`date +"%Y-%m-%d %H:%M:%S"`] $$ $1" >> "$RUNLOG"
}

getlock()
{
	if [ -s $PIDFILE ]; then
		if [ "$(ps -p `cat $PIDFILE` | wc -l)" -gt 1 ]; then
			return 1 #false
		fi
	fi
	
	echo $$ >"$PIDFILE"
	return 0 #true
}

freelock()
{
	rm "$PIDFILE"
}

WDIR=`dirname $0`
pushd $WDIR >/dev/null
#m_info "Workingdir $WDIR"

if ! getlock; then
	m_info "pid `cat $PIDFILE` still running"
	exit 3
fi

if [ -e stop -o -e stop.txt ]; then
	m_info "stopped"
	exit 2
fi


m_ok "start import"
echo $$ >"$PIDFILE"

/bin/cp state.txt last.state.txt
m_ok "downloading diff"
if ! $OSMOSIS --read-replication-interval --simplify-change --write-xml-change "$CURRENT" 1>&2 2> "$OSMOSISLOG"; then
	m_error "osmosis error"
fi

NODES=`grep '<node' < "$CURRENT" |wc -l`
WAYS=`grep '<way' < "$CURRENT" |wc -l`
RELS=`grep '<rel' < "$CURRENT" |wc -l`

m_info "expecting Node("$((NODES / 1000))"k) Way("$((WAYS / 1000))"k) Relation("$((RELS / 1000))"k)"

m_ok "importing diff"
if ! $OSM2PGSQL --append --slim -e15 -o expire.list --number-processes=4 --cache 2048 --merc --database $DB --username $USER -P $PORT --hstore --verbose "$CURRENT" 1>&2 2> "$PSQLLOG"; then
	m_error "osm2pgsql error"
fi

m_ok "import done"
freelock

if [ $EXPIRE -gt 0 ]; then
	m_ok "expiring tiles"
#	if ! sudo -u www-data tile_expiry/do-expire.rb "$CURRENT" 2>&1 > "$EXPIRYLOG"; then
	# jaakl: this do-expire.rb is not optimal, better should be to use osm2plsql expiry list as following
	# it seems that big changeset with do-expire.rb takes minutes, render_expired takes 30 sec (~10x faster)
	if ! sudo -u www-data cat expire.list | /usr/bin/render_expired  -s /var/run/renderd/renderd.sock --map=default --min-zoom=10 --touch-from=10 2>&1 > "$EXPIRYLOG"; then
		#m_error "tile_expiry error"
		m_ok "tile_expiry error"
	fi
	if ! sudo -u www-data cat expire.list | /usr/bin/render_expired  -s /var/run/renderd/renderd.sock --map=est --min-zoom=10 --touch-from=10 2>&1 > "$EXPIRYLOG"; then
		#m_error "tile_expiry error"
                m_ok "tile_expiry error"
	fi
        /bin/rm -f expire.list
	m_ok "expire done"
fi

/bin/rm -f "$CURRENT"

if [ `wc -l < "$RUNLOG"` -gt 1000 ]; then
	m_ok "truncating runlog"
	tail -n500 "$RUNLOG" > "$RUNLOG".tmp
	mv "$RUNLOG".tmp "$RUNLOG"
fi

popd >/dev/null

```