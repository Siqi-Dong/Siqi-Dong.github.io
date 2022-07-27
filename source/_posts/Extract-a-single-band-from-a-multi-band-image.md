---
title: Extract a single band from a multi-band image
date: 2020-10-22 21:53:28
author: Siqi Dong
categories: Raster
---

```python
# -*- coding: utf-8 -*-
import arcpy
import os

def extractSingleBand(multiBandImgPath,singleBandSavePath):
    f_list = os.listdir(multiBandImgPath)
    for file in f_list:
        if os.path.splitext(file)[1] == ".tif":
            tif = str(multiBandImgPath) + "\\" + str(file)
            arcpy.env.workspace = tif
            for raster in arcpy.ListRasters():
                #The name of band to extract
                if str(raster) == "bandName":
                    bandrasteroutputname = singleBandSavePath+"bandName" + file
                    arcpy.CopyRaster_management(raster, bandrasteroutputname, "", -999, -999)
    print  "done"
```