---
title: Get extent of raster or shp
date: 2020-10-22 11:11:00
author: Siqi Dong
categories: DataProperties
---

```python
# -*- coding: utf-8 -*-
import arcpy
import os
from arcpy import env
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
                ymin.append( result.YMin)
                ymax.append(result.YMax)
        xmin.sort()
        ymin.sort()
        xmax.sort(reverse=True)
        ymax.sort(reverse=True)
        return str(xmin[0])+" "+str(ymin[0])+" "+str(xmax[0])+" "+str(ymax[0])
 ```
