<template>
  <div id="wfmap" class="wfmap">
    <div class="header">
      <h1>Wildfire Tracker</h1>
      <p>By Andrew Mitchell | Data from <a href="https://eonet.gsfc.nasa.gov/" target="_blank">NASA EONET</a></p>
      <p>{{numWildfires}} wildfires | Last update {{lastUpdate.toLocaleString()}}</p>
    </div>
  </div>
</template>

<script setup>
import 'ol/ol.css';
import {Map, View} from 'ol';
import {Tile as TileLayer, Vector as VectorLayer} from 'ol/layer';
import {OSM, XYZ, Vector} from 'ol/source';
import {fromLonLat} from 'ol/proj';
import Feature from 'ol/Feature';
import {Point} from 'ol/geom';
import {Style, Icon} from 'ol/style';

let vectorSource;
const numWildfires = ref(0);
const lastUpdate = ref(new Date());
let firstRun = true;
let wildfireIds = [];

function updateWildfires() {
  fetch('https://eonet.gsfc.nasa.gov/api/v3/categories/wildfires?status=open&days=120')
    .then(response => response.json())
    .then(data => {
      lastUpdate.value = new Date();
      numWildfires.value = data.events.length;
      for (let i = 0; i < data.events.length; i++) {
        let wildfire = data.events[i];
        for (let j = 0; j < data.events[i].geometry.length; j++) {
          addWildfire(wildfire.geometry[j].coordinates[0], wildfire.geometry[j].coordinates[1], wildfire.sources[0].link, wildfire.id, !firstRun);
        }
      }
      firstRun = false;
    });
}

function addWildfire(longitude, latitude, url, id, isNew = false) {
  if (wildfireIds.includes(id)) {
    return;
  }
  console.log(`${isNew? 'New wildfire' : 'Wildfire'} at (longitude: ${longitude}, latitude: ${latitude})!`);
  var marker = new Feature({
      geometry: new Point(
        fromLonLat([longitude, latitude])
      ),
  });
  marker.set("url", url);
  if (isNew) {
    marker.setStyle(new Style({
      image: new Icon(({
          crossOrigin: 'anonymous',
          src: 'img/wildfire.png',
          scale: 0.1,
          color: 'orangered'
      }))
    }));
  } else {
    marker.setStyle(new Style({
      image: new Icon(({
          crossOrigin: 'anonymous',
          src: 'img/wildfire.png',
          scale: 0.1
      }))
    }));
  }
  vectorSource.addFeature(marker);
  wildfireIds.push(id);
}

onMounted(() => {
  try {
    let map = new Map({
      target: 'wfmap',
      layers: [
        new TileLayer({
          source: new OSM()
        })
      ],
      view: new View({
        center: fromLonLat([-116.15, 42.05]),//[0, 0],
        zoom: 6,
      })
    });
    vectorSource = new Vector({
      features: []
    });
    var markerVectorLayer = new VectorLayer({
      source: vectorSource,
    });
    map.addLayer(markerVectorLayer);
    updateWildfires();
    const wildfireUpdateTimer = setInterval(() => {
      console.log(`Refreshing data... ${new Date().toLocaleString()}`);
      updateWildfires();
    }, 60000);
  } catch (err) {
    console.error(err);
  }
})

defineExpose({
  numWildfires
})
</script>

<style>
#wfmap {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 100%;
  z-index: -1;
}

.header {
  background-color: gray;
  width: fit-content;
  border-radius: 15px;
  padding: 5px 10px;
  opacity: 0.9;
  color: white;
  position: absolute;
  top: 10px;
  left: 45px;
  z-index: 1;
}

.header h1, .header p, .header a {
  padding: 0;
  margin: 0;
}

.header h1 {
  font-size: 1.5rem;
}

.header a, .header a:visited {
  color: royalblue;
}
</style>