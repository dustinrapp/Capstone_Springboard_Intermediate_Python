Data cleaning steps:  
1.) the asfreq(freq='H') method was performed to insure there were no missing hourly observatoions in the snowdepth data  
2.)-99.9 values in the raw snowdepth data were set to NaN.  
3.) Values less then 0 or greater then -99.9 were set to NaN.  
4.) Consecutive NaN values were then interpolated using pandas interpolate function for short timeframes of NaN values.  If more then 4 consecutive hours were NaN, the values were not changed.  
5.) SNOTEL data contains hourly snowdepth data, rather then snowfall data. For simplicity, 12-hr snowfall totals were calculated at 00:00 and 12:00.  The 00:00 snowfall total was calculated by subtracting the previous days 12:00 snowdepth measurement from the 00:00 snow depth measurement.  The 12:00 snowfall total was calculated by subtracting the 00:00 snowdepth measurement from the 12:00 measurement.  
6.) Only keep 12-hr snowfall totals geater then 1" due to coarse resolution of snowdepth data.
