# 如何使用 PnP PowerShell 将 SharePoint 列字段设置为只读/隐藏

> 原文：<https://dev.to/uisce/how-to-make-column-fields-ready-only-with-pnp-powershell-1ad2>

```
 Param(  $tenantUrl  =  "https://YOURSITE.sharepoint.com",  $spCredential  =  (Get-Credential),  )  $sites  =  @("siteOne",  "SiteTwo")  foreach($name  in  $sites)  {  Connect-PnPOnline  -Url  $tenantUrl/sites/$name  -Credentials  $spCredential  #Field Guids  $Summary  =  Get-PnPField  -Identity  98e1ecf3-3ae3-44a4-820c-f1de12ff346a  $Country  =  Get-PnPField  -Identity  7d9fd33a-e726-4d7f-b3ed-60e1c8e9690d  $Location  =  Get-PnPField  -Identity  0d2db348-a438-48f2-bcd2-461acd6f149f  $Language  =  Get-PnPField  -Identity  81b712c3-a41f-4e65-9a3a-8e7110bd9b5e  $Topic  =  Get-PnPField  -Identity  5f9fd169-0cf6-4779-a6be-e4635327c9a8  $columns  =  $Summary,  $Country,  $Location,  $Language,  $Topic  foreach  ($column  in  $columns)  {  try{  $column.ReadOnlyField  =  $true  $Column.SetShowInEditForm($false)  $column.UpdateAndPushChanges($true)  $column.Context.ExecuteQuery();  }  catch{  Write-Host  "error"  $columns  }  }  Write-Host  "finished updating read Only fields in"  $name  } 
```

Enter fullscreen mode Exit fullscreen mode