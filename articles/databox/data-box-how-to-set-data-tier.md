---
title: Skicka data till frekvent, kall, Arkiv BLOB-nivå via Azure Data Box/Azure Data Box Heavy
description: Beskriver hur du använder Azure Data Box eller Azure Data Box Heavy för att skicka data till en lämplig Block Blob Storage-nivå, till exempel frekvent, kall eller Arkiv
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 05/24/2019
ms.author: alkohli
ms.openlocfilehash: 80c4d8a70454c007ac45f588e59c03ef45f10933
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92125072"
---
# <a name="use-azure-data-box-or-azure-data-box-heavy-to-send-data-to-appropriate-azure-storage-blob-tier"></a>Använd Azure Data Box eller Azure Data Box Heavy för att skicka data till rätt Azure Storage BLOB-nivå

Azure Data Box flyttar stora mängder data till Azure genom att leverera en patentskyddad lagrings enhet. Du fyller i enheten med data och returnerar den. Data från Data Box-enhet överförs till en standard nivå som är kopplad till lagrings kontot. Du kan sedan flytta data till en annan lagrings nivå.

Den här artikeln beskriver hur data som laddas upp av Data Box-enhet kan flyttas till en varm, kall eller Arkiv-blobnivå. Den här artikeln gäller både Azure Data Box och Azure Data Box Heavy.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="choose-the-correct-storage-tier-for-your-data"></a>Välj rätt lagrings nivå för dina data

Azure Storage gör det möjligt för tre olika nivåer att lagra data på det mest kostnads effektiva sättet – frekvent, kall eller arkiv. Frekvent lagrings nivå är optimerad för att lagra data som används ofta. Frekvent lagring har högre lagrings kostnader än låg frekvent lagring och Arkiv lag ring, men de lägsta åtkomst kostnaderna.

Låg frekvent lagrings nivå är för data som inte används ofta och som måste lagras i minst 30 dagar. Lagrings kostnaden för kall nivå är lägre än den för frekvent lagrings nivå men data åtkomst avgifterna är höga jämfört med frekvent nivå.

Azure Archive-nivån är offline och erbjuder de lägsta lagrings kostnaderna, men även de högsta åtkomst kostnaderna. Den här nivån är avsedd för data som finns kvar i Arkiv lagring under minst 180 dagar. Mer information om var och en av dessa nivåer och pris sättnings modellen finns i [jämförelse av lagrings nivåerna](../storage/blobs/storage-blob-storage-tiers.md).

Data från Data Box-enhet eller Data Box Heavy överförs till en lagrings nivå som är kopplad till lagrings kontot. När du skapar ett lagrings konto kan du ange åtkomst nivån som hett eller kall. Beroende på åtkomst mönstret för din arbets belastning och kostnad kan du flytta dessa data från standard nivån till en annan lagrings nivå.

Du kan bara Skikta objekt lagrings data i Blob Storage-eller Generell användning v2-konton (GPv2). GPv1-konton (General Purpose v1) har inte stöd för flera lagringsnivåer. Om du vill välja rätt lagrings nivå för dina data granskar du de överväganden som beskrivs i [Azure Blob Storage: Premium,](../storage/blobs/storage-blob-storage-tiers.md)frekvent, låg frekvent och Arkiv lag rings nivå.

## <a name="set-a-default-blob-tier"></a>Ange en standard-BLOB-nivå

Standard-BLOB-nivån anges när lagrings kontot skapas i Azure Portal. När du har valt en lagrings typ som GPv2 eller Blob Storage kan du ange attributet åtkomst nivå. Som standard är den aktiva nivån markerad.

Det går inte att ange nivåerna om du försöker skapa ett nytt konto när du beställer en Data Box-enhet eller Data Box Heavy. När kontot har skapats kan du ändra kontot i portalen för att ställa in standard åtkomst nivån.

Du kan också skapa ett lagrings konto först med det angivna attributet för åtkomst nivå. När du skapar Data Box-enhet-eller Data Box Heavys ordningen väljer du det befintliga lagrings kontot. Mer information om hur du ställer in standard-BLOB-nivån när du skapar ett lagrings konto finns i [skapa ett lagrings konto i Azure Portal](../storage/common/storage-account-create.md?tabs=portal).

## <a name="move-data-to-a-non-default-tier"></a>Flytta data till en annan nivå än standard nivån

När data från Data Box-enhet enhet har laddats upp till standard nivån kanske du vill flytta data till en annan nivå än standard nivån. Det finns två sätt att flytta dessa data till en annan nivå än standard nivån.

- **Azure Blob Storage livs cykel hantering** – du kan använda en principbaserad metod för att automatiskt nivå data eller förfalla i slutet av livs cykeln. Mer information finns i [Hantera Azure Blob Storage-livscykeln](../storage/blobs/storage-lifecycle-management-concepts.md).
- **Skript** – du kan använda en skript metod via Azure PowerShell för att aktivera skiktning på BLOB-nivå. Du kan anropa `SetBlobTier` åtgärden för att ange nivån på blobben.

## <a name="use-azure-powershell-to-set-the-blob-tier"></a>Använd Azure PowerShell för att ställa in BLOB-nivån

Följande steg beskriver hur du kan ställa in BLOB-nivån att arkivera med ett Azure PowerShell-skript.

1. Öppna en upphöjd Windows PowerShell-session. Kontrol lera att du kör PowerShell 5,0 eller senare. Ange:

   `$PSVersionTable.PSVersion`     

2. Logga in på Azure PowerShell. 

   `Login-AzAccount`  

3. Definiera variabler för lagrings konto, åtkomst nyckel, behållare och lagrings kontext.

    ```powershell
    $StorageAccountName = "<enter account name>"
    $StorageAccountKey = "<enter account key>"
    $ContainerName = "<enter container name>"
    $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    ```

4. Hämta alla blobar i behållaren.

    `$blobs = Get-AzStorageBlob -Container "<enter container name>" -Context $ctx`
 
5. Ange nivån för alla blobar i behållaren som ska arkiveras.

    ```powershell
    Foreach ($blob in $blobs) {
    $blob.ICloudBlob.SetStandardBlobTier("Archive")
    }
    ```

    Ett exempel på utdata visas nedan:

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    PS C:\WINDOWS\system32> $PSVersionTable.PSVersion

    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      17763  134
    PS C:\WINDOWS\system32> Login-AzAccount

    Account          : gus@contoso.com
    SubscriptionName : MySubscription
    SubscriptionId   : subscription-id
    TenantId         : tenant-id
    Environment      : AzureCloud

    PS C:\WINDOWS\system32> $StorageAccountName = "mygpv2storacct"
    PS C:\WINDOWS\system32> $StorageAccountKey = "mystorageacctkey"
    PS C:\WINDOWS\system32> $ContainerName = "test"
    PS C:\WINDOWS\system32> $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    PS C:\WINDOWS\system32> $blobs = Get-AzStorageBlob -Container "test" -Context $ctx
    PS C:\WINDOWS\system32> Foreach ($blob in $blobs) {
    >> $blob.ICloudBlob.SetStandardBlobTier("Archive")
    >> }
    PS C:\WINDOWS\system32>
    ```
   > [!TIP]
   > Om du vill att data ska arkiveras vid inmatning ställer du in standard konto nivån på aktiv. Om standard nivån är låg, finns det en 30 dagars tidig borttagnings avgift om data flyttas direkt.

## <a name="next-steps"></a>Nästa steg

-  Lär dig hur du åtgärdar [vanliga data skikts scenarier med policy regler för livs cykeln](../storage/blobs/storage-lifecycle-management-concepts.md#examples)