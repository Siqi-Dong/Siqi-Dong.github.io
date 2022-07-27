---
title: Source inundated analysis
author: Siqi Dong
date: 2020-11-11 19:39:44
categories: DataAnalysis
---
 
```python
# -*- coding: utf-8 -*-
import arcpy
from arcpy import env
from arcpy.sa import *
import copy
arcpy.CheckOutExtension("Spatial")
env.overwriteOutput = True

def getLocationOfPointInGrid(rasterPath,pointPath,worksapceTempPath):
    env.workspace = worksapceTempPath
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

def  getfloodcell(waterLevel,sourcerow,sourcecol,elevationCell,rows,cols):
    elevationCellCopy = copy.deepcopy(elevationCell)
    floodbufferArray=[]
    floodbufferArray .append(elevationCellCopy[sourcerow,sourcecol])
    while(len(floodbufferArray)!=0):
        rowfirst=floodbufferArray[0][0]
        colfirst=floodbufferArray[0][1]
        elevationCellCopy[(rowfirst,colfirst)][3] = True
        floodbufferArray.pop(0)
        for row in range(rowfirst - 1, rowfirst + 2):
            for col in range(colfirst - 1, colfirst + 2):
                if(row<rows and col<cols and row>=0 and col>=0):
                    elevationinfo=elevationCellCopy[(row,col)][2]
                    if elevationinfo<=waterLevel and elevationCellCopy[(row,col)][3]==False:
                        elevationCellCopy[(row,col)][3] = True
                        floodbufferArray.append((row, col))
    return elevationCellCopy

# original_para
worksapce_path = "D:\\sourceFlood\\temp"
dem = "D:\\sourceFlood\\DEM.tif"
seed = "D:\\sourceFlood\\seed.shp"

# Convert elevation raster to array
elevationCell={}
cellXSzie = arcpy.GetRasterProperties_management(dem, "CELLSIZEX")
cellYSzie = arcpy.GetRasterProperties_management(dem, "CELLSIZEY")
leftPosition = arcpy.GetRasterProperties_management(dem, "LEFT")
topPosition = arcpy.GetRasterProperties_management(dem, "TOP")
bottomPosition = arcpy.GetRasterProperties_management(dem, "BOTTOM")
maxdem=arcpy.GetRasterProperties_management(dem,"MAXIMUM")
mindem=arcpy.GetRasterProperties_management(dem,"MINIMUM")
rstArray = arcpy.RasterToNumPyArray(arcpy.Raster(dem))

# Number of rows and columns of dem
rows, cols = rstArray.shape
print rows,cols
for rowNum in xrange(rows):
    for colNum in xrange(cols):
        # Set elevation raster pixel, initialize to no flooding
        elevationCell[(rowNum, colNum)] = [rowNum,colNum,rstArray.item(rowNum, colNum), False]

RowCol=getLocationOfPointInGrid(dem,seed,worksapce_path)
# Setting the inundation level 100
floodelevationCell=getfloodcell(100,RowCol[0],RowCol[1],elevationCell,rows,cols)
# Convert array to  raster
for rowNum in xrange(rows):
    for colNum in xrange(cols):
        rstArray[(rowNum, colNum)] = floodelevationCell[(rowNum, colNum)][3]
result = arcpy.NumPyArrayToRaster(rstArray, arcpy.Point(leftPosition[0], bottomPosition[0]), float(cellXSzie[0]),float(cellYSzie[0]))
result.save("D:\\sourceFlood\\result.tif")
```
 <div style="margin: 0 auto;overflow: hidden;">
 <img src="SourceFlood.png" alt="" />
 </div>