# 如何将文件上传到 Wia 的 REST API

> 原文：<https://dev.to/wiaio/how-to-upload-a-file-to-wias-rest-api-3oe5>

在本教程中，我将向您展示如何将任何类型的文件(如 png、jpg、pdf 或 txt)上传到 Wia。

### **安装 Python 和 Wia 库**

如果您还没有，您需要安装 python 语言来完成本教程。

[https://www.python.org/downloads/](https://www.python.org/downloads/)

*   版本 3.x.x

*   遵循适用于您的操作系统的下载说明

接下来是安装 python 包管理器`pip`。Pip 允许我们获得 Wia python 库，该库提供了用 python 连接 Wia 的功能。

对于 windows，打开 power-shell 并在 power-shell 终端中运行`pip install wia`。

对于 Mac/Linux，打开一个终端并在终端中运行`pip install wia`。

[![alt text](../Images/33dfe733408215ac1ed2291aeb4c7c8f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QrpoHoL7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-04-12/1523536061-16186-pip-install.png)

### **添加代码**

*   在您选择的位置创建一个新文件夹(名称无关紧要)(如 Documents 文件夹)
*   将您要上传到 Wia 的文件添加到文件夹中

注意:文件可以是任何东西，文本图像等

在你最喜欢的文本编辑器中，创建一个新文件，命名为`file_upload_to_wia.py`

```
from wia import Wia
wia = Wia()
wia.access_token = 'your-device_secret_key'

file_name = ‘name_of_your_file’ 
dir_path = os.path.dirname(os.path.realpath(__file__))
result = wia.Event.publish(name='file', file=open(dir_path + '/’ + file_name, 'rb')) 
```

Enter fullscreen mode Exit fullscreen mode

[![alt text](../Images/e18679007fe15d25978d1647d35cd4ff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gReEfLTp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-04-12/1523537030-717816-selection-057.png)

*   用 Wia 的设备密钥替换你的设备密钥，如果你还没有，关于如何设置 Wia 的细节可以在这里找到。
*   用您想要上传的文件的名称替换`name_of_your_file`
*   保存文件

注意:文件和代码的位置必须在同一个文件夹中，以便教程中的代码无需修改即可运行。

### **运行代码**

对于 windows，

*   打开 powershell
*   cd 放入您的代码所在的文件夹
*   在 powershell 终端中运行 python `file_upload_to_wia.py`

对于 Mac/Linux，

*   打开终端
*   cd 放入您的代码所在的文件夹
*   在终端中运行`python file_upload_to_wia.py`

[![alt text](../Images/46b0a184a4938475ae8f643240f83283.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AFRT9IEd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-04-12/1523537728-927221-python-run.png)

前往您的 [Wia 仪表板](https://dashboard.wia.io/spaces)，在您的`Device`部分，点击`Events`选项卡，并点击`file`上的`View File`以查看文件。

[![alt text](../Images/679450f796f50d1b127bb72f11b02d93.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3QzfPdv7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-eu-west-1.amazonaws.com/wia-flarum-bucket/2018-04-12/1523538165-225663-photo-upload.png)

本教程到此为止！检查我们的其他[教程](https://community.wia.io/)，新的一直在增加。