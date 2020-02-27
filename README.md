# Gentrification and demographic analysis — 2000 to 2017

This repository contains data, analytic code, and findings that support portions of the BuzzFeed News article, “These 11 Maps Show How Black People Have Been Driven Out Of Neighborhoods In Five Of The Most Gentrified US Cities,” published February 27, 2020. Please read that article, which contains important context and details, before proceeding.

## Data

The data used in this analysis come from three sources: US Census Bureau, [censusreporter.org](https://censusreporter.org/), and *Logan et al.*’s [Longitudinal Tract Data Base](https://s4.ad.brown.edu/Projects/Diversity/Researcher/Bridging.htm).

### Data from the US Census Bureau

The analysis uses two Census datasets, described below.

#### American Community Survey results for 2013–2017

The analysis uses data from the American Community Survey’s 2013–2017 estimates, the most recent five-year demographic estimates available from the Census Bureau.

To obtain this data, BuzzFeed News downloaded it from the [Census’s API](https://www.census.gov/developers/). (The Python code used to do so can be found in this repository’s [`01-download-census-data.ipynb` notebook](notebooks/01-download-census-data.ipynb).)

BuzzFeed News downloaded the data for every tract in in every county in the following Metropolitan Statistical Areas (MSA):

- Atlanta-Sandy Springs-Alpharetta, GA (for Atlanta)
- Baltimore-Columbia-Towson, MD (for Baltimore)
- New York-Newark-Jersey City, NY-NJ-PA (for New York City)
- San Francisco-Oakland-Berkeley, CA (for Oakland)
- Washington-Arlington-Alexandria, DC-VA-MD-WV (for Washington, D.C.)

The results can be found in [`output/census_tracts.csv`](output/census_tracts.csv). For each tract in the MSA, that dataset includes the following variables:

* `geoid` — Census tract ID
* `total_population` — The tract’s total population
* `total_population_25_over` — The tract’s population of people age 25 or older
* `median_income` — Median income (1,000,001 is the upper limit for this column)
* `median_home_value` — Median home value (1,000,001 is the upper limit for this column)
* `educational_attainment` — The number people who are 25 or older and have the equivalent of a 4-year college degree
* `white_alone` — The number of people whose race is white alone, and are not Hispanic
* `black_alone` — The number people who are black or African American alone, and are not Hispanic
* `native_alone` — The number of people who are American Indian and Alaska Native alone
* `asian_alone` — The number of people who are Asian alone
* `native_hawaiian_pacific_islander` — The number of people who are Native Hawaiian and Other Pacific Islander alone
* `some_other_race_alone` — The number of people who are some other race alone
* `two_or_more` — The number of people who are two or more races
* `hispanic_or_latino` — The number of people who are Hispanic or Latino

#### Census tract shapefiles

BuzzFeed News also downloaded shapefiles detailing the geographic boundaries and Census tracts for California, Georgia, Maryland, New York, and the District of Columbia [from the Census Bureau’s website](https://www.census.gov/geographies/mapping-files/time-series/geo/carto-boundary-file.2018.html). These files have been saved in `data/censusTracts/states/`.

### Data from censusreporter.org

BuzzFeed News used [Census Reporter](https://censusreporter.org/) to obtain a list of Census tracts that intersect with the official Census boundaries of the five cities to be analyzed:

- [Atlanta](https://censusreporter.org/data/table/?table=B03002&geo_ids=140|16000US1304000)
- [Baltimore](https://censusreporter.org/data/table/?table=B03002&geo_ids=140|05000US24510)
- [New York](https://censusreporter.org/data/table/?table=B03002&geo_ids=140|16000US3651000)
- [Oakland](https://censusreporter.org/data/table/?table=B03002&geo_ids=140|16000US0653000)
- [Washington, DC](https://censusreporter.org/data/table/?table=B03002&geo_ids=140|16000US1150000)

These files have been saved in `data/city_tracts/`.

### Data from *Logan et al.*’s Longitudinal Tract Data Base

Every decade, the Census updates some of its tract boundaries, based on population increases and decreases. To make tract-level Census data from the 2000s and 2010s comparable, *Logan et al.* have created the [`Longitudinal Tract Data Base`](https://s4.ad.brown.edu/Projects/Diversity/Researcher/Bridging.htm) (LTDB). BuzzFeed News used this dataset to obtain demographic estimates for the year 2000, and to link them to the data for the tract-level data from the 2013-2017 American Community Survey.

Due to republishing limitations, the LTDB files are not included in this repository, but can be downloaded in full from the project’s [website](https://s4.ad.brown.edu/Projects/Diversity/Researcher/LTBDDload/Default.aspx). To replicate the analysis, follow these steps:

- Open [the download page](https://s4.ad.brown.edu/Projects/Diversity/Researcher/LTBDDload/Default.aspx) in your web browser
- At the prompt, enter your email address and agree to the listed restrictions; press "Continue"
- In the first set of dropdowns, choose `Select a file type: Full` and `Select a year: 2000`; press "Download Standard Data Files"
- Unzip the downloaded file, and then move the `LTDB_Std_2000_fullcount.csv` file directly into this repository’s `data` folder
- In the same set of dropdowns, change `Select a file type: Full` to `Select a file type: Sample`; press "Download Standard Data Files" again
- Move the downloaded `LTDB_Std_2000_Sample.csv` file directly into this repository’s `data` folder

The LTDB’s data dictionary can be found [here](https://s4.ad.brown.edu/Projects/Diversity/Researcher/LTBDDload/Dfiles/codebooks.pdf).


## Gentrification Methodology

BuzzFeed News’ analysis uses [a methodology devised by Governing Magazine](http://www.governing.com/gov-data/gentrification-report-methodology.html) (which in turn is similar to the definition from a [Columbia University study](http://uar.sagepub.com/content/40/4/463.abstract)). The methodology focuses on a median income, median home value, and educational attainment metrics.

The methodology is comprised of the following two tests, as described by Governing Magazine:

##### Test 1: Does the tract qualify for gentrification?

A tract qualifies for potential gentrification if it meets *all three* of following criteria at the beginning of the study period (in this case, the year 2000):

- Had a population of at least 500 residents and was located within a central city*
- Its median household income was in the bottom 40th percentile when compared to all tracts within its metro area
- Its median home value was in the bottom 40th percentile when compared to all tracts within its metro area

* The city must also still have at least 500 residents at the end of the study period.

##### Test 2: Has it gentrified?

A tract is considered to have gentrified if it passes the test above, and also if it meets these three additional criteria at the end of the study period (in this case, in the 2013-2017 ACS survey results):

- Its median home value increased when adjusted for inflation
- Its increase inflation-adjusted median home value was in the top third of all tracts within a metro area
- Its increase in educational attainment (as measured by the percentage of residents age 25 who hold bachelor’s degrees) was in the top third of all tracts within a metro area

## Data analysis

The data analysis was performed in the following Jupyter notebook, using the Python programming language.

### 02-gentrification_measure_and_race_changes_2017.ipynb

The Python code for BuzzFeed News analysis, implementing the methodology above, can be found in the [`02-analyze-gentrification-and-demographic-changes.ipynb` notebook](notebooks/02-analyze-gentrification-and-demographic-changes.ipynb). The notebook additionally calculates percentage-point changes for six non-overlapping race/ethnicity groups.

The notebook produces the following files:
* [`output/census_data_metro.csv`](output/census_data_metro.csv) — a merged spreadsheet of Census data for all metro areas in 2000 and 2017.
* [`output/gentrification.csv`](output/gentrification.csv) — a spreadsheet, covering all Census tracts for the cities of interest (Atlanta, Baltimore, New York, Oakland and Washington, DC), with the following columns:
  * `GEOID` — Census tract ID
  * `total_population_17` — The tract’s total population in 2017
  * `gentrified` — Whether the tract gentrified between 2000 and 2017
  * `low_population` — Whether the tract’s population was too low to qualify for gentrification
  * `eligible_for_gentrification` — Whether a tract was eligible for gentrification, based on Test 1 above
  * `pct_white_alone_change` — Percentage-point change for population that was white alone
  * `pct_black_alone_change` — Percentage-point change for population that was black alone
  * `pct_native_alone_change` — Percentage-point change for population that was Native American
  * `pct_asian_alone_change` — Percentage-point change for population that was Asian alone
  * `pct_hispanic_or_latino_alone_change` — Percentage-point change for population that was Hispanic or Latino alone
  * `pct_native_hawaiian_pacific_islander_change` — Percentage-point change for population that was Native Hawaiian or Pacific Islander

* `for_maps/`, a directory that contains CSV and GeoJSON related to the maps displayed with the story, with the following variables:

  Geographic information:
  * `GEOID` - the Census tract’s official ID
  * `name` - the name of the Census tract
  * `city` - the name of the city
  * `INTPTLAT`, `INTPTLON`, `geometry` - information from the shape files

  Person-counts, overall and by race/ethnicity:
  * `total_population_17`
  * `white_alone_17`
  * `black_alone_17`
  * `asian_alone_17`
  * `hispanic_or_latino_17`

  Analysis findings (among others):
  * `gentrified` — Whether the tract gentrified, according to the two tests described above
  * `pct_white_alone_change` — Percentage-point change for population that was white alone
  * `pct_black_alone_change` — Percentage-point change for population that was black alone
  * `pct_native_alone_change` — Percentage-point change for population that was Native American
  * `pct_asian_alone_change` — Percentage-point change for population that was Asian alone
  * `pct_hispanic_or_latino_alone_change` — Percentage-point change for population that was Hispanic or Latino alone

## Licensing

All code in this repository is available under the [MIT License](https://opensource.org/licenses/MIT). All data files in the output/ directory are available under the [Creative Commons Attribution 4.0 International](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0) license. All data files in the data/ directory are available, under their own terms, from the sources described above.

## Feedback / Questions?

Contact Lam Thuy Vo at lam.vo@buzzfeed.com and [Lo Bénichou](https://twitter.com/lobenichou?lang=en) from [Mapbox](https://www.mapbox.com/?utm_source=buzzfeedcollab&utm_medium=buzzfeed&utm_content=main-page&utm_campaign=narrativescollab) at lo.benichou@mapbox.com.

Looking for more from BuzzFeed News? [Click here for a list of our open-sourced projects, data, and code.](https://github.com/buzzfeednews/)
