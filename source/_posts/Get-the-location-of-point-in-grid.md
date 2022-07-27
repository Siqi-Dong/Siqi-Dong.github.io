---
title: Get the location of point in grid
date: 2020-10-23 17:56:09
author: Siqi Dong
categories: Raster
---

```python
# -*- coding: utf-8 -*-
import arcpy
from arcpy import env
from arcpy.sa import *

arcpy.CheckOutExtension("Spatial")
env.overwriteOutput = True

def getLocationOfPointInGrid(rasterPath,pointPath,worksapcePath):
    env.workspace = worksapcePath
    cellXSzie = arcpy.GetRasterProperties_management(rasterPath, "CELLSIZEX")
    cellYSzie = arcpy.GetRasterProperties_management(rasterPath, "CELLSIZEY")
    leftPosition = arcpy.GetRasterProperties_management(rasterPath, "LEFT")
    bottomPosition = arcpy.GetRasterProperties_management(rasterPath, "BOTTOM")
    # Converting raster to array
    rstArray = arcpy.RasterToNumPyArray(arcpy.Raster(rasterPath))
    # Number of rows and columns of raster
    rows, cols = rstArray.shape
    # Create row and column number grid
    rowcell = {}
    colcell = {}
    for rowNum in xrange(rows):
        for colNum in xrange(cols):
            rowcell[(rowNum, colNum)] = [rowNum]
            colcell[(rowNum, colNum)] = [colNum]
    for rowNum in xrange(rows):
        for colNum in xrange(cols):
            rstArray[(rowNum, colNum)] = rowcell[(rowNum, colNum)][0]
    result = arcpy.NumPyArrayToRaster(rstArray, arcpy.Point(leftPosition[0], bottomPosition[0]), float(cellXSzie[0]),float(cellYSzie[0]))
    result.save("row.tif")
    dsc = arcpy.Describe(pointPath)
    coord_sys = dsc.spatialReference
    arcpy.DefineProjection_management("row.tif", coord_sys)
    for rowNum in xrange(rows):
        for colNum in xrange(cols):
            rstArray[(rowNum, colNum)] = colcell[(rowNum, colNum)][0]
    result = arcpy.NumPyArrayToRaster(rstArray, arcpy.Point(leftPosition[0], bottomPosition[0]), float(cellXSzie[0]),float(cellYSzie[0]))
    result.save("col.tif")
    dsc = arcpy.Describe(pointPath)
    coord_sys = dsc.spatialReference
    arcpy.DefineProjection_management("col.tif", coord_sys)
    # Get the row and column of the point
    inPointFeatures = pointPath
    inRasterList = [["row.tif", "row"], ["col.tif", "col"]]
    # Execute ExtractValuesTo Points
    ExtractMultiValuesToPoints(inPointFeatures, inRasterList, "")
    cursor = arcpy.SearchCursor(inPointFeatures)
    rowN = ''
    colN = ''
    for rowcol in cursor:
        rowN = int(rowcol.row)
        colN = int(rowcol.col)
    return [rowN, colN]
```