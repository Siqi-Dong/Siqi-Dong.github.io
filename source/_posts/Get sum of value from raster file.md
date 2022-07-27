---
title: Get sum of value from raster file
date: 2020-10-16 19:26:32
author: Siqi Dong
categories: Raster
---

```python
import arcpy
from arcpy.sa import *
arcpy.CheckOutExtension("Spatial")

def getValueSum(rasterPath):
    raster=arcpy.Raster(rasterPath)
    raster2 = SetNull(raster == 0,raster)
    sum=0
    rstArray = arcpy.RasterToNumPyArray(raster2)
    rows, cols = rstArray.shape
    for rowNum in xrange(rows):
        for colNum in xrange(cols):
            s=rstArray.item(rowNum, colNum)
            sum=sum+s
    return sum
```