---
title: Händelse agg regering med Windows Azure-diagnostik
description: Lär dig mer om agg regering och insamling av händelser med WAD för övervakning och diagnostik av Azure Service Fabric-kluster.
ms.topic: conceptual
ms.date: 04/03/2018
ms.openlocfilehash: bbc8efcb2600e1832ad8a37560ab231a4a7f3185
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626737"
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Händelse agg regering och insamling med Windows Azure-diagnostik
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

När du kör ett Azure Service Fabric-kluster, är det en bra idé att samla in loggarna från alla noder på en central plats. Genom att logga in på en central plats kan du analysera och felsöka problem i klustret, eller problem i de program och tjänster som körs i klustret.

Ett sätt att ladda upp och samla in loggar är att använda Windows Azure-diagnostik-tillägget (WAD), som överför loggar till Azure Storage och även har möjlighet att skicka loggar till Azure Application insikter eller Event Hubs. Du kan också använda en extern process för att läsa händelserna från lagringen och placera dem i en analys plattforms produkt, till exempel [Azure Monitor loggar](./service-fabric-diagnostics-oms-setup.md) eller en annan logg tolknings lösning.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar
Följande verktyg används i den här artikeln:

* [Azure Resource Manager](../azure-resource-manager/management/overview.md)
* [Azure PowerShell](/powershell/azure/)
* [Azure Resource Manager-mall](../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json)

## <a name="service-fabric-platform-events"></a>Service Fabric plattforms händelser
Service Fabric anger att du har några färdiga [loggnings kanaler](service-fabric-diagnostics-event-generation-infra.md), varav följande kanaler är förkonfigurerade med tillägget för att skicka övervaknings-och diagnostikdata till en lagrings tabell eller någon annan stans:
  * [Drift händelser](service-fabric-diagnostics-event-generation-operational.md): åtgärder på högre nivå som Service Fabric-plattformen utför. Exempel på detta är att skapa program och tjänster, ändringar i Node-tillstånd och uppgraderings information. Dessa genereras som ETW (Event Tracing for Windows) (ETW) loggar
  * [Reliable Actors programmerings modell händelser](service-fabric-reliable-actors-diagnostics.md)
  * [Reliable Services programmerings modell händelser](service-fabric-reliable-services-diagnostics.md)

## <a name="deploy-the-diagnostics-extension-through-the-portal"></a>Distribuera tillägget för diagnostik via portalen
Det första steget i att samla in loggar är att distribuera Diagnostics-tillägget på noderna för skalnings uppsättning för virtuella datorer i Service Fabric klustret. Diagnostics-tillägget samlar in loggar på varje virtuell dator och laddar upp dem till det lagrings konto som du anger. Följande steg beskriver hur du kan göra detta för nya och befintliga kluster genom Azure Portal och Azure Resource Manager mallar.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Distribuera Diagnostics-tillägget som en del av klustrets skapande genom Azure Portal
När du skapar klustret går du till steget kluster konfiguration och expanderar valfria inställningar och kontrollerar att diagnostik är inställt **på på** (standardinställningen).

![Azure-diagnostik inställningar i portalen för att skapa kluster](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics-new.png)

Vi rekommenderar starkt att du hämtar mallen **innan du klickar på Skapa** i det sista steget. Mer information finns i [Konfigurera ett Service Fabric kluster med hjälp av en Azure Resource Manager mall](service-fabric-cluster-creation-via-arm.md). Du behöver mallen för att göra ändringar på vilka kanaler (som anges ovan) för att samla in data från.

![Kluster mal len](media/service-fabric-diagnostics-event-aggregation-wad/download-cluster-template.png)

Nu när du sammanställer händelser i Azure Storage [konfigurerar du Azure Monitors loggar](service-fabric-diagnostics-oms-setup.md) för att få insikter och fråga dem i Azure Monitor loggar Portal

>[!NOTE]
>Det finns för närvarande inget sätt att filtrera eller rensa händelser som skickas till tabellerna. Om du inte implementerar en process för att ta bort händelser från tabellen fortsätter tabellen att växa (standard begränsningen är 50 GB). Anvisningar om hur du ändrar det här finns [i den här artikeln](service-fabric-diagnostics-event-aggregation-wad.md#update-storage-quota). Det finns dessutom ett exempel på en data rensnings tjänst som körs i [övervaknings exemplet](https://github.com/Azure-Samples/service-fabric-watchdog-service), och vi rekommenderar att du skriver en åt dig själv, såvida det inte finns ett bra skäl för att lagra loggar över en 30-eller 90-dagars tidsram.



## <a name="deploy-the-diagnostics-extension-through-azure-resource-manager"></a>Distribuera tillägget för diagnostik via Azure Resource Manager

### <a name="create-a-cluster-with-the-diagnostics-extension"></a>Skapa ett kluster med tillägget Diagnostics
Om du vill skapa ett kluster med hjälp av Resource Manager måste du lägga till JSON-konfigurationsfilen i fullständig Resource Manager-mall. Vi tillhandahåller ett exempel på en Resource Manager-mall med fem virtuella datorer som har lagts till som en del av vårt exempel i Resource Manager-mallar. Du kan se den på den här platsen i Azure samples-galleriet: [fem noder kluster med mall för diagnostisk Resource Manager-mall](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

Om du vill se diagnostik-inställningen i Resource Manager-mallen öppnar du azuredeploy.jspå fil och söker efter **IaaSDiagnostics**. Om du vill skapa ett kluster med hjälp av den här mallen väljer du knappen **distribuera till Azure** som är tillgänglig på föregående länk.

Du kan också hämta Resource Manager-exemplet, göra ändringar i det och skapa ett kluster med den ändrade mallen genom att använda `New-AzResourceGroupDeployment` kommandot i ett Azure PowerShells fönster. Se följande kod för de parametrar som du skickar till kommandot. Detaljerad information om hur du distribuerar en resurs grupp med hjälp av PowerShell finns i artikeln [distribuera en resurs grupp med Azure Resource Manager-mallen](../azure-resource-manager/templates/deploy-powershell.md).

### <a name="add-the-diagnostics-extension-to-an-existing-cluster"></a>Lägg till ett diagnostik-tillägg i ett befintligt kluster
Om du har ett befintligt kluster som inte har någon diagnostik distribuerad kan du lägga till eller uppdatera det via kluster mal len. Ändra den Resource Manager-mall som används för att skapa det befintliga klustret eller ladda ned mallen från portalen enligt beskrivningen ovan. Ändra template.jspå filen genom att utföra följande uppgifter:

Lägg till en ny lagrings resurs till mallen genom att lägga till i avsnittet resurser.

```json
{
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
    "name": "[parameters('applicationDiagnosticsStorageAccountType')]"
    "tier": "standard"
  },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Lägg sedan till i avsnittet parametrar strax efter lagrings kontots definitioner, mellan `supportLogStorageAccountName` . Ersätt plats hållarens text *lagrings konto namn här* med namnet på det lagrings konto som du vill ha.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
Uppdatera sedan `VirtualMachineProfile` avsnittet i template.jsfilen genom att lägga till följande kod i matrisen tillägg. Se till att lägga till ett kommatecken i början eller slutet, beroende på var det infogas.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                },
                {
                    "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

När du har ändrat template.jspå filen enligt beskrivningen, publicerar du om Resource Manager-mallen. Om mallen har exporter ATS, publicerar deploy.ps1-filen mallen på samma gång. När du har distribuerat kontrollerar du att **ProvisioningState** har **slutförts**.

> [!TIP]
> Om du ska distribuera behållare till klustret, aktiverar du WAD för att hämta Docker-statistik genom att lägga till den i **WadCfg > DiagnosticMonitorConfiguration** -avsnittet.

```json
"DockerSources": {
    "Stats": {
        "enabled": true,
        "sampleRate": "PT1M"
    }
},
```

### <a name="update-storage-quota"></a>Uppdatera lagrings kvot

Eftersom de tabeller som är fyllda av tillägget växer tills kvoten har nåtts, kanske du vill överväga att minska kvot storleken. Standardvärdet är 50 GB och kan konfigureras i mallen under `overallQuotaInMB` fältet under `DiagnosticMonitorConfiguration`

```json
"overallQuotaInMB": "50000",
```

## <a name="log-collection-configurations"></a>Konfiguration av logg samling
Loggar från ytterligare kanaler är också tillgängliga för insamling, här följer några av de vanligaste konfigurationerna som du kan göra i mallen för kluster som körs i Azure.

* Drift kanal – bas: aktive ras som standard, hög nivå åtgärder som utförs av Service Fabric och klustret, inklusive händelser för en nod som kommer, ett nytt program som distribueras eller en uppgraderings återställning osv. En lista över händelser finns i [drift kanal händelser](./service-fabric-diagnostics-event-generation-operational.md).
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* Drift kanal – detaljerad: Detta omfattar hälso rapporter och belastnings Utjämnings beslut, plus allt i den grundläggande operativa kanalen. Dessa händelser genereras av antingen systemet eller din kod med hjälp av hälso tillstånds-eller inläsnings-API: er som [ReportPartitionHealth](/previous-versions/azure/reference/mt645153(v=azure.100)) eller [ReportLoad](/previous-versions/azure/reference/mt161491(v=azure.100)). Om du vill visa dessa händelser i Visual Studios diagnostiska Loggboken lägger du till "Microsoft-ServiceFabric: 4:0x4000000000000008" i listan över ETW-providers.

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* Data-och meddelande kanal – bas: kritiska loggar och händelser som genererats i meddelande tjänsten (för närvarande endast ReverseProxy) och data Sök väg, förutom detaljerade drift kanal loggar. Dessa händelser är bearbetnings fel och andra kritiska problem i ReverseProxy, samt begär Anden som bearbetas. **Detta är vår rekommendation för utförlig loggning**. Om du vill visa dessa händelser i Visual Studios diagnostiska Loggboken lägger du till "Microsoft-ServiceFabric: 4:0x4000000000000010" i listan över ETW-providers.

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* Data & meddelande kanal – detaljerat: utförlig kanal som innehåller alla icke-kritiska loggar från data och meddelanden i klustret och den detaljerade drift kanalen. Detaljerad fel sökning av alla omvänd proxy-händelser finns i [guiden för omvänd proxy-diagnostik](service-fabric-reverse-proxy-diagnostics.md).  Om du vill visa dessa händelser i Visual Studios diagnostisk logg bok lägger du till "Microsoft-ServiceFabric: 4:0x4000000000000020" i listan över ETW-providers.

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>Den här kanalen har en mycket stor mängd händelser, vilket gör att händelse insamling från den här detaljerade kanalen resulterar i en mängd spår som skapas snabbt och kan förbruka lagrings kapacitet. Aktivera bara detta om det är absolut nödvändigt.


Om du vill aktivera den **grundläggande operativa kanalen** för vår rekommendation för omfattande loggning med minsta möjliga mängd brus, `EtwManifestProviderConfiguration` skulle i- `WadCfg` mallen se ut så här:

```json
  "WadCfg": {
        "DiagnosticMonitorConfiguration": {
          "overallQuotaInMB": "50000",
          "EtwProviders": {
            "EtwEventSourceProviderConfiguration": [
              {
                "provider": "Microsoft-ServiceFabric-Actors",
                "scheduledTransferKeywordFilter": "1",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricReliableActorEventTable"
                }
              },
              {
                "provider": "Microsoft-ServiceFabric-Services",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricReliableServiceEventTable"
                }
              }
            ],
            "EtwManifestProviderConfiguration": [
              {
                "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                "scheduledTransferLogLevelFilter": "Information",
                "scheduledTransferKeywordFilter": "4611686018427387904",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricSystemEventTable"
                }
              },
              {
                "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
                "scheduledTransferLogLevelFilter": "Information",
                "scheduledTransferKeywordFilter": "4611686018427387904",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                "eventDestination": "ServiceFabricSystemEventTable"
                }
              }
            ]
          }
        }
      },
```

## <a name="collect-from-new-eventsource-channels"></a>Samla in från nya EventSource-kanaler

Om du vill uppdatera diagnostiken för att samla in loggar från nya EventSource-kanaler som representerar ett nytt program som du håller på att distribuera, utför du samma steg som tidigare beskrivits för installation av diagnostik för ett befintligt kluster.

Uppdatera `EtwEventSourceProviderConfiguration` avsnittet i template.jspå filen för att lägga till poster för de nya EventSource-kanalerna innan du installerar konfigurations uppdateringen med hjälp av `New-AzResourceGroupDeployment` PowerShell-kommandot. Namnet på händelse källan definieras som en del av koden i Visual Studio-genererad ServiceEventSource. CS-fil.

Om din händelse källa exempelvis kallas My-EventSource, lägger du till följande kod för att placera händelserna från My-Eventsource i en tabell med namnet MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Om du vill samla in prestanda räknare eller händelse loggar ändrar du Resource Manager-mallen med hjälp av de exempel som finns i [skapa en virtuell Windows-dator med övervakning och diagnostik med hjälp av en Azure Resource Manager mall](../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json). Publicera sedan om Resource Manager-mallen.

## <a name="collect-performance-counters"></a>Samla in prestanda räknare

Om du vill samla in prestanda mått från klustret lägger du till prestanda räknarna i "WadCfg > DiagnosticMonitorConfiguration" i Resource Manager-mallen för klustret. Se [prestanda övervakning med wad](service-fabric-diagnostics-perf-wad.md) för steg om hur `WadCfg` du ändrar för att samla in vissa prestanda räknare. Referens [Service Fabric prestanda räknare](service-fabric-diagnostics-event-generation-perf.md) för en lista över prestanda räknare som vi rekommenderar att samla in.
  
Om du använder en Application Insights mottagare, enligt beskrivningen i avsnittet nedan, och vill att dessa mått ska visas i Application Insights, måste du lägga till mottagar namnet i avsnittet "Sinks" enligt ovan. Detta skickar automatiskt de prestanda räknare som kon figurer ATS individuellt till din Application Insights-resurs.


## <a name="send-logs-to-application-insights"></a>Skicka loggar till Application Insights

### <a name="configuring-application-insights-with-wad"></a>Konfigurera Application Insights med WAD

>[!NOTE]
>Detta gäller endast för Windows-kluster för tillfället.

Det finns två huvudsakliga sätt att skicka data från WAD till Azure Application insikter som uppnås genom att lägga till en Application Insights mottagare i WAD-konfigurationen via Azure Portal eller via en Azure Resource Manager mall.

#### <a name="add-an-application-insights-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Lägg till en Application Insights Instrumentation-nyckel när du skapar ett kluster i Azure Portal

![Lägga till en AIKey](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

När du skapar ett kluster, om diagnostik är aktive rad, visas ett valfritt fält för att ange en Application Insights Instrumentation-nyckel. Om du klistrar in Application Insights nyckel här konfigureras Application Insights-mottagaren automatiskt åt dig i Resource Manager-mallen som används för att distribuera klustret.

#### <a name="add-the-application-insights-sink-to-the-resource-manager-template"></a>Lägg till Application Insights-Sink i Resource Manager-mallen

I "WadCfg" i Resource Manager-mallen lägger du till en "mottagare" genom att inkludera följande två ändringar:

1. Lägg till Sink-konfigurationen direkt efter att deklarationen av `DiagnosticMonitorConfiguration` har slutförts:

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. Inkludera sinken i `DiagnosticMonitorConfiguration` genom att lägga till följande rad i `DiagnosticMonitorConfiguration` `WadCfg` (höger innan de `EtwProviders` är deklarerade):

    ```json
    "sinks": "applicationInsights"
    ```

I båda föregående kodfragment användes namnet "applicationInsights" för att beskriva mottagaren. Detta är inte ett krav och så länge namnet på mottagaren ingår i "Sinks", kan du ange namnet på valfri sträng.

För närvarande visas loggar från klustret som **spårningar** i Application Insights logg visaren. Eftersom de flesta spårningar som kommer från plattformen är av nivå "information" kan du också överväga att ändra Sink-konfigurationen till att endast skicka loggar av typen "varning" eller "Error". Detta kan göras genom att lägga till "kanaler" i din mottagare, som visas i [den här artikeln](../azure-monitor/agents/diagnostics-extension-to-application-insights.md).

>[!NOTE]
>Om du använder en felaktig Application Insights nyckel antingen i portalen eller i din Resource Manager-mall måste du manuellt ändra nyckeln och uppdatera klustret/omdistribuera det.

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Azure Diagnostics korrekt visas data i dina lagrings tabeller från ETW-och EventSource-loggarna. Om du väljer att använda Azure Monitor loggar, Kibana eller någon annan plattform för data analys och visualisering som inte är direkt konfigurerad i Resource Manager-mallen, måste du konfigurera den plattform som du väljer för att läsa data från dessa lagrings tabeller. Detta för Azure Monitor loggar är relativt enkelt och förklaras i [händelse-och logg analys](service-fabric-diagnostics-event-analysis-oms.md). Application Insights är en del av ett specialfall i denna mening, eftersom det kan konfigureras som en del av konfigurationen för diagnostik, så du kan se [lämplig artikel](service-fabric-diagnostics-event-analysis-appinsights.md) om du väljer att använda AI.

>[!NOTE]
>Det finns för närvarande inget sätt att filtrera eller rensa händelser som skickas till tabellen. Om du inte implementerar en process för att ta bort händelser från tabellen fortsätter tabellen att växa. För närvarande finns det ett exempel på en data rensnings tjänst som körs i [övervaknings provet](https://github.com/Azure-Samples/service-fabric-watchdog-service), och vi rekommenderar att du skriver en åt dig själv, såvida det inte finns ett bra skäl för att lagra loggar över en 30-eller 90-dagars tidsram.

* [Lär dig hur du samlar in prestanda räknare eller loggar med hjälp av Diagnostics-tillägget](../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json)
* [Händelse analys och visualisering med Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Händelse analys och visualisering med Azure Monitor loggar](service-fabric-diagnostics-event-analysis-oms.md)
* [Händelse analys och visualisering med Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Händelse analys och visualisering med Azure Monitor loggar](service-fabric-diagnostics-event-analysis-oms.md)
