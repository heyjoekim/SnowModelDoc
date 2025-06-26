# SnowModel Documentation
A guide to getting SnowModel to run
Internal documentation for Tuttle Hydroclimatology and Snow (HAS) Lab

- Written by Haejo Kim
- Date Created: 10-21-2022
- Last Change:  03-18-2025

## Table of Contents
1. [Introduction](#1-introduction)
2. [Getting Started](#2-getting-started)
3. [SnowModel](#3-snowmodel)
4. [Before Running SnowModel](#4-before-running-snowmodel)
5. [Running SnowModel](#5-running-snowmodel)
6. [Plotting SnowModel Results](#6-plotting-snowmodel-results)
7. [Summaries](#7-summaries)

## Acknowledgements
Thank you to Anna Grunes who provided some updated code and guidance!

## 1 Introduction {#1-introduction}
This is a documentation for getting SnowModel up and running. This is a combination of my struggles, Anna Grunes' help, and Glen Liston's documentation. This is what I found works with my current setup and the computer resources available to us at Syracuse University.

Current setup:
- SU Managed Macbook Air with macOS Ventura 13.7.4
- Sam's Linux VM

## 2 Getting Started {#2-getting-started}
### 2.1 Dependencies
To run SnowModel, you will need access to linux vm (as-setuttle-lvm.syr.edu) with:

1. X11 Forwarding
2. Anaconda
3. ncl
4. gfortran
5. GDAL
6. GrADS$^{*}$

#### 2.1.1 Setting up X11 Forwarding and Connecting to VM

X11 Forwading is an SSH protocal that allows us to run graphical applications on a remote server and interact with them on our local machine. This  is needed because the linux VM is only currently available on commandline only. This is extremely useful for running GrADS on the VM, as we are able to see the graphs. To allow X11 Forwarding, we need to do the following steps:

On the server:

1. Make sure `xauth` is installed (it was, but doesn't hurt to double check)
2. Edit `/etc/ssh/ssh_config`. Make sure the following items are uncommented and has the following values
```bash
AllowAgentForwarding yes
AllowTcpForwarding yes
X11Forwarding
X11DisplayOffset 10
X11UseLocalHost no
```
3. Restart the sshd daemon
```bash
sudo service sshd restart
```

For more information go to this stack overflow answer [here.](https://stackoverflow.com/a/23033038)

On your local machine:
1. connect to the VM. If you have Mac or Linux, the following command should suffice
```bash
ssh -Y user-name@as-setuttle-lvm.syr.edu
```

is `-Y` necessary? Yes! To double check that X11 forwarding is working, simply try to run GrADS from the command line. After typinh a y or no for landscape/portrait mode, a blank window should pop-up on your machine. For Windows, you will most likely use a program like PuTTY. I'm pretty sure the process is mostly the same.

#### 2.1.2 NCL

The NCAR Command Language (NCL) is an interpreted language designed for scientific data analysis and visualization, mainly for climate/weather data. For more info: [click here.](https://www.ncl.ucar.edu/)

Currently the preferred method to downloading NCL is through conda. After downloading Anaconda or miniconda, install ncl with a conda environment:
```bash
conda create -n ncl_stable -c conda-forge ncl
conda activate ncl_stable
```
#### 2.1.3 GrADS

The Grid Analysis and Display System (GrADS) is a desktop tool that allows for easy access to manipulate and visualize earth science data files. The inputs and outputs are binary files that follows GrADS conventions. The website is [here](http://cola.gmu.edu/grads/).

GrADS is technically not required to run SnowModel. However, it is useful to plot input and output variables. GrADS can be downloaded for Windows, Mac, and Linux. It may be on some repositories and we don't need to download a tar file. On Sam's linux VM which runs some version of Ubuntu, GrADS can be installed using the following command. 

```bash
sudo apt-get install grads
```

There is another updated version of GrADS called OpenGrADS that can interface with python. It may be worth experimenting with this version in the future.

#### 2.1.4 Fortran Compiler

SnowModel was designed to run on a Fortran 77, 90, or 95 compiler. The two tested compliers were the Portland Group complier (`pgf77` or `pgf90`) and the GNU compiler (`gfortran`). `gfortran` should be included in most Linux distributions. Mac OS may have gfortran as well depending on the Xcode utilities that are available.

To check if it is installed, open terminal and type

```bash
gfortran --version
```

NOTE: all fortran code needs to be compiled before we run it. A lot of scripts are compiled using this command.
```bash
gfortran -mcmodel=small file.f
```
If you are running this on a Mac using the arm64 architecture (usually an M# Macbook), run the above code. If you are going to run it on a linux system run the following line of code.
```bash
gfortran -mcmodel=medium file.f
```
#### 2.1.5 GDAL

GDAL is a classic package that is used to manipulate geographical data. GDAL is required to run SnowModel. [Website](https://gdal.org). To check if GDAL is installed, use the following command:
```bash
gdalinfo --version
```

## 3 SnowModel {#3-snowmodel}
### 3.1 Introduction
SnowModel runs off of 4 subroutines:
1. MicroMet: defines meteorological forcing conditions
2. EnBal: calculate surface energy exchange
3. SnowPack: simulates snow depth and SWE evolution
4. SnowTran-3D: simulates snow redistribution by wind

Highly recommended to read the SnowModel papers by Glen Liston!.
[Link to paper](https://doi.org/10.1175/JHM548.1)

### 3.2 Downloading SnowModel Code
We can download code from [ftp://gliston.cira.colostate.edu//SnowModel/code](ftp://gliston.cira/colostate.edu/SnowModel/code). The original SnowModel code will be uploaded to Sam's shared drive in "include path".

Alternatively, we can download Justin Plugh's updated SnowModel code from his [GitHub](https://github.com/jupflug/SnowModel). For modeling in the Eastern US, we may need a different rain/snow threshold using the wet bulb temperature. For that, using Anna Grunes' code may be better "add path to code here". 

## 4 Before Running SnowModel {#4-before-running-snowmodel}
At this point, all dependencies are taken care of and SnowModel is downloaded. Before we can actually run SnowModel there is fair amount of work that needs to be done. The following workflow needs to be done:

1. Set up Topography and Vegetation Data
2. Set up Meteorlogical Data

### 4.1 Topo_vege
I assume that we are using the NoAm_30m data. The file structure for this directory is as follows:

- `1_readme_general_topo_vege.txt`
- `2_readme_topo_vege_data_download.txt`
- `3_readme_inputs.txt`
- `4_readme_process_topo_vege.txt`
- `5_readme_input_names_locations.txt`
- `6_readme_other_topo_vege_datasets.txt`
- `SM_domain_config_info_OUTPUTS.dat`
- `SM_dxdy_cornerll_proj_INPUTS.dat`
- `data_download/`
    - `wget_topo_vege_gliston.script`
- `input_files_info/`
    - `topo_filepath.dat`
    - `topo_proj_string.dat`
    - `vege_filename.dat`
    - `vege_proj_string.dat`
- `plot_topo_vege.gs`
- `process_data/`
    - `1_topo/`
        - `1_find_subdomain_ll.f`
        - `2_project.script`
        - `3_find_corner_coords_proj.f`
        - `4_project_back_to_ll.script`
        - `5_find_subdomain_ll_final.f`
        - `6_find_length_scale.f`
        - `7_mk_ll_tile_list.f`
        - `8_mk_merge_script.script`
        - `9_gdal_merge_tiles.script`
        - `10_gdal_ll_to_proj.script`
        - `11_cleanup_tmp_topo_files.script`
        - `A_write_coords_info.f`
        - `B_project_back_to_ll.script`
        - `C_find_subdomain_ll_final.f`
        - `outputs/`
        - `readme.txt`
    - `2_vege/`
        - `1_gdal_laea_to_proj.script`
        - `2_cleanup_tmp_vege_files.script`
        - `outputs/`
    - `3_merge_topo_vege/`
        - `1_merge_topo_vege.f`
        - `2_cleanup_flt.script` 
        - `classes.txt`
        - `negative_topo_values.dat`
`process_topo_vege.script`

#### 4.1.1 Data Download

A wget script is provided from Glen Liston to obtain his topo_vege data. You can supply your own, but it may be easier to use his data. I have downloaded Glen Liston's data and it is on Sam's shared drive. It should be located in `/General/hkim/sm_datafiles/topography/` and `/General/hkim/sm_datafiles/landcover/`.

The topo data is a 1-arcsec, North American dem dataset from US NED 3DEP program. The land cover data is the 2015 North American Land Changeg Monitoring System dataset (NALCMS).

#### 4.1.2 Making Processing Input Files

It is highly recommended that you read the friendly manual (RTFM...ok not that friendly but read them all!). The main files that need to be edited before we start are

- `SM_dxdy_cornerll_proj_INPUTS.dat`
- `input_files_info/`
    - `topo_filepath.dat`
    - `topo_proj_string.dat`
    - `vege_filename.dat`
    - `vege_proj_string.dat`

If you are using Glen's data, then we just need to edit the `SM_dxdy_cornerll_proj_INPUTS.dat`, and the `topo_filepath.dat` and `vege_filename.dat`.

Again RTFM on how to format the `SM_dxdy_cornerll_proj_INPUTS.dat`.

#### 4.1.3 Running A Lot of Code

If everything is in place, we can run the `process_topo_vege.script`.

### 4.2 Meteorological Data
I believe SnowModel was initially designed to run using data from meteorological stations that were interpolated using MicroMet. We can also force SnowModel using reanalysis data (i.e. ERA5, NLDAS2, MERRA-2). However, it does take some time.

Since I got it working for NLDAS2, this document will go over the steps I took. The file tree looks as such.

- `1_topo_lonlat/`
    - `nldas2_topo.txt`
    - `nldas2_ll.txt`
    - `readme.txt`
- `2_define_points/`
    - `1_points_from_ll_to_proj.script`
    - `2_pts_sm_domain.f`
    - `ij_xy_topo.dat`
    - `npts.dat`
    - `met_points_proj.dat`
- `3_figs/`
    - `SM_info.dat`
    - `pts.dat`
    - `met_points.gs`
    - `fig_files`
    - `met_points.pdf`
    - `met_points.png`
    - `met_points.eps`
    - `readme.txt`
- `4_maxiter_offset/`
    - `start_end_dates_maxiter_ioffset.dat`
    - `start_end_dates_maxiter_ioffset.f`
- `5_mk_gdat_optional/`
    - `mk_SM_gdat_met_file.f`
    - `sm_met_inputs.ctl`
    - `sm_met_inputs.gdat`
    - `readme.txt`
- `6_fix_drizzle_optional/`
    - `plot1.gs`
    - `fix_drizzle.f`
    - `drizzle_storm_clip.gdat`
    - `drizzle_info.dat`
    - `sprec_clipped.gdat`
    - `drizzle_storm_clip.ctl`
    - `sprec_clipped.ctl`
    - `plot2.gs`
- `7_mk_mm/`
    - `mk_micromet_NLDAS2.f`
- `readme.txt`

#### 4.2.1 Download Reanalysis Data

There are script templates to download reanalysis data. I (and Sam) should have some templates scripts to download ERA5 and NLDAS2 data. Depending on your needs, daily or hourly data can be downloaded.

#### 4.2.2 Some Processing

The inputs for SnowModel is the following:

- tair: Air Temperatire (C)
- relh: Relative humidity (%)
- wspd: Wind Speed (m/s)
- wdir: Wind Direction (0-360 degress)
- prec: water equivalent Precipitation (mm/dt)

Your may notice that most reanalysis datasets do not have relh, wspd, or wdir. We need to process them! This can vary depending on the reanalysis dataset. wspd and wdir can be obtained from the U and V components of the wind. relh calculation will be a little more involved depending on what variables are available (e.g., dewpoint in ERA5 or specific humidity in NLDAS2). Also be careful as reanalysis datasets treat precipitation differently.

The best method I found is to download these data as netcdf files. Process the data and save each input variable that SnowModel needs into its own netcdf file (i.e., save tair to tair.nc, relh to relh.nc). We can use ncl to convert these data into a binary file to create th Micromet input file.

#### 4.2.3 NCL: .nc to binary data

Glen's code (and Fortran) reads the data as binary files. We need to convert our 3 hourly or daily variable files into the correct format. I found that using NCL is the most straight forward. The following commands in ncl will convert our netcdf files to the binary files
```bash
f_in = addfile("path_to_netcdf", "r")
var = f_in->var
fbinwrite("path_to_gdat", var)
```
#### 4.2.4 Running some Fortran Code

Follow Glen's steps to create the MicroMet input file. I removed the optional steps that removes the drizzle fraction! Read the appropriate readme in the directory if you want to and need to do the optional steps!

1. 1_topo_lonlat
    - Change the `nldas2_ll.txt` and `nldas2_topo.txt` files only if the input topo_vege is slightly different from Glen Liston's. For example, since I only wanted SRRW, I subset the NLDAS2 forcing to cover the easter US.
2. 2_define_points
    1. run `1_points_from_ll_to_proj.script`
    2. edit `2_pts_sm_domain.f`
    3. compile and run `2_pts_sm_domain.f`
3. 4_maxiter_offset
    1. edit `start_end_dates_maxiter_ioffset.f`
    2. compile and run `start_end_dates_maxiter_ioffset.f`
4. 7_mk_mm
    1. edit `mk_micromet_NLDAS2.f`
    2. compile and run `mk_micromet_NLDAS2.f`

The process should be similar if you wanted to run SnowModel with ERA5 instead. The processing steps will be different.

## 5 Running SnowModel {#5-running-snowmodel}
Once you have both vege_topo.gdat and a MicroMet input file, we are FINALLY ready to actually run SnowModel.

Here is how the file structure of SnowModel should look to run.:

- `code/`
    - `compile_snowmodel.script` 
    - `readparam_code.f`
    - `dataassim_user.f`
    - `snowmodel.inc`
    - `enbal_code.f`         
    - `snowmodel_main.f`
    - `enbal_code_old.f`
    - `snowmodel_vars.inc`
    - `micromet_code.f` 
    - `snowpack_code.f`
    - `micromet_code_dai.f` 
    - `snowpack_code_2lay.f`
    - `micromet_code_tw.f` 
    - `snowpack_code_old.f`
    - `outputs_user.f`
    - `snowtran_code.f`
    - `preprocess_code.f`
- `ctl/`
- `figures/`
- `met/`
- `outputs/`
    - `wo_assim/`
    - `wi_assim/`
- `snowmodel.par`

Here is what each item does:

- `snowmodel.par`: SnowModel parameter list file
- `code/`: Where the SnowModel code is located
- `met`: My input directory. It doesn't have to be called met, but its where I like to    put the topo/vege and met data files. 
- `outputs/`: Directory where the SnowModel output gets saved. There are two directorie   s within outputs depending on whether or not you use a data assimilation scheme in S   nowModel.
- `figures/`: Directory containing scripts to create GrADS plots.
- `ctl/`: Directory containing the data descriptor files for the output data files.

### 5.1 Edit snowmodel.par
The `snowmodel.par` file lists important parameters for our SnowModel run. Read this document carefully and make sure you change all of the relevant parameters to what you need.

### 5.2 create output folders
we need to create an outputs directory
```
mkdir -p outputs/wo_assim
```

### 5.3 Compile SnowModel
Within the code directory, there is a file called `compile_snowmodel,script`. Go into the code directory and run this script.
```
cd code
./compile_snowmodel.script
```

### 5.4 Run SnowModel
Go back to the main directory. There should now be an executable called `snowmodel`. We can run snowmodel!
```
cd ..
./snowmodel > ./output.dat
```

Running just `./snowmodel` just prints a lot of lines to the screen. If you don't to see that, the command above just puts it into a text file. If you don't run into any errors, congratulations! SnowModel has finished and there should be a bunch of ouput data files in `outputs/wo_assim` (or `outputs/wi_assim` if you run it with data assimilation).

## 6 Plotting SnowModel Results {#6-plotting-snowmodel-results}
So we completed a SnowModel run. Let's plot the results. Let's use the model output snow depths as an example.
### 6.1 Make a GrADS Data Descriptor File
The binary data file for the model output snow depths should be located in `outputs/wo_assim/snod.gdat`. To open this file in GrADS, we need to create a data descriptor file. I like to put these descriptor files into a separate directory called `ctls\`. Feel free to call it whatever you like.

```
mkdir ctls
```
We can create the data descriptor files for each of the variables we want to plot. The data descriptor for snow depth will look as such:
```bash
DSET ^../outputs/wo_assim/snod.gdat
TITLE SnowModel single-var output file
UNDEF -9999.0
XDEF nx LINEAR 1.0 1.0
YDEF ny LINEAR 1.0 1.0
ZDEF 1 LINEAR 1 1
TDEF ndays LINEAR DDmonYYYY 1dy
VARS 1
snod 0 0 snow depth (m)
ENDVARS
```

We just need to create these for each of the variables we want to plot!

#### 6.1.1 Explaining the .ctl files:
Here is a breif explanation of each line of the ctl file.

`DSET ^../outputs/wo_assim/snod.gdat`
- Points to where the binary data files is located. The ^ denotes to look in the same directory as the ctl file.

`TITLE SnowModel single-var output file`
- Gives a title to the data

`UNDEF -9999.0`
- Define the undefined values to -9999.0

`XDEF nx LINEAR 1.0 1.0`
- Define the x dimension limits. nx should be the same number as in `snowmodel.par` and linearly increases from 1.0 with steps of 1.0

`YDEF ny LINEAR 1.0 1.0`
- Defines the y dimension limits.

`ZDEF 1 LEVELS 1 1`
- Define the number of levels in the vertical direction. We only need one.

`TDEF ndays LINEAR DDmonYYYY 1dy`
- Define the time dimension. This is the number of days we run our SnowModel simulation for. We define the start date (e.g., 01oct2023) and increments daily.

`VARS 1`
- Define how many variables are stored in the binary data file.

`snod 0 0 snow depth (m)`
- Define the snow depth variable, snod

`ENDVARS`
- Final line of the ctl file

### 6.2 Plot with GrADS
We can start up GrADS to plot the variable. Better yet, you can use a GrADS script in my example folder to make plots of snow depth or SWE.

#### 6.2.1 GrADS References
Here are some good resources that can be used for reference for plotting GrADS:

1. [GrADS Documentation](http://cola.gmu.edu/grads/gadoc/gadoc.php) The documentation for GrADS. A User guide and quick tutorials are available. 
2. [GrADS-aholic!](https://gradsaddict.blogspot.com/p/grads-tutorials.html) Nice tutorials here!

### 6.3 TODO: Plotting without GrADS
For better figures, I eventually would like to get this into a format where I can plot it on a grid and add other items (i.e., outline of a watershed or other points on the plot). This needs to be worked out. Additionally, we may want to look to switch to OpenGrADS which build on GrADS and adds functionality with python for higher quality figures.

## 7 Summaries {#7-summaries}
### 7.1 Original SM File Structure
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

#### 7.1.1 READMEs

A bunch of documentation is provided by Glen Liston. Please try to read them outside of this documentation. Also, additional documentation is provided in the folder `/sm/readme_docs/`.

#### 7.1.2 code

The code. This must be compiled before run!

#### 7.1.3 topo_vege

directory that contains the processing for topography and vegetation inputs

#### 7.1.4 met

directory that contains the processing for meteorological data. This contains separate directories for reanalysis datasets and station data.

#### 7.1.5 snowmodel.par
parameter file that controls the Simulation and tailor model specifications

#### 7.1.6 run_snowmodel.script
See section Running SnowModel.

### 7.2 Files relating to Outputs

1. `/sm/outputs/`
2. `/sm/ctl_files/`
3. `/sm/figures/`

#### 7.2.1 outputs

This folder contains the `.gdat` binary files

#### 7.2.2 ctl_files

This folders contains the `.ctl` files that are also required for outputs through GrADS.

#### 7.2.3. figures
Figures

### 7.3 Memory Requirements
Because of the large amounts of data stored in this model, we must take into account the size of our domain. 1 GB of data can run on a domain size of about 1200 x 1200. 16 GB of data allows for sizes of about 4800 x 4800. 

|memory (Gb) |   number of grid cells in x and y |   number of cells|
|------------|-----------------------------------|------------------|
|  0.5       |             900 x 900             |       800,000    |
|  1.0       |            1300 x 1300            |     1,700,000    |
|  2.0       |            1900 x 1900            |     3,600,000    |
|  4.0       |            2600 x 2600            |     7,000,000    |

### 7.4 SnowModel Output Vars
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
|ros|	number of days with rain on snow, defined to be daily rainfall $\geq$ 3 mm on snow depths $\geq$ 1.5 cm |(days)|


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

```bash
! Define whether the vegetation will be constant or defined by the
!   topography/vegetation input file name (0.0 means use the file,
!   1.0 or greater means use a constant vegetation type equal to
!   the number that is used).  This will define the associated
!   veg_shc that will be used.  The reason you might use a constant
!   vegetation type is to avoid generating a veg-distribution file.
!     const_veg_flag = 12.0
      const_veg_flag = 0.0
```
