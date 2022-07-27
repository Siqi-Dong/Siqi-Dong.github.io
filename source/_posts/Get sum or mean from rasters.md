---
title: Get sum or mean from rasters
date: 2020-10-21 21:38:00
author: Siqi Dong
categories: Raster
---

```python
# -*- coding: utf-8 -*-
import arcpy
from arcpy import env
from arcpy.sa import *

arcpy.CheckOutExtension("spatial")
env.overwriteOutput= 1

# get Sum
def getSumOfRasters(rasterPath,resultSavePath):
    arcpy.env.workspace = rasterPath
    rasterlist = arcpy.ListRasters("*", "TIF")
    baseraster=rasterlist[0]
    baseraster2= Con(IsNull(baseraster),0,0)
    for raster in rasterlist:
        baseraster2 = baseraster2 + raster
    baseraster2.save(resultSavePath)
    print "finish!"

# get Mean
def getMeanOfRasters(rasterPath,resultSavePath):
    arcpy.env.workspace = rasterPath
    rasterlist = arcpy.ListRasters("*", "TIF")
    baseraster=rasterlist[0]
    baseraster2= Con(IsNull(baseraster),0,0)
    for raster in rasterlist:
        baseraster2 = baseraster2 + raster
    meanraster=baseraster2/len(rasterlist)
    meanraster.save(resultSavePath)
    print "finish!"
 ```