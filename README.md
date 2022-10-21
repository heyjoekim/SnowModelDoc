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

To check if it is included, open terminal and type
```
gfortran --version
```
##### GDAL
Classic package.
we need to run `gdaltransform`, `gdalwarp`, and `gdal_translate` (These are custom code located in `/sm/topo_vege/`. 
##### GrADS
The inputs and outputs are binary files that follows GrADS conventions. The website is <include link>.
This is not required to run SnowModel. This is only used to view and graph input and output data.
### Connecting to VM
#### Mac/Linux
probably SSH in terminal
#### Windows
probably PuTTY
### Downloading SnowModel Code
we can download code from <>.
We may need to use a ftp client downloader such as FileZilla or CyberDuck.
### Running Fortran
Unlike Python, R, or MATLAB, Fortran is not an interperted computer language. This section goes over the basics of Fortran.

## SnowModel
Here is a rundown of the file structures of SnowModel vital to getting a simulation running.
### File Structure
1. `/sm/readme_docs/` + README_First.txt
2. `/sm/code/`
3. `/sm/topo_vege/`
    1. `asc_to_grads/`
    2. `NoAm_30m/`
    3. `readme.txt`
4. `/sm/met/`
    1. `data_download/`
    2. `era5/`
    3. `merra2/`
    4. `nldas2/`
    5. `nora10/`
    6. `stations/`
5. `/sm/snowmodel.par`
6. `/sm/run_snowmodel.script`
7. `/sm/copy_to_new_application.script`
8. others
    1. `/sm/extra_met/`
    2. `/sm/hrestart`
    3. `/sm/post_process/`
    4. `/sm/precip_cf/`
    5. `/sm/seaice/`
    6. `/sm/swe_assim/`
    7. `/sm/misc_programs/`


#### 1. READMEs
A bunch of documentation is provided by Glen Liston. Please try to read them outside of this documentation. Also, additional documentation is provided in the folder `/sm/readme_docs/`.
#### 2. code
The code. This must be compiled before run!
#### 3. topo_vege
directory that contains the processing for topography and vegetation inputs
#### 4. met
#### 5. snowmodel.par
parameter file that controls the Simulation and tailor model specifications
#### 6. run_snowmodel.script
See section Running SnowModel.
