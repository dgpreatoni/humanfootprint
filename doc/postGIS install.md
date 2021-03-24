#### Postgres + PostGIS install
Started from a basic Debian 10 (Buster) install.

Roughly following Konstantin Klemmer tutorial on [`r-spatial.org`](https://www.r-spatial.org/2017/07/14/large_scale_osm_in_r),

## First, have a working PostGIS server
After a look at [Debian postGIS](https://tracker.debian.org/pkg/postgis), just install the production packages:

```
sudo apt-get install postgresql postgresql-contrib postgis 
```
Then, as per [OSM Wiki](https://wiki.openstreetmap.org/wiki/PostGIS/Installation), time to configure.


# Configuration
Check whether postgres is running: `systemctl status postgreesql`
If not, can bs started with `systemctl start postgresql`

Refer to https://www.postgresql.org/docs/11/index.html

- postgres user account
- check netwoek access on port 5432


--- to be completed --


## On the client side
Install `pgadmin3` (you should also need to install  `postgresql-client`). 

