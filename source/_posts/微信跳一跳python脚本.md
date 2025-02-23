---
title: 微信跳一跳Python脚本
date: 2018-01-09 19:57:19
tags: [Python]
categories: Python
description: 闲着无聊，搞一搞跳一跳。
---


# 微信跳一跳Python脚本

adb+python，用的话先装adb， 感觉80%能跳到中心吧。

### 代码

``` python
# -*- coding: utf-8 -*-
import time
from PIL import Image
import os
import math

def isPixFuzzyDiff(r,g,b,pix):
    if abs(r - pix[0]) < 10 and abs(g - pix[1]) < 10 and abs(b - pix[2]) < 10:
        return False
    else:
        return True
def isPixAccurateDiff(r,g,b,pix):
    if abs(r - pix[0]) < 1 and abs(g - pix[1]) < 1 and abs(b - pix[2]) < 1:
        return False
    else:
        return True
def isWhiteCenter(pix):
    if pix[0] == 245 and pix[1] == 245 and pix[2] ==245:
        return True
    else:
        return False
    
def getDistance(dep,des):
    if des[0] > dep[0]:
        x1,x2 = des[0],dep[0]
    else:
        x2,x1 = des[0],dep[0]
    x = (x1 - x2)
    y = (dep[1] - des[1])/2
    dis = (x**2+y**2)**0.5
    return dis


def getTopPixel(pix,height,width,depPix):
    topPixel = [0,0]
    if depPix[0] < width/2:
        startpoint = depPix[0] + 40
        endpoint = width - 2
    else:
        startpoint = 40
        endpoint = depPix[0] - 40
    deltaX = x0 = 0
    flag = False
    for y in range(int(0.18 * height),int(0.66 * height)):
        for x in range(startpoint,endpoint):
            if x == startpoint:           
                r, g, b = pix[x, y][0:3]
            elif isPixFuzzyDiff(r,g,b,pix[x,y]):   
                r, g, b = pix[x, y][0:3]
                flag = not flag
                if not flag:
                    break
                x0 = x
            elif flag :                
                deltaX += 1 
        else:
            continue
        break
    print deltaX
    topPixel = [x0 + math.floor(deltaX)/2,y]
    return topPixel
# button == buttom 懒得改
def getButtonPixel(topPixel,pix):
    buttonPixel = [topPixel[0],0]
    r, g, b = pix[topPixel[0],topPixel[1]][0:3]
    for y in range(topPixel[1],topPixel[1] + 250):
        if not isPixAccurateDiff(r,g,b,pix[topPixel[0],y]):
            buttonPixel[1] = y          
    return buttonPixel

def getdepPix(pix,height,width):
    depPix = [0,0]
    for y in range(1388,200,-1):
        for x in range(2,width-2):
            if isPixAccurateDiff(54,60,102,pix[x,y]):
                continue
            else:
                depPix = [x,y]
                break
        else:
            continue
        break
    return depPix



def main():
    for i in range(20):
        filename = str(int(time.time())) + '.png'
        time.sleep(2)
        print "filename: " + filename
        time.sleep(2)
        os.system('adb shell /system/bin/screencap -p /sdcard/jump/' + filename)    
        print "screencap"
        time.sleep(2)
        os.system('adb pull /sdcard/jump/' + filename + ' d:/MyCode/Python/jump/' + filename)
        print "pull"

##        filename = '1515321961' + '.png'
        im = Image.open(filename)
        pix = im.load()
        width = im.size[0]
        height = im.size[1]
        depPix = getdepPix(pix,height,width)
        
        topPixel = getTopPixel(pix,height,width,depPix)
        buttonPixel = getButtonPixel(topPixel,pix)
        desPix = [topPixel[0],(buttonPixel[1] - topPixel[1])/2 + topPixel[1]]
        print topPixel
        print buttonPixel
        print desPix


        print depPix
        
        dis =  getDistance(depPix,desPix)
        print "distance: " + str(dis)
        swipeTime = 722/475.1 * dis 
        print "swipeTime:" + str(swipeTime)
        os.system("adb shell input swipe 250 250 300 300  " + str(int(swipeTime)))
    
if __name__ == '__main__':
    main()


```