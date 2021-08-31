---
title: pyqt小记|matplotlib嵌入pyqt界面|添加工具条 | matplotlib保存太慢解决方法
keywords: PyQT
description: pyqt小记
categories: PyQt
tags:
  - PyQt
  - GUI
  - matplotlib
abbrlink: 33326
date: 2021-08-30 23:14:23
---

# pyqt小记|matplotlib嵌入pyqt界面|添加工具条 | matplotlib保存太慢解决方法

上源码：

```python
from PyQt5 import QtWidgets
from matplotlib.figure import Figure
from matplotlib.backends.backend_qt5 import NavigationToolbar2QT as NavigationToolbar
from matplotlib.backends.backend_qt5agg import FigureCanvasQTAgg  # pyqt5的画布
import io
import cv2
import matplotlib
import numpy as np
from PIL import Image
from PyQt5.QtWidgets import QWidget

matplotlib.use("Qt5Agg")  # 声明使用pyqt5

class MatplotlibFigure(FigureCanvasQTAgg):
    """
    创建一个画布类，并把画布放到FigureCanvasQTAgg
    """

    def __init__(self, parent=None, filePrefix=None):
        """
        
        :param parent:
        :param filePrefix:
        """
        self.figs = Figure(figsize=(10, 8), dpi=200)
        super(MatplotlibFigure, self).__init__(self.figs)  # 在父类中激活self.fig
        self.setParent(parent)
        self.filePrefix = filePrefix
        self.axes = self.figs.add_subplot(111)
        self.axes.patch.set_alpha(1)  # 设置ax区域背景颜色透明度
        # self.figs.canvas.setStyleSheet("background-color:transparent;")
        FigureCanvasQTAgg.setSizePolicy(
            self, QtWidgets.QSizePolicy.Expanding, QtWidgets.QSizePolicy.Expanding)
        # 用于告知包含该widget的layout：该widget的size hint已发生变化，layout会自动进行调整。
        FigureCanvasQTAgg.updateGeometry(self)

    def saveFig(self):
        """
        保存图片，使用二进制字节流存储，opencv读取。速度优秀
        """
        buffer = io.BytesIO()  # 字节流对象
        self.figs.canvas.print_png(buffer)  # 把画布写入字节流
        data = buffer.getvalue()
        buffer.write(data)
        img = Image.open(buffer)
        img = np.asarray(img)
        img = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
        cv2.imwrite("./temp save files/" + self.filePrefix + ".png", img)

    def saveFig2(self):
        """
        FIXME:需要优化保存图片的速度
        """
        self.figs.savefig("./temp save files/" + self.filePrefix +
                          ".jpg", dpi=144, bbox_inches="tight")


class MplWidget(QWidget):
    """Qt控件，用于嵌入matplotlib画布和工具栏

    Args:
        QWidget ([type]): [description]
    """

    def __init__(self, parent=None, filePrefix=None):
        """

        :param parent:
        """
       
        QWidget.__init__(self, parent)
        self.qCanvas = MatplotlibFigure(parent, filePrefix)
        self.mpl_toolbar = NavigationToolbar(self.qCanvas, self)  # 创建工具条
        # 创建布局，把画布类组件对象和工具条对象添加到QWidget控件中
        self.vbl = QtWidgets.QVBoxLayout(self)
        self.vbl.addWidget(self.qCanvas)
        self.vbl.addWidget(self.mpl_toolbar)
```
