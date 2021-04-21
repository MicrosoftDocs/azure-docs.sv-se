---
title: Installera och konfigurera Windows Azure Diagnostics-tillägget (WAD)
description: Lär dig mer om att installera och konfigurera Tillägget Windows Diagnostics. Lär dig också hur en beskrivning av hur data lagras i och Azure Storage konto.
services: azure-monitor
author: bwren
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 3ff752b673c49047551c48c4c8693b00d7b5edeb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787413"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Installera och konfigurera Windows Azure Diagnostics-tillägget (WAD)
[Azure Diagnostics-tillägget](diagnostics-extension-overview.md) är en agent Azure Monitor som samlar in övervakningsdata från gästoperativsystemet och arbetsbelastningar för virtuella Azure-datorer och andra beräkningsresurser. Den här artikeln innehåller information om hur du installerar och konfigurerar tillägget Windows Diagnostics och en beskrivning av hur data lagras i och Azure Storage konto.

Diagnostiktillägget implementeras som [ett](../../virtual-machines/extensions/overview.md) tillägg för virtuella datorer i Azure, så det stöder samma installationsalternativ med hjälp av Resource Manager-mallar, PowerShell och CLI. Mer [information om hur du installerar och underhåller tillägg för](../../virtual-machines/extensions/features-windows.md) virtuella datorer finns i Tillägg och funktioner för virtuella datorer för Windows.

## <a name="overview"></a>Översikt
När du konfigurerar diagnostiktillägget för Windows Azure måste du ange ett lagringskonto där alla angivna data ska skickas. Du kan också lägga till en eller flera *data mottagare för* att skicka data till olika platser.

- Azure Monitor mottagare – Skicka gästprestandadata till Azure Monitor Mått.
- Händelsehubbens mottagare – Skicka gästprestanda och loggdata till Azure-händelsehubbbar som ska vidarebefordras utanför Azure. Den här mottagaren kan inte konfigureras i Azure Portal.


## <a name="install-with-azure-portal"></a>Installera med Azure Portal
Du kan installera och konfigurera diagnostiktillägget på en enskild virtuell dator i Azure Portal vilket ger dig ett gränssnitt i stället för att arbeta direkt med konfigurationen. När du aktiverar diagnostiktillägget används automatiskt en standardkonfiguration med de vanligaste prestandaräknarna och händelserna. Du kan ändra standardkonfigurationen enligt dina specifika krav.

> [!NOTE]
> Nedan beskrivs de vanligaste inställningarna för diagnostiktillägget. Mer information om alla konfigurationsalternativ finns i [Schema för Windows-diagnostiktillägg.](diagnostics-extension-schema-windows.md)

1. Öppna menyn för en virtuell dator i Azure Portal.

2. Klicka på **Diagnostikinställningar** i avsnittet **Övervakning** på VM-menyn.

3. Klicka **på Aktivera övervakning på gästnivå** om diagnostiktillägget inte redan har aktiverats.

   ![Aktivera övervakning](media/diagnostics-extension-windows-install/enable-monitoring.png)

4. Ett nytt Azure Storage-konto skapas för den virtuella datorn med namnet baserat på namnet på resursgruppen för den virtuella datorn och en standarduppsättning med gästprestandaräknare och loggar väljs.

   ![Diagnostikinställningar](media/diagnostics-extension-windows-install/diagnostic-settings.png)

5. På fliken **Prestandaräknare** väljer du de gästmått som du vill samla in från den här virtuella datorn. Använd den **anpassade** inställningen för mer avancerade val.

   ![Prestandaräknare](media/diagnostics-extension-windows-install/performance-counters.png)

6. På fliken **Loggar** väljer du de loggar som ska samlas in från den virtuella datorn. Loggar kan skickas till lagring eller händelsehubbbar, men inte till Azure Monitor. Använd [Log Analytics-agenten för](../agents/log-analytics-agent.md) att samla in gästloggar för att Azure Monitor.

   ![Skärmbild som visar fliken Loggar med olika loggar valda för en virtuell dator.](media/diagnostics-extension-windows-install/logs.png)

7. På fliken **Kraschdumpar** anger du eventuella processer för att samla in minnesdumpar efter en krasch. Data skrivs till lagringskontot för diagnostikinställningen och du kan välja att ange en blobcontainer.

   ![Kraschdumpar](media/diagnostics-extension-windows-install/crash-dumps.png)

8. På fliken **Mottagare anger** du om du vill skicka data till andra platser än Azure Storage. Om du **Azure Monitor** skickas gästprestandadata till Azure Monitor Mått. Du kan inte konfigurera händelsehubbens mottagare med hjälp av Azure Portal.

   ![Skärmbild som visar fliken Mottagare med alternativet Skicka diagnostikdata till Azure Monitor aktiverat.](media/diagnostics-extension-windows-install/sinks.png)
   
   Om du inte har aktiverat en system tilldelad identitet som konfigurerats för den virtuella datorn kan du se varningen nedan när du sparar en konfiguration med Azure Monitor mottagare. Klicka på banderollen för att aktivera den system tilldelade identiteten.
   
   ![Hanterad entitet](media/diagnostics-extension-windows-install/managed-entity.png)

9. I **Agent kan** du ändra lagringskontot, ange diskkvoten och ange om du vill samla in loggar för diagnostikinfrastruktur.  

   ![Skärmbild som visar fliken Agent med alternativet att ange lagringskontot.](media/diagnostics-extension-windows-install/agent.png)

10. Spara **konfigurationen genom** att klicka på Spara. 

> [!NOTE]
> Konfigurationen för diagnostiktillägget kan formateras antingen i JSON eller XML, men alla konfigurationer som görs i Azure Portal lagras alltid som JSON. Om du använder XML med en annan konfigurationsmetod och sedan ändrar konfigurationen med Azure Portal ändras inställningarna till JSON.

## <a name="resource-manager-template"></a>Resource Manager-mall
Se [Använda övervakning och diagnostik med en virtuell Windows-dator och Azure Resource Manager för](../../virtual-machines/extensions/diagnostics-template.md) att distribuera diagnostiktillägget med Azure Resource Manager mallar. 

## <a name="azure-cli-deployment"></a>Azure CLI-distribution
Azure CLI kan användas för att distribuera Azure Diagnostics till en befintlig virtuell dator med [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) som i följande exempel. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

De skyddade inställningarna definieras i [PrivateConfig-elementet](diagnostics-extension-schema-windows.md#privateconfig-element) i konfigurationsschemat. Följande är ett minimalt exempel på en skyddad inställningsfil som definierar lagringskontot. Se [Exempelkonfiguration](diagnostics-extension-schema-windows.md#privateconfig-element) för fullständig information om de privata inställningarna.

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```

De offentliga inställningarna definieras i det [offentliga elementet](diagnostics-extension-schema-windows.md#publicconfig-element) i konfigurationsschemat. Följande är ett minimalt exempel på en fil för offentliga inställningar som möjliggör insamling av loggar för diagnostikinfrastruktur, en enskild prestandaräknare och en enda händelselogg. Se [Exempelkonfiguration](diagnostics-extension-schema-windows.md#publicconfig-element) för fullständig information om de offentliga inställningarna.

```JSON
{
  "StorageAccount": "mystorageaccount",
  "WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": 5120,
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
          {
            "counterSpecifier": "\\Processor Information(_Total)\\% Processor Time",
            "unit": "Percent",
            "sampleRate": "PT60S"
          }
        ]
      },
      "WindowsEventLog": {
        "scheduledTransferPeriod": "PT1M",
        "DataSource": [
          {
            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
          }
        ]
      }
    }
  }
}
```



## <a name="powershell-deployment"></a>PowerShell-distribution
PowerShell kan användas för att distribuera Azure Diagnostics-tillägget till en befintlig virtuell dator med hjälp av [Set-AzVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) som i följande exempel. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

De privata inställningarna definieras i [PrivateConfig-elementet](diagnostics-extension-schema-windows.md#privateconfig-element), medan de offentliga inställningarna definieras i [det offentliga elementet](diagnostics-extension-schema-windows.md#publicconfig-element) i konfigurationsschemat. Du kan också välja att ange information om lagringskontot som parametrar för Set-AzVMDiagnosticsExtension-cmdleten i stället för att inkludera dem i de privata inställningarna.

Följande är ett minimalt exempel på en konfigurationsfil som möjliggör insamling av loggar för diagnostikinfrastruktur, en enskild prestandaräknare och en enda händelselogg. Se [Exempelkonfiguration](diagnostics-extension-schema-windows.md#publicconfig-element) för fullständig information om de privata och offentliga inställningarna. 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000,
                "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
                },
                "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "PerformanceCounterConfiguration": [
                        {
                            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                            "sampleRate": "PT3M",
                            "unit": "percent"
                        }
                    ]
                },
                "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                        "DataSource": [
                        {
                            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                        }
                    ]
                }
            }
        },
        "StorageAccount": "mystorageaccount",
        "StorageType": "TableAndBlob"
    },
    "PrivateConfig": {
        "storageAccountName": "mystorageaccount",
        "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
    }
}
```

Se även [Använda PowerShell för att aktivera Azure Diagnostics på en virtuell dator som kör Windows](../../virtual-machines/extensions/diagnostics-windows.md).

## <a name="data-storage"></a>Datalagring
I följande tabell visas de olika typerna av data som samlas in från diagnostiktillägget och om de lagras som en tabell eller en blob. Data som lagras i tabeller kan också lagras i blobar beroende på [inställningen StorageType i](diagnostics-extension-schema-windows.md#publicconfig-element) din offentliga konfiguration.


| Data | Lagringstyp | Description |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Tabell | Diagnostikövervakare och konfigurationsändringar. |
| WADDirectoriesTable | Tabell | Kataloger som diagnostikövervakaren övervakar.  Detta inkluderar IIS-loggar, misslyckade IIS-begärandeloggar och anpassade kataloger.  Platsen för blobloggfilen anges i fältet Container och namnet på bloben finns i fältet RelativePath.  Fältet AbsolutePath anger platsen och namnet på filen så som den fanns på den virtuella Azure-datorn. |
| WadLogsTable | Tabell | Loggar som skrivits i kod med hjälp av spårningslyssnaren. |
| WADPerformanceCountersTable | Tabell | Prestandaräknare. |
| WADWindowsEventLogsTable | Tabell | Windows-händelseloggar. |
| wad-iis-failedreqlogfiles | Blob | Innehåller information från IIS-loggar med misslyckade förfrågningar. |
| wad-iis-logfiles | Blob | Innehåller information om IIS-loggar. |
| "anpassad" | Blob | En anpassad container som baseras på konfiguration av kataloger som övervakas av diagnostikövervakaren.  Namnet på den här blobcontainern anges i WADDirectoriesTable. |

## <a name="tools-to-view-diagnostic-data"></a>Verktyg för att visa diagnostikdata
Det finns flera verktyg för att visa data när de har överförts till lagringen. Exempel:

* Server Explorer i Visual Studio – Om du har installerat Azure Tools for Microsoft Visual Studio kan du använda Azure Storage-noden i Server Explorer för att visa skrivskyddade blob- och tabelldata från dina Azure Storage-konton. Du kan visa data från ditt lokala lagringsemulatorkonto och även från lagringskonton som du har skapat för Azure. Mer information finns i Bläddra [bland och hantera lagringsresurser med Server Explorer](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) är en fristående app som gör att du enkelt kan arbeta med Azure Storage data i Windows, OSX och Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) innehåller Azure Diagnostics Manager som gör att du kan visa, ladda ned och hantera diagnostikdata som samlas in av de program som körs i Azure.

## <a name="next-steps"></a>Nästa steg
- Mer [information om hur du vidarebefordrar övervakningsdata till Event Hubs](diagnostics-extension-stream-event-hubs.md) finns i Skicka data från Windows Azure Diagnostics-Azure Event Hubs.
