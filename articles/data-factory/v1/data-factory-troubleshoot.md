---
title: Felsöka Azure Data Factory problem
description: Lär dig hur du felsöker problem med att använda Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
robots: noindex
ms.openlocfilehash: 70c1aecd358b52a7b5b62ba7cb8f3badffef2998
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779927"
---
# <a name="troubleshoot-data-factory-issues"></a>Felsök Data Factory-problem
> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory. 

Den här artikeln innehåller fel söknings tips för problem när du använder Azure Data Factory. I den här artikeln beskrivs inte alla möjliga problem när du använder tjänsten, men det finns några problem och allmänna fel söknings tips.   

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="troubleshooting-tips"></a>Felsökningstips
### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Fel: Prenumerationen har inte registrerats för användning av namnområdet Microsoft.DataFactory
Om du får det här felmeddelandet har inte Azure Data Factory-resursprovidern registrerats på datorn. Gör följande:

1. Starta Azure PowerShell.
2. Logga in på ditt Azure-konto med hjälp av följande kommando.

   ```powershell
   Connect-AzAccount
   ```

3. Kör följande kommando för att registrera Azure Data Factory-providern.

   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
   ```

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Problem: ett obehörigt fel inträffade vid körning av en Data Factory-cmdlet
Du använder förmodligen inte rätt Azure-konto eller -prenumeration med Azure PowerShell. Använd följande cmdlets för att välja rätt Azure-konto och -prenumeration för Azure PowerShell.

1. Connect-AzAccount – Använd rätt användar-ID och lösen ord
2. Get-AzSubscription – Visa alla prenumerationer för kontot.
3. Select-AzSubscription &lt; prenumerations namn &gt; – Välj rätt prenumeration. Använd samma som du använder för att skapa en data fabrik på Azure Portal.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Problem: det gick inte att starta installations programmet för Data Management Gateway Express från Azure Portal
Expressinstallationen för Data Management Gateway kräver Internet Explorer eller en Microsoft ClickOnce-kompatibel webbläsare. Om det inte går att starta expressinstallationen, gör du något av följande:

* Använd Internet Explorer eller en Microsoft ClickOnce-kompatibel webbläsare.

    Om du använder Chrome går du till [Chrome-webbarkivet](https://chrome.google.com/webstore/), söker med nyckelordet "ClickOnce", väljer ett av ClickOnce-tilläggen och installerar det.

    Gör samma sak för Firefox (installera tillägg). Klicka på knappen Öppna menyn i verktygsfältet (tre horisontella linjer högst upp till höger), klicka på Tillägg, sök efter nyckelordet ”ClickOnce”, välj ett av ClickOnce-tilläggen och installera det.
* Använd den **manuella installations** länken som visas på samma blad i portalen. Du kan använda den här metoden för att ladda ned installations filen och köra den manuellt. När installationen har slutförts visas dialog rutan Data Management Gateway konfiguration. Kopiera **nyckeln** från portalskärmen och använd den i konfigurationshanteraren för att manuellt registrera gatewayen i tjänsten.  

### <a name="problem-fail-to-connect-to-sql-server"></a>Problem: det gick inte att ansluta till SQL Server
Starta **Data Management Gateway Configuration Manager** på gateway-datorn och Använd fliken **fel sökning** för att testa anslutningen till SQL Server från gateway-datorn. Mer information om fel sökning av problem med anslutning/Gateway finns i [Felsöka Gateway-problem](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .   

### <a name="problem-input-slices-are-in-waiting-state-forever"></a>Problem: angivna sektorer är i vänte läge för alltid
Sektorerna kan vara i **vänte** läge på grund av olika orsaker. En av de vanligaste orsakerna är att den **externa** egenskapen inte har angetts till **True**. Alla data uppsättningar som skapas utanför omfånget för Azure Data Factory ska markeras med **extern** egenskap. Den här egenskapen anger att data är externa och inte backas upp av några pipelines i data fabriken. Datasektorerna markeras som **Klar** när datauppsättningen är tillgänglig i respektive lager.

I följande exempel kan du se hur egenskapen **external** används. Du kan också ange **extern Aldata*** när du ställer in external till true.

Mer info om den här egenskapen finns i artikeln om [datauppsättningar](data-factory-create-datasets.md).

```json
{
  "name": "CustomerTable",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "MyLinkedService",
    "typeProperties": {
      "folderPath": "MyContainer/MySubFolder/",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      }
    }
  }
}
```

Lös problemet genom att lägga till egenskapen **external** och det valfria avsnittet **externalData** i indatatabellens JSON-definition. Återskapa sedan tabellen.

### <a name="problem-hybrid-copy-operation-fails"></a>Problem: det går inte att utföra hybrid kopiering
Se [Felsök Gateway-problem](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) för steg för att felsöka problem med att kopiera till/från ett lokalt data lager med hjälp av data Management Gateway.

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Problem: det går inte att utföra HDInsight-etablering på begäran
När du använder en länkad tjänst av typen HDInsightOnDemand, måste du ange ett linkedServiceName som pekar på en Azure-blobblagring. Data Factory-tjänsten använder den här lagringen för att lagra loggar och stödfiler för HDInsight-klustret på begäran.  Ibland går det inte att etablera ett HDInsight-kluster på begäran på grund av följande fel:

```
Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.
```

Det här felet innebär oftast att lagringskontots plats som anges i linkedServiceName inte är på samma datacenterplats där HDInsight-etableringen utförs. Exempel: om din data fabrik är i västra USA och Azure Storage är i östra USA, kan etableringen på begäran Miss lyckas i västra USA.

Dessutom finns det en andra JSON-egenskap, additionalLinkedServiceNames, där ytterligare lagringskonton kan anges i HDInsight på begäran. De ytterligare länkade lagrings kontona bör finnas på samma plats som HDInsight-klustret, eller så fungerar det inte med samma fel.

### <a name="problem-custom-net-activity-fails"></a>Problem: anpassad .NET-aktivitet Miss lyckas
Se [Felsöka en pipeline med anpassad aktivitet](data-factory-use-custom-activities.md#troubleshoot-failures) för detaljerade steg.

## <a name="use-azure-portal-to-troubleshoot"></a>Använd Azure Portal för att felsöka
### <a name="using-portal-blades"></a>Använda Portal-blad
Se [övervaka pipeline](data-factory-monitor-manage-pipelines.md) för steg.

### <a name="using-monitor-and-manage-app"></a>Övervaka och hantera app
Se [övervaka och hantera data Factory-pipeline med övervaka och hantera app](data-factory-monitor-manage-app.md) för mer information.

## <a name="use-azure-powershell-to-troubleshoot"></a>Använd Azure PowerShell för att felsöka
### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Felsöka ett fel med hjälp av Azure PowerShell
Mer information finns i [övervaka Data Factory pipelines med hjälp av Azure PowerShell](data-factory-monitor-manage-pipelines.md) .

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: /previous-versions/azure/dn835050(v=azure.100)

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png