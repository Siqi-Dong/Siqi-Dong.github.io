---
title: Batch download DEM data from Geospatial Data Cloud
author: Siqi Dong
date: 2020-10-25 20:13:23
categories: DataCollection
---
# Data Source
http://www.gscloud.cn/

 <div style="margin: 0 auto;overflow: hidden;">
 <img src="GeoDataCloud.png" alt="" />
 </div>
 
```python
# -*- coding: utf-8 -*-
import time
from selenium import webdriver
from selenium.webdriver.common.keys import Keys

# chromedriver.exe path
driver=webdriver.Chrome(executable_path='XXX\\chromedriver.exe')
driver.get('http://www.gscloud.cn/accounts/login_user')
email=driver.find_element_by_xpath('//*[@id="email"]')
# Your gscloud email address
email.send_keys('XXX')
password=driver.find_element_by_xpath('//*[@id="password"]')
# Your gscloud password
password.send_keys('XXX')

captcha=driver.find_element_by_xpath('//*[@id="id_captcha_1"]')
captcha_sj=input('Please enter the code：').strip()
captcha.send_keys(captcha_sj)

dr_buttoon=driver.find_element_by_xpath('//*[@id="login-form"]/input[2]').click()
time.sleep(3)
sjzy=driver.find_element_by_link_text('数据资源').click()
time.sleep(3)
sjzy=driver.find_element_by_link_text('公开数据').click()
time.sleep(3)
sjzy=driver.find_element_by_link_text('DEM 数字高程数据').click()
time.sleep(3)
sjzy=driver.find_element_by_link_text('GDEMV2 30M 分辨率数字高程数据').click()
time.sleep(3)

# 2261 pages in all
page_num=2261
page=1
while page<=page_num:
    print('Downloading {} page'.format(page))
    # Must be 3 ~12.
    for tr_num in range(3,13):
        d_everypage='//*[@id="datasource"]/div/table/tr['+str(tr_num)+']/td[9]/div/div/p[2]'
        download=driver.find_element_by_xpath(d_everypage).click()
        # Give 20 seconds per download time
        time.sleep(20)
    page += 1
    page_sr=driver.find_element_by_xpath('//*[@id="pager1"]/div[2]/table/tr/td[7]/input')
    page_sr.clear()
    page_sr.send_keys(page)
    page_sr.send_keys(Keys.RETURN)
    time.sleep(3)
```