---
title: Multi-field IDW interpolation
date: 2020-10-21 22:36:17
author: Siqi Dong
categories: BatchDataAnalysis
---

```python
# -*- coding: utf-8 -*-
import arcpy
import os
import sys
from arcpy.sa import *
from arcpy import env
reload(sys)
sys.setdefaultencoding('utf-8')
arcpy.CheckOutExtension("Spatial")
env.overwriteOutput=1

def getExtentOfRasterOrShp(filePath):
    folder_path, file_name = os.path.split(filePath)
    arcpy.env.workspace =folder_path
    ext=file_name[-4:]
    if ext==".tif":
        extent=arcpy.Raster(filePath).extent
        return str(extent.XMin)+" "+str(extent.YMin)+" "+str(extent.XMax)+" "+str(extent.YMax)
    elif ext==".shp":
        xmin=[]
        xmax=[]
        ymin=[]
        ymax=[]
        with arcpy.da.SearchCursor(filePath, 'SHAPE@') as cursor:
            for row in cursor:
                result = row[0].extent
                xmin.append(result.XMin)
                xmax.append(result.XMax)
                ymin.append(result.YMin)
                ymax.append(result.YMax)
        xmin.sort()
        ymin.sort()
        xmax.sort(reverse=True)
        ymax.sort(reverse=True)
        return str(xmin[0])+" "+str(ymin[0])+" "+str(xmax[0])+" "+str(ymax[0])

def MultiFieldIDWInterpolation(extentShp,shpFilePath,fieldArray,cellSize,power,searchRadius,resultSaveFolder):
    arcpy.env.workspace =resultSaveFolder
    # Get extent
    extent=getExtentOfRasterOrShp(extentShp)
    arcpy.env.extent=extent
    fields = arcpy.ListFields(shpFilePath)
    for f in fields:
        if f.name in fieldArray:
            fieldname=fieldArray[fieldArray.index(f.name)]
            # Execute IDW
            outIDW = Idw(shpFilePath, fieldname,cellSize, power, searchRadius)
            # Save the output
            outIDW.save(resultSaveFolder+"\\"+fieldname+"IDW.tif")
    print "done!"
 ```