---
name: longdo-map
description: >
  Reference and guide for Longdo Map API v3 (map.longdo.com). Use when
  working with the Longdo Map JavaScript SDK — initializing maps, adding
  markers/shapes/popups, configuring layers, using the Search API, handling
  events, or integrating Longdo Map into web projects (React, Vue, vanilla JS).
  Triggers on: longdo.Map, longdo.Marker, Longdo Map, แผนที่ longdo,
  map.longdo.com, api.longdo.com, longdo.Layers, longdo.Overlays.
when_to_use: >
  Use when the user is writing code that imports or uses the Longdo Map SDK,
  asks how to do something with a map (add marker, draw route, search location,
  change layer theme), or asks to integrate a map into a web framework.
  Also use when debugging Longdo Map issues or when asked about the
  differences between Longdo Map and other map providers.
allowed-tools: Read WebFetch WebSearch
---

# Longdo Map API v3 — Developer Reference

Official docs: https://map.longdo.com/docs3/ | https://api.longdo.com/map3/doc.html  
Get API key: https://map.longdo.com/

## Setup

### Script tag (CDN)
```html
<script src="https://api.longdo.com/map3/?key=YOUR_API_KEY"></script>
<div id="map" style="width:100%;height:500px;"></div>
```

### Initialize map
```javascript
const map = new longdo.Map({
  placeholder: document.getElementById('map'),
  location: { lon: 100.5018, lat: 13.7563 },   // Bangkok
  zoom: 13,
  layer: longdo.Layers.NORMAL,
  language: 'th'   // 'th' | 'en'
});
```

### Wait for ready
```javascript
map.Event.bind(longdo.EventName.Ready, () => {
  // map is ready
});
```

---

## Core Objects

### Marker
```javascript
const marker = new longdo.Marker(
  { lon: 100.5018, lat: 13.7563 },
  {
    title: 'My Location',
    detail: 'Description text',
    icon: { url: 'icon.png', offset: { x: 12, y: 32 } },
    clickable: true,
    draggable: false,
    weight: longdo.OverlayWeight.Top
  }
);
map.Overlays.add(marker);
```

### Popup (info window)
```javascript
const popup = new longdo.Popup(
  { lon: 100.5018, lat: 13.7563 },
  {
    title: 'Title',
    detail: 'Detail text',
    html: '<b>Custom HTML</b>',
    size: { width: 200, height: 100 },
    closable: true
  }
);
map.Overlays.add(popup);
marker.pop(true);   // show popup attached to marker
```

### Polyline (route/path)
```javascript
const route = new longdo.Polyline(
  [
    { lon: 100.5, lat: 13.7 },
    { lon: 100.6, lat: 13.8 }
  ],
  {
    lineColor: '#FF0000',
    lineWidth: 3,
    clickable: true
  }
);
map.Overlays.add(route);
```

### Polygon (area)
```javascript
const area = new longdo.Polygon(
  [/* location list */],
  {
    lineColor: '#0000FF',
    lineWidth: 2,
    fillColor: 'rgba(0,0,255,0.2)',
    clickable: true
  }
);
map.Overlays.add(area);
```

### Circle
```javascript
const circle = new longdo.Circle(
  { lon: 100.5018, lat: 13.7563 },
  500,   // radius in meters
  { lineColor: '#00FF00', fillColor: 'rgba(0,255,0,0.2)' }
);
map.Overlays.add(circle);
```

---

## Overlays API

```javascript
map.Overlays.add(overlay)        // Add object to map
map.Overlays.remove(overlay)     // Remove specific object
map.Overlays.clear()             // Remove all overlays
map.Overlays.list()              // Array of all overlays
map.Overlays.drop(marker)        // Drop animation
map.Overlays.bounce(marker)      // Bounce animation
```

---

## Map Controls

```javascript
map.zoom()                           // Get current zoom level
map.zoom(15)                         // Set zoom level
map.location()                       // Get current center {lon, lat}
map.location({ lon: 100.5, lat: 13.7 })  // Pan to location
map.resize()                         // Call after container resize

map.Layers.setBase(longdo.Layers.DARK)          // Change base layer
map.Layers.add(longdo.Layers.TRAFFIC)           // Add overlay layer
map.Layers.remove(longdo.Layers.TRAFFIC)        // Remove overlay layer
```

---

## Layer Types

```javascript
// Base layers
longdo.Layers.NORMAL          // Standard
longdo.Layers.EASY            // Simplified
longdo.Layers.DARK            // Dark theme
longdo.Layers.LIGHT           // Light monochrome
longdo.Layers.GRAY            // Colorful light
longdo.Layers.OSM             // OpenStreetMap
longdo.Layers.TOPO            // Topographic

// Overlay layers
longdo.Layers.TRAFFIC         // Traffic conditions
longdo.Layers.SATELLITE       // Satellite imagery
```

---

## Search API

```javascript
// Autocomplete suggestions
map.Search.suggest('สยาม', {
  area: 5,         // search radius km
  limit: 10
});

// Full text search
map.Search.search('ร้านอาหาร สาทร', {
  area: 2,
  tag: 'restaurant',
  offset: 0,
  limit: 20,
  dataset: 'place'
});

// Reverse geocoding (location → address)
map.Search.address({ lon: 100.5018, lat: 13.7563 }, {
  limit: 1
});

// Nearby POIs
map.Search.nearPoi(
  { lon: 100.5018, lat: 13.7563 },
  {
    tag: 'hospital',
    area: 3,
    limit: 10
  }
);
```

### Handle search result
```javascript
map.Event.bind(longdo.EventName.Search, (result) => {
  if (result.error) return;
  result.data.forEach(place => {
    const marker = new longdo.Marker({ lon: place.lon, lat: place.lat }, {
      title: place.name
    });
    map.Overlays.add(marker);
  });
});
```

---

## Events

```javascript
map.Event.bind(longdo.EventName.Ready, handler)
map.Event.bind(longdo.EventName.Click, handler)       // {lon, lat}
map.Event.bind(longdo.EventName.Zoom, handler)
map.Event.bind(longdo.EventName.Location, handler)    // map panned
map.Event.bind(longdo.EventName.OverlayClick, handler)
map.Event.bind(longdo.EventName.Search, handler)      // search result

map.Event.unbind(longdo.EventName.Click, handler)
```

---

## React Integration

```jsx
import { useEffect, useRef } from 'react';

export default function LongdoMap({ apiKey }) {
  const mapRef = useRef(null);

  useEffect(() => {
    const script = document.createElement('script');
    script.src = `https://api.longdo.com/map3/?key=${apiKey}`;
    script.onload = () => {
      const map = new window.longdo.Map({
        placeholder: mapRef.current,
        location: { lon: 100.5018, lat: 13.7563 },
        zoom: 13
      });
    };
    document.head.appendChild(script);
    return () => document.head.removeChild(script);
  }, [apiKey]);

  return <div ref={mapRef} style={{ width: '100%', height: '500px' }} />;
}
```

---

## Vue Integration

```vue
<template>
  <div ref="mapEl" style="width:100%;height:500px;" />
</template>

<script setup>
import { onMounted, ref } from 'vue';

const mapEl = ref(null);
const props = defineProps({ apiKey: String });

onMounted(() => {
  const script = document.createElement('script');
  script.src = `https://api.longdo.com/map3/?key=${props.apiKey}`;
  script.onload = () => {
    new window.longdo.Map({
      placeholder: mapEl.value,
      location: { lon: 100.5018, lat: 13.7563 },
      zoom: 13
    });
  };
  document.head.appendChild(script);
});
</script>
```

---

## Common Patterns

### Fit map to overlay bounds
```javascript
map.bound();   // auto zoom/pan to fit all overlays
```

### Get clicked location
```javascript
map.Event.bind(longdo.EventName.Click, (e) => {
  const location = map.location();
  console.log(location.lon, location.lat);
});
```

### Custom marker icon
```javascript
new longdo.Marker(location, {
  icon: {
    url: 'https://example.com/pin.png',
    offset: { x: 16, y: 32 }   // hotspot from top-left
  }
});
```

### Multiple markers from array
```javascript
const places = [
  { lon: 100.5, lat: 13.7, name: 'Place A' },
  { lon: 100.6, lat: 13.8, name: 'Place B' }
];

places.forEach(p => {
  map.Overlays.add(new longdo.Marker(
    { lon: p.lon, lat: p.lat },
    { title: p.name }
  ));
});
```

### Clear and re-draw
```javascript
map.Overlays.clear();
// re-add overlays
```

---

## MapLibre Access via map.Renderer

Longdo Map uses MapLibre GL JS as its rendering engine. Access the underlying MapLibre instance via `map.Renderer` to use MapLibre's full API directly.

```javascript
const mlMap = map.Renderer;   // MapLibre Map instance

// Add GeoJSON source and layer
mlMap.addSource('my-source', {
  type: 'geojson',
  data: { type: 'FeatureCollection', features: [] }
});

mlMap.addLayer({
  id: 'my-layer',
  type: 'fill',
  source: 'my-source',
  paint: { 'fill-color': '#088', 'fill-opacity': 0.6 }
});

// Layer events
mlMap.on('click', 'my-layer', (e) => {
  console.log(e.features[0]);
});

// Camera controls
mlMap.flyTo({ center: [100.5, 13.7], zoom: 15 });
mlMap.easeTo({ bearing: 45, pitch: 60 });

// Data-driven paint expressions
mlMap.setPaintProperty('my-layer', 'fill-color', [
  'interpolate', ['linear'], ['get', 'value'],
  0, '#00f', 100, '#f00'
]);
```

### When to use map.Renderer
- Custom vector tile layers (MVT)
- Complex GeoJSON visualizations (heatmap, choropleth, extrusion)
- 3D buildings and terrain
- Data-driven styling with MapLibre expressions
- Animations via `requestAnimationFrame`
- MapLibre plugins (e.g. `maplibre-gl-draw`)

### Notes
- Always wait for `longdo.EventName.Ready` before accessing `map.Renderer`
- Longdo `map.Overlays` and MapLibre layers via `map.Renderer` are independent systems — they coexist but have separate z-order stacks
- Avoid source/layer IDs that conflict with Longdo Map's internal layers

```javascript
map.Event.bind(longdo.EventName.Ready, () => {
  const mlMap = map.Renderer;
  // safe to use MapLibre API here
});
```

---

## Troubleshooting

| Problem | Cause | Fix |
|---------|-------|-----|
| Map not rendering | Container has no height | Add `height` CSS to container div |
| `longdo is not defined` | Script loaded before SDK | Ensure SDK script loads first |
| Map blank after resize | Container size changed | Call `map.resize()` |
| Markers not visible | Wrong lon/lat order | Longdo uses `{lon, lat}` not `{lat, lng}` |
| API key error | Invalid/missing key | Check key at map.longdo.com/profile |

---

## Supporting Files
- [api-reference.md](api-reference.md) — Full API reference: all constructor options, event names, layer constants
- [services.md](services.md) — REST API services: search, geocoding, routing, traffic, TSP, span area
