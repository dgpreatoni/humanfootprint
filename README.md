#### How to (re)calculate the "human footprint"
The following ingredient list stems from an educated choice of what is available in the literature. Most papers use only small-scale, low-resolution readmade rasters describing land cover / land use (LULC herainafter) or human demography or signatures (populaiton dnesity, built areas, road networks).
In many papers (e.g Dong et al. 2020) wildlife is often cited but never considered in the calculations.
Furthermore, all the rasters mentironed above are 'summed up' in a standard map algebra exercise, assigning an (arbitrary) rank to each coverage in order to achieve a plausible final score.
Finally, dealing with an evidence-based approach, no _a posteriori_ check of the footprint maps created is made[^1].

[^1] A thorough literature analysis shoud be made here.

#### Requisites
Reinventing the wheel just because we want it a different color is useless, so, of the spatial resolution needed is about 1000 m the publicly available dataset by Venter et al. (2016) is perfect, just use that.

The dataset we need to produce should have the following requisites:
  - coverage as plantewide as possible, **or**
  - based on easily available layers, so that it can be (re)calculated everywhere
  - spatial resolution no more than 250 m (i.e MODIS-friendly), preferred 30 m (i.e. landsat-friendly), eim at 10 m (sentinel-2)

#### Ingredients
As per Sanderson et al. (2002), the basic recipe calls for four different proxies for human influence (see list below): population density, land transformation, accessibility, and electrical power nfrastructure.
Note the total lack of natural elements such as pristine areas, forests, etc.

Basic datasets are:
  - :check: population density, possibly gridded
  - land cover/land transformation
    - built up areas
    - settlements
  - roads, railways
  - natural features such as coestline, lakes, rivers
  - electrical power infrastructure
  - biome distribution
In particular, Sanderson et al. (2002, Table 1) resorted to 9 datasets publicly available, see `https://academic.oup.com/view-large/126030980`.

Also note that in most of the literature, an arbitrary scoring system is attached to the features listed above, in order to rank a pixel on the final footprint map.

A revised, regional scale version of the scoring system has to be devised, too. 

### Possible sources (Italy)
**Note:** it is more convenient use data at the EU scale level, so that no harmonisation will be needed. 
 
ISTAT census data, vector, by census unit `https://www.istat.it/it/archivio/104317`
This should be spatially corrected with built up areas, see `http://foss4g-it2018.gfoss.it/data/20_febbrario/3_archeo/Baiocchi_Lelo_Vatore_FOSS4G-IT_2018.pdf` for an example on a small area.



### Possible sources (EU)
In the following paragraphs the available data sources are described along with the basic procedure (at least at the conceptual level) to preprocess them.

## :heavy_check_mark: Human population pressure
Gallego F.J., (2010) people per cell, 100 m resolution, `http://cmshare.eea.europa.eu/s/NAPoXyePo4J3o5X/download`. Description at `https://data.europa.eu/euodp/en/data/dataset/jrc-ghsl-ghs_pop_eurostat_europe_r2016a/resource/46f53786-71f5-41da-ab62-f8c9dc7f0d1b`


## Road network
  - EuroGeographic (one of the official European GIS data provider) released as an opendata product the EuroGlobalMap dataset: `https://eurogeographics.org/maps-for-europe/open-data/topographic-data/`
  - OpenStreetmap requires preprocessing but perhaps is the finest high-resolution coverage possible. Nightly OpenStreetMap Europe snapshots available on GeoFabrik `http://download.geofabrik.de/osm/`, see `http://www.gdal.org/ogr/ogr_sql.html` for inspiraiton on how to query. Warning: we're in the tenth of GB size region.

CORINE Level IV

Road network, from OSM/GeFabrik (see below)

Railways network, from OSM/GeFabrik (see below)

Power lines? Do they exist?







These could be tested, but look line just points that need to be interpolated
PM10 concentration `https://www.eea.europa.eu/data-and-maps/figures/annual-mean-pm10-concentrations-in-3`
PM2.5 concentration https://www.eea.europa.eu/data-and-maps/figures/annual-mean-pm2-5-concentrations-6


(To be completed)


#### Methods
(define a procedure to have not-so-arbitrary raknings, or borrow and refine already published rankings)


#### References
Dong J, Peng J, Liu Y, Qiu S, Han Y, 2020 Integrating spatial continuous wavelet transform and jernel density estimation to identify ecologivcal corridors in megacities. Landscape and Urban Planning 199: 103815. doi:10.1016/j.landurbanplan.2020.103815

Gallego FJ, 2010. A population density grid of the European Union. Population and Environment 31: 460–473. doi:10.1007/s11111-010-0108-y

Sanderson EW, Jaiteh M, Levy MA, Redford KH, Wannebo AV, Woolmer G, 2002. The Human Footprint and the Last of the Wild: The human footprint is a global map of human influence on the land surface, which suggests that human beings are stewards of nature, whether we like it or not, BioScience 52(10): 891-904. doi:10.1641/0006-3568(2002)052[0891:THFATL]2.0.CO;2

Venter O, Sanderson EW, Magrach A, Allan JR, Beher J, Jones KR, Possingham HP, Laurance WF, Wood P, Fekete BM, Levy MA, Watson JEM,2016. Global terrestrial Human Footprint maps for 1993 and 2009. Scientific Data 3(1): 160067. doi:10.1038/sdata.2016.67



