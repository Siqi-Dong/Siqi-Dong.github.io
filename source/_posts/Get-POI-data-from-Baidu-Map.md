---
title: Get POI data from Baidu Map
author: Siqi Dong
date: 2020-10-25 21:59:32
categories: DataCollection
---

###Baidu Map POI Industry Classification 
####http://lbsyun.baidu.com/index.php?title=lbscloud/poitags

 <div style="margin: 0 auto;overflow: hidden;">
 <img src="POIClass.png" alt="" />
 </div>
 
```python
# -*- coding: utf-8 -*-
import json
import urllib
import urllib.request
import xlwt

def getPOIFromBaiduMap(lat_1,lon_1,lat_2,lon_2,las,keyword,excelSaveFolder):
    count = 0
    # Your BaiduMap API Key
    ak = ''
    keynew = str(urllib.parse.quote(keyword))
    push = excelSaveFolder+'\\'+keyword+'.xls'
    f = open(push, 'w')
    lat_count = int((lat_2 - lat_1) / las + 1)
    lon_count = int((lon_2 - lon_1) / las + 1)
    for lat_c in range(0, lat_count):
        lat_b1 = lat_1 + las * lat_c
        for lon_c in range(0, lon_count):
            lon_b1 = lon_1 + las * lon_c
            for i in range(0, 20):
                page_num = str(i)
                url = r'http://api.map.baidu.com/place/v2/search?query=' + keynew + '&bounds=' + str(lat_b1) + ',' + str(lon_b1) + ',' + str(lat_b1 + las) + ',' + str(lon_b1 + las) + '&page_size=20&page_num=' + str(page_num) + '&output=json&ak=' + ak
                count=count+1
                print(str(count))
                response = urllib.request.urlopen(url)
                data = json.load(response)
                try:
                    for item in data['results']:
                        jname = item['name']
                        print(jname)
                        jlat = item['location']['lat']
                        jlon = item['location']['lng']
                        jadd = item['address']
                        j_str = jname + ',' + str(jlat) + ',' + str(jlon) + ',' + jadd + '\n'
                        f.write(j_str)      
                        print(j_str)
                except:
                    print("Error")
    f.close()

# Example
# POI category
classArray=
['中餐厅',
'外国餐厅',
'小吃快餐店',
'蛋糕甜品店',
'咖啡厅',
'茶座',
'酒吧',
'星级酒店',
'快捷酒店',
'公寓式酒店',
'民宿',
'购物中心',
'百货商场',
'超市',
'便利店',
'家居建材',
'家电数码',
'商铺',
'市场',
'通讯营业厅',
'邮局',
'物流公司',
'售票处',
'洗衣店',
'图文快印店',
'照相馆',
'房产中介机构',
'公用事业',
'维修点',
'家政服务',
'殡葬服务',
'彩票销售点',
'宠物服务',
'报刊亭',
'公共厕所',
'步骑行专用道驿站',
'美容',
'美发',
'美甲',
'美体',
'公园',
'动物园',
'植物园',
'游乐园',
'博物馆',
'水族馆',
'海滨浴场',
'文物古迹',
'教堂',
'风景区',
'景点',
'寺庙',
'度假村',
'农家院',
'电影院',
'ktv',
'剧院',
'歌舞厅',
'网吧',
'游戏场所',
'洗浴按摩',
'休闲广场',
'体育场馆',
'极限运动场所',
'健身中心',
'高等院校',
'中学',
'小学',
'幼儿园',
'成人教育',
'亲子教育',
'特殊教育学校',
'留学中介机构',
'科研机构',
'培训机构',
'图书馆',
'科技馆',
'新闻出版',
'广播电视',
'艺术团体',
'美术馆',
'展览馆',
'文化宫',
'综合医院',
'专科医院',
'诊所',
'药店',
'体检机构',
'疗养院',
'急救中心',
'疾控中心',
'医疗器械',
'医疗保健',
'汽车销售',
'汽车维修',
'汽车美容',
'汽车配件',
'汽车租赁',
'汽车检测场',
'飞机场',
'火车站',
'地铁站',
'地铁线路',
'长途汽车站',
'公交车站',
'公交线路',
'港口',
'停车场',
'加油加气站',
'服务区',
'收费站',
'桥',
'充电站',
'路侧停车位',
'普通停车位',
'接送点',
'银行',
'ATM',
'信用社',
'投资理财',
'典当行',
'写字楼',
'住宅区',
'宿舍',
'内部楼栋',
'公司',
'园区',
'农林园艺',
'厂矿',
'中央机构',
'各级政府',
'行政单位',
'公检法机构',
'涉外机构',
'党派团体',
'福利机构',
'政治教育机构',
'社会团体',
'民主党派',
'居民委员会',
'高速公路出口',
'高速公路入口',
'机场出口',
'机场入口',
'车站出口',
'车站入口',
'门',
'停车场出入口',
'自行车高速出口',
'自行车高速入口',
'自行车高速出入口',
'岛屿',
'山峰',
'水系',
'省',
'省级城市',
'地级市',
'区县',
'商圈',
'乡镇',
'村庄']

# Large rectangular coordinate
lat_1=21.502705
lon_1=110.321145
lat_2=25.519951
lon_2=116.220296

# Step
las=0.2

# Get 中餐厅 POI
getPOIFromBaiduMap(lat_1, lon_1, lat_2, lon_2, las, classArray[0])

# Get all categories of POI
for i in range(len(classArray)):
    print("-------------------Collecting"+classArray[i]+"!---------------------------")
    getPOIFromBaiduMap(lat_1, lon_1, lat_2, lon_2, las, classArray[i])
    print("-------------------" + classArray[i] + "Collected-------------------------")
```