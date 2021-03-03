---
title: Översikt och distribution av GPU-virtuella datorer på din Azure Stack Edge Pro-enhet
description: 'Beskriver hur du skapar och hanterar virtuella GPU-datorer (VM: ar) på en Azure Stack Edge Pro-enhet med hjälp av mallar.'
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 12/21/2020
ms.author: alkohli
ms.openlocfilehash: cb6b8e310f46cd98c4d3343d44fd83a3cc714a32
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730719"
---
# <a name="gpu-vms-for-your-azure-stack-edge-pro-device"></a>GPU-virtuella datorer för din Azure Stack Edge Pro-enhet

Den här artikeln innehåller en översikt över virtuella GPU-datorer (VM) på din Azure Stack Edge Pro-enhet. I artikeln beskrivs hur du skapar en GPU-VM och sedan installerar GPU-drivrutiner för att installera lämpliga NVIDIA-drivrutiner. Använd Azure Resource Manager mallar för att skapa den virtuella GPU-datorn och installera GPU-drivrutinen. 

Den här artikeln gäller för Azure Stack Edge Pro GPU och Azure Stack Edge Pro R-enheter.

## <a name="about-gpu-vms"></a>Om GPU-virtuella datorer

Dina Azure Stack Edge Pro-enheter är utrustade med 1 eller 2 av NVIDIA: s Tesla T4-GPU. Använd GPU-optimerade VM-storlekar för att distribuera GPU-accelererade VM-arbetsbelastningar på dessa enheter. Till exempel bör NC T4 v3-serien användas för att distribuera arbets belastningar med T4-GPU: er. 

Mer information finns i [NC T4 v3-seriens virtuella datorer](../virtual-machines/nct4-v3-series.md).

## <a name="supported-os-and-gpu-drivers"></a>Operativ system och GPU-drivrutiner som stöds 

För att kunna dra nytta av GPU-funktionerna i virtuella datorer i Azure N-serien måste du installera nVidia GPU-drivrutiner. 

NVidia GPU driv rutins tillägget installerar lämpliga NVIDIA-CUDA eller RUTNÄTs driv rutiner. Du kan installera eller hantera tillägget med Azure Resource Manager-mallarna.

### <a name="supported-os-for-gpu-extension-for-windows"></a>Operativ system som stöds för GPU-tillägg för Windows

Det här tillägget stöder följande operativ system (OSs). Andra versioner kan fungera men har inte testats internt på GPU-datorer som körs på Azure Stack Edge Pro-enheter.

| Distribution | Version |
|---|---|
| Windows Server 2019 | Kärna |
| Windows Server 2016 | Kärna |

### <a name="supported-os-for-gpu-extension-for-linux"></a>Operativ system som stöds för GPU-tillägg för Linux

Det här tillägget har stöd för följande OS-distributioner, beroende på driv rutins stöd för en speciell OS-version. Andra versioner kan fungera men har inte testats internt på GPU-datorer som körs på Azure Stack Edge Pro-enheter.


| Distribution | Version |
|---|---|
| Ubuntu | 18,04 LTS |
| Red Hat Enterprise Linux | 7,4 |


## <a name="gpu-vms-and-kubernetes"></a>GPU VM och Kubernetes

Innan du distribuerar GPU-virtuella datorer på enheten bör du gå igenom följande om Kubernetes har kon figurer ATS på enheten.

#### <a name="for-1-gpu-device"></a>För 1 – GPU-enhet: 

- **Skapa en GPU-VM följt av Kubernetes-konfigurationen på enheten**: i det här scenariot kommer den virtuella datorn för skapande av GPU-datorer och Kubernetes att lyckas. Kubernetes kommer inte att ha åtkomst till GPU i det här fallet.

- **Konfigurera Kubernetes på enheten följt av en GPU-dator**: i det här scenariot ansöker Kubernetes GPU på enheten och den virtuella datorn kommer inte att fungera eftersom det inte finns några GPU-resurser tillgängliga.

#### <a name="for-2-gpu-device"></a>För 2-GPU-enhet

- **Skapa en GPU-VM följt av Kubernetes-konfigurationen på enheten**: i det här scenariot kommer den GPU-dator som du skapar att kräva en GPU på din enhet och Kubernetes-konfigurationen kommer också att lyckas och kräva att den återstående en GPU används. 

- **Skapa två GPU-datorer följt av Kubernetes-konfigurationen på enheten**: i det här scenariot ansluts de två GPU-datorerna på enheten och Kubernetes har kon figurer ATS utan GPU: er. 

- **Konfigurera Kubernetes på din enhet följt av en virtuell GPU-dator**: i det här scenariot ansluts Kubernetes både GPU: er på enheten och den virtuella datorn kommer inte att fungera eftersom inga GPU-resurser är tillgängliga.

Om du har en virtuell dator med GPU: er som körs på enheten och Kubernetes har kon figurer ATS, så finns det en risk för att Kubernetes-klustret kommer att kräva att alla GPU: er som är tillgängliga på enheten anStop-AzureRmVM sluts. I en sådan instans kan du inte starta om de GPU-datorer som distribuerats på enheten eller skapa GPU-datorer.


## <a name="create-gpu-vms"></a>Skapa GPU-virtuella datorer

Följ dessa steg när du distribuerar GPU-virtuella datorer på enheten:

1. Ta reda på om din enhet också ska köra Kubernetes. Om enheten ska köra Kubernetes måste du först skapa den virtuella GPU-datorn och sedan konfigurera Kubernetes. Om Kubernetes har kon figurer ATS först anspråks alla tillgängliga GPU-resurser och det går inte att skapa GPU-datorer.

1. [Ladda ned filerna för VM-mallarna och-parametrarna](https://aka.ms/ase-vm-templates) till klient datorn. Zippa upp den till en katalog som du ska använda som arbets katalog.

1. Om du vill skapa virtuella GPU-datorer följer du alla steg i den [distribuera virtuella datorn på Azure Stack Edge Pro med hjälp av mallar](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) , förutom följande skillnader: 

    1. När du konfigurerar Compute Network aktiverar du den port som är ansluten till Internet för data bearbetning. På så sätt kan du hämta GPU-drivrutinerna som krävs för GPU-tillägg för dina GPU-datorer.

        Här är ett exempel där port 2 var ansluten till Internet och användes för att aktivera beräknings nätverket. Om du har identifierat att Kubernetes inte behövs i det tidigare steget kan du hoppa över noden IP-adress för Kubernetes och extern tjänst.

        ![Aktivera beräknings inställningar på port ansluten till Internet](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

            
    1. Skapa en virtuell dator med mallarna. När du anger storlekar för GPU-datorer ska du se till att använda NCasT4-v3-serien i `CreateVM.parameters.json` som de stöds för GPU-virtuella datorer. Mer information finns i [VM-storlekar som stöds för GPU-virtuella datorer](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview).

        ```json
            "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        ```

    1. När GPU-datorn har skapats kan du Visa den här virtuella datorn i listan över virtuella datorer i din Azure Stack Edge-resurs i Azure Portal.

        ![Virtuell GPU-dator i listan över virtuella datorer i Azure Portal](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machine-1.png)

1. Välj den virtuella datorn och öka detalj nivån till detaljerna. Kopiera den IP-adress som allokeras till den virtuella datorn.

    ![IP allokeras till den virtuella GPU-datorn i Azure Portal](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/get-ip-gpu-virtual-machine-1.png)

1. När den virtuella datorn har skapats distribuerar du GPU-tillägget med hjälp av tilläggs mal len. För virtuella Linux-datorer, se [Installera GPU-tillägg för Linux](#gpu-extension-for-linux) och för virtuella Windows-datorer, se [Installera GPU-tillägg för Windows](#gpu-extension-for-windows).

1. Om du vill kontrol lera installationen av GPU-tillägget ansluter du till den virtuella GPU-datorn:
    1. Om du använder en virtuell Windows-dator följer du stegen i [ansluta till en virtuell Windows-dator](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-windows-vm). [Verifiera installationen](#verify-windows-driver-installation).
    1. Om du använder en virtuell Linux-dator följer du stegen i [ansluta till en virtuell Linux-dator](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-linux-vm). [Verifiera installationen](#verify-linux-driver-installation).

1. Om det behövs kan du byta tillbaka beräknings nätverket till det du behöver. 


> [!NOTE]
> När du uppdaterar enhetens program varu version från 2012 till senare måste du manuellt stoppa de virtuella GPU-datorerna.


## <a name="install-gpu-extension"></a>Installera GPU-tillägg

Beroende på operativ systemet för den virtuella datorn kan du installera GPU-tillägg för Windows eller Linux.

> [!NOTE]
> Innan du installerar GPU-tillägget kontrollerar du att porten som är aktive rad för beräknings nätverk på enheten är ansluten till Internet och har åtkomst. GPU-drivrutinerna hämtas via Internet åtkomst.

### <a name="gpu-extension-for-windows"></a>GPU-tillägg för Windows

Om du vill distribuera nVidia GPU-drivrutiner för en befintlig virtuell dator redigerar du `addGPUExtWindowsVM.parameters.json` parameter filen och distribuerar sedan mallen `addGPUextensiontoVM.json` .

#### <a name="edit-parameters-file"></a>Redigera parameter fil

Filen `addGPUExtWindowsVM.parameters.json` tar följande parametrar:

```json
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: windowsGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverWindows" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DriverURL" : "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
    "DriverCertificateUrl" : "https://go.microsoft.com/fwlink/?linkid=871664",
    "DriverType":"CUDA"
    }
    }
    }
```

Här är en exempel parameter fil som användes i den här artikeln:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateVM\CreateVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateVM\CreateVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment2"

DeploymentName          : deployment2
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:02:56 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          adminUsername    String                     Administrator
                          password         String                     Password1
                          imageName        String                     myasewindowsimg
                          vmSize           String                     Standard_NC4as_T4_v3
                          vnetName         String                     ASEVNET
                          vnetRG           String                     aserg
                          subnetName       String                     ASEVNETsubNet
                          nicName          String                     nic6
                          ipConfigName     String                     ipconfig6
                          privateIPAddress  String

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```
#### <a name="deploy-template"></a>Distribuera mallen

Distribuera mallen `addGPUextensiontoVM.json` . Den här mallen distribuerar tillägg till en befintlig virtuell dator. Kör följande kommando:

```powershell
$templateFile = "<Path to addGPUextensiontoVM.json>" 
$templateParameterFile = "<Path to addGPUExtWindowsVM.parameters.json>" 
$RGName = "<Name of your resource group>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
```
> [!NOTE]
> Tilläggs distributionen är ett långvarigt jobb och tar cirka 10 minuter att slutföra.

Här är exempel på utdata:

```powershell
PS C:\WINDOWS\system32> "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment3"

DeploymentName          : deployment3
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:18:50 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          extensionName    String                     windowsgpuext
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverWindows
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
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
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM2 -Name windowsgpuext

ResourceGroupName       : myasegpuvm1
VMName                  : VM2
Name                    : windowsgpuext
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverWindows
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM2/extensions/windowsgpuext
PublicSettings          : {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

Utökning av utdata loggas i följande fil. Läs den här filen `C:\Packages\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\1.3.0.0\Status` om du vill följa installations status. 


En lyckad installation indikeras av en `message` as `Enable Extension` och `status` som `success` .

```powershell
"status":  {
                       "formattedMessage":  {
                                                "message":  "Enable Extension",
                                                "lang":  "en"
                                            },
                       "name":  "NvidiaGpuDriverWindows",
                       "status":  "success",
```

#### <a name="verify-windows-driver-installation"></a>Verifiera installationen av Windows-drivrutinen

Logga in på den virtuella datorn och kör kommando rads verktyget NVIDIA-SMI installerat med driv rutinen. Finns `nvidia-smi.exe` på  `C:\Program Files\NVIDIA Corporation\NVSMI\nvidia-smi.exe` . Om du inte ser filen är det möjligt att driv rutins installationen fortfarande körs i bakgrunden. Vänta i 10 minuter och kontrol lera igen.

Om driv rutinen är installerad visas utdata som liknar följande exempel: 

```powershell
PS C:\Users\Administrator> cd "C:\Program Files\NVIDIA Corporation\NVSMI"
PS C:\Program Files\NVIDIA Corporation\NVSMI> ls

    Directory: C:\Program Files\NVIDIA Corporation\NVSMI

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        2/26/2020  12:00 PM         849640 MCU.exe
-a----        2/26/2020  12:00 PM         443104 nvdebugdump.exe
-a----        2/25/2020   2:06 AM          81823 nvidia-smi.1.pdf
-a----        2/26/2020  12:01 PM         566880 nvidia-smi.exe
-a----        2/26/2020  12:01 PM         991344 nvml.dll

PS C:\Program Files\NVIDIA Corporation\NVSMI> .\nvidia-smi.exe
Wed Dec 16 00:35:51 2020
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 442.50       Driver Version: 442.50       CUDA Version: 10.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name            TCC/WDDM | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla T4            TCC  | 0000503C:00:00.0 Off |                    0 |
| N/A   35C    P8    11W /  70W |      8MiB / 15205MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
PS C:\Program Files\NVIDIA Corporation\NVSMI>
```

Mer information finns i [tillägg för NVIDIA GPU-drivrutiner för Windows](../virtual-machines/extensions/hpccompute-gpu-windows.md).

### <a name="gpu-extension-for-linux"></a>GPU-tillägg för Linux

Om du vill distribuera nVidia GPU-drivrutiner för en befintlig virtuell dator redigerar du parameter filen och distribuerar sedan mallen `addGPUextensiontoVM.json` . Det finns vissa parametrar-filer för Ubuntu och Red Hat Enterprise Linux (RHEL) som beskrivs i följande avsnitt.

#### <a name="edit-parameters-file"></a>Redigera parameter fil

Om du använder Ubuntu, `addGPUExtLinuxVM.parameters.json` tar filen med följande parametrar:

```powershell
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: linuxGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverLinux" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
    "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
    "CUDA_ver": "10.0.130",
    "InstallCUDA": "true"
    }
    }
    }
```
Om du använder Red Hat Enterprise Linux (RHEL) `addGPUExtensionRHELVM.parameters.json` tar filen med följande parametrar:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<name of the VM>" 
        },
        "extensionName": {
            "value": "<name for the extension. Example: linuxGpu>" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
            "value": {
                    "isCustomInstall":true,
                    "DRIVER_URL":"https://go.microsoft.com/fwlink/?linkid=874273",
                    "CUDA_ver":"10.0.130",
                    "PUBKEY_URL":"http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                    "DKMS_URL":"https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm",
                    "LIS_URL":"https://aka.ms/lis",
                    "LIS_RHEL_ver":"3.10.0-1062.9.1.el7"
            }
        }
    }
}
```


Här är en exempel på en Ubuntu parameter fil som användes i den här artikeln:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM1" 
        },
        "extensionName": {
            "value": "gpuLinux" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
            "value": {
            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
            "CUDA_ver": "10.0.130",
            "InstallCUDA": "true"
            }
        }
    }
}
```


#### <a name="deploy-template"></a>Distribuera mallen

Distribuera mallen `addGPUextensiontoVM.json` . Den här mallen distribuerar tillägg till en befintlig virtuell dator. Kör följande kommando:

```powershell
$templateFile = "Path to addGPUextensiontoVM.json" 
$templateParameterFile = "Path to addGPUExtLinuxVM.parameters.json" 
$RGName = "<Name of your resource group>" 
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
``` 

> [!NOTE]
> Tilläggs distributionen är ett långvarigt jobb och tar cirka 10 minuter att slutföra.

Här är exempel på utdata:

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "rg2"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "delpoyment7"

DeploymentName          : delpoyment7
ResourceGroupName       : rg2
ProvisioningState       : Succeeded
Timestamp               : 12/10/2020 10:43:23 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM1
                          extensionName    String                     gpuLinux
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverLinux
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL":
                          "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```

#### <a name="track-deployment-status"></a>Spåra distributions status    
    
Malldistribution är ett jobb som körs länge. Öppna en annan PowerShell-session (kör som administratör) om du vill kontrol lera distributions statusen för tillägg för en specifik virtuell dator. Kör följande kommando: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
Här är exempel på utdata: 

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName rg2 -VMName VM1 -Name gpulinux

ResourceGroupName       : rg2
VMName                  : VM1
Name                    : gpuLinux
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverLinux
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg2/providers/Microsoft.Compute/virtualMachines/VM1/extensions/gpuLinux
PublicSettings          : {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
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

Utdata för att köra tillägg loggas i följande fil: `/var/log/azure/nvidia-vmext-status` .

#### <a name="verify-linux-driver-installation"></a>Verifiera installationen av Linux-drivrutinen

Följ dessa steg för att verifiera installationen av driv rutinen:

1. Anslut till den virtuella GPU-datorn. Följ anvisningarna i [ansluta till en virtuell Linux-dator](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-linux-vm). 

    Här är exempel på utdata:

    ```powershell
    PS C:\WINDOWS\system32> ssh -l Administrator 10.57.50.60
    Administrator@10.57.50.60's password:
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
      System information as of Thu Dec 10 22:57:01 UTC 2020
    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
    
    249 packages can be updated.
    140 updates are security updates.
    
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage    
      System information as of Thu Dec 10 22:57:01 UTC 2020    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
        
    249 packages can be updated.
    140 updates are security updates.
    
    New release '20.04.1 LTS' available.
    Run 'do-release-upgrade' to upgrade to it.
        
    *** System restart required ***
    Last login: Thu Dec 10 21:49:29 2020 from 10.90.24.23
    To run a command as administrator (user "root"), use "sudo <command>".
    See "man sudo_root" for details.
    
    Administrator@VM1:~$
    ```
2. Kör kommando rads verktyget NVIDIA-SMI installerat med driv rutinen. Om driv rutinen har installerats kan du köra verktyget och se följande utdata:

    ```powershell
    Administrator@VM1:~$ nvidia-smi
    Thu Dec 10 22:58:46 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 455.45.01    Driver Version: 455.45.01    CUDA Version: 11.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            Off  | 0000941F:00:00.0 Off |                    0 |
    | N/A   48C    P0    27W /  70W |      0MiB / 15109MiB |      5%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    Administrator@VM1:~$
    ```

Mer information finns i [tillägg för NVIDIA GPU-drivrutiner för Linux](../virtual-machines/extensions/hpccompute-gpu-linux.md).

## <a name="remove-gpu-extension"></a>Ta bort GPU-tillägg

Om du vill ta bort GPU-tillägget använder du följande kommando:

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

Här är exempel på utdata:

```powershell
PS C:\azure-stack-edge-deploy-vms> Remove-AzureRmVMExtension -ResourceGroupName rgl -VMName WindowsVM -Name windowsgpuext
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue? [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
Requestld IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------    
          True                OK         OK
```




## <a name="next-steps"></a>Nästa steg

[Azure Resource Manager-cmdletar](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)