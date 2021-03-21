---
title: Använd Azure Storage inventering för att hantera BLOB-data (för hands version)
description: Azure Storage Inventory är ett verktyg som hjälper dig att få en översikt över alla dina BLOB-data i ett lagrings konto.
services: storage
author: mhopkins-msft
ms.service: storage
ms.date: 03/05/2021
ms.topic: conceptual
ms.author: mhopkins
ms.reviewer: yzheng
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: a625ad780d01f3d19d26f2b9626ead3ae455b86b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102631480"
---
# <a name="use-azure-storage-blob-inventory-to-manage-blob-data-preview"></a>Använd Azure Storage BLOB Inventory för att hantera BLOB-data (för hands version)

Funktionen Azure Storage BLOB Inventory ger en översikt över dina BLOB-data i ett lagrings konto. Använd inventerings rapporten för att förstå din totala data storlek, ålder, krypterings status och så vidare. Rapporten ger en översikt över dina data för affärs-och efterlevnads krav. När den är aktive rad skapas en inventerings rapport automatiskt varje dag.

## <a name="availability"></a>Tillgänglighet

BLOB-inventering stöds för både generell användnings version 2 (GPv2) och Premium Block Blob Storage-konton. Den här funktionen stöds med eller utan funktionen [hierarkiskt namn område](data-lake-storage-namespace.md) aktive rad.

> [!IMPORTANT]
> Bob Inventory Inventory är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versioner av juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släpps till allmän tillgänglighet.

### <a name="preview-regions"></a>Förhandsgranska regioner

För hands versionen av BLOB Inventory är tillgänglig på lagrings konton i följande regioner:

- Frankrike, centrala
- Kanada, centrala
- Kanada, östra
- East US
- USA, östra 2

### <a name="pricing-and-billing"></a>Priser och fakturering

Avgiften för inventerings rapporter debiteras inte under för hands perioden. Priserna bestäms när den här funktionen är allmänt tillgänglig.

## <a name="enable-inventory-reports"></a>Aktivera inventerings rapporter

Aktivera BLOB Inventory-rapporter genom att lägga till en princip till ditt lagrings konto. Lägg till, redigera eller ta bort en princip med hjälp av [Azure Portal](https://portal.azure.com/).

1. Navigera till [Azure Portal](https://portal.azure.com/)
1. Välj ett av dina lagrings konton
1. Under **BLOB service** väljer du **BLOB-inventering**
1. Kontrol lera att **BLOB-inventering har Aktiver** ATS
1. Välj **Lägg till en regel**
1. Namnge din nya regel
1. Välj **BLOB-typer** för inventerings rapporten
1. Lägg till en prefix matchning för att filtrera inventerings rapporten
1. Välj om du vill **Inkludera BLOB-versioner** och **Inkludera ögonblicks bilder** i inventerings rapporten. Versioner och ögonblicks bilder måste vara aktiverade på kontot för att en ny regel ska kunna sparas med motsvarande alternativ aktiverat.
1. Välj **Spara**

:::image type="content" source="./media/blob-inventory/portal-blob-inventory.png" alt-text="Skärm bild som visar hur du lägger till en BLOB-inventerings regel med hjälp av Azure Portal":::

Inventerings principer läses eller skrivs fullständigt. Partiella uppdateringar stöds inte.

> [!IMPORTANT]
> Om du aktiverar brandväggsregler för ditt lagringskonto kan inventeringsbegäranden blockeras. Du kan avblockera dessa begäranden genom att tillhandahålla undantag för betrodda Microsoft-tjänster. Mer information finns i avsnittet undantag i [Konfigurera brand väggar och virtuella nätverk](../common/storage-network-security.md#exceptions).

En BLOB Inventory-körning schemaläggs automatiskt varje dag. Det kan ta upp till 24 timmar innan en inventerings körning har slutförts. En inventerings rapport konfigureras genom att lägga till en inventerings princip med en eller flera regler.

## <a name="inventory-policy"></a>Inventerings princip

En inventerings princip är en samling regler i ett JSON-dokument.

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition": {. . .}
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition": {. . .}
    }]
}
```

Visa JSON för en inventerings princip genom att välja fliken **kodvy** i avsnittet **BLOB inventory** i Azure Portal.

| Parameternamn | Parametertyp        | Kommentarer | Obligatoriskt? |
|----------------|-----------------------|-------|-----------|
| mål    | Sträng                | Mål behållaren där alla Inventory Files kommer att skapas. Mål containern måste redan finnas. | Ja |
| enabled        | Boolesk               | Används för att inaktivera hela principen. När det är inställt på **Sant** åsidosätter det aktiverade fältet för regel nivå den här parametern. När det är inaktiverat inaktive ras inventering för alla regler. | Ja |
| regler          | Matris med regel objekt | Minst en regel krävs i en princip. Det finns stöd för upp till 10 regler. | Ja |

## <a name="inventory-rules"></a>Inventerings regler

En regel samlar in filtrerings villkor och utdataparametrar för att generera en inventerings rapport. Varje regel skapar en inventerings rapport. Regler kan ha överlappande prefix. En BLOB kan visas i mer än en inventering, beroende på regel definitioner.

Varje regel i principen har flera parametrar:

| Parameternamn | Parametertyp                 | Kommentarer | Obligatoriskt? |
|----------------|--------------------------------|-------|-----------|
| name           | Sträng                         | Ett regel namn kan innehålla upp till 256 Skift läges känsliga alfanumeriska tecken. Namnet måste vara unikt inom en princip. | Ja |
| enabled        | Boolesk                        | En flagga som tillåter att en regel aktive ras eller inaktive ras. Standardvärdet är **True**. | Ja |
| definition     | Inventerings regel definition för JSON | Varje definition består av en regel filter uppsättning. | Ja |

Flaggan global **BLOB Inventory Enabled** har företräde framför den *aktiverade* parametern i en regel.

### <a name="rule-filters"></a>Regel filter

Det finns flera filter för att anpassa en BLOB Inventory-rapport:

| Filternamn         | Filtertyp                     | Kommentarer | Obligatoriskt? |
|---------------------|---------------------------------|-------|-----------|
| blobTypes           | Matris med fördefinierade uppräknings värden | Giltiga värden är `blockBlob` och `appendBlob` för hierarkiskt namn områdes aktiverade konton, och `blockBlob` ,, `appendBlob` och `pageBlob` för andra konton. | Ja |
| prefixMatch         | Matris med upp till 10 strängar för prefix som ska matchas. Ett prefix måste börja med ett behållar namn, till exempel "container1/foo" | Om du inte definierar *prefixMatch* eller anger ett tomt prefix gäller regeln för alla blobar i lagrings kontot. | Inga |
| includeSnapshots    | Boolesk                         | Anger om lagret ska innehålla ögonblicks bilder. Standardvärdet är **false**. | Inga |
| includeBlobVersions | Boolesk                         | Anger om inventeringen ska inkludera BLOB-versioner. Standardvärdet är **false**. | Inga |

Visa JSON för inventerings regler genom att välja fliken **kodvy** i avsnittet **BLOB inventory** i Azure Portal. Filter anges i en regel definition.

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["blockBlob", "appendBlob", "pageBlob"],
                "prefixMatch": ["inventorycontainer1", "inventorycontainer2/abcd", "etc"]
            }
        }
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["pageBlob"],
                "prefixMatch": ["inventorycontainer-disks-", "inventorycontainer4/"],
                "includeSnapshots": true,
                "includeBlobVersions": true
            }
        }
    }]
}
```

## <a name="inventory-output"></a>Inventerings utdata

Varje inventerings körning genererar en uppsättning CSV-formaterade filer i den angivna inventerings mål behållaren. Inventerings resultatet genereras under följande sökväg: `https://<accountName>.blob.core.windows.net/<inventory-destination-container>/YYYY/MM/DD/HH-MM-SS/` där:

- *accountName* är namnet på ditt Azure Blob Storage-konto
- *lager-mål-container* är mål behållaren som du angav i inventerings principen
- *Åååå/mm/dd/HH-mm-SS* är den tid då inventeringen började köras

### <a name="inventory-files"></a>Inventory Files

Varje inventerings körning genererar följande filer:

- **Lager-CSV-fil**: en fil med kommaavgränsade värden (CSV) för varje inventerings regel. Varje fil innehåller matchade objekt och deras metadata. Den första raden i varje CSV-formaterad fil är alltid schema raden. Följande bild visar en lager-CSV-fil som öppnats i Microsoft Excel.

   :::image type="content" source="./media/blob-inventory/csv-file-excel.png" alt-text="Skärm bild av en lager-CSV-fil som öppnats i Microsoft Excel":::

- **Manifest fil**: en manifest.jspå en fil som innehåller information om de inventerings filer som genererats för varje regel i som körs. Manifest filen fångar också den regel definition som tillhandahålls av användaren och sökvägen till inventeringen för den regeln.

- **Kontroll Summa fil**: en manifest. kontroll Summa fil som innehåller MD5-kontrollsumma för innehållet i manifest.jsi filen. Generering av manifest. kontroll Summa-filen markerar att en inventerings körning slutförts.

## <a name="inventory-completed-event"></a>Inventering slutförd händelse

Prenumerera på händelsen inventering slutförd för att få ett meddelande när inventerings körningen har slutförts. Den här händelsen genereras när manifest kontroll filen skapas. Händelsen inventerings slutfört inträffar även om inventerings körningen Miss lyckas med ett användar fel innan den börjar köras. Till exempel kommer en ogiltig princip eller en ogiltig mål behållare att utlösa händelsen. Händelsen är publicerad i avsnittet BLOB Inventory.

Exempel händelse:

```json
{
  "topic": "/subscriptions/3000151d-7a84-4120-b71c-336feab0b0f0/resourceGroups/BlobInventory/providers/Microsoft.EventGrid/topics/BlobInventoryTopic",
  "subject": "BlobDataManagement/BlobInventory",
  "eventType": "Microsoft.Storage.BlobInventoryPolicyCompleted",
  "id": "c99f7962-ef9d-403e-9522-dbe7443667fe",
  "data": {
    "scheduleDateTime": "2020-10-13T15:37:33Z",
    "accountName": "inventoryaccountname",
    "policyRunStatus": "Succeeded",
    "policyRunStatusMessage": "Inventory run succeeded, refer manifest file for inventory details.",
    "policyRunId": "b5e1d4cc-ee23-4ed5-b039-897376a84f79",
    "manifestBlobUrl": "https://inventoryaccountname.blob.core.windows.net/inventory-destination-container/2020/10/13/15-37-33/manifest.json"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-10-13T15:47:54Z"
}
```

## <a name="next-steps"></a>Nästa steg

- [Beräkna antal och Total storlek på blobbar per behållare](calculate-blob-count-size.md)
- [Hantera Azure Blob Storage-livscykeln](storage-lifecycle-management-concepts.md)
