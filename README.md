GM2Calc
=======

[![Build Status](https://travis-ci.org/GM2Calc/GM2Calc.svg?branch=master)](https://travis-ci.org/GM2Calc/GM2Calc)

GM2Calc calculates the SUSY contributions to the anomalous magnetic
moment of the muon a_mu = (g-2)/2 in the real MSSM at the 1- and
leading 2-loop level.

 * Homepage:                https://gm2calc.hepforge.org
 * Source code repository:  https://github.com/gm2calc
 * References:              Eur.Phys.J. C76 (2016) no.2, 62
   [[arxiv:1510.08071](https://arxiv.org/abs/1510.08071)]


Requirements
============

 * C++ compiler (g++ >= 4.6.3 or clang++ >= 3.4 or icpc >= 14.0.3)
 * Boost (version 1.37.0 or higher) [http://www.boost.org]
 * Eigen 3 (version 3.1 or higher) [http://eigen.tuxfamily.org]


Compilation
===========

To compile GM2Calc run:

    mkdir build
    cd build
    cmake ..
    make

The GM2Calc executable can then be found in `bin/gm2calc.x` and the
GM2Calc library can be found in the `lib/` directory.  The used
compiler and package paths can be passed as arguments to cmake.

**Example:**

    cmake \
       -DCMAKE_CXX_COMPILER=icpc \
       -DEIGEN3_INCLUDE_DIR=/opt/eigen3/eigen3 \
       -DBOOST_ROOT=/opt/boost \
       ..


Running GM2Calc
===============

From the command line
---------------------

GM2Calc can be run from the command line using an SLHA input file or a
custom GM2Calc input file (similar to SLHA, but different definition
of input parameters).  See `bin/gm2calc.x --help` for all options.

**Example:** Running GM2Calc with an SLHA input file:

    bin/gm2calc.x --slha-input-file=../input/example.slha

or

    cat ../input/example.slha | bin/gm2calc.x --slha-input-file=-

**Example:** Running GM2Calc with a custom GM2Calc input file:

    bin/gm2calc.x --gm2calc-input-file=../input/example.gm2

or

    cat ../input/example.gm2 | bin/gm2calc.x --gm2calc-input-file=-


From within Mathematica
-----------------------

When Mathematica is installed, a MathLink executable for GM2Calc is
build, which allows one to run GM2Calc from within Mathematica.  The
MathLink executable can be found in `bin/gm2calc.mx`.  This executable
can be installed in Mathematica by calling

    Install["bin/gm2calc.mx"]

Afterwards, the GM2Calc Mathematica interface functions can be used.
See `examples/example-slha.m` and `examples/example-gm2calc.m` for
examples with SLHA and GM2Calc input parameters, respectively.

**Example:**

    math -run "<< ../examples/example-slha.m"
    math -run "<< ../examples/example-gm2calc.m"


Input parameters
================

SLHA input parameters
---------------------

When GM2Calc is called with an SLHA input file, for example as

    bin/gm2calc.x --slha-input-file=../input/example.slha

then the input parameters are read from the following blocks:

 * `SMINPUTS`: Standard Model fermion masses, W and Z pole masses,
   alpha_s(MZ)

 * `MASS`: pole masses of SUSY particles, W pole mass.  If the W pole
   mass is given in `MASS[24]`, then this value will be used instead
   of the W pole mass given in `SMINPUTS[9]`.

 * `HMIX`: tan(beta), mu parameter, renormalization scale

 * `AU`, `AD`, `AE`: soft-breaking trilinear couplings,
   renormalization scale

 * `MSOFT`: soft-breaking gaugino masses, soft-breaking sfermion
   masses, renormalization scale

 * `GM2CalcInput`: alpha_em(MZ), alpha_em in the Thomson limit

See `input/example.slha` for an example SLHA input file.


GM2Calc input parameters
------------------------

When GM2Calc is called with an input file in the custom GM2Calc
format, for example as

    bin/gm2calc.x --gm2calc-input-file=../input/example.gm2

then the input parameters are read from the following blocks:

 * `SMINPUTS`: Standard Model fermion masses, W and Z pole masses,
   alpha_s(MZ)

 * `GM2CalcInput`: renormalization scale, alpha_em(MZ), alpha_em in
   the Thomson limit, tan(beta), mu parameter, soft-breaking gaugino
   masses, soft-breaking Bmu parameter, soft-breaking sfermion masses,
   soft-breaking trilinear couplings

See `input/example.gm2` for an example input file with custom GM2Calc
input parameters.


Block `GM2CalcConfig`
---------------------

When running GM2Calc from the command line with an (SLHA or custom
GM2Calc) input file, the input file may contain the `GM2CalcConfig`
configuration block to customize the calculation and the output.  The
`GM2CalcConfig` block entries are summarized in the following table
and are described below:

| Entry    | Description           | Possible values | Defaul value                              |
|----------|-----------------------|-----------------|-------------------------------------------|
| 0        | output format         | `0`, ..., `4`   | `4` for SLHA input, `1` for GM2Calc input |
| 1        | loop order            | `0`, `1`, `2`   | `2`                                       |
| 2        | force output          | `0`, `1`        | `0`                                       |
| 3        | verboe output         | `0`, `1`        | `0`                                       |
| 4        | estimate uncertainty  | `0`, `1`        | `0`                                       |

Description:

 * `GM2CalcConfig[0]`: defines the output format (`0` ... `4`)

   `0`: minimal output (a single number)  
   `1`: detailed (a detailed output of the various contributions)  
   `2`: write the value of a_mu into `LOWEN` block, entry `6`  
   `3`: write the value of a_mu into `SPhenoLowEnergy` block, entry `21`  
   `4`: write the value of a_mu into `GM2CalcOutput` block, entry `0`

 * `GM2CalcConfig[1]`: loop order of the calculation (`0`, `1` or `2`).
   We recommend to use `2`.

 * `GM2CalcConfig[2]`: disable/enable tan(beta) resummation (`0` or `1`).
   We recommend to use `1`.

 * `GM2CalcConfig[3]`: force output even if physical problem has
   occured (`0` or `1`).  **WARNING**: The result might not be trusted
   if a problem has occured!  We recommend to use `0`.

 * `GM2CalcConfig[4]`: disable/enable verbose output (`0` or `1`).
   We recommend to use `0` by default, and `1` only for debugging.

 * `GM2CalcConfig[5]`: disable/enable uncertainty estimation (`0` or `1`).
   Depending on the chosen output format (see `GM2CalcConfig[0]`) the
   uncertainty is written

   * as a single number to stdout   in case of minimal output,
   * to the first line              in case of detailed output,
   * to `GM2CalcOutput[1]`          otherwise.

We recommend to use the following configuration block in an SLHA input
file:

    Block GM2CalcConfig
         0     4     # output format (0 = minimal, 1 = detailed,
                     #  2 = NMSSMTools, 3 = SPheno, 4 = GM2Calc)
         1     2     # loop order (0, 1 or 2)
         2     1     # disable/enable tan(beta) resummation (0 or 1)
         3     0     # force output (0 or 1)
         4     0     # verbose output (0 or 1)
         5     1     # calculate uncertainty


C/C++ interface
===============

GM2Calc provides a C and a C++ interface.  To use the routines of
GM2Calc in a C++ program, the following C++ header files have to be
included:

    src/gm2_1loop.hpp
    src/gm2_2loop.hpp
    src/gm2_uncertainty.hpp
    src/MSSMNoFV_onshell.hpp

To use the routines of GM2Calc in a C program, the following C header
files have to be included:

    src/gm2_1loop.h
    src/gm2_2loop.h
    src/gm2_uncertainty.h
    src/MSSMNoFV_onshell.h

Please refer to the content of these header files for a precise
definition of all interface functions.  The C/C++ example programs in
the `examples/` directory serve as an illustration of the interface
routines.


Source code documentation
=========================

GM2Calc uses Doxygen to document the source code.  The complete source
code documentation can be generated by running

    make doc

Afterwards, the generated HTML pages can be found in `doc/html/` and
can be openend with your favourite web browser, e.g.

    firefox doc/html/index.html


References
==========

GM2Calc has been published in

- Peter Athron, Markus Bach, Helvecio G. Fargnoli, Christoph
  Gnendiger, Robert Greifenhagen, Jae-hyeon Park, Sebastian Paßehr,
  Dominik Stöckinger, Hyejung Stöckinger-Kim, Alexander Voigt:
  *GM2Calc: Precise MSSM prediction for (g-2) of the muon* [<a
  href="http://arxiv.org/abs/1510.08071">Eur.Phys.J. C76 (2016) no.2,
  62</a>]

The expressions implemented in GM2Calc have been taken from
[<a href="http://arxiv.org/abs/1003.5820">Phys.Rev. D81 (2010) 093004</a>,
 <a href="http://arxiv.org/abs/1309.0980">Phys.Lett. B726 (2013) 717-724</a>,
 <a href="http://arxiv.org/abs/1311.1775">JHEP 1402 (2014) 070</a>,
 <a href="http://arxiv.org/abs/1504.05500">JHEP 1510 (2015) 026</a>]
