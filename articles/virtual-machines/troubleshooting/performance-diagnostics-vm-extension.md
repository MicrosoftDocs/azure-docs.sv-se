---
title: VM-tillägg för Azure Performance Diagnostics för Windows | Microsoft Docs
description: Introducerar VM-tillägget för Azure Performance Diagnostics för Windows.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 9edba575b35613abb8bc3081964a37b838bb358b
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656603"
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Azures VM-tillägg för prestandadiagnostik för Windows

Med VM-tillägget för Azure Performance Diagnostics kan du samla in prestanda diagnostikdata från virtuella Windows-datorer. Tillägget utför analyser och innehåller en rapport över resultat och rekommendationer för att identifiera och lösa prestanda problem på den virtuella datorn. Det här tillägget installerar ett fel söknings verktyg som kallas [PerfInsights](./how-to-use-perfinsights.md).

> [!NOTE]
> Om du vill köra diagnostik på den virtuella datorn från Azure Portal för icke-klassiska virtuella datorer, rekommenderar vi att du använder den nya upplevelsen. Mer information finns i [prestandadiagnostik för Azure Virtual Machines](performance-diagnostics.md) 

## <a name="prerequisites"></a>Krav

Tillägget kan installeras på
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows 10
* Windows 8,1
* Windows 8

## <a name="extension-schema"></a>Tilläggsschema
Följande JSON visar schemat för Azure Performance Diagnostics VM-tillägget. Det här tillägget kräver namnet och nyckeln för ett lagrings konto för att lagra utdata och rapporter för diagnostik. Dessa värden är känsliga. Lagrings konto nyckeln ska lagras i en konfiguration för en skyddad inställning. Skyddade inställnings data för Azure VM-tillägg krypteras och dekrypteras bara på den virtuella mål datorn. Observera att **storageAccountName** och **storageAccountKey** är Skift läges känsliga. Andra nödvändiga parametrar visas i följande avsnitt.

```JSON
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "storageAccountName": "[parameters('storageAccountName')]",
          "performanceScenario": "[parameters('performanceScenario')]",
          "traceDurationInSeconds": "[parameter('traceDurationInSeconds')]",
          "perfCounterTrace": "[parameters('perfCounterTrace')]",
          "networkTrace": "[parameters('networkTrace')]",
          "xperfTrace": "[parameters('xperfTrace')]",
          "storPortTrace": "[parameters('storPortTrace')]",
          "srNumber": "[parameters('srNumber')]",
          "requestTimeUtc":  "[parameters('requestTimeUtc')]",
          "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"        
        }
      }
    }
```

### <a name="property-values"></a>Egenskaps värden

| Namn | Värde/exempel | Beskrivning |
|--|--|--|
| apiVersion | 2015-06-15 | API-versionen. |
| utgivare | Microsoft. Azure. Performance. Diagnostics | Utgivarens namn område för tillägget. |
| typ | AzurePerformanceDiagnostics | Typ av VM-tillägg. |
| typeHandlerVersion | 1,0 | Tilläggs hanterarens version. |
| performanceScenario | frö | Det prestanda scenario som data ska samlas in för. Giltiga värden är: **Basic**, **vmslow**, **migreringsåtgärden** och **Custom**. |
| traceDurationInSeconds | 300 | Spårens varaktighet, om något av spårnings alternativen är markerat. |
| perfCounterTrace | P | Alternativ för att aktivera spårning av prestanda räknare. Giltiga värden är **p** eller ett tomt värde. Lämna värdet tomt om du inte vill avbilda den här spårningen. |
| networkTrace | n | Alternativ för att aktivera nätverks spårning. Giltiga värden är **n** eller tomma värden. Lämna värdet tomt om du inte vill avbilda den här spårningen. |
| xperfTrace | x | Alternativ för att aktivera XPerf trace. Giltiga värden är **x** eller tomma värden. Lämna värdet tomt om du inte vill avbilda den här spårningen. |
| storPortTrace | s | Alternativ för att aktivera StorPort trace. Giltiga värden är **s** eller tomma. Lämna värdet tomt om du inte vill avbilda den här spårningen. |
| srNumber | 123452016365929 | Support ärende numret, om det är tillgängligt. Lämna värdet tomt om du inte har det. |
| requestTimeUtc | 2017-09-28T22:08:53.736 Z | Aktuell datum tid i UTC. Om du använder portalen för att installera det här tillägget behöver du inte ange det här värdet. |
| resourceId | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName} | Den unika identifieraren för en virtuell dator. |
| storageAccountName | mystorageaccount | Namnet på det lagrings konto där du vill lagra diagnostikloggar och resultat. |
| storageAccountKey | lDuVvxuZB28NNP... hAiRF3voADxLBTcc = = | Nyckeln för lagrings kontot. |

## <a name="install-the-extension"></a>Installera tillägget

Följ de här anvisningarna för att installera tillägget på virtuella Windows-datorer:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj den virtuella dator där du vill installera tillägget.

    ![Skärm bild av Azure Portal med virtuella datorer markerade](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Välj bladet **tillägg** och välj **Lägg till**.

    ![Skärm bild av bladet tillägg med Lägg till markerat](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Välj **Azure-prestandadiagnostik**, granska de allmänna villkoren och välj **skapa**.

    ![Skärm bild av den nya resurs skärmen med Azures prestandadiagnostik markerat](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Ange parameter värden för installationen och välj **OK** för att installera tillägget. Mer information om vilka scenarier som stöds finns i [så här använder du PerfInsights](how-to-use-perfinsights.md#supported-troubleshooting-scenarios). 

    ![Skärm bild av dialog rutan installera tillägg](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. När installationen är klar visas ett meddelande som anger den här statusen.

    ![Skärm bild av meddelande om slutförd etablering](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > Tillägget körs när etableringen har slutförts. Det tar två minuter eller mindre att slutföra för det grundläggande scenariot. För andra scenarier körs den med den varaktighet som anges under installationen.

## <a name="remove-the-extension"></a>Ta bort tillägget

Följ dessa steg om du vill ta bort tillägget från en virtuell dator:

1. Logga in på [Azure Portal](https://portal.azure.com), Välj den virtuella dator som du vill ta bort tillägget från och välj sedan bladet **tillägg** . 
2. Välj (**...**) för tillägget för prestandadiagnostik i listan och välj **Avinstallera**.

    ![Skärm bild av bladet tillägg, med avinstallation markerat](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Du kan också välja tilläggs posten och välja alternativet **Avinstallera** .

## <a name="template-deployment"></a>Malldistribution

Tillägg för virtuella Azure-datorer kan distribueras med Azure Resource Manager mallar. Det JSON-schema som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall. Detta kör VM-tillägget för Azure Performance Diagnostics under en Azure Resource Manager mall-distribution. Här är en exempel mal len:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "yourVMName"
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus"
    },
    "storageAccountName": {
      "type": "securestring",
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring",
      "defaultValue": "yourStorageAccountKey"
    },
    "performanceScenario": {
      "type": "string",
      "defaultValue": "basic"
    },
    "srNumber": {
      "type": "string",
      "defaultValue": ""
    },
  "traceDurationInSeconds": {
    "type": "int",
    "defaultValue": 300
  },
    "perfCounterTrace": {
      "type": "string",
      "defaultValue": "p"
    },
    "networkTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "xperfTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "storPortTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "requestTimeUtc": {
      "type": "string",
      "defaultValue": "10/2/2017 11:06:00 PM"
    }       
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "storageAccountName": "[parameters('storageAccountName')]",
          "performanceScenario": "[parameters('performanceScenario')]",
          "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
          "perfCounterTrace": "[parameters('perfCounterTrace')]",
          "networkTrace": "[parameters('networkTrace')]",
          "xperfTrace": "[parameters('xperfTrace')]",
          "storPortTrace": "[parameters('storPortTrace')]",
          "srNumber": "[parameters('srNumber')]",
          "requestTimeUtc":  "[parameters('requestTimeUtc')]",
          "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
  ]
}
```

## <a name="powershell-deployment"></a>PowerShell-distribution

`Set-AzVMExtension`Kommandot kan användas för att distribuera Azure Performance Diagnostics VM-tillägg till en befintlig virtuell dator.

PowerShell

```
$PublicSettings = @{ "storageAccountName"="mystorageaccount";"performanceScenario"="basic";"traceDurationInSeconds"=300;"perfCounterTrace"="p";"networkTrace"="";"xperfTrace"="";"storPortTrace"="";"srNumber"="";"requestTimeUtc"="2017-09-28T22:08:53.736Z";"resourceId"="VMResourceId" }
$ProtectedSettings = @{"storageAccountKey"="mystoragekey" }

Set-AzVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS
```

## <a name="information-on-the-data-captured"></a>Information om de data som samlas in
PerfInsights-verktyget samlar in olika loggar, konfigurations-och diagnostikdata, beroende på det valda scenariot. Mer information finns i PerfInsights- [dokumentationen](./how-to-use-perfinsights.md).

## <a name="view-and-share-the-results"></a>Visa och dela resultaten

Utdata från tillägget finns i en zip-fil som överförts till det lagrings konto som angavs under installationen och delas i 30 dagar med hjälp av [signaturer för delad åtkomst (SAS)](../../storage/common/storage-sas-overview.md). Den här zip-filen innehåller diagnostikloggar och en rapport med undersöknings resultat och rekommendationer. En SAS-länk till zip-filen för utdata finns i en textfil med namnet *zipfilename* _saslink.txt under mappen **C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics \\ \<version>**. Alla som har den här länken kan hämta zip-filen.

För att hjälpa Support teknikern att arbeta med ditt support ärende kan Microsoft använda SAS-länken för att hämta diagnostikdata.

Om du vill visa rapporten extraherar du zip-filen och öppnar filen **PerfInsights Report.html** .

Du bör också kunna hämta zip-filen direkt från portalen genom att välja tillägget.

![Skärm bild av detaljerad status för prestanda diagnostik](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> SAS-länken som visas i portalen kanske inte fungerar ibland. Detta kan bero på en felaktig URL vid kodning och avkodning av åtgärder. I stället kan du Hämta länken direkt från * _saslink.txt-filen från den virtuella datorn.

## <a name="troubleshoot-and-support"></a>Felsöka och support

- Distributions status för tillägg (i meddelande fältet) kan Visa "distribution pågår", även om tillägget har kon figurer ATS.

    Det här problemet kan ignoreras så länge tilläggets status visar att tillägget har allokerats.
- Du kan åtgärda problem under installationen med hjälp av tilläggs loggarna. Utökning av utdata loggas till filer som finns i följande katalog:

    `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\<version>`

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/)och välj **få support**. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).