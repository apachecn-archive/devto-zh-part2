# 获取 ARM 模板中最新的 Azure API 版本

> 原文：<https://dev.to/owendavies/get-latest-azure-api-version-in-arm-templates-3ibc>

以下脚本将遍历文件夹中的所有 ARM 模板，并检查最新的 Azure API 版本。

如果你想确保你使用最新的 API，这是很有用的。

目前，如果你的 ARM 模板有一个更新版本的 API，你没有办法得到通知。如果你和我一样，你有数百个嵌套的 ARM 模板需要更新。跟踪您在所有文件中使用的所有资源是很困难的。

如果您将资源 API 版本保存在变量、参数中或者直接保存在 apiVersion 本身中，这将会考虑到。

## 看上去哪里的例子

根据 ARM 模板中的一些不同原因，我将资源 API 版本存储在多个位置。我试图尽可能多地存储在变量中以供重用，但通常我需要存储在参数中，以便我可以传递到嵌套模板中。

下面的 JSON ARM 模板文件在 3 个地方存储了 API 版本的示例:

1.  直接在资源中
2.  在变量中
3.  在参数中

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "apiVersionDisks": "2017-06-01"
  },
  "variables": {
    "apiVersionResourcesDeployment": "2017-05-10",
    "apiVersionDisks": "2017-04-01",
    "apiVersionStorage": "2017-04-01"
  },
  "resources": [{
      "apiVersion": "[variables('apiVersionResourcesDeployment')]",
      "type": "Microsoft.Resources/deployments"
    },
    {
      "name": "Name1",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "[variables('apiVersionStorage')]",
    },
    {
      "name": "Name1",
      "type": "Microsoft.Compute/disks",
      "apiVersion": "[parameters('apiVersionDisks')]",
    },
    {
      "name": "Name1",
      "type": "Microsoft.Compute/disks",
      "apiVersion": "2015-06-15",
    }
  ]
} 
```

## 剧本！

该脚本将检查所有 3 个位置的 API 版本。

```
$templatePath = 'C:\YOUR\ARM\PATH'

$listOfResources = @()

Get-ChildItem $templatePath -Recurse -Filter *.json |
Foreach-Object {

    $content = (Get-Content $_.FullName | ConvertFrom-Json)

    foreach($resource in $content.resources)
    {
        $resourceObject = New-Object -TypeName pscustomobject -Property @{
            "fileName" = $_.FullName
            "resourceName" = $($resource.type)
            "apiVersion" = ""
            "apiVersionVariableName" = ""
            "apiVersionParameterName" = ""
            "latestApiVersion" = ""
        }

        if($($resource.apiVersion) -like '*variables*')
        {
            $apiVariable = ($resource.apiVersion).replace("[variables('","").replace("')]","")

            $resourceObject.apiVersionVariableName = $apiVariable
            $resourceObject.apiVersion = $($($content.variables).$apiVariable)
        }
        elseif($resource.apiVersion -like '*parameters*')
        {
            $apiVariable = ($resource.apiVersion).replace("[parameters('","").replace("')]","")

            $apiParameter = ($resource.apiVersion).replace("[parameters('","").replace("')]","")

            $resourceObject.apiVersionParameterName = $apiParameter
            $resourceObject.apiVersion = $($($content.parameters).$apiParameter)
        }
        else
        {
            $resourceObject.apiVersion = $($resource.apiVersion)
        }

        $listOfResources += $resourceObject
    }
}

$resources = $listOfResources.resourceName | Sort-Object | Get-Unique

Foreach($i in $resources)
{   
    $providerNamespace,$resourceTypeName = $i.split("/",2)
    $latestApiVersion = ((Get-AzureRmResourceProvider -ProviderNamespace $providerNamespace).ResourceTypes | Where-Object ResourceTypeName -eq $resourceTypeName).ApiVersions | select -First 1

    $editObj = $listOfResources | where {$_.resourceName -eq $i}
    foreach($r in $editObj)
    {
        $r.latestApiVersion=$latestApiVersion   
    }

}

Foreach($resource in $listOfResources)
{
    if($resource.latestApiVersion -ne $resources.apiVersion)
    {
        Write-Host "`nThere is a new ApiVersion for $($resource.resourceName)"
        Write-Host "Filename: [$($resource.fileName)]"
        Write-Host "Current ApiVersion: [$($resource.apiVersion)]"
        Write-Host "Latest ApiVersion: [$($resource.latestApiVersion)]"
    }
} 
```

如果你觉得这有用，请告诉我！:-)