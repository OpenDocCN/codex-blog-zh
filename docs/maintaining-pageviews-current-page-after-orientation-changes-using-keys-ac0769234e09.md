# 方向改变后使用按键保持滚动状态

> 原文：<https://medium.com/codex/maintaining-pageviews-current-page-after-orientation-changes-using-keys-ac0769234e09?source=collection_archive---------10----------------------->

## 颤振案例研究

![](img/2380b0b9c8c126a75e46b2106fc1a86b.png)

欢迎阅读我们的**案例研究系列**的第二篇文章。如果你还没有看过第一篇关于在 Android 上使用 Navigator 2.0 时捕捉回按键的文章，请在这里查看。今天，我们将讨论 roll `Key`在微件重新创建之间维护*状态*的作用。在这个来自 Flutter 团队的精彩视频中，可以找到对`Key`及其与小部件的使用的精彩解释。

如果您想通过一个示例来更好地理解这一点，请留下来继续学习。

[StackOverflow](https://stackoverflow.com) 上的用户希望在设备方向改变时提供不同的屏幕布局(横向& `Column`为纵向`Row`)。当然，这对于使用`OrientationBuilder`小部件来说是小菜一碟。但是，用户注意到，当使用`PageView`改变设备方向时，当前页面索引丢失。这是因为方向改变时创建了一个新的`PageView`小部件，而*状态*丢失了。事实上，任何`Scrollable`或类似的有状态小部件都会发生同样的事情。通过运行下面的代码来亲自查看。

```
class HomePage extends StatefulWidget {
  @override
  _HomePageState createState() => _HomePageState();
}class _HomePageState extends State<HomePage> {
  final _controller = PageController();
  final _modes = ['Bike', 'Boat', 'Car', 'Foot'];
  final _icons = [
    Icons.directions_bike,
    Icons.directions_boat_outlined,
    Icons.directions_car,
    Icons.directions_run,
  ];
  var _page = 0; Widget _buildHeader() {
    return ColoredBox(
      color: Colors.grey,
      child: Center(
        child: Text(
          'Travel Information',
          style: TextStyle(color: Colors.white, fontSize: 24.0),
        ),
      ),
    );
  } @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: OrientationBuilder(builder: (context, orientation) {
        return orientation == Orientation.portrait
          ? Column(
              children: [
                Expanded(child: _buildHeader()),
                Expanded(
                  child: PageView(
                    controller: _controller,
                    children: _modes.map((mode) =>
                      Center(child:Text(mode))).toList(),
                  ),
                ),
              ],
            )
          : Row(
              children: [
                Expanded(child: _buildHeader()),
                Expanded(
                  child: PageView(
                    controller: _controller,
                    children: _modes.map((mode) =>
                      Center(child: Text(mode))).toList(),
                  ),
                ),
              ],
            );
      }),
      bottomNavigationBar: BottomNavigationBar(
        type: BottomNavigationBarType.fixed,
        currentIndex: _page,
        onTap: (page) {
          setState(() => _page = page));
          _controller.jumpToPage(page);
        },
        items: [
          BottomNavigationBarItem(
            icon: Icon(_icons[0]),
            label: _modes[0],
          ),
          BottomNavigationBarItem(
            icon: Icon(_icons[1]),
            label: _modes[1],
          ),
          BottomNavigationBarItem(
            icon: Icon(_icons[2]),
            label: _modes[2],
          ),
          BottomNavigationBarItem(
            icon: Icon(_icons[3]),
            label: _modes[3],
          ),
        ],
      ),
    );
  }
}
```

您会注意到，每次旋转设备时，`PageView`的当前页面都会重置，但`BottomNavigationBar`中的选定项目不会重置，这不仅会导致我们丢失位置，还会导致应用程序导航不同步。谢天谢地，有一个简单的解决方案。

我们可以创建一个`PageStorageKey`并将其传递给两个`PageView`小部件。这告诉 Flutter 框架，这实际上是同一个小部件，页面状态应该在实例之间维护。

```
class _HomePageState extends State<HomePage> {
  // create a PageStorageKey, passing in a value for identification
  final _key = PageStorageKey('pageStorageKey');
  final _controller = PageController(); ...
  // pass to the Columns PageView
  child: PageView(
    key: _key,
    controller: _controller,
  ...
  // pass to the Row's PageView
  child: PageView(
    key: _key,
    controller: _controller,
  ...
}
```

现在你知道了！重新运行应用程序，你会注意到页面在方向变化之间保持导航同步，并保持用户的位置。如前所述，同样的方法将在`Scrollable`小部件之间保持*滚动状态*。我鼓励你试一试，如果你还没有看过，一定要看看文章顶部的视频。它将帮助您更深入地了解何时何地使用*键、*，以及哪种键适合特定情况。

感谢您的阅读！如果你觉得这篇文章有帮助或者有趣，并且想阅读更多的案例研究，请鼓掌并关注。编码快乐！