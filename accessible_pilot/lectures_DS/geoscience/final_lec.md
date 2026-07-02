# A quick tour of further topics in geoscience data science

:::{admonition} Following along with a screen reader
:class: important
Work through the examples as `.py` scripts in VS Code — see [Setting up an accessible workflow](../computing_env/accessible_setup.md) and [Working with Python scripts](../computing_env/working_with_scripts.md). Run a script in the Git Bash terminal with `python yourfile.py`, and press `Ctrl+Up` to read the output.

Two page-specific notes. First, this is an **appendix survey** — you are not expected to run most of it. Every output is reproduced on this page in labelled "expected output" blocks, so you can read along without running anything. Second, most examples here use packages (`cartopy`, `xmip`, `intake-esm`) that are **not part of the local course install**. If you do want to try a piece yourself, this page is best worked through connected to the **LEAP hub**, where those packages exist — use VS Code's Remote-SSH connection to the hub, described at the end of the [setup page](../computing_env/accessible_setup.md). The few blocks marked *runnable locally* below need only the usual course packages plus an internet connection.
:::

The field of data science for the Earth sciences is vast, and the software tools are always growing and changing. If you end up using these tools in your work, you can (and should) expect to keep learning and refining your skills and toolbox. It also means that no single course can cover everything — especially not a six-week one.

This page is a **survey**: a high-level tour of a few more concepts and software packages that make good starting points for further exploration. It belongs to the appendix and is **not assessed** — read it for orientation, and come back to it when a project (or your later work) pushes you toward one of these tools.

:::{admonition} This is a read-along tour, not a hands-on lab
:class: note
Unlike the rest of the course, this page uses specialised packages that are **not installed in the course environment** (`cartopy`, `xmip`, `intake-esm`, …) and pulls large datasets from the cloud. You are **not expected to run it**. The outputs are saved in the page so you can read along; if you want to try a piece yourself, connect to the LEAP hub first (and `pip install` anything still missing there).

One script-specific habit, used throughout: where the original notebook ended a cell with a bare expression (like `sst` or `ccrs.PlateCarree()`), a script would display nothing — so on this page those are wrapped in `print(...)`.
:::

## Maps

This is a derivative of the longer notebook available [here](https://earth-env-data-science.github.io/lectures/mapping_cartopy.html).

Making maps is a fundamental part of geoscience research. Maps differ from regular figures in the following principle ways:

- Maps require a projection of geographic coordinates on the 3D Earth to the 2D space of your figure.

- Maps often include extra decorations besides just our data (e.g. continents, country borders, etc.)

Mapping is a notoriously hard and complicated problem, mostly due to the complexities of projection.

In this lecture, we will learn about Cartopy, one of the most common packages for making maps within python.

### Projections: Most of our media for visualization *are* flat

Our two most common media are flat:

 * Paper
 * Screen

*The original page shows a photograph here: a hand holding a printed page up in front of a computer monitor that is displaying a document. In words: both of our everyday media — paper and screen — are flat rectangles.*

#### [Map] Projections: Taking us from spherical to flat

A map projection (or more commonly refered to as just "projection") is:

> a systematic transformation of the latitudes and longitudes of locations from the surface of a sphere or an ellipsoid into locations on a plane. [[Wikipedia: Map projection](https://en.wikipedia.org/wiki/Map_projection)].

#### The major problem with map projections

*The original page shows a photograph of an orange peel, removed from the fruit and pressed flat on a glass table, with a world map drawn on it in ink. In words: the peel refuses to lie flat as one piece — it splits into lobes with gaps between them, and the inked continents tear apart at the splits. That is exactly the problem with flattening a sphere:*

 * The surface of a sphere is topologically different to a 2D surface, therefore we *have* to cut the sphere *somewhere*
 * A sphere's surface cannot be represented on a plane without distortion.

There are many different ways to make a projection, and we will not attempt to explain all of the choices and tradeoffs here. Instead, you can read Phil's [original tutorial](https://github.com/SciTools/cartopy-tutorial/blob/master/tutorial/projections_crs_and_terms.ipynb) for a great overview of this topic.
Instead, we will dive into the more practical sides of Caropy usage.

### Introducing Cartopy

<https://scitools.org.uk/cartopy/docs/latest/>

Cartopy makes use of the powerful [PROJ.4](https://proj4.org/), numpy and shapely libraries and includes a programatic interface built on top of Matplotlib for the creation of publication quality maps.

Key features of cartopy are its object oriented projection definitions, and its ability to transform points, lines, vectors, polygons and images between those projections.

The following is Python code:

```python
import cartopy.crs as ccrs
import cartopy
```

End of code.

A projection in cartopy is an object. Printing its `repr` gives the same multi-line summary a notebook would display — a description of the coordinate reference system (CRS):

The following is Python code:

```python
print(repr(ccrs.PlateCarree()))
```

End of code.

The following is the expected output:

```
<Projected CRS: +proj=eqc +ellps=WGS84 +a=6378137.0 +lon_0=0.0 +to ...>
Name: unknown
Axis Info [cartesian]:
- E[east]: Easting (unknown)
- N[north]: Northing (unknown)
- h[up]: Ellipsoidal height (metre)
Area of Use:
- undefined
Coordinate Operation:
- name: unknown
- method: Equidistant Cylindrical
Datum: Unknown based on WGS 84 ellipsoid
- Ellipsoid: WGS 84
- Prime Meridian: Greenwich
```

End of output.

Listening tips: the useful lines are the **method** ("Equidistant Cylindrical" — the formal name of the Plate Carree projection, where longitude and latitude map directly to x and y) and the **ellipsoid** (WGS 84, the standard model of the Earth's shape). The various "unknown"s just mean this generic projection isn't tied to a named, registered CRS.

Another projection, same kind of summary:

The following is Python code:

```python
print(repr(ccrs.LambertAzimuthalEqualArea()))
```

End of code.

The following is the expected output:

```
<Projected CRS: +proj=laea +ellps=WGS84 +lon_0=0.0 +lat_0=0.0 +x_0 ...>
Name: unknown
Axis Info [cartesian]:
- E[east]: Easting (metre)
- N[north]: Northing (metre)
Area of Use:
- undefined
Coordinate Operation:
- name: unknown
- method: Lambert Azimuthal Equal Area
Datum: Unknown based on WGS 84 ellipsoid
- Ellipsoid: WGS 84
- Prime Meridian: Greenwich
```

End of output.

Cartopy plugs straight into matplotlib: pass a projection when you create the axes, and you get a special map-aware axes (a `GeoAxes`) with extra methods like `coastlines()` and `stock_img()`. It still answers all the usual matplotlib questions (`ax.get_title()`, `len(fig.axes)`, and so on) if you want to interrogate it.

The following is Python code:

```python
# Can use with matplotlib
import matplotlib.pyplot as plt

ax = plt.axes(projection=ccrs.PlateCarree())
ax.coastlines()
ax.stock_img()
plt.show()
```

End of code.

**What this shows.** A flat rectangular world map, about twice as wide as it is tall: on Plate Carree, longitude maps uniformly left-to-right (180°W to 180°E) and latitude bottom-to-top (90°S to 90°N). The background is cartopy's built-in "stock image" of Earth — oceans in blue, land shaded tan and green — with black coastlines drawn on top. Shapes near the equator look about right; toward the poles everything is stretched east–west, so Antarctica smears along the entire bottom edge.

The following is Python code:

```python
ax = plt.axes(projection=ccrs.LambertAzimuthalEqualArea())
ax.coastlines()
ax.stock_img()
plt.show()
```

End of code.

**What this shows.** The entire globe squeezed into a single circular disk, centered on latitude 0, longitude 0 (on the equator, just south of West Africa): Africa and Europe sit near the center, the Americas curve around the left side, Asia around the right, Antarctica at the bottom of the disk. This projection preserves *areas* exactly — the price is shape distortion, which grows toward the rim, where the continents look bent around the circle.

The following is Python code:

```python
# Can make regional maps
central_lon, central_lat = -10, 45
extent = [-40, 20, 30, 60]
ax = plt.axes(projection=ccrs.Orthographic(central_lon, central_lat))
ax.set_extent(extent)
ax.gridlines()
ax.coastlines(resolution='50m')
plt.show()
```

End of code.

**What this shows.** A regional line-drawing map (no color fill) of western Europe and the nearby Atlantic — 40°W to 20°E, 30°N to 60°N: black coastlines trace Great Britain and Ireland near top center, the French and Iberian coasts below them, the western Mediterranean with Italy, Corsica and Sardinia at the lower right, and the northwest coast of Africa along the bottom edge. The Azores appear as tiny specks at lower left. Thin gray gridlines arc gently across the frame — on an orthographic view (the Earth as seen from space), circles of latitude are curves, not straight lines. Note `resolution='50m'`: we asked for medium-resolution coastlines, appropriate for a regional map.

The following is Python code:

```python
# Can make regional maps
central_lon, central_lat = -10, 45
extent = [-75, -72, 39, 41]
ax = plt.axes(projection=ccrs.Orthographic(central_lon, central_lat))
ax.set_extent(extent)
ax.gridlines()
ax.coastlines(resolution='50m')
ax.stock_img()
plt.show()
```

End of code.

**What this shows.** A very zoomed-in map of the New York area (75°W to 72°W, 39°N to 41°N): black coastlines showing Long Island, Long Island Sound, and the New Jersey shore. The background gives the figure away: at this zoom the global stock image is far too coarse, so it appears as huge blurry diamond-shaped pixels — greenish-tan across the top (land), flat blue below (ocean). The lesson: the built-in background is a low-resolution *global* image — fine for whole-Earth maps, useless up close.

The following is Python code:

```python
# Can add some default features
import cartopy.feature as cfeature
import numpy as np

central_lat = 37.5
central_lon = -96
extent = [-120, -70, 24, 50.5]
central_lon = np.mean(extent[:2])
central_lat = np.mean(extent[2:])

plt.figure(figsize=(12, 6))
ax = plt.axes(projection=ccrs.AlbersEqualArea(central_lon, central_lat))
ax.set_extent(extent)

ax.add_feature(cartopy.feature.OCEAN)
ax.add_feature(cartopy.feature.LAND, edgecolor='black')
ax.add_feature(cartopy.feature.LAKES, edgecolor='black')
ax.add_feature(cartopy.feature.RIVERS)
ax.gridlines()
plt.show()
```

End of code.

**What this shows.** A map of the contiguous United States on an Albers equal-area projection (the standard choice for the US — the frame itself is a plain rectangle, but the faint gray gridlines bow gently across the map rather than running straight). Land is filled pale beige, the ocean pale blue; the Great Lakes stand out in blue with black outlines, and major rivers — the Mississippi system, the Columbia, the Rio Grande — thread across the interior as thin blue lines. Nothing here is data: OCEAN, LAND, LAKES and RIVERS are ready-made decoration layers from cartopy's Natural Earth feature library.

So far we've only drawn decorations. The real point of a map is to put **your data** on it. Let's make a synthetic 2-D field and first plot it on ordinary, non-map axes. *(This block and its check are runnable locally — only numpy and matplotlib.)*

The following is Python code:

```python
import numpy as np
import matplotlib.pyplot as plt

lon = np.linspace(-80, 80, 25)
lat = np.linspace(30, 70, 25)
lon2d, lat2d = np.meshgrid(lon, lat)
data = np.cos(np.deg2rad(lat2d) * 4) + np.sin(np.deg2rad(lon2d) * 4)
plt.contourf(lon2d, lat2d, data)
plt.show()
```

End of code.

**What this shows.** A filled-contour plot on plain axes: x from −80 to 80, y from 30 to 70, in the default viridis colormap. The field forms two large dark-purple ovals (its lowest values, about −2.0) centered near x = −22 and x = +67 at mid-height (y around 45), bright-yellow patches (its highest values, up to about +1.2) hugging the top edge near x = −67 and x = +22 (with smaller yellow wedges on the bottom edge at the same x positions), and green–teal bands in between.

**Check it.** Confirm the extremes from the array itself:

The following is Python code:

```python
print(data.shape)
print(round(data.min(), 2), round(data.max(), 2))
imin = np.unravel_index(data.argmin(), data.shape)
print(round(lon2d[imin], 1), lat2d[imin])
```

End of code.

The following is the expected output:

```
(25, 25)
-2.0 1.17
66.7 45.0
```

End of output.

Now the same field on a map — the identical `contourf` call, but into a map-projection axes. Because these axes are `PlateCarree` and our coordinates are plain longitude/latitude, the data lands in the right place without any extra arguments:

The following is Python code:

```python
ax = plt.axes(projection=ccrs.PlateCarree())
ax.set_global()
ax.coastlines()
ax.contourf(lon, lat, data)
plt.show()
```

End of code.

**What this shows.** The whole world drawn only as black coastal outlines on white (`set_global()` keeps the full globe in view), with the colored contour field draped over its true geographic region — a band from 80°W to 80°E and 30°N to 70°N covering the North Atlantic, Europe, and central Asia. One purple low sits over the mid-North-Atlantic (near 22°W), the other over central Asia (near 67°E); the rest of the map is blank. The data didn't change — the axes just know about the Earth now.

### Putting real data on a map: SST with xarray

Cartopy integrates cleanly with xarray: an `xr.DataArray.plot(...)` call can draw straight into a map axes. Let's map sea surface temperature (SST) from the NOAA Extended Reconstructed SST dataset, served over OPeNDAP. *(The data-loading and printing parts of this block are runnable locally with the course packages, if you're online; only the plotting needs cartopy.)*

:::{admonition} A gotcha with this NOAA server
:class: warning
As covered in the xarray module: always pass `chunks` (for example `chunks={}`) when opening this PSL THREDDS dataset. Asking the server for the whole array in one request can **silently return all zeros** — with chunks, xarray fetches the data in pieces and the values come back correct.
:::

The following is Python code:

```python
# Integration with xarray
import xarray as xr

url = 'https://psl.noaa.gov/thredds/dodsC/Datasets/noaa.ersst.v5/sst.mnmean.nc'
ds = xr.open_dataset(url, drop_variables=['time_bnds'], chunks={})
sst = ds.sst.sel(time='2000-01-01', method='nearest')
print(sst)
```

End of code.

The following is the expected output:

```
<xarray.DataArray 'sst' (lat: 89, lon: 180)> Size: 64kB
dask.array<getitem, shape=(89, 180), dtype=float32, chunksize=(89, 180), chunktype=numpy.ndarray>
Coordinates:
  * lat      (lat) float32 356B 88.0 86.0 84.0 82.0 ... -82.0 -84.0 -86.0 -88.0
  * lon      (lon) float32 720B 0.0 2.0 4.0 6.0 8.0 ... 352.0 354.0 356.0 358.0
    time     datetime64[ns] 8B 2000-01-01
Attributes:
    long_name:     Monthly Means of Sea Surface Temperature
    units:         degC
    var_desc:      Sea Surface Temperature
    level_desc:    Surface
    statistic:     Mean
    dataset:       NOAA Extended Reconstructed SST V5
    parent_stat:   Individual Values
    actual_range:  [-1.8     42.32636]
    valid_range:   [-1.8 45. ]
    _ChunkSizes:   [  1  89 180]
```

End of output.

How to listen to this printout, top to bottom: the **first line** names the variable (`sst`) and its dimensions — 89 latitudes by 180 longitudes, one 2-degree global grid for the single selected month. The **second line** says the values live in a lazily loaded dask array (because we opened with `chunks`) — nothing is downloaded until we compute or plot. Then **Coordinates**: `lat` runs from 88 down to −88 in 2-degree steps, `lon` from 0 to 358, and `time` is the single date 2000-01-01. Finally **Attributes** carry the metadata — units of degrees Celsius, the dataset name, and its valid range.

Now the map:

The following is Python code:

```python
fig = plt.figure(figsize=(9,6))
ax = plt.axes(projection=ccrs.Robinson())
ax.coastlines()
ax.gridlines()
sst.plot(ax=ax, transform=ccrs.PlateCarree(),
         vmin=2, vmax=30, cbar_kwargs={'shrink': 0.4})
plt.show()
```

End of code.

**What this shows.** A global map in the Robinson projection — an oval-shaped world map with curved meridians, a common choice for global fields — titled "time = 2000-01-01" (xarray wrote the title from the coordinate automatically). The ocean is colored by January-2000 SST on the viridis colormap, clipped to the range 2–30: a broad bright-yellow band of water warmer than about 28 °C wraps the tropics, widest in the western Pacific and Indian Ocean, grading through green (mid-teens) in the mid-latitudes to dark purple (below 5 °C) poleward of roughly 50–60° in both hemispheres. Continents are blank white — SST is undefined over land — outlined by black coastlines, with pale gridlines over the ocean. A vertical colorbar on the right — shrunk by `cbar_kwargs={'shrink': 0.4}` so it stands noticeably shorter than the map, spanning a bit over half the figure's height — is labelled "Monthly Means of Sea Surface Temperature [degC]". Checked against the data: this slice spans −1.8 °C (the freezing point of seawater, at the ice edges) to 30.1 °C; the warm pool at the equator, 160°E reads 29.0 °C, and the North Atlantic at 60°N, 20°W reads 8.7 °C.

Note the key argument `transform=ccrs.PlateCarree()`: it tells cartopy what coordinate system the *data* is in (plain longitude/latitude), so it can be re-projected into the Robinson axes. Getting `projection=` (the axes) and `transform=` (the data) right is most of practical cartopy.

What happens if we strip the decorations away?

The following is Python code:

```python
fig = plt.figure(figsize=(9,6))
ax = plt.axes(projection=ccrs.Robinson())
#ax.coastlines()
#ax.gridlines()
sst.plot(ax=ax, transform=ccrs.PlateCarree(),
         vmin=2, vmax=30) #cbar_kwargs={'shrink': 0.4})
plt.show()
```

End of code.

**What this shows.** The same Robinson SST map with the coastline and gridline calls commented out (and the colorbar now at full height). The striking part: you can still "see" every continent — as blank white holes in the colored field — because the data itself is masked over land. Sometimes the data draws the map for you.

:::{admonition} Reading a map by ear
:class: tip
A 2-D map like this can't be explored with [MAIDR](../sci_python/trying_maidr.md) (which handles line, bar, scatter and histogram plots — like the model time series at the end of this page). For maps, use the 1-D profile trick from the matplotlib lecture: slice out one row or column of the field and print it (or plot it as a line). *(Runnable locally, continuing from the `sst` block above.)*
:::

The following is Python code:

```python
# a pole-to-pole temperature profile along the dateline (180 degrees East)
profile = sst.sel(lon=180.0)
for lat in [60, 40, 20, 0, -20, -40, -60]:
    val = float(profile.sel(lat=lat, method='nearest'))
    print(f"lat {lat}: {round(val, 1)} degC")
```

End of code.

The following is the expected output:

```
lat 60: 0.7 degC
lat 40: 12.0 degC
lat 20: 25.9 degC
lat 0: 26.9 degC
lat -20: 28.0 degC
lat -40: 17.9 degC
lat -60: 4.2 degC
```

End of output.

That's the whole map's story in seven numbers: near-freezing water at 60°N, warmth rising through the tropics (peaking just *south* of the equator — this is January, southern-hemisphere summer), then cooling again toward the Southern Ocean.

## Earth System Model (ESM) Output

ESMs are computer models, which approximately model the earth system. These types of models are used in a variety of contexts in the Earth Science: From idealized experiments to understand meachanisms in the climate systems, to reanalyses and state estimates to synthesize past and current observations, to fully coupled representations of the earth system enabling forecasts of climate conditions under future scenarios.

### What is a General Circulation Model?

A GCM is a computer model that simulates the circulation of a fluid (e.g., the ocean or atmosphere), and is thus one (but important) component of an ESM. It is based on a set of partial differential equations, which describe the motion of a fluid in 3D space, and integrates these forward in time.  Most fundamentally, these models use a discrete representation of the [Navier-Stokes Equations](https://en.wikipedia.org/wiki/Navier%E2%80%93Stokes_equations) but can include more equations to represent e.g., the thermodynamics, chemistry or biology of the coupled earth system.
Depending on the goal these equations are forced with predetermined boundary conditions (e.g. an ocean only simulation is forced by observed atmospheric conditions) or the components of the earth system are coupled together and allowed to influence each other. A 'climate model' is often the latter case which is forced by e.g. different scenarios of greenhouse gas emissions. This enables predictions of how the coupled system will evolve in the future.

### The globe divided into boxes

Since there is no analytical solution to the full Navier-Stokes equation, modern GCMs solve them using numerical methods. They use a discretized version of the equations, which approximates them within a finite volume, or grid-cell. Each GCM splits the ocean or atmosphere into many cells, both in the horizontal and vertical.

*The original page shows a widely used schematic here (credit: Dr. David Viner, Climatic Research Unit, via www.ipcc-data.org). In words: a tilted plane carries a latitude–longitude grid over the British Isles, with horizontal grid spacings labelled between about 1.25 and 3.75 degrees. Out of one grid cell a tall column of stacked boxes rises into the atmosphere, and another column descends into the ocean. Arrows between the boxes are labelled "horizontal exchange between columns of momentum, heat and moisture" and "vertical exchange between layers by diffusion, convection and upwelling". An inset panel lists what is handled inside each box: cloud types, radiatively active gases and aerosols, precipitation, the biosphere, land heat and moisture storage, sea ice, and the surface ocean layers.*

It is numerically favorable to shift (or 'stagger') the grid points where the model calculates the velocity with regard to the grid point where tracer values (temperature, salinity, or others) are calculated. There are several different ways to shift these points, commonly referred to as [Arakawa grids](https://en.wikipedia.org/wiki/Arakawa_grids).

*The original page shows the C-grid diagram from the xgcm documentation. In words: two square grid cells, side by side. In the "horizontal view", the tracer point `t` sits at the center of the cell; the east–west velocity points `u` sit at the middles of the left and right edges; the north–south velocity points `v` at the middles of the top and bottom edges; and vorticity points `f` at the four corners. The "vertical view" is the same idea in the x–z plane: `t` at the center, `u` on the side edges, and vertical-velocity points `w` on the top and bottom faces.*

Most GCMs use a [curvilinear grid](https://en.wikipedia.org/wiki/Curvilinear_coordinates) to avoid infinitely small grid cells at the North Pole. Some examples of curvilinear grids are a tripolar grid (the Arctic region is defined by two poles, placed over landmasses)

*The original page shows a globe here (source: [GEOMAR](https://www.geomar.de)), viewed from above the North Atlantic and Arctic, overlaid with a model mesh. In words: south of a marked circle in the mid-latitudes, the mesh is an ordinary latitude–longitude grid; north of that circle the mesh smoothly deforms so that the meridians converge not at the geographic North Pole but at two poles relocated over land — one over northern Canada, one over Siberia. No singular grid point ends up in the ocean, where the equations would actually have to be solved.*

or a cubed-sphere grid or a lat-lon cap (different versions of connected patches of curvilinear grids).

*The original page shows a globe covered by a mesh in six colored patches (credit: [Gael Forget](http://www.gaelforget.net); more information about the simulation and grid at <https://doi.org/10.5194/gmd-8-3071-2015>). In words: instead of one global latitude–longitude grid, the sphere is tiled by six quadrilateral patches — like the faces of a cube inflated onto the sphere — each carrying its own nearly regular grid: one patch caps the Arctic, and the others cover the Americas, the Atlantic, the Pacific, and so on, joined edge-to-edge along visible seams.*

In most ocean models, due to these 'warped' coordinate systems, the boxes described are not perfectly rectangular [cuboids](https://en.wikipedia.org/wiki/Cuboid). To accurately represent the volume of the cell, we require so-called grid metrics - the distances, cell areas, and volume to calculate operators like e.g., divergence.

If you want to compare models with different grids it can sometimes be necessary to regrid your output. However, some calculations should be performed on the native model grid for maximum accuracy. We will discuss these different cases below.

### Grid resolution

Discretizing the equations has consequences:

- In order to get a realistic representation of the global circulation, the size of grid cells needs to be chosen so that all relevant processes are resolved.  In reality, this usually requires too much computing power for global models and so processes that are too small to be explicitly resolved, like [mesoscale eddies](https://www.gfdl.noaa.gov/ocean-mesoscale-eddies/) or [vertical mixing](https://www.gfdl.noaa.gov/ocean-mixing/), need to be carefully parameterized since they influence the large scale circulation. The following video (watch it at <https://player.vimeo.com/video/259423826>) illustrates the representation of mesoscale eddies in global models of different grid resolution. It shows the surface nutrient fields of three coupled climate models (produced by NOAA/GFDL) around the Antarctic Peninsula with increasing ocean resolution from left to right.

> Nominal model resolution from left to right: 1 degree (CM2.1deg), 0.25 degree (CM2.5) and 0.1 degree (CM2.6). The left ocean model employs a parametrization for both the advective and diffusive effects of mesoscale eddies, while the middle and right model do not.

*In words, for those not watching: the coarse 1-degree model shows smooth, blurry nutrient fields; as resolution increases to 0.25 and then 0.1 degree, the same fields fill with swirling eddies — filaments and vortices tens of kilometres across — that the coarse model can only represent through a parameterization.*

### CMIP - Climate Model Intercomparison Project

Climate modeling has been a cornerstone of understanding our climate and the [consequences of continued emissions of greenhouse gases](https://www.gfdl.noaa.gov/awards/former-noaa-scientist-suki-manabe-shares-nobel-prize-in-physics-for-pioneering-climate-prediction/) for decades. Much of the recent efforts in the community have been focus on model intercomparison projects (MIPs), which invite submissions of many different modeling groups around the world to run their models (which are all set up slightly different) under centralized forcing scenarios. These results can then be analyzed and the spread between different models can give an idea about the certainty of these predictions. The recent [Coupled Model Intercomparison Project Phase 6](https://gmd.copernicus.org/articles/9/1937/2016/) (CMIP6) represents an international effort to represent the state-of-the-art knowledge about how the climate system might evolve in the future and informs much of the [Intergovernmental Panel on Climate Change Report](https://github.com/IPCC-WG1/Chapter-9).

*The original notebook embeds an illustration here that is not included with the course materials; the surrounding text carries the content.*

In this lecture we will learn how to quickly search and analyze CMIP6 data with Pangeo tools in the cloud, a process that using the 'download and analyze' workflow often becomes prohibitively slow and inefficient due to the sheer scale of the data.

The basis for this workflow are the analysis-ready-cloud-optimized repositories of CMIP6 data, which are currently maintained by the pangeo community and publicly available on both [Google Cloud Storage](https://medium.com/pangeo/cmip6-in-the-cloud-five-ways-96b177abe396) and [Amazon S3](https://www.youtube.com/watch?v=C0UhiiGgbWA&t=3267s) as a collection of [zarr](https://zarr.readthedocs.io/en/stable/) stores.

The cloud native approach enables scientific results to be fully reproducible, encouraging to build onto and collaborate on scientific results.

Here we use a Python library called [xmip](https://cmip6-preprocessing.readthedocs.io/en/latest/?badge=latest).

The following is Python code:

```python
from xmip.preprocessing import combined_preprocessing
from xmip.utils import google_cmip_col
from xmip.postprocessing import match_metrics
import matplotlib.pyplot as plt
```

End of code.

The first thing we have to do is to get an overview of all the data available. In this case we are using [intake-esm](https://intake-esm.readthedocs.io/en/stable/index.html) to load a collection of zarr stores on Google Cloud Storage, but there are [other options](https://pangeo-data.github.io/pangeo-cmip6-cloud/accessing_data.html) to access the data too.

Lets create a collection object and look at it

The following is Python code:

```python
col = google_cmip_col()
```

End of code.

In a notebook, displaying `col` produces a rich HTML summary table — one row per search "facet", giving the number of unique values of each. From a script, the accessible way in is the catalog's underlying **pandas DataFrame**, `col.df`: one row per zarr store — over 500,000 of them — and one column per facet (`activity_id`, `institution_id`, `source_id`, `experiment_id`, `member_id`, `table_id`, `variable_id`, `grid_label`, plus the store's cloud address (`zstore`), a `dcpp_init_year` column (used only by decadal-prediction runs), and a `version`). As with any DataFrame, hear its size and columns with:

The following is Python code:

```python
print(col.df.shape)
print(col.df.columns.tolist())
```

End of code.

This object describes all the available data (over 500k single zarr stores!). The facets can be used to search and query subsets of the data. For detailed info on each of the facets please refer to this [document](https://docs.google.com/document/d/1h0r8RZr_f3-8egBMMh7aqLwy3snpD6_MrDz1q8n5XUk/edit).

So obviously we never want to work with *all* the data. Lets check out how to get a subset.

First we need to understand a bit better what all these facets mean and how to see which ones are in the full collection. Lets start with the `experiment_id`: This is the prescribed forcing for a particular MIP that is exactly the same across all different models. We can look at the values of the collection as a pandas dataframe to convieniently list all values. In a notebook you'd display the whole `.unique()` array at once; by ear it's easier to take the count first and then a slice:

The following is Python code:

```python
exps = col.df['experiment_id'].unique()
print(len(exps))
print(list(exps[:10]))
```

End of code.

This prints the number of distinct experiments, then the first ten names. This is the expected output (as of this page's last run — the catalog keeps growing):

The following is the expected output:

```
170
['highresSST-present', 'piControl', 'control-1950', 'hist-1950', 'historical', 'amip', 'abrupt-4xCO2', 'abrupt-2xCO2', 'abrupt-0p5xCO2', '1pctCO2']
```

End of output.

The full list of 170 runs from `highresSST-present` through families like `ssp585`, `ssp245`, `hist-GHG`, `piClim-...`, `dcpp...`, down to `pdSST-pdSICSIT` — print `list(exps)` if you want to hear them all.

The experiments you are probably most interested in are the following:

- `piControl`: In most cases this is the 'spin up' phase of the model, where it is run with a constant forcing (representing pre-industrial greenhouse gas concentrations)
- `historical`: This experiment is run with the observed forcing in the past.
- `ssp***`: A set of ["Shared Socioeconomic Pathways"](https://www.sciencedirect.com/science/article/pii/S0959378016300681) which represent different combined scenarios of future greenhouse gas emissions and other socio-economic indicators ([Explainer](https://www.carbonbrief.org/explainer-how-shared-socioeconomic-pathways-explore-future-climate-change/)). These are a fairly complex topic, but to simplify this for our purposes here we can treat `ssp585` as the 'worst-case' and `ssp245` as 'middle-of-the-road'.

You can explore the available models (`source_id`) in the same way as above:

The following is Python code:

```python
models = col.df['source_id'].unique()
print(len(models))
print(list(models[:10]))
```

End of code.

The following is the expected output:

```
88
['CMCC-CM2-HR4', 'EC-Earth3P-HR', 'HadGEM3-GC31-MM', 'HadGEM3-GC31-HM', 'HadGEM3-GC31-LM', 'EC-Earth3P', 'ECMWF-IFS-HR', 'ECMWF-IFS-LR', 'HadGEM3-GC31-LL', 'CMCC-CM2-VHR4']
```

End of output.

Eighty-eight models, from dozens of modeling centers worldwide — GFDL, IPSL, MPI, CESM, UKESM, MIROC, and many more.

Now you want to decide what variable (`variable_id`) you want to look at and in conjunction what time frequency of output (`table_id`) is available. This handy [spreadsheet](https://docs.google.com/spreadsheets/d/1UUtoz6Ofyjlpx5LdqhKcwHFz2SGoTQV2_yekHyMfL9Y/edit#gid=1221485271) can help you find the value for `variable_id` and the available time frequencies (`table_id`).

Lets look at an example for monthly (`'table_id'='Omon'`) sea surface temperature (`'variable_id'='tos'`) for the `historical` experiment:

The following is Python code:

```python
cat = col.search(
    variable_id='tos', # ocean surface temperature
    table_id='Omon',
    experiment_id='historical',
    source_id=['GFDL-ESM4'],
)
```

End of code.

(In a notebook, displaying `cat` shows another HTML summary — the same kind of catalog object, now filtered down to just the GFDL-ESM4 historical monthly SST holdings.)

The last facet we need to discuss is `member_id` and `grid_label`:
You can see here that we have 3 `member_id` values for the given query.

The following is Python code:

```python
print(list(cat.df['member_id'].unique()))
```

End of code.

The following is the expected output:

```
['r3i1p1f1', 'r2i1p1f1', 'r1i1p1f1']
```

End of output.

This indicates that for this particular model/experiment 3 ensemble members were run with varing realizations/initial_condiditions/physics/forcing or a combination thereof. The naming is explained in [this](https://docs.google.com/document/d/1h0r8RZr_f3-8egBMMh7aqLwy3snpD6_MrDz1q8n5XUk/edit) document:

> Example of a variant_label:  if realization_index=2, initialization_index=1, physics_index=3, and forcing_index=233, then variant_label = "r2i1p3f233".

There can be many more members for some particular models, which provides an opportunity to investigate internal variability of the climate system as compared to forced signals.

Finally there are two values of `grid_label`

The following is Python code:

```python
print(list(cat.df['grid_label'].unique()))
```

End of code.

The following is the expected output:

```
['gr', 'gn']
```

End of output.

The value `gn` always stands for the native model grid (which can be quite complex but preserves the most detail) and `gr` indicates data that has been regridded on regular lon/lat intervals.

Now that you know how to query and subset the collection, it is time to actually load in some model data as xarray datasets!

The following is Python code:

```python
# create a smaller catalog from the full collection using faceted search
cat = col.search(
    variable_id='tos', # Ocean surface temperature
    experiment_id='historical', # only runs for the historical forcing period
    table_id='Omon', # monthly oceanic data
    grid_label='gn', #native model grid only
    source_id=['IPSL-CM6A-LR', 'MRI-ESM2-0', 'GFDL-ESM4'], # only choosing a few models here, there are many more!
    member_id=['r1i1p1f1', 'r2i1p1f1', 'r3i1p1f1'], #lets restrict us here to only a few members, you can modify this later to experiment.
)

# read all datasets into a dictionary but apply the xmip preprocessing before
ddict = cat.to_dataset_dict(
    preprocess=combined_preprocessing,
    xarray_open_kwargs={'use_cftime':True},
)
```

End of code.

This prints a note about how the dictionary keys are built (plus, in a notebook, a progress bar):

The following is the expected output:

```

--> The keys in the returned dictionary of datasets are constructed as follows:
    'activity_id.institution_id.source_id.experiment_id.table_id.grid_label'

```

End of output.

You will also hear several `UserWarning` lines from xmip about the MRI model, of the form "While renaming to target `lon_bounds`, more than one candidate was found ['x_bnds', 'vertices_longitude']. … Please double check results." — that is xmip's data-cleaning at work on inconsistently named variables, and is expected here.

Here we did two things:

- We searched the full collection based on a combination of facets like the variable, the experiment and only a test set of models.
- We loaded the datasets into a dictionary of xarray datasets (note they are not loaded into memory, but instead these are lazyly loaded dask arrays -- more on this in the following lectures).

The following is Python code:

```python
print(list(ddict.keys()))
```

End of code.

The following is the expected output:

```
['CMIP.NOAA-GFDL.GFDL-ESM4.historical.Omon.gn', 'CMIP.IPSL.IPSL-CM6A-LR.historical.Omon.gn', 'CMIP.MRI.MRI-ESM2-0.historical.Omon.gn']
```

End of output.

Let's print one of the three datasets in full — this is the richest xarray object in the course so far, so it's worth a guided listen:

The following is Python code:

```python
print(ddict['CMIP.IPSL.IPSL-CM6A-LR.historical.Omon.gn'])
```

End of code.

The following is the expected output:

```
<xarray.Dataset> Size: 3GB
Dimensions:         (member_id: 3, dcpp_init_year: 1, time: 1980, y: 332,
                     x: 362, vertex: 4, bnds: 2)
Coordinates: (12/13)
    area            (y, x) float32 481kB dask.array<chunksize=(332, 362), meta=np.ndarray>
    lat             (y, x) float32 481kB dask.array<chunksize=(332, 362), meta=np.ndarray>
    lon             (y, x) float32 481kB dask.array<chunksize=(332, 362), meta=np.ndarray>
  * time            (time) object 16kB 1850-01-16 12:00:00 ... 2014-12-16 12:...
    lon_verticies   (y, x, vertex) float32 2MB dask.array<chunksize=(332, 362, 4), meta=np.ndarray>
    lat_verticies   (y, x, vertex) float32 2MB dask.array<chunksize=(332, 362, 4), meta=np.ndarray>
    ...              ...
  * y               (y) int64 3kB 0 1 2 3 4 5 6 ... 325 326 327 328 329 330 331
  * x               (x) int64 3kB 0 1 2 3 4 5 6 ... 355 356 357 358 359 360 361
    lon_bounds      (bnds, y, x) float32 961kB dask.array<chunksize=(1, 332, 362), meta=np.ndarray>
    lat_bounds      (bnds, y, x) float32 961kB dask.array<chunksize=(1, 332, 362), meta=np.ndarray>
  * member_id       (member_id) object 24B 'r1i1p1f1' 'r2i1p1f1' 'r3i1p1f1'
  * dcpp_init_year  (dcpp_init_year) float64 8B nan
Dimensions without coordinates: vertex, bnds
Data variables:
    tos             (member_id, dcpp_init_year, time, y, x) float32 3GB dask.array<chunksize=(1, 1, 251, 332, 362), meta=np.ndarray>
Attributes: (12/51)
    CMIP6_CV_version:                 cv=6.2.3.5-2-g63b123e
    Conventions:                      CF-1.7 CMIP-6.2
    EXPID:                            historical
    NCO:                              "4.6.0"
    activity_id:                      CMIP
    branch_method:                    standard
    ...                               ...
    intake_esm_attrs:variable_id:     tos
    intake_esm_attrs:grid_label:      gn
    intake_esm_attrs:version:         20180803
    intake_esm_attrs:_data_format_:   zarr
    variant_info:                     Restart from another point in piControl...
    intake_esm_dataset_key:           CMIP.IPSL.IPSL-CM6A-LR.historical.Omon.gn
```

End of output.

Walking through it by ear: the **first line** says the whole dataset is 3 gigabytes — which is exactly why it stays lazy. The **Dimensions** line: 3 ensemble members, 1980 monthly time steps (1850 through 2014, i.e. 165 years times 12), and a native ocean grid of 332 by 362 points indexed by plain integers `y` and `x` — *not* by latitude and longitude, because this is a curvilinear grid: the real `lat` and `lon` are 2-D coordinate fields of shape `(y, x)`, listed under **Coordinates** along with cell areas and cell-corner (`verticies`, `bounds`) arrays. There is exactly **one data variable**, `tos` (sea surface temperature), a 5-dimensional dask array. The 51 **Attributes** carry CMIP metadata — model, experiment, conventions, and the intake-esm bookkeeping. Notice everything says `dask.array` with chunk sizes: nothing has been downloaded yet.

You can see that both datasets have the same names for many of the coordinates (e.g. 'x' and 'y' for the logical indices in zonal and meridional direction). This is actually not always the case for the raw CMIP6 data, which is why [xMIP](https://github.com/jbusecke/xMIP) was developed in an effort to crowdsource these common data-cleaning tasks. For this example we only use the `combined_preprocessing` function which fixes some of the naming, but check out the [docs](https://cmip6-preprocessing.readthedocs.io/en/latest/?badge=latest) to see more helpful code for CMIP analysis.

Ok but now lets analyze the data! Using what we know about xarray we can get a timeseries of the global sea surface temperature:

The following is Python code:

```python
for name, ds in ddict.items():
    # construct yearly global mean timeseries
    mean_temp = ds.tos.mean(['x', 'y']).coarsen(time=12).mean()
    plt.figure()
    mean_temp.plot(hue='member_id')
    # lets also plot the average over all members
    mean_temp.mean('member_id').plot(color='k', linewidth=2)
    plt.title(ds.attrs['source_id']) #Extract the model name right from the dataset metadata
    plt.show()
```

End of code.

**What this shows.** Three separate figures, one per model, all with the same layout: the x-axis is time from 1850 to 2014; the y-axis is `tos`, the (unweighted) global-mean sea surface temperature in degrees Celsius; three thin colored lines — blue, orange, green for members `r1i1p1f1`, `r2i1p1f1`, `r3i1p1f1`, named in a legend titled `member_id` — jitter within roughly 0.1–0.2 °C of one another, and a thick black line (the average over the members) cuts through the middle. Model by model:

- **GFDL-ESM4** (first figure): y-axis from about 11.8 to 12.85. The black mean hovers near 12.0–12.3 °C from 1850 to about 1970, with brief sharp dips near 1885 and the mid-1960s, then climbs steeply after about 1980 to roughly 12.65 °C by 2014.
- **IPSL-CM6A-LR** (second figure): y-axis from about 13.0 to 14.3. The mean rises gradually from about 13.35 °C to 13.7 °C by the 1970s, then steeply to about 14.1 °C by 2014. (Small metadata quirk you can hear: this model's x-axis is labelled "Time axis" rather than "time" — the label comes from the time coordinate's attributes, which differ between modeling centers.)
- **MRI-ESM2-0** (third figure): y-axis from about 14.8 to 15.7. The mean stays nearly flat around 15.0–15.1 °C until roughly 1930, rises slowly, then accelerates after about 1975 to roughly 15.6 °C by 2014.

Two things to take away. Each model has its own *absolute* temperature — the three curves sit whole degrees apart (about 12, 13.5, and 15 °C), which is normal for unweighted means over different native grids. Yet all three tell the same story: roughly flat through the nineteenth century, then warming that accelerates sharply after about 1975. The colored members disagree in any individual year (that spread is internal variability — the model's "weather"), while sharing the forced long-term trend; the brief cold dips that several models show around 1885 and the early 1960s follow major volcanic eruptions, which are part of the historical forcing. These are ordinary line plots, so you can also explore them point-by-point with [MAIDR](../sci_python/trying_maidr.md).

The above result is roughly correct, but as discussed in earlier lectures we need to do proper area weighting to ensure that different parts of the globe are weighted properly. You can look at the [original notebook](https://earth-env-data-science.github.io/lectures/models/cmip.html) to see how to do things correctly.

## Other ideas to explore:

- ESMs are not always on simple or similar grids:
    - For intercomparison, the most common choice is to regrid everything to a common grid. There is a package called [xesmf](https://xesmf.readthedocs.io/en/latest/) for this. See [this example](https://earth-env-data-science.github.io/lectures/models/regridding.html).
    - Sometimes one needs to work with the native grid, and there is a package called [xgcm](https://xgcm.readthedocs.io/en/latest/index.html) for this. See [this example](https://earth-env-data-science.github.io/lectures/models/xgcm.html).
- Datasets can sometimes (often) get quite big, making it challenging even for the largest computers to handle their analysis. One solution to this is to use multiple computers in parallel. There are a few different Python frameworks to do this, where [dask](https://docs.dask.org/en/latest/) is probably the most adopted framework at this point. See [this example](https://earth-env-data-science.github.io/lectures/dask/intro.html). One advantage to dask is that [integrates natively with xarray](https://docs.xarray.dev/en/stable/user-guide/dask.html), and with some forethought much of the parallelization can take place automatically.
