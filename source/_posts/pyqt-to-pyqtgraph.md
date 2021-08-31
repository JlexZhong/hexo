---
title: pyqtgraph嵌入pyqt界面中 | 修改pyqtgraph背景颜色 | 坐标轴等比例缩放
tags:
  - PyQt
  - GUI
  - pyqtgraph
categories: PyQt
abbrlink: 32701
date: 2021-08-30 23:16:16
---

# pyqtgraph嵌入pyqt界面中 | 修改pyqtgraph背景颜色 | 坐标轴等比例缩放

```python
class pyqtgraph_widget(QWidget):
    """将pyqtgraph嵌入到pyqt界面中

    Args:
        QWidget ([type]): 基于QWidget组件
    """

    def __init__(self, parent=None):
        """
        :param parent: 父组件
        """
       
        QWidget.__init__(self, parent)
        # 创建垂直布局
        self.layout = QtWidgets.QVBoxLayout(self)
        self.layout.setObjectName('layout')
        self.layout.setContentsMargins(0,0,0,0)
        # 修改背景颜色
        pg.setConfigOption('background', '#FFFFFF')
        pg.setConfigOption('foreground', 'k')
        self.plot_widget = pg.PlotWidget()
        self.layout.addWidget(self.plot_widget)
        self.plot_widget.setAspectLocked()  # 坐标轴等比例缩放
```
