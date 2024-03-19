<!-- <script setup>
    <script src="https://js.arcgis.com/4.29/"></script> -->

<script setup>
  // const src="https://js.arcgis.com/4.29/"
  //import { loadModules } from "esri-loader"
  import Map from "@arcgis/core/Map.js"
  import Basemap from "@arcgis/core/Basemap.js"
  import MapView from "@arcgis/core/views/MapView.js"
  import WMTSLayer from "@arcgis/core/layers/WMTSLayer.js"
  import SceneView from "@arcgis/core/views/SceneView.js"
  import Zoomist from "zoomist"

    new Zoomist('.zoomist-container', {
    // Optional parameters
    maxScale: 4,
    bounds: true,
    // if you need slider
    slider: true,
    // if you need zoomer
    zoomer: true
    });

    const moonTrekBasemap = new Basemap({
      baseLayers: [
        new WMTSLayer({
          url: "https://trek.nasa.gov/tiles/Moon/EQ/LRO_WAC_Mosaic_Global_303ppd_v02/1.0.0/WMTSCapabilities.xml",
        }),
      ],
    });

    const map = new Map({
      basemap: moonTrekBasemap,
    });

    const view = new MapView({
      container: "viewDiv",
      map: map,
      center: [0, 0],
    });

    const view = new SceneView({
  // An instance of Map or WebScene
      map: new Map({
      basemap: "hybrid"
    }),
    container: "viewDiv",
  });

    window.addEventListener("DOMContentLoaded", () => {
    initMapView();
    });

  // });
</script>

<template>
    <head>
    <meta charset="utf-8" />
    <meta
      name="viewport"
      content="initial-scale=1,maximum-scale=1,user-scalable=no"
    />
    <title>WMTSLayer | Sample | ArcGIS Maps SDK for JavaScript 4.29</title>
    <link
      rel="stylesheet"
      href="https://js.arcgis.com/4.29/esri/themes/light/main.css"
    />
  </head>
  <body>
    <div id="viewDiv"></div>
    <div id="app">
      <div class="zoomist-container cat-zoomist">
        <div class="zoomist-wrapper">
          <div class="zoomist-image">
            <img
              src="data.images.user.src"
              alt="moon"
            />
          </div>
        </div>
      </div>
    </div>
  </body>
</template>

<style scoped>
      html,
      body,
      #viewDiv {
        padding: 0;
        margin: 0;
        height: 100%;
        width: 100%;
      }

      .esri-layer-list {
        width: 310px;
      }
</style>