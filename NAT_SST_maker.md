# Weather@home 2014 ancillary files - natural SSTs

Author: Mitchell Black (mtblack@student.unimelb.edu.au)

###1. Source CMIP5 data

Working directory: `@raijin: /short/w42/mtb563/wah_delta-SSTs.dir/`


List of files used for calculating delta-SSTs: [wah_deltaSST_infiles.csv](https://github.com/MitchellBlack/PhD_Code/blob/73908d41b8b37b0a4cb10b89bfc62efe7d522c82/working_files.dir/wah.dir/wah_deltaSST_infiles.csv)

> | Model   | Ensemble members (..i1p1)  |
> | :----- | :------  |
> | CCSM4 | r1, r2, r4, r6 |
> | CESM1-CAM5* | r1, r2, r3 | 
> | CNRM-CM5 | r1, r2, r3, r4, r5, r8 |
> | CanESM2 | r1, r2, r3, r4, r5 |
> | GFDL-CM3 | r1, r3, r5 |
> | GISS-E2-H | r1, r2, r3, r4, r5 |
> | GISS-E2-R | r1, r2, r3, r4, r5 |
> | HadGEM2-ES** | r1, r2, r3, r4 |
> | IPSL-CM5A-LR | r1, r2, r3 |
> | IPSL-CM5A-MR | r1, r2, r3 |
> | MIROC-ESM | r1, r2, r3 |

*_Removed as no sea ice files._

**_Dec 2005 did not exist for HadGEM2-ES. Instead, used period Dec-1995 to Nov-2005._

1. Join any split files (e.g. 1981-2000, 2000-2005) using `cdo mergetime` to get one continuous file per ensemble member (for each of HIST and HISTNAT).
	> File name convention: *tos_Omon_{model}_{historical/historicalNat}_r{1,2,3...}i1p1_199601-200512.nc*
	
2. Remove any unwanted files from directory. Remaining files must be the individual ensemble members ready for averaging.

3. Run the following script within the working directory:
	> e.g. `@raijin: /short/w42/mtb563/wah_delta-SSTs.dir/CCSM4.dir`:
	>
	> ` bash ~/PhD_Code/data_processing.dir/calc_deltaSST_CMIP5.sh CCSM4`
	>
	> Script: [calc_deltaSST_CMIP5.sh](https://github.com/MitchellBlack/PhD_Code/blob/b7dadd2a7f05cae51dda8d5583344f6bac052eb9/data_processing.dir/calc_deltaSST_CMIP5.sh)

4. Repeat for each CMIP5 model.

5. I repeated the above process for the sea ice files for each model. List of sice files: [wah_deltaSST_sice_infiles.csv](https://github.com/MitchellBlack/PhD_Code/blob/66750b5891d3c92945af0091efeda6dcb63c1d91/working_files.dir/wah.dir/wah_deltaSST_sice_infiles.csv). 

	> File name 	convention *sic_OImon_{model}_{historical/historicalNat}_r{1,2,3...}i1p1_199601-200512.nc*
	> 
	> ` bash ~/PhD_Code/data_processing.dir/calc_deltaSST_CMIP5_sice.sh CCSM4`
	>
	> Repeat for each CMIP model.
	>
	> Script: [calc_deltaSST_CMIP5_sice.sh](https://github.com/MitchellBlack/PhD_Code/blob/6403ac5d5bc4617aa57e8cfdcae58352dd217b15/data_processing.dir/calc_deltaSST_CMIP5_sice.sh).

**Final delta-SST files:** All of the CMIP5 delta-SSTs are available here: [download link](https://www.dropbox.com/s/i5u57x2vl1rw9e6/deltaSST_CMIP5.zip?dl=0).

###2. Generate natural sea ice file

I will use Dec 1987-- Nov 1988 for NH sea ice extent as this time had the maximum extent in observations [see link here](http://nsidc.org/arcticseaicenews/files/2014/03/Figure3.png) [and here](http://nsidc.org/arcticseaicenews/files/2014/10/monthly_ice_NH_09.png).

I will use Dec 2013 -- Nov 2014 for SH sea ice extent as this time had the maximum extent in observations [see link here](http://arctic.atmos.uiuc.edu/cryosphere/antarctic.sea.ice.interactive.html). 

1. Calculate 5-day average OSTIA SICE:

	> e.g., `bash ~/Documents/University.dir/PhD.dir/CPDN.dir/calc_5daymean_OSTIA.sh /Users/mtblack/Documents/University.dir/PhD.dir/Data.dir/OSTIA.dir/OSTIA_SICE_1985_01-2014_11_N96_daily.nc 1987 SICE`

2. Select time steps for model year:

	> `cdo mergetime -selmon,12 OSTIA_SST_N96_2013_5daymean.nc -selmon,1,2,3,4,5,6,7,8,9,10,11,12 OSTIA_SST_N96_2014_5daymean.nc OSTIA_SST_N96_2013_12_2014_12.nc`

	> `cdo mergetime -selmon,12 OSTIA_SICE_N96_2013_5daymean.nc -selmon,1,2,3,4,5,6,7,8,9,10,11 OSTIA_SICE_N96_2014_5daymean.nc OSTIA_SICE_N96_2013_12_2014_11.nc`
	
	> Dec 2014 not available, so repeat Dec 2013 and append to end of file:
	>
	> `cdo selmon,12 OSTIA_SICE_N96_2013_5daymean.nc OSTIA_SICE_N96_2014_12.nc`
	>
	> `cdo cat OSTIA_SICE_N96_2013_12_2014_11.nc OSTIA_SICE_N96_2014_12.nc OSTIA_SICE_N96_2013_12_2014_12.nc`

3. Combine NH sea ice field  (1987-1988) with SH sea ice field  (2013-2014):

	> `cdo setclonlatbox,0.,0.,360.,-90.,-1. OSTIA_SICE_N96_1987_12_1988_12.nc OSTIA_SICE_N96_1987_12_1988_12_SH_zero.nc`
	>
	> `cdo setclonlatbox,0.,0.,360.,1.,90. OSTIA_SICE_N96_2013_12_2014_12.nc OSTIA_SICE_N96_2013_12_2014_12_NH_zero.nc`
	>
	> *Go to +-1, rather than zero, so that land mask at equator is maintained*
	>
	> `cdo add OSTIA_SICE_N96_1987_12_1988_12_SH_zero.nc OSTIA_SICE_N96_2013_12_2014_12_NH_zero.nc  OSTIA_SICE_N96_natural.nc`

4. Interpolate SICE fields over land

	`ncl ncl_interp_OSTIA_SICE_overland.ncl`

	>  **Code**: [ncl_interp_OSTIA_SICE_overland.ncl](https://github.com/MitchellBlack/CPDN/blob/master/ncl_interp_OSTIA_SICE_overland.ncl)
	> 
	>  **Operation**: change input and output name in scripts
	> 
	>  **Output**: OSTIA_SICE_N96_natural_interp_land.nc

5. Adapt land sea mask of HadAM3P model

	HadAM3P land-sea mask: [lsm_n96_add.nc](https://www.dropbox.com/s/j1dgrxdny0jhfmd/lsm_n96_add.nc?dl=0)

	SICE:

	`cdo -add lsm_n96_add.nc -invertlat OSTIA_SICE_N96_natural_interp_land.nc OSTIA_SICE_N96_natural_landmask.nc`

	>Final SICE file (before conversion to ancil format): [OSTIA_SICE_N96_natural_landmask.nc](https://www.dropbox.com/s/ia4uo1w5cu3qgm0/OSTIA_SICE_N96_natural_landmask.nc?dl=0)


###3. Calculate natural SST fields

1. Amend delta-SST fields to take into consideration sea ice location:

> This step uses the script [calc_deltaSST_SICE.py](https://github.com/MitchellBlack/PhD_Code/blob/ffac7015328eae07e154d587769b4cae55674d91/data_processing.dir/calc_deltaSST_SICE.py) and the file [lsm_n96.nc](https://www.dropbox.com/s/9idyh1yiljhzg05/lsm_n96.nc?dl=0).
> 
> **Execution:** `python calc_deltaSST_SICE.py {CMIP_deltaSST} {CMIP_sice_HIST} {CMIP_sice_HISTNAT} {OSTIA_sice_OBS} {CMIP_modelname} {landmask}` [-h for help string]
> 
> *The OSTIA sice file must be the monthly average field: `cdo monmean {in: sice_daily} {out: sice_monthly}`*
>
> `cdo monmean OSTIA_SICE_N96_natural_landmask.nc OSTIA_SICE_N96_natural_landmask_monmean.nc`
>
> Calculate smoothed delta-SST field:
> 
> `python ~/Documents/University.dir/PhD.dir/Code.dir/data_processing.dir/calc_deltaSST_SICE.py tos_Omon_CCSM4_deltaSST_1996-2005_N96.nc sic_OImon_CCSM4_historical_ensmean_monmean_1996-2005_N96.nc sic_OImon_CCSM4_historicalNat_ensmean_monmean_1996-2005_N96.nc ../OSTIA_SICE_N96_natural_landmask_monmean.nc CCSM4_ ../lsm_n96.nc`
>
> **Output**: deltaSST_CCSM4_N96.nc
>
> ...repeat for each CMIP model. These working files are archived on MITCHDATA for future reference. Use cdo ensmean to calculate MMM.
>
> **Smoothed delta-SST files:** [download link](https://www.dropbox.com/s/yaw5ifvogiydeac/WAH_deltaSST.dir.zip?dl=0).





