# WetlandMapper workshop, Wetlands Indaba 2026

Materials for the 90-minute workshop *From an outline to wetland maps, statistics and GeoTIFFs*. The notebook runs in Google Colab or on your own computer, and you do not need to write code. Upload an outline of your own wetland, press run, and get statistics, figures, GeoTIFFs and wetland polygons.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/manudeo/Wetlands_Indaba_2026_workshop/blob/main/notebooks/WetlandMapper_workshop.ipynb)

Everything is done with [WetlandMapper](https://github.com/manudeo/wetlandmapper) itself: `fetch` downloads the satellite record from Google Earth Engine, `classify_dynamics` and `classify_wct_ema` classify it, and `summarize_dynamics`, `summarize_wct`, `class_area_timeseries`, `summarize_by_polygons`, `linear_trend`, `detect_wet_events`, `build_run_manifest` and the `plot_*` functions summarise and draw it. The notebook adds only short pieces of its own: reading your outline, caching downloads, and writing files. There are no other scripts or modules to install.

## Quick start

1. Click the Colab badge above (or open `notebooks/WetlandMapper_workshop.ipynb` on your computer, see [Run it on your own computer](#run-it-on-your-own-computer)).
2. Press the play button on each cell, in order. Cells with settings show a form on the right; click *Show code* to see what runs.
3. Run **Setup** first. In Colab it takes about a minute.
4. Run Parts A, B and C. They use sample data cached in `data/cache/` and need no account.
5. For **your own area** and for the live "this week" map, tick *USE_EARTH_ENGINE* in the setup cell (see [Use your own area](#use-your-own-area)).

| Part | What | Needs Earth Engine |
|---|---|---|
| Setup | Install, find the repository, optional Earth Engine sign-in | no |
| **A** | **Find the wetlands** in the Agulhas Plain, compared with the national wetland map | no (cached data) |
| **B** | **One wetland through 36 years**: a pan on the Agulhas Plain; dynamics, cover types, rainfall | no (cached data) |
| **C** | **Cover types this week**: the Langebaan Ramsar site, to compare with a field visit | live map: yes; sample snapshot: no |
| **Your area** | In each part, set *AREA* to *my own outline* and run it again | yes |


## What WetlandMapper gives you

**Wetland dynamics** (how has each pixel behaved?). Each pixel is classed from its wet and dry years. Anything that is not class 0 is wetland.

| Code | Class | Colour |
|---|---|---|
| 10 | Persistent | `#1a5276` |
| 6 | Intermittent | `#76d7c4` |
| 5 | Intensifying | `#2ecc71` |
| 4 | Diminishing | `#e67e22` |
| 3 | Lost | `#c0392b` |
| 2 | New | `#8e44ad` |
| 0 | Non-wetland | `#d5d8dc` |

**Wetland Cover Types** (what is it made of?). Three indices from one season's composite: MNDWI (open water), NDVI (green vegetation) and NDTI (turbidity).

| Code | Class | Colour |
|---|---|---|
| 1 | Open Clear Water | `#1a78c2` |
| 2 | Turbid / Sediment-laden Water | `#d4a017` |
| 3 | Submerged Aquatic Vegetation | `#27ae60` |
| 4 | Emergent / Floating Vegetation | `#145a32` |
| 5 | Moist / Waterlogged Soil | `#a04000` |
| 0 | Non-wetland | `#f2f3f4` |

## What you get out

Every run writes to the `outputs/` folder, one sub-folder per run. Colab deletes it when the session ends, so use the last cell to download a zip or copy it to Google Drive.

| File | What |
|---|---|
| `*_dynamics.tif`, `*_cover_types_*.tif` | GeoTIFFs in the local UTM zone (uint8, 255 = no data) with the **class colours embedded**, so QGIS shows them without a style file |
| `*_wetland_objects.gpkg`, `.kmz`, `.csv` | Wetland objects (Part A) as polygons with area, dominant class and wet frequency. Open the GeoPackage in QGIS or ArcGIS and the KMZ in Google Earth or on a phone |
| `*_dynamics_classes.csv`, `*_wet_area_by_year.csv`, `*_cover_types_by_year.csv`, `*_cover_types_change.csv`, `*_per_polygon.csv` | Statistics tables |
| `*_vs_mapped_layer.csv`, `*_candidates_not_in_mapped_layer.csv` | Comparison with a mapped layer (Part A example) |
| `*_cover_types_now.csv`, `*_scenes.csv`, `*_field_points_now.csv` | The "this week" map: class areas, the scenes used with their local times, and values at your field points |
| `*.png` | Figures |
| `*_report.md` | The statistics as plain sentences |
| `*_run_manifest.json` | Settings, input information and the software version, from `build_run_manifest`. Keep it with the maps |

## Use your own area

You need an **outline** of your wetland or study area, and **Google Earth Engine**.

### 1. Make the outline

An outline is a **polygon** (points and lines are not enough). Accepted formats:

| Format | Notes |
|---|---|
| `.kmz`, `.kml` | Draw it in Google Earth (Add, Polygon) and save the place as KMZ. Height values and extra points in the file are ignored |
| `.gpkg` | A GeoPackage, from QGIS (Export, Save Features As) or ArcGIS. A single file, so nothing can go missing |
| `.geojson` | |
| `.zip` | A zipped shapefile |
| `.shp` | Upload the `.dbf`, `.shx` and `.prj` with it. Without the `.prj` the coordinate system is unknown and the notebook stops |

Several polygons are fine; they are analysed together and the per-polygon table has one row each.

### 2. Set up Earth Engine

1. Register a Google account for Earth Engine and create a Google Cloud project with the Earth Engine API enabled ([guide](https://developers.google.com/earth-engine/guides/access)). Note the project ID.
2. In the setup cell tick `USE_EARTH_ENGINE`, paste the project ID, and run it again. Approve the sign-in the first time. On your own computer the saved credentials are reused.

Earth Engine is free for non-commercial use. Check its terms before using it for commercial work. A route without Earth Engine (the Microsoft Planetary Computer) is planned inside WetlandMapper itself; it is not in this notebook.

### 3. Run it

Run the *Your own outline* cell: choose **upload** (a button opens a file dialog), **path in Drive or Colab**, or the example KMZ to test the route. Then in Part A, B or C set *AREA* to **my own outline** and run the cell.

| Setting | Meaning |
|---|---|
| `MINIMUM_WET_YEARS_PERCENT` | How often a pixel must be wet to count as wetland (default 25) |
| `MINIMUM_OBJECT_SIZE_PIXELS` | Part A: drop wetland objects smaller than this |
| `FIRST_YEAR_*`, `LAST_YEAR_*` | 1990 to 2025 by default. Landsat 5, 7, 8 and 9 are combined. Landsat 7 scenes after May 2003 have stripes (failed scan-line corrector) and are **included**, so that years with few Landsat 5 scenes are not empty; other scenes fill the stripes |
| `RAINY_SEASON_MONTHS` | Part B: `auto` or a list such as `11,12,1,2,3`. See below |
| `DAYS_BACK`, `MINIMUM_VALID_PERCENT` | Part C: how far back to take Sentinel-2 scenes, and how clear a scene must be inside the outline |
| `FIELD_POINTS` | Part C: optional points (CSV with `lat` and `lon` columns, or a KMZ, KML or GeoPackage of points). A template is in `data/examples/` |

**Choose the rainy season for your site.** Cover types are built from a composite of the rainy-season months. `auto` reads the rainfall at your site (ERA5-Land) and picks the five wettest months inside one calendar year. The Western Cape wet season (about June to October) is not the wet season of Gauteng, KwaZulu-Natal or Lesotho (about October to March). A season that spans New Year cannot be one composite, because yearly composites are built by calendar year: it would mix two rainy seasons. The notebook tells you if the wettest months do span New Year.

### 4. Size and time

| | Comfortable |
|---|---|
| Area at 30 m | Up to about 40 km by 40 km. The notebook refuses outlines whose box is over about 2.5 million pixels |
| Record | 20 to 36 years |
| Time | Earth Engine fetches the years one by one: a small wetland takes a few minutes, a large area much longer. Years skipped by a failed download are fetched again once |

For very large areas WetlandMapper has `fetch_xee`, a lazy version of `fetch`; this notebook uses `fetch`.

## Run it on your own computer

The notebook finds the repository itself, whether you open it from `notebooks/`, from the repository root, or from a folder that contains the repository. It never clones the repository, and it does not change your Python environment: if a package is missing it stops and tells you what to install.

```bash
git clone https://github.com/manudeo/Wetlands_Indaba_2026_workshop.git
cd Wetlands_Indaba_2026_workshop
pip install "wetlandmapper[gee]>=1.2.0" geopandas rioxarray netCDF4 scipy jupyter   # or use your existing environment
jupyter lab notebooks/WetlandMapper_workshop.ipynb                                   # or open it in VS Code
```

- Any recent WetlandMapper from 1.2.0 on works.
- **Uploading** works in Colab only. Locally, set `OUTLINE = "path in Drive or Colab"` and give the file's path.
- Earth Engine: run `earthengine authenticate` once, or let the setup cell do it. The Cloud project ID is needed either way.
- The notebook writes to `outputs/` in the repository folder and caches downloads in `data/cache/`.


## How the sample data are made

| Item | Setting |
|---|---|
| Dynamics (Parts A, B) | `fetch(..., sensor="LandsatAll", index="AWEIsh", temporal_aggregation="annual", reduction_method="percentile", percentile=90, use_slc_off=True)`: one image per year, 1990 to 2025, 30 m. The 90th percentile is used because in semi-arid places water is seasonal or ephemeral and a median would miss most of it |
| Cover types (Part B, and the usual rainy season in Part C) | `fetch(..., index=["MNDWI","NDVI","NDTI"], months=[6,7,8,9,10], reduction_method="median")`: the rainy-season median of each year, 30 m. June to October is the winter-rainfall season in the Western Cape |
| Part C, this week | `fetch(..., sensor="Sentinel2", temporal_aggregation="all", scale=20, max_cloud_cover=60)`: per-scene indices of the last days, then a median of the scenes with enough valid pixels |
| Rainfall | ERA5-Land monthly through Earth Engine, averaged over the area |

Sample years with no usable composite are possible: when Landsat 7 was excluded, the sample sites had no data for about 2009 to 2012. The notebook leaves such years out of every statistic (they are not counted as dry) and greys them in the charts.

## Reading the results

- Open water is seen well. **Small, vegetated or briefly wet features** (springs, seeps, reed beds) are seen poorly: a 30 m pixel covers 0.09 ha.
- A yearly composite records the wettest cloud-free moment of the year, not the whole year.
- The **trend** is a direction over about 36 noisy yearly values, not a precise rate. The notebook says "no clear trend" when that is what the data show.
- **Cover-type classes are spectral**, not a verified habitat.

- A wetland on steep, shaded ground can be confused with shadow, and in dry climates a yearly composite can miss brief water. WetlandMapper has options for both (`dem_mask=True`, `climate_adaptive=True` in `fetch`) that this notebook does not run.
- **Report with every map:** index, sensor, composite rule (percentile and months), thresholds, years, software version. The run manifest has them.

## Troubleshooting

| Message or problem | What to do |
|---|---|
| "needs Earth Engine" | Tick `USE_EARTH_ENGINE`, enter your project ID, run Setup again |
| "Earth Engine sign-in failed" | Check that the Earth Engine API is enabled for your Cloud project and that the project ID is exact |
| "sample data ... not in data/cache/ yet" | The leader has not cached them yet. Switch on Earth Engine to download them once |
| "... is empty (an interrupted download)" | Delete that file in `data/cache/` and run the cell again with Earth Engine on |
| "Missing packages" (local) | Install what the message lists into your environment, then restart the kernel |
| "Open this notebook from inside the cloned repository" | You are outside the clone. Open the notebook from the repository folder |
| "No polygons found" | The file holds points or lines. Draw a polygon and save again |
| "has no ... coordinate system" | A shapefile is missing its `.prj`. Upload it, or save as GeoPackage |
| "too large for this notebook" | Use a smaller outline |
| "No scene has at least 40% valid pixels" | Increase `DAYS_BACK`, lower the minimum, or try after the next clear satellite pass |
| "Skipping time step ... name resolution" in the output | A download failed. The notebook fetches skipped years again once; if a year is still missing it is left out. On WSL, a restart of networking often cures name-resolution errors |
| The run is slow or the session crashes | Use a smaller outline or fewer years. The Colab free tier has about 12 GB of memory |
| Files vanish | Files in `/content` are deleted when the Colab session ends. Download the zip or copy to Drive in the last cell |

## Repository layout

```
notebooks/WetlandMapper_workshop.ipynb   the workshop notebook
data/boundaries/                         Langebaan Ramsar site, Agulhas Plain Ramsar wetlands
data/examples/                           an example outline (KMZ, GPKG) and a field-points template
data/cache/                              cached sample downloads (filled by the leader cell)
```

## Data sources and credits

- **Agulhas Plain Ramsar wetlands layer:** South African National Parks (SANParks), supplied by Ruth-Mary Fisher. It is a subset of the National Wetland Map 5 (National Biodiversity Assessment 2018); its metadata asks that the SAIIAE report is cited (see Citation). The validation fields (`Val_type`, `Val_person`, `Val_date`) are empty for pan 3462.
- **Langebaan Ramsar site boundary:** Ramsar Sites Information Service ([rsis.ramsar.org](https://rsis.ramsar.org)). The shapefile was supplied as `features_param_idrvis` and is renamed here.
- **Landsat Collection 2 Level-2:** USGS and NASA. **Sentinel-2:** ESA and Copernicus. **ERA5-Land:** Copernicus and ECMWF. All accessed through Google Earth Engine (Gorelick et al., 2017, *Remote Sensing of Environment* 202, 18-27). See the providers' terms of use.

## Citation

If you use the software or the methods, please cite:

- Singh, M. (2026). WetlandMapper. https://doi.org/10.5281/zenodo.18967176
- Singh, M., and Sinha, R. (2022). *Remote Sensing Letters*, 13(1), 1-13. https://doi.org/10.1080/2150704X.2021.1980919 (dynamics)
- Singh, M., Allaka, S., Gupta, P. K., Patel, J. G., and Sinha, R. (2022). *Environmental Monitoring and Assessment*, 194(12), 878. https://doi.org/10.1007/s10661-022-10541-7 (cover types)
- Van Deventer, H., Smith-Adao, L., Mbona, N., Petersen, C., Skowno, A., Collins, N.B., Grenfell, M., Job, N., Lötter, M., Ollis, D., Scherman, P., Sieben, E., and Snaddon, K. (2018). South African National Biodiversity Assessment 2018: Technical Report, Volume 2a: South African Inventory of Inland Aquatic Ecosystems (SAIIAE), version 3. CSIR and SANBI, Pretoria. http://hdl.handle.net/20.500.12143/5847 (Agulhas Plain layer)


## Contact

Manudeo Singh, Aberystwyth University: manudeo.singh@aber.ac.uk
