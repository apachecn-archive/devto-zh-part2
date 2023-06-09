# 通过 PowerShell 删除多个 Azure Active Directory 应用程序

> 原文：<https://dev.to/mehmetseckin/delete-multiple-azure-active-directory-applications-via-powershell-4bm8>

# 通过 PowerShell 删除多个 Azure Active Directory 应用

最近，我需要一种快速的方法来删除多个 Azure Active Directory 应用程序。不幸的是，通过 Azure 门户这是不可能的，所以是时候来点 PowerShell 魔法了。

我想到了一个名为`Remove-AzureADApplications`的小命令，它将显示您的租户上的应用程序列表，并删除选定的应用程序。它还具有静默执行模式(`-Force`参数)和`display name begins with`过滤器选项(`-SearchString`参数)。

来看看:

```
<#
  .SYNOPSIS
  Delete multiple Azure AD applications based on a search criteria.

  .DESCRIPTION
  The Remove-AzureADApplications cmdlet displays a list of all applications registered in the Azure Active Directory, and deletes specified applications from Azure Active Directory (AD).

  .PARAMETER SearchString
  The service principal search string.

  .PARAMETER Force
  Forces the command to run without asking for user confirmation. This will remove all applications that match the filter criteria specified by the SearchString parameter, without displaying a list and waiting for the user to specify which applications are going to be deleted. If the SearchString parameter is omitted, this will attempt to delete all application registrations without confirmation. Use with caution.

  .EXAMPLE
  Remove-AzureADApplications

  Displays the list of all applications registered in the Azure Active Directory, and deletes selected applications.

  .EXAMPLE
  Remove-AzureADApplications -SearchString "deleteme" -Force

  Remove applications whose names start with "deleteme" without displaying a list for the user to select.
#>
function Remove-AzureADApplications 
{
    [CmdletBinding()]
    param
    (
        [Parameter(HelpMessage = "The service principal search string.")]
        [string]
        $SearchString = "",    
        [Parameter(HelpMessage ="Forces the command to run without asking for user confirmation. This will remove all applications that match the filter criteria specified by the SearchString parameter. If the SearchString parameter is omitted, this will attempt to delete all application registrations without confirmation. Use with caution.")]
        [Switch]
        $Force
    )

    Import-Module "AzureAD";

    if ($SearchString) {
        $apps = (Get-AzureADApplication -SearchString $SearchString)
    }
    else {
        Write-Warning "No search string specified. Fetching all applications."
        $apps = (Get-AzureADApplication -All $true)
    }

    if($Force)
    {
        $selectedApps = $apps;
    }
    else
    {
        $selectedApps = $apps | Out-GridView -Title "Please select applications to remove..." -OutputMode Multiple;
    }

    $selectedApps | ForEach-Object {

        $displayName = $_.DisplayName;
        $objectId = $_.ObjectId;
        try {
            Remove-AzureADApplication -ObjectId $objectId
            Write-Host "Removed $displayName..." -ForegroundColor Green;
        }
        catch {
            Write-Host "Failed to remove $displayName..." -ForegroundColor Red;
        }
    }
} 
```