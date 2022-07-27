---
title: GDB convert to shp
date: 2020-10-19 22:02:36
author: Siqi Dong
categories: DataConverion
---
```python
import arcpy
import os

def  GDBConvertToShp(GDBfolder ,SHPfolder):
    for root, dirs, files in os.walk(GDBfolder):
        for d in dirs:
            path= root+"\\"+d
            if path[-4:]==".gdb":
                name=path.split(".")[0].split("\\")[-1]
                arcpy.env.workspace =path
                gdbshp=arcpy.ListFeatureClasses()
                for fc in gdbshp:
                    # the name of the feature in GDB you want to convert to shpfile
                    if str(fc)=="XXXX":
                        arcpy.FeatureClassToFeatureClass_conversion(fc, SHPfolder,name+str(fc)+".shp")
```