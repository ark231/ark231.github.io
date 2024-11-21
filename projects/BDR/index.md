---
layout: page
title: BDR
---

# BDRとは
BDRは、廉価な材料を用いたデータレコーダーで、6軸IMU及び温度気圧センサーを搭載している。  
廉価な材料で制作することで気軽に投げたり落としたりして試験することができるようにした。

# 仕様
## センサー
* **MPU6050**  
  6軸IMU
* **BMP280**  
  温度気圧センサー

## マイコン
**CH32V003F4P6**  
32bit risc-vマイクロコントローラ  
秋月で50円で売っている。

## ストレージ
**IS25LP040E**  
4MBit SPIフラッシュ  
秋月で80円で売っている[^uson8]。

## 電源
未定(LR44?)


# スペック
## 記録データ
* 3軸加速度
* 3軸角速度
* 気圧
* 気温

## データレート
~1KHz

## 記録時間
10秒程度  
データレートを落としたりストレージを増設[^upgrade]すれば延長可能  

[^uson8]: W25Q16JVも売っているが、USON8にビビって採用しなかった。  
[^upgrade]: SPI接続なので、GPIO1本につき1つ増設可能