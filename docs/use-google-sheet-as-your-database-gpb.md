# 使用谷歌电子表格作为你的数据库

> 原文：<https://dev.to/sahilrajput/use-google-sheet-as-your-database-gpb>

今天，我们将看看如何使用谷歌电子表格作为我们的数据库。

比如，如果你正在创建一些内部应用程序，而你认为你可能需要一个数据库，那你就错了。

你需要的只是 Google Spread Sheet 和 Python。遵循这些步骤，你就会知道怎么做了。

1.  转到 [Google API 管理器](https://console.cloud.google.com/apis/dashboard)并创建一个项目。![google_api_manager](../Images/3f655b9e16fa2d657fccf965164376e1.png) ![name_project](../Images/53a29cc4a374b274db524b1d6e68e193.png)这是我们的[电子表格](https://docs.google.com/spreadsheets/d/1fnOhXNT2vyuguQzGN1YSe-vsxFcDXIgFPIw_PbHaox0/edit?usp=sharing)，我们将使用它作为我们的数据库。![spread_sheet](../Images/26b10f705a917961a8bcde1c0dcfc729.png)
2.  将 google drive api 添加到项目中，这将允许我们访问 google sheets 帐户中的电子表格。![add_api1](../Images/6047f5a80d68023d3d49003f371e55a6.png)![add_api2](../Images/d3563e5efcbcd08a542910532addc66b.png)T2】
3.  一旦添加，我们需要创建一些凭证。![create_credential](../Images/f5cb12eef9432385ccb6ad158e4b7f60.png)因为我们是从 web 服务器上进行的，所以我们将添加“web 服务器”选项，并允许它访问应用程序数据。![choose_options](../Images/14d3371e98aab89a3b3cb9c6b00fc814.png)填写选项后，点击“我需要什么凭证”。
4.  接下来，我们将创建一个服务帐户，并允许角色“项目编辑者”访问和编辑 api 中的数据。
5.  单击 continue，它将生成一个 json 文件，我将重命名该文件并将其添加到项目中，命名为“statup_funding.json”。![cmd](../Images/1e7066545050d807015624f0b0c8ec99.png)
6.  在文本编辑器中打开该文件，我们将在一个名为“client_email”的属性中找到一个电子邮件地址。
7.  复制并将其放在电子表格上，我们可以将电子表格共享到该电子邮件地址，以便从 api 访问电子表格。![share_spreadsheet](../Images/b5498be62319988b2d85297252f60fec.png)
8.  使用 pip 安装 gspread 和 oauth2client 包。

```
$ pip install gspread oauth2client 
```

Enter fullscreen mode Exit fullscreen mode

[![install](../Images/db0aa050754b2545d853e0b574f8e98a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---vhpd9_h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/20112458/47849066-5a364700-ddf6-11e8-9bdb-66311772354e.png)

1.  然后，我们将创建一个文件“tutorial.py ”,在其中编写我们的代码，并使用 google sheets 作为我们的数据库。

## Python 程序

```
#import library
import gspread
#Service client credential from oauth2client
from oauth2client.service_account import ServiceAccountCredentials
# Print nicely
import pprint
#Create scope
scope = ['https://spreadsheets.google.com/feeds']
#create some credential using that scope and content of startup_funding.json
creds = ServiceAccountCredentials.from_json_keyfile_name('startup_funding.json',scope)
#create gspread authorize using that credential
client = gspread.authorize(creds)
#Now will can access our google sheets we call client.open on StartupName
sheet = client.open('StartupName').sheet1
pp = pprint.PrettyPrinter()
#Access all of the record inside that
result = sheet.get_all_record() 
```

Enter fullscreen mode Exit fullscreen mode

我们可以做更多事情，比如访问特定行/列/单元格中的数据。

```
result = sheet.row_values(5) #See individual row
# result = sheet.col.values(5) #See individual column
#result = sheet.cell(5,2) # See particular cell
pp = pprint.PrettyPrinter() 
```

Enter fullscreen mode Exit fullscreen mode

更新特定单元格和更多内容

```
#update values
sheet.update_cell(2,9,'500000')  #Change value at cell(2,9) in the sheet
result = sheet.cell(2,9)
pp.pprint(result) 
```

Enter fullscreen mode Exit fullscreen mode

希望这篇文章对你们有所帮助。