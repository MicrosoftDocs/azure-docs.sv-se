---
title: Dehydratisera BLOB-data från Arkiv lag rings nivå
description: Gör om Blobbarna från Arkiv lag ring så att du kan komma åt BLOB-data. Kopiera en arkiverad blob till en onlinenivå.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/11/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 2f0ddca9cbd7d85909b1d86e68b92fa1d847476d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103225089"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Dehydratisera BLOB-data från Arkiv lag rings nivå

När en BLOB finns i Arkiv åtkomst nivån anses den vara offline och kan inte läsas eller ändras. BLOB-metadata är online och tillgängliga, så att du kan lista bloben och dess egenskaper. Läsning och ändring av BLOB-data är bara tillgängligt med online-nivåer som frekvent eller låg frekvent. Det finns två alternativ för att hämta och komma åt data som lagras i Arkiv åtkomst nivån.

1. [Dehydratiserar en arkiverad blob till en online-nivå](#rehydrate-an-archived-blob-to-an-online-tier) – reserverar en Arkiv-blob till frekvent eller låg frekvent genom att ändra dess nivå med hjälp av åtgärden [Ange BLOB-nivå](/rest/api/storageservices/set-blob-tier) .
2. [Kopiera en arkiverad blob till en online-nivå](#copy-an-archived-blob-to-an-online-tier) – skapa en ny kopia av en Arkiv-BLOB med hjälp av åtgärden [Kopiera BLOB](/rest/api/storageservices/copy-blob) . Ange ett annat BLOB-namn och en mål nivå för frekvent eller låg frekvent.

 Mer information om nivåer finns i [Azure Blob Storage: frekvent åtkomst, låg frekvent åtkomst och Arkiv](storage-blob-storage-tiers.md)lag rings nivåer.

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Retorkade en arkiverad blob till en onlinenivå

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

### <a name="lifecycle-management"></a>Livs cykel hantering

Återuppväcks en BLOB ändrar inte dess `Last-Modified` tid. Genom att använda [livs cykel hanterings](storage-lifecycle-management-concepts.md) funktionen kan du skapa ett scenario där en BLOB har reserver ATS, och sedan flyttar en princip för livs cykel hantering tillbaka bloben till arkivet, eftersom `Last-Modified` tiden överskrider den tröskel som angetts för principen. Undvik det här scenariot genom att använda *[Kopiera en arkiverad blob till en onlinenivå](#copy-an-archived-blob-to-an-online-tier)* metod. Kopierings metoden skapar en ny instans av blobben med en uppdaterad `Last-Modified` tid och utlöser inte policyn för livs cykel hantering.

## <a name="monitor-rehydration-progress"></a>Övervaka ÅTERUPPVÄCKNING-förlopp

Under ÅTERUPPVÄCKNING använder du åtgärden Hämta BLOB-egenskaper för att kontrol lera attributet **arkivera status** och bekräfta när nivå ändringen har slutförts. Status är ”rehydrate-pending-to-hot” eller ”rehydrate-pending-to-cool” beroende på målnivån. När åtgärden har slutförts tas statusen för status för arkivet bort och egenskapen BLOB för **åtkomst nivå** visar den nya frekventa eller låg frekventa nivån.

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Kopiera en arkiverad blob till onlinenivå

Om du inte vill skapa en nytorkad Arkiv-BLOB kan du välja att göra en [kopierings-BLOB](/rest/api/storageservices/copy-blob) -åtgärd. Den ursprungliga blobben förblir oförändrad i arkivet medan en ny BLOB skapas på låg frekvent eller låg frekvent nivå så att du kan arbeta med. I åtgärden **Kopiera BLOB** kan du också ange den valfria egenskapen *x-MS-rehydratiserat-Priority* till standard eller hög för att ange den prioritet som du vill att din BLOB-kopia ska skapas i.

Det kan ta flera timmar att kopiera en BLOB från arkivet, beroende på vilken rehydratiserad prioritet som har valts. I bakgrunden läser **kopierings-BLOB** -åtgärden din Arkiv käll-BLOB för att skapa en ny online-BLOB på den valda mål nivån. Den nya blobben kan vara synlig när du listar blobbar, men data är inte tillgängliga förrän läsningen från källans Arkiv-BLOB har slutförts och data skrivs till den nya online-målcachen. Den nya blobben är som en oberoende kopia och eventuella ändringar eller borttagningar av den påverkar inte källans Arkiv-blob.

> [!IMPORTANT]
> Ta inte bort käll-bloben förrän kopieringen har slutförts på mål platsen. Om käll-bloben tas bort kan inte mål-bloben slutföra kopieringen och måste vara tom. Du kan kontrol lera *x-MS-Copy-status* för att fastställa status för kopierings åtgärden.

Arkiv-blobbar kan bara kopieras till mål nivåerna online inom samma lagrings konto. Det finns inte stöd för att kopiera en Arkiv-blob till en annan Archive-blob. I följande tabell visas funktionerna i en **kopierings-BLOB** -åtgärd.

|                                           | **Källa för frekvent nivå**   | **Källa för låg frekvent nivå** | **Arkiv nivå källa**    |
| ----------------------------------------- | --------------------- | -------------------- | ------------------- |
| **Hot nivå mål**                  | Stöds             | Stöds            | Stöds inom samma konto; väntande rehydratisera               |
| **Mål på låg nivå**                 | Stöds             | Stöds            | Stöds inom samma konto; väntande rehydratisera               |
| **Mål på Arkiv nivå**              | Stöds             | Stöds            | Stöd saknas         |

## <a name="pricing-and-billing"></a>Priser och fakturering

Återuppväcks blobs från arkivet till frekventa eller låg frekventa nivåer debiteras som Läs åtgärder och data hämtning. Användning av hög prioritet har högre kostnader för drift och data hämtning jämfört med standard prioritet. Hög prioritet ÅTERUPPVÄCKNING visas som ett separat rad objekt på fakturan. Om en begäran om hög prioritet för att returnera en Arkiv-blob av några få gigabyte tar över 5 timmar debiteras du inte den hög prioritets hämtnings takten. Dock gäller standard avgifter för hämtning fortfarande när ÅTERUPPVÄCKNING prioriteras för andra förfrågningar.

Att kopiera blobbar från arkivet till frekventa eller låg frekventa nivåer debiteras som Läs åtgärder och data hämtning. En Skriv åtgärd debiteras för att skapa den nya BLOB-kopian. Avgifter för tidig borttagning gäller inte när du kopierar till en online-BLOB eftersom käll-bloben förblir oförändrad på Arkiv nivån. Högprioriterade hämtnings avgifter gäller om de valts.

Blobbar i Arkiv lag rings nivån lagras i minst 180 dagar. Om du tar bort eller återuppväcks arkiverade blobbar innan 180 dagar påförs avgifter för tidig borttagning.

> [!NOTE]
> Mer information om priser för block-blobar och data ÅTERUPPVÄCKNING finns [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage/blobs/). Mer information om avgifter för utgående data överföringar finns i [pris uppgifter för data överföring](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="quickstart-scenarios"></a>Snabbstartsscenarier

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>Dehydratiserar en Arkiv-blob till en onlinenivå
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Sök efter och välj **alla resurser** i Azure Portal.

1. Välj ditt lagringskonto.

1. Välj din behållare och välj sedan din BLOB.

1. I **BLOB-egenskaperna** väljer du **ändra nivå**.

1. Välj frekvent **eller** låg **frekvent åtkomst nivå** . 

1. Välj en rehydratiserad prioritet på **standard** eller **hög**.

1. Välj **Spara** längst ned.

![Ändra ](media/storage-tiers/blob-access-tier.png)
 status för kontroll av lagrings konto nivå ![](media/storage-tiers/rehydrate-status.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Följande PowerShell-skript kan användas för att ändra BLOB-nivån för en Arkiv-blob. `$rgName`Variabeln måste initieras med resurs gruppens namn. `$accountName`Variabeln måste initieras med ditt lagrings konto namn. `$containerName`Variabeln måste initieras med ditt container namn. `$blobName`Variabeln måste initieras med ditt BLOB-namn. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", "Standard")
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>Kopiera en Arkiv-blob till en ny BLOB med en onlinenivå
Följande PowerShell-skript kan användas för att kopiera en Arkiv-blob till en ny BLOB inom samma lagrings konto. `$rgName`Variabeln måste initieras med resurs gruppens namn. `$accountName`Variabeln måste initieras med ditt lagrings konto namn. `$srcContainerName` `$destContainerName` Variablerna och måste initieras med dina behållar namn. `$srcBlobName` `$destBlobName` Variablerna och måste initieras med dina BLOB-namn. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$srcContainerName = ""
$destContainerName = ""
$srcBlobName = ""
$destBlobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Copy source blob to a new destination blob with access tier hot using standard rehydrate priority
Start-AzStorageBlobCopy -SrcContainer $srcContainerName -SrcBlob $srcBlobName -DestContainer $destContainerName -DestBlob $destBlobName -StandardBlobTier Hot -RehydratePriority Standard -Context $ctx
```

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om Blob Storage nivåer](storage-blob-storage-tiers.md)
* [Kontrollera priser för frekvent/lågfrekvent lagring och arkivlagring i Blob Storage-/GPv2-konton efter region](https://azure.microsoft.com/pricing/details/storage/)
* [Hantera Azure Blob Storage-livscykeln](storage-lifecycle-management-concepts.md)
* [Se priser för dataöverföring](https://azure.microsoft.com/pricing/details/data-transfers/)