---
title: HDF convert to TIF
date: 2020-10-16 19:39:35
author: Siqi Dong
categories: DataConverion
---

```python
# -*- coding: utf-8 -*-
import arcpy
import  os
from arcpy.sa import *
import envipy
arcpy.CheckOutExtension("Spatial")
arcpy.env.overwriteOutput = True

name=["h23v04","h24v04","h25v04","h23v05","h24v05","h25v05","h26v05","h25v06","h26v06","h27v06"]
# parameter
path="E:\\MODIS"
shp="E:\\MODIS\\tibetALB_Dissolve.shp"
Sprj="E:\\MODIS\\Sinusoidal.prj"
Albprj="E:\\MODIS\\Albers_Conic_Equal_Area.prj"

for i in range(len(name)):
    img=name[i]
    imgarray=[]
    f_list = os.listdir(path)
    for file in f_list:
        filename = path + "\\" + file
        if os.path.splitext(file)[1] == '.hdf' and filename.split(".")[2][0:6] == img:
            hdf = path + "\\" + file
            tif = path+"\\"+os.path.splitext(file)[0] + ".tif"
            print hdf
            print tif
            # Extract 0-band subdataset from HDF
            arcpy.ExtractSubDataset_management(hdf, tif, "0")
            # DefineProjection
            arcpy.DefineProjection_management(tif, Sprj)
            imgarray.append(tif)
    outCellStatistics = CellStatistics(imgarray, "MEAN", "DATA")
    # Save the output
    outCellStatistics.save("E:\\MODIS\\result\\"+img+"_2015NDVI_MEAN.tif")
f_list = os.listdir("E:\\MODIS\\result")
rasterarray = []
for file in f_list:
    filename = path + "\\result\\" + file
    if os.path.splitext(file)[1] == ".tif":
        rasterarray.append(filename)
print rasterarray
arcpy.MosaicToNewRaster_management(rasterarray,"E:\\MODIS\\result","NDVI2015_m.tif","", "16_BIT_SIGNED", "", "1", "LAST", "FIRST")
# Project transfer
arcpy.ProjectRaster_management("E:\\MODIS\\result\\NDVI2015_m.tif", "E:\\MODIS\\result\\NDVI2015_alb.tif", Albprj, "","250", "","","","")

# Extract
# outExtractByMask = ExtractByMask("E:\\MODIS\\result\\NDVI2015_alb.tif", shp)
# Save the output
# outExtractByMask.save("E:\\MODIS\\result\\Tibet_NDVI2015_int_alb.tif")
# intRaster=arcpy.Raster("E:\\MODIS\\result\\Tibet_NDVI2015_int_alb.tif")
```
