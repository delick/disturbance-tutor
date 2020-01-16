---
title: 森林扰动代码说明
categories:
- Documentation
tags:
- WOFOST
- Code
date: 2019-06-13
---
# Intro

计算森林生态系统扰动与恢复指标，具体如下：

![扰动指标](https://i.loli.net/2019/06/03/5cf483a2547a365509.png)

| 指标         	| 定义                                           	| 说明                                             	|
|--------------	|------------------------------------------------	|--------------------------------------------------	|
| 扰动起始时刻 	| 开始发生局部LL聚类的时刻                       	| 约定条件：连续两次发生LL聚类，持续时间大于16天。 	|
| 扰动结束时刻 	| 局部LL聚类结束的时刻                           	| -                                                	|
| 扰动持续时间 	| 开始时刻到结束时刻的天数                       	| 多次扰动时扰动时间最长的，扰动的平均时长         	|
| 扰动量级     	| 扰动中残参NDVI最小值与扰动前一时刻残参NDVI之差 	| 扰动量级最大的                                   	|
| 恢复阶段时长 	| 从扰动开始时刻到高-高聚类开始时刻的天数       	| -                                                	|
| 扰动速度     	| 扰动量级与扰动阶段时长的比值                   	| 扰动速度最大的                                   	|
| 恢复速度     	| 扰动量级与恢复阶段时长的比值                   	| 扰动量级最大时，恢复原来100%，75%，50%需要的时长 	|

# 代码文件

| 文件                     	| 说明                     	|
|--------------------------	|--------------------------	|
| `DistMarker.mlx`         	| 0/1 标记森林是否受到扰动 	|
| `DistMaxDuration.mlx`    	| 计算最大扰动时间         	|
| `disturbanceCounter.mlx` 	| 计算扰动次数             	|
| `doyDiff.m`              	| 计算两个日期之间的长度   	|
| `Duration.mlx`           	| 计算所有扰动长度         	|
| `natsort.m`              	| 名称自然排序             	|
| `natsortfiles.m`         	| 文件名称自动排序         	|
| `StartEndMarker2.mlx`    	| 计算开始和终止时间       	|

# 如何使用？
## 运行顺序

1. 运行 `DistMarker.mlx` 标记是否受到扰动，得到结果 `distMarker` 影像，0 表示没有扰动，1 表示有扰动，文件名为 `YYYY-DOY` 格式。
1. 运行 `StartEndMarker2,mlx`，输入上一步的 `distMarker` 影像，得到 `distStart`, `distEnd` 系列影像。影像 DN 值表示开始的日期（以 1987001 为第一天计算）。
1. 使用 **QGIS** 的 `DurationModel_QGIS.model3` 模型，右击 *Run as batch process* ，批量输入 `distStart`, `distEnd` 影像，得到 `distDuration` 影像。
1. 运行 `DistMaxDuration.mlx`，输入上一步的 `distDuration` 影像，得到扰动持续时间的最大值、平均值影像。
1. 运行 `disturbanceCounter.mlx`，输入 `distStart` 影像，得到扰动次数影像 `distCounter` 。

## 注意事项

1. 运行中需要依赖 `natsort.m`, `natsortfiles.m` , `doyDiff.m` ，请把这三个基础函数 **添加到搜索路径** 。
1. `doyDiff()` 函数目前尚未考虑闰年情况，默认每年 365 天，提取的时间长度可能会略微偏小，极端情况下可能会有 8 天误差。
1. 每个函数的具体功能和提示见 `mlx` 文件。
