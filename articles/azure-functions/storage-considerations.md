---
title: Lagrings överväganden för Azure Functions
description: Läs mer om lagrings kraven för Azure Functions och om kryptering av lagrade data.
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: c4ffb622482585e35337caf8e43b69e0f3b0385c
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517271"
---
# <a name="storage-considerations-for-azure-functions"></a>Lagrings överväganden för Azure Functions

Azure Functions kräver ett Azure Storage-konto när du skapar en Function App-instans. Följande lagrings tjänster kan användas av din Function-app:


|Lagrings tjänst  | Funktions användning  |
|---------|---------|
| [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)     | Bevara bindnings tillstånd och funktions nycklar.  <br/>Används också av [aktivitets nav i Durable Functions](durable/durable-functions-task-hubs.md). |
| [Azure Files](../storage/files/storage-files-introduction.md)  | Fil resurs som används för att lagra och köra din Function app-kod i en [förbruknings plan](consumption-plan.md) och [Premium-plan](functions-premium-plan.md). |
| [Azure Queue Storage](../storage/queues/storage-queues-introduction.md)     | Används av [aktivitets nav i Durable Functions](durable/durable-functions-task-hubs.md).   |
| [Azure Table Storage](../storage/tables/table-storage-overview.md)  |  Används av [aktivitets nav i Durable Functions](durable/durable-functions-task-hubs.md).       |

> [!IMPORTANT]
> När du använder användnings-/Premium värd planen lagras funktions koden och bindningens konfigurationsfiler i Azure File Storage i huvud lagrings kontot. När du tar bort huvudlagringskontot tas även det här innehållet bort och kan inte återställas.

## <a name="storage-account-requirements"></a>Krav för lagringskonto

När du skapar en Function-app måste du skapa eller länka till ett allmänt Azure Storage konto som har stöd för BLOB-, Queue-och table-lagring. Detta beror på att funktioner förlitar sig på Azure Storage för åtgärder som att hantera utlösare och loggning av funktions körningar. Vissa lagrings konton stöder inte köer och tabeller. Dessa konton inkluderar enbart BLOB-lagrings konton och Azure Premium Storage.

Mer information om lagringskontotyper finns i [introduktionen till Azure Storage Services](../storage/common/storage-introduction.md#core-storage-services). 

Även om du kan använda ett befintligt lagrings konto med din Function-app måste du kontrol lera att det uppfyller dessa krav. Lagrings konton som skapats som en del av Function-appen Create Flow i Azure Portal garanterar att de uppfyller dessa krav för lagrings kontot. Konton som inte stöds filtreras ut när du väljer ett befintligt lagrings konto när du skapar en Function-app i portalen. I det här flödet får du bara välja befintliga lagrings konton i samma region som den Function-app som du skapar. Mer information finns i [lagrings kontots plats](#storage-account-location).

<!-- JH: Does using a Premium Storage account improve perf? -->

## <a name="storage-account-guidance"></a>Vägledning för lagrings konto

Alla Functions-appar kräver ett lagrings konto för att fungera. Om kontot har tagits bort körs inte funktions programmet. Information om hur du felsöker problem med lagring finns i [Felsöka problem som rör lagring](functions-recover-storage-account.md). Följande ytterligare överväganden gäller för det lagrings konto som används av Function-appar.

### <a name="storage-account-location"></a>Lagringskontoplats

För bästa prestanda bör din Function-app använda ett lagrings konto i samma region, vilket minskar svars tiden. Azure Portal tillämpar den här rekommenderade metoden. Om du av någon anledning behöver använda ett lagrings konto i en annan region än din Function-app måste du skapa din funktions app utanför portalen. 

### <a name="storage-account-connection-setting"></a>Anslutnings inställning för lagrings konto

Lagrings konto anslutningen upprätthålls i [program inställningen AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

Anslutnings strängen för lagrings kontot måste uppdateras när du återskapar lagrings nycklar. [Läs mer om hantering av lagrings nycklar här](../storage/common/storage-account-create.md).

### <a name="shared-storage-accounts"></a>Delade lagrings konton

Det är möjligt att flera Function-appar delar samma lagrings konto utan problem. I Visual Studio kan du till exempel utveckla flera appar med hjälp av Azure Storage emulatorn. I det här fallet fungerar emulatorn som ett enda lagrings konto. Samma lagrings konto som används av din Function-app kan också användas för att lagra program data. Den här metoden är dock inte alltid en bra idé i en produktions miljö.

### <a name="optimize-storage-performance"></a>Optimera lagringsprestanda

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Kryptering av lagrings data

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

### <a name="in-region-data-residency"></a>Placering för region data

När alla kunddata måste finnas kvar inom en enda region måste det lagrings konto som är kopplat till Function-appen vara ett med [redundans på region](../storage/common/storage-redundancy.md). Ett redundant lagrings konto i regionen måste också användas med [Azure Durable Functions](./durable/durable-functions-perf-and-scale.md#storage-account-selection).

Andra plattforms hanterade kunddata lagras bara inom regionen när de är värdar för ett internt belastningsutjämnad App Service-miljön (ASE). Läs mer i [ASE Zone-redundans](../app-service/environment/zone-redundancy.md#in-region-data-residency).

## <a name="mount-file-shares"></a>Montera fil resurser

_Den här funktionen är endast tillgänglig när den körs på Linux._ 

Du kan montera befintliga Azure Files resurser i dina Linux Function-appar. Genom att montera en resurs till din Linux Function-app kan du utnyttja befintliga maskin inlärnings modeller eller andra data i dina funktioner. Du kan använda [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) kommandot för att montera en befintlig resurs till din Linux Function-app. 

I det här kommandot `share-name` är namnet på den befintliga Azure Files resursen och `custom-id` kan vara en sträng som unikt definierar resursen när den monteras i Function-appen. Är också `mount-path` sökvägen från vilken resursen nås i din Function-app. `mount-path` måste vara i formatet `/dir-name` och får inte börja med `/home` .

Ett fullständigt exempel finns i skripten i [skapa en python Function-app och montera en Azure Files-resurs](scripts/functions-cli-mount-files-storage-linux.md). 

För närvarande stöds endast en `storage-type` av `AzureFiles` . Du kan bara montera fem resurser till en specifik Function-app. Genom att montera en fil resurs kan du öka den kall start tiden med minst 200-300ms, eller till och med mer om lagrings kontot finns i en annan region.

Den monterade resursen är tillgänglig för funktions koden i den `mount-path` angivna. Om till exempel `mount-path` är `/path/to/mount` , kan du komma åt mål katalogen med API: er för fil system, som i följande python-exempel:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Functions värd alternativ.

> [!div class="nextstepaction"]
> [Skala och var värd i Azure Functions](functions-scale.md)
