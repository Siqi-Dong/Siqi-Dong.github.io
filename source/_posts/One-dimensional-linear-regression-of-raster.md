---
title: One-dimensional linear regression of raster
date: 2020-10-22 23:13:18
author: Siqi Dong
categories: DataAnalysis
---
The trend analysis is based on one-dimensional linear regression trend analysis, which can simulate the trend of each raster, and then integrated to reflect the spatial and temporal changes of the whole region, calculated by the following formula.

$$
Slope=\frac{n×\sum_{i=1}^{n}{(i×M_{r,i})-(\sum_{i=1}^{n}i)(\sum_{i=1}^{n}M_{r,i})}}{n×\sum_{i=1}^{n}i^2-(\sum_{i=1}^{n}i)^2}
$$

where *Slope* is the slope of the pixel *r* regression equation, the variable *i* is the time series number from 1 to *n*, *n* is the time span, and *M<sub>r,i</sub>* is the maximum *r* value in the nth period.
If *Slope* > 0, it means that the change in *r* over time shows an upward trend, and the larger the *Slope* value, the more obvious the upward trend, and conversely, if *Slope* < 0, it means that the change in *r* over time shows a downward trend.
 
 
```python
# -*- coding: utf-8 -*-
import arcpy
from arcpy.sa import *
arcpy.CheckOutExtension("Spatial")

def rasterSlope(rasterFolder,resultSlopeSavePath):
	folderin = rasterFolder
	arcpy.env.workspace = folderin
	rlist = arcpy.ListRasters()
	N=len(rlist)
	i = 0
	sum1 = 0
	sum2 = 0
	sum3 = 0
	sum4 = 0
	for r in rlist:
		i += 1
		print(i)
		sum1 += i * Raster(r)
		sum2 += Raster(r)
		sum3 += i * i
		sum4 += i
		print(r)
	result = (N * sum1 - ((N + 1) * N / 2) * sum2) / (N * sum3 - sum4 * sum4)
	result.save(resultSlopeSavePath)
```