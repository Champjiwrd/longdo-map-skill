# Longdo Map API v3 — Full Reference

Official docs: https://api.longdo.com/map3/doc.html

---

## Map Constructor Options

```typescript
new longdo.Map({
  placeholder: HTMLElement,   // Required: container element
  location: LonLat,           // Initial center { lon, lat }
  zoom: number,               // Initial zoom (1–20, default 8)
  zoomRange: { min, max },    // Restrict zoom range
  layer: LayerObject,         // Base layer (default: Layers.NORMAL)
  language: 'th' | 'en',     // Map label language
  ui: UIOptions,              // UI component visibility
  lastView: boolean,          // Restore last viewport (default false)
})
```

### UIOptions
```javascript
{
  fullscreen: boolean,    // Fullscreen button
  geolocation: boolean,  // GPS locate button
  zoom: boolean,         // Zoom controls
  layerSelector: boolean, // Layer switcher
  crosshair: boolean,    // Center crosshair
  scale: boolean         // Scale bar
}
```

---

## Marker Options (MarkerOptions)

```typescript
{
  title: string,              // Label / popup title
  detail: string,             // Popup body text
  html: string,               // Custom popup HTML
  icon: {
    url: string,              // Icon image URL
    offset: { x, y },        // Hotspot offset from top-left (px)
    size: { width, height }  // Icon size (px)
  },
  clickable: boolean,         // Default: true
  draggable: boolean,         // Default: false
  visible: boolean,           // Default: true
  weight: longdo.OverlayWeight.Top | .Bottom,
  rotate: number,             // Rotation degrees
  popup: PopupOptions         // Attached popup options
}
```

---

## Popup Options (PopupOptions)

```typescript
{
  title: string,
  detail: string,
  html: string,               // Overrides title+detail
  size: { width, height },   // px
  offset: { x, y },          // px from anchor
  closable: boolean,          // Show close button
  loadDetail: boolean         // Auto-load place detail from API
}
```

---

## Geometry Options (LineOptions / AreaOptions)

```typescript
// Shared by Polyline, Polygon, Circle, Dot
{
  lineColor: string,          // CSS color, e.g. '#FF0000'
  lineWidth: number,          // px
  lineOpacity: number,        // 0.0–1.0
  fillColor: string,          // CSS color (polygons/circle)
  fillOpacity: number,        // 0.0–1.0
  clickable: boolean,
  draggable: boolean,
  editable: boolean,          // Show edit handles
  label: string,              // Text overlay on shape
  title: string               // Popup title on click
}
```

---

## Map Methods

```javascript
// Viewport
map.location()                     // → {lon, lat} current center
map.location(lonlat)               // Pan to location
map.zoom()                         // → number current zoom
map.zoom(level)                    // Set zoom
map.bound()                        // Fit all overlays in view
map.bound(lonlatBounds)            // Fit to { minLon, minLat, maxLon, maxLat }
map.resize()                       // Update after container resize
map.rotate(degrees)                // Rotate map
map.pitch(degrees)                 // Tilt map (3D)

// Conversion
map.locationToPixel(lonlat)        // → {x, y} screen coords
map.pixelToLocation(pixel)         // → {lon, lat}
```

---

## Search Options

### suggest(keyword, options)
```javascript
{
  area: number,        // radius km from map center (default: 0 = nationwide)
  offset: number,      // pagination start (default: 0)
  limit: number,       // results count (default: 10)
  dataset: string      // e.g. 'place', 'road'
}
```

### search(keyword, options)
```javascript
{
  area: number,
  tag: string,         // POI category tag
  span: string,        // bounding box '100.4,13.6,100.6,13.9'
  offset: number,
  limit: number,
  dataset: string,
  smart: boolean       // fuzzy matching
}
```

### address(location, options)
```javascript
{
  limit: number,       // default: 1
  dataset: string
}
```

### nearPoi(location, options)
```javascript
{
  area: number,        // radius km
  tag: string,         // category
  span: string,
  zoom: number,
  offset: number,
  limit: number,
  dataset: string
}
```

---

## Event Names

```javascript
longdo.EventName.Ready           // Map initialized
longdo.EventName.Click           // Map click
longdo.EventName.RightClick      // Right-click
longdo.EventName.DoubleClick     // Double-click
longdo.EventName.Zoom            // Zoom changed
longdo.EventName.ZoomStart       // Zoom started
longdo.EventName.Location        // Map panned/moved
longdo.EventName.LocationStart   // Pan started
longdo.EventName.OverlayClick    // Overlay (marker/shape) clicked
longdo.EventName.OverlayRightClick
longdo.EventName.OverlayDrop     // Dragged overlay dropped
longdo.EventName.Search          // Search result returned
longdo.EventName.Suggest         // Suggest result returned
```

---

## Layer Constants

```javascript
// Base layers (setBase)
longdo.Layers.NORMAL          // Standard road map
longdo.Layers.EASY            // Simplified design
longdo.Layers.DARK            // Dark theme
longdo.Layers.LIGHT           // Light monochrome
longdo.Layers.GRAY            // Colorful light
longdo.Layers.PASTEL          // Pastel colors
longdo.Layers.OSM             // OpenStreetMap
longdo.Layers.TOPO            // Topographic
longdo.Layers.POI             // POI only

// Overlay layers (add/remove)
longdo.Layers.TRAFFIC         // Real-time traffic
longdo.Layers.SATELLITE       // Aerial/satellite
longdo.Layers.HYBRID          // Satellite + labels

// External (requires additional config)
longdo.Layers.GOOGLE_ROADMAP
longdo.Layers.GOOGLE_SATELLITE
longdo.Layers.ARCGIS_STREET
longdo.Layers.ARCGIS_TOPO
```

---

## Overlay Weight

```javascript
longdo.OverlayWeight.Top      // Always on top
longdo.OverlayWeight.Bottom   // Always at bottom
// (default: ordered by add sequence)
```

---

## GeoJSON Support

Longdo Map accepts standard GeoJSON for locations:
```javascript
// Point
{ type: 'Point', coordinates: [100.5018, 13.7563] }   // [lon, lat]

// Use with Marker
new longdo.Marker({ type: 'Point', coordinates: [100.5, 13.7] })
```

---

## Notes

- Coordinates: always `{lon, lat}` — NOT `{lat, lng}` (unlike Google Maps)
- Zoom level 1 = world, 20 = building level
- Search API requires the map to be ready before calling
- Multiple layers can be active simultaneously (base + overlays)
- API key usage tracked per registered domain at map.longdo.com
