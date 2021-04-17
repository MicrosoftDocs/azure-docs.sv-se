---
title: Ändringsflöde i Azure Blob Storage | Microsoft Docs
description: Lär dig mer om ändringsflödesloggar i Azure Blob Storage och hur du använder dem.
author: normesta
ms.author: normesta
ms.date: 02/08/2021
ms.topic: how-to
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 6da83ceb6d8ee51916d25949309d7ddfba0e4b30
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503613"
---
# <a name="change-feed-support-in-azure-blob-storage"></a>Stöd för ändringsflöde i Azure Blob Storage

Syftet med ändringsflödet är att tillhandahålla transaktionsloggar för alla ändringar som sker i blobarna och blobmetadata i ditt lagringskonto. Ändringsflödet tillhandahåller **sorterade,** **garanterade,** **varaktiga,** **oföränderliga,** **skrivskyddade** loggfiler för dessa ändringar. Klientprogram kan läsa loggarna när som helst, antingen i strömnings- eller batchläge. Med ändringsflödet kan du skapa effektiva och skalbara lösningar som bearbetar ändringshändelser som inträffar i ditt Blob Storage konto till en låg kostnad.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-the-change-feed-works"></a>Så här fungerar ändringsflödet

Ändringsflödet lagras som [blobar i](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) en särskild container i ditt lagringskonto till [standardkostnad för blobbar.](https://azure.microsoft.com/pricing/details/storage/blobs/) Du kan styra kvarhållningsperioden för dessa filer baserat på dina krav (se [villkoren för](#conditions) den aktuella versionen). Ändringshändelser läggs till i ändringsflödet som poster i [Apache Avro-formatspecifikationen:](https://avro.apache.org/docs/1.8.2/spec.html) ett kompakt, snabbt, binärt format som tillhandahåller omfattande datastrukturer med ett infogade schema. Det här formatet används ofta i Hadoop-ekosystemet, Stream Analytics och Azure Data Factory.

Du kan bearbeta loggarna asynkront, inkrementellt eller i sin helhet. Vilket antal klientprogram som helst kan läsa ändringsflödet separat parallellt och i sin egen takt. Analysprogram som [Apache Drill](https://drill.apache.org/docs/querying-avro-files/) eller [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) kan använda loggar direkt som Avro-filer, vilket gör att du kan bearbeta dem till en låg kostnad, med hög bandbredd och utan att behöva skriva ett anpassat program.

Följande diagram visar hur poster läggs till i ändringsflödet:

:::image type="content" source="media/storage-blob-change-feed/change-feed-diagram.png" alt-text="Diagram som visar hur ändringsflödet fungerar för att tillhandahålla en ordnad logg över ändringar i blobar":::

Stöd för ändringsflöde passar bra för scenarier som bearbetar data baserat på objekt som har ändrats. Program kan till exempel:

  - Uppdatera ett sekundärt index, synkronisera med ett cacheminne, sökmotor eller andra scenarier för innehållshantering.
  
  - Extrahera företagsanalysinsikter och mått baserat på ändringar som görs i dina objekt, antingen med direktuppspelning eller i ett gruppläge.
  
  - Lagra, granska och analysera ändringar i dina objekt under en viss tidsperiod, för att se säkerhet, efterlevnad eller information om företagets datahantering.

  - Skapa lösningar för säkerhetskopiering, spegling eller replikering av objekttillstånd i ditt konto för haveriberedskap eller efterlevnad.

  - Skapa anslutna programpipelines som reagerar på ändringshändelser eller schemakörningar baserat på skapade eller ändrade objekt.
  
Ändringsflöde är en [förutsättningsfunktion för](object-replication-overview.md) objektreplikering och återställning till tidpunkt för [blockblobar.](point-in-time-restore-overview.md)

> [!NOTE]
> Ändringsflödet ger en beständig, sorterad loggmodell för de ändringar som sker i en blob. Ändringar skrivs och görs tillgängliga i ändringsflödesloggen inom några minuter från ändringen. Om ditt program måste reagera på händelser mycket snabbare än så bör du överväga att använda [Blob Storage händelser](storage-blob-event-overview.md) i stället. [Blob Storage-händelser](storage-blob-event-overview.md) tillhandahåller realtidshändelser för en gång som gör att dina Azure Functions eller program snabbt kan reagera på ändringar som inträffar i en blob. 

## <a name="enable-and-disable-the-change-feed"></a>Aktivera och inaktivera ändringsflödet

Du måste aktivera ändringsflödet på ditt lagringskonto för att börja samla in och registrera ändringar. Inaktivera ändringsflödet för att sluta samla in ändringar. Du kan aktivera och inaktivera ändringar med hjälp Azure Resource Manager mallar i portalen eller PowerShell.

Här är några saker att tänka på när du aktiverar ändringsflödet.

- Det finns bara en ändringsfeed för blobtjänsten i varje lagringskonto och lagras i **$blobchangefeed containern.**

- Ändringarna Skapa, Uppdatera och Ta bort avbildas endast på blobtjänstnivå.

- Ändringsflödet samlar *in* alla ändringar för alla tillgängliga händelser som inträffar på kontot. Klientprogram kan filtrera bort händelsetyper efter behov. (Se villkoren [för](#conditions) den aktuella versionen).

- Endast GPv2- och Blob Storage-konton kan aktivera Ändringsflöde. Premium BlockBlobStorage-konton och hierarkiska namnrymdsaktiverade konton stöds inte för närvarande. GPv1-lagringskonton stöds inte men kan uppgraderas till GPv2 utan driftstopp. Mer information finns i Uppgradera till [ett GPv2-lagringskonto.](../common/storage-account-upgrade.md)

### <a name="portal"></a>[Portal](#tab/azure-portal)

Aktivera ändringsflöde på ditt lagringskonto med hjälp av Azure Portal:

1. I [Azure Portal](https://portal.azure.com/)du ditt lagringskonto.
1. Gå till **alternativet Dataskydd** under **Datahantering**.
1. Under **Spårning** väljer du **Aktivera blobändringsflöde**.
1. Välj knappen **Spara** för att bekräfta dina dataskyddsinställningar.

    :::image type="content" source="media/storage-blob-change-feed/change-feed-enable-portal.png" alt-text="Skärmbild som visar hur du aktiverar ändringsflöde i Azure Portal":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aktivera ändringsflöde med hjälp av PowerShell:

1. Installera den senaste versionen av PowershellGet.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Stäng och öppna sedan PowerShell-konsolen igen.

3. Installera version 2.5.0 eller senare av **Modulen Az.Storage.**

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 2.5.0 –AllowClobber –Force
   ```

4. Logga in på Din Azure-prenumeration med `Connect-AzAccount` kommandot och följ anvisningarna på skärmen för att autentisera.

   ```powershell
   Connect-AzAccount
   ```

5. Aktivera ändringsflöde för ditt lagringskonto.

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[Mall](#tab/template)
Använd en Azure Resource Manager för att aktivera ändringsflödet för ditt befintliga lagringskonto via Azure Portal:

1. I Azure Portal du Skapa **en resurs**.

2. I **Sök på Marketplace** skriver du **malldistribution och** trycker sedan på **RETUR.**

3. Välj **[Distribuera en anpassad mall](https://portal.azure.com/#create/Microsoft.Template)** och välj sedan Skapa en egen mall i **redigeraren**.

4. Klistra in följande json i mallredigeraren. Ersätt platshållaren `<accountName>` med namnet på ditt lagringskonto.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts/blobServices",
           "apiVersion": "2019-04-01",
           "name": "<accountName>/default",
           "properties": {
               "changeFeed": {
                   "enabled": true
               }
           } 
        }]
   }
   ```
    
5. Välj knappen **Spara,** ange resursgruppen för kontot och  välj sedan knappen Köp för att distribuera mallen och aktivera ändringsflödet.

---

## <a name="consume-the-change-feed"></a>Använda ändringsflödet

Ändringsflödet genererar flera metadata och loggfiler. De här filerna finns i **$blobchangefeed** containern för lagringskontot. 

> [!NOTE]
> I den aktuella versionen är $blobchangefeed containern endast synlig i Azure Portal men inte synlig i Azure Storage Explorer. Du kan för närvarande inte se $blobchangefeed-containern när du anropar ListContainers-API:et, men du kan anropa ListBlobs-API:et direkt i containern för att se blobarna

Dina klientprogram kan använda ändringsflödet med hjälp av blob-biblioteket för ändringsflödesprocessorn som medföljer SDK:n för ändringsflödesprocessorn. 

Se [Bearbeta ändringsflödesloggar i Azure Blob Storage](storage-blob-change-feed-how-to.md).

## <a name="understand-change-feed-organization"></a>Förstå ändringsflödesorganisationen

<a id="segment-index"></a>

### <a name="segments"></a>Segment

Ändringsflödet är en logg med ändringar som är ordnade **i** segment per *timme* men som läggs till i och uppdateras med några minuters varannan minut. Dessa segment skapas bara när det finns blobändringshändelser som inträffar under den timmen. Detta gör att klientprogrammet kan använda ändringar som inträffar inom specifika tidsintervall utan att behöva söka igenom hela loggen. Mer information finns i [Specifikationer.](#specifications)

Ett tillgängligt timsegment i ändringsflödet beskrivs i en manifestfil som anger sökvägarna till ändringsflödesfilerna för det segmentet. I listan över den `$blobchangefeed/idx/segments/` virtuella katalogen visas dessa segment sorterade efter tid. Sökvägen till segmentet beskriver starten av det timbaserade tidsintervall som segmentet representerar. Du kan använda listan för att filtrera bort de segment av loggar som är av intresse för dig.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json`skapas automatiskt när du aktiverar ändringsflödet. Du kan ignorera den här filen på ett säkert sätt. Det är en alltid tom initieringsfil. 

Segmentmanifestfilen ( `meta.json` ) visar sökvägen till ändringsflödesfilerna för det segmentet i `chunkFilePaths` egenskapen . Här är ett exempel på en segmentmanifestfil.

```json
{
    "version": 0,
    "begin": "2019-02-22T18:10:00.000Z",
    "intervalSecs": 3600,
    "status": "Finalized",
    "config": {
        "version": 0,
        "configVersionEtag": "0x8d698f0fba563db",
        "numShards": 2,
        "recordsFormat": "avro",
        "formatSchemaVersion": 1,
        "shardDistFnVersion": 1
    },
    "chunkFilePaths": [
        "$blobchangefeed/log/00/2019/02/22/1810/",
        "$blobchangefeed/log/01/2019/02/22/1810/"
    ],
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-22T18:11:01.187Z",
        "data": {
            "aid": "55e507bf-8006-0000-00d9-ca346706b70c"
        }
    }
}
```

> [!NOTE]
> Containern `$blobchangefeed` visas först när du har aktiverat funktionen för ändringsflöde på ditt konto. Du måste vänta några minuter efter att du har aktivera ändringsflödet innan du kan visa blobarna i containern. 

<a id="log-files"></a>

### <a name="change-event-records"></a>Ändra händelseposter

Ändringsflödesfilerna innehåller en serie ändringshändelseposter. Varje ändringshändelsepost motsvarar en ändring i en enskild blob. Posterna serialiseras och skrivs till filen med hjälp av [Apache Avro-formatspecifikationen.](https://avro.apache.org/docs/1.8.2/spec.html) Posterna kan läsas med hjälp av specifikationen för Avro-filformatet. Det finns flera bibliotek för bearbetning av filer i det formatet.

Ändringsflödesfiler lagras i `$blobchangefeed/log/` den virtuella katalogen som [tilläggsblobar](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs). Den första ändringsflödesfilen under varje `00000` sökväg har filnamnet (till exempel `00000.avro` ). Namnet på varje efterföljande loggfil som läggs till i sökvägen ökar med 1 (till exempel: `00001.avro` ).

Följande händelsetyper avbildas i ändringsflödesposterna:
- BlobCreated
- BlobDeleted
- BlobPropertiesUpdated
- BlobSnapshotCreated

Här är ett exempel på ändringshändelsepost från ändringsflödesfil som konverterats till Json.

```json
{
     "schemaVersion": 1,
     "topic": "/subscriptions/dd40261b-437d-43d0-86cf-ef222b78fd15/resourceGroups/sadodd/providers/Microsoft.Storage/storageAccounts/mytestaccount",
     "subject": "/blobServices/default/containers/mytestcontainer/blobs/mytestblob",
     "eventType": "BlobCreated",
     "eventTime": "2019-02-22T18:12:01.079Z",
     "id": "55e5531f-8006-0000-00da-ca3467000000",
     "data": {
         "api": "PutBlob",
         "clientRequestId": "edf598f4-e501-4750-a3ba-9752bb22df39",
         "requestId": "00000000-0000-0000-0000-000000000000",
         "etag": "0x8D698F13DCB47F6",
         "contentType": "application/octet-stream",
         "contentLength": 128,
         "blobType": "BlockBlob",
         "url": "",
         "sequencer": "000000000000000100000000000000060000000000006d8a",
         "storageDiagnostics": {
             "bid": "11cda41c-13d8-49c9-b7b6-bc55c41b3e75",
             "seq": "(6,5614,28042,28038)",
             "sid": "591651bd-8eb3-c864-1001-fcd187be3efd"
         }
  }
}
```

En beskrivning av varje egenskap finns i [Azure Event Grid händelseschema för Blob Storage](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties). Händelserna BlobPropertiesUpdated och BlobSnapshotCreated är för närvarande exklusiva för ändringsflödet och stöds inte än för Blob Storage händelser.

> [!NOTE]
> Ändringsflödesfilerna för ett segment visas inte omedelbart när ett segment har skapats. Fördröjningen ligger inom det normala publiceringssvarstiden för ändringsflödet, vilket är inom några minuter från ändringen.

<a id="specifications"></a>

## <a name="specifications"></a>Specifikationer

- Ändringshändelser läggs bara till i ändringsflödet. När de här posterna har lagts till är de oföränderliga och postpositionen är stabil. Klientprogram kan behålla sin egen kontrollpunkt på ändringsflödets läsposition.

- Ändringshändelseposter läggs till inom några minuter efter ändringen. Klientprogram kan välja att använda poster när de läggs till för strömmande åtkomst eller gruppindelade när som helst.

- Ändringshändelseposter sorteras efter ändringsordning **per blob**. Ordningen på ändringar mellan blobar är odefinierad i Azure Blob Storage. Alla ändringar i ett tidigare segment sker före eventuella ändringar i efterföljande segment.

- Ändringshändelseposter serialiseras i loggfilen med hjälp av [formatspecifikationen Apache Avro 1.8.2.](https://avro.apache.org/docs/1.8.2/spec.html)

- Ändringshändelseposter `eventType` där har värdet `Control` är interna systemposter och återspeglar inte en ändring av objekt i ditt konto. Du kan ignorera dessa poster på ett säkert sätt.

- Värdena i `storageDiagnostics` egenskapsdegenskapen är endast avsedda för internt bruk och är inte utformade för användning av ditt program. Dina program bör inte ha ett avtalsberoende av dessa data. Du kan ignorera dessa egenskaper på ett säkert sätt.

- Den tid som representeras av segmentet **är ungefärlig** med en gräns på 15 minuter. För att säkerställa förbrukningen av alla poster inom en angiven tid använder du därför segmentet föregående och nästa timme i följd.

- Varje segment kan ha olika antal på grund av intern partitionering av `chunkFilePaths` loggströmmen för att hantera publicering av dataflöde. Loggfilerna i var och en kommer garanterat att innehålla ömsesidigt uteslutande blobar och kan användas och bearbetas parallellt utan att ändra ordning per blob under `chunkFilePath` iterationen.

- Segmenten börjar i `Publishing` status. När du har slutfört att lägga till posterna i segmentet blir det `Finalized` . Loggfiler i alla segment som är daterade efter datumet för egenskapen `LastConsumable` i filen ska inte användas av ditt `$blobchangefeed/meta/Segments.json` program. Här är ett exempel på egenskapen `LastConsumable` i en `$blobchangefeed/meta/Segments.json` fil:

```json
{
    "version": 0,
    "lastConsumable": "2019-02-23T01:10:00.000Z",
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-23T02:24:00.556Z",
        "data": {
            "aid": "55e551e3-8006-0000-00da-ca346706bfe4",
            "lfz": "2019-02-22T19:10:00.000Z"
        }
    }
}

```

<a id="conditions"></a>

## <a name="conditions-and-known-issues"></a>Villkor och kända problem

I det här avsnittet beskrivs kända problem och villkor i den aktuella versionen av ändringsflödet. 

- Ändringshändelseposter för en enskild ändring kan förekomma mer än en gång i ändringsflödet.
- Du kan ännu inte hantera livslängden för loggfiler för ändringsflöde genom att ange en tidsbaserad bevarandeprincip för dem och du kan inte ta bort blobarna.
- Egenskapen `url` för loggfilen är för närvarande alltid tom.
- Egenskapen `LastConsumable` för den segments.jsfilen visar inte det första segmentet som ändringsflödet slutför. Det här problemet uppstår först efter att det första segmentet har åtgärdats. Alla efterföljande segment efter den första timmen avbildas korrekt i `LastConsumable` egenskapen .
- För närvarande kan du **inte $blobchangefeed** containern när du anropar API:et ListContainers och containern visas inte på Azure Portal eller Storage Explorer. Du kan visa innehållet genom att anropa ListBlobs-API:et på $blobchangefeed containern direkt.
- Lagringskonton som tidigare har initierat [en redundans](../common/storage-disaster-recovery-guidance.md) för ett konto kan ha problem med att loggfilen inte visas. Eventuella framtida redundanser kan också påverka loggfilen.

## <a name="faq"></a>Vanliga frågor

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>Vad är skillnaden mellan ändringsflöde och Lagringsanalys loggning?
Analysloggar innehåller poster över alla läs-, skriv-, list- och borttagningsåtgärder med lyckade och misslyckade begäranden för alla åtgärder. Analysloggar fungerar bra och ingen ordning garanteras.

Ändringsflödet är en lösning som tillhandahåller transaktionslogg med lyckade ändringar eller ändringar i ditt konto, till exempel skapande, ändring och borttagning av blobar. Ändringsflödet garanterar att alla händelser registreras och visas i ordningen för lyckade ändringar per blob, vilket innebär att du inte behöver filtrera bort brus från en stor mängd läsåtgärder eller misslyckade begäranden. Ändringsflödet är i grunden utformat och optimerat för programutveckling som kräver vissa garantier.

### <a name="should-i-use-change-feed-or-storage-events"></a>Ska jag använda ändringsflöde eller lagringshändelser?
Du kan använda båda funktionerna som Ändringsflöde och [Blob Storage-händelser](storage-blob-event-overview.md) ger samma information med samma garanti för leveranstillförlitlighet. Den största skillnaden är svarstid, ordning och lagring av händelseposter. Ändringsflödet publicerar poster i loggen inom några minuter från ändringen och garanterar även ordningen på ändringsåtgärder per blob. Lagringshändelser push-skickas i realtid och kanske inte ordnas. Ändringsflödeshändelser lagras durably i ditt lagringskonto som skrivskyddade stabila loggar med din egen definierade kvarhållning, medan lagringshändelser är tillfälliga att användas av händelsehanteraren om du inte uttryckligen lagrar dem. Med ändringsflödet kan val av antal program använda loggarna på egen hand med hjälp av blob-API:er eller -API:er. 

## <a name="next-steps"></a>Nästa steg

- Se ett exempel på hur du läser ändringsflödet med hjälp av ett .NET-klientprogram. Se [Bearbeta ändringsflödesloggar i Azure Blob Storage](storage-blob-change-feed-how-to.md).
- Lär dig mer om hur du reagerar på händelser i realtid. Mer [information finns i Reacting to Blob Storage events (Reagera på Blob Storage händelser)](storage-blob-event-overview.md)
- Läs mer om detaljerad loggningsinformation för både lyckade och misslyckade åtgärder för alla begäranden. Se [Azure Storage analytics-loggning](../common/storage-analytics-logging.md)
