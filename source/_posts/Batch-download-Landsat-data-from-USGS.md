---
title: Batch download Landsat data from USGS
author: Siqi Dong
date: 2020-10-27 20:43:06
categories: DataCollection
---
# Data Source
https://earthexplorer.usgs.gov/

 <div style="margin: 0 auto;overflow: hidden;">
 <img src="USGS.png" alt="" />
 </div>
 
```python
# -*- coding: utf-8 -*-
# Python version 3.X
import landsatxplore.api
from landsatxplore.earthexplorer import EarthExplorer

def request_Landsat(username,password,product,bbox,start_date,end_date,cloud_max):
    api = landsatxplore.api.API(username, password)
    scenes = api.search(
        dataset=product,
        # latitude=lat,
        # longitude=lon,
        bbox=bbox,
        start_date=start_date,
        end_date=end_date,
        max_cloud_cover=cloud_max)
    print('{} data queried.'.format(len(scenes)))
    api.logout()
    return scenes

def download_landsat(username,password,Landsat_name,output_dir):
    Earth_Down = EarthExplorer(username, password)
    for scene in Landsat_name:
        ID = scene['entityId']
        print('Downloading %s '% ID)
        Earth_Down.download(scene_id=ID, output_dir=output_dir)
    Earth_Down.logout()


if __name__ == '__main__':
    # Your USGS account
    username = ''
    password = ''
    # Select data according to sensor type
    product = 'LANDSAT_TM_C1'    # Landsat 4、5
    # product = 'LANDSAT_ETM_C1'    # Landsat 7
    # product = 'LANDSAT_8_C1'    # Landsat 8
    # lat =
    # lon =
    # The Image Coverage (xmin、ymin、xmax、ymax)
    bbox = [34, 119, 35, 120]
    start_date='1999-01-01'
    end_date='2000-01-01'
    # Maximum cloud coverage
    cloud_max = 10
    # Save path
    output_dir = 'E:\\LandsatImg\\'
    Landsat_name = request_Landsat(username,password,product,bbox,start_date,end_date,cloud_max)
    download_landsat(username,password,Landsat_name,output_dir)
 ```