---
title: Batch download Sentinel data from ESA Copernicus Data Centre
author: Siqi Dong
date: 2020-10-27 21:54:46
categories: DataCollection
---
# Data Source
https://scihub.copernicus.eu/dhus/#/home

 <div style="margin: 0 auto;overflow: hidden;">
 <img src="COAH.png" alt="" />
 </div>

```python
# -*- coding: utf-8 -*-
from sentinelsat.sentinel import SentinelAPI
from collections import OrderedDict

# Your Copernicus Open Access Hub Account
# Register on https://scihub.copernicus.eu/dhus/#/self-registration
name=''
password=''
api = SentinelAPI(name,password, 'https://scihub.copernicus.eu/dhus/'
xmin,ymin,xmax,ymax=120,34,121,35
roi="POLYGON(("+str(xmin)+" "+str(ymin)+","+str(xmax)+" "+str(ymin)+","+str(xmax)+" "+str(ymax)+","+str(xmin)+" "+str(ymax)+","+str(xmin)+" "+str(ymin)+"))"
# roi = 'POLYGON((DEFINE ROI))' # http://geojson.io/#map=10/42.4012/117.2488

start_date = '20190524'
end_date = '20190530'

# S1/2
platformname='Sentinel-2'
# S1：SLC(Single Look Complex)、GRD(Ground Range Detected)、OCN
# product_type = 'SLC'
# S2：S2MSI1C、S2MSI2A(S2-L2A)、S2MSI2Ap
product_type = 'S2MSI2A'
# Min, Max percentage
cloud_cover = (0, 50)
# Img save path
download_path="E:\\"

# Ordered dictionary
success_products = OrderedDict()
products = OrderedDict()

results  = api.query(area=roi,date=(start_date, end_date),platformname=platformname,producttype=product_type, cloudcoverpercentage=cloud_cover)
products.update(results)

total = len(products)
print(" {} data finded ".format(total))

def download_one(api, product, product_info):
    # download
    api.download(product, directory_path=download_path)
    # save info
    success_products[product] = product_info
    print('\t[success:] {}/{}'.format(len(success_products), total))

# Trigger offline product first
try:
    print("---Trigger offline product---")
    cnt = 1
    for product in products:
        product_odata = api.get_product_odata(product)
        if not product_odata['Online']:
            print("Trigger offline data {}： {}".format(cnt, product_odata['date']))
            # Triggering retrieval from long term archive
            api.download(product, download_path)
            cnt += 1
except:
    print("Sorry, requests for retrieval from LTA exceed user quota (No more than 20 times)")
    pass

# Start downloading
print("---Download Start---")
while len(success_products)!=total:
        for product in products:
            product_odata = api.get_product_odata(product)
            # Online
            if product_odata['Online']:
                print('Data online，can be downloaded：{} {}'.format(product_odata['date'], product_odata['title']) )
                download_one(api, product, products[product])
            else:
                print("[Data offline，please wait：] {}".format(product_odata['date'] ))
```