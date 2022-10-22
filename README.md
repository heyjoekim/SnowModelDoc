# SnowModel Documentation
Documentation for Tuttle Hydroclimatology and Snow (HAS) Lab

- Written by Haejo Kim
- Date Created: 10-21-2022
- Last Change: 10-21-2022

## 1) Introduction
This is a documentation for getting SnowModel up and running. This is a rewriting of Glen Liston's documentation but updated and corrected to fit our needs at Tuttle HAS Lab at Syracuse University.

## 2) Getting Started
### 2.1 Dependencies
Largely, we should be running this on the Linux VM. The dependencies that we need are:
1. Fortran Compiler
2. GDAL
3. GrADS

I also needed to install a couple of other things on my Linux distribution (Pop_OS 22.04) to get SnowModel to run.
* at
* mailutils

Testing will be needed to see what the Linux VM  will need to have to get SnowModel to run.
#### 2.1.1 Fortran Compiler
Glen Liston's code was designed to run on a Fortran 77, 90, or 95 compiler. The two tested compliers were the Portland Group complier (`pgf77` or `pgf90`) and the GNU compiler (`gfortran`). `gfortran` should be included in most Linux distributions.

To check if it is installed, open terminal and type
```
gfortran --version
```

#### 2.1.2 GDAL
Classic package. [Website](https://gdal.org). To check if GDAL is installed
```
gdalinfo --version
```

#### 2.1.3 GrADS
The Grid Analysis and Display System (GrADS) is a desktop tool that allows for easy access to manipulate and visualize earth science data files. The inputs and outputs are binary files that follows GrADS conventions. The website is [here](http://cola.gmu.edu/grads/). This is not required to run SnowModel. This is only used to view and graph input and output data.

GrADS can be downloaded for Windows, Mac, and Linux. It may be on some repositories and we don't need to download a tar file.
### 2.2 Connecting to VM
#### 2.2.1 Mac/Linux
probably SSH in terminal
#### 2.2.2 Windows
probably PuTTY
### 2.3 Downloading SnowModel Code
we can download code from [ftp://gliston.cira.colostate.edu//SnowModel/code](ftp://gliston.cira/colostate.edu/SnowModel/code).
We may need to use a ftp client downloader such as FileZilla or CyberDuck.
### 2.4 Running Fortran
Unlike Python, R, or MATLAB, Fortran is not an interperted computer language. This section goes over the basics of Fortran.

## 3) SnowModel
Here is a rundown of the file structures of SnowModel vital to getting a simulation running.
### 3.1 File Structure
1. `/sm/readme_docs/`
2. `/sm/README_First.txt`
3. `/sm/code/`
4. `/sm/topo_vege/`
    1. `asc_to_grads/`
    2. `NoAm_30m/`
    3. `readme.txt`
5. `/sm/met/`
    1. `data_download/`
    2. `era5/`
    3. `merra2/`
    4. `nldas2/`
    5. `nora10/`
    6. `stations/`
6. `/sm/snowmodel.par`
7. `/sm/run_snowmodel.script`
8. `/sm/copy_to_new_application.script`
9. others folders
    1. `/sm/extra_met/`
    2. `/sm/hrestart`
    3. `/sm/post_process/`
    4. `/sm/precip_cf/`
    5. `/sm/seaice/`
    6. `/sm/swe_assim/`
    7. `/sm/misc_programs/`


#### 3.1.1. READMEs
A bunch of documentation is provided by Glen Liston. Please try to read them outside of this documentation. Also, additional documentation is provided in the folder `/sm/readme_docs/`.
#### 3.1.2. code
The code. This must be compiled before run!
#### 3.1.3. topo_vege
directory that contains the processing for topography and vegetation inputs
#### 3.1.4. met
#### 3.1.5. snowmodel.par
parameter file that controls the Simulation and tailor model specifications
#### 3.1.6. run_snowmodel.script
See section Running SnowModel.

### 3.2) Files relating to Outputs
1. `/sm/outputs/`
2. `/sm/ctl_files/`
3. `/sm/figures/`
4. `/sm/snowmodel.err`
5. `/sm/snowmodel.list`
#### 3.2.1. outputs
This folder contains the `.gdat` binary files
#### 3.2.2. ctl_files
This folders contains the `.ctl` files that are also required for outputs through GrADS.
#### 3.2.3. figures
Figures
#### 3.2.4. `snowmodel.err` and `snowmodel.list`
2 text files created during model run call. `snowmodel.err` will have any errors during run that occurred. It also contains runtimes. `snowmodel.list` is a textfile that gives a summary of the run.
## 4) Prep before Running
### 4.1 Memory Requirements
Because of the large amounts of data stored in this model, we must take into account the size of our domain. 1 GB of data can run on a domain size of about 1200 x 1200. 16 GB of data allows for sizes of about 4800 x 4800. 

|memory (Gb) |   number of grid cells in x and y |   number of cells|
|------------|-----------------------------------|------------------|
|  0.5       |             900 x 900             |       800,000    |
|  1.0       |            1300 x 1300            |     1,700,000    |
|  2.0       |            1900 x 1900            |     3,600,000    |
|  4.0       |            2600 x 2600            |     7,000,000    |
## 5) Running SnowModel
Now that we got that all out of the way, we can finally run SnowModel. This section will go through how we compille the code, and run the code.
### 5.1 Compile
To compile SnowModel, run the `compile_snowmodel.script` (type
it, or click on it or something, depending on your environment).
This creates an executable called `snowmodel`. We may need to edit certain lines depending on the compiler that we are using.
### 5.2 Method 1
Probably the easiet way to run SnowModel is to run the script `run_snowmodel.script`. All this script does is call another script called `/sm/code/utility/running_SnowModel.script`, and brings up a message when the run is finished. It also create 2 new files: `snowmodel.err` and `snowmodel.list`

Edit this file to change the email address you want the "The SnowModel Run Has Finished" message to be sent to once your simulation is finished (or comment it out, if you don't want such a message sent). For this email to be sent, 'mail' or 'sendmail' must be running on your workstation. It can also be helpful to modify the email subject to provide more information, such as "Subject: the 1999-2010 Oregon SnowModel simulation has finished". It is crucial that the email address in this script is edited - otherwise you will not receive an email saying the SnowModel simulation is finished.
### 5.3 Method 2
The other way is to simply execute the the created executable `snowmodel`. We can write the outputs of SnowModel to an output file called `output.info` (or any other name).
```
./snowmodel > output.info
```

## 6) Summaries
### SnowModel Output Vars
SnowModel keeps track of approximately 175 spatially distributed, temporally evolving, snow and other environmental variables that can be output if they are needed for a specific application.

The lists below include the most common output variables.

Variables commonly output as part of typical SnowModel simulations:
|Daily Outputs, 2D distributions|Units   |
|-------------------------------|-----   |
|air temperature                | (deg C)|
|relative humidity| (%)|
|wind speed |(m/s)|
|wind direction |(deg from True North)|
|incoming solar radiation | ($\text{W}/\text{m}^2$)|
|total precipitation (rain+snow)| (m) |
|rainfall |(m)|
|snowfall |(m)|
|snow melt |(m)|
|snow sublimation |(m)|
|runoff |(m)|
|glacier melt |(m)|
|snow depth |(m)|
|snow density |($\text{kg}/\text{m}^3$)|
|snow-water-equivalent (SWE) depth |(m)|


The SnowModel post-processing scripts commonly create yearly values of these variables:
|Variable Name|Variable Description|Units|
|--|--|--|
|snow_onset_dos|	day of the start of the core snow period |(day of simulation)|
|snow_onset_doy|	day of the start of the core snow period |(day of year, 1-365,366)|
|snow_free_dos|	day of the end of the core snow period |(day of simulation)|
|snow_free_doy|	day of the end of the core snow period |(day of year, 1-365,366)|
|snow_first_dos|	day of first snow occurrence during the year| (day of simulation)|
|snow_first_doy|	day of first snow occurrence during the year| (day of year, 1-365,366)|
|snow_last_dos|	day of last snow occurrence during the year| (day of simulation)|
|snow_last_doy|	day of last snow occurrence during the year| (day of year, 1-365,366)|
|core_snow_days|	number of days in core snow period = the longest period of continuous snow cover |(days)|
|total_snow_days|	total number of days with snow on the ground during the year |(days)|
|prec_sum|	total precipitation |(m/yr)|
|rpre_sum|	rain precipitation |(m/yr)|
|spre_sum|	solid precipitation (snowfall) |(m/yr)|
|roff_sum|	total liquid water reaching the ground surface (includes snowmelt, rain, canopy unload, glacier melt, etc.) |(m/yr)|
|smlt_sum|	total melt per day (from the energy balance) |(m/yr)|
|glmt_sum|	glacier melt |(m/yr)|
|snod_max|	maximum snow depth in the year| (m)|
|snod_max_dos|	day of simulation that snod_max occurred||
|snod_max_doy|	day of year (1-365,366) that snod_max occurred||
|swed_max|	maximum snow water equivalent depth in the year| (m)|
|swed_max_dos|	day of simulation that swed_max occurred||
|swed_max_doy|	day of year (1-365,366) that swed_max occurred||
|tair_ave|	annual average 10-m air temperature |(degrees C)|
|ros|	number of days with rain on snow, defined to be daily rainfall ≥ 3 mm on snow depths ≥ 1.5 cm |(days)|


Other fields that are often output during SnowModel runs are in these lists:


ENERGY BALANCE:
|Var|Description|Units|
|--|--|--|
|tair|		air temperature| (deg C)|
|tsfc|		surface (skin) temperature |(deg C)|
|qsin|		incoming solar rad at the surface |(W/m2)|
|qlin|		incoming longwave rad at the surface| (W/m2)|
|qlem|		emitted longwave radiation |(W/m2)|
|qh	|	sensible heat flux |(W/m2)|
|qe	|	latent heat flux |(W/m2)|
|qc	|	conductive heat flux |(W/m2)|
|qm	|	melt energy flux |(W/m2)|
|albd|		albedo |(0-1)|
|ebal|		energy balance error| (W/m2)|


METEOROLOGY:
|Var|Description|Units|
|--|--|--|
|tair|		air temperature| (deg C)|
|relh|		relative humidity| (%)|
|uwnd|		meridional wind component| (m/s)|
|vwnd|		zonal wind component| (m/s)|
|wspd|		wind speed |(m/s)|
|wdir|		wind direction |(0-360, true N)|
|qsin|		incoming solar rad at the surface| (W/m2)|
|qlin|		incoming longwave rad at the surface| (W/m2)|
|prec|		precipitation |(m/time_step)|


SNOWPACK:
|Var|Description|Units|
|--|--|--|
|snod|		snow depth |(m)|
|sden|		snow density |(kg/m3)|
|swed|	snow-water-equivalent depth |(m)|
|roff|		runoff from snowpack base |(m/time_step)|
|rain|		liquid precipitation |(m/time_step)|
|spre|		solid precipitation |(m/time_step)|
|qcs|		canopy sublimation |(m/time_step)|
|canopy|	canopy interception store |(m)|
|sumqcs	|summed canopy sublim during year |(m)|
|sumprec|	summed precipitation during year| (m)|
|sumsprec|	summed snow precip during year |(m)|
|sumunload|	summed canopy unloading during year |(m)|
|sumroff	|summed runoff during the year |(m)|
|sumswemelt	|summed snow-water-equivalent melt |(m)|
|sumsublim	|summed static-surface sublimation| (m)|
|wbal		|water bal error |(m)|


BLOWING SNOW:
|Var|Description|Units|
|--|--|--|
|snod	|	snow depth| (m)|
|subl	|	sublimation at this time step| (m)|
|salt	|	saltation transport at this time step| (m)|
|susp	|	suspended transport at this time step| (m)|
|subgrid|	tabler snow redist at this time step| (m)|
|sumsubl|	summed sublimation during the year| (m)|
|sumtran|	summed blowing-snow transport for year| (m)|

### SnowModel Vegetation Classes
The first 23 vegetation types, and the associated vegetation snow-holding capacities (depth, in meters).

| code |description |        veg_shc (m)  |example|                class|
|--|--|--|--|--|
|  1  |coniferous forest |15.00|  spruce-fir/taiga/lodgepole | forest|
|  2  |deciduous forest  |12.00|  aspen forest|                forest|
|  3  |mixed forest      |14.00 | aspen/spruce-fir/low taiga | forest|
|  4  |scattered short-conifer |8.00 | pinyon-juniper |         forest|
|  5  |clearcut conifer  |4.00 | stumps and regenerating|     forest|
| ||
|  6|  mesic upland shrub   |0.50 | deeper soils, less rocky |  shrub
|  7 | xeric upland shrub   |0.25  |rocky, windblown soils    |  shrub
|  8  |playa shrubland      |1.00  |greasewood, saltbush       | shrub
|  9  |shrub wetland/riparian |1.75  |willow along streams        |shrub
| 10  |erect shrub tundra   |0.65  |arctic shrubland           | shrub
| 11  |low shrub tundra     |0.30  |low to medium arctic shrubs |shrub
| |||
| 12  |grassland rangeland   |0.15|  graminoids and forbs   |     grass
| 13 | subalpine meadow      |0.25 | meadows below treeline  |    grass
| 14  |tundra (non-tussock)  |0.15  |alpine, high arctic      |   grass
| 15  |tundra (tussock)      |0.20  |graminoid and dwarf shrubs|  grass
| 16  |prostrate shrub tundra |0.10  |graminoid dominated        | grass
| 17  |arctic gram. wetland  |0.20  |grassy wetlands, wet tundra |grass
| |||
| 18  |bare                    |0.01|                            |bare|
|||
| 19|  water/possibly frozen    |0.01 |                        |water|
| 20 | permanent snow/glacier   |0.01  |                         |water
| |||
| 21 | residential/urban        |0.01 |                          |human
| 22 | tall crops               |0.40 | e.g., corn stubble      |human
| 23  |short crops              |0.25 | e.g., wheat stubble      |human
| |||
| 24|  user defined (see below)||
| 25 | user defined (see below)||
| 26  |user defined (see below)||
| 27  |user defined (see below)||
| 28  |user defined (see below)||
| 29  |user defined (see below)||
| 30  |user defined (see below)||

The last 7 types are available to be user-defined vegetation types and vegetation snow-holding capacities. It is also possible to run the model with no vegetation-type data array.  To do this you set the following vegetation=constant flag in snowmodel.par.

```
! Define whether the vegetation will be constant or defined by the
!   topography/vegetation input file name (0.0 means use the file,
!   1.0 or greater means use a constant vegetation type equal to
!   the number that is used).  This will define the associated
!   veg_shc that will be used.  The reason you might use a constant
!   vegetation type is to avoid generating a veg-distribution file.
!     const_veg_flag = 12.0
      const_veg_flag = 0.0
```