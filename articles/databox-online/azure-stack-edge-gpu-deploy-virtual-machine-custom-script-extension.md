---
title: Användning av anpassade skript tillägg för virtuella datorer på din Azure Stack Edge Pro-enhet
description: Beskriver hur du installerar anpassade skript tillägg på virtuella datorer (VM) som körs på en Azure Stack Edge Pro-enhet med hjälp av mallar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 12/21/2020
ms.author: alkohli
ms.openlocfilehash: f622d6e752b658172bce115cde4f30d555e2ed3a
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763859"
---
# <a name="deploy-custom-script-extension-on-vms-running-on-your-azure-stack-edge-pro-device"></a>Distribuera anpassat skript tillägg på virtuella datorer som körs på din Azure Stack Edge Pro-enhet

Det anpassade skript tillägget laddar ned och kör skript eller kommandon på virtuella datorer som körs på dina Azure Stack Edge Pro-enheter. Den här artikeln beskriver hur du installerar och kör det anpassade skript tillägget med hjälp av en Azure Resource Manager-mall. 

Den här artikeln gäller Azure Stack Edge Pro GPU, Azure Stack Edge Pro R och Azure Stack Edge Mini R-enheter.

## <a name="about-custom-script-extension"></a>Om anpassat skript tillägg

Det anpassade skript tillägget är användbart för konfiguration efter distribution, program varu installation eller andra konfigurations-och hanterings åtgärder. Du kan ladda ned skript från Azure Storage eller en annan tillgänglig Internet plats, eller så kan du ange skript eller kommandon i tilläggs körningen.

Det anpassade skript tillägget integreras med Azure Resource Manager mallar. Du kan också köra det med hjälp av Azure CLI, PowerShell eller Azure Virtual Machines REST API.

## <a name="os-for-custom-script-extension"></a>OS för anpassat skript tillägg

#### <a name="supported-os-for-custom-script-extension-on-windows"></a>Operativ system som stöds för anpassat skript tillägg i Windows

Det anpassade skript tillägget för Windows kommer att köras på följande OSs. Andra versioner kan fungera men har inte testats internt på virtuella datorer som körs på Azure Stack Edge Pro-enheter.

| Distribution | Version |
|---|---|
| Windows Server 2019 | Kärna |
| Windows Server 2016 | Kärna |

#### <a name="supported-os-for-custom-script-extension-on-linux"></a>Operativ system som stöds för anpassat skript tillägg på Linux

Det anpassade skript tillägget för Linux kommer att köras på följande OSs. Andra versioner kan fungera men har inte testats internt på virtuella datorer som körs på Azure Stack Edge Pro-enheter.

| Distribution | Version |
|---|---|
| Linux: Ubuntu | 18,04 LTS |
| Linux: Red Hat Enterprise Linux | 7,4 |

<!--### Script location

Instead of the scripts, in this article, we pass a command to execute via the Custom Script Extension. 

### Internet Connectivity

To download a script externally such as from GitHub or Azure Storage, make sure that the port on which you enable compute network, is connected to the internet. 

If your script is on a local server, then you may still need additional firewall and Network Security Group ports need to be opened.

> [!NOTE]
> Before you install the Custom Script extension, make sure that the port enabled for compute network on your device is connected to Internet and has access. -->

## <a name="prerequisites"></a>Förutsättningar

1. [Ladda ned filerna för VM-mallarna och-parametrarna](https://aka.ms/ase-vm-templates) till klient datorn. Zippa upp den till en katalog som du ska använda som arbets katalog.

1. Du bör ha en virtuell dator som skapats och distribuerats på enheten. Om du vill skapa virtuella datorer följer du alla steg i den [distribuera virtuella datorn på Azure Stack Edge Pro med hjälp av mallar](azure-stack-edge-gpu-deploy-virtual-machine-templates.md).

    Om du behöver hämta ett skript externt, till exempel från GitHub eller Azure Storage, när du konfigurerar beräknings nätverk, aktiverar du den port som är ansluten till Internet för data bearbetning. På så sätt kan du hämta skriptet.

    Här är ett exempel där port 2 var ansluten till Internet och användes för att aktivera beräknings nätverket. Om du har identifierat att Kubernetes inte behövs i det tidigare steget kan du hoppa över noden IP-adress för Kubernetes och extern tjänst.    

    ![Aktivera beräknings inställningar på port ansluten till Internet](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

## <a name="install-custom-script-extension"></a>Installera anpassat skript tillägg

Beroende på operativ systemet för den virtuella datorn kan du installera anpassat skript tillägg för Windows eller Linux.
 

### <a name="custom-script-extension-for-windows"></a>Anpassat skripttillägg för Windows

Om du vill distribuera ett anpassat skript tillägg för Windows för en virtuell dator som körs på enheten redigerar du `addCSExtWindowsVM.parameters.json` parameter filen och distribuerar sedan mallen `addCSextensiontoVM.json` .

#### <a name="edit-parameters-file"></a>Redigera parameter fil

Filen `addCSExtWindowsVM.parameters.json` tar följande parametrar:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of VM>" 
        },
        "extensionName": {
            "value": "<Name of extension>" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
Ange namn på virtuell dator, namn på tillägget och kommandot som du vill köra.

Här är en exempel parameter fil som användes i den här artikeln. 

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM5" 
        },
        "extensionName": {
            "value": "CustomScriptExtension" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "md C:\\Users\\Public\\Documents\\test"
            }
        }
    }
}
```
#### <a name="deploy-template"></a>Distribuera mallen

Distribuera mallen `addCSextensiontoVM.json` . Den här mallen distribuerar tillägg till en befintlig virtuell dator. Kör följande kommando:

```powershell
$templateFile = "<Path to addCSExtensiontoVM.json file>"
$templateParameterFile = "<Path to addCSExtWindowsVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
```
> [!NOTE]
> Tilläggs distributionen är ett långvarigt jobb och tar cirka 10 minuter att slutföra.

Här är exempel på utdata:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment7"

DeploymentName          : deployment7
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/17/2020 10:07:44 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM5
                          extensionName    String                     CustomScriptExtension
                          publisher        String                     Microsoft.Compute
                          type             String                     CustomScriptExtension
                          typeHandlerVersion  String                     1.10
                          settings         Object                     {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```
#### <a name="track-deployment"></a>Spåra distribution

Kör följande kommando för att kontrol lera distributions statusen för tillägg för en specifik virtuell dator: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName <Name of resource group> -VMName <Name of VM> -Name <Name of the extension>
```
Här är exempel på utdata:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> När distributionen är klar `ProvisioningState` ändras ändringarna i `Succeeded` .

Utökning av utdata loggas till filer som finns i följande mapp på den virtuella mål datorn. 

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

De angivna filerna laddas ned till följande mapp på den virtuella mål datorn.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
där <n> är ett decimal tal som kan ändras mellan körningar av tillägget. Värdet 1. * matchar det faktiska, aktuella `typeHandlerVersion` värdet för tillägget. Till exempel var den faktiska katalogen i den här instansen `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.9\Downloads\0` . 


I den här instansen var det kommando som ska köras för det anpassade tillägget: `md C:\\Users\\Public\\Documents\\test` . När tillägget har installerats kan du kontrol lera att katalogen har skapats i den virtuella datorn på den angivna sökvägen i kommandot. 


### <a name="custom-script-extension-for-linux"></a>Anpassat skript tillägg för Linux

Om du vill distribuera ett anpassat skript tillägg för Windows för en virtuell dator som körs på enheten redigerar du `addCSExtLinuxVM.parameters.json` parameter filen och distribuerar sedan mallen `addCSExtensiontoVM.json` .

#### <a name="edit-parameters-file"></a>Redigera parameter fil

Filen `addCSExtLinuxVM.parameters.json` tar följande parametrar:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of your VM>" 
        },
        "extensionName": {
            "value": "<Name of your extension>" 
        },
        "publisher": {
            "value": "Microsoft.Azure.Extensions" 
        },
        "type": {
            "value": "CustomScript" 
        },
        "typeHandlerVersion": {
            "value": "2.0" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
Ange namn på virtuell dator, namn på tillägget och kommandot som du vill köra.

Här är en exempel parameter fil som användes i den här artikeln:

```powershell
$templateFile = "<Path to addCSExtensionToVM.json file>"
$templateParameterFile = "<Path to addCSExtLinuxVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
``` 

> [!NOTE]
> Tilläggs distributionen är ett långvarigt jobb och tar cirka 10 minuter att slutföra.

Här är exempel på utdata:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensionToVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment99"

DeploymentName          : deployment99
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/18/2020 1:55:23 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM6
                          extensionName    String                     LinuxCustomScriptExtension
                          publisher        String                     Microsoft.Azure.Extensions
                          type             String                     CustomScript
                          typeHandlerVersion  String                     2.0
                          settings         Object                     {
                            "commandToExecute": "sudo echo 'some text' >> /home/Administrator/file2.txt"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```

`commandToExecute`Har angetts för att skapa en fil `file2.txt` i `/home/Administrator` katalogen och filens innehåll är `some text` . I så fall kan du kontrol lera att filen har skapats på den angivna sökvägen.

```powershell
Administrator@VM6:~$ dir
file2.txt
Administrator@VM6:~$ cat file2.txt
some text
Administrator@VM6:
```

#### <a name="track-deployment-status"></a>Spåra distributions status    
    
Malldistribution är ett jobb som körs länge. Öppna en annan PowerShell-session (kör som administratör) om du vill kontrol lera distributions statusen för tillägg för en specifik virtuell dator. Kör följande kommando: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
Här är exempel på utdata: 

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> När distributionen är klar `ProvisioningState` ändras ändringarna i `Succeeded` .

Utdata för att köra tillägg loggas i följande fil: `/var/lib/waagent/custom-script/download/0/` .


## <a name="remove-custom-script-extension"></a>Ta bort anpassat skript tillägg

Om du vill ta bort det anpassade skript tillägget använder du följande kommando:

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

Här är exempel på utdata:

```powershell
PS C:\WINDOWS\system32> Remove-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM6 -Name LinuxCustomScriptExtension
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Yes
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```


## <a name="next-steps"></a>Nästa steg

[Azure Resource Manager-cmdletar](/powershell/module/azurerm.resources/?view=azurermps-6.13.0)