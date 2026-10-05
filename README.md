# velib-data

History of the availability of the Vélib' Métropole bike-sharing stations (Paris), recorded every
5 minutes from the official GBFS open data feeds.

The collection code lives in [tibzsecondaire/velib](https://github.com/tibzsecondaire/velib).
A live map of the latest snapshot is on <https://tibzsecondaire.github.io/velib/map/>.

## Source and license

Contains data from the Vélib' Métropole GBFS feeds
(<https://velib-metropole-opendata.smovengo.cloud/opendata/Velib_Metropole/gbfs.json>), published as
open data. The equivalent dataset published by the City of Paris on data.gouv.fr is under the ODbL.
This derived database is made available under the [Open Database License (ODbL) 1.0](./LICENSE).

## Files

| Path | Content | Updated |
|---|---|---|
| `raw/station_status.csv` | latest snapshot, one row per station | every 5 minutes |
| `raw/snapshot_meta.json` | time of the latest snapshot and number of stations | every 5 minutes |
| `daily/YYYY-MM-DD.parquet` | one UTC day, one row per station and per snapshot | every night |
| `daily/index.csv` | one row per day: snapshots, first and last fetch, largest gap | every night |
| `stations/station_information.csv` | code, name, latitude, longitude and capacity of each station | every night |

All times are UTC. `raw/station_status.csv` has the columns `station_id`, `mechanical` and `ebike`
(available bikes of each type), `docks` (free docks), `is_installed`, `is_renting`, `is_returning`
(0 or 1) and `last_reported` (Unix time of the station's last report).

Every snapshot is a commit: its message records `fetched_at`, the time of the download, and
`feed_updated_at`, the time the feed was generated. The daily Parquet files rebuild each day from
this history.

## Caveats

- GitHub can delay or skip scheduled runs, so snapshots are not exactly 5 minutes apart.
  `daily/index.csv` gives the largest gap of each day.
- Some stations have not reported for a long time. Check `last_reported`.

## Loading a day with polars

```python
import polars as pl

url = "https://raw.githubusercontent.com/tibzsecondaire/velib-data/main/daily/2026-10-06.parquet"
day = pl.read_parquet(url)
```
