---
title: Batch calculation of NDWI and extraction of water body from Landsat 8 OLI data
author: Siqi Dong
date: 2020-11-01 22:15:44
categories: DataAnalysis
---

```python
# -*- coding: utf-8 -*-

import math
import arcpy
import os
from arcpy import sa
from arcpy.sa import *
from time import strftime
arcpy.CheckOutExtension("Spatial")
arcpy.env.overwriteOutput = True
print("Program start time:"+strftime("%m/%d/%Y %H:%M"))

#------------------------The parameters ---------------------------------
# The path to the parent folder of the unzipped folder of landsat 8 img
Inputpath="E://waterL8data"                             
# Projection file
projectFilePath="E://waterL8data//AEA_WGS_1984.prj"     
# Study area shp
Zone="E://waterL8data//province.shp"                  
# Temp data and results data path
arcpy.env.workspace="E://waterL8data//test"      
# Water extraction final result name (grid value of 1 for water, 2 for non-water)       
finalwaterName="finalwater.tif"                         
#------------------------------------------------------------------------------
# Get all files and folders
allFilesOrFoldersList = os.listdir(Inputpath)
for FileOrFolder in allFilesOrFoldersList:
    FileOrFolder_path = os.path.join(Inputpath, FileOrFolder)
    # Is folder
    if os.path.isdir(FileOrFolder_path):
        FolderPath=FileOrFolder_path
        allFilesList = os.listdir(FolderPath)
        for file in allFilesList:
            fileFullPath=os.path.join(FolderPath, file)
            # No.3 band
            if file[-6:] == "B3.TIF":
                green = arcpy.Raster(fileFullPath)
                # No.6 band
            elif file[-6:] == "B6.TIF":
                Mir = arcpy.Raster(fileFullPath)
                # NDWI
                MNDVI=(green-Mir)*1.0/(green+Mir)
                # NDWI correction
                MNDVIcor=Con((MNDVI>0.05) & (Mir<7200),1,2)
                # Remove the black edge
                outputPath=FileOrFolder+".tif"
                arcpy.CopyRaster_management(MNDVIcor, outputPath, "", 0, "")
                print(FolderPath+"CopyRaster success")

# Mosaic rasters
rasters=[]
for raster in arcpy.ListRasters("*.tif"):
    rasters.append(raster)
arcpy.MosaicToNewRaster_management(rasters, str(arcpy.env.workspace), "water.tif", "","8_BIT_UNSIGNED", "30", "1", "MINIMUM","FIRST")
print("Mosaic success")

water= "water.tif"
arcpy.ProjectRaster_management(water, "water_rep.tif",projectFilePath, "NEAREST", "30","#", "#", "#")
print("ProjectRaster success")

ExtractByMask("water.tif",Zone).save(finalwaterName)
print("ExtractByMask success")

print("program end time:"+strftime("%m/%d/%Y %H:%M"))
```