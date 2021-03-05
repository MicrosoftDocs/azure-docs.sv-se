---
title: Skapa händelsebaserade utlösare i Azure Data Factory
description: Lär dig hur du skapar en utlösare i Azure Data Factory som kör en pipeline som svar på en händelse.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 10/18/2018
ms.openlocfilehash: ff8c549f74b59706de5203f2d2e46867d6cb1d0a
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177799"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-storage-event"></a>Skapa en utlösare som kör en pipeline som svar på en lagrings händelse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs de utlösare för lagrings händelser som du kan skapa i Data Factory pipeliner.

Händelse driven arkitektur (EDA) är ett gemensamt data integrerings mönster som omfattar produktion, identifiering, konsumtion och reaktion på händelser. Data integrerings scenarier kräver ofta Data Factory kunder som utlöser pipelines baserat på händelser i lagrings kontot, till exempel när en fil tas emot eller tas bort i Azure Blob Storage-kontot. Data Factory integreras internt med [Azure Event Grid](https://azure.microsoft.com/services/event-grid/), vilket gör att du kan utlösa pipelines för sådana händelser.

En introduktion till tio minuter och demonstration av den här funktionen finns på följande video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]

> [!NOTE]
> Den integrering som beskrivs i den här artikeln är beroende av [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Se till att din prenumeration är registrerad hos Event Grid Resource Provider. Mer information finns i [resurs leverantörer och typer](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). Du måste kunna utföra åtgärden *Microsoft. EventGrid/eventSubscriptions/**. Den här åtgärden är en del av den inbyggda rollen EventGrid EventSubscription Contributor.

## <a name="data-factory-ui"></a>Data Factory-användargränssnitt

I det här avsnittet visas hur du skapar en utlösare för lagrings händelser i Azure Data Factory användar gränssnitt.

1. Växla till fliken **Redigera** , som visas med en Penn symbol. 

1. Välj **utlösare** på menyn och välj sedan **ny/redigera**. 

1. På sidan **Lägg till utlösare** väljer du **Välj utlösare...** och väljer sedan **+ ny**. 

1. Välj **lagrings händelse** för Utlös ande typ

    ![Skapa ny utlösare för lagrings händelse](media/how-to-create-event-trigger/event-based-trigger-image1.png)

1. Välj ditt lagrings konto i list rutan för Azure-prenumeration eller manuellt med resurs-ID för lagrings kontot. Välj den behållare som du vill att händelserna ska inträffa på. Val av behållare är valfritt, men mindful att välja alla behållare kan leda till ett stort antal händelser.

   > [!NOTE]
   > Utlösaren för lagrings händelser stöder för närvarande endast Azure Data Lake Storage Gen2 och generella version 2-lagrings konton. På grund av en Azure Event Grid begränsning stöder Azure Data Factory bara maximalt 500 lagrings händelse utlösare per lagrings konto.

   > [!NOTE]
   > Om du vill skapa och ändra en ny utlösare för lagrings händelser måste det Azure-konto som används för att logga in på Data Factory ha minst *ägar* behörighet för lagrings kontot. Ingen ytterligare behörighet krävs: tjänstens huvud namn för Azure Data Factory behöver _inte_ ha särskild behörighet till antingen lagrings kontot eller event Grid.

1. I **BLOB-sökvägen börjar med** och **BLOB-sökvägen slutar med** egenskaper kan du ange de behållare, mappar och blob-namn som du vill ta emot händelser för. Din utlösare för lagrings händelse kräver att minst en av dessa egenskaper definieras. Du kan använda olika mönster för båda **BLOB-sökvägen börjar med** och **BLOB-sökvägen slutar med** egenskaper, som du ser i exemplen senare i den här artikeln.

    * **BLOB-sökvägen börjar med:** Blobb Sök vägen måste börja med en mappsökväg. Giltiga värden är `2018/` och `2018/april/shoes.csv` . Det går inte att välja det här fältet om ingen behållare är markerad.
    * **BLOB-sökvägen slutar med:** BLOB-sökvägen måste sluta med ett fil namn eller fil namns tillägg. Giltiga värden är `shoes.csv` och `.csv` . Behållare och mappnamn är valfria, men om de anges måste de avgränsas med ett `/blobs/` segment. Till exempel kan en behållare med namnet "Orders" ha värdet `/orders/blobs/2018/april/shoes.csv` . Om du vill ange en mapp i en behållare utelämnar du det inledande "/"-tecken. Utlöser till exempel `april/shoes.csv` en händelse på en fil som heter `shoes.csv` i mapp a som kallas april i valfri behållare.
    * Obs: BLOB-sökvägen **börjar med** och **slutar med** är den enda mönster matchning som tillåts i utlösaren för lagrings händelser. Andra typer av matchning av jokertecken stöds inte för utlösnings typen.

1. Välj om utlösaren ska svara på en **blob som skapats** , en **BLOB borttagen** händelse eller både och. På den angivna lagrings platsen utlöser varje händelse de Data Factory pipelines som är associerade med utlösaren.

    ![Konfigurera lagrings händelse utlösaren](media/how-to-create-event-trigger/event-based-trigger-image2.png)

1. Välj om utlösaren ska ignorera blobbar med noll byte.

1. När du har konfigurerat du utlösaren klickar du på **Nästa: Förhandsgranska data**. Den här skärmen visar de befintliga blobbar som matchas av konfigurationen av din utlösare för lagrings händelser. Kontrol lera att du har specialfilter. Att konfigurera filter som är för breda kan matcha ett stort antal filer som skapas/raderas och kan påverka din kostnad avsevärt. När filter villkoren har verifierats klickar du på **Slutför**.

    ![Förhands granskning av data för Utlösar för lagring](media/how-to-create-event-trigger/event-based-trigger-image3.png)

1. Om du vill koppla en pipeline till den här utlösaren går du till pipeline-arbetsytan och klickar på **Lägg till utlösare** och väljer **ny/redigera**. När sido navigerings fältet visas, klickar du på list rutan **Välj utlösare...** och väljer den utlösare som du skapade. Klicka på **Nästa: Förhandsgranska data** för att bekräfta att konfigurationen är korrekt och klicka sedan på **Nästa** för att verifiera att förhands granskningen är korrekt.

1. Om din pipeline har parametrar, kan du ange dem i utlösaren kör parameter sidans navigerings fält. Utlösaren för lagrings händelser fångar in mappsökvägen och fil namnet för blobben i egenskaperna `@triggerBody().folderPath` och `@triggerBody().fileName` . Om du vill använda värdena för dessa egenskaper i en pipeline måste du mappa egenskaperna till pipeline-parametrar. När du har mappat egenskaperna till parametrar kan du komma åt de värden som samlas in av utlösaren genom `@pipeline().parameters.parameterName` uttrycket i hela pipelinen. Klicka på **Slutför** när du är klar.

    ![Mappa egenskaper till pipeline-parametrar](media/how-to-create-event-trigger/event-based-trigger-image4.png)

I föregående exempel är utlösaren konfigurerad att utlösa när en BLOB-sökväg slutar i. csv skapas i mappen Event-test i container-data. Egenskaperna **folderPath** och **filename** registrerar platsen för den nya blobben. Till exempel, när MoviesDB.csv läggs till i Sök vägs exemplet-data/Event-test, `@triggerBody().folderPath` har värdet `sample-data/event-testing` och `@triggerBody().fileName` har värdet `moviesDB.csv` . Dessa värden mappas i exemplet till pipeline-parametrarna `sourceFolder` och `sourceFile` kan användas i hela pipelinen som respektive `@pipeline().parameters.sourceFolder` `@pipeline().parameters.sourceFile` .

## <a name="json-schema"></a>JSON-schema

Följande tabell innehåller en översikt över de schema element som är relaterade till utlösare för lagrings händelser:

| **JSON-element** | **Beskrivning** | **Typ** | **Tillåtna värden** | **Obligatoriskt** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **utrymme** | Azure Resource Manager resurs-ID för lagrings kontot. | Sträng | Azure Resource Manager-ID | Ja |
| **planering** | Den typ av händelser som orsakar utlösaren att utlösa. | Matris    | Microsoft. Storage. BlobCreated, Microsoft. Storage. BlobDeleted | Ja, valfri kombination av dessa värden. |
| **blobPathBeginsWith** | BLOB-sökvägen måste börja med det mönster som tillhandahölls för utlösaren för att starta. Till exempel `/records/blobs/december/` utlöses utlösaren för blobbar i `december` mappen under `records` behållaren. | Sträng   | | Du måste ange ett värde för minst en av följande egenskaper: `blobPathBeginsWith` eller `blobPathEndsWith` . |
| **blobPathEndsWith** | BLOB-sökvägen måste sluta med det mönster som tillhandahölls för utlösaren för att starta. Till exempel `december/boxes.csv` utlöses endast utlösaren för blobbar som heter `boxes` i en `december` mapp. | Sträng   | | Du måste ange ett värde för minst en av följande egenskaper: `blobPathBeginsWith` eller `blobPathEndsWith` . |
| **ignoreEmptyBlobs** | Om blobar med noll byte ska utlösa en pipeline-körning. Som standard är detta inställt på sant. | Boolesk | sant eller falskt | Inga |

## <a name="examples-of-storage-event-triggers"></a>Exempel på utlösare för lagrings händelser

Det här avsnittet innehåller exempel på Inställningar för utlösare av lagrings händelser.

> [!IMPORTANT]
> Du måste inkludera `/blobs/` segmentets segment, som du ser i följande exempel när du anger behållare och mapp, behållare och fil, eller behållare, mapp och fil. För **blobPathBeginsWith** läggs Data Factory-gränssnittet automatiskt till `/blobs/` mellan mappen och container namnet i utlösaren JSON.

| Egenskap | Exempel | Beskrivning |
|---|---|---|
| **BLOB-sökvägen börjar med** | `/containername/` | Tar emot händelser för alla blobar i behållaren. |
| **BLOB-sökvägen börjar med** | `/containername/blobs/foldername/` | Tar emot händelser för alla blobbar i `containername` behållaren och `foldername` mappen. |
| **BLOB-sökvägen börjar med** | `/containername/blobs/foldername/subfoldername/` | Du kan också referera till en undermapp. |
| **BLOB-sökvägen börjar med** | `/containername/blobs/foldername/file.txt` | Tar emot händelser för en blob som heter `file.txt` i `foldername` mappen under `containername` behållaren. |
| **BLOB-sökvägen slutar med** | `file.txt` | Tar emot händelser för en blob med namnet `file.txt` i valfri sökväg. |
| **BLOB-sökvägen slutar med** | `/containername/blobs/file.txt` | Tar emot händelser för en blob med namnet `file.txt` under container `containername` . |
| **BLOB-sökvägen slutar med** | `foldername/file.txt` | Tar emot händelser för en blob med namnet `file.txt` i `foldername` mappen under vilken behållare som helst. |

## <a name="next-steps"></a>Nästa steg

* Detaljerad information om utlösare finns i [pipeline-körning och utlösare](concepts-pipeline-execution-triggers.md#trigger-execution).
* Lär dig hur du refererar till Utlös ande metadata i pipelinen finns i [referens utlösare metadata i pipeline-körningar](how-to-use-trigger-parameterization.md)
