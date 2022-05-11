
<!-- README.md is generated from README.Rmd. Please edit that file -->

# osmexport

<!-- badges: start -->

[![DOI](https://zenodo.org/badge/489214893.svg)](https://zenodo.org/badge/latestdoi/489214893)
<!-- badges: end -->

**osmexport** is an R package useful to download, read, prepare and
(rudimentarily) visualise OpenStreetMap (OSM) exports like the `map.osm`
file you would get from the [OSM website’s “Export”
page](https://www.openstreetmap.org/export).

This package might not be what you’re after: please see below the
section “Other OSM-related R packages” to find more powerful tools.

## Installation

You can install the development version of osmexport from
[GitHub](https://github.com/) with:

``` r
# install.packages("devtools")
devtools::install_github("stragu/osmexport")
```

## Available functions

-   `oexp_download()`: download an OSM export by defining its bounding
    box, save it to file.
-   `oexp_read()`: read a `.osm` file as an object of class `osm`
-   `oexp_separate_tags()`: separate the tags contained in the
    `other_tags` column. This is done by default when importing the data
    with `oexp_read()`

## The `osm` class

The `osm` S3 class – very much subject to changing in name and
properties – has `print()` and `plot()` methods. It is made of
[`sf`](https://r-spatial.github.io/sf/) (“simple features”) spatial
objects.

## Example

The functions available are designed to be pipeable. For example, to
download and read the area around [Te Kura Tatauranga, Waipapa Taumata
Rau](https://www.auckland.ac.nz/en/science/about-the-faculty/department-of-statistics.html):

``` r
library(osmexport)
#> All data, downloaded or included as an example, is © OpenStreetMap contributors, and the conditions of its reuse are defined by the ODbL licence.
#> Find out more on the OSM website: https://www.openstreetmap.org/copyright
TKT <- c(174.76598, -36.85440, 174.77019, -36.85129) |>
  oexp_download() |>
  oexp_read()
# see what the object contains
TKT
#> OSM data object made of 5 simple feature collections: points, lines, multilinestrings, multipolygons, other_relations
#> The corresponding sf geometry types are:
#>             points              lines   multilinestrings      multipolygons 
#>              POINT         LINESTRING    MULTILINESTRING       MULTIPOLYGON 
#>    other_relations 
#> GEOMETRYCOLLECTION
```

We can now use the default plot method to have a glimpse at the data:

``` r
plot(TKT)
```

<img src="man/figures/README-plot-1.png" width="100%" />

Because the parts of the object are of class `sf`, they can be processed
with sf and well as dplyr functions:

``` r
library(dplyr)
library(sf)
# most common "building" values in polygons
TKT$multipolygons |>
  st_drop_geometry() |>         # remove geometry column
  filter(!is.na(building)) |>   # only keep buildings
  count(building, sort = TRUE)  # most commons values at the top
#>      building  n
#> 1  university 31
#> 2         yes  6
#> 3        roof  5
#> 4  apartments  2
#> 5      church  1
#> 6       civic  1
#> 7     garages  1
#> 8      gazebo  1
#> 9      retail  1
#> 10      ruins  1
```

## Contributions and feedback

Contributions are welcome and appreciated. You can contribute to this
package by:

-   Testing it and [reporting
    issues](https://github.com/stragu/osmexport/issues) you encounter
-   Suggesting a change with a pull request (but please discuss your
    idea in an issue beforehand)
-   Writing a new vignette with an interesting worked example

Note that all contributions to the codebase will be released under the
[GPL](LICENSE.md), and submitting your contribution to this repository
is an implicit agreement.

We expect contributors to respect UQRUG’s [Code of
Conduct](https://gitlab.com/stragu/uqrug/blob/master/Code_of_Conduct.md).

If a conversation or comment does not belong in a public issue report,
please contact the maintainer listed in the [package
description](DESCRIPTION).

## Data licence

OSM data included in this package (in the `./inst` directory) and
downloaded with the `oexp_download()` function is © OpenStreetMap
contributors, and the conditions of its reuse are defined by the [ODbL
licence](https://opendatacommons.org/licenses/odbl/). You likely need to
include this information on anything derived from it. Find out more on
the [OSM website](https://www.openstreetmap.org/copyright).

## Other OSM-related R packages

There are many other OSM-related R packages that might be more suitable
for what you are hoping to achieve. Notable packages available on CRAN
are:

-   [osmadata](https://cran.r-project.org/web/packages/osmdata) uses the
    Overpass API to download datasets based on location and tags. Use
    this for a more targeted download.
-   [osmextract](https://cran.r-project.org/web/packages/osmextract)
    uses different providers to download chunks of large data dumps, for
    specific regions and dates. Some providers offer data already
    prepared for particular uses (e.g. data relevant to cycling).
-   [osmplotr](https://cran.r-project.org/web/packages/osmplotr)
    downloads OSM data with the Overpass API and provides many tools to
    render the data.
-   [osmar](https://cran.r-project.org/web/packages/osmar/index.html)
    accesses OSM data from different sources and converts it to various
    formats. Note that it hasn’t been updated since 2013.

Transport-specific packages:

-   [stplanr](https://cran.r-project.org/web/packages/stplanr) provides
    tools for sustainable transport planning, making use of the osrm
    package.
-   [osrm](https://cran.r-project.org/web/packages/osrm) bridges R with
    the OSRM API (routing service based on OpenStreetMap data).
-   [opentripplanner](https://docs.ropensci.org/opentripplanner/)
    provides an interface to OpenTripPlanner (OTP), a routing service
    that relies on OpenStreetMap data.

To acquire basemaps:

-   [OpenStreetMap](https://cran.r-project.org/web/packages/OpenStreetMap)
    allows downloading rendered OSM data from various servers.
-   [OSMscale](https://cran.r-project.org/web/packages/osmplotr) plots
    spatial data with OSM basemaps and corresponding scale bars.

Other interactive visualisation packages automatically fetch rendered
OSM data as basemaps, like [leaflet](https://rstudio.github.io/leaflet/)
and [tmap](https://r-tmap.github.io/tmap/).
