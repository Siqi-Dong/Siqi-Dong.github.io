---
title: Non source inundated analysis based on specified amount of water
author: Siqi Dong
date: 2020-11-11 20:39:59
categories: DataAnalysis
---

```python
# -*- coding: utf-8 -*-
import arcpy
import copy
from arcpy import env
from arcpy.sa import *

# Check out the ArcGIS Spatial Analyst extension license
arcpy.CheckOutExtension("Spatial")
env.workspace = "D:\\NonSourceInundation\\result"
env.overwriteOutput = True
# original_para
dem="D:\\NonSourceInundation\\dem.tif"

# Set environment settings
cellXSzie = float(arcpy.GetRasterProperties_management(dem, "CELLSIZEX")[0])
cellYSzie = float(arcpy.GetRasterProperties_management(dem, "CELLSIZEY")[0])
maxdem=float(arcpy.GetRasterProperties_management(dem,"MAXIMUM")[0])
mindem=float(arcpy.GetRasterProperties_management(dem,"MINIMUM")[0])

def getValueSum(raster):
    sum=0
    rstArray = arcpy.RasterToNumPyArray(raster)
    rows, cols = rstArray.shape
    for rowNum in xrange(rows):
        for colNum in xrange(cols):
            v=rstArray.item(rowNum, colNum)
            sum=sum+v
    return sum

#  Calculate volume and extent of inundation based on flood levels
def getVandRaster(cellxsize,cellysize,h):
    inRaster = Raster(dem)
    deepRaster = Con(inRaster<=h, h-inRaster, 0)
    deepRaster2 = SetNull(deepRaster == 0,deepRaster)
    Vraster=deepRaster*cellxsize*cellysize
    Vraster2=Con(IsNull(Vraster),0,Vraster)
    V=getValueSum(Vraster2)
    # Set the flood amount (m3)
    if V>=1921911.658:
        deepRaster2.save("Level"+str(h)+"m_V"+str(V)+"m3.tif")
        print "Level"+str(h)+"m_V"+str(V)+"m3.tif"
        return 1
    else:
        return 0

waterLevel=mindem
# Rate of water level rise (m)
step=0.01
while waterLevel<maxdem:
    if getVandRaster(cellXSzie,cellYSzie,waterLevel)==1:
        break
    else:
        waterLevel=waterLevel+step
```
 <div style="margin: 0 auto;overflow: hidden;">
 <img src="NonSourceFlood.png" alt="" />
 </div>