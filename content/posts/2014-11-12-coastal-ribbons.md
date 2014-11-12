---
author: Christopher Knox
---

Use postgis to create a geojson file containing with a coastal ribbon of fishing 
boundaries.

I used postgis to generate files as google maps overlays for the various paua qmas
and as an aid to remembering the steps I would like to write a blog post. That is
not going to happing just yet so this is a brief out of the process I used so that
I can at least remember it.

Started with: 

- share/dragonfly/gis/paua-stat-areas-custom
		- which was created by Yvan by clipping the original
    shape file with a seawards buffer
- /dragonfly/gis/shapes/nz/

Ran shp2sql on both of these and loaded into database

```
CREATE TABLE paua4_statarea AS
SELECT area_code, code_label, ST_Transform(ST_SetSRID(geom, 2193), 4326) AS geom
FROM “paua-stat-areas-custom”
WHERE area_code like 'P4%';
```

```
CREATE TABLE nz AS
SELECT ST_Union(geom) AS geom FROM “paua_coastline”;
```

```
CREATE TABLE paua4 AS SELECT code_label, area_code, ST_Difference(p.geom, nz.geom) AS geom FROM paua4_statarea p, nz;
```