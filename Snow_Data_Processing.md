SNOTEL Data cleaning steps:  
1.) Date and Time columns were combined into single column representing the Date and Time.  This was set as the dataframe index.  
2.) The asfreq(freq='H') method was performed to insure there were no missing hourly observations in the snowdepth data.  Missing observations were set to -99.9, the default missing value found in the SNOTEL data.  
3.)-99.9 values in the raw snowdepth data were set to NaN.  
4.) Values in the snowdepth data which were less then 0 or greater then 100 were set to NaN.  
5.) Consecutive NaN values were then interpolated using pandas interpolate function for short timeframes of NaN values.  If more then 4 consecutive hours were NaN, the values were not changed.  
6.) SNOTEL data contains hourly snowdepth data, rather then snowfall data. For simplicity, 12-hr snowfall totals were calculated at 00:00 and 12:00.  The 00:00 snowfall total was calculated by subtracting the previous days 12:00 snowdepth measurement from the 00:00 snow depth measurement.  The 12:00 snowfall total was calculated by subtracting the previous 00:00 snowdepth measurement from the 12:00 measurement. 7.) Set summer months snowdepth value to 0.  
8.) Only 12-hr snowfall totals geater then 1" due to coarse resolution of snowdepth data.

