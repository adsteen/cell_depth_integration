    library(mosaic) # unsure whether this is necessary

    ## Warning: package 'mosaic' was built under R version 4.0.2

    ## Loading required package: dplyr

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

    ## Loading required package: lattice

    ## Loading required package: ggformula

    ## Warning: package 'ggformula' was built under R version 4.0.2

    ## Loading required package: ggplot2

    ## Loading required package: ggstance

    ## Warning: package 'ggstance' was built under R version 4.0.2

    ## 
    ## Attaching package: 'ggstance'

    ## The following objects are masked from 'package:ggplot2':
    ## 
    ##     geom_errorbarh, GeomErrorbarh

    ## 
    ## New to ggformula?  Try the tutorials: 
    ##  learnr::run_tutorial("introduction", package = "ggformula")
    ##  learnr::run_tutorial("refining", package = "ggformula")

    ## Loading required package: mosaicData

    ## Loading required package: Matrix

    ## Registered S3 method overwritten by 'mosaic':
    ##   method                           from   
    ##   fortify.SpatialPolygonsDataFrame ggplot2

    ## 
    ## The 'mosaic' package masks several functions from core packages in order to add 
    ## additional features.  The original behavior of these functions should not be affected by this.
    ## 
    ## Note: If you use the Matrix package, be sure to load it BEFORE loading mosaic.
    ## 
    ## Have you tried the ggformula package for your plots?

    ## 
    ## Attaching package: 'mosaic'

    ## The following object is masked from 'package:Matrix':
    ## 
    ##     mean

    ## The following object is masked from 'package:ggplot2':
    ## 
    ##     stat

    ## The following objects are masked from 'package:dplyr':
    ## 
    ##     count, do, tally

    ## The following objects are masked from 'package:stats':
    ## 
    ##     binom.test, cor, cor.test, cov, fivenum, IQR, median, prop.test,
    ##     quantile, sd, t.test, var

    ## The following objects are masked from 'package:base':
    ## 
    ##     max, mean, min, prod, range, sample, sum

    library(mosaicCalc)

    ## Warning: package 'mosaicCalc' was built under R version 4.0.2

    ## Loading required package: mosaicCore

    ## Warning: package 'mosaicCore' was built under R version 4.0.2

    ## 
    ## Attaching package: 'mosaicCore'

    ## The following objects are masked from 'package:dplyr':
    ## 
    ##     count, tally

    ## 
    ## Attaching package: 'mosaicCalc'

    ## The following object is masked from 'package:stats':
    ## 
    ##     D

    library(tidyverse)

    ## ── Attaching packages ────────────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ tibble  3.0.3     ✓ purrr   0.3.4
    ## ✓ tidyr   1.1.0     ✓ stringr 1.4.0
    ## ✓ readr   1.3.1     ✓ forcats 0.5.0

    ## ── Conflicts ───────────────────────────────────────── tidyverse_conflicts() ──
    ## x mosaicCore::count()        masks mosaic::count(), dplyr::count()
    ## x purrr::cross()             masks mosaic::cross()
    ## x mosaic::do()               masks dplyr::do()
    ## x tidyr::expand()            masks Matrix::expand()
    ## x dplyr::filter()            masks stats::filter()
    ## x ggstance::geom_errorbarh() masks ggplot2::geom_errorbarh()
    ## x dplyr::lag()               masks stats::lag()
    ## x tidyr::pack()              masks Matrix::pack()
    ## x mosaic::stat()             masks ggplot2::stat()
    ## x mosaicCore::tally()        masks mosaic::tally(), dplyr::tally()
    ## x tidyr::unpack()            masks Matrix::unpack()

I’m imagining a volume of the Earth’s crust like this: [crust
diagram](schema.jpg)

Note that x is positive to the right, y is positive into the page, and z
is positive down.

Magnabosco says that the number of cells per unit volume, as a function
of depth, is given by
*ρ* = 10<sup>*A*</sup>*z*<sup>*B*</sup>
Where *ρ* is the cell concentration along a column and *A* and *B* are
parameters determined empirically from a linear fit to log-transformed
cell data.

We can find the total number of cells in a column by integrating from
*z* = 0 to *z* = *z*<sub>*m**a**x*</sub>,

*c*<sub>*c**o**l*</sub> = ∫<sub>0</sub><sup>*z*<sub>*m**a**x*</sub></sup>*d**z*
where *c*<sub>*c**o**l*</sub> is the number of cells per unit area in a
column. (Magnabosco et al used cells cm<sup> − 3</sup> so this would be
the number of cells in a 1 cm<sup>2</sup> column of crust.)

We can integrate over *x* (left-to-right across the page) to get the
number of cells in a unit of cross-sectional area of crust.

*c*<sub>*a**r**e**a*</sub> = ∫<sub>0</sub><sup>*x*<sub>*m**a**x*</sub></sup>*c*<sub>*c**o**l*</sub>(*x*)*d**x* = ∫<sub>0</sub><sup>*x*<sub>*m**a**x*</sub></sup>∫<sub>0</sub><sup>*z*<sub>*m**a**x*</sub></sup>10<sup>*A*</sup>*z*<sup>*B*</sup>*d**z**d**x*

Donato says that *x*<sub>*m**a**x*</sub> is given by a logarithmic
function, which I’ll express as
*x*<sub>*m**a**x*</sub> = *θ*log (*x* + 1)
*θ* is a scaling factor so that *x*<sub>*m**a**x*</sub> is the
appropriate number at the deepest end of our volume of crust, and we
have to do *l**o**g*(*x* + 1) because *l**o**g*(0) is undefined. So the
cross-section of the volume looks like this:

*Insert plot here*.

Let’s let R do the symbolic integration for us:

    #A <- -8; B <- -0.7 # These are not quite right
    rho <- formula(10^(A) * z^(B) ~ z) 
      
    mosaicCalc::antiD(rho)

    ## function (z, C = 0, A, B) 
    ## 10^(A) * 1/((B) + 1) * z^((B) + 1) + C

    z_max <- formula(theta * log10(x+1) ~ x)
    antiD(z_max)

    ## function (x, theta, C = 0) 
    ## {
    ##     numerical_integration(.newf, .wrt, as.list(match.call())[-1], 
    ##         formals(), from, ciName = intC, .tol)
    ## }
    ## <environment: 0x7ffc01c4fe40>
