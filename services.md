# Longdo Map REST API — Services Reference

**Version:** 2.3.0  
**Authentication:** Pass `?key=YOUR_API_KEY` on every request.  
**Default locale:** `th` (Thai). Add `&locale=en` for English responses.

---

## Place / Search Services

### Suggest (Autocomplete)
`GET https://search.longdo.com/mapsearch/json/suggest`

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `keyword` | string | Yes | — | Autocomplete keyword |
| `area` | CSV | No | any | Geocode filter |
| `sdx` | number | No | 0 | Enable Soundex phonetic fuzzy match (1=on) |
| `offset` | integer | No | 0 | Pagination start |
| `limit` | integer | No | 10 | Results count |
| `dataset` | CSV | No | see below | Dataset filter |
| `callback` | string | No | — | JSONP callback |
| `key` | string | No | — | API key |

Default datasets: `poi_p`, `poi_r`, `poi_a`, `poi_b`, `poi_c`, `poi2`, `poi3`, `s_pg`, `nw_s`, `m2h_s`. Optional: `bus_s`, `s_osmpnt`, `s_osmline`, `s_osmpol`, `overture_p`.

```json
{
  "meta": { "keyword": "hotel" },
  "data": [{ "w": "Hotel Suggestion", "d": "<b>Hotel</b> Suggestion", "s": "poi_p" }]
}
```

---

### Search (Full Text)
`GET https://search.longdo.com/mapsearch/json/search`

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `keyword` | string | No | — | Search keyword |
| `area` | CSV | No | any | Geocode filter |
| `lon` | number | No | Silom | Center longitude |
| `lat` | number | No | Silom | Center latitude |
| `span` | string | No | any | Radius: `"1km"`, `"500m"`, `"1deg"` |
| `tag` | CSV | No | any | Category tag filter |
| `offset` | integer | No | 0 | Pagination start |
| `limit` | integer | No | 20 | Results count |
| `dataset` | CSV | No | see below | Dataset filter |
| `locale` | string | No | `th` | `th` or `en` |
| `callback` | string | No | — | JSONP callback |
| `key` | string | No | — | API key |

Default datasets: `data2p`, `data2r`, `data2a`, `data2b`, `change`, `con`, `tag`, `pg`, `nw`, `m2h`. Optional: `bus`, `osmpnt`, `osmline`, `osmpol`, `overture2p`.

```json
{
  "meta": { "hasmore": true, "start": 0, "end": 20, "keyword": "hotel", "lon": 100.5, "lat": 13.7 },
  "data": [{
    "type": "poi", "id": "A10000001", "name": "Hotel Name",
    "lat": 13.72, "lon": 100.53, "icon": "https://...",
    "tag": ["hotel"], "url": "", "address": "123 Silom Rd",
    "tel": "02-xxx-xxxx", "contributor": "user", "verified": true,
    "obsoleted": false, "distance": "0.5km"
  }]
}
```

---

### Smart Search (Extended)
`GET https://search.longdo.com/smartsearch/json/search`

Same core parameters as Search, plus:

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `forcesmartsearch` | integer (0/1) | 0 | Always run SmartSearch (1) vs only when few results (0) |
| `forcelimit` | integer | 1 | Min results from Search before triggering SmartSearch |
| `extendedsearch` | integer (0–3) | 0 | 0=none, 1=fallback, 2=always, 3=extended only |
| `extendedlimit` | integer | 3 | Min Longdo results before using extended search |
| `extendedkey` | string | — | Extended API key |
| `extendedtype` | string | `findplacefromtext` | `findplacefromtext` or `textsearch` |
| `exceedtime` | number | 0 | Time limit in seconds; 0=disabled |
| `cache` | integer (0/1) | 0 | Enable result caching |
| `extractaddress` | integer (0/1) | 0 | Include extracted address object in response |

Response: same as Search, each item has an additional `source` field (e.g. `"longdo"`, `"OSM"`).

---

### Nearby POI
`GET https://api.longdo.com/POIService/json/search`

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `lon` | number | No | Silom | Center longitude |
| `lat` | number | No | Silom | Center latitude |
| `span` | string | No | any | Radius: `"1km"`, `"500m"` |
| `tag` | CSV | No | any | Category tag filter |
| `zoom` | integer | No | any | POI importance level |
| `area` | CSV | No | any | Geocode filter |
| `offset` | integer | No | 0 | Pagination start |
| `limit` | integer | No | 20 | Results count |
| `dataset` | CSV | No | see below | Dataset filter |
| `locale` | string | No | `th` | `th` or `en` |
| `callback` | string | No | — | JSONP callback |
| `key` | string | No | — | API key |

Default datasets: `main2p`, `main2road`, `main2adming`, `main2building`, `poi-changed-today`, `poi-contrib`, `pg-clustertag`, `water`. Optional: `osm-points`, `osm-line`, `osm-polygon`, `overture-points`.

Response: same format as Search.

---

## Address / Geocoding Services

### Reverse Geocoding (Coordinates → Address)
`GET https://api.longdo.com/map/services/address`

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `lon` | number | No* | — | Longitude (*use `id` or `lon`+`lat`) |
| `lat` | number | No* | — | Latitude |
| `id` | string | No* | — | Longdo Map ID (alternative to lon/lat) |
| `locale` | string | No | `th` | `th` or `en` |
| `noadmin` | any | No | include | Omit geocode/province/district/subdistrict |
| `nopostcode` | any | No | include | Omit postcode |
| `noelevation` | any | No | include | Omit elevation |
| `noroad` | any | No | include | Omit road info |
| `noaoi` | any | No | include | Omit area of interest |
| `nowater` | any | No | include | Omit water body |
| `callback` | string | No | — | JSONP callback |
| `key` | string | No | — | API key |

```json
{
  "geocode": "101501",
  "country": "Thailand", "province": "Bangkok",
  "district": "Bangrak", "subdistrict": "Silom",
  "moo": "", "moo_name": "", "house_num": "",
  "postcode": "10500", "aoi": "", "water": "",
  "elevation": 5.2,
  "road": "Sathorn Nuea Rd", "road_lon": 100.534, "road_lat": 13.724
}
```

---

### Batch Reverse Geocoding
`GET https://api.longdo.com/map/services/addresses`

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `lon[]` | number[] | No* | — | Longitudes, up to 100 (*`lon[]`/`lat[]` or `wkt`) |
| `lat[]` | number[] | No* | — | Latitudes, up to 100 |
| `wkt` | string | No* | — | LineString WKT (returns admin only) |
| `locale` | string | No | `th` | `th` or `en` |
| `noadmin` | any | No | include | Omit admin fields |
| `nopostcode` | any | No | include | Omit postcode |
| `noelevation` | any | No | include | Omit elevation |
| `noroad` | any | No | include | Omit road info |
| `noaoi` | any | No | include | Omit AOI |
| `nowater` | any | No | include | Omit water body |
| `callback` | string | No | — | JSONP callback |
| `key` | string | No | — | API key |

Response: array of `ReverseGeocodingResult` objects (same schema as single reverse geocoding).

---

### Geocoding (Address → Coordinates)
`GET https://search.longdo.com/addresslookup/api/addr/geocoding`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `text` | string | Yes | Address text to geocode |
| `dataset` | CSV | No | Additional dataset |
| `key` | string | No | API key |

```json
[{
  "confidence": 0.95,
  "location": {
    "point": { "name": "Sathorn Square" },
    "houseno": "98",
    "road": { "name": "Sathorn Nuea Rd" },
    "village": {}, "moo": "",
    "subdistrict": { "name": "Silom" },
    "district": { "name": "Bangrak" },
    "province": { "name": "Bangkok" },
    "zipcode": "10500"
  },
  "point": [{ "lon": 100.534, "lat": 13.724 }]
}]
```

---

### Geocode / Postcode Lookup
`GET https://api.longdo.com/POIService/json/address`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `geocode` | string | No* | Geocode to look up (*one required) |
| `postcode` | string | No* | Postcode to look up |
| `locale` | string | No | `th` or `en` |
| `callback` | string | No | JSONP callback |
| `key` | string | No | API key |

```json
// By geocode
{ "geocode": "101501", "province": "กรุงเทพมหานคร", "district": "บางรัก", "subdistrict": "สีลม", "lat": 13.72, "lon": 100.53 }

// By postcode — returns array
{ "address": [{ "geocode": "101501", "province": "...", ... }] }
```

---

### Extract Address (Parse Address Text)
`GET https://search.longdo.com/smartsearch/json/extract_address/v2`

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `text` | string | Yes | — | Address text to parse |
| `correction` | integer | No | 1 | Enable text correction (1=on) |
| `censor` | integer | No | 0 | Remove phone/email from remark (1=on) |
| `locale` | string | No | `th` | `th` or `en` |
| `key` | string | No | — | API key |

```json
{
  "meta": { "input": "...", "remark": "Tel. 02-632-9700", "locale": "th" },
  "data": [{
    "house_no": "98", "floor": "4", "room": "407", "moo": "",
    "place": { "name": "Sathorn Square", "distance": 0, "prefix": true },
    "building": { "name": "Sathorn Square Office Tower" },
    "road": { "name": "Sathorn Nuea" },
    "subdistrict": { "name": "Silom" },
    "district": { "name": "Bangrak" },
    "province": { "name": "Bangkok" },
    "postcode": "10500", "country": "Thailand", "confidence": 0.92
  }]
}
```

---

## Routing Services

### Calculate Route
`GET https://api.longdo.com/RouteService/json/route/guide`

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `flon` | number | No* | — | Start longitude (*coordinates recommended) |
| `flat` | number | No* | — | Start latitude |
| `tlon` | number | No* | — | Destination longitude |
| `tlat` | number | No* | — | Destination latitude |
| `mode` | string | No | `t` | `t`=fastest+traffic, `c`=fastest, `d`=shortest, `e`=shortest+traffic |
| `type` | integer | No | — | Travel methods (OR-combined): `1`=road, `8`=ferry, `16`=tollway |
| `restrict` | integer | No | 0 | `0`=none, `1`=no motorcycle routes |
| `locale` | string | No | `th` | `th` or `en` |
| `time` | Unix time | No | now | Departure time (future only) |
| `maxresult` | integer | No | 1 | Number of routes (max 8) |
| `callback` | string | No | — | JSONP callback |
| `key` | string | No | — | API key |

```json
{
  "meta": { "from": { "lon": 100.537, "lat": 13.724 }, "to": { "lon": 100.541, "lat": 13.727 }, "config": "..." },
  "data": [{
    "fdistance": 10, "tdistance": 15, "distance": 1200, "interval": 300, "id": 1,
    "guide": [
      { "turn": 5, "name": "Sathorn Nuea Rd", "distance": 600, "interval": 90 },
      { "turn": 1, "name": "Silom Rd", "distance": 600, "interval": 90 },
      { "turn": 9, "name": "", "distance": 0, "interval": 0 }
    ]
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
| 5 | Go straight |
| 6 | Enter tollway |
| 9 | Arrive at destination |
| 11 | Travel by ferry |

---

### Get Route Path (Geometry)
`GET https://api.longdo.com/RouteService/json/route/path`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | integer | Yes | Path ID from Calculate Route response |
| `key` | string | No | API key |

```json
{
  "meta": { "id": 1 },
  "data": [[{ "lon": 100.537, "lat": 13.724 }, { "lon": 100.538, "lat": 13.725 }]]
}
```

---

### Calculate Route (GeoJSON)
`GET https://api.longdo.com/RouteService/geojson/route`

Same parameters as Calculate Route. Returns a GeoJSON `FeatureCollection` where each `Feature` has a `LineString` geometry and `GuideRoute` properties, plus top-level `meta` and `data` fields.

```javascript
const res = await fetch(
  `https://api.longdo.com/RouteService/geojson/route?flon=100.537&flat=13.724&tlon=100.541&tlat=13.727&key=${API_KEY}`
);
const geojson = await res.json();
map.Renderer.addSource('route', { type: 'geojson', data: geojson });
map.Renderer.addLayer({ id: 'route', type: 'line', source: 'route',
  paint: { 'line-color': '#0080ff', 'line-width': 4 }
});
```

---

### Route Matrix (Multiple Origins/Destinations)
`GET https://api.longdo.com/RouteService/json/route/matrix`

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `flon[]` | number[] | Yes | — | Start longitudes (max 25) |
| `flat[]` | number[] | Yes | — | Start latitudes (max 25) |
| `tlon[]` | number[] | Yes | — | Destination longitudes (max 25) |
| `tlat[]` | number[] | Yes | — | Destination latitudes (max 25) |
| `mode` | string | No | `t` | Route mode |
| `type` | integer | No | — | Travel methods |
| `restrict` | integer | No | 0 | Vehicle restriction |
| `locale` | string | No | `th` | `th` or `en` |
| `time` | Unix time | No | now | Departure time |
| `callback` | string | No | — | JSONP callback |
| `key` | string | No | — | API key |

```json
{
  "meta": { "fromCount": 2, "toCount": 2 },
  "data": [[{ "interval": 300, "distance": 1200 }, { "interval": 600, "distance": 2400 }]]
}
```

---

### Snap to Road
`GET https://api.longdo.com/RouteService/json/route/snap`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `wkt` | string | No | LineString WKT (e.g. `LINESTRING(100.5 13.7, 100.51 13.71)`) |
| `key` | string | No | API key |

Response: array of `Location` objects snapped to nearest road.

---

### Traffic Speed (Real-time)
`GET https://api.longdo.com/RouteService/json/traffic/speed`

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `lon` | number | Yes | — | Longitude |
| `lat` | number | Yes | — | Latitude |
| `range` | number | No | 0.001 | Search radius in degrees (max 0.001 ≈ 100 m) |
| `locale` | string | No | `th` | `th` or `en` |
| `time` | Unix time | No | now | Get predicted data at future time |
| `callback` | string | No | — | JSONP callback |
| `key` | string | No | — | API key |

```json
{ "road": "Sathorn Nuea Rd", "dir": "forward", "speed": 13.8, "source": "real-time", "lon": 100.534, "lat": 13.724 }
```

Speed unit: **m/s** (multiply by 3.6 for km/h). On no data: `{ "meta": { "status": 404, "message": "..." } }`.

---

### Get Span Area (Reachable Area)
`GET https://api.longdo.com/RouteService/json/span/get`

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `flon` | number | Yes | — | Start longitude |
| `flat` | number | Yes | — | Start latitude |
| `span` | string | No | — | Distance or time: `"1000m"` (max 30000m), `"1800s"` (max 1800s) |
| `flag` | integer | No | all | Bitwise: `1`=road lines, `2`=boundary polygon |
| `callback` | string | No | — | JSONP callback |
| `key` | string | No | — | API key |

```json
{
  "from": { "lon": 100.537, "lat": 13.724 },
  "lines": [[{ "lon": 100.537, "lat": 13.724 }, ...]],
  "polygon": [{ "lon": 100.54, "lat": 13.73 }, ...]
}
```

---

### TSP — Route Planner (Traveling Salesman)
`POST https://api.longdo.com/route-tsp-v2`  
Content-Type: `application/x-www-form-urlencoded`

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `lon[]` | number[] | Yes | — | Waypoint longitudes |
| `lat[]` | number[] | Yes | — | Waypoint latitudes |
| `cost` | boolean | No | false | Include `time_step`/`distance_step` details |
| `otsp` | boolean | No | false | `true`=return to start (closed), `false`=open loop |
| `mode` | string | No | `t` | Route mode (`t`,`c`,`d`,`e`) |
| `key` | string | Yes | — | API key |

```json
{
  "status": 200, "message": "ok", "otsp": "0", "mode": "t",
  "body": {
    "sequence": [0, 2, 1, 3],
    "location": [{ "lon": 100.5, "lat": 13.7 }],
    "total_distance": 5400,
    "total_time": 900
  }
}
```

---

## Extra API Services

> **Note:** Extra API endpoints are experimental. They consume API transactions but are not covered by the Longdo Map SLA and may change without notice.

### Population (Thai Official Statistics)
`GET https://map.longdo.com/ws/stats/population`

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `year` | number | No | latest | Thai Buddhist Era year (พ.ศ.) |
| `geocode` | string | No | all | Province/district/subdistrict/village code (2–6 digits, e.g. `10`=Bangkok, `1101`=Phra Nakhon) |
| `key` | string | No | — | API key |

Source: Department of Provincial Administration (DOPA).

```json
{
  "data": [{ "geocode": "10", "name": "กรุงเทพมหานคร", "name_en": "Bangkok", "m_amount": 2800000, "f_amount": 3100000, "total": 5900000 }],
  "status": { "code": 200, "error_code": null, "text": "ok", "details": null }
}
```

---

### Household Count
`GET https://map.longdo.com/ws/stats/household`

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `year` | number | Yes | — | Thai Buddhist Era year |
| `geocode` | string | No | all | Province/district/subdistrict/village code |
| `key` | string | No | — | API key |

Source: DOPA.

```json
{
  "data": [{ "geocode": "10", "name": "กรุงเทพมหานคร", "name_en": "Bangkok", "value": 2300000 }],
  "status": { "code": 200 }
}
```

---

### Household Debt (Province Level)
`GET https://map.longdo.com/ws/stats/debt`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `year` | number | Yes | Thai Buddhist Era year |
| `key` | string | No | API key |

Source: National Statistical Office (NSO). Returns debt per household in Baht.

```json
{
  "data": [{ "geocode": "10", "name": "กรุงเทพมหานคร", "name_en": "Bangkok", "value": 195000 }],
  "status": { "code": 200 }
}
```

---

### Deposits (District/Province Level)
`GET https://map.longdo.com/ws/stats/deposit`

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `year` | number | Yes | — | Thai Buddhist Era year |
| `month` | number | Yes | — | Month (1–12) |
| `geocode` | string | No | all | Province code (e.g. `10`=Bangkok) |
| `key` | string | No | — | API key |

Source: NSO. Value in million Baht.

```json
{
  "data": [{ "geocode": "10", "name": "กรุงเทพมหานคร", "name_en": "Bangkok", "value": 12500000 }],
  "status": { "code": 200 }
}
```

---

### Loans (District/Province Level)
`GET https://map.longdo.com/ws/stats/loan`

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `year` | number | Yes | — | Thai Buddhist Era year |
| `month` | number | Yes | — | Month (1–12) |
| `geocode` | string | No | all | Province code |
| `key` | string | No | — | API key |

Source: NSO. Value in million Baht.

```json
{
  "data": [{ "geocode": "10", "name": "กรุงเทพมหานคร", "name_en": "Bangkok", "value": 8700000 }],
  "status": { "code": 200 }
}
```

---

### Income (Province Level)
`GET https://map.longdo.com/ws/stats/income`

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `year` | number | No | latest | Thai Buddhist Era year |
| `key` | string | No | — | API key |

Source: NSO. Value in Baht.

```json
{
  "data": [{ "geocode": "10", "name": "กรุงเทพมหานคร", "name_en": "Bangkok", "value": 450000 }],
  "status": { "code": 200 }
}
```

---

### Facebook Population (Density Estimate)
`GET https://map.longdo.com/ws/population/facebook`

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `wkt` | string | No* | — | WKT polygon (max 1,000 km²) (*one of `wkt`/`geocode`/`dlacode` required) |
| `geocode` | string | No* | — | Thai admin geocode (2/4/6 digits) |
| `dlacode` | string | No* | — | DLA local admin code (2–7 digits) |
| `year` | number | No | 2020 | Data year (CE) |
| `key` | string | No | — | API key |

Source: Meta/Facebook High Resolution Population Density Maps.

```json
{
  "data": { "summary": 15234.5 },
  "meta": { "source": "Facebook", "year": "2020" },
  "status": { "code": 200 }
}
```

---

### Area Info (Building Count)
`GET https://api.longdo.com/GeoServiceMS/json/areainfo`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `polygon` | string (GeoJSON) | Yes | GeoJSON Polygon string |
| `key` | string | Yes | API key |

```json
{ "ms_building": 42 }
```

Example polygon: `{"type":"Polygon","coordinates":[[[100.538,13.734],[100.545,13.733],[100.544,13.726],[100.536,13.729],[100.538,13.734]]]}`

---

### Government Count (Admin Boundary Count)
`POST https://map.longdo.com/ws/area/government-count`  
Content-Type: `application/json`

Body (one of):
```json
{ "wkt": "POLYGON((100.5 13.7, 100.6 13.7, 100.6 13.8, 100.5 13.8, 100.5 13.7))" }
// or
{ "geocode": "10" }
```

```json
{
  "data": { "p_count": 1, "a_count": 50, "t_count": 154, "m_count": 1964 },
  "status": { "code": 200 }
}
```

`p_count`=provinces, `a_count`=amphoes (districts), `t_count`=tambons (subdistricts), `m_count`=mubans (villages).

---

### Land Parcel Count
`POST https://map.longdo.com/ws/area/land-parcel-count`  
Content-Type: `application/json`

Body: `{ "wkt": "POLYGON(...)" }` or `{ "geocode": "10" }`

```json
{
  "data": { "land_parcel_count": 125430 },
  "status": { "code": 200 }
}
```

---

### Land Custom (Zoning / City Plan)
`POST https://map.longdo.com/ws/area/land-custom`  
Content-Type: `application/json`

Body: `{ "wkt": "POLYGON(...)" }` or `{ "geocode": "10" }`

```json
{
  "data": {
    "result": [
      { "code": "Y3", "area_sq_meters": 120000, "count": 45, "description": "ที่อยู่อาศัยหนาแน่นน้อย" }
    ],
    "total_area": 500000, "total_count": 150
  },
  "status": { "code": 200 }
}
```

---

### Land Use
`POST https://map.longdo.com/ws/area/land-use`  
Content-Type: `application/json`

Body: `{ "wkt": "POLYGON(...)" }` or `{ "geocode": "10" }`

```json
{
  "data": {
    "result": [
      { "lu_code": "A", "lu_des_th": "เกษตรกรรม", "lu_des_en": "Agriculture", "sum_area": 30000 }
    ]
  },
  "status": { "code": 200 }
}
```

---

### Land Value (Appraisal Price)
`POST https://map.longdo.com/ws/area/land-value`  
Content-Type: `application/json`

Body: `{ "wkt": "POLYGON(...)" }` or `{ "geocode": "10" }`

```json
{
  "data": {
    "count": 320, "sum_area": 450000, "average_area": 1406.25,
    "average_price": 85000, "min_price": 5000, "max_price": 500000
  },
  "status": { "code": 200 }
}
```

Prices in Baht/sq.wah (ตร.ว.).

---

### Highway Route Location Lookup
`GET https://map.longdo.com/ws/route/location`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `route_no` | number | Yes | Highway route number |
| `km` | number | Yes | Kilometre marker |
| `key` | string | No | API key |

Source: DOH (กรมทางหลวง) / DRR (กรมทางหลวงชนบท).

```json
{
  "data": { "route_no": "4", "road_name": "ถนนเพชรเกษม", "km": "177.367", "km_start": "0.000", "km_end": "400.000", "lat": "8.0000", "lon": "98.9000", "source": "DOH" },
  "status": { "code": 200 }
}
```

---

### Highway Route Name from Location
`GET https://map.longdo.com/ws/route/name`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `lat` | number | Yes | Latitude |
| `lon` | number | Yes | Longitude |
| `key` | string | No | API key |

```json
{
  "data": [{ "route_no": "4", "road_name": "ถนนเพชรเกษม", "km": "177.367", "distance": "12.5", "source": "DOH" }],
  "meta": { "span": "0.01deg", "max_span_m": "5000", "max_span_deg": "0.045" },
  "status": { "code": 200 }
}
```

---

## Schemas

### AddressInfo
| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Place/building/road/area name |
| `distance` | integer | Edit distance from input (0 = exact match) |
| `prefix` | boolean | Whether input had a prefix word |

### Location
| Field | Type | Description |
|-------|------|-------------|
| `lon` | number | Longitude |
| `lat` | number | Latitude |

### ReverseGeocodingResult
| Field | Type | Description |
|-------|------|-------------|
| `geocode` | string | Thai admin geocode |
| `country` | string | Country name |
| `province` | string | Province name |
| `district` | string | District name |
| `subdistrict` | string | Subdistrict name |
| `moo` | string | Village number |
| `moo_name` | string | Village name |
| `house_num` | string | House number |
| `postcode` | string | Postal code |
| `aoi` | string | Area of interest name |
| `water` | string | Nearby water body name |
| `elevation` | number | Elevation in meters |
| `road` | string | Nearest road name |
| `road_lon` | number | Nearest road longitude |
| `road_lat` | number | Nearest road latitude |

### GuideRoute
| Field | Type | Description |
|-------|------|-------------|
| `turn` | integer | TurnCode (see table above) |
| `name` | string | Road/segment name |
| `distance` | integer | Segment distance in meters |
| `interval` | integer | Segment travel time in seconds |

---

## Enumerations

| Type | Values |
|------|--------|
| **locale** | `th` (default, Thai), `en` (English) |
| **mode** (route) | `t`=fastest+traffic (default), `c`=fastest, `d`=shortest, `e`=shortest+traffic |
| **type** (route) | `1`=road, `8`=ferry, `16`=tollway — OR-combinable (e.g. `17`=road+tollway) |
| **restrict** | `0`=none (default), `1`=no motorcycle-restricted roads |
| **flag** (span) | `1`=road lines, `2`=boundary — bitwise OR-combinable |
| **extendedsearch** | `0`=none (default), `1`=fallback, `2`=always, `3`=extended-only |

---

## Common Patterns

### Route + draw on map
```javascript
const API_KEY = 'YOUR_KEY';

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

### Population stats for a province
```javascript
// Get population data for Bangkok (geocode 10), year 2566 (BE)
const res = await fetch(
  `https://map.longdo.com/ws/stats/population?geocode=10&year=2566&key=${API_KEY}`
);
const { data } = await res.json();
console.log(`${data[0].name_en}: ${data[0].total.toLocaleString()} people`);
```

### Land value for a drawn polygon
```javascript
const wkt = 'POLYGON((100.5 13.7, 100.6 13.7, 100.6 13.8, 100.5 13.8, 100.5 13.7))';
const res = await fetch('https://map.longdo.com/ws/area/land-value?key=' + API_KEY, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ wkt })
});
const { data } = await res.json();
console.log(`Avg price: ${data.average_price} THB/sq.wah, Parcels: ${data.count}`);
```
