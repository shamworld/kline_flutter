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
```
//k线完整调用方法以及接口和切换，最后一根线处理方法，参考如下
class KlineWidget extends StatefulWidget {
  final String coinId;

  const KlineWidget({Key key, this.coinId}) : super(key: key);

  @override
  _KlineWidgetState createState() => _KlineWidgetState();
}

class _KlineWidgetState extends State<KlineWidget> {
  List<KLineEntity> kLinelist = [];
  MainState _mainState = MainState.MA;
  VolState _volState = VolState.VOL;
  SecondaryState _secondaryState = SecondaryState.MACD;
  Timer timer;
  String period = '5min';
  bool showLoading = false;

  String moreName = "更多";
  bool selectMore = false; //更多
  bool selectSDT = false; //深度图
  bool selectZb = false; //指标

  @override
  void initState() {
    super.initState();
    // _getData();
    // _getLast();
    timer = Timer.periodic(Duration(seconds: 2), (timer) {
      // _getLast();
    });
  }

  @override
  void dispose() {
    super.dispose();
    timer?.cancel();
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: <Widget>[
        SizedBox(
          height: height(10),
        ),
        Row(
          mainAxisAlignment: MainAxisAlignment.spaceEvenly,
          mainAxisSize: MainAxisSize.max,
          //交叉轴的布局方式，对于column来说就是水平方向的布局方式
          crossAxisAlignment: CrossAxisAlignment.center,
          //就是字child的垂直布局方向，向上还是向下
          verticalDirection: VerticalDirection.down,

          children: <Widget>[
            _periodBtn('1分钟', '1min', false),
            _periodBtn('5分钟', '5min', false),
            _periodBtn('1天', '1day', false),
            FSuper(
              backgroundColor: selectMore || "更多" != moreName
                  ? Color(0xFF1A4AB6)
                  : Colors.transparent,
              corner: Corner.all(width(8)),
              text: moreName,
              textSize: sp(24),
              textColor: Colors.white,
              width: width(98),
              padding: EdgeInsets.symmetric(vertical: height(2)),
              onClick: () {
                selectZb = false;
                selectMore = !selectMore;
                setState(() {});
              },
            ),
//            FSuper(
//              backgroundColor: selectSDT ? Color(0xFF1A4AB6) : Colors.transparent,
//              corner: Corner.all(width(8)),
//              text: "深度图",
//              textSize: sp(24),
//              textColor: Colors.white,
//              width: width(98),
//              padding: EdgeInsets.symmetric(vertical: height(2)),
//              onClick: (){
//                selectSDT = !selectSDT;
//                setState(() {});
//              },
//            ),
            FSuper(
              backgroundColor:
                  selectZb ? Color(0xFF1A4AB6) : Colors.transparent,
              corner: Corner.all(width(8)),
              text: "指标",
              textSize: sp(24),
              textColor: Colors.white,
              width: width(98),
              padding: EdgeInsets.symmetric(vertical: height(2)),
              onClick: () {
                selectMore = false;
                selectZb = !selectZb;
                setState(() {});
              },
            ),
          ],
        ),
        SizedBox(
          height: height(10),
        ),
        _widgetSecond(),
        SizedBox(
          height: height(10),
        ),
        Stack(
          children: <Widget>[
            Container(
              height: height(700),
              child: KChartWidget(
                kLinelist,
                isLine: period == null,
                mainState: _mainState,
                secondaryState: _secondaryState,
                volState: _volState,
                fractionDigits: 6,
              ),
            ),
            if (showLoading)
              Container(
                  width: double.infinity,
                  height: 450,
                  alignment: Alignment.center,
                  child: CircularProgressIndicator()),
          ],
        )
      ],
    );
  }

  _widgetSecond() {
    if (selectMore) {
      return Row(
        mainAxisAlignment: MainAxisAlignment.spaceEvenly,
        mainAxisSize: MainAxisSize.max,
        //交叉轴的布局方式，对于column来说就是水平方向的布局方式
        crossAxisAlignment: CrossAxisAlignment.center,

        //就是字child的垂直布局方向，向上还是向下
        verticalDirection: VerticalDirection.down,

        children: <Widget>[
          _periodBtn('分时', null, true),
          _periodBtn('15分钟', '15min', true),
          _periodBtn('30分钟', '30min', true),
          _periodBtn('1小时', '1hour', true),
//          _periodBtn('4小时', '${4*60*60*1000}', true),
        ],
      );
    }
    if (selectZb) {
      return Column(
        children: <Widget>[
          SizedBox(
            height: height(10),
          ),
          Row(
            mainAxisAlignment: MainAxisAlignment.spaceEvenly,
            mainAxisSize: MainAxisSize.max,
            //交叉轴的布局方式，对于column来说就是水平方向的布局方式
            crossAxisAlignment: CrossAxisAlignment.center,

            //就是字child的垂直布局方向，向上还是向下
            verticalDirection: VerticalDirection.down,
            children: <Widget>[
              SizedBox(
                width: width(20),
              ),
              Text("主图"),
              _zbBtnMain("MA", MainState.MA),
              _zbBtnMain("BOLL", MainState.BOLL),
              Spacer(),
              _zbBtnMain("隐藏", MainState.NONE),
            ],
          ),
          SizedBox(
            height: height(10),
          ),
          Row(
            mainAxisAlignment: MainAxisAlignment.spaceEvenly,
            mainAxisSize: MainAxisSize.max,
            //交叉轴的布局方式，对于column来说就是水平方向的布局方式
            crossAxisAlignment: CrossAxisAlignment.center,

            //就是字child的垂直布局方向，向上还是向下
            verticalDirection: VerticalDirection.down,
            children: <Widget>[
              SizedBox(
                width: width(20),
              ),
              Text("副图1"),
              _zbBtnVol("VOL", VolState.VOL),
              Spacer(),
              _zbBtnVol("隐藏", VolState.NONE),
            ],
          ),
          SizedBox(
            height: height(10),
          ),
          Row(
            mainAxisAlignment: MainAxisAlignment.spaceEvenly,
            mainAxisSize: MainAxisSize.max,
            //交叉轴的布局方式，对于column来说就是水平方向的布局方式
            crossAxisAlignment: CrossAxisAlignment.center,

            //就是字child的垂直布局方向，向上还是向下
            verticalDirection: VerticalDirection.down,
            children: <Widget>[
              SizedBox(
                width: width(20),
              ),
              Text("副图2"),
              _zbBtnSecond("MACD", SecondaryState.MACD),
              _zbBtnSecond("KDJ", SecondaryState.KDJ),
              _zbBtnSecond("RSI", SecondaryState.RSI),
              _zbBtnSecond("WR", SecondaryState.WR),
              Spacer(),
              _zbBtnSecond("隐藏", SecondaryState.NONE),
            ],
          ),
        ],
      );
    }
    return SizedBox(
      height: height(0),
    );
  }

  _periodBtn(String msg, String times, bool isChangeMore) {
    return FSuper(
      backgroundColor: period == times ? Color(0xFF1A4AB6) : Colors.transparent,
      corner: Corner.all(width(8)),
      text: msg,
      textSize: sp(24),
      textColor: Colors.white,
      width: width(98),
      padding: EdgeInsets.symmetric(vertical: height(2)),
      onClick: () {
        moreName = isChangeMore ? msg : "更多";
        selectMore = false;
        if (period != times) {
          kLinelist = [];
        }
        period = times;
        setState(() {});
        _getData();
      },
    );
  }

  _zbBtnMain(String msg, MainState mainState) {
    return FSuper(
      corner: Corner.all(width(8)),
      text: msg,
      textSize: sp(24),
      textColor: mainState == _mainState ? Color(0xFF1A4AB6) : Colors.white,
      width: width(98),
      padding: EdgeInsets.symmetric(vertical: height(2)),
      onClick: () {
        _mainState = mainState;
        setState(() {});
        _getData();
      },
    );
  }

  _zbBtnVol(String msg, VolState volState) {
    return FSuper(
      corner: Corner.all(width(8)),
      text: msg,
      textSize: sp(24),
      textColor: volState == _volState ? Color(0xFF1A4AB6) : Colors.white,
      width: width(98),
      padding: EdgeInsets.symmetric(vertical: height(2)),
      onClick: () {
        _volState = volState;
        setState(() {});
        _getData();
      },
    );
  }

  _zbBtnSecond(String msg, SecondaryState secondaryState) {
    return FSuper(
      corner: Corner.all(width(8)),
      text: msg,
      textSize: sp(24),
      textColor:
          _secondaryState == secondaryState ? Color(0xFF1A4AB6) : Colors.white,
      width: width(98),
      padding: EdgeInsets.symmetric(vertical: height(2)),
      onClick: () {
        _secondaryState = secondaryState;
        setState(() {});
        _getData();
      },
    );
  }

  _getData() async{
    showLoading = true;
    setState(() {
    });

    int endTime = new DateTime.now().millisecondsSinceEpoch;
    print(endTime);
    String result =
     await getIPAddress(endTime.toString());

    print(result);
    final parseJson = json.decode(result);
    print(parseJson);
    List<MarketData> marketData = parseJson['data']
        .map<MarketData>((item) => MarketData.fromJson(item))
        .toList();
    kLinelist = marketData.map((item)=>KLineEntity(
        open: (item.open as num).toDouble(),
        high: (item.high as num).toDouble(),
        low: (item.low as num).toDouble(),
        close: (item.close as num).toDouble(),
        vol: (item.volume as num).toDouble(),
        id: item.time~/1000
    )).toList();

    DataUtil.calculate(kLinelist);
    showLoading = false;
    setState(() {});
  }

  _getLast() async{
    String result = await getLastKline();
    final parseJson = json.decode(result);

    List<MarketData> list=parseJson['data']
        .map<MarketData>((item) => MarketData.fromJson(item))
        .toList();
    if (list.length > 0) {
      MarketData item = list[0];
      KLineEntity kLineEntity = KLineEntity(
          open: (item.open as num).toDouble(),
          high: (item.high as num).toDouble(),
          low: (item.low as num).toDouble(),
          close: (item.close as num).toDouble(),
          vol: (item.volume as num).toDouble(),
          id: item.time~/1000
      );

      if (kLinelist.last.id == kLineEntity.id) {
        kLinelist.last.close = kLineEntity.close;
        kLinelist.last.high = max(kLinelist.last.high, kLineEntity.high);
        kLinelist.last.low = min(kLinelist.last.low, kLineEntity.low);
        kLinelist.last.vol = kLineEntity.vol;
        DataUtil.updateLastData(kLinelist);
      } else {
        DataUtil.addLastData(kLinelist, kLineEntity);
      }
      setState(() {});
    }
  }

  // 请求K线数据
  Future<String> getIPAddress(String time) async {
    var url = GlobalConfig.apiHost1 +
        "api/v2/market/kline?symbol=${widget.coinId}&period=${period??'5min'}&limit=200&etime=${time}";
    print(url);
    String result;
    var response = await http.get(url);
    if (response.statusCode == HttpStatus.ok) {
      result = response.body;
    } else {
      print('Failed getting IP address');
    }
    return result;
  }

  // 请求最后一条K线数据
  Future<String> getLastKline() async {
    var url = GlobalConfig.apiHost1 +
        "/api/v2/market/currentKline?symbol=${widget.coinId}&period=${period??'5min'}";
    String result;
    var response = await http.get(url);
    if (response.statusCode == HttpStatus.ok) {
      result = response.body;
    } else {
      print('Failed lastKline');
    }
    return result;
  }

}

```
