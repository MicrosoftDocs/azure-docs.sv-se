---
title: Skapa händelsebaserade utlösare i Azure Data Factory
description: Lär dig hur du skapar en utlösare i Azure Data Factory som kör en pipeline som svar på en händelse.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 3021d049a38f1d883518fc7c45aa8ca0a906c2f7
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221593"
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

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image1.png" alt-text="Skärm bild av sidan författare för att skapa en ny utlösare för lagrings händelser i Data Factory UI.":::

1. Välj ditt lagrings konto i list rutan för Azure-prenumeration eller manuellt med resurs-ID för lagrings kontot. Välj den behållare som du vill att händelserna ska inträffa på. Val av behållare krävs, men mindful att välja alla behållare kan leda till ett stort antal händelser.

   > [!NOTE]
   > Utlösaren för lagrings händelser stöder för närvarande endast Azure Data Lake Storage Gen2 och generella version 2-lagrings konton. På grund av en Azure Event Grid begränsning stöder Azure Data Factory bara maximalt 500 lagrings händelse utlösare per lagrings konto. Om du når gränsen kontaktar du supporten för rekommendationer och ökar gränsen vid utvärdering av Event Grids teamet. 

   > [!NOTE]
   > Om du vill skapa en ny eller ändra en befintlig utlösare för lagrings händelser, måste Azure-kontot som används för att logga in på Data Factory och publicera lagrings händelse utlösaren ha rätt rollbaserad åtkomst kontroll (Azure RBAC)-behörighet för lagrings kontot. Ingen ytterligare behörighet krävs: tjänstens huvud namn för Azure Data Factory behöver _inte_ ha särskild behörighet till antingen lagrings kontot eller event Grid. Mer information om åtkomst kontroll finns i avsnittet [rollbaserad åtkomst kontroll](#role-based-access-control) .

1. I **BLOB-sökvägen börjar med** och **BLOB-sökvägen slutar med** egenskaper kan du ange de behållare, mappar och blob-namn som du vill ta emot händelser för. Din utlösare för lagrings händelse kräver att minst en av dessa egenskaper definieras. Du kan använda olika mönster för båda **BLOB-sökvägen börjar med** och **BLOB-sökvägen slutar med** egenskaper, som du ser i exemplen senare i den här artikeln.

    * **BLOB-sökvägen börjar med:** Blobb Sök vägen måste börja med en mappsökväg. Giltiga värden är `2018/` och `2018/april/shoes.csv` . Det går inte att välja det här fältet om ingen behållare är markerad.
    * **BLOB-sökvägen slutar med:** BLOB-sökvägen måste sluta med ett fil namn eller fil namns tillägg. Giltiga värden är `shoes.csv` och `.csv` . Behållare-och mappnamn som anges måste avgränsas med ett `/blobs/` segment. Till exempel kan en behållare med namnet "Orders" ha värdet `/orders/blobs/2018/april/shoes.csv` . Om du vill ange en mapp i en behållare utelämnar du det inledande "/"-tecken. Utlöser till exempel `april/shoes.csv` en händelse på en fil som heter `shoes.csv` i mapp a som kallas april i valfri behållare.
    * Observera att BLOB-sökvägen **börjar med** och **slutar med** är den enda mönster matchning som tillåts i utlösaren för lagrings händelser. Andra typer av matchning av jokertecken stöds inte för utlösnings typen.

1. Välj om utlösaren ska svara på en **blob som skapats** , en **BLOB borttagen** händelse eller både och. På den angivna lagrings platsen utlöser varje händelse de Data Factory pipelines som är associerade med utlösaren.

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image2.png" alt-text="Skärm bild av sidan för att skapa lagrings händelse utlösare.":::

1. Välj om utlösaren ignorerar blobbar med noll byte.

1. När du har konfigurerat du utlösaren klickar du på **Nästa: Förhandsgranska data**. Den här skärmen visar de befintliga blobbar som matchas av konfigurationen av din utlösare för lagrings händelser. Kontrol lera att du har specialfilter. Att konfigurera filter som är för breda kan matcha ett stort antal filer som skapas/raderas och kan påverka din kostnad avsevärt. När filter villkoren har verifierats klickar du på **Slutför**.

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image3.png" alt-text="Skärm bild av sidan för hands version av lagrings händelse utlösare":::

1. Om du vill koppla en pipeline till den här utlösaren går du till pipeline-arbetsytan och klickar på **Utlös** och väljer **ny/redigera**. När sido navigerings fältet visas, klickar du på list rutan **Välj utlösare...** och väljer den utlösare som du skapade. Klicka på **Nästa: Förhandsgranska data** för att bekräfta att konfigurationen är korrekt och klicka sedan på **Nästa** för att verifiera att förhands granskningen är korrekt.

1. Om din pipeline har parametrar, kan du ange dem i utlösaren kör parameter sidans navigerings fält. Utlösaren för lagrings händelser fångar in mappsökvägen och fil namnet för blobben i egenskaperna `@triggerBody().folderPath` och `@triggerBody().fileName` . Om du vill använda värdena för dessa egenskaper i en pipeline måste du mappa egenskaperna till pipeline-parametrar. När du har mappat egenskaperna till parametrar kan du komma åt de värden som samlas in av utlösaren genom `@pipeline().parameters.parameterName` uttrycket i hela pipelinen. Detaljerad förklaring finns i [referens utlösare metadata i pipelines](how-to-use-trigger-parameterization.md)

   :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image4.png" alt-text="Skärm bild av mappnings egenskaper för utlösare för lagrings händelse till pipeline-parametrar.":::

   I föregående exempel är utlösaren konfigurerad att utlösa när en BLOB-sökväg slutar i. csv skapas i mappen _Event-test_ i container _-data_. Egenskaperna **folderPath** och **filename** registrerar platsen för den nya blobben. Till exempel, när MoviesDB.csv läggs till i Sök vägs exemplet-data/Event-test, `@triggerBody().folderPath` har värdet `sample-data/event-testing` och `@triggerBody().fileName` har värdet `moviesDB.csv` . Dessa värden mappas i exemplet till pipeline-parametrarna `sourceFolder` och `sourceFile` , som kan användas i hela pipelinen som respektive `@pipeline().parameters.sourceFolder` `@pipeline().parameters.sourceFile` .

   > [!NOTE]
   > Om du skapar en pipeline och utlösare i [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md)måste du använda `@trigger().outputs.body.fileName` och `@trigger().outputs.body.folderPath` som parametrar. Dessa två egenskaper fångar BLOB-information. Använd dessa egenskaper i stället för att använda `@triggerBody().fileName` och `@triggerBody().folderPath` .

1. Klicka på **Slutför** när du är klar.

## <a name="json-schema"></a>JSON-schema

Följande tabell innehåller en översikt över de schema element som är relaterade till utlösare för lagrings händelser:

| **JSON-element** | **Beskrivning** | **Typ** | **Tillåtna värden** | **Obligatoriskt** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **utrymme** | Azure Resource Manager resurs-ID för lagrings kontot. | Sträng | Azure Resource Manager-ID | Ja |
| **planering** | Den typ av händelser som orsakar utlösaren att utlösa. | Matris    | Microsoft. Storage. BlobCreated, Microsoft. Storage. BlobDeleted | Ja, valfri kombination av dessa värden. |
| **blobPathBeginsWith** | BLOB-sökvägen måste börja med det mönster som tillhandahölls för utlösaren för att starta. Till exempel `/records/blobs/december/` utlöses utlösaren för blobbar i `december` mappen under `records` behållaren. | Sträng   | | Ange ett värde för minst en av följande egenskaper: `blobPathBeginsWith` eller `blobPathEndsWith` . |
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

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Azure Data Factory använder rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att säkerställa att obehörig åtkomst att lyssna på, prenumererar på uppdateringar från, och utlöser pipeliner som är kopplade till BLOB-händelser, är strikt förbjudna.

* För att kunna skapa en ny eller uppdatera en befintlig utlösare för lagrings händelser måste Azure-kontot som loggats in på Data Factory ha lämplig åtkomst till det aktuella lagrings kontot. Annars _nekas åtgärden med nekad åtkomst_.
* Data Factory behöver ingen särskild behörighet till din Event Grid och du behöver _inte_ tilldela särskild RBAC-behörighet för att Data Factory tjänstens huvud namn för åtgärden.

Följande RBAC-inställningar fungerar för utlösare för lagrings händelser:

* Ägar rollen till lagrings kontot
* Deltagar rollen till lagrings kontot
* _Microsoft. EventGrid/EventSubscriptions/Write-_ behörighet till lagrings konto _/Subscriptions/# # # #/resourceGroups/#_ # #/providers/Microsoft.Storage/storageAccounts/storageAccountName

För att förstå hur Azure Data Factory levererar det två löftet ska vi gå tillbaka ett steg och ta en förhandstitt bakom scenen. Här följer de övergripande arbets flödena för integration mellan Data Factory, lagring och Event Grid.

### <a name="create-a-new-storage-event-trigger"></a>Skapa en ny utlösare för lagrings händelser

Det här arbets flödet på hög nivå beskriver hur Azure Data Factory interagerar med Event Grid för att skapa en utlösare för lagrings händelser

:::image type="content" source="media/how-to-create-event-trigger/storage-event-trigger-5-create-subscription.png" alt-text="Arbets flöde för att skapa utlösare för lagrings händelser.":::

Två märkbara anrop från arbets flödena:

* Azure Data Factory får _ingen_ direkt kontakt med lagrings kontot. Begäran om att skapa en prenumeration är i stället vidarebefordrad och bearbetad av Event Grid. Därför behöver Data Factory saknar behörighet till lagrings kontot för det här steget.

* Åtkomst kontroll och behörighets kontroll sker på Azure Data Factory sida. Innan ADF skickar en begäran om att prenumerera på lagrings händelsen, kontrollerar den användaren behörigheten. Mer specifikt kontrollerar det om det Azure-konto som har loggat in och försöker skapa lagrings händelse utlösaren har lämplig åtkomst till det aktuella lagrings kontot. Om behörighets kontrollen Miss lyckas går det inte heller att skapa utlösare.

### <a name="storage-event-trigger-data-factory-pipeline-run"></a>Lagrings händelse utlösare Data Factory pipeline-körning

Det här arbets flödet för hög nivå beskriver hur utlösare i pipeline för lagrings händelser körs genom Event Grid

:::image type="content" source="media/how-to-create-event-trigger/storage-event-trigger-6-trigger-pipeline.png" alt-text="Arbets flödet för lagrings händelsen som utlöser pipelinen körs.":::

När den kommer till händelse som utlöser pipeline i Data Factory, tre märkbara uppmaningar i arbets flödet:

* Event Grid använder en push-modell som den vidarebefordrar meddelandet så snart som möjligt när lagringen släpper meddelandet i systemet. Detta skiljer sig från meddelande systemet, till exempel Kafka där ett pull-system används.
* Händelse utlösare på Azure Data Factory fungerar som en aktiv lyssnare till det inkommande meddelandet och utlöser den associerade pipelinen korrekt.
* Lagrings händelse utlösare gör ingen direkt kontakt med lagrings kontot

  * Om du har en kopia eller annan aktivitet i pipelinen för att bearbeta data i lagrings kontot, kommer Data Factory att göra direkt kontakt med lagringen med hjälp av autentiseringsuppgifterna som lagrats i den länkade tjänsten. Se till att den länkade tjänsten är korrekt konfigurerad
  * Men om du inte gör någon referens till lagrings kontot i pipelinen behöver du inte bevilja behörighet att Data Factory åtkomst till lagrings kontot

## <a name="next-steps"></a>Nästa steg

* Detaljerad information om utlösare finns i [pipeline-körning och utlösare](concepts-pipeline-execution-triggers.md#trigger-execution).
* Lär dig hur du refererar till Utlös ande metadata i pipelinen finns i [referens utlösare metadata i pipeline-körningar](how-to-use-trigger-parameterization.md)
