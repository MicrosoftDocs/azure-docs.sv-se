---
title: Microsoft Azure Data Box Gateway användnings fall | Microsoft Docs
description: Beskriver användnings fall för Azure Data Box Gateway, en lagrings lösning för virtuella enheter som gör att du kan överföra data till Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 3bf137f968082e677f45c20947793232b9181220
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98786620"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Användnings fall för Azure Data Box Gateway

Azure Data Box Gateway är en gateway för en moln lagrings enhet som finns på din lokala plats och skickar din avbildning, media och annan data till Azure. Den här moln lagrings-gatewayen är en virtuell dator som är etablerad i hypervisorn. Du skriver data till den här virtuella enheten med hjälp av NFS-och SMB-protokollen, som sedan skickas till Azure. Den här artikeln innehåller en detaljerad beskrivning av de scenarier där du kan distribuera den här enheten.

Använd Data Box Gateway för följande scenarier:

- För att kontinuerligt Hämta enorma mängder data.
- För lagring av data i molnet på ett säkert och effektivt sätt.
- För stegvis data överföring över nätverket när den första Mass överföringen är klar med Data Box-enhet.

Var och en av dessa scenarier beskrivs i detalj i följande avsnitt.


## <a name="continuous-data-ingestion"></a>Kontinuerlig data inmatning

En av de främsta fördelarna med Data Box Gateway är möjligheten att kontinuerligt mata in data i enheten för att kopiera till molnet, oavsett data storlek.

När data skrivs till gateway-enheten överför enheten data till Azure Storage. Enheten hanterar lagringen automatiskt genom att ta bort filerna lokalt samtidigt som de behåller metadata när de når ett visst tröskelvärde. Om du behåller en lokal kopia av metadata kan gateway-enheten bara överföra ändringarna när filen uppdateras. De data som överförs till din gateway-enhet bör vara enligt rikt linjerna i [data överförings varningar](data-box-gateway-limits.md#data-upload-caveats).

När enheten fyller med data börjar den begränsa ingångs frekvensen (efter behov) för att matcha den hastighet med vilken data överförs till molnet. Om du vill övervaka den kontinuerliga inmatningen på enheten använder du aviseringar. De här aviseringarna utlöses när begränsningen börjar och rensas när begränsningen har stoppats.

## <a name="cloud-archival-of-data"></a>Lagring av data i molnet

Använd Data Box Gateway när du vill behålla dina data för lång sikt i molnet. Du kan använda lagrings nivån Arkiv för långsiktig kvarhållning.

Arkiv nivån är optimerad för att lagra data som sällan används i minst 180 dagar. Arkiv nivån ger de lägsta lagrings kostnaderna men har de högsta åtkomst kostnaderna. Mer information finns på [Arkiv åtkomst nivå](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

### <a name="move-data-to-the-archive-tier"></a>Flytta data till Arkiv nivån

Innan du börjar ska du kontrol lera att du har en Data Box Gateway enhet som körs. Följ stegen som beskrivs i [själv studie kursen: Förbered för att distribuera Azure Data Box Gateway](data-box-gateway-deploy-prep.md) och fortsätt gå vidare till nästa självstudie tills du har en fungerande enhet.

- Använd Data Box Gateway-enheten för att ladda upp data till Azure via den vanliga överförings proceduren enligt beskrivningen i [överför data via data Box Gateway](data-box-gateway-deploy-add-shares.md).
- När data har överförts måste du flytta den till Arkiv lag rings nivå. Du kan ställa in BLOB-nivån på två sätt: genom att använda ett Azure PowerShell-skript eller en princip för Azure Storage livs cykel hantering.  
    - Följ dessa [steg](../databox/data-box-how-to-set-data-tier.md#use-azure-powershell-to-set-the-blob-tier) om du vill flytta data till Arkiv nivån om du använder Azure PowerShell.
    - Om du använder Azures livs cykel hantering följer du dessa steg för att flytta data till Arkiv nivån.
        - [Registrera dig](../storage/blobs/storage-lifecycle-management-concepts.md) för för hands versionen av tjänsten BLOB Lifecycle Management för att använda Arkiv-nivån.
        - Använd följande princip för att [arkivera data vid inmatning](../storage/blobs/storage-lifecycle-management-concepts.md#archive-data-after-ingest).
- När Blobbarna har marker ATS som Arkiv kan de inte längre ändras av gatewayen om de inte flyttas till den frekventa eller kall nivån. Om filen finns i den lokala lagringen överförs inte alla ändringar som görs i den lokala kopian (inklusive borttagningar) till Arkiv nivån.
- Om du vill läsa data i Arkiv lag ring måste du extrahera data genom att ändra BLOB-nivån till frekvent eller låg frekvent. Att [Uppdatera resursen](data-box-gateway-manage-shares.md#refresh-shares) på gatewayen dehydratiserar inte blobben.

Mer information finns i Lär dig mer om hur du [hanterar Azure Blob Storage Lifecycle](../storage/blobs/storage-lifecycle-management-concepts.md).

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>Första Mass överföring följt av stegvis överföring

Använd Data Box-enhet och Data Box Gateway tillsammans när du vill göra en Mass uppladdning av en stor mängd data som följs av stegvisa överföringar. Använd Data Box-enhet för Mass överföring i ett offline-läge (första startvärdet) och Data Box Gateway för stegvisa överföringar (pågående matning) över nätverket.

### <a name="seed-the-data-with-data-box"></a>Dirigera data med Data Box-enhet

Följ dessa steg om du vill kopiera data till Data Box-enhet och ladda upp till Azure Storage.

1. [Beställ dina data Box-enhet](../databox/data-box-deploy-ordered.md).
2. [Konfigurera din data Box-enhet](../databox/data-box-deploy-set-up.md).
3. [Kopiera data till data Box-enhet via SMB](../databox/data-box-deploy-copy-data.md).
4. [Returnera data Box-enhet, verifiera data överföringen till Azure](../databox/data-box-deploy-picked-up.md).
5. När data överföringen till Azure har slutförts bör alla data finnas i Azure Storage-behållare. I lagrings kontot för Data Box-enhet går du till BLOB-behållaren (och filen) för att kontrol lera att alla data har kopierats. Anteckna behållar namnet eftersom du kommer att använda det här namnet senare. I följande skärm bild används till exempel `databox` container för den stegvisa överföringen.

    ![Container med data på Data Box-enhet](media/data-box-gateway-use-cases/data-container.png)

Den här Mass överföringen Slutför den inledande initierings fasen.

### <a name="ongoing-feed-with-data-box-gateway"></a>Pågående matning med Data Box Gateway

Följ de här stegen för pågående inhämtning av Data Box Gateway. 

1. Skapa en moln resurs på Data Box Gateway. Den här resursen överför automatiskt data till Azure Storage-kontot. Gå till **resurser** i data Box gateway resurs och klicka på **+ Lägg till resurs**.

    ![Klicka på + Lägg till resurs](media/data-box-gateway-use-cases/add-share.png)

2. Se till att den här resursen mappar till den behållare som innehåller de data som har dirigerats. För **Välj BLOB-behållare** väljer du **Använd befintlig** och bläddra till den behållare där data från data Box-enhet överfördes.

    ![Delnings inställningar](media/data-box-gateway-use-cases/share-settings-select-existing-container.png)

3. Uppdatera resursen när resursen har skapats. Den här åtgärden uppdaterar den lokala resursen med innehållet från Azure.

    ![Uppdatera resurs](media/data-box-gateway-use-cases/refresh-share.png)

    När resursen synkroniseras, kommer Data Box Gateway att överföra de stegvisa ändringarna om filerna har ändrats på klienten.

## <a name="next-steps"></a>Nästa steg

- Se [systemkraven för Data Box Gateway](data-box-gateway-system-requirements.md).
- Förstå [begränsningarna för Data Box Gateway](data-box-gateway-limits.md).
- Distribuera [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) i Azure-portalen.