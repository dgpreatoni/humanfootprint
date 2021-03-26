#### Postgres + PostGIS install
Started from a basic Debian 10 (Buster) install.

Roughly following Konstantin Klemmer tutorial on [`r-spatial.org`](https://www.r-spatial.org/2017/07/14/large_scale_osm_in_r),

## First, have a working PostGIS server
After a look at [Debian postGIS](https://tracker.debian.org/pkg/postgis), just install the production packages:

```
sudo apt-get install postgresql-11 postgresql-11-postgis-2.5 postgresql-11-postgis-2.5-scripts postgresql-client-11 postgresql-client-common postgresql-common postgresql-contrib 
```

Then, as per [OSM Wiki](https://wiki.openstreetmap.org/wiki/PostGIS/Installation), time to configure.


## Configure the PostGIS installation
Check whether postgres is running: `systemctl status postgresql`
If not, can bs started with `systemctl start postgresql`

# Check the postgres user account
Check if the `postgres` user account works, usually is set up during postgres installation, just touch up its password with `passwd postgres` and supply a viable password. Note that this is an UNIX user named 'postgres' and has nothing to do with the _databse user_ named 'postgres' (if any such user exists).

Check for the postgres database administrator: become the `postgres` UNIX user, run `psql`, once in the psql postgres client, issue `\l` command. You should see at the bare minimum data for `postgres` and `template0` databaaes. So far, so good.

Issue a sensible password for the _database user_ named 'postgres': inside psql issue the SQL command:

```
ALTER USER postgres WITH PASSWORD '<your password of choice here>';
```

Once done, you can exit `psql` with `\q`.

# Check network accessibility
Postgres RDBMS should be accessible on port 5432.
Check `postgresql.conf`, should be in `/etc/postgresql/<version>/main/postgresql.conf`.
Look for the `listen_addresses` parameter, usually is commented out, that is, a basic postgresql installation listens on local host only. Uncomment, and change it either to listen on a valid static IP or on '*':

```
# - Connection Settings - 
listen_address = '192.168.1.30'  # place your host IP addrsss here
port = 5432
```

You should also allow connection in `pg_hba.conf`, (as well should be in `/etc/postgresql/<version>/main/pg_hba.conf`), chenge the following stanza

```
# IPv4 local connections:
host    all    all    127.0.0.1/32    md5
```

into

```
# IPv4 local connections:
host    all    all    127.0.0.1/32    md5
host    all    all    192.168.1.0/24  md5
```

that is, allowing MD5 authentication for all hosts sitting in your LAN, provided that your LAN addressing schema is `192.168.1.<whatever>/24`, of course.

Once done, you need to restart postgresql with `systemctl restart postgresql`.

Try connecting from a remote host (same LAN, of course) e.g. using pgAdmin III.

For further information check the [postgresql 11 documentation](Refer to https://www.postgresql.org/docs/11/index.html)


## Check and install the postGIS estensions
It is advisable to create a separate user and database to handle GIS stuff:
```
sudo -u postgres createuser gisuser
sudo -u postgres createdb --encoding=UTF8 --owner=gisuser gis
```

Next, activate postGIS extensions on the `gis` database:
```
psql --username=postgres --dbname=gis -c "CREATE EXTENSION postgis;"
psql --username=postgres --dbname=gis -c "CREATE EXTENSION postgis_topology;"
```

That should be all to set up a postGIS appliance.

#### Set up Osmosis
To have the postGIS server able to ingest OSM data, is a good idea to have at hand osmosis.

On the postGIS server, install the latest version of Osmosis as per [OpenStreetMap wiki](https://wiki.openstreetmap.org/wiki/Osmosis/Installation#Linux). This basically consists in dowloading the latest `tar.gz` grom github, decompress it in a suitable place (what about `/usr/local/share/osmosis`?), making `bin/osmosis` world-executable, symlinking `bin/osmosis` to somewhere in your `$PATH` (what about `/usr/local/bin/osmosis`?) and having a working Java Runtime Environment.

