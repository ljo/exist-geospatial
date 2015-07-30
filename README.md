# exist-geospatial
===========================

Integrates geospatial indexing of GML 2D geometries through geotools and HSQLDB into eXist-db.

## Compile and install
If you installed the app through the package manager you only need to do step 5 and restart.

1. clone the github repository: https://github.com/ljo/exist-geospatial
2. edit local.build.properties and set exist.dir to point to your eXist-db install directory
3. call "ant" in the directory to create a .xar
4. upload the xar into eXist-db using the dashboard
5. enable the module in conf.xml, if you have entries for "spatial" just change them to "geospatial", otherwise add one module declaration under "indexer/modules":
```xml
<module id="geospatial-index" connectionTimeout="10000" flushAfter="300" class="org.exist.indexing.geospatial.GMLHSQLIndex"/>
```

## Overview
There are many geometry functions available in the geospatial library module. You find usages for each of them in the usage example below.

## Usage example

```xquery
xquery version "3.0";
import module namespace geospatial="http://exist-db.org/xquery/geospatial" at
"java:org.exist.xquery.modules.geospatial.GeoSpatialModule";

declare namespace gml = "http://www.opengis.net/gml";

let $gmltest-coll := xmldb:create-collection("/db", "gmltest")
let $gmltest-conf-coll := xmldb:create-collection("/db/system/config/db", "gmltest")
let $gml-conf :=
    <collection xmlns="http://exist-db.org/collection-config/1.0">
       <index xmlns:xs="http://www.w3.org/2001/XMLSchema">
          <gml />
       </index>
       <validation mode="no"/>
    </collection>
let $gml-xml-1 :=
    <gml:Polygon xmlns:gml="http://www.opengis.net/gml" srsName="osgb:BNG">
        <gml:outerBoundaryIs>
            <gml:LinearRing>
                <gml:coordinates>
                278515.400,187060.450 278515.150,187057.950
                278516.350,187057.150 278546.700,187054.000
                278580.550,187050.900 278609.500,187048.100
                278609.750,187051.250 278574.750,187054.650
                278544.950,187057.450 278515.400,187060.450
                </gml:coordinates>
            </gml:LinearRing>
        </gml:outerBoundaryIs>
    </gml:Polygon>
let $gml-wkt-polygon := 
"POLYGON ((-3.7530493069563913 51.5695210244188,
-3.7526220716233705 51.569500427086325,
-3.752191300029012 51.569481679670055,
-3.7516853221460167 51.5694586575048,
-3.751687839470607 51.569430291017945,
-3.752106350923544 51.56944922336166,
-3.752595638781826 51.5694697950237,
-3.753034464037513 51.56949156828257,
-3.753052048201362 51.56949850020053,
-3.7530493069563913 51.5695210244188))"
    
let $dummy1 := xmldb:store($gmltest-conf-coll, "collection.xconf", $gml-conf)
let $dummy2 := xmldb:store($gmltest-coll, "gml.xml", $gml-xml-1)

let $query1 := geospatial:boundary($gml-xml-1)
let $query2 := geospatial:buffer((collection("/db/gmltest")//gml:Polygon)[1], 100)
let $query3 := geospatial:buffer((collection("/db/gmltest")//gml:Polygon)[1], 100, 1)
let $query4 := geospatial:buffer((collection("/db/gmltest")//gml:Polygon)[1], 100, 1, 1)
let $query5 := geospatial:contains(collection("/db/gmltest")//gml:*, collection("/db/gmltest")//gml:Point[gml:coordinates[. = '278697.450,187740.900']])
let $query6 := geospatial:convexHull((collection("/db/gmltest")//gml:Polygon)[1])
let $query7 := geospatial:crosses(collection("/db/gmltest")//gml:*, collection("/db/gmltest")//gml:Point[gml:coordinates[. = '278697.450,187740.900']])
let $query8 := geospatial:difference($gml-xml-1, (collection("/db/gmltest")//gml:Polygon)[1])
let $query9 := geospatial:disjoint(collection("/db/gmltest")//gml:*, collection("/db/gmltest")//gml:Point[gml:coordinates[. = '278697.450,187740.900']])
let $query10 := geospatial:equals(collection("/db/gmltest")//gml:*, $gml-xml-1)
let $query11 := geospatial:equals(collection("/db/gmltest")//gml:*, collection("/db/gmltest")//gml:Point[gml:coordinates[. = '278697.450,187740.900']])
let $query12 := geospatial:getArea((collection("/db/gmltest")//gml:Polygon)[1])
let $query13 := geospatial:getBbox($gml-xml-1)
let $query14 := geospatial:getCentroidX($gml-xml-1)
let $query15 := geospatial:getCentroidY($gml-xml-1)
let $query16 := geospatial:getEPSG4326Area($gml-xml-1)
let $query17 := geospatial:getEPSG4326CentroidX($gml-xml-1)
let $query18 := geospatial:getEPSG4326CentroidY($gml-xml-1)
let $query19 := geospatial:getEPSG4326MaxX($gml-xml-1)
let $query20 := geospatial:getEPSG4326MaxY($gml-xml-1)
let $query21 := geospatial:getEPSG4326MinX($gml-xml-1)
let $query22 := geospatial:getEPSG4326MinY($gml-xml-1)
let $query23 := geospatial:getEPSG4326WKB($gml-xml-1)
let $query24 := geospatial:getEPSG4326WKT($gml-xml-1)
let $query25 := geospatial:getGeometryType($gml-xml-1)
let $query26 := geospatial:getMaxX($gml-xml-1)
let $query27 := geospatial:getMaxY($gml-xml-1)
let $query28 := geospatial:getMinX($gml-xml-1)
let $query29 := geospatial:getMinY($gml-xml-1)
let $query30 := geospatial:getSRS($gml-xml-1)
let $query31 := geospatial:getWKB($gml-xml-1)
let $query32 := geospatial:getWKT($gml-xml-1)
let $query33 := geospatial:intersection($gml-xml-1, (collection("/db/gmltest")//gml:Polygon)[1])
let $query34 := geospatial:intersects($gml-xml-1, collection("/db/gmltest")//gml:Point[gml:coordinates[. = '278697.450,187740.900']])
let $query35 := geospatial:isClosed($gml-xml-1)
let $query36 := geospatial:isSimple($gml-xml-1)
let $query37 := geospatial:isValid($gml-xml-1)
let $query38 := geospatial:overlaps($gml-xml-1, collection("/db/gmltest")//gml:Point[gml:coordinates[. = '278697.450,187740.900']])
let $query39 := geospatial:symetricDifference($gml-xml-1, (collection("/db/gmltest")//gml:Polygon)[1])
let $query40 := geospatial:touches($gml-xml-1, collection("/db/gmltest")//gml:Point[gml:coordinates[. = '278697.450,187740.900']])
let $query41 := geospatial:transform($gml-xml-1, "EPSG:4326")
let $query42 := geospatial:union($gml-xml-1, (collection("/db/gmltest")//gml:Polygon)[1])
let $query43 := geospatial:within($gml-xml-1, collection("/db/gmltest")//gml:Point[gml:coordinates[. = '278697.450,187740.900']])
let $query44 := geospatial:WKTtoGML($gml-wkt-polygon, "EPSG:4326")
let $queries := ($query1, $query2, $query3, $query4, $query5, $query6, $query7, $query8, $query9,
$query10, $query11, $query12, $query13, $query14, $query15, $query16, $query17, $query18, $query19,
$query20, $query21, $query22, $query23, $query24, $query25, $query26, $query27, $query28, $query29,
$query30, $query31, $query32, $query33, $query34, $query35, $query36, $query37, $query38, $query39,
$query40, $query41, $query42, $query43, $query44)

return
  for $query in $queries
    return $query
```
