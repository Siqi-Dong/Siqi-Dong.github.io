---
title: Get the water flow path based on DEM
author: Siqi Dong
date: 2020-10-24 21:28:15
categories: DataAnalysis
---
 <div style="margin: 0 auto;overflow: hidden;">
 <img src="waterPath.png" alt="" />
 </div>
 
```python
# -*- coding: utf-8 -*-
import arcpy

# The path of the DEM
demPath=""
pathList = []
elevationCell = {}
cellXSzie = arcpy.GetRasterProperties_management(demPath, "CELLSIZEX")
cellYSzie = arcpy.GetRasterProperties_management(demPath, "CELLSIZEY")
leftPosition = arcpy.GetRasterProperties_management(demPath, "LEFT")
topPosition = arcpy.GetRasterProperties_management(demPath, "TOP")
bottomPosition = arcpy.GetRasterProperties_management(demPath, "BOTTOM")
# Converting raster to array
rstArray = arcpy.RasterToNumPyArray(arcpy.Raster(demPath))
# Number of rows and columns of raster
elevationCell = {}
rows, cols = rstArray.shape
for rowNum in xrange(rows):
    for colNum in xrange(cols):
        # Set the elevation raster pixel, initialized as not flooded
        elevationCell[(rowNum, colNum)] = [rowNum, colNum, rstArray.item(rowNum, colNum), -1]

def getDirectionOfWaterFlow(rowNumber,colNumber,elevationCell):
    cell8list = []
    for rowi in range(rowNumber - 1, rowNumber + 2):
        for coli in range(colNumber - 1, colNumber + 2):
            # No out of bounds
            if (rowi < rows and coli < cols and coli >= 0 and rowi >= 0 ):
                # Exclude oneself
                if(rowi != rowNumber or coli !=colNumber):
                    # Exclude the already flooded
                    if  elevationCell[rowi, coli][3] == -1:
                        # Get elevation
                        elevationcell = elevationCell[rowi, coli]
                        cell8list.append(elevationcell)
    if len(cell8list)!=0:
        cell8list.sort(key=lambda x: (x[2]), reverse=False)
        nercelllist = cell8list
        # Get the lowest elevation grid
        mincell=nercelllist[0]
        mincell_ele = nercelllist[0][2]
        selfcell_ele = elevationCell[rowNum, colNum][2]
        # whether it can find a lower grid or not
        if mincell_ele <= selfcell_ele:
            depth = 0
            # Set to Flooded
            elevationCell[rowNum, colNumber][3] = 1
        else:
            depth = mincell_ele - selfcell_ele
            # Set to Flooded
            elevationCell[rowNumber, colNumber][3] = 1
        return mincell, depth
    else:
        return None

def getWaterFlowPath(rowNumber,colNumber,elevationCell):
    if getDirectionOfWaterFlow(rowNumber,colNumber,elevationCell)!=None:
        mincell, depth=getDirectionOfWaterFlow(rowNumber,colNumber,elevationCell)
        r=mincell[0]
        c=mincell[1]
        pathList.append(mincell)
        getWaterFlowPath(r,c,elevationCell)
    else:
        return pathList

def getWaterFlowPathRaster(pointPath,pathList,waterFlowPathRasterSaveName):
    # Set the flooded order
    for i in range(len(pathList)):
        r = pathList[i][0]
        c = pathList[i][1]
        elevationCell[(r, c)][3] = i
    for rowNum in xrange(rows):
                for colNum in xrange(cols):
                    rstArray[(rowNum, colNum)] = elevationCell[(rowNum, colNum)][3]
    result = arcpy.NumPyArrayToRaster(rstArray, arcpy.Point(leftPosition[0], bottomPosition[0]), float(cellXSzie[0]), float(cellYSzie[0]))
    result.save(waterFlowPathRasterSaveName)
    # Save the raster
    dsc = arcpy.Describe(pointPath)
    coord_sys = dsc.spatialReference
    arcpy.DefineProjection_management(waterFlowPathRasterSaveName, coord_sys)
```