---
date: 2025-10-11
draft: false
tags:
- Trading
- MA
- Plot
title: Python绘制蜡烛图和成交额柱状图以及添加MA线
---

学习程序化交易的第一步，绘制蜡烛图、MA线、成交额图，图片框架我使用的是plotly，和pandas以及jupyter的结合非常好。下图是我绘制的腾讯的股票蜡烛图、成交额图、以及在上面加上三条常规的MA线。
<!--more-->
![蜡烛图](../Python%E7%BB%98%E5%88%B6%E8%9C%A1%E7%83%9B%E5%9B%BE%E5%92%8C%E6%88%90%E4%BA%A4%E9%A2%9D%E6%9F%B1%E7%8A%B6%E5%9B%BE%E4%BB%A5%E5%8F%8A%E6%B7%BB%E5%8A%A0MA%E7%BA%BF/d93ea5531461ae1915658b490f61857b19e6a608.png)
![成交额图](../Python%E7%BB%98%E5%88%B6%E8%9C%A1%E7%83%9B%E5%9B%BE%E5%92%8C%E6%88%90%E4%BA%A4%E9%A2%9D%E6%9F%B1%E7%8A%B6%E5%9B%BE%E4%BB%A5%E5%8F%8A%E6%B7%BB%E5%8A%A0MA%E7%BA%BF/3cda366d7dfdf8543d12471cdf491e62797d3b7d.png)
在jupyter上，点击对应的位置，可以有图示数据的展示。

示例实现代码如下：

``` python
# 绘制蜡烛图
import plotly.graph_objects as go
import plotly.io as pio
import plotly.colors as pic
import numpy as np
import pandas
from quantify.logger import Logger
logger = Logger.get_logger()
pio.renderers.default = "notebook"  # 使用默认浏览器打开 HTML 文件
# 只展示3位小数
pandas.set_option('display.float_format', '{:,.3f}'.format)
class candleStick:
    def __init__(self, quote_ctx, df: pandas.DataFrame):
        self.ctx = quote_ctx
        df = df.copy()
        df = df.sort_values('time_key')
        # 因为存储的数据放到了100万倍，这里要做处理
        df['open'] = df['open']/1000000.0
        df['close'] = df['close']/1000000.0
        df['high'] = df['high']/1000000.0
        df['low'] = df['low']/1000000.0
        df['last_close'] = df['last_close']/1000000.0
        df['turnover'] = df['turnover']/100000000000000.0 #以亿为单位
        df['pe_ratio'] = df['pe_ratio']/1000000.0
        df['change_rate'] = df['change_rate']/1000000.0
        self.df = df
        self.fig = go.Figure()
        self.ma_periods = []

    def addMA(self, period: int):
        if period in self.ma_periods:
            logger.warning(f"已经添加过{period}日均线")
            return
        df = self.df
        ma_label = f"MA{period}"
        df[ma_label] = df['close'].rolling(window=period).mean()
        self.ma_periods.append(period)

    def maPloter(self):
        colors = pic.qualitative.Plotly
        for i, period in enumerate(self.ma_periods):
            ma_label = f"MA{period}"
            self.fig.add_trace(go.Scatter(
                x=self.df['time_key'],
                y=self.df[ma_label],
                mode='lines',
                line=dict(color=colors[i % len(colors)], width=2),
                name=ma_label
            ))

    def klinePloter(self):
        df = self.df
        # K 线
        self.fig.add_trace(go.Candlestick(
            x=df['time_key'],
            open=df['open'],
            high=df['high'],
            low=df['low'],
            close=df['close'],
            increasing_line_color='green',
            decreasing_line_color='red',
            name='K线'
        ))

    def plotShow(self):
        self._updateLayout()
        self.fig.show()

    def _updateLayout(self):
        df = self.df
        fig = self.fig
        # 确定 Y 轴的刻度范围
        # 找到所有价格的最小值和最大值
        min_price = min(df['low'])
        max_price = max(df['high'])

        # 从最小值向下取整到最接近的5的倍数
        start_tick = int(np.floor(min_price / 5) * 5)
        # 从最大值向上取整到最接近的5的倍数
        end_tick = int(np.ceil(max_price / 5) * 5)

        # 使用 numpy.arange() 创建刻度值列表, 每隔10一个刻度
        tick_values = np.arange(start_tick, end_tick + 10, 10)
      
        fig.update_layout(
            title='股票 K 线图',
            xaxis_rangeslider_visible=False,
            xaxis_title='日期',
            yaxis_title='价格',
            width=1000,
            height=600,
        # 设置图表背景为纯白色
            plot_bgcolor='white',
            paper_bgcolor='white',
            
            # 设置 X 轴样式
            xaxis=dict(
                title='日期',
                showgrid=True,
                gridcolor='#cccccc',  # 细灰色网格线
                gridwidth=1,          # 宽度为1
                showline=True,
                zeroline=True,
                linecolor='black',  # 边框颜色
                linewidth=2        # 边框宽度    
            ),
            
            # 设置 Y 轴样式
            yaxis=dict(
                title='价格',
                showgrid=True,
                gridcolor='#cccccc',  # 细灰色网格线
                gridwidth=1,
                showline=True,
                zeroline=True,
                linecolor='black',  # 边框颜色
                linewidth=2,        # 边框宽度,
                # 指定 Y 轴的刻度数量
                # nticks=5  # 您可以尝试不同的数值，比如 10, 20, 30
                # 使用 tickvals 明确指定刻度值
                tickvals=tick_values
            ),
            legend=dict(orientation="h", yanchor="bottom", y=1.02, xanchor="right", x=1),
            hovermode='x unified',  # 统一的悬浮显示
            hoverlabel=dict(
                bgcolor="white",       # 背景颜色
                font_size=14,          # 字体大小
                font_family="Rockwell",# 字体
                namelength=-1,         # 提示框显示完整名字
                
            ),
            # 鼠标悬停时显示十字星线
            hoverdistance=10,  # 鼠标距离触发十字星线的距离
        )
       
```

``` python
# 绘制成交额柱状图
import plotly.graph_objects as go
import plotly.io as pio
import plotly.colors as pic
import numpy as np
import pandas
from quantify.logger import Logger
logger = Logger.get_logger()
pio.renderers.default = "notebook"  # 使用默认浏览器打开 HTML 文件

class TurnoverBar:
    def __init__(self, quote_ctx, df: pandas.DataFrame):
        self.ctx = quote_ctx
        df = df.copy()
        df = df.sort_values('time_key')
        df['open'] = df['open']/1000000.0
        df['close'] = df['close']/1000000.0
        df['high'] = df['high']/1000000.0
        df['low'] = df['low']/1000000.0
        df['last_close'] = df['last_close']/1000000.0
        df['turnover'] = df['turnover']/100000000000000.0  #成交量以亿为单位
        df['pe_ratio'] = df['pe_ratio']/1000000.0
        df['change_rate'] = df['change_rate']/1000000.0
        self.df = df
        self.fig = go.Figure()
        self.ma_periods = []

    def addMA(self, period: int):
        if period in self.ma_periods:
            logger.warning(f"已经添加过{period}日均线")
            return
        df = self.df
        ma_label = f"MA{period}"
        df[ma_label] = df['turnover'].rolling(window=period).mean()
        self.ma_periods.append(period)

    def plotShow(self):
        self._updateLayout()
        self.fig.show()

    def maPloter(self):
        colors = pic.qualitative.Plotly
        for i, period in enumerate(self.ma_periods):
            ma_label = f"MA{period}"
            self.fig.add_trace(go.Scatter(
                x=self.df['time_key'],
                y=self.df[ma_label],
                mode='lines',
                line=dict(color=colors[i % len(colors)], width=2),
                name=ma_label
            ))

    def turnoverBarPloter(self):
        df = self.df
        colors = np.where(df['close'] >= df['open'], 'green', 'red')
        self.fig.add_trace(go.Bar(
            x=df['time_key'],
            y=df['turnover'],
            marker_color=colors,
            name='成交额',
            opacity=0.5 # 透明度
        ))

    def _updateLayout(self):
        df = self.df
        fig = self.fig
        # 确定 Y 轴的刻度范围
        max_turnover = max(df['turnover'])
        # 从最大值向上取整到最接近的5的倍数
        end_tick = int(np.ceil(max_turnover / 5) * 5)
        fig.update_layout(
            title='成交额图',
            xaxis_title='时间',
            yaxis_title='成交额(亿)',
            yaxis=dict(range=[0, end_tick]),
            xaxis_tickformat="%Y-%m-%d",
            hovermode='x unified'  #可以让多个图的hover信息同步显示
        )
```

``` python
import futu
from quantify.stock.historyKline import HistoryKLineQuery
from quantify.plot.candleStick import candleStick
from quantify.plot.turnoverBar import TurnoverBar
from quantify.config.config import config
from IPython.display import display
from quantify.logger import Logger
logger = Logger.get_logger()

def plotTest():
    quote_ctx = futu.OpenQuoteContext(host=config.futu_openD.host, port=config.futu_openD.port)  # 创建行情对象
    hisk = HistoryKLineQuery(ctx=quote_ctx)
    df = hisk.query_kline("HK", "00700", "2025-06-01", "2025-10-10", futu.KLType.K_DAY) #type: ignore
    dfadj = hisk.adjusted_forward_price("HK", "00700", df)
    cs = candleStick(quote_ctx, dfadj)
    cs.klinePloter()
    cs.addMA(5)
    cs.addMA(10)
    cs.addMA(20)
    cs.maPloter()
    cs.plotShow()
    tb = TurnoverBar(quote_ctx, dfadj)
    tb.turnoverBarPloter()
    tb.addMA(5)
    tb.addMA(10)
    tb.addMA(20)
    tb.maPloter()
    tb.plotShow()
    quote_ctx.close()  # 关闭行情对象

if __name__ == "__main__":
    plotTest()
```
