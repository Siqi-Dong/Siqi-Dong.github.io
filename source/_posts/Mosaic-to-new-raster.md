---
title: Mosaic to new raster
date: 2020-10-20 15:08:15
author: Siqi Dong
categories: Raster
---

```python
# -*- coding: utf-8 -*-
import arcpy
import os

arcpy.CheckOutExtension("Spatial")
arcpy.env.overwriteOutput = True
def mosaicToNewRaster(inputFolder,outputFolder,tempfolder,outputname):
    infolder = inputFolder + "\\"
    outfolder = outputFolder + "\\"
    tempfolder = tempfolder + "\\"
    rasters = []
    datanames = os.listdir(infolder)
    for dataname in datanames:
        if os.path.splitext(dataname)[1] == '.tif':
            print  dataname
            inputfullname = infolder + dataname
            print "inputfullname: " + inputfullname
            raster = arcpy.Raster(inputfullname)
            # remove black border
            deldarkfullname = tempfolder + "temp" + dataname
            arcpy.CopyRaster_management(raster, deldarkfullname, "DEFAULTS", 0, 0, "", "", "8_BIT_UNSIGNED")
            raster2 = arcpy.Raster(deldarkfullname)
            rasters.append(raster2)
    # mosaic raster
    arcpy.MosaicToNewRaster_management(rasters, outfolder, outputname, "", "8_BIT_UNSIGNED", "30", "1", "MINIMUM", "FIRST")
    print("Mosaic success")
```