# mapserver configs for sctc wms vm
Mapserver configs for the WMS hosted on the SCTC vm for Frank D and the PVT. 

Some notes on the layout of the services:
```
data - /ogc/data/ (big data area)    
configs - /ogc/maps/pvt/(mapserver configs)
          /ogc/maps/pvt/map_wrap/conf.yml
map_wrap - 
   
```
requests like:
(map wrap config set)/url
http://host/pvt/test (for example
