# River Pollution Catchments: Synthetic Sonde Spikes with Hidden Sources

## Overview

This dataset contains 300 synthetic river catchments, each observed for 60 days. Each catchment records its river network, the positions of its water-quality sondes, hourly rainfall and outlet flow, the storm overflow, treatment works and trade effluent releases that were recorded upstream, and every pollution spike the sondes logged. The source of each spike, either a specific recorded release or something unrecorded, is kept in a separate table and is the quantity of interest.

Nothing here is observed in any real river. Every catchment, release, spike, identifier and hourly value is produced by a generator whose draws are HMAC-SHA256 keyed to a withheld 256-bit secret, so no part of the release can be regenerated or matched against any public archive.

## Release At A Glance

- 300 catchments, 1,223 river reaches, 1,763 sondes, 29,701 recorded releases, 83,841 recorded spikes.
- A main river of 50 to 90 km and two to four tributaries of 15 to 40 km in each catchment.
- 60 days of hourly rainfall and outlet flow per catchment.
- Releases are storm overflow spills, treatment works storm spills and operator-reported trade effluent releases.
- Each spike has a peak hour, peak excesses of ammonium, conductivity and turbidity, and a duration.
- A catchment is the independent unit: its network, releases and spikes belong to it alone.

## How The Data Was Generated

Rain storms cross each catchment and raise the flow. When enough rain falls, storm overflows and treatment works spill, each for a while, and factories occasionally release trade effluent. Other pollution enters without any record: runoff from land during storms, misconnected drains, unmonitored discharges, and spills whose monitors failed. Each plume travels down the network at a speed that rises with flow, spreads out as it goes, and is diluted by the flow it joins. Ammonium and turbidity are partly lost along the way while conductivity is not. A sonde records a spike when a plume passes it strongly enough, and two plumes arriving together are recorded as one spike.

A spike's recorded source is the recorded release that contributed most to it when that release is upstream of the sonde within 60 km of river and was recorded no more than 120 hours before the spike's peak. Otherwise it is `unattributed`.

The settings follow published studies of storm overflow spill frequency and duration, velocity-discharge relations in rivers, longitudinal dispersion, in-stream ammonium loss, effluent composition, and the share of pollution incidents without an identified source. Three parts are ours: the exact signal mix of each release kind, how much turbidity settles, and how often releases go unrecorded.

## Raw File Structure

The uploaded ZIP is flat and contains exactly these ten files at its root:

- `catchments.csv`: one record per catchment: `catchment_id`, `n_reaches`, `n_stations`, `n_releases`, `n_spikes`.
- `reaches.csv`: one record per reach: `catchment_id`, `reach_id` (`main` for the main river), `length_km`, `joins_main_at_km` (empty for the main river).
- `stations.csv`: one record per sonde: `catchment_id`, `station_id`, `reach_id`, `km`.
- `releases.csv`: one record per recorded release: `release_id`, `catchment_id`, `kind` (`overflow`, `works` or `trade`), `site_id`, `reach_id`, `km`, `hour`, `size` (spill duration in hours for overflow and works, reported volume in cubic metres for trade).
- `spikes.csv`: one record per recorded spike: `spike_id`, `catchment_id`, `station_id`, `peak_hour`, `ammonium_mg_l`, `conductivity_us_cm`, `turbidity_ntu`, `duration_h`.
- `hydrology.csv`: one record per catchment and hour: `catchment_id`, `hour` (0 to 1439), `rain_mm`, `outlet_flow_m3s`.
- `sources.csv`: one creator-side record per spike: `spike_id`, `catchment_id`, `source`, which is `unattributed` or `<kind>:<release_id>`; used by `prepare.py` and never copied into public prepared data for the test catchments.
- `LICENSE`: CC BY 4.0 notice and licence URL.
- `DATASET_DESCRIPTION.md`: this description, shipped inside the archive so the card and the data cannot drift apart.
- `PACKAGE_MANIFEST.sha256`: SHA-256 checksum of every other file in the package.

## Intended Use And Limitations

The dataset is intended for work on attributing events seen at fixed monitors to typed upstream sources, where travel speed must be inferred and several sources overlap in time. It is fully synthetic. It simplifies real rivers: there are no lakes, weirs or tidal reaches, sondes do not drift or foul, background levels are constant, and plumes are summarised by their peak and duration. Results on it say nothing about any real river.

## Licence

CC BY 4.0. The dataset is synthetic and contains no personal data and no third-party material.
