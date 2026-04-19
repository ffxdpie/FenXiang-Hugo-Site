---
title: "PyQt5的固定写法"
date: 2023-09-09T22:46:27+08:00
description: "PyQt5的固定写法"
categories:
  - "python"
tags:
  - "教程"
---

```python
#from PyQt5.Qt import *
from PyQt5.QtWidgets import QApplication, QMainWindow, QMessageBox
import sys
from PyQt5 import QtCore
from C:\python file\wisdom\zhui.py import Ui_MainWindow   #替换成自己的


class Window(QMainWindow):
    def __init__(self, parent=None, *args, **kwargs):
        super().__init__(parent, *args, **kwargs)
        self.ui = Ui_MainWindow()
        self.ui.setupUi(self)
        self.ui.pushButton.clicked.connect(self.login_button_clicked) #将按钮与事件绑定
        # self.setWindowFlags(Qt.FramelessWindowHint)  #隐藏标题栏
        # self.setAttribute(Qt.WA_TranslucentBackground)  #隐藏多余背景


    def login_button_clicked(self):                 #事件
        try:
            # 获取文本输入框中的账号、密码、最大值和最小值
            username = self.ui.lineEdit.text()
            password = self.ui.lineEdit_2.text()
            max_1 = int(self.ui.lineEdit_3.text())
            min_1 = int(self.ui.lineEdit_4.text())

            # 在此进行输入的合法性验证

            res = run(username, password, min_1, max_1)
            self.ui.textBrowser.append(res)
        except ValueError:
            # 处理类型转换错误
            QMessageBox.warning(self, "Error", "输入正确的值，最大值不能小于最小值")
        except Exception as e:
            # 处理其他异常
            QMessageBox.warning(self, "Error", "检查账户和网络: {}".format(str(e)))



if __name__ == '__main__':
    QtCore.QCoreApplication.setAttribute(QtCore.Qt.AA_EnableHighDpiScaling)
    app = QApplication(sys.argv)
    window = Window()
    window.show()
    sys.exit(app.exec_())
```

