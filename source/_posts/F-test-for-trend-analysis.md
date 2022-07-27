---
title: F-test for trend analysis
date: 2020-10-21 21:59:28
author: Siqi Dong
categories: DataAnalysis
---

```python
# -*- coding: utf-8 -*-
import arcpy
import math
from arcpy.sa import *
arcpy.CheckOutExtension("spatial")

def F_TestForTrendAnalysis(dataFolder,resultSaveFolder):
    workSpace1 = dataFolder
    arcpy.env.workspace = unicode(workSpace1, "utf8")
    ys = arcpy.ListRasters("1*", "tif")
    # Generate time series layers
    i = 0
    for y in ys:
        i = i + 1
        out = dataFolder+"\\"
        if i < 10:
            out1 = unicode(out, "utf8") + "20" + str(i) + ".tif"
        else:
            out1 = unicode(out, "utf8") + "2" + str(i) + ".tif"
        outx = Con(1, i, y)
        outx.save(out1)
    # Read the time series layer
    arcpy.env.workspace = unicode(workSpace1, "utf8")
    xs = arcpy.ListRasters("2*", "tif")
    # Calculate
    x_ = CellStatistics(xs, "MEAN", "DATA")
    y_ = CellStatistics(ys, "MEAN", "DATA")
    n = len(xs)
    xy = 0
    x2 = 0
    St = 0
    for i in range(0, len(xs)):
        St = (Raster(ys[i]) - y_) ** 2 + St
        xy = Raster(xs[i]) * Raster(ys[i]) + xy
        x2 = Raster(xs[i]) ** 2 + x2
    k = (xy - n * x_ * y_) / (x2 - n * (x_ ** 2))
    outpath1 = resultSaveFolder+"\\"
    sty1 = "Trend" + ".tif"
    out1 = outpath1 + sty1
    k.save(out1)
    # F-test
    Sr = (x2 - n * (x_ ** 2)) * (k ** 2)
    Se = St - Sr
    F = (n - 2) * Sr / Se
    outpath2 = resultSaveFolder+"\\"
    sty2 = "F" + ".tif"
    out2 = outpath2 + sty2
    F.save(out2)
```