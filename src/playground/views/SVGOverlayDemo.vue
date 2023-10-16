<template>
  <l-map
    ref="map"
    v-model:zoom="zoom"
    :crs="crs"
    :center="[height / 2, width / 2]"
    :minZoom="-5"
  >
    <l-s-v-g-overlay
      :svgImage="imageOverlayUrl"
      :bounds="bounds"
    ></l-s-v-g-overlay>

    <l-marker
      v-for="(marker, idx) in markers"
      :key="idx"
      :lat-lng="marker.coordinates"
      ><l-popup>{{ idx }}</l-popup></l-marker
    >
  </l-map>

  <!-- Map Settings -->
  <label for="imageOverlayUrl">Url to render: </label>
  <input
    type="text"
    id="imageOverlayUrl"
    placeholder="Url for image overlay"
    v-model="imageOverlayUrl"
  />
  <!-- Bounds settings -->
  <label for="width">Width: </label>
  <input type="number" id="width" placeholder="Width" v-model="width" />
  <label for="height">Height: </label>
  <input type="number" id="height" placeholder="Height" v-model="height" />

  <!-- Marker settings -->
  <div class="markers-list">
    <h4>Markers</h4>
    <ul>
      <li v-for="(marker, idx) in markers" :key="idx">
        {{ idx }} - lng (X): {{ marker.coordinates.lng }} - lat (Y):
        {{ marker.coordinates.lat }}
      </li>
    </ul>
  </div>
</template>
<script lang="ts">
import type L from "leaflet";
import { CRS } from "leaflet/dist/leaflet-src.esm";
import { computed, ref } from "vue";

import { LMap, LMarker, LPopup, LSVGOverlay } from "@src/components";

export default {
  components: {
    LMap,
    LSVGOverlay,
    LMarker,
    LPopup,
  },
  setup() {
    const svg = `<svg width="100" height="100" viewbox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
      <rect width="100" height="100" fill="rgb(0,0,255)" stroke-width="1" stroke="rgb(0,0,0)"/>
      <circle cx="50" cy="50" r="40" fill="rgb(255,0,0)" stroke-width="1" stroke="rgb(0,0,0)"/>
      <circle cx="50" cy="50" r="4" fill="rgb(0,0,0)" stroke-width="1" stroke="rgb(0,0,0)"/>
    </svg>`;

    const elem = document.createElement("div");
    elem.innerHTML = svg;

    const svgElem = elem.firstChild as SVGElement;
    const imageOverlayUrl = ref(svgElem);
    const width = ref(100);
    const height = ref(100);
    const zoom = ref(1);

    const markers = ref([
      { coordinates: { lng: 0, lat: 0 } },
      { coordinates: { lng: 100, lat: 0 } },
      { coordinates: { lng: 0, lat: 100 } },
      { coordinates: { lng: 100, lat: 100 } },
      { coordinates: { lng: 0, lat: 50 } },
      { coordinates: { lng: 50, lat: 0 } },
      { coordinates: { lng: 50, lat: 100 } },
      { coordinates: { lng: 100, lat: 50 } },
    ]);

    const bounds = computed(
      () =>
        [
          [0, 0],
          [height.value, width.value],
        ] as L.LatLngBoundsLiteral
    );
    const crs = CRS.Simple;

    return {
      imageOverlayUrl,
      width,
      height,
      zoom,
      markers,
      bounds,
      crs,
    };
  },
};
</script>

<style></style>
