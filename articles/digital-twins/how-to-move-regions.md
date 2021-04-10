---
title: Flytta instansen till en annan Azure-region
titleSuffix: Azure Digital Twins
description: Se hur du flyttar en digital Azure-instans från en Azure-region till en annan.
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: e268cca87479625af023b5970bb27c56721f6d39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102049856"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Flytta en digital Azure-instans till en annan Azure-region

Om du behöver flytta din Azure Digital-instansen från en region till en annan, är den aktuella processen att återskapa resurserna i den nya regionen och sedan ta bort de ursprungliga resurserna. I slutet av den här processen kommer du att arbeta med en ny Azure Digital-instans som är identisk med den första, förutom den uppdaterade platsen.

Den här artikeln innehåller rikt linjer för hur du utför en fullständig flytt och kopierar allt du behöver för att göra den nya instansen att matcha originalet.

Den här processen omfattar följande steg:

1. Förbered: Ladda ned dina ursprungliga modeller, garn och diagram.
1. Flytta: skapa en ny Azure Digitals-instans i en ny region.
1. Flytta: Fyll i den nya Azure Digital-instansen.
    - Ladda upp de ursprungliga modellerna, garnen och grafen.
    - Återskapa slut punkter och vägar.
    - Länka om anslutna resurser.
1. Rensa käll resurser: ta bort den ursprungliga instansen.

## <a name="prerequisites"></a>Förutsättningar

Innan du försöker återskapa din Azure Digital-instansen går du igenom komponenterna i den ursprungliga instansen för att få en tydlig uppfattning om alla delar som behöver skapas på nytt.

Här är några frågor att överväga:

* Vilka *modeller* överförs till min instans? Hur många finns det?
* Vilka är de *dubbla* i min instans? Hur många finns det?
* Vad är den allmänna formen i *grafen* i min instans? Hur många relationer finns det?
* Vilka *slut punkter* finns i min instans?
* Vilka *vägar* finns i min instans? Har de filter?
* Var ansluter min instans *till andra Azure-tjänster*? Några vanliga integrerings punkter är:

    - Azure Event Grid, Azure Event Hubs eller Azure Service Bus
    - Azure Functions
    - Azure Logic Apps
    - Azure Time Series Insights
    - Azure Maps
    - Azure IoT Hub Device Provisioning-tjänst
* Vilka andra *personliga appar eller företags program* har jag som ansluter till min instans?

Du kan samla in den här informationen med hjälp av exemplet [Azure Portal](https://portal.azure.com), [Azure Digitals dubbla API: er och SDK: er](how-to-use-apis-sdks.md), [Azure Digitals CLI-kommandon](how-to-use-cli.md)eller [Azures digitala sammanflätade Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) -exempel.

## <a name="prepare"></a>Förbereda

I det här avsnittet ska du förbereda för att återskapa instansen genom att ladda ned dina ursprungliga modeller, dubbla och grafer från den ursprungliga instansen. I den här artikeln används [Azure Digitals Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) -exemplet för den här uppgiften.

>[!NOTE]
>Du kanske redan har filer som innehåller modellerna eller grafen i din instans. I så fall behöver du inte hämta allting igen, bara de delar som du saknar eller saker som kan ha ändrats sedan du ursprungligen laddade upp filerna. Du kan till exempel ha dubbla som har uppdaterats med nya data.

### <a name="limitations-of-azure-digital-twins-explorer"></a>Begränsningar för Azure Digitals sammanflätade Utforskare

[Exempel på Azure Digitals Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) är ett exempel på en app för klient program som har stöd för visuell representation av grafen och ger visuell interaktion med din instans. Den här artikeln visar hur du kan använda den för att ladda ned och senare Ladda upp igen, modeller, dubbla och grafer.

Det här exemplet är inte ett komplett verktyg. Den har inte testats och har inte skapats för att hantera grafer med stor storlek. Tänk därför på följande exempel begränsningar som är färdiga:

* Exemplet har för närvarande endast testats i diagram storlekar upp till 1 000 noder och 2 000-relationer.
* Exemplet har inte stöd för att försöka igen om det uppstår tillfälliga haverier.
* Exemplet meddelar inte nödvändigt vis användaren om data som laddas upp är ofullständiga.
* Exemplet hanterar inte fel som orsakas av mycket stora grafer som överskrider tillgängliga resurser som minne.

Om det inte går att hantera diagrammets storlek, kan du exportera och importera grafen med hjälp av andra Azure Digitals sammanflätade utvecklingsverktyg:

* [Azure Digitals flätade CLI-kommandon](how-to-use-cli.md)
* [Azure Digitals dubbla API: er och SDK: er](how-to-use-apis-sdks.md)

### <a name="set-up-the-azure-digital-twins-explorer-application"></a>Konfigurera programmet Azure Digital, dubblare Explorer

Om du vill fortsätta med Azure Digitals Internet Explorer måste du först hämta exempel koden och konfigurera den så att den körs på din dator.

Information om hur du hämtar exemplet finns i [Azure Digitals-Utforskaren](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Välj **Hämta zip** -knappen för att ladda ned en. zip-fil av den här exempel koden till datorn som **Azure_Digital_Twins__ADT__explorer.zip**. Packa upp filen.

Sedan ställer du in och konfigurerar behörigheter för Azure Digitals sammanflätade Utforskare. Följ anvisningarna i avsnittet [Konfigurera digitala Azure-dubbla och Azure Digitals](quickstart-adt-explorer.md#set-up-azure-digital-twins-and-azure-digital-twins-explorer) -sektioner i snabb starten för Azure Digital. Det här avsnittet vägleder dig genom följande steg:

1. Konfigurera en digital Azure-instans. Du kan hoppa över den här delen eftersom du redan har en instans.
1. Konfigurera lokala Azure-autentiseringsuppgifter för att ge åtkomst till din instans.
1. Kör Azure Digitals Internet Explorer och konfigurera den för att ansluta till din instans. Du använder *värd namnet* för den ursprungliga Azure Digital-instansen som du flyttar.

Nu bör du ha exempel appen Azure Digitals Explorer som körs i en webbläsare på din dator. Exemplet bör vara anslutet till den ursprungliga Azure Digital-instansen.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="Webbläsarfönster som visar en app som körs på localhost: 3000. Appen kallas Azure Digital-Utforskare och innehåller rutor för Query Explorer, vyn modell, diagramvy och Property Explorer. Det finns inga skärm data ännu." lightbox="media/how-to-move-regions/explorer-blank.png":::

Om du vill kontrol lera anslutningen väljer du knappen **Kör fråga** för att köra standard frågan som visar alla dubbla och relationer i diagrammet i rutan **diagram Utforskaren** .

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="En knapp som läser kör fråga i det övre högra hörnet i fönstret är markerad." lightbox="media/how-to-move-regions/run-query.png":::

Du kan lämna Azure Digitals Internet Explorer igång eftersom du kommer att använda den senare i den här artikeln för att ladda upp dessa objekt till din nya instans i mål regionen.

### <a name="download-models-twins-and-graph"></a>Ladda ned modeller, dubbla och diagram

Sedan laddar du ned modeller, dubbla och grafer i din lösning till din dator.

Om du vill ladda ned alla dessa objekt samtidigt måste du först se till att hela grafen visas i **diagram** rutan. Om hela grafen inte redan visas kör du standard frågan i `SELECT * FROM digitaltwins` rutan fråga i **Utforskaren** igen.
 
Välj sedan ikonen **Exportera diagram** i rutan **diagram** .

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="I rutan diagram visas en ikon. Den visar en pil som pekar nedåt i ett moln." lightbox="media/how-to-move-regions/export-graph.png":::

Den här åtgärden aktiverar en **nedladdnings** länk i **diagramvyn** . Välj den för att ladda ned en JSON-baserad representation av frågeresultatet, som innehåller dina modeller, dubbla och relationer. Den här åtgärden ska ladda ned en. JSON-fil till din dator.

>[!NOTE]
>Om den hämtade filen verkar ha ett annat fil namns tillägg kan du försöka redigera tillägget direkt och ändra det till. JSON.

## <a name="move"></a>Flytta

Sedan slutför du "flytta"-instansen genom att skapa en ny instans i mål regionen. Sedan fyller du den med data och komponenter från din ursprungliga instans.

### <a name="create-a-new-instance"></a>Skapa en ny instans

Börja med att skapa en ny instans av Azure Digitals dubbla i mål regionen. Följ stegen i [instruktion: Konfigurera en instans och autentisering](how-to-set-up-instance-portal.md). Tänk på följande:

* Du kan behålla samma namn för den nya instansen *om* den finns i en annan resurs grupp. Om du behöver använda samma resurs grupp som innehåller den ursprungliga instansen måste den nya instansen ha sitt eget distinkta namn.
* Ange den nya mål regionen när du uppmanas att ange en plats.

När det här steget har slutförts behöver du värd namnet för den nya instansen för att fortsätta konfigurera den med dina data. Om du inte antecknade värd namnet under installationen följer du [dessa anvisningar](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) för att hämta det nu från Azure Portal.

### <a name="repopulate-the-old-instance"></a>Fyll i den gamla instansen

Härnäst ska du ställa in den nya instansen så att den är en kopia av originalet.

#### <a name="upload-the-original-models-twins-and-graph-by-using-azure-digital-twins-explorer"></a>Överför de ursprungliga modellerna, garnen och grafen med hjälp av Azure Digitals sammanflätade Utforskare

I det här avsnittet kan du överföra modeller, dubbla och grafer till den nya instansen. Om du inte har några modeller, dubbla eller grafer i din ursprungliga instans eller om du inte vill flytta dem till den nya instansen kan du gå vidare till [Nästa avsnitt](#re-create-endpoints-and-routes).

Annars går du tillbaka till webbläsarfönstret med Azure Digitals Internet Explorer och följer de här stegen.

##### <a name="connect-to-the-new-instance"></a>Anslut till den nya instansen

Azure Digitals Internet Explorer är för närvarande ansluten till den ursprungliga Azure Digital-instansen. Växla anslutningen så att den pekar på den nya instansen genom att välja knappen **Logga** in i det övre högra hörnet i fönstret.

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="Azure Digitals flätade Utforskare markerar ikonen Logga in i det övre högra hörnet i fönstret. Ikonen visar en enkel Silhouette av en person som har en Silhouette av en nyckel." lightbox="media/how-to-move-regions/sign-in.png":::

Ersätt **ADT-URL: en** för att avspegla din nya instans. Ändra det här värdet så att det läser *https://{New instance Host-namn}*.

Välj **Anslut**. Du kan uppmanas att logga in igen med dina Azure-autentiseringsuppgifter eller bevilja detta program medgivande för din instans.

##### <a name="upload-models-twins-and-graph"></a>Ladda upp modeller, garn och diagram

Ladda sedan upp lösnings komponenterna som du laddade ned tidigare till din nya instans.

Om du vill överföra modeller, flätade och diagram väljer du ikonen **Importera diagram** i rutan **diagram** . Med det här alternativet överförs alla tre komponenterna samtidigt. Den överför även modeller som inte används i diagrammet för närvarande.

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="I rutan diagram visas en ikon. Den visar en pil som pekar på ett moln." lightbox="media/how-to-move-regions/import-graph.png":::

I rutan fil väljare går du till den hämtade grafen. Välj filen Graph **. JSON** och välj **Öppna**.

Efter några sekunder öppnar Azure Digitals Explorer en **import** -vy som visar en för hands version av grafen som ska läsas in.

Om du vill bekräfta att diagrammet laddas upp väljer du ikonen **Spara** i det övre högra hörnet i rutan **diagramvy** .

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="Markera ikonen Spara i förhands gransknings fönstret för diagrammet." lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Azure Digitals sammanflätade Utforskare överför nu dina modeller och diagram (inklusive de dubbla och relationerna) till den nya Azure Digital-instansen. Du bör se ett meddelande som anger hur många modeller, dubbla och relationer som har överförts.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="Dialog rutan visar att grafen har importer klarat. Den läser importen lyckades. 2 importerade modeller. 4 dubbla importer. 2 relationer har importer ATS. &quot;" lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Om du vill kontrol lera att allt har överförts väljer du knappen **Kör fråga** i **Graph Explorer** -rutan för att köra standard frågan som visar alla dubbla och relationer i grafen. Den här åtgärden uppdaterar också listan över modeller i rutan **modell** .

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Markera knappen Kör fråga i det övre högra hörnet i fönstret." lightbox="media/how-to-move-regions/run-query.png":::

Du bör se grafen med alla dess sammanflätade och relationer i rutan **Graph Explorer** . Du bör också se vilka modeller som visas i rutan **modell** .

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="En vy över Azure Digitals Internet Explorer visar två modeller som är markerade i rutan modell och en graf som är markerad i rutan diagram Utforskaren." lightbox="media/how-to-move-regions/post-upload.png":::

De här vyerna bekräftar att dina modeller, dubbla och Graf-överförts till den nya instansen i mål regionen.

#### <a name="re-create-endpoints-and-routes"></a>Återskapa slut punkter och vägar

Om du har slut punkter eller vägar i den ursprungliga instansen måste du återskapa dem i den nya instansen. Om du inte har några slut punkter eller vägar i den ursprungliga instansen eller om du inte vill flytta dem till den nya instansen kan du gå vidare till [Nästa avsnitt](#relink-connected-resources).

Annars följer du stegen i [anvisningar: hantera slut punkter och vägar](how-to-manage-routes-portal.md) med den nya instansen. Tänk på följande:

* Du behöver *inte* återskapa den Event Grid, Event Hubs eller Service Bus resurs som du använder för slut punkten. Mer information finns i avsnittet "krav" i slut punkts anvisningarna. Du behöver bara återskapa slut punkten på Azure Digitals-instansen.
* Du kan återanvända slut punkts-och Dirigerings namn eftersom de är begränsade till en annan instans.
* Kom ihåg att lägga till eventuella nödvändiga filter till de vägar som du skapar.

#### <a name="relink-connected-resources"></a>Länka om anslutna resurser

Om du har andra appar eller Azure-resurser som är anslutna till den ursprungliga Azure Digital-instansen måste du redigera anslutningen så att de når den nya instansen i stället. De här resurserna kan omfatta andra Azure-tjänster eller personliga eller företags-appar som du har konfigurerat för att arbeta med Azure Digitals.

Om du inte har några andra resurser som är anslutna till den ursprungliga instansen eller om du inte vill flytta dem till den nya instansen kan du gå vidare till [Nästa avsnitt](#verify).

Annars kan du överväga de anslutna resurserna i ditt scenario. Du behöver inte ta bort och återskapa anslutna resurser. I stället behöver du bara redigera de punkter där de ansluter till en digital Azure-instans via dess värdnamn. Sedan uppdaterar du dessa punkter så att de använder värd namnet för den nya instansen i stället för den ursprungliga.

De exakta resurser som du behöver redigera beror på ditt scenario, men här är några vanliga integrerings punkter:

* Azure Functions. Om du har en Azure-funktion vars kod innehåller värd namnet för den ursprungliga instansen bör du uppdatera värdet till den nya instansens värdnamn och publicera om funktionen.
* Event Grid, Event Hubs eller Service Bus.
* Logic Apps.
* Time Series Insights.
* Azure Maps.
* IoT Hub Device Provisioning Service.
* Personliga appar eller företags program utanför Azure, t. ex. klient programmet som skapades i [Självstudier: kod a klient program](tutorial-code.md), som ansluter till instansen och anropar Azure Digitals dubbla API: er.
* Azure AD-App-registreringar behöver *inte* skapas på nytt. Om du använder en [app-registrering](how-to-create-app-registration.md) för att ansluta till Azures digitala dubbla API: er kan du återanvända samma app-registrering med den nya instansen.

När du har slutfört det här steget ska den nya instansen i mål regionen vara en kopia av den ursprungliga instansen.

## <a name="verify"></a>Verifiera

Kontrol lera att den nya instansen har ställts in korrekt genom att använda följande verktyg:

* [Azure-portalen](https://portal.azure.com). Portalen är lämplig för att verifiera att din nya instans finns och är i rätt mål region. Det är också lämpligt att verifiera slut punkter och vägar och anslutningar till andra Azure-tjänster.
* [Azure Digitals flätade CLI-kommandon](how-to-use-cli.md). De här kommandona är lämpliga för att verifiera att din nya instans finns och är i rätt mål region. De kan också användas för att verifiera instans data.
* [Azure Digitals sammanflätade Utforskare](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Azure Digitals Internet Explorer är lämpligt för att verifiera instans data som modeller, dubbla och grafer.
* [Azure Digitals dubbla API: er och SDK: er](how-to-use-apis-sdks.md). Dessa resurser är lämpliga för att verifiera instans data, t. ex. modeller, dubbla objekt och grafer. De är också användbara för att verifiera slut punkter och vägar.

Du kan också prova att köra anpassade appar eller slut punkt till slut punkt som du har kört med den ursprungliga instansen för att hjälpa dig att kontrol lera att de fungerar korrekt med den nya instansen.

## <a name="clean-up-source-resources"></a>Rensa käll resurser

Nu när den nya instansen har kon figurer ATS i mål regionen med en kopia av den ursprungliga instansens data och anslutningar kan du ta bort den ursprungliga instansen.

Du kan använda [Azure Portal](https://portal.azure.com), [Azure CLI](how-to-use-cli.md)eller [kontroll Plans-API: erna](how-to-use-apis-sdks.md#overview-control-plane-apis).

Om du vill ta bort instansen med hjälp av Azure Portal [öppnar du portalen](https://portal.azure.com) i ett webbläsarfönster och går till den ursprungliga Azure Digital-instansen genom att söka efter namnet i portalens Sök fält.

Klicka på knappen **ta bort** och följ anvisningarna för att slutföra borttagningen.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Vy över information om Azure Digitals sammanhållna instanser i Azure Portal på fliken Översikt. Knappen Ta bort är markerad.":::