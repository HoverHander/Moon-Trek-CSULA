<script setup>
// import GeoTIFF from "ol/source/GeoTIFF.js";
// import Map from "ol/Map.js";
// import TileLayer from "ol/layer/WebGLTile.js";
import Map from "ol/Map.js";
import OSM from "ol/source/OSM.js";
import TileLayer from "ol/layer/Tile.js";
import View from "ol/View.js";
import TileDebug from 'ol/source/TileDebug.js';
import WMTS, { optionsFromCapabilities } from "ol/source/WMTS.js";
import WMTSCapabilities from "ol/format/WMTSCapabilities.js";
import proj4 from 'proj4';
import {applyTransform} from 'ol/extent.js';
import {get as getProjection, getTransform} from 'ol/proj.js';
import {register} from 'ol/proj/proj4.js';


const osmSource = new OSM();


const parser = new WMTSCapabilities();
let map;
const code = "104903";
// const proj4def = 'GEOGCS["GCS_Moon_2000",DATUM["D_Moon_2000",SPHEROID["Moon_2000_IAU_IAG",1737400,0,AUTHORITY["ESRI","107903"]],AUTHORITY["ESRI","106903"]],PRIMEM["Reference_Meridian",0,AUTHORITY["ESRI","108900"]],UNIT["degree",0.0174532925199433,AUTHORITY["EPSG","9122"]],AUTHORITY["ESRI","104903"]]';
const proj4def = "+proj=longlat +R=1737400 +no_defs +type=crs"
const bbox = [90.0,-180.0,-90.0,180.0];

const newProjCode = 'EPSG:' + code;
proj4.defs(newProjCode, proj4def);
register(proj4);
const newProj = getProjection(newProjCode);
const fromLonLat = getTransform('EPSG:4326', newProj);

let worldExtent = [bbox[1], bbox[2], bbox[3], bbox[0]];
newProj.setWorldExtent(worldExtent);

// approximate calculation of projection extent,
// checking if the world extent crosses the dateline
if (bbox[1] > bbox[3]) {
  worldExtent = [bbox[1], bbox[2], bbox[3] + 360, bbox[0]];
}
console.log(worldExtent);
const extent = applyTransform(worldExtent, fromLonLat, undefined, 8);
newProj.setExtent(extent);

const debugLayer = new TileLayer({
  source: new TileDebug({
    tileGrid: osmSource.getTileGrid(),
    projection: osmSource.getProjection(),
  }),
  visible: true,
});

fetch(
  "https://trek.nasa.gov/tiles/Moon/EQ/LRO_WAC_Mosaic_Global_303ppd/1.0.0/WMTSCapabilities.xml"
)
  .then(function (response) {
    return response.text();
  })
  .then(function (text) {
    const result = parser.read(text);
    console.log(result)
    const options = optionsFromCapabilities(result, {
      layer: "LRO_WAC_Mosaic_Global_303ppd",
      matrixSet: "default028mm",
      style: 'default',
      projection: 'EPSG:104903',
    });


    map = new Map({
      layers: [
        new TileLayer({
          opacity: 1,
          source: new WMTS(options),
        }),
        debugLayer,
      ],
      target: "map",
      view: new View({
        center: [0, 0],
        zoom: 1,
      }),
    });
  
    const newView = new View({
      projection: 'EPSG:104903',
    });
    map.setView(newView);
    newView.fit(extent);
  });
  
</script>

<template>
    <head>
    <meta charset="UTF-8">
    <title>WMTS Layer from Capabilities</title>
    <link rel="stylesheet" href="node_modules/ol/ol.css">
    
  </head>
  <body>
    <div id="map" class="map"></div>

    <!-- <script type="module" src="main.js"></script> -->
  </body>
</template>

<style>
      .map {
        width: 100%;
        height: 400px;
      }
</style>