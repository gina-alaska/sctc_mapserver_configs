Notes:

Data Prep:

(Source data from here: http://feeder.gina.alaska.edu/npp-gina-alaska-landcover-images/2016_04_06_23_53_jd_97 )

1) Warp the image to a geographic projection like used by the PVT
gdalwarp -srcnodata 0 -dstnodata 0 -co TILED=YES -co COMPRESS=DEFLATE -co ZLEVEL=9 -t_srs epsg:4326 17_17_52_731_npp.16097.2353_I03_I02_I01.tif 17_17_52_731_npp.16097.2353_I03_I02_I01.geo.tif
Creating output file that is 21903P x 2276L.
Processing input file 17_17_52_731_npp.16097.2353_I03_I02_I01.tif.
Using internal nodata values (eg. 0) for image 17_17_52_731_npp.16097.2353_I03_I02_I01.tif.
0...10...20...30...40...50...60...70...80...90...100 - done.

2) Add overviews - reduced resolution versions of the data so the rendering is fast
add_overviews.rb 17_17_52_731_npp.16097.2353_I03_I02_I01.geo.tif 
Runner running "gdaladdo -r average  17_17_52_731_npp.16097.2353_I03_I02_I01.geo.tif  2  4  8  16  32  64  128  256  512  1024  2048  4096  8192 "
0...10...20...30...40...50...60...70...80...90...100 - done.


if there are issues with the dateline, you might need to add -wo SOURCE_EXTRA=100 



Adding data to an existing service:
1) Copy the data to the ogc-pvt box, under "/ogc/data/sctc-pvt/", ideally grouped in some directory structure
Note: the data should be world readable, and the directory world readable and world exicutable  (chmod a+r file | chmod a+rx dirname)
2) create a layer definition in the git repo https://github.com/gina-alaska/sctc_mapserver_configs
For example: layer/example/test.layer.map

The basic template looks like this:
LAYER
  NAME "Example"
  STATUS on
  DATA "/ogc/data/sctc-pvt/example/example.tif"
  TYPE RASTER
  OFFSITE 0 0 0 
  DEBUG on
  include "includes/projections/geo.include.map"
  GROUP "Test"
  METADATA
       "wms_title"      "Example"
       WMS_ABSTRACT     "Example for pvt"
        "wms_group_abstract"    "Example for pvt"
        "wms_group_title"       "Example for pvt"
  END
END

Notes: 
NAME must be unique
GROUP can be used to group layers together
wms_title is what the item will be refered to in the wms querry / call string
DATA should be the full path to the data

3) add it to the map file (example.map
MAP
        include "defaults/example.wms.default.includes.map"
        include "layers/example/example.layer.map"
	include "layers/example/test.layer.map"
END

Docs on the syntax and meaning can be found here: 
http://mapserver.org/mapfile/map.html


If you want to create a seperate service:
1) create a new root mapfile
2) copy the example.wms.default.includes.map to a new default.include.map file, adjusting the "wms_onlineresource" to point to the new url you are configuring, the name, and possibly other things, depending on your purpose
3) include the default.include.map you just made, and setup layers
4) make an entry in the map_wrap/conf.yml in the configs section:
configs:
  "/example":
    prefix: "extras"
    url: "extras"
    projections:
      default: "example.map"
  "/new_url":
    prefix: "new_url"
    url: "new_url"
    projections:
      default: "new_mapfile.map"

This would have http://ogc-pvt.gina.alaska.edu/sctc-pvt/new_url use new_mapfile.map

