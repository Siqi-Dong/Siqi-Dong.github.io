---
title: Get the quantile of raster image
date: 2022-07-27 09:07:00
author: Siqi Dong
categories: DataProperties
---


```python
# -*- coding: utf-8 -*-
import tifffile as tiff
import math
import numpy
from itertools import chain
img = tiff.imread('tiffilePath')
listImg = list(chain.from_iterable(img))
#Filter out negative numbers (optional)
x = numpy.array([num for num in listImg if num >= 0])
def percentile(data, perc: int):
    size = len(data)
    return sorted(data)[int(math.ceil((size * perc) / 100)) - 1]
    
#5th percentile
print("5th percentile："+str(percentile(x, 5)))
#95th percentile
print("95th percentile："+str(percentile(x, 95)))
 ```