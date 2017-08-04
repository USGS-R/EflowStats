# `EflowStats`

Calculates a suite of ecological flow statistics and fundamental properties of daily streamflow for a given set of data. 

|Linux|Windows| Test Coverage | USGS Status |
|----------|------------|------------|------------|
| [![travis](https://travis-ci.org/USGS-R/EflowStats.svg?branch=master)](https://travis-ci.org/USGS-R/EflowStats) |
| [![AppVeyor Build Status](https://ci.appveyor.com/api/projects/status/github/USGS-R/EflowStats?branch=master&svg=true)](https://ci.appveyor.com/project/USGS-R/EflowStats) |
| Code Coverage: [![Coverage Status](https://coveralls.io/repos/github/USGS-R/EflowStats/badge.svg?branch=master)](https://coveralls.io/github/USGS-R/EflowStats?branch=master) |
| [![status](https://img.shields.io/badge/USGS-Research-blue.svg)](https://owi.usgs.gov/R/packages.html#research) |

## !!!NOTICE!!!
There have been major revisions to `EflowStats` since version 4.1.1!. [Review the latest [vignette](https://cdn.rawgit.com/USGS-R/EflowStats/9507f714/inst/doc/intro.html) for how to use the package and a brief description of changes [in this vignette.]((https://cdn.rawgit.com/USGS-R/EflowStats/9507f714/inst/doc/packageDiscrepencies.html))

### Code of Conduct

We want to encourage a warm, welcoming, and safe environment for contributing to this project. See the [code of conduct](https://github.com/USGS-R/EflowStats/blob/master/CONDUCT.md) for more information.

### Reporting bugs

Please consider reporting bugs and asking questions on the [Issues page.](https://github.com/USGS-R/EGRET/issues)

### Package Support

The Water Mission Area of the USGS has supported the development and maintenance of the `EflowStats` R-package. Further maintenance is expected to be stable until further notice. Resources are available primarily for maintenance and responding to user questions. Priorities on the development of new features are determined by the `EflowStats` development team.

![USGS](http://usgs-r.github.io/images/usgs.png)

### Overview

The `EflowStats` package was created to simplify the process of generating hydrologic indicator statistics using daily streamflow records. It has been specifically designed to work seamlessly with U.S. Geological Survey (USGS) National Water Information System (NWIS) data. This package is intended to be an update of the previously existing USGS National Hydrologic Assessment Tool (NAHAT) program with additional statistics previously published by Archfield et al (Archfield).


Installation
----------
To install this package use the following code:

```r
devtools::install_github("USGS-R/EflowStats")
```


Summary of bugs and updates
---------------------------
There are a number of bugs and/or miscalulated statistics in the original EflowStats R package, which have been corrected here. Below is a table summary of indices that have been identified as being problematic in the original package.



|Indices                               |Definition                                                                                                                                                                                                                                                           |Original.method                                                                                                                                                                                               |New.method                                                                                                                                                                                           |Issue.description                                                                                                                                                                                                                                                                                                                                                                                                                                          |
|:-------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|ma3                                   |Mean (or median - Use Preference option) of the coefficients of variation (standard deviation/mean) for each year. Compute the coefficient of variation for each year of daily flows. Compute the mean of the annual coefficients of variation (percent - temporal). |Calculate CV as the standard deviation of flow for each year divided by the mean flow for the entire period of record                                                                                         |Calculate CV as the standard deviation of flow for each year divided by the mean flow for each year                                                                                                  |Definition states to calculate the CV on a yearly basis. Original method did not calculate CV correctly given this definition                                                                                                                                                                                                                                                                                                                              |
|ma12-23; ml14, ml21; mh16; dl6        |See documentation.                                                                                                                                                                                                                                                   |No change                                                                                                                                                                                                     |No change                                                                                                                                                                                            |Minor differences in numerical rounding methods                                                                                                                                                                                                                                                                                                                                                                                                            |
|ma24-35                               |See documentation.                                                                                                                                                                                                                                                   |Round to 0 places always                                                                                                                                                                                      |Round to user specified decimal places                                                                                                                                                               |Bug in rounding in original method where always rounded to 0 decimal places                                                                                                                                                                                                                                                                                                                                                                                |
|dl16-17; dh15-16, dh22-24; ra8-9      |See documentation                                                                                                                                                                                                                                                    |Flow events counted for each year. An event at the start of the year is counted while an event that runs to the end of the year is dropped. Dropping the year at the end is likely not intended and is a bug. |Retain events at start AND end of the record and count their duration even though no true duration can be determined. This will always be an issue as long as stats are calculated on a yearly basis |No true duration can be determined for events that did not start or end in the year. However, the bigger issue is dropping the last event in the original method. In particular, If there is only 1 event that spans the entire year, for example there were no flood events and all year is below that threshold, then the entire year gets a 0 event duration because it is dropped as the last event. This introduces a SERIOUS error in the statistic. |
|dh2-5, dh7-10, dh12-13; dl2-5, dl7-10 |See documentation.                                                                                                                                                                                                                                                   |Rolling means calculated on yearly basis                                                                                                                                                                      |Rolling means calculated for entire period of record before yearly subset                                                                                                                            |Edge effects in calculating rolling means are compounded by splitting the data on a yearly basis before calculating the rolling means. This introduces significant error into the calculations, particularly for 90 day rolling means                                                                                                                                                                                                                      |
|dh17-21                               |See documentation                                                                                                                                                                                                                                                    |Calculates average durations on a yearly basis                                                                                                                                                                |Calculates average duration for entire period of record (matches definition)                                                                                                                         |Average durations are calculated for each year in original method, and then those are averaged to get the final number. This introduces both edge effect issues and also it does not match the definition where the average for the entire period is to be computed                                                                                                                                                                                        |
|ta1-2                                 |See documentation. Relies on Colwell matrix calculation, which omits February 29th on leap years                                                                                                                                                                     |Omits day 366, NOT February 29th                                                                                                                                                                              |Appropriately handles leap years and omits February 29th from Colwell matrix                                                                                                                         |Data for day 366 is omitted from the Colwell matrix calculation instead of the proper day, February 29th on leap years                                                                                                                                                                                                                                                                                                                                     |
|tl1-4                                 |See documentation                                                                                                                                                                                                                                                    |Does not handle leap years                                                                                                                                                                                    |Appropriately handles leap years                                                                                                                                                                     |                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
|ra4                                   |Variability in fall rate. Compute the standard deviation for the negative flow changes. RA4 is 100 times the standard deviation divided by the mean (percent – spatial).                                                                                             |Rounds mean to 2 decimal places and then rounds again to 2 decimal places when returning the statistic                                                                                                        |Only rounds the final number for the statistic returned                                                                                                                                              |Don't round calculated numbers until the end to reduce numerical rounding errors                                                                                                                                                                                                                                                                                                                                                                           |


References
----------

Henriksen, J.A., Heasley, J. Kennen, J.G., and Nieswand, S., 2006, Users' manual for the Hydroecological Integrity Assessment Process software (including the New Jersey Assessment Tools): U.S. Geological Survey Open-File Report 2006-1093. 72 p. ([http://www.fort.usgs.gov/products/publications/21598/21598.pdf](http://www.fort.usgs.gov/products/publications/21598/21598.pdf))

Archfield, S.A., J.G. Kennen, D.M. Carlisle, and D.M. Wolock. 2013. An Objective and Parsimonious Approach for Classifying Natural Flow Regimes at a Continental Scale. River Res. Applic. doi: 10.1002/rra.2710 ([http://onlinelibrary.wiley.com/doi/10.1002/rra.2710/abstract](http://onlinelibrary.wiley.com/doi/10.1002/rra.2710/abstract))

Disclaimer
----------
This software is in the public domain because it contains materials that originally came from the U.S. Geological Survey, an agency of the United States Department of Interior. For more information, see the official USGS copyright policy at [http://www.usgs.gov/visual-id/credit_usgs.html#copyright](http://www.usgs.gov/visual-id/credit_usgs.html#copyright)


Although this software program has been used by the U.S. Geological Survey (USGS), no warranty, expressed or implied, is made by the USGS or the U.S. Government as to the accuracy and functioning of the program and related program material nor shall the fact of distribution constitute any such warranty, and no responsibility is assumed by the USGS in connection therewith.

This software is provided "AS IS."


 [
    ![CC0](http://i.creativecommons.org/p/zero/1.0/88x31.png)
  ](http://creativecommons.org/publicdomain/zero/1.0/)
