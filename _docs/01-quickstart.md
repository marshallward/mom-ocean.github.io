---
layout: single
title: "Quick-Start Guide"
permalink: /docs/quick-start-guide/
excerpt: "Brief instructions for running MOM5 experiments"
toc: true
Author: Niki Zadeh (Niki.Zadeh@@noaa.gov)
---
   

##  Where to start?
   
A good place to start is the online [User Guide](https://mom-ocean.github.io/docs/userguide/), which gives full details on all the steps involved in running a complete experiment.

This document gives a short outline of how to get a very basic example experiment running.
   
## How to get the source code and scripts

You can access the source code by following these [download instructions](http://mom-ocean.github.io/downloads)
      
In the sections below, `TEST_CASE` is a generic name referring to the name of a specific experiment you are working on. After you clone the repository from github you will have a directory called `mom/` in your working directory, which will be referred to as `$root_dir` in this guide.
   
## How to compile and run the MOM tests
   
MOM requires that NetCDF and MPI libraries be installed on users' platform.
    
Ensure that you have the right environment variable for your platform in the file `bin/environs.PLATFORM_ID`. `PLATFORM_ID` could be any string that identifies your platform. The file `bin/environs.PLATFORM_ID` gets sourced at the beginning of all compile and run scripts and is there to make sure all compile and run time library paths are found.
    
There are a few types of test models provided for this release  all using the GFDL shared infrastructure (FMS) but have different FMS component models for atmosphere and/or land. 
We refer to these types as `MODEL_TYPE` in this guide: 
     
       
* `MOM_solo`: stand alone MOM ocean model.
* `MOM_SIS`: MOM coupled with GFDL ice model (SIS) besides null versions of atmosphere and land models.
* `EBM`: `MOM_SIS` coupled with `land_lad` and energy balanced atmosphere model 
* `ICCM`: `MOM_SIS` coupled with `land_lad` and `bgrid` atmosphere model in low resolution setup.  
* `CM2M`: GFDL CM2.1 model which is `MOM_SIS` coupled with `land_lad` and finite volume atmosphere model (with `am2` physics).
* `ESM2M`: GFDL Earth System Model which is `MOM_SIS` coupled with `land_lad2` and finite volume atmosphere model (with `am2` physics).
      
### To compile the models:
      
Find out what `MODEL_TYPE` you want to work on and what is `PLATFORM_ID` then
        
    $ cd $root_dir/exp    
    $ ./MOM_compile.csh --platform PLATFORM_ID --type MODEL_TYPE
      
NOTE: The Energy Balanced Model (`EBM`) cannot be compiled by the above procedure and a separate compile script is provided for it. 

### To run an experiment

Make sure you have a large enough working directory (`WORKDIR`) and made a symbolic link to it called `work` in your top directory, i.e.,    

    $ cd $root_dir
    $ ln -s WORKDIR work
            
Find out what test cases are available for a particular `MODEL_TYPE`

    $ cd $root_dir/exp
    $ ./MOM_run.csh --platform PLATFORM_ID --type MODEL_TYPE -h      

To run a `TEST_CASE`
                
    $ ./MOM_run.csh --platform PLATFORM_ID --type MODEL_TYPE  --experiment TEST_CASE --download_input_data

This will attempt to automatically download the necessary input data for your experiment. The [download instructions](http://mom-ocean.github.io/downloads) page has further information about downloading the input data.

### Notes

* The scripts have been tested fully only with Intel Fortran and PGI compilers on ia64 platform. They are partially tested  with pathscale compiler on x86\_64 and also gfortran4.3 compiler on Core2Duo processor. 
* Some of these test cases  require a large disk space to save the input data. Choose a partition with enough space (1-2 G) to untar the code and data bundels.
* IBM platform users might want to add the following line to the top of the run scripts 


        setenv LDR_CNTRL MAXDATA=0xD0000000@DSA 


* The compile script provides the basic capability with dynamic memory allocation. To use static memory allocation which might be faster on some platforms  you need to adjust the values of domain bounds properly according to the number of processors and layout. 
* The compile script use netcdf3 by default. If you want to use netcdf4 libraries instead you can do so by deleting the `-Duse_netCDF3` from the CPPs in compile script and then recompile.   

## How to prepare input data
   
The input data needed to run the selected experiments (tests) that are in this release need to be downloaded into the `data/` directory. This can done automatically or manually, See [download instructions](http://mom-ocean.github.io/downloads) for details. 
   
Note that data in `ASCII/`, `HISTORY/`, `RESTART/` directories are NOT needed for running experiments. They are the outputs of the experiments and are provided for the purpose of comparing your results with results produced at GFDL. Tools are provided so that users can create data from scratch for their own experiments. For more details refer to `src/preprocessing`.

## Examine the output
   
To keep the runscript simple all output files of a model run will be in the work directory. There are three types of output files:
     
* ascii file with `.fms.out` extension: the description of the setup of the run and verbose comments printed out during the run.
* restart files in `RESTART` directory: the model fields necessary to initialize future runs of the model.
* history files with `.nc` or `.nc.tar` extension: output of the model, both averaged over specified time intervals and snapshots.
   
The ascii file contains everything written to the screen during model execution. The total time for model execution as well as the times of separate modules are reported here. All `.tar` files should be decompressed for viewing. The decompress command is:
     
    tar -xvf filename.tar
     
Users will see result files in NetCDF format. Postprocessing tools such as Ferret, ncview, grads or matlab can be used to view data in these files.
The outputs of the selected experiments are available in the `data/` directory for the purpose of comparing your results with results produced at GFDL.
