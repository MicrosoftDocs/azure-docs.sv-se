---
title: Flytta en instans till en annan Azure-region
titleSuffix: Azure Digital Twins
description: Se hur du flyttar en Azure Digital Twins-instans från en Azure-region till en annan.
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: 62db56ac9791cea7d6f1a40f794241ed68fa90fa
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483589"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Flytta en Azure Digital Twins-instans till en annan Azure-region

Om du behöver flytta din Azure Digital Twins-instans från en region till en annan är den aktuella processen att skapa om dina resurser i den nya regionen och sedan ta bort de ursprungliga resurserna. I slutet av den här processen kommer du att arbeta med en ny Azure Digital Twins instans som är identisk med den första, förutom den uppdaterade platsen.

Den här artikeln innehåller råd om hur du gör en fullständig flytt och kopierar över allt du behöver för att den nya instansen ska matcha originalet.

Den här processen omfattar följande steg:

1. Förbered: Ladda ned dina ursprungliga modeller, tvillingar och diagram.
1. Flytta: Skapa en ny Azure Digital Twins instans i en ny region.
1. Flytta: Fylla i den nya Azure Digital Twins instansen på nytt.
    - Ladda upp de ursprungliga modellerna, tvillingarna och grafen.
    - Skapa slutpunkter och vägar på nytt.
    - Länka om anslutna resurser.
1. Rensa källresurser: Ta bort den ursprungliga instansen.

## <a name="prerequisites"></a>Förutsättningar

Innan du försöker skapa om din Azure Digital Twins-instans går du igenom komponenterna i den ursprungliga instansen för att få en tydlig uppfattning om alla delar som behöver skapas på nytt.

Här är några frågor att överväga:

* Vilka modeller *har laddats* upp till min instans? Hur många finns det?
* Vad är *tvillingarna i* min instans? Hur många finns det?
* Vilken är grafens allmänna form *i* min instans? Hur många relationer finns det?
* Vilka *slutpunkter* har jag i min instans?
* Vilka *vägar* har jag i min instans? Har de filter?
* Var ansluter min instans *till andra Azure-tjänster?* Några vanliga integreringspunkter är:

    - Azure Event Grid, Azure Event Hubs eller Azure Service Bus
    - Azure Functions
    - Azure Logic Apps
    - Azure Time Series Insights
    - Azure Maps
    - Azure IoT Hub Device Provisioning-tjänst
* Vilka andra *personliga appar eller företagsappar* har jag som ansluter till min instans?

Du kan samla in den här informationen med [hjälp av Azure Portal,](https://portal.azure.com)Azure Digital Twins API:er och [SDK:er,](how-to-use-apis-sdks.md) [Azure Digital Twins CLI-kommandon](how-to-use-cli.md)eller [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) exempel.

## <a name="prepare"></a>Förbereda

I det här avsnittet förbereder du att skapa instansen på nytt genom att ladda ned dina ursprungliga modeller, tvillingar och diagram från den ursprungliga instansen. Den här artikeln använder [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) för den här uppgiften.

>[!NOTE]
>Du kanske redan har filer som innehåller modellerna eller grafen i din instans. I så fall behöver du inte ladda ned allt igen – bara de delar som du saknar eller saker som kan ha ändrats sedan du ursprungligen laddade upp filerna. Du kan till exempel ha tvillingar som har uppdaterats med nya data.

### <a name="limitations-of-azure-digital-twins-explorer"></a>Begränsningar för Azure Digital Twins Explorer

Exemplet [Azure Digital Twins Explorer är ett](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) klientappexempel som stöder en visuell representation av grafen och ger visuell interaktion med din instans. Den här artikeln visar hur du använder den för att ladda ned och senare ladda upp dina modeller, tvillingar och grafer på nytt.

Det här exemplet är inte ett komplett verktyg. Det har inte stresstestat och har inte skapats för att hantera grafer av stor storlek. Tänk därför på följande out-of-the-box-exempelbegränsningar:

* Exemplet har för närvarande endast testats på grafstorlekar på upp till 1 000 noder och 2 000 relationer.
* Exemplet stöder inte återförsök vid tillfälliga fel.
* Exemplet meddelar inte nödvändigtvis användaren om data som laddats upp är ofullständiga.
* Exemplet hanterar inte fel som beror på mycket stora grafer som överskrider tillgängliga resurser, till exempel minne.

Om exemplet inte kan hantera grafens storlek kan du exportera och importera diagrammet med hjälp av andra Azure Digital Twins utvecklarverktyg:

* [Azure Digital Twins CLI-kommandon](how-to-use-cli.md)
* [Azure Digital Twins API:er och SDK:er](how-to-use-apis-sdks.md)

### <a name="set-up-the-azure-digital-twins-explorer-application"></a>Konfigurera Azure Digital Twins Explorer program

Om du vill Azure Digital Twins Explorer måste du först ladda ned exempelprogramkoden och konfigurera den så att den körs på datorn.

Hämta exemplet genom att gå till [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Välj knappen **Bläddra efter** kod under rubriken, vilket tar dig till GitHub-lagringsplatsen för exemplet. Välj knappen **Kod** och Ladda **ned ZIP för** att ladda ned exemplet som en *. ZIP-fil* till datorn.

:::image type="content" source="media/how-to-move-regions/download-repo-zip.png" alt-text="Skärmbild av lagringsplatsen digital-twins-explorer på GitHub. Knappen Kod är markerad och skapar en liten dialogruta där knappen Ladda ned ZIP är markerad." lightbox="media/how-to-move-regions/download-repo-zip.png":::

Packa upp filen.

Konfigurera sedan behörigheter för Azure Digital Twins Explorer. Följ anvisningarna i [avsnittet Konfigurera Azure Digital Twins och Azure Digital Twins Explorer](quickstart-azure-digital-twins-explorer.md#set-up-azure-digital-twins-and-azure-digital-twins-explorer) i Azure Digital Twins snabbstarten. I det här avsnittet går vi igenom följande steg:

1. Konfigurera en Azure Digital Twins instans. Du kan hoppa över den här delen eftersom du redan har en -instans.
1. Konfigurera lokala Azure-autentiseringsuppgifter för att ge åtkomst till din instans.
1. Kör Azure Digital Twins Explorer och konfigurera den för att ansluta till din instans. Du använder värdnamnet *för den* ursprungliga Azure Digital Twins som du flyttar.

Nu bör du ha Azure Digital Twins Explorer exempelappen som körs i en webbläsare på datorn. Exemplet ska vara anslutet till den ursprungliga Azure Digital Twins instansen.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="Webbläsarfönster som visar en app som körs på localhost:3000. Appen kallas för Azure Digital Twins Explorer och innehåller rutor för Frågeutforskaren, Modellvy, Diagramvy och Egenskapsutforskaren. Det finns inga data på skärmen ännu." lightbox="media/how-to-move-regions/explorer-blank.png":::

Kontrollera anslutningen genom att  välja knappen Kör fråga för att köra standardfrågan som visar alla tvillingar och relationer i diagrammet i **rutan GRAPH EXPLORER.**

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="En knapp som läser Kör fråga i det övre högra hörnet av fönstret är markerad." lightbox="media/how-to-move-regions/run-query.png":::

Du kan låta Azure Digital Twins Explorer köra eftersom du kommer att använda det igen senare i den här artikeln för att ladda upp objekten på nytt till den nya instansen i målregionen.

### <a name="download-models-twins-and-graph"></a>Ladda ned modeller, tvillingar och diagram

Ladda sedan ned modeller, tvillingar och grafer i lösningen till din dator.

Om du vill ladda ned alla dessa objekt samtidigt kontrollerar du först att hela diagrammet visas i **rutan GRAPH VIEW.** Om den fullständiga grafen inte redan visas kör du standardfrågan igen `SELECT * FROM digitaltwins` i **rutan QUERY EXPLORER.**
 
Välj sedan ikonen **Exportera graf** i **rutan DIAGRAMVY.**

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="I rutan Graph View (Diagramvy) är en ikon markerad. Den visar en pil som pekar nedåt från ett moln." lightbox="media/how-to-move-regions/export-graph.png":::

Den här åtgärden aktiverar en **nedladdningslänk** i **rutan GRAPH VIEW.** Välj den för att ladda ned en JSON-baserad representation av frågeresultatet, som innehåller dina modeller, tvillingar och relationer. Den här åtgärden bör ladda ned en .json-fil till datorn.

>[!NOTE]
>Om den nedladdade filen verkar ha ett annat filnamnstillägg kan du prova att redigera tillägget direkt och ändra det till .json.

## <a name="move"></a>Flytta

Därefter slutför du "flytten" av instansen genom att skapa en ny instans i målregionen. Sedan fyller du i den med data och komponenter från den ursprungliga instansen.

### <a name="create-a-new-instance"></a>Skapa en ny instans

Skapa först en ny instans av Azure Digital Twins i målregionen. Följ stegen i [Anvisningar: Konfigurera en instans och autentisering.](how-to-set-up-instance-portal.md) Tänk på följande:

* Du kan behålla samma namn för den nya *instansen om* den finns i en annan resursgrupp. Om du behöver använda samma resursgrupp som innehåller den ursprungliga instansen behöver den nya instansen ett eget distinkt namn.
* Ange den nya målregionen när du tillfrågas om en plats.

När det här steget är klart behöver du värdnamnet för den nya instansen för att fortsätta konfigurera den med dina data. Om du inte antecknade värdnamnet under installationen [](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) följer du de här instruktionerna för att hämta det nu från Azure Portal.

### <a name="repopulate-the-old-instance"></a>Fylla i den gamla instansen på nya sätt

Nu ska du konfigurera den nya instansen så att den är en kopia av originalet.

#### <a name="upload-the-original-models-twins-and-graph-by-using-azure-digital-twins-explorer"></a>Ladda upp de ursprungliga modellerna, tvillingarna och grafen med hjälp av Azure Digital Twins Explorer

I det här avsnittet kan du ladda upp modeller, tvillingar och grafer på nytt till den nya instansen. Om du inte har några modeller, tvillingar eller grafer i den ursprungliga instansen, eller om du inte vill flytta dem till den nya instansen, kan du gå vidare till [nästa avsnitt.](#re-create-endpoints-and-routes)

Annars går du tillbaka till webbläsarfönstret som kör Azure Digital Twins Explorer och följer dessa steg.

##### <a name="connect-to-the-new-instance"></a>Ansluta till den nya instansen

För närvarande Azure Digital Twins Explorer till din ursprungliga Azure Digital Twins instans. Växla anslutningen så att den pekar på den nya instansen genom **att** välja knappen Logga in i det övre högra hörnet i fönstret.

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="Azure Digital Twins Explorer att markera inloggningsikonen i det övre högra hörnet i fönstret. Ikonen visar en enkel bild av en person som är överlagrad med en försett med en nyckel." lightbox="media/how-to-move-regions/sign-in.png":::

Ersätt **ADT-URL:en** så att den återspeglar den nya instansen. Ändra det här värdet så att det *https://{nytt instansvärdnamn}*.

Välj **Anslut**. Du kan bli ombedd att logga in igen med dina Azure-autentiseringsuppgifter eller ge programmet tillstånd för din instans.

##### <a name="upload-models-twins-and-graph"></a>Ladda upp modeller, tvillingar och diagram

Ladda sedan upp lösningskomponenterna som du laddade ned tidigare till den nya instansen.

Om du vill ladda upp modeller, tvillingar och grafer väljer **du ikonen Importera** graf i rutan GRAPH **VIEW.** Det här alternativet laddar upp alla tre komponenterna samtidigt. Den laddar även upp modeller som för närvarande inte används i diagrammet.

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="I rutan Graph View (Diagramvy) är en ikon markerad. Den visar en pil som pekar på ett moln." lightbox="media/how-to-move-regions/import-graph.png":::

I rutan filväljare går du till den nedladdade grafen. Välj **.json-graffilen** och välj **Öppna**.

Efter några sekunder öppnar Azure Digital Twins Explorer en **importvy** som visar en förhandsgranskning av diagrammet som ska läsas in.

Bekräfta grafuppladdningen genom att välja **ikonen** Spara i det övre högra hörnet i **rutan GRAPH VIEW.**

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="Markera ikonen Spara i förhandsgranskningsfönstret i Graph." lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Azure Digital Twins Explorer nu dina modeller och diagram (inklusive tvillingar och relationer) till din nya Azure Digital Twins instans. Du bör se ett meddelande som visar hur många modeller, tvillingar och relationer som har laddats upp.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="Dialogruta som anger att grafimporten lyckades. Det står &quot;Importen lyckades. 2 modeller har importerats. 4 tvillingar har importerats. 2 relationer importerade.'" lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Kontrollera att allt har laddats  upp genom att välja knappen Kör fråga i **rutan GRAPH EXPLORER** för att köra standardfrågan som visar alla tvillingar och relationer i diagrammet. Den här åtgärden uppdaterar även listan över modeller i **rutan MODELLVY.**

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Markera runt knappen Kör fråga i det övre högra hörnet i fönstret." lightbox="media/how-to-move-regions/run-query.png":::

Du bör se grafen med alla dess tvillingar och relationer i rutan **GRAPH EXPLORER.** Du bör också se dina modeller i listan i **rutan MODELLVY.**

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="En vy Azure Digital Twins Explorer två modeller markerade i rutan Modellvy och ett diagram markerat i rutan Graph Explorer." lightbox="media/how-to-move-regions/post-upload.png":::

Dessa vyer bekräftar att dina modeller, tvillingar och diagram har laddats upp på nytt till den nya instansen i målregionen.

#### <a name="re-create-endpoints-and-routes"></a>Skapa slutpunkter och vägar på nytt

Om du har slutpunkter eller vägar i den ursprungliga instansen måste du skapa dem på nytt i den nya instansen. Om du inte har några slutpunkter eller vägar i den ursprungliga instansen eller om du inte vill flytta dem till den nya instansen kan du gå vidare till [nästa avsnitt.](#relink-connected-resources)

Annars följer du stegen i [Anvisningar: Hantera slutpunkter och vägar med den](how-to-manage-routes-portal.md) nya instansen. Tänk på följande:

* Du *behöver inte* skapa den resurs Event Grid, Event Hubs eller Service Bus som du använder för slutpunkten. Mer information finns i avsnittet "Förutsättningar" i anvisningarna för slutpunkten. Du behöver bara skapa slutpunkten på nytt på Azure Digital Twins instansen.
* Du kan återanvända slutpunkts- och vägnamn eftersom de är begränsade till en annan instans.
* Kom ihåg att lägga till eventuella filter som krävs för de vägar som du skapar.

#### <a name="relink-connected-resources"></a>Länka om anslutna resurser

Om du har andra appar eller Azure-resurser som är anslutna till din ursprungliga Azure Digital Twins-instans måste du redigera anslutningen så att de når den nya instansen i stället. Dessa resurser kan omfatta andra Azure-tjänster eller personliga appar eller företagsappar som du har skapat för att arbeta med Azure Digital Twins.

Om du inte har några andra resurser som är anslutna till den ursprungliga instansen eller om du inte vill flytta dem till den nya instansen kan du gå vidare till [nästa avsnitt.](#verify)

Annars bör du överväga de anslutna resurserna i ditt scenario. Du behöver inte ta bort och skapa några anslutna resurser på nytt. I stället behöver du bara redigera de punkter där de ansluter till en Azure Digital Twins-instans via dess värdnamn. Sedan uppdaterar du de här punkterna för att använda värdnamnet för den nya instansen i stället för den ursprungliga.

De exakta resurser som du behöver redigera beror på ditt scenario, men här är några vanliga integreringspunkter:

* Azure Functions. Om du har en Azure-funktion vars kod innehåller värdnamnet för den ursprungliga instansen bör du uppdatera det här värdet till den nya instansens värdnamn och publicera funktionen igen.
* Event Grid, Event Hubs eller Service Bus.
* Logic Apps.
* Time Series Insights.
* Azure Maps.
* IoT Hub Device Provisioning Service.
* Personliga appar eller företagsappar utanför Azure, till exempel klientappen som skapades i Självstudie: Koda en [klientapp](tutorial-code.md)som ansluter till instansen och anropar Azure Digital Twins API:er.
* Azure AD-appregistreringar behöver inte skapas igen.  Om du använder en [appregistrering för att](how-to-create-app-registration.md) ansluta till Azure Digital Twins API:er kan du återanvända samma appregistrering med din nya instans.

När du är klar med det här steget bör den nya instansen i målregionen vara en kopia av den ursprungliga instansen.

## <a name="verify"></a>Verifiera

Använd följande verktyg för att kontrollera att den nya instansen har ställts in korrekt:

* [Azure-portalen](https://portal.azure.com). Portalen är bra för att verifiera att den nya instansen finns och är i rätt målregion. Det är också bra för att verifiera slutpunkter, vägar och anslutningar till andra Azure-tjänster.
* [Azure Digital Twins CLI-kommandon](how-to-use-cli.md). De här kommandona är bra för att verifiera att den nya instansen finns och är i rätt målregion. De kan också användas för att verifiera instansdata.
* [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Azure Digital Twins Explorer är bra för att verifiera instansdata som modeller, tvillingar och grafer.
* [Azure Digital Twins API:er och SDK:er](how-to-use-apis-sdks.md). De här resurserna är bra för att verifiera instansdata som modeller, tvillingar och grafer. De är också bra för att verifiera slutpunkter och vägar.

Du kan också prova att köra anpassade appar eller flöden från slutet till slut som du körde med din ursprungliga instans för att kontrollera att de fungerar korrekt med den nya instansen.

## <a name="clean-up-source-resources"></a>Rensa källresurser

Nu när den nya instansen har ställts in i målregionen med en kopia av den ursprungliga instansens data och anslutningar kan du ta bort den ursprungliga instansen.

Du kan använda [Azure Portal,](https://portal.azure.com)Azure [CLI eller](how-to-use-cli.md) [kontrollplans-API:erna](how-to-use-apis-sdks.md#overview-control-plane-apis).

Om du vill ta bort instansen [](https://portal.azure.com) med hjälp av Azure Portal öppnar du portalen i ett webbläsarfönster och går till din ursprungliga Azure Digital Twins-instans genom att söka efter namnet i portalens sökfält.

Välj knappen **Ta** bort och följ anvisningarna för att slutföra borttagningen.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Vy över Azure Digital Twins instansinformation i Azure Portal på fliken Översikt. Knappen Ta bort är markerad.":::