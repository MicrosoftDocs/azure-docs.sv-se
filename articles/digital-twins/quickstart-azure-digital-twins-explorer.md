---
title: Snabbstart – Utforska ett exempelscenario
titleSuffix: Azure Digital Twins
description: Snabbstart – Använd Azure Digital Twins Explorer för att visualisera och utforska ett förbyggt scenario.
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: adafbdb903c9ba827e8ccf56890c69ee2ce4eaf5
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107495272"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-azure-digital-twins-explorer"></a>Snabbstart – Utforska ett exempel Azure Digital Twins scenario med hjälp av Azure Digital Twins Explorer

Med Azure Digital Twins kan du skapa och interagera med live-modeller av dina verkliga miljöer. Först modellerar du enskilda element som **digitala tvillingar.** Sedan ansluter du dem till ett **kunskapsdiagram** som kan svara på livehändelser och tillfrågas om information.

I den här snabbstarten utforskar du en förbyggd Azure Digital Twins graf med hjälp av ett exempelprogram som heter [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Du använder Azure Digital Twins Explorer för att:

- Ladda upp en digital representation av en miljö.
- Visa visuella bilder av tvillingarna och grafen som skapas för att representera miljön i Azure Digital Twins.
- Utföra andra hanteringsaktiviteter via en webbläsarbaserad, visuell upplevelse.

Snabbstarten innehåller följande viktiga steg:

1. Konfigurera en Azure Digital Twins instans och Azure Digital Twins Explorer.
1. Ladda upp fördefinierade modeller och grafdata för att konstruera exempelscenariot.
1. Utforska scenariodiagrammet som skapas.
1. Gör ändringar i diagrammet.

Exempeldiagrammet som du kommer att arbeta med representerar en byggnad med två våningar och två rum. Diagrammet ser ut som den här bilden:

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-view-full.png" alt-text="Vy av ett diagram som består av fyra cirkelformade noder som är anslutna med pilar. En cirkel med etiketten &quot;Floor1&quot; ansluts med en pil med etiketten &quot;contains&quot; till en cirkel med etiketten &quot;Room1&quot;. En cirkel med etiketten &quot;Floor0&quot; ansluts med en pil med etiketten &quot;contains&quot; till en cirkel med etiketten &quot;Room0&quot;. &quot;Floor1&quot; och &quot;Floor0&quot; är inte anslutna.":::

## <a name="prerequisites"></a>Förutsättningar

Du behöver en Azure-prenumeration för att slutföra den här snabbstarten. Om du inte redan har en kan du [skapa en kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nu.

Du måste också **Node.js** datorn. Information om hur du hämtar den senaste versionen finns [ iNode.js](https://nodejs.org/).

Slutligen måste du också ladda ned exemplet som ska användas under snabbstarten. Exempelprogrammet är **Azure Digital Twins Explorer**. Det här exemplet innehåller den app som du använder i snabbstarten för att läsa in och utforska ett Azure Digital Twins scenario. Den innehåller även exempelscenariofilerna. Hämta exemplet genom att gå till [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Välj knappen **Bläddra efter** kod under rubriken, vilket tar dig till GitHub-lagringsplatsen för exemplet. Välj knappen **Kod** och Ladda **ned ZIP för** att ladda ned exemplet som en *. ZIP-fil.* 

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/download-repo-zip.png" alt-text="Skärmbild av lagringsplatsen digital-twins-explorer på GitHub. Knappen Kod är markerad och skapar en liten dialogruta där knappen Ladda ned ZIP är markerad." lightbox="media/quickstart-azure-digital-twins-explorer/download-repo-zip.png":::

Packa upp **digital-twins-explorer-main.zip** mappen och extrahera filerna.

## <a name="set-up-azure-digital-twins-and-azure-digital-twins-explorer"></a>Konfigurera Azure Digital Twins och Azure Digital Twins Explorer

Det första steget i att arbeta Azure Digital Twins är att konfigurera en Azure Digital Twins instans. När du har skapat en instans av tjänsten och angett dina autentiseringsuppgifter för autentisering med Azure Digital Twins Explorer kan du ansluta till instansen i Azure Digital Twins Explorer och fylla den med exempeldata senare i snabbstarten.

Resten av det här avsnittet går igenom de här stegen.

### <a name="set-up-an-azure-digital-twins-instance"></a>Konfigurera en Azure Digital Twins instans

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="set-up-local-azure-credentials"></a>Konfigurera lokala Azure-autentiseringsuppgifter

Programmet Azure Digital Twins Explorer använder [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (en del av biblioteket) för att autentisera användare med Azure Digital Twins-instansen när du kör den på `Azure.Identity` den lokala datorn. Mer information om olika sätt som en klientapp kan autentisera med Azure Digital Twins finns i [Skriva kod för appautentisering.](how-to-authenticate-client.md)

Med den här typen av autentisering Azure Digital Twins Explorer du efter autentiseringsuppgifter i din lokala miljö, till exempel en Azure-inloggning i ett lokalt [Azure CLI](/cli/azure/install-azure-cli) eller i Visual Studio eller Visual Studio Code. Därför bör du logga **in på Azure lokalt med** någon av dessa metoder för att konfigurera autentiseringsuppgifter för Azure Digital Twins Explorer appen.

Om du redan har loggat in på Azure på något av följande sätt kan du gå vidare till [nästa avsnitt.](#run-and-configure-azure-digital-twins-explorer)

Annars kan du installera lokala Azure CLI med följande steg:

1. Följ installationslänken [för att](/cli/azure/install-azure-cli) slutföra installationen som matchar ditt operativsystem.
1. Öppna ett konsolfönster på datorn.
1. Kör `az login` och följ autentiseringsanvisningarna för att logga in på ditt Azure-konto.
1. Möjligt sista steg: Om du använder flera Azure-prenumerationer under det här kontot anger du autentiseringskontexten till den Azure-prenumeration som innehåller din Azure Digital Twins-instans genom att köra (antingen fungerar namn- eller `az account set --subscription "<your-subscription-name-or-ID>"` ID-värdet för prenumerationen).

När du har loggat in Azure Digital Twins Explorer hämta dina Azure-autentiseringsuppgifter automatiskt när du kör dem i nästa avsnitt.

Du kan stänga autentiseringskonsolfönstret om du vill. Eller så kan du hålla den öppen och använda den i nästa steg.

### <a name="run-and-configure-azure-digital-twins-explorer"></a>Köra och konfigurera Azure Digital Twins Explorer

Kör sedan programmet Azure Digital Twins Explorer och konfigurera det för din Azure Digital Twins instans.

1. Gå till den nedladdade och uppackade **mappen digital-twins-explorer-main.**
Öppna ett konsolfönster till mappen **digital-twins-explorer-main/client/src**.

1. Kör `npm install` för att ladda ned alla nödvändiga beroenden.

1. Starta appen genom att köra `npm run start` .

   Efter några sekunder öppnas ett webbläsarfönster och appen visas i webbläsaren.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/explorer-blank.png" alt-text="Webbläsarfönster som visar en app som körs på localhost:3000. Appen kallas för Azure Digital Twins Explorer innehåller rutor för Frågeutforskaren, Modellvy, Diagramvy och Egenskapsutforskaren. Det finns inga data på skärmen ännu." lightbox="media/quickstart-azure-digital-twins-explorer/explorer-blank.png":::

1. Välj knappen **Logga in** i det övre högra hörnet i fönstret, som du ser i följande bild, för att konfigurera Azure Digital Twins Explorer att fungera med den instans som du har konfigurerat.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/sign-in.png" alt-text="Azure Digital Twins Explorer att markera inloggningsikonen längst upp i fönstret. Ikonen visar en enkel bild av en person som har överlagrat en nyckel." lightbox="media/quickstart-azure-digital-twins-explorer/sign-in.png":::

1. Ange den Azure Digital Twins-instans-URL som du samlade in tidigare i avsnittet Konfigurera en [Azure Digital Twins-instans](#set-up-an-azure-digital-twins-instance) i formatet *https://<instance-host-name>*.

> [!TIP]
> Om ett `SignalRService.subscribe` felmeddelande visas när du ansluter kontrollerar du att url:en Azure Digital Twins börjar med *https://*.
>
> Om ett **autentiseringsfel** visas kan du kontrollera miljövariablerna för att kontrollera att alla autentiseringsuppgifter som ingår där är giltiga för Azure Digital Twins. Försöken `DefaultAzureCredential` att autentisera mot autentiseringstyper i en [viss ordning](/dotnet/api/overview/azure/identity-readme#defaultazurecredential), och miljövariablerna utvärderas först.

Om du ser **popup-fönstret Behörigheter** som begärts från Microsoft beviljar du medgivande för det här programmet och godkänner att det fortsätter.

>[!NOTE]
> Du kan gå tillbaka till eller redigera den här informationen när som helst genom att välja samma ikon för att **öppna rutan Logga in** igen. De värden som du skickade behålls.

## <a name="add-the-sample-data"></a>Lägga till exempeldata

Därefter importerar du exempelscenariot och grafen till Azure Digital Twins Explorer. Exempelscenariot finns också i mappen **digital-twins-explorer-main** som du laddade ned tidigare.

### <a name="models"></a>Modeller

Det första steget i en Azure Digital Twins lösning är att definiera vokabulären för din miljö. Du skapar anpassade modeller som [beskriver](concepts-models.md) de typer av entiteter som finns i din miljö.

Varje modell är skriven på ett språk som JSON-LD som kallas Digital Twin Definition Language (DTDL). Varje modell beskriver en enskild typ av entitet vad gäller **dess egenskaper,** **telemetri,** **relationer** och **komponenter**. Senare använder du dessa modeller som grund för digitala tvillingar som representerar specifika instanser av dessa typer.

När du skapar en modell utför du vanligtvis tre steg:

1. Skriv modelldefinitionen. I snabbstarten är det här steget redan gjort som en del av exempellösningen.
1. Verifiera den för att kontrollera att syntaxen är korrekt. I snabbstarten är det här steget redan gjort som en del av exempellösningen.
1. Ladda upp den till din Azure Digital Twins instans.
 
I den här snabbstarten är modellfilerna redan skrivna och verifierade åt dig. De ingår i den lösning som du laddade ned. I det här avsnittet laddar du upp två förskrivna modeller till din instans för att definiera dessa komponenter i en byggmiljö:

* Floor
* Rum

#### <a name="upload-models"></a>Ladda upp modeller

Följ de här stegen för att ladda upp modeller.

1. I rutan **MODELLVY** väljer du ikonen **Ladda upp en** modell.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/upload-model.png" alt-text="I rutan Modellvy är mellanikonen markerad. Den visar en pil som pekar på ett moln." lightbox="media/quickstart-azure-digital-twins-explorer/upload-model.png":::
 
1. I filväljarrutan som visas går du till mappen **digital-twins-explorer-main/client/examples** på den nedladdade lagringsplatsen.
1. Välj **Room.jspå** ochFloor.js **på** och välj **OK.** Du kan ladda upp ytterligare modeller om du vill, men de kommer inte att användas i den här snabbstarten.
1. Följ popup-dialogrutan där du uppmanas att logga in på ditt Azure-konto.

>[!NOTE]
>Om följande felmeddelande visas: En popup-ruta med meddelandet "Fel: Fel vid hämtning av :::image type="content" source="media/quickstart-azure-digital-twins-explorer/error-models-popup.png" alt-text="modeller: ClientAuthError: Fel vid öppning av popup-fönster. Detta kan inträffa om du använder Internet Explorer" border="false"::: eller om popup-fönster blockeras i webbläsaren." med knappen Stäng längst ned. 
> Prova att inaktivera blockeringen av popup-fönster eller använda en annan webbläsare.

Azure Digital Twins Explorer laddar nu upp dessa modellfiler till din Azure Digital Twins instans. De bör visas i rutan **MODELLVY** och visa sina egna namn och fullständiga modell-ID:er. Du kan välja **ikonerna visa** modellinformation för att se DTDL-koden bakom dem.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/model-info.png" alt-text="En vy av rutan Modellvy med två modelldefinitioner listade inuti, Floor (dtmi:example:Floor;1) och Room (dtmi:example:Room;1). Informationsikonen Visa modell med bokstaven &quot;i&quot; i en cirkel är markerad för varje modell." lightbox="media/quickstart-azure-digital-twins-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>Tvillingar och tvillingdiagrammet

Nu när vissa modeller har laddats upp till din Azure Digital Twins instans kan du lägga till [digitala tvillingar](concepts-twins-graph.md) som följer modelldefinitionerna.

Digitala tvillingar representerar de faktiska entiteterna i din företagsmiljö. De kan vara saker som sensorer på en farm, lampor i en bil eller – i den här snabbstarten – rum på en byggnad. Du kan skapa många tvillingar av en viss modelltyp, till exempel flera rum som alla använder *rumsmodellen.* Du ansluter dem med relationer till en **tvillinggraf** som representerar den fullständiga miljön.

I det här avsnittet laddar du upp förskapade tvillingar som är anslutna till en förskapad graf. Diagrammet innehåller två våningar och två rum som är anslutna i följande layout:

* Floor0
    - Innehåller Room0
* Floor1
    - Innehåller Room1

#### <a name="import-the-graph"></a>Importera diagrammet

Importera diagrammet genom att följa dessa steg.

1. I rutan **GRAPH VIEW** (DIAGRAMVY) väljer du **ikonen Importera** graf.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/import-graph.png" alt-text="I rutan Graph View (Diagramvy) är en ikon markerad. Den visar en pil som pekar på ett moln." lightbox="media/quickstart-azure-digital-twins-explorer/import-graph.png":::

2. I filväljarrutan går du till mappen **digital-twins-explorer-main/client/examples** och väljerbuildingScenario.xlsx kalkylbladsfilen. Den här filen innehåller en beskrivning av exempeldiagrammet. Välj **OK**.

   Efter några sekunder öppnar Azure Digital Twins Explorer en **importvy** som visar en förhandsgranskning av diagrammet som ska läsas in.

3. Bekräfta grafuppladdningen genom att välja **ikonen** Spara i det övre högra hörnet i **rutan GRAPH VIEW.**

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-preview-save.png" alt-text="Markera ikonen Spara i förhandsgranskningsfönstret i Graph." lightbox="media/quickstart-azure-digital-twins-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

4. Azure Digital Twins Explorer använder nu den uppladdade filen för att skapa de begärda tvillingarna och relationerna mellan dem. En dialogruta visas när den är klar. Välj **Stäng**.

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/import-success.png" alt-text="En dialogruta som anger att grafimporten lyckades. Det står &quot;Importen lyckades. Fyra importerade tvillingar. 2 relationer importerade.'" lightbox="media/quickstart-azure-digital-twins-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

5. Diagrammet har nu laddats upp till Azure Digital Twins Explorer. Om du vill se diagrammet väljer **du knappen Kör** fråga i rutan GRAPH **EXPLORER,** längst upp i Azure Digital Twins Explorer fönstret.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/run-query.png" alt-text="Knappen Kör fråga i det övre högra hörnet i fönstret är markerad." lightbox="media/quickstart-azure-digital-twins-explorer/run-query.png":::

Den här åtgärden kör standardfrågan för att välja och visa alla digitala tvillingar. Azure Digital Twins Explorer hämtar alla tvillingar och relationer från tjänsten. Den ritar grafen som definieras av dem i **rutan GRAPH VIEW.**

## <a name="explore-the-graph"></a>Utforska diagrammet

Nu kan du se det uppladdade diagrammet för exempelscenariot.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-view-full.png" alt-text="Vy av rutan Diagramvy med en tvillinggraf inuti. En cirkel med etiketten &quot;floor1&quot; ansluts med en pil med etiketten &quot;contains&quot; till en cirkel med etiketten &quot;room1&quot;. En cirkel med etiketten &quot;floor0&quot; ansluts med en pil med etiketten &quot;contains&quot; till en cirkel med etiketten &quot;room0&quot;.":::

Cirklarna (grafen "noder") representerar digitala tvillingar. Raderna representerar relationer. **Floor0-tvillingen** **innehåller Room0** och **Floor1-tvillingen** innehåller **Room1.**

Om du använder en mus kan du dra delar av grafen för att flytta runt dem.

### <a name="view-twin-properties"></a>Visa tvillingegenskaper

Du kan välja en tvilling för att se en lista över dess egenskaper och deras värden i **rutan PROPERTY EXPLORER.**

Här är egenskaperna för Room0:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/properties-room0.png" alt-text="Markera runt rutan Egenskapsutforskaren som visar egenskaper för Room0, som innehåller (bland annat) ett $dtId-fält i Fältet Rum0, ett temperaturfält på 70 och fältet Fuktighet på 30." lightbox="media/quickstart-azure-digital-twins-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Rum0 har en temperatur på 70.

Här är egenskaperna för Room1:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/properties-room1.png" alt-text="Markera runt rutan Egenskapsutforskaren som visar egenskaper för Room1, som innehåller (bland annat) ett $dtId-fält i Rum1, ett temperaturfält på 80 och fältet Fuktighet på 60." lightbox="media/quickstart-azure-digital-twins-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Rum1 har en temperatur på 80.

### <a name="query-the-graph"></a>Fråga i grafen

En viktig funktion i Azure Digital Twins är möjligheten att köra frågor [mot](concepts-query-language.md) tvillingdiagrammet enkelt och effektivt för att besvara frågor om din miljö.

Ett sätt att fråga tvillingarna i grafen är med deras **egenskaper**. Frågor som baseras på egenskaper kan hjälpa dig att besvara en mängd olika frågor. Du kan till exempel hitta extremvärden i din miljö som kan behöva uppmärksammas.

I det här avsnittet kör du en fråga för att besvara frågan om hur många tvillingar i din miljö som har en temperatur över 75.

Om du vill se svaret kör du följande fråga i **rutan QUERY EXPLORER.**

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="TemperatureQuery":::

Som du kanske minns från visningen av tvillingegenskaperna tidigare har Room0 en temperatur på 70 och Room1 har en temperatur på 80. Därför visas endast Room1 i resultatet här.
    
:::image type="content" source="media/quickstart-azure-digital-twins-explorer/result-query-property-before.png" alt-text="Resultat av egenskapsfråga som endast visar Room1." lightbox="media/quickstart-azure-digital-twins-explorer/result-query-property-before.png":::

>[!TIP]
> Andra jämförelseoperatorer (<,>, =, eller !=) stöds också i föregående fråga. Du kan prova att ansluta dessa operatorer, olika värden eller olika tvillingegenskaper till frågan för att prova att svara på dina egna frågor.

## <a name="edit-data-in-the-graph"></a>Redigera data i diagrammet

Du kan använda Azure Digital Twins Explorer för att redigera egenskaperna för de tvillingar som visas i diagrammet. I det här avsnittet höjer vi temperaturen för Rum0 till 76.

Starta genom att välja **Room0** för att visa dess egenskapslista i **rutan PROPERTY EXPLORER.**

Egenskaperna i den här listan kan redigeras. Välj temperaturvärdet **70 om du vill** ange ett nytt värde. Ange **76** och välj ikonen **Spara** för att uppdatera temperaturen till **76**.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/new-properties-room0.png" alt-text="Rutan Egenskapsutforskaren visar egenskaper för Room0. Temperaturvärdet är en redigerbar ruta som visar 76 och det finns en markering runt ikonen Spara." lightbox="media/quickstart-azure-digital-twins-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Nu visas ett fönster med **korrigeringsinformation** där korrigeringskoden visas som användes i bakgrunden med [Azure Digital Twins-API:er](how-to-use-apis-sdks.md) för att göra uppdateringen. Välj **Stäng**.

### <a name="query-to-see-the-result"></a>Fråga för att se resultatet

Kontrollera att grafen registrerade din uppdatering av temperaturen för Room0 genom att köra frågan igen från tidigare för att hämta alla tvillingar i miljön med en temperatur över 75.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="TemperatureQuery":::

Nu när temperaturen för Room0 har ändrats från 70 till 76 bör båda tvillingarna visas i resultatet.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/result-query-property-after.png" alt-text="Resultatet av egenskapsfrågan visar både Room0 och Room1." lightbox="media/quickstart-azure-digital-twins-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>Granska och kontextualisera inlärningar

I den här snabbstarten skapade du en Azure Digital Twins-instans, anslöt den till Azure Digital Twins Explorer och fylla i den med ett exempelscenario.

Sedan utforskade du diagrammet genom att:

* Använda en fråga för att besvara en fråga om scenariot.
* Redigera en egenskap på en digital tvilling.
* Kör frågan igen för att se hur svaret ändrades till följd av uppdateringen.

Syftet med den här övningen är att demonstrera hur du kan använda Azure Digital Twins för att besvara frågor om din miljö, även när miljön fortsätter att ändras.

I den här snabbstarten har du gjort temperaturuppdateringen manuellt. Det är vanligt i Azure Digital Twins att ansluta digitala tvillingar till verkliga IoT-enheter så att de får uppdateringar automatiskt baserat på telemetridata. På så sätt kan du skapa en live-graf som alltid återspeglar miljöns verkliga tillstånd. Du kan använda frågor för att få information om vad som händer i din miljö i realtid.

## <a name="clean-up-resources"></a>Rensa resurser

Avsluta konsolappen som körs för att avsluta arbetet med den här snabbstarten. Den här åtgärden stänger av anslutningen Azure Digital Twins Explorer appen i webbläsaren. Du kommer inte längre att kunna visa livedata i webbläsaren. Du kan stänga webbläsarfliken.

Sedan kan du välja vilka resurser du vill ta bort, beroende på vad du vill göra härnäst.

* **Om du planerar att fortsätta till Azure Digital Twins självstudier** kan du återanvända instansen i den här snabbstarten för dessa artiklar och du behöver inte ta bort den.

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Du kanske också vill ta bort projektmappen från den lokala datorn.

## <a name="next-steps"></a>Nästa steg

Fortsätt sedan till självstudierna om Azure Digital Twins att bygga ut dina egna Azure Digital Twins och interaktionsverktyg.

> [!div class="nextstepaction"]
> [Självstudie: Koda en klientapp](tutorial-code.md)
