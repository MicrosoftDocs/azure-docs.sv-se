---
title: Introduktion till blob-lagring (objekt)
titleSuffix: Azure Storage
description: Använd Azure Blob Storage för att lagra enorma mängder ostrukturerade objekt data, till exempel text eller binära data. Azure Blob Storage är mycket skalbar och tillgänglig.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 06/24/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 8e3f0435d9856d2b602a4f2b6f604aee05e040ba
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505213"
---
# <a name="introduction-to-azure-blob-storage"></a>Introduktion till Azure Blob storage

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

## <a name="blob-storage-resources"></a>Blobblagringsresurser

I blobblagringen finns tre typer av resurser:

- **Lagrings kontot**
- En **container** på lagringskontot
- En **blobb** i en container

Följande diagram visar relationen mellan de här resurserna.

![Diagram över förhållandet mellan ett lagrings konto, behållare och blobbar](./media/storage-blobs-introduction/blob1.png)

### <a name="storage-accounts"></a>Lagringskonton

På ett lagringskonto finns ett unikt namnområde i Azure för dina data. Alla objekt som du lagrar i Azure Storage har en adress som innehåller ditt unika kontonamn. Kombinationen av kontonamnet och Azure Storage-blobslutpunkten utgör basadressen för objekten i ditt lagringskonto.

Om ditt lagringskonto till exempel heter *mystorageaccount*, kommer standardslutpunkterna för blobblagringen att vara:

```
http://mystorageaccount.blob.core.windows.net
```

Om du vill skapa ett lagringskonto kan du läsa mer i [Skapa ett lagringskonto](../common/storage-account-create.md). Mer information om lagringskonton finns i [Översikt över Azure-lagringskonton](../common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="containers"></a>Containrar

I en container finns en uppsättning med blobbar, ungefär som i en katalog i ett filsystem. Ett lagringskonto kan omfatta ett obegränsat antal containrar, och varje container kan lagra ett obegränsat antal blobar.

> [!NOTE]
> Containerns namn får endast innehålla gemener. Mer information om namngivning av containrar finns i [Ge namn och referera till containrar, blobbar och metadata](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).

### <a name="blobs"></a>Blobar

Azure Storage har stöd för tre typer av blobbar:

- **Block-blobs** lagrar text och binära data. Blockblobbar består av datablock som kan hanteras individuellt. Block-blobs lagrar upp till cirka 4,75 TiB data. Större block blobbar är tillgängliga i för hands versionen, upp till cirka 190,7 TiB
- **Lägg till blobar** består av block som block blobbar, men är optimerade för att lägga till åtgärder. Tilläggsblobar passar utmärkt för åtgärder som att logga data från virtuella datorer.
- **Page blobbar** lagrar slumpmässiga åtkomst filer upp till 8 TIB i storlek. Page blobbar lagrar filer för virtuella hård diskar (VHD) och fungerar som diskar för virtuella Azure-datorer. Mer information om sidblobar finns [översikten av Azure-sidblobar](storage-blob-pageblob-overview.md)

Mer information om de olika typerna av blobbar finns i [Förstå blockblobbar, sidblobbar och bilageblobbar](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="move-data-to-blob-storage"></a>Flytta data till blobblagring

Det finns flera olika lösningar för att migrera befintliga data till blobblagring:

- **AzCopy** är ett lättanvänt kommandoradsverktyg för Windows och Linux som kopierar data till och från blobblagringen, mellan containrar eller mellan lagringskonton. Mer information om AzCopy finns i [överföra data med AZCopy-v10](../common/storage-use-azcopy-v10.md).
- **Azure Storages dataflyttbibliotek** är ett .NET-bibliotek som används till att flytta data mellan Azure Storage-tjänster. AzCopy-verktyget skapas med dataflyttbiblioteket. Mer information finns i [referensdokumentationen](/dotnet/api/microsoft.azure.storage.datamovement) för dataflyttbiblioteket.
- **Azure Data Factory** stöder kopiering av data till och från Blob Storage med hjälp av konto nyckeln, en signatur för delad åtkomst, ett huvud namn för tjänsten eller hanterade identiteter för Azure-resurser. Mer information finns i [Kopiera data till och från Azure-blobblagring med hjälp av Azure Data Factory](../../data-factory/connector-azure-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
- **Blobfuse** är en virtuell filsystemsdrivrutin för Azure-blobblagring. Du kan använda blobfuse för att få åtkomst till dina befintliga blockblobbdata på ditt lagringskonto via filsystemet Linux. Mer information finns i [Montera blobblagring som ett filsystem med blobfuse](storage-how-to-mount-container-linux.md).
- **Azure Data Box** tjänst är tillgänglig för överföring av lokala data till blob-lagring när stora data mängder eller nätverks begränsningar gör det möjligt att ladda upp data över den realistiska tråden. Beroende på din data storlek kan du begära [Azure Data Box disk](../../databox/data-box-disk-overview.md), [Azure Data Box](../../databox/data-box-overview.md)eller [Azure Data Box Heavy](../../databox/data-box-heavy-overview.md) enheter från Microsoft. Du kan sedan kopiera dina data till de enheterna och skicka tillbaka dem till Microsoft för att laddas upp till Blob Storage.
- **Tjänsten Azure import/export** är ett sätt att importera eller exportera stora data mängder till och från ditt lagrings konto med hjälp av hård diskar som du anger. Mer information finns i [Använda Microsoft Azure Import/Export till att överföra data till blobblagring](../common/storage-import-export-service.md).

## <a name="next-steps"></a>Nästa steg

- [Skapa ett lagringskonto](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Skalbarhets-och prestanda mål för Blob Storage](scalability-targets.md)
