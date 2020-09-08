# kline_flutter

[![License](https://img.shields.io/badge/license-MIT-green.svg)](/LICENSE)

## 介绍
一个仿火币的flutter图表库包含深度图，支持各种指标及放大缩小、平移等操作



## 简单用例
#### 1.在 pubspec.yaml 中添加依赖
```yaml
//本地导入方式
dependencies:
  flutter_k_chart:
    path: 项目路径
```

#### 2.在布局文件中添加
```dart
import 'package:flutter_k_chart/flutter_k_chart.dart';
....
Container(
  height: 450,
  width: double.infinity,
  child: KChartWidget(
    datas,//数据
    isLine: isLine,//是否显示折线图
    mainState: _mainState,//控制主视图指标线
    secondaryState: _secondaryState,//控制副视图指标线
    volState: VolState.VOL,//控制成交量指标线
    fractionDigits: 4,//保留小数位数
  ),
 )
 
 //深度图使用
 Container(
   height: 230,
   width: double.infinity,
   child: DepthChart(_bids, _asks),
 )         
```
### 3.修改样式
可在chart_style.dart里面修改图表样式

#### 4.数据处理
```dart
//接口获取数据后，计算数据
DataUtil.calculate(datas);
//更新最后一条数据
DataUtil.updateLastData(datas);
//添加数据
DataUtil.addLastData(datas,kLineEntity);
```