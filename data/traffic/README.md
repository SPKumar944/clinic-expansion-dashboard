# Indiranagar traffic data

## Files

- `indiranagar_driving_isochrones.geojson` — 5-, 10-, and 15-minute OSM/Valhalla driving-time contours centered near 754 Shri Krishna Temple Road (12.9793757 N, 77.6449363 E). Generated 14 Oct 2026; contours do not model traffic.
- `indiranagar_traffic_observations.csv` and `indiranagar_google_traffic_origins.geojson` — five Google Maps route observations captured once on 14 Oct 2026 around 14:51–14:54 IST. These are the only measured traffic records in this folder.
- `indiranagar_hourly_traffic_estimates_30d.csv` — 2,400 modeled rows: five sampled approach origins × 30 dates (15 Sep–14 Oct 2026) × 16 hour slots (08:00–23:00 IST).
- `indiranagar_hourly_traffic_estimates_30d.xlsx` — workbook version of the same estimates, with provenance/method and assumed hourly factor sheets.
- `indiranagar_hourly_traffic_estimates_map.html` — date/hour map view of the modeled route ETAs, with an optional overlay of the five measured snapshot points.
- `indiranagar_traffic_snapshot_map.html` — earlier static map showing only the one-time snapshot.

## Critical interpretation

The 30-day route ETA values are **estimates for prototyping, not historical traffic measurements**. Google Maps did not provide archived readings for these dates, and the project did not contain an existing route-monitoring archive. No past values have been represented as if they were observed.

The estimates use a simple reproducible scenario formula:

```text
estimated route ETA = ETA reference × assumed weekday/weekend hour factor
```

The ETA reference is the single snapshot ETA, less Google's reported delay where a numeric incident delay was shown. This subtraction assumes the displayed incident delay is additive. The hourly multipliers are an illustrative weekday/weekend time-of-day profile normalized to 1.00 at 14:00. They are not calibrated to historical Google data or traffic counts. Date-specific variation, weather, holidays, incidents, and roadworks are not modeled. Do not use the values for routing, operational, or investment decisions.

In the CSV/XLSX, `DataStatus` explicitly distinguishes the modeled rows. `SnapshotEtaMin` and `SnapshotReportedDelayMin` are populated only for the 14 Oct 2026 14:00–14:59 slot, where the one-time UI observations occurred. The observed and estimated values remain separate.

The five origins are sampled route endpoints within the OSM isochrone bands, not a complete traffic sensor network and not every road segment in each polygon. Absolute ETAs differ by route length; comparisons are most meaningful for the same origin across hours. Map point colors encode estimated route-time bands, not measured road speed.

## Map view

Open `indiranagar_hourly_traffic_estimates_map.html` in a browser. Select a date and hour between 08:00 and 23:00 IST. Check **Show 14 Oct measured snapshot markers** to reveal the measured readings only when 14 Oct 2026 and the 14:00 hour are selected. The isochrone map zoom remains independent of date/hour selection.

Rebuild the interactive map after updating the source CSV or GeoJSON files:

```bash
python3 "/Volumes/2TB/office works/Hospital project/dashboard/source/build_traffic_snapshot_map.py"
```

The builder embeds the local data in the generated HTML. The basemap and Leaflet library load from external OpenStreetMap and unpkg URLs, so the page needs an internet connection to render the map.

## Building measured history going forward

Google's regular driving Routes API does not accept past departure times. Google Roads Management Insights can collect selected-route traffic after route registration; it cannot retroactively create the missing September history for routes that were not already monitored. To replace estimates with measurements, create/register the route set in a configured Google Cloud project, capture its hourly route travel time and speed intervals, and append only provider-returned records with capture timestamps and source metadata. Keep modeled rows separate from measured observations. See Google's [Roads Selection API overview](https://developers.google.com/maps/tt/roads-selection/overview) and [historical road data reference](https://developers.google.com/maps/tt/roads-selection/historical-roads-data).
