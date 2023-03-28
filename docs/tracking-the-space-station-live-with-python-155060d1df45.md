# 用 Python 实时跟踪空间站

> 原文：<https://medium.com/codex/tracking-the-space-station-live-with-python-155060d1df45?source=collection_archive---------8----------------------->

这是一件非常酷的事情，空间站的坐标是实时传输的，任何人都可以免费获得！当然，有些应用和网站已经为你提供了视觉跟踪器，但这有什么意思呢？通过自己编码，你可以自由地在其他项目中实现它，或者当它出现在空中时用它来提醒你！

![](img/a84001f2c4b4d4072ce4a2ce31b9a76f.png)

所以像这样的东西从哪里入手，有点随意！我们的国际空间站(ISS)坐标数据可以通过来自 API 的 pull 请求导入 python，然后我们需要一种方法将其可视化在绘图上。因此，让我们导入完成此任务所需的包:

```
import pandas as pd
import plotly.express as px
import time
from IPython.display import clear_output
```

你可能已经注意到我已经导入了一个 IPython 包，这主要是因为不使用 Dash 来制作 plotly 图形是一个噩梦，所以你将在后面看到如何使用“clear_output”方法。关于这一点，我已经在 jupyter notebook 中对此进行了编码，我建议使用这个或 google colab，如果你正在跟进的话，这肯定会更容易。

我们可以从中提取包含 ISS 位置的 json 数据的 URL 是:[' http://API . open-notify . org/ISS-now . JSON '](http://api.open-notify.org/iss-now.json')

让我们先把它定义为一个变量:

```
URL = '[http://api.open-notify.org/iss-now.json'](http://api.open-notify.org/iss-now.json')
```

现在，让我们创建一个快速的函数，它可以提取数据，也可以修改数据，以便我们更容易在绘图函数中使用。

```
def pullData(url):
   df = pd.read_json(url)
   df['Latitude'] = df.loc['latitude','iss_position']
   df['Longitude'] = df.loc['longitude','iss_position']
   df.reset_index(inplace=True)
   df = df.drop(['index','messages'])
   df = df.drop(0)
   return df
```

所以我们在这里所做的是从我们的 API 中读入 json 数据，并将其简化为一个 pandas 数据帧，该数据帧只保存被调用时的经度和纬度。

如你所见，将国际空间站的经度和纬度坐标输入 python 非常容易。如果这就是你来看的全部，那么没有必要继续读下去，除非你感兴趣。然而，如果你计划放在一起的视觉援助，显示空间站目前在哪里，那么你很快就会看到！

我们现在将开始使用 plotly 来绘制一个散点图，它只绘制一个点，即国际空间站的位置。幸运的是，plotly 有一个内置的函数，可以保存地球的地图，所以不需要导入图像并将其映射到球体或类似的东西上！首先，我们将设置图表的初始状态，然后我们将进入实时更新。我们需要做的第一件事是获取 ISS 数据的第一个实例，并绘制该点。

```
df = pullData(URL)
fig = px.scatter_geo(df,lat='Latitude',lon='Longitude',
                     projection='orthographic',
                     width=900,height=700)
```

这样我们就得到了地球的基本图形和正交视图中的静态 ISS 点。这个“投影”参数可能是你想自己摆弄的东西。就我个人而言，我建议省去它，使用默认的，使用正投影，或“自然地球”的论点。选择最适合你的！

出于样式的目的，在实现实时更新之前，我们应该添加几行代码。

```
fig.update_layout(margin=dict(l=0,r=0,t=0,b=0))
fig.update_geos(showcoastlines=True,coastlinecolor='white',
                showland=False,
                showocean=True,oceancolor='DarkBlue',
                showcountries=True,countrycolor='black',
                resolution=110)
```

通过各种方式改变你喜欢的样式，这只是让它保持漂亮和干净。这让我们更新了图表。由于 plotly 没有 matplotlib 这样的动画功能(据我所知)，我们只是使用了一个‘while’循环。在这个循环中，我们将引入最新的 ISS 数据，更新我们绘图的一些数据，并展示一个新的数据。这就是 IPython 'clear_output '函数派上用场的地方，因为我们可以用新的图替换旧的图。这只是一个选项，因为渲染这些图形对 CPU 来说很轻，在其他情况下，这太慢了。无论如何，它看起来应该是这样的:

```
while True:
   df = pullData(URL)
   clear_output(wait=True)
   fig.update_traces(lat=df['Latitude'],lon=df['Longitude'],
                     marker=dict(color='lime',size=10))
   fig.update_geos(projection_rotation=dict(lon=df['Longitude'],
                                            lat=df['Latitude']),
                                            roll=0)
   fig.show()
   time.sleep(5)
```

这里,“fig.update_traces”方法改变了 ISS 标记的位置。我们使用的另一种方法是更新相机的视角，这样相机就总是看着国际空间站，而不是一半时间隐藏在地球的另一边。

如果您继续操作，您应该会得到类似如下的输出:

![](img/de007ceecc6dad1fef06974edf320aa7.png)

这就是全部，但是你可以从中得到更多的乐趣。当国际空间站在你家附近时，添加一个通知系统，或者如果你喜欢 flask web 应用程序，添加这个并嵌入 youtube 上持续可用的实时流片段！

无论你决定什么，我希望你发现这是有帮助的和有趣的！