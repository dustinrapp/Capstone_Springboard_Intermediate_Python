
## Milestone Capstone:  
# Utilizing Meteorological Data with Supervised Learning to Predict Snowfall Amounts at Ski Resort
**By Dustin Rapp**  

--  
--  

## Introduction
***
Complex terrain in mountainous areas often make predicting snowfall difficult with prognostic weather models - especially on specific slopes or mountainsides where extremely localized air flows may complicate such forecasts.  With accurate snow forecasts, ski resorts can optimize their snowfall making, grooming, and snow removal operations. An accurate short term snowfall forecast, even for a small segment of the mountain may likely assist a ski resort's operation.  The goal of this study is to get a glimpse into the potential of utilizing a supervised learning techniques with freely available surface and meteorological data to predict snowfall on a slope at Copper Mountain Ski Resort in Colorado.  Copper Mountain Ski Resort may be especially interested in such predictive models because of the unique access to government funded meteorological data being recorded near or onsite to their resort.  

The purpose of this report is to discuss data to be utilized and make general assessment regarding how well a supervised learning model might perform . 


## Data
The Copper Mountain ski resort is unique as there is an official SNOTEL National Resources Conservation Service monitoring station the north slope of Copper Mountain, where many popular ski runs are located. SNOTEL is a telemetry automated system of snowpack and related climate sensors in the Western United States. In addition to reporting hourly snowfall amounts, it also records temperature.  The Copper Mountain ski resort is also has an Colorado Department of Transportation Automated Weather Observing System (AWOS) which monitors a suite of hourly variables near the top of Copper Mountain.  Additionally, a National Weather Service Automated Surface Observing Station (ASOS) is located in Leadville, CO approximately 30 km to the northwest of Copper Mountain.  These three stations give a comprehensive meteorological dataset of surface variables in the vicinity of the Copper Mountain Resort.   **Table 1-1** gives a listing of all surface level meteorological variables by station. Hourly data for each station was downloaded for years 2005-2017 from online sources.  Data sources for each station are found in **Table 2**.  


A map showing the Copper Mountain SNOTEL site and the meteorological sites used in this assessment is also shown in **Figure 1**  
  
  ***
**Figure 1 - Map of Locations**  
  
<p float="left">
  <img src="https://github.com/dustinrapp/Capstone_Springboard_Intermediate_Python/blob/master/figs/KLXV.png" width="300" />
  <img src="https://github.com/dustinrapp/Capstone_Springboard_Intermediate_Python/blob/master/figs/KLXV.png" width="300" /> 
  <img src="https://github.com/dustinrapp/Capstone_Springboard_Intermediate_Python/blob/master/figs/KLXV.png" width="300" />
</p>
  

***


**Table 1 - Meteorological Variables by Station**  
  
| SNOTEL        | LXV ASOS       | KCCU AWOS      |        
|     :---:     |     :---:      |       ---:     |
| Temperature   | Temperature    | Temperature    |
| Snow Depth    | Dewpoint       | Dewpoint       |
|               | Wind Speed     | Wind Speed     |  
|               | Wind Direction | Wind Direction | 
|               | Cloud Cover    | Cloud Cover    | 
  

***

**Table 2 - Sources of Meteorological Data.** 

| Station | Data Source                                                           |
|-------- |---------------------------------------------------------------------- |
|  SNOTEL | National Resources Conservation Service (www.NRCS.gov)                |   
|LXV ASOS | National Climatic Data Center - ISHD Lite format (www.NCDC.gov)     |
|KCCU AWOS| National Climatic Data Center (ISHD format) (www.NCDC.gov)          |

## Data and Wrangling Cleaning

### Data Organization
Hourly surface data from each station, downloaded, organized and combined into a single  timeseries dataframes with UTM timestamps.  

The following cleanup steps were performed on this dataset:

 - While the KCCU and KXLV datasets were already in UTM time, the NRCS dataset was in local time and required conversion to UTM.   
 -  The KCCU and KXLV datasets are in the Integrated Surface Hourly Data (ISHD) format and did require some manipulation (e.g. divided by 10) to get values into typical units. 
 - Missing values (e.g. 9999 values) were translated to NaN values.
 -  Missing data for all variables was linearly interpolated for time periods where 3 hours or less of data was missing. 

The data was plotted to see if there were any extreme values warranting removal. It was noted that some of the KCCU data (especially temperature) did not demonstrate as much of a diurnal variation as the KXLV station.  These data are considered suspicious but were not removed from the dataset.  A more robust quality control of this dataset is outside the scope of this prelimiaryn study, but should be considered for future studies.

A small amount of anomalous data was observed in the SNOTEL snow depth data and was removed.  These physically unrealistic readings (e.g. spikes in some of the snow depth data or snowdepth reports which occur when temperatures did not support snowfall) were removed as well as extreme negative values. 



### Additional Calculations

**Pressure**  
Changes in pressure are often a predictive indicator of weather conditions (i.e. pressure drops often accompany strong storm systems), a twelve hour pressure change variable was added to the datset.  This was calculated by subtracting the 00:00 observation from the upcoming 12:00 observation.

**Snowfall**    
As the SNOTEL data only includes snow depth data instead of snowfall data, snowfall was calculated based on changes in snowdepth. Due to the sensitivity of the SNOTEL snow depth measurement sensors to external forces (e.g. debris, air pressure), snow depth data from the SNOTEL site appeared noisy for smaller snowstorms (i.e. less then 3 inches). To minimize the small scale perturbations found in the data, 12 hour snowfall totals were estimated at 00:00 UTC and 12:00 UTC and only 12-hr snowfall events where greater then or equal to 3 inches occurred were considered a snowfall event.  The snowfall data was then added to meteorological dataframe.   

Because only 00:00 and 12:00 snowfall observations were utilized in the analysis, all variables in the meteorological dataframe were reduced from hourly observations to twelve hour observations.  A new dataframe was created utilizing only 00:00 and 12:00 observations.

A table showing the total number of snowfall events, along with mean, max, and standard deviation of snowfall for each year is found in **Table 3**.  A timeseries plot showing the snowdepth, along with these snowfall events is found in **Figure 2**.
  

***


**Table 3  Statistics on Snowfall Events To be Used in Analysis**  

|   Year |   Number 12hr Snowfall Events >=3 |   Mean |   Median |   Max |   %Missing SnowDepth |   Std Deviation |
|--------|-----------------------------------|--------|----------|-------|----------------------|-----------------|
|   2006 |                                26 |    4.8 |      4   |  11   |                 0.69 |            1.87 |
|   2007 |                                29 |    3.9 |      3.3 |   6.5 |                 0.69 |            1.17 |
|   2008 |                                27 |    4.5 |      3.7 |   8   |                 0.69 |            1.85 |
|   2009 |                                27 |    4.3 |      4   |  13   |                 0.69 |            1.92 |
|   2010 |                                30 |    4.6 |      4   |   9   |                 0.69 |            1.75 |
|   2011 |                                32 |    4.3 |      4   |   7   |                 0.69 |            1.38 |
|   2012 |                                14 |    5.1 |      4   |  10   |                 0.69 |            2.29 |
|   2013 |                                32 |    4.3 |      4   |  12   |                 0.69 |            1.78 |
|   2015 |                                23 |    4.2 |      4   |   8   |                 0.68 |            1.24 |
|   2016 |                                32 |    4.9 |      4   |  16   |                 0.69 |            2.98 |
|   2017 |                                29 |    4.6 |      3   |  16   |                 0.69 |            2.81
  

***

**Insert Figure 2  Timeseries of snow depth and snowfall events**  
 


![alt text](https://github.com/dustinrapp/Capstone_Springboard_Intermediate_Python/\Capstone\MS_CS_report\figsfigs/snowdepth_snowfall.png)



## Linear Regression Analysis  

To assess snowfall prediction potential with Ordinary Least Squares model, a linear regression analysis was performed on each dataset.  For each potential variable, data was plotted against snowfall amounts which would occur over the next 12 hours.    Slope, standard error, R square values, along with p values were calculated for all variables.  A table showing results from this analysis are shown in **Table 3**.  The data are sorted by largest R value. Note that the variables with the best predictive capabilities are dewpoint, KCCU Wind Speed, and pressure changes. Though Cloud Cover does have higher R values as well, the p values and amount of data missing is also very high. While the R values are not notably high (all are less then 0.2), p values for dewpoint, 12-hr pressure change are less then 0.05, indicating that there may be some predictive skill with an OLS model.
  

***

**Table 3 - Output statistics from Linear Regression Analysis**  

|                                                |       Max |       Min^[] |          Mean^[] |   Slope |   Std Error |   R Value |   P-value |   % Missing |
|------------------------------------------------|-----------|-----------|---------------|---------|-------------|-----------|-----------|-------------|
| KCCU Dewpoint (deg C)                        |      0    |    -27    |     -9.7  |   0.085 |       0.03  |     0.171 |     0.005 |       21.9 |
| KCCU CloudCover (oktas)                     |      8    |      0    |      7.4  |   0.145 |       0.085 |     0.142 |     0.089 |       57.1 |
| LXV Dewpoint (deg C)                          |      2.8  |    -22.8  |     -8.1  |   0.074 |       0.031 |     0.134 |     0.019 |       9.5 |
| LXV CloudCover (oktas)                       |      8    |      0    |      6.8  |   0.088 |       0.059 |     0.122 |     0.136 |       55.0 |
| LXV 12hr Pressure difference (hp)                 |     13.35 |    -20.2  |     -3.0  |  -0.051 |       0.024 |    -0.122 |     0.033 |       10.4 |
|CMtn WindSpeed (m/s)                        |     20.1  |      0    |      7.7  |   0.061 |       0.034 |     0.112 |     0.073 |       24.3 |
| CMtn Temperature (degC)                     |      7    |    -21    |     -4.7  |   0.035 |       0.031 |     0.069 |     0.261 |       21.3 |
| CMtn Temperature (deg C)                        |      7.6  |    -18.7  |     -3.6  |   0.036 |       0.03  |     0.066 |     22.6 |       0.0     |
| SNTL  Temperature (deg C)                    |      7.6  |    -18.7  |     -3.6  |   0.036 |       0.03  |     0.066 |     0.226 |       0     |
| LXV Temperature (deg C)                       |     13.3  |    -17.2  |     -3.0 |   0.03  |       0.026 |     0.065 |     0.257 |       9.5 |
| LXV Wind Direction (deg)                      |    360    |      0.0    |    184.1    |  -0.001 |       0.001 |    -0.055 |     0.336 |       0.095 |
| CMtn Wind Direction (deg)                    |    360    |      0.0    |    236.5    |   0.002 |       0.002 |     0.044 |     0.48  |       0.243 |
|LXV Pressure (hp)                            |   1028.5  |    983.3  |   1005.5     |  -0.011 |       0.015 |    -0.043 |     0.457 |       10.1 |
| LXV WindSpeed m/s                          |     13.4  |      0.0    |      3.7  |   0.005 |       0.054 |     0.005 |     0.926 |       9.5 |

## Conclusion
While not large, there are some significant relationships between some meteorological variables and snowfall amount when snowfall does occur.  It is anticipated that there may be some 12-hr snowfall predictive ability predicting snowfall utilizing a very simple Ordinary Least Squares model  with these meteorological measurements.    Additional data sources, such as upper air measurements could be utilized to improve predictive ability.  

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTcwNzcxNzk4MCw3OTY1ODg2MjksODg3NT
Q3NjIyLC0yMDk3NDQ3OTc2LC02MTgxNjgyNzcsNzExNTU3MjQx
LC0xMzE0MjQzNjQwLDE3ODg5OTYxMDAsLTE5MDQ4MjcyMDgsLT
ExOTUxMjYyODUsNjkwNDY1MTE0LDIxNDIzNTM0NzMsLTY5OTgw
NTEzNyw3NTg3MTE0OF19
-->