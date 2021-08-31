---
title: pyqy小记|生成gif并嵌入到GUI中
tags:
  - PyQt
  - GUI
catefories: PyQt

abbrlink: 52383
date: 2021-08-30 23:13:27
---

# pyqy小记|生成gif并嵌入到GUI中

## 生成gif，嵌入到标签QLabel中

```python
    def compose_gif(self):
     	"""生成gif
     	"""
        jpgPath_List = []
        # 读取图片集
        jpgList = os.listdir("./temp save files")  # 参数是图片集存放的路径，返回该路径下的所有文件名
        for jpgName in jpgList:
            absolutePath = os.path.join("./temp save files/", jpgName)  # 拼接成绝对路径
            jpgPath_List.append(absolutePath)
        gif_images = []
        # 读取单个图片，将数据存入列表中
        for path in jpgPath_List:
            gif_images.append(imageio.imread(path))
        #保存为gif，fps=2
        imageio.mimsave("./temp save files/test.gif", gif_images, fps=2)
        # 实例化QMovie
        self.gif = QMovie('./temp save files/test.gif')
        # tabwidget增加一页
        gif_tab = QtWidgets.QWidget()
        gif_tab.setObjectName('gif_tab')
        self.tabWidget.addTab(gif_tab, 'GIF')
        gif_verticalLAYOUT = QtWidgets.QVBoxLayout(gif_tab)
        # 
        gif_verticalLAYOUT.setObjectName('gif_verticalLAYOUT')
        # 用gifLabel展示gif
        self.gifLabel = QtWidgets.QLabel(gif_tab)
        self.gifLabel.setObjectName('gifLabel')
        gif_verticalLAYOUT.addWidget(self.gifLabel)
        self.tabWidget.setCurrentWidget(gif_tab)  # 设置当前页为gif播放tab
        # 把gif嵌入到label
        self.gifLabel.setMovie(self.gif)
        self.gif.start()
        self.gifLabel.show()
```

## 播放/暂停gif

```python
def pauseGif(self):
	self.gif.setPaused(True)

def playGif(self):
	self.gif.setPaused(False)
```

