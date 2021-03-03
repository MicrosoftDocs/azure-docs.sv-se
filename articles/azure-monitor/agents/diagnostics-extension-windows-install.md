---
title: Installera och konfigurera Windows Azure Diagnostics-tillägget (WAD)
description: Lär dig mer om att installera och konfigurera Windows Diagnostics-tillägget. Lär dig också hur en beskrivning av hur data lagras i och Azure Storage konto.
services: azure-monitor
author: bwren
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 174f372f9dbe8dc0449c7f9b9f5b34c6206f92de
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708568"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Installera och konfigurera Windows Azure Diagnostics-tillägget (WAD)
[Tillägget Azure Diagnostics](diagnostics-extension-overview.md) är en agent i Azure Monitor som samlar in övervaknings data från gäst operativ systemet och arbets belastningar på virtuella Azure-datorer och andra beräknings resurser. Den här artikeln innehåller information om hur du installerar och konfigurerar Windows Diagnostics-tillägget och en beskrivning av hur data lagras i och Azure Storage konto.

Tillägget för diagnostik implementeras som ett [tillägg för virtuella datorer](../../virtual-machines/extensions/overview.md) i Azure, så det stöder samma installations alternativ med Resource Manager-mallar, POWERSHELL och CLI. Se [tillägg och funktioner för virtuella datorer för Windows](../../virtual-machines/extensions/features-windows.md) om du vill ha mer information om hur du installerar och hanterar tillägg för virtuella datorer.

## <a name="overview"></a>Översikt
När du konfigurerar Windows Azure Diagnostics-tillägget måste du ange ett lagrings konto där alla angivna data ska skickas. Du kan också lägga till en eller flera *data mottagare* för att skicka data till olika platser.

- Azure Monitor mottagare – skicka gäst prestanda data till Azure Monitor mått.
- Event Hub-mottagare – skicka gäst prestanda och logga data till Azure Event Hub för att vidarebefordra utanför Azure. Det går inte att konfigurera denna mottagare i Azure Portal.


## <a name="install-with-azure-portal"></a>Installera med Azure Portal
Du kan installera och Konfigurera diagnostik-tillägget på en enskild virtuell dator i Azure Portal, vilket ger dig ett gränssnitt i stället för att arbeta direkt med konfigurationen. När du aktiverar Diagnostics-tillägget använder det automatiskt en standard konfiguration med de vanligaste prestanda räknarna och-händelserna. Du kan ändra den här standard konfigurationen utifrån dina egna krav.

> [!NOTE]
> Följande beskriver de vanligaste inställningarna för tillägget Diagnostics. Mer information om alla konfigurations alternativ finns i [Windows Diagnostics-tilläggsprogram schema](diagnostics-extension-schema-windows.md).

1. Öppna menyn för en virtuell dator i Azure Portal.

2. Klicka på **diagnostikinställningar** i avsnittet **övervakning** på menyn VM.

3. Klicka på **Aktivera övervakning på gästnivå** om inget diagnostik-tillägg redan har Aktiver ATS.

   ![Aktivera övervakning](media/diagnostics-extension-windows-install/enable-monitoring.png)

4. Ett nytt Azure Storage-konto skapas för den virtuella datorn med namnet baserat på namnet på resurs gruppen för den virtuella datorn och en standard uppsättning med gäst prestanda räknare och loggar väljs.

   ![Diagnostikinställningar](media/diagnostics-extension-windows-install/diagnostic-settings.png)

5. På fliken **prestanda räknare** väljer du de gäst mått som du vill samla in från den här virtuella datorn. Använd den **anpassade** inställningen för mer avancerade val.

   ![Prestandaräknare](media/diagnostics-extension-windows-install/performance-counters.png)

6. På fliken **loggar** väljer du de loggar som ska samlas in från den virtuella datorn. Loggar kan skickas till lagrings-eller händelse nav, men inte Azure Monitor. Använd [Log Analytics-agenten](../agents/log-analytics-agent.md) för att samla in gäst loggar till Azure Monitor.

   ![Skärm bild som visar fliken loggar med olika loggar som har valts för en virtuell dator.](media/diagnostics-extension-windows-install/logs.png)

7. På fliken **krasch dum par** anger du de processer som ska samla in minnes dum par efter en krasch. Data skrivs till lagrings kontot för den diagnostiska inställningen och du kan också ange en BLOB-behållare.

   ![Kraschdumpar](media/diagnostics-extension-windows-install/crash-dumps.png)

8. På fliken **mottagare** anger du om du vill skicka data till andra platser än Azure Storage. Om du väljer **Azure Monitor** skickas gäst prestanda data till Azure Monitor mått. Du kan inte konfigurera händelse Hubbs mottagaren med hjälp av Azure Portal.

   ![Skärm bild som visar fliken mottagare med alternativet Skicka diagnostikdata till Azure Monitor aktiverat.](media/diagnostics-extension-windows-install/sinks.png)
   
   Om du inte har aktiverat en tilldelad identitet som har kon figurer ATS för den virtuella datorn kan du se varningen nedan när du sparar en konfiguration med Azure Monitor mottagare. Klicka på banderollen för att aktivera identiteten som tilldelats av systemet.
   
   ![Hanterad entitet](media/diagnostics-extension-windows-install/managed-entity.png)

9. I **agenten** kan du ändra lagrings kontot, ange disk kvoten och ange om du vill samla in diagnostiska infrastruktur loggar.  

   ![Skärm bild som visar fliken agent med alternativet att ange lagrings kontot.](media/diagnostics-extension-windows-install/agent.png)

10. Klicka på **Spara** för att spara konfigurationen. 

> [!NOTE]
> Även om konfigurationen för Diagnostics-tillägget kan formateras i JSON eller XML kommer alla konfigurationer som görs i Azure Portal alltid att lagras som JSON. Om du använder XML med en annan konfigurations metod och sedan ändrar konfigurationen med Azure Portal, ändras inställningarna till JSON.

## <a name="resource-manager-template"></a>Resource Manager-mall
Se [använda övervakning och diagnostik med en virtuell Windows-dator och Azure Resource Manager mallar](../../virtual-machines/extensions/diagnostics-template.md) för distribution av diagnostikprogrammet med Azure Resource Manager mallar. 

## <a name="azure-cli-deployment"></a>Azure CLI-distribution
Azure CLI kan användas för att distribuera Azure-diagnostik-tillägget till en befintlig virtuell dator som använder [AZ VM-tillägg](/cli/azure/vm/extension#az-vm-extension-set) som i följande exempel. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

De skyddade inställningarna definieras i PrivateConfig- [elementet](diagnostics-extension-schema-windows.md#privateconfig-element) för konfigurations schemat. Följande är ett minimalt exempel på en skyddad inställnings fil som definierar lagrings kontot. Se [exempel på konfiguration](diagnostics-extension-schema-windows.md#privateconfig-element) för fullständig information om de privata inställningarna.

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```

De offentliga inställningarna definieras i det [offentliga elementet](diagnostics-extension-schema-windows.md#publicconfig-element) i konfigurations schemat. Följande är ett minimalt exempel på en fil med en offentlig inställning som möjliggör insamling av diagnostiska infrastruktur loggar, en enda prestanda räknare och en enda händelse logg. Se [exempel på konfiguration](diagnostics-extension-schema-windows.md#publicconfig-element) för fullständig information om de offentliga inställningarna.

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
PowerShell kan användas för att distribuera Azure-diagnostik-tillägget till en befintlig virtuell dator med [set-AzVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) som i följande exempel. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

De privata inställningarna definieras i PrivateConfig- [elementet](diagnostics-extension-schema-windows.md#privateconfig-element), medan de offentliga inställningarna definieras i det [offentliga elementet](diagnostics-extension-schema-windows.md#publicconfig-element) i konfigurations schemat. Du kan också välja att ange information om lagrings kontot som parametrar i Set-AzVMDiagnosticsExtension cmdlet i stället för att inkludera dem i de privata inställningarna.

Följande är ett minimalt exempel på en konfigurations fil som möjliggör insamling av diagnostiska infrastruktur loggar, en enda prestanda räknare och en enda händelse logg. Se [exempel på konfiguration](diagnostics-extension-schema-windows.md#publicconfig-element) för fullständig information om de privata och offentliga inställningarna. 

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

Se även [använda PowerShell för att aktivera Azure-diagnostik på en virtuell dator som kör Windows](../../virtual-machines/extensions/diagnostics-windows.md).

## <a name="data-storage"></a>Datalagring
I följande tabell visas olika typer av data som har samlats in från diagnostikprogrammet och om de lagras som en tabell eller en blob. Data som lagras i tabeller kan också lagras i blobbar beroende på [inställningen StorageType](diagnostics-extension-schema-windows.md#publicconfig-element) i din offentliga konfiguration.


| Data | Lagringstyp | Beskrivning |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Tabell | Diagnostisk övervakning och konfigurations ändringar. |
| WADDirectoriesTable | Tabell | Kataloger som diagnostiken övervakar.  Detta inkluderar IIS-loggar, IIS misslyckade begär Anden och anpassade kataloger.  Platsen för blobb logg filen anges i fältet container och namnet på blobben är i fältet RelativePath.  Fältet AbsolutePath anger platsen och namnet på filen som den fanns på den virtuella Azure-datorn. |
| WadLogsTable | Tabell | Loggar som skrivits i kod med spårnings lyssnaren. |
| WADPerformanceCountersTable | Tabell | Prestanda räknare. |
| WADWindowsEventLogsTable | Tabell | Händelse loggar i Windows. |
| wad-IIS-failedreqlogfiles | Blob | Innehåller information från IIS-begäranden om misslyckade förfrågningar. |
| wad-IIS-loggfiler | Blob | Innehåller information om IIS-loggar. |
| bild | Blob | En anpassad behållare baserat på hur du konfigurerar kataloger som övervakas av diagnostisk övervakare.  Namnet på den här BLOB-behållaren anges i WADDirectoriesTable. |

## <a name="tools-to-view-diagnostic-data"></a>Verktyg för att Visa diagnostikdata
Det finns flera verktyg för att visa data när de har överförts till lagring. Exempel:

* Server Explorer i Visual Studio – om du har installerat Azure-verktygen för Microsoft Visual Studio kan du använda noden Azure Storage i Server Explorer för att visa skrivskyddade blob-och tabell data från dina Azure Storage-konton. Du kan visa data från ditt lokala Storage emulator-konto och även från lagrings konton som du har skapat för Azure. Mer information finns i avsnittet [om att bläddra och hantera lagrings resurser med Server Explorer](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) är en fristående app som gör det enkelt att arbeta med Azure Storage data på Windows, OSX och Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) innehåller Azure-diagnostik Manager som gör att du kan visa, ladda ned och hantera de diagnostikdata som samlas in av programmen som körs på Azure.

## <a name="next-steps"></a>Nästa steg
- Se [skicka data från Windows Azure Diagnostics-tillägget till Event Hubs](diagnostics-extension-stream-event-hubs.md) för information om vidarebefordran av övervaknings data till Azure Event Hubs.
