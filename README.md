# SnowModel Documentation
Documentation for Tuttle Hydroclimatology and Snow (HAS) Lab

## Introduction
This is a documentation for getting SnowModel up and running. This is a rewriting of Glen Liston's documentation but update to fit our needs at Tuttle HAS LAB at Syracuse University.

### Getting Started
#### Dependencies
Largely, we should be running this on the Linux VM. The dependencies that we need are:
1. Fortran Compiler
2. GDAL
3. GrADS
##### Fortran Compiler
Glen Liston's code was designed to run on a Fortran 77, 90, or 95 compiler. The two tested compliers were the Portland Group complier (`pgf77` or `pgf90`) and the GNU compiler (`gfortran`). `gfortran` should be included in most Linux distributions.
##### GDAL
Classic package.
we need to run `gdaltransform`, `gdalwarp`, and `gdal_translate` (These are custom code located in `/sm/topo_vege/`. 
##### GrADS
The inputs and outputs are binary files that follows GrADS conventions. The website is <include link>.
This is not required to run SnowModel. This is only used to view and graph input and output data.
