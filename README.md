# CO_ML_Predictions

A repo containing the modified Aquasat framework code create a matched In-situ/remote sensing database to be used for training Machine Learning Algorithms to predict water quality in the Colorado River Basin.

Dissertation title “A Framework for Pairing In-situ Water Quality Measurements with Remote Sensing Imagery: A Case Study of Estimating Total Suspended Sediment Trends in The Upper Colorado River Basin Using Machine Learning Algorithms”

By: Daniel Dominguez

Email contact: ddominguez2293@gmail.com

Code available here:
University of Glasgow

MSc candidate for the Sustainable Water Environments Program

The following code is based on the Aquasat framework (1-10) as published by Matthew Ross and is available at the following URL: https://github.com/GlobalHydrologyLab/AquaSat

The modeling and analysis are modeled after the “riverTSS” repository published by John Gardner and is available at the following URL: https://github.com/johngardner87/riverTSS

Note that the original Aquasat framework was written four years ago, and thus, it needed many updates. Since the python codes initially worked with Python 2 and R 3.0 using the reticulate package, it was decided to split these codes and run them individually. Thus, you will need to run the Python codes using a Python 2 instance such as in google collab, with instructions here on how to accomplish this: 
https://stackoverflow.com/questions/61475248/how-can-i-use-python-2-on-google-colab

Because the project used only publicly available data such as that available from the Water Quality Portal and used cloud computing available from Google Earth Engine, it is unnecessary to provide all the smaller datasets used in between steps. To fully recreate the results, only a handful of data is needed;
The first is the final joined dataset between in-situ TSS measurements to the band values extracted from satellite images. This csv is the input file for all the machine learning algorithms labeled as the following;
sr_wq_rs_join.csv

Next is a site list (feather format, you will need “feather” R-package to read in) that will be converted into a shapefile to draw the partition of data within/outside the Upper Colorado River Basin. 
unique_site_visible_inv.feather
, or you can use the Final sites list if available for visual representation in the TSS_Figures code.
Final_Sites.feather

To separate the unique site list, you need to download the shapefile with the HUC-2 Upper Colorado River Basin Boundary from the USGS Watershed Boundary Dataset. (Not included because it is publicly available and a large file) at: https://apps.nationalmap.gov/downloader/

Instructions on how to easily do so are available here: https://www.hec.usace.army.mil/confluence/hmsdocs/hmsguides/gis-tutorials-and-guides/downloading-elevation-and-watershed-boundary-data

If you do not want to run all the models and instead only want to repeat the analysis, you will only need the Random Forest Predictions files (feather format) or any of the other result files included.
rf_CO_Final_Predictions.feather

Software versions:
R-Studio (v2022.07.00)
Python (v2.0)

Code Instructions

The following is the workflow in which to run the code to replicate the project and a short description of what is accomplished in each script

1.	TSS.RMD
a.	The first script pulls the Total Suspended Sediment (TSS) from the Water Quality Portal by individual state, then concatenates and writes another file for easier calling and working. 

2.	TSS_processing.RMD
a.	This script will focus on processing the TSS data and analytics on how the data was processed and why certain steps were taken. Then after the final data processing in this script, a site list will be created and then joined to a full list of sites in each state with additional data from the WQP.

3.	TSS_munge.RMD
a.	The next steps will be to clean and join the other data types not part of this project, but the same code will bind and join all the data and clean the dates. 

4.	Unity.RMD
a.	A script that standardizes the time of observation as per the methodology and gets rid of duplicate observations. 

5.	Clouds.ipynb
a.	Before running this piece of code, it is important that you load a python2 work environment due to depreciated packages in python3 and mount your google drive to the session. The first chunk of code is all the functions that will be used for all the python scripts. It is easier to maintain them all together as some will be reused. The second chunk will connect your python session to the google earth engine  API and must be rerun at the start of individual sessions. The third chunk will extract the cloud scenes for all of the sites and write them to a csv in your local drive.

6.	FlatSiteinventory
a.	This script begins with merging the clouds outputs; then, the coordinate reference systems are standardized to ensure that all sites have a harmonized projection. Next, make sure to run the tempSiteWater to get the water pixel percentage for each site. The code then uses the tempSiteWater results to filter out pixels that don’t have enough water percentage to reduce the inclusion of mixed pixels. 
b.	You will have to run the next script partly through this one, so ensure that you don’t run all of it at once. 

7.	tempSiteWater.ipynb
a.	In this script the unique sites are called into python, and then a peckel water mask is used to retrieve the water pixel percentage. 

8.	WQPOverlaps.RMD
a.	This script will merge the visible sites to the path and row (WRS data) for the landsat satellites. The users need to download the WRS shapefile at this address. This code is computer power intensive! Ensure that you have proper RAM power to run this step. For reference with 8gb of RAM the code kept hanging in the 4th chunk to work around this, it was broken into subsequent steps that you can load and continue if you don’t have extensive computer power available. 

9.	FlatOverpass.RMD
a.	In this step, you will now extract the matching satellite scenes from the cloud cover database that you previously created. This step will create the +/-1 pairs of in-situ to remote sensing images.

10.	OverpassSplit.RMD
a.	 This script will split the matched pairs based on tile for easier retrieval and management when extracting the band values. 
b.	You will need to upload the data to a google drive to be read in for the next step

11.	SR_match.ipynb
a.	This script reads in the scenes and extracts the matching pixel values per the methodology and will then write said values into individual csv sheets in a google drive of your choice. 

12.	SR_WQ_RS_Join.RMD
a.	You will now finally join the in-situ water quality measurements to the extracted pixel values. You will first concatenate the individual path row csvs into one database and then join that to the water quality database previously created. 

13.	TSS_modeling.RMD
a.	This script will read in the matched pairs, and after some data filtering, it will create the data partitions as described in the methodology. This script uses the most current version of R as some packages are only available in the most recent versions. If you are not using the most current version, you will have to find and download individual packages such as “RamdomForest”. 
b.	After you partition said data, individual machine learning models will be created using the “CARET” package. The individual prediction databases and summary statistics are created for each algorithm. 

14.	TSS_Figures.RMD
a.	Lastly, this script will create final figures starting with a map which data was downloaded from and then running through the statistical analysis of the water quality predictions across the longitudinal profile of the Upper Colorado River Basin. 
