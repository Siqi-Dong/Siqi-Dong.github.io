---
title: Get the water depth and volume of flood inundation by water level
author: Siqi Dong
date: 2020-10-28 22:58:53
categories: DataAnalysis
---
 <div style="margin: 0 auto;overflow: hidden;">
 <img src="WaterDepth.png" alt="" />
 </div>
 
```python
# -*- coding: utf-8 -*-
import arcpy
from arcpy import env
from arcpy.sa import *
arcpy.CheckOutExtension("Spatial")
# Workspace
env.workspace = ""
env.overwriteOutput = True
# Dem data path
dem="XXX\\xx.tif"
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
            s=rstArray.item(rowNum, colNum)
            sum=sum+s
    return sum

# Calculation of the volume and extent of inundation based on water levels
def getVandRaster(cellxsize,cellysize,h,V_threshold):
    inRaster = Raster(dem)
    # Water depth raster
    deepRaster = Con(inRaster<=h, h-inRaster, 0)
    deepRaster2 = SetNull(deepRaster == 0,deepRaster)
    # Volume of water
    Vraster=deepRaster*cellxsize*cellysize
    Vraster2=Con(IsNull(Vraster),0,Vraster)
    # Sum
    V=getValueSum(Vraster2)
    # Threshold of volume
    if V>=V_threshold:
        deepRaster2.save("Level"+str(h)+"m_Volume"+str(V)+"m3.tif")
        return 1
    else:
        return 0

h=mindem
step=0.1
while h<maxdem:
    if getVandRaster(cellXSzie,cellYSzie,h)==1:
        break
    else:
        h=h+step
```