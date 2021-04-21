---
title: Lagringsöverväganden för Azure Functions
description: Lär dig mer om lagringskraven för Azure Functions och om kryptering av lagrade data.
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: 5faa85a4fac9fc0b8639f33c475283f4f043c627
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779263"
---
# <a name="storage-considerations-for-azure-functions"></a>Lagringsöverväganden för Azure Functions

Azure Functions kräver ett Azure Storage-konto när du skapar en funktionsappinstans. Följande lagringstjänster kan användas av funktionsappen:


|Lagringstjänst  | Användning av funktioner  |
|---------|---------|
| [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)     | Underhåll bindningars tillstånd och funktionsnycklar.  <br/>Används också av [aktivitetshubben i Durable Functions](durable/durable-functions-task-hubs.md). |
| [Azure Files](../storage/files/storage-files-introduction.md)  | Filresurs som används för att lagra och köra funktionsappens kod i en [förbrukningsplan](consumption-plan.md) och [en Premium-plan.](functions-premium-plan.md) |
| [Azure Queue Storage](../storage/queues/storage-queues-introduction.md)     | Används av [aktivitetshubar i Durable Functions](durable/durable-functions-task-hubs.md).   |
| [Azure Table Storage](../storage/tables/table-storage-overview.md)  |  Används av [aktivitetshubar i Durable Functions](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> När du använder förbruknings-/Premium-värdplanen lagras funktionskoden och bindningskonfigurationsfilerna i Azure File Storage i huvudlagringskontot. När du tar bort huvudlagringskontot tas även det här innehållet bort och kan inte återställas.

## <a name="storage-account-requirements"></a>Krav för lagringskonto

När du skapar en funktionsapp måste du skapa eller länka till ett allmänt Azure Storage som stöder blob-, kö- och tabellagring. Det beror på att Functions är Azure Storage för åtgärder som att hantera utlösare och logga funktionskörningar. Vissa lagringskonton stöder inte köer och tabeller. Dessa konton omfattar endast bloblagringskonton och Azure Premium Storage.

Mer information om lagringskontotyper finns i [introduktionen till Azure Storage Services](../storage/common/storage-introduction.md#core-storage-services). 

Du kan använda ett befintligt lagringskonto med funktionsappen, men du måste se till att det uppfyller dessa krav. Lagringskonton som skapas som en del av funktionsappens skapa-flöde i Azure Portal kommer garanterat att uppfylla dessa krav på lagringskonton. Konton som inte stöds filtreras bort i portalen när du väljer ett befintligt lagringskonto när du skapar en funktionsapp. I det här flödet kan du bara välja befintliga lagringskonton i samma region som funktionsappen som du skapar. Mer information finns i [Lagringskontoplats.](#storage-account-location)

<!-- JH: Does using a Premium Storage account improve perf? -->

## <a name="storage-account-guidance"></a>Vägledning för lagringskonto

Varje funktionsapp kräver ett lagringskonto för att fungera. Om kontot tas bort körs inte funktionsappen. Information om hur du felsöker lagringsrelaterade [problem finns i Så här felsöker du lagringsrelaterade problem.](functions-recover-storage-account.md) Följande ytterligare överväganden gäller för lagringskontot som används av funktionsappar.

### <a name="storage-account-location"></a>Lagringskontoplats

För bästa prestanda bör funktionsappen använda ett lagringskonto i samma region, vilket minskar svarstiden. Den Azure Portal tillämpar denna bästa praxis. Om du av någon anledning behöver använda ett lagringskonto i en annan region än funktionsappen måste du skapa funktionsappen utanför portalen. 

### <a name="storage-account-connection-setting"></a>Anslutningsinställning för lagringskonto

Anslutningen till lagringskontot finns i [programinställningen AzureWebJobsStorage.](./functions-app-settings.md#azurewebjobsstorage) 

Anslutningssträngen för lagringskontot måste uppdateras när du återskapar lagringsnycklar. [Läs mer om hantering av lagringsnyckel här.](../storage/common/storage-account-create.md)

### <a name="shared-storage-accounts"></a>Delade lagringskonton

Det är möjligt för flera funktionsappar att dela samma lagringskonto utan problem. I det här Visual Studio du till exempel utveckla flera appar med hjälp av Azure Storage emulatorn. I det här fallet fungerar emulatorn som ett enda lagringskonto. Samma lagringskonto som används av funktionsappen kan också användas för att lagra dina programdata. Den här metoden är dock inte alltid en bra idé i en produktionsmiljö.

### <a name="optimize-storage-performance"></a>Optimera lagringsprestanda

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Kryptering av lagringsdata

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

### <a name="in-region-data-residency"></a>Datahemhemlighet i regionen

När alla kunddata måste finnas inom en enda region måste det lagringskonto som är associerat med funktionsappen vara ett [med redundans i regionen](../storage/common/storage-redundancy.md). Ett redundant lagringskonto i regionen måste också användas med [Azure Durable Functions](./durable/durable-functions-perf-and-scale.md#storage-account-selection).

Andra plattformsbaserade kunddata lagras bara i regionen när de hanteras i en internt belastningsutjämnad App Service-miljön (ASE). Mer information finns i [ASE-zonredundans.](../app-service/environment/zone-redundancy.md#in-region-data-residency)

## <a name="mount-file-shares"></a>Montera filresurser

_Den här funktionen är för närvarande endast tillgänglig när den körs i Linux._ 

Du kan montera befintliga Azure Files-resurser till dina Linux-funktionsappar. Genom att montera en resurs i din Linux-funktionsapp kan du använda befintliga maskininlärningsmodeller eller andra data i dina funktioner. Du kan använda kommandot [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) för att montera en befintlig resurs till din Linux-funktionsapp. 

I det här kommandot är namnet på den befintliga Azure Files resursen och kan vara valfri sträng som unikt definierar resursen när den monteras `share-name` `custom-id` i funktionsappen. Är också `mount-path` den sökväg som resursen nås från i funktionsappen. `mount-path` måste ha formatet `/dir-name` och kan inte börja med `/home` .

Ett komplett exempel finns i skripten i Skapa en [Python-funktionsapp och montera en Azure Files dela](scripts/functions-cli-mount-files-storage-linux.md). 

För närvarande stöds `storage-type` endast en `AzureFiles` av. Du kan bara montera fem resurser till en viss funktionsapp. Att montera en filresurs kan öka kallstartstiden med minst 200–300 ms eller ännu mer när lagringskontot finns i en annan region.

Den monterade resursen är tillgänglig för funktionskoden på `mount-path` angiven . När är till exempel kan du komma åt `mount-path` `/path/to/mount` målkatalogen via FILSYSTEM-API:er, som i följande Python-exempel:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Functions värdalternativ.

> [!div class="nextstepaction"]
> [Skala och var värd i Azure Functions](functions-scale.md)
