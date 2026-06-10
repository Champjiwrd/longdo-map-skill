# Longdo Map REST API — Services Reference

Base API key: pass as `?key=YOUR_API_KEY` on every request  
All endpoints accept GET or POST (unless noted). POST uses `application/x-www-form-urlencoded`.

---

## Search Services

### Suggest (Autocomplete)
`GET https://search.longdo.com/mapsearch/json/suggest`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `keyword` | string | Yes | Search keyword |
| `area` | CSV | No | Geocode of suggestion area |
| `offset` | integer | No | Pagination start (default: 0) |
| `limit` | integer | No | Results count (default: 10) |
| `dataset` | CSV | No | Dataset filter |
| `key` | string | No | API key |
| `callback` | string | No | JSONP callback |

```json
// Response
{
  "meta": { "keyword": "string" },
  "data": [{ "w": "string", "d": "string", "s": "string" }]
}
```

---

### Search (Full Text Search)
`GET https://search.longdo.com/mapsearch/json/search`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `keyword` | string | Yes | Search term |
| `lon` | number | No | Center longitude |
| `lat` | number | No | Center latitude |
| `span` | string | No | Radius: `"1km"`, `"1000m"`, `"1deg"` |
| `area` | CSV | No | Geocode filter |
| `tag` | CSV | No | Category tag filter |
| `offset` | integer | No | Default: 0 |
| `limit` | integer | No | Default: 20 |
| `dataset` | CSV | No | Data source |
| `locale` | LocaleCode | No | `th` / `en` |
| `key` | string | No | API key |

```json
// Response
{
  "meta": { "hasmore": true, "start": 0, "end": 20, "keyword": "string", "lon": 0, "lat": 0 },
  "data": [{
    "type": "string", "id": "string", "name": "string",
    "lat": 13.7, "lon": 100.5, "icon": "string",
    "tag": ["string"], "url": "string", "address": "string",
    "tel": "string", "contributor": "string",
    "verified": true, "obsoleted": false, "distance": "string"
  }]
}
```

---

### Smart Search (Extended)
`GET https://search.longdo.com/smartsearch/json/search`

Same parameters as Search plus:

| Parameter | Type | Description |
|-----------|------|-------------|
| `forcesmartsearch` | 0/1 | Force SmartSearch mode |
| `forcelimit` | integer | Minimum result threshold |
| `extendedsearch` | 0/1/2/3 | Extended search flag |
| `extendedlimit` | integer | Extended result threshold |
| `extendedkey` | string | Extended API key |
| `extendedtype` | string | `findplacefromtext` or `textsearch` |
| `exceedtime` | number | Time limit (seconds) |
| `cache` | 0/1 | Enable caching |
| `extractaddress` | bit | Extract address from keyword |

Response: same as Search, each result has additional `source` field.

---

### Nearby POI Search
`GET https://api.longdo.com/POIService/json/search`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `lon` | number | Yes | Center longitude |
| `lat` | number | Yes | Center latitude |
| `span` | string | No | Radius: `"1km"`, `"500m"` |
| `tag` | CSV | No | Category tag filter |
| `zoom` | integer | No | POI importance level |
| `area` | CSV | No | Geocode filter |
| `offset` | integer | No | Default: 0 |
| `limit` | integer | No | Default: 20 |
| `dataset` | CSV | No | Data source |
| `locale` | LocaleCode | No | `th` / `en` |
| `key` | string | No | API key |

Response: same format as Search.

---

## Address / Geocoding Services

### Reverse Geocoding (Coordinates → Address)
`GET https://api.longdo.com/map/services/address`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `lon` | number | Yes | Longitude |
| `lat` | number | Yes | Latitude |
| `locale` | LocaleCode | No | `th` / `en` |
| `noaddress` | any | No | Exclude address fields |
| `noelevation` | any | No | Exclude elevation |
| `noroad` | any | No | Exclude road info |
| `key` | string | No | API key |

```json
// Response
{
  "geocode": "string",
  "country": "string", "province": "string",
  "district": "string", "subdistrict": "string",
  "moo": "string", "moo_name": "string",
  "house_num": "string", "postcode": "string",
  "aoi": "string", "water": "string",
  "elevation": 10.5,
  "road": "string", "road_lon": 100.5, "road_lat": 13.7,
  "error": "string"
}
```

---

### Batch Reverse Geocoding (Multiple Coordinates)
`POST https://api.longdo.com/map/services/addresses`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `lon[]` | number[] | Yes | Array of longitudes (max 100) |
| `lat[]` | number[] | Yes | Array of latitudes (max 100) |
| `wkt` | string | No | LineString in WKT as alternative |
| `locale` | LocaleCode | No | `th` / `en` |
| `noaddress` | any | No | Exclude address fields |
| `noelevation` | any | No | Exclude elevation |
| `noroad` | any | No | Exclude road info |
| `key` | string | No | API key |

Response: array of reverse geocoding result objects.

---

### Geocoding (Address → Coordinates)
`GET https://search.longdo.com/addresslookup/api/addr/geocoding`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `text` | string | Yes | Address text to geocode |
| `dataset` | string | No | Additional dataset |

```json
// Response
{
  "data": [{
    "confidence": 0.95,
    "location": {
      "point": {},
      "houseno": "string", "road": {}, "village": {},
      "moo": "string", "subdistrict": {}, "district": {},
      "province": {}, "zipcode": "string"
    },
    "point": [{ "lon": 100.5, "lat": 13.7 }]
  }],
  "meta": "string"
}
```

---

### Geocode / Postcode Lookup
`GET https://api.longdo.com/POIService/json/address`

| Parameter | Type | Description |
|-----------|------|-------------|
| `geocode` | string | Geocode to lookup |
| `postcode` | string | Postcode to lookup |
| `locale` | LocaleCode | `th` / `en` |
| `key` | string | API key |

```json
// Response (by geocode)
{ "geocode": "string", "province": "string", "district": "string", "subdistrict": "string", "lat": 13.7, "lon": 100.5 }

// Response (by postcode)
{ "address": [/* array of geocode result objects */] }
```

---

### Extract Address (Parse Address Text)
`POST https://search.longdo.com/smartsearch/json/extract_address/v2`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `text` | string | Yes | Address text to parse |
| `correction` | bit | No | Enable text correction (default: 1) |
| `censor` | bit | No | Remove contact info (default: 0) |

```json
// Response — extracted address components
{
  "meta": { "input": "string", "remark": "string", "locale": "th" },
  "data": [{
    "house_no": "string", "floor": "string", "room": "string", "moo": "string",
    "place": { "name": "string", "distance": 0, "prefix": true },
    "road": {}, "subdistrict": {}, "district": {}, "province": {},
    "postcode": "string", "country": "string", "confidence": 0.9
  }]
}
```

---

## Routing Services

### Calculate Route
`GET https://api.longdo.com/RouteService/json/route/guide`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `flon` | number | Yes | Start longitude |
| `flat` | number | Yes | Start latitude |
| `tlon` | number | Yes | Destination longitude |
| `tlat` | number | Yes | Destination latitude |
| `mode` | RouteMode | No | `t`/`c`/`d`/`e` (default: `t`) |
| `type` | RouteType | No | `1`=road, `8`=ferry, `16`=tollway (combinable) |
| `locale` | LocaleCode | No | `th` / `en` |
| `restrict` | RouteRestrict | No | `0`=none, `1`=no motorcycle routes |
| `time` | Unix timestamp | No | Departure time |
| `maxresult` | integer | No | Number of routes (1–8) |
| `key` | string | No | API key |

```json
// Response
{
  "meta": { "from": {}, "to": {}, "config": "string" },
  "data": [{
    "fdistance": 0, "tdistance": 1200, "distance": 1200, "interval": 300, "id": 1,
    "guide": [{ "turn": 5, "name": "ถนนสุขุมวิท", "distance": 500, "interval": 120 }]
  }]
}
```

**TurnCode values:**
| Code | Meaning |
|------|---------|
| 0 | Turn left |
| 1 | Turn right |
| 2 | Slight left |
| 3 | Slight right |
| 4 | Unknown |
| 5 | Straight |
| 6 | Tollway |
| 9 | Arrive at destination |
| 11 | Ferry |

---

### Get Route Path (Geometry)
`GET https://api.longdo.com/RouteService/json/route/path`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | integer | Yes | Path ID from Calculate Route response |

```json
// Response — array of location arrays per route segment
{ "meta": { "id": 1 }, "data": [{ "data": [{ "lon": 100.5, "lat": 13.7 }] }] }
```

---

### Calculate Route (GeoJSON)
`GET https://api.longdo.com/RouteService/geojson/route`

Same parameters as Calculate Route. Returns GeoJSON FeatureCollection.

```javascript
// Example usage
fetch(`https://api.longdo.com/RouteService/geojson/route?flon=100.5&flat=13.7&tlon=100.6&tlat=13.8&key=${API_KEY}`)
  .then(r => r.json())
  .then(geojson => {
    map.Renderer.addSource('route', { type: 'geojson', data: geojson });
    map.Renderer.addLayer({ id: 'route', type: 'line', source: 'route',
      paint: { 'line-color': '#0080ff', 'line-width': 4 }
    });
  });
```

---

### Route Matrix (Multiple Origins/Destinations)
`POST https://api.longdo.com/RouteService/json/route/matrix`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `flon[]` | number[] | Yes | Start longitudes (max 25) |
| `flat[]` | number[] | Yes | Start latitudes (max 25) |
| `tlon[]` | number[] | Yes | Destination longitudes (max 25) |
| `tlat[]` | number[] | Yes | Destination latitudes (max 25) |
| `mode` | RouteMode | No | Default: `t` |
| `type` | RouteType | No | Route type |
| `time` | Unix timestamp | No | Departure time |
| `key` | string | No | API key |

```json
// Response — 2D matrix [from][to]
{ "meta": { "fromCount": 2, "toCount": 2 }, "data": [[{ "interval": 300, "distance": 1200 }]] }
```

---

### Snap to Road
`POST https://api.longdo.com/RouteService/json/route/snap`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `wkt` | string | Yes | LineString in WKT format |

Response: array of snapped Location objects.

```javascript
// Example WKT
const wkt = 'LINESTRING(100.5018 13.7563, 100.5100 13.7600)';
```

---

### Traffic Speed (Real-time)
`GET https://api.longdo.com/RouteService/json/traffic/speed`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `lon` | number | Yes | Longitude |
| `lat` | number | Yes | Latitude |
| `range` | number | No | Search radius in degrees (max: 0.001) |
| `locale` | LocaleCode | No | `th` / `en` |
| `time` | Unix timestamp | No | Predicted data time |
| `key` | string | No | API key |

```json
// Response
{ "road": "string", "dir": "forward", "speed": 13.8, "source": "real-time", "lon": 100.5, "lat": 13.7 }
```
Speed unit: **m/s** (multiply by 3.6 for km/h)

---

### Get Span Area (Reachable Area)
`GET https://api.longdo.com/RouteService/json/span/get`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `flon` | number | Yes | Start longitude |
| `flat` | number | Yes | Start latitude |
| `span` | string | Yes | Distance or time: `"1000m"`, `"1km"`, `"1800s"` (max: 30000m / 1800s) |
| `flag` | SpanFlag | No | `1`=roads, `2`=boundary (combinable) |
| `key` | string | No | API key |

```json
// Response
{ "from": { "lon": 100.5, "lat": 13.7 }, "lines": [[]], "polygon": [[]] }
```

---

### TSP — Route Planner (Traveling Salesman)
`POST https://api.longdo.com/route-tsp-v2`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `lon[]` | number[] | Yes | Waypoint longitudes |
| `lat[]` | number[] | Yes | Waypoint latitudes |
| `cost` | boolean | No | Include step details (default: false) |
| `otsp` | boolean | No | Open loop — don't return to start (default: false) |
| `mode` | RouteMode | No | Default: `c` |
| `key` | string | Yes | API key |

```json
// Response
{
  "status": "ok", "otsp": false, "mode": "c",
  "body": {
    "sequence": [0, 2, 1, 3],
    "location": [{ "lon": 100.5, "lat": 13.7 }],
    "total_distance": 5400, "total_time": 900,
    "time_step": [{ "start": 0, "end": 1, "cost": 300 }],
    "distance_step": [{ "start": 0, "end": 1, "cost": 1800 }]
  }
}
```

---

### Close Road Management
**Get closed roads:**
`GET https://api.longdo.com/RouteService/json/close/get`

```json
// Response
[{ "polygon": "POLYGON((...))", "time": [1700000000, 1700003600] }]
```

**Set closed roads** (auth required):
`POST https://api.longdo.com/RouteService/json/close/set`

| Parameter | Type | Description |
|-----------|------|-------------|
| `polygon[]` | string[] | WKT polygon array |
| `time[]` | string[] | CSV time ranges (omit = always closed) |

---

## Geo Services

### Area Info (Building Count)
`POST https://api.longdo.com/GeoServiceMS/json/areainfo`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `polygon` | GeoJSON | Yes | Area polygon |
| `key` | string | Yes | API key |

```json
// Response
{ "ms_building": 42, "error": "string" }
```

---

## Enumerations

| Type | Values |
|------|--------|
| **LocaleCode** | `th`, `en` |
| **RouteMode** | `t` (fastest+traffic), `c` (fastest), `d` (shortest), `e` (shortest+traffic) |
| **RouteType** | `1`=road, `8`=ferry, `16`=tollway — combinable: `1\|8`=9 |
| **RouteRestrict** | `0`=none, `1`=no motorcycle routes |
| **SpanFlag** | `1`=roads, `2`=boundary — combinable |

---

## Common Patterns

### Route + draw on map
```javascript
const API_KEY = 'YOUR_KEY';

// Get route as GeoJSON and draw with MapLibre
const res = await fetch(
  `https://api.longdo.com/RouteService/geojson/route?flon=100.5&flat=13.7&tlon=100.6&tlat=13.8&key=${API_KEY}`
);
const geojson = await res.json();

map.Event.bind(longdo.EventName.Ready, () => {
  const ml = map.Renderer;
  ml.addSource('route', { type: 'geojson', data: geojson });
  ml.addLayer({
    id: 'route-line', type: 'line', source: 'route',
    paint: { 'line-color': '#0080ff', 'line-width': 4 }
  });
});
```

### Reverse geocode on map click
```javascript
map.Event.bind(longdo.EventName.Click, async () => {
  const loc = map.location();
  const res = await fetch(
    `https://api.longdo.com/map/services/address?lon=${loc.lon}&lat=${loc.lat}&locale=th&key=${API_KEY}`
  );
  const data = await res.json();
  console.log(data.province, data.district, data.subdistrict);
});
```

### Search nearby and add markers
```javascript
const res = await fetch(
  `https://api.longdo.com/POIService/json/search?lon=100.5&lat=13.7&span=1km&tag=hospital&key=${API_KEY}`
);
const { data } = await res.json();
data.forEach(place => {
  map.Overlays.add(new longdo.Marker(
    { lon: place.lon, lat: place.lat },
    { title: place.name }
  ));
});
```
