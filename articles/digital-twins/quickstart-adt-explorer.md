---
title: Snabb start – utforska ett exempel scenario
titleSuffix: Azure Digital Twins
description: Snabb start – Använd exemplet Azure Digital flätat Explorer för att visualisera och utforska ett fördefinierat scenario.
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: c1c1b5f58aa0e760ff626af4607cf8e2d1e13910
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232429"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-azure-digital-twins-explorer"></a>Snabb start – utforska ett Azure-exempel med Azure Digitals dubblare med Azure Digitals Internet Explorer

Med Azure Digitals dubbla, kan du skapa och interagera med Live-modeller i dina verkliga miljöer. Först ska du modellera enskilda element som *digitala dubbla*. Sedan ansluter du dem till ett kunskaps *diagram* som kan svara på Live-händelser och frågas efter information.

I den här snabb starten ska du utforska ett fördefinierat Azure Digital-diagram med hjälp av ett exempel program som kallas [Azure Digitals flätade Utforskare](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Du kan använda Azure Digitals Internet Explorer för att:

- Ladda upp en digital representation av en miljö.
- Visa visuella bilder av de dubbla och grafer som skapas för att representera miljön i digitala Azure-nätverk.
- Utföra andra hanterings aktiviteter via en webbläsarbaserad, visuell upplevelse.

Snabb starten innehåller följande viktiga steg:

1. Konfigurera en digital Azure-instans och Azure Digitals-Utforskare.
1. Ladda upp fördefinierade modeller och diagram data för att skapa exempel scenariot.
1. Utforska scenario diagrammet som skapas.
1. Gör ändringar i grafen.

Det exempel diagram du arbetar med representerar en byggnad med två våningar och två rum. Diagrammet ser ut som den här bilden:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Vy över ett diagram av fyra cirkulära noder som är anslutna via pilar. En cirkel med etiketten &quot;Floor1&quot; är kopplad till en cirkel med etiketten &quot;Room1&quot;. En cirkel med etiketten &quot;Floor0&quot; är kopplad till en cirkel med etiketten &quot;Room0&quot;. ' Floor1 ' och ' Floor0 ' är inte anslutna.":::

## <a name="prerequisites"></a>Förutsättningar

Du behöver en Azure-prenumeration för att slutföra den här snabb starten. Om du inte redan har en, kan du [skapa ett kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nu.

Du behöver också **Node.js** på din dator. För att hämta den senaste versionen, se [Node.js](https://nodejs.org/).

Slutligen måste du hämta exemplet som ska användas under snabb starten. Exempel programmet är **Azure Digitals-Utforskaren**. Det här exemplet innehåller den app som du använder i snabb starten för att läsa in och utforska ett Azure Digital-scenario. Den innehåller också exempel scenariots filer. Hämta exemplet genom att gå till [Azure Digitals-Utforskaren](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Välj **Hämta zip** -knappen för att ladda ned en zip-fil av den här exempel koden till din dator. Zippa upp **Azure_Digital_Twins__ADT__explorer.zip** -mappen och extrahera filerna.

## <a name="set-up-azure-digital-twins-and-azure-digital-twins-explorer"></a>Konfigurera Azure Digitals, dubbla och Azure Digitals-Utforskare

Det första steget när du arbetar med Azure Digitals dubbla är att konfigurera en digital Azure-instans. När du har skapat en instans av tjänsten och konfigurerat dina autentiseringsuppgifter för att autentisera med Azure Digitals Internet Explorer kan du ansluta till instansen i Azure Digitals Internet Explorer och fylla den med exempel data senare i snabb starten.

Resten av det här avsnittet vägleder dig genom de här stegen.

### <a name="set-up-an-azure-digital-twins-instance"></a>Konfigurera en digital Azure-instans

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="set-up-local-azure-credentials"></a>Konfigurera lokala autentiseringsuppgifter för Azure

Azure Digitals Internet Explorer-appen använder [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (del av `Azure.Identity` biblioteket) för att autentisera användare med Azure Digitals-instansen när du kör den på din lokala dator. Mer information om olika sätt som en klient-app kan autentisera med Azure Digitals dubbla finns i [Write app authentication code](how-to-authenticate-client.md).

Med den här typen av autentisering söker Azure Digitals Internet Explorer efter autentiseringsuppgifter i din lokala miljö, t. ex. en Azure-inloggning i ett lokalt [Azure CLI](/cli/azure/install-azure-cli) eller i Visual Studio eller Visual Studio Code. Av den anledningen bör du *Logga in på Azure lokalt* via någon av dessa metoder för att ställa in autentiseringsuppgifter för appen Azure Digital-appens Utforskare.

Om du redan är inloggad på Azure på något av följande sätt kan du gå vidare till [Nästa avsnitt](#run-and-configure-azure-digital-twins-explorer).

Annars kan du installera den lokala Azure CLI med följande steg:

1. Följ processen på [den här installations länken](/cli/azure/install-azure-cli) för att slutföra installationen som matchar ditt operativ system.
1. Öppna ett konsol fönster på din dator.
1. Kör `az login` och följ de begärda autentiseringarna för att logga in på ditt Azure-konto.
1. Möjligt sista steget: om du använder flera Azure-prenumerationer under det här kontot anger du autentiserings kontexten till den Azure-prenumeration som innehåller din Azure Digital-instansen genom att köra `az account set --subscription "<your-subscription-name-or-ID>"` (antingen namnet eller ID-värdet för prenumerationen fungerar).

När du har loggat in ska Azure Digitals Internet Explorer automatiskt hämta dina Azure-autentiseringsuppgifter när du kör det i nästa avsnitt.

Du kan stänga fönstret för verifierings konsolen om du vill. Eller så kan du fortsätta vara öppen att använda i nästa steg.

### <a name="run-and-configure-azure-digital-twins-explorer"></a>Köra och konfigurera Azure Digitals Internet Explorer

Sedan kör du Azure Digitals program Utforskaren och konfigurerar den för din Azure Digital-instansen.

1. Gå till mappen hämtade och zippade **Azure_Digital_Twins__ADT__explorer** .
Öppna ett konsol fönster till mappens plats **Azure_Digital_Twins__ADT__explorer/client/src**.

1. Kör `npm install` för att ladda ned alla nödvändiga beroenden.

1. Starta appen genom att köra `npm run start` .

   Efter några sekunder öppnas ett webbläsarfönster och appen visas i webbläsaren.

   :::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="Webbläsarfönster som visar en app som körs på localhost: 3000. Appen kallas Azure Digital-Utforskare och innehåller rutor för Query Explorer, vyn modell, diagramvy och Property Explorer. Det finns inga skärm data ännu." lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

1. Välj knappen **Logga** in i det övre högra hörnet i fönstret, som du ser i följande bild, för att konfigurera Azure Digitals Internet Explorer så att den fungerar med den instans som du har konfigurerat.

   :::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="Azure Digitals flätade Utforskare markerar ikonen Logga in längst upp i fönstret. Ikonen visar en enkel Silhouette av en person som har en Silhouette av en nyckel." lightbox="media/quickstart-adt-explorer/sign-in.png":::

1. Ange den URL som du har samlat in tidigare i avsnittet [Konfigurera en digital Azure Digitals-instans](#set-up-an-azure-digital-twins-instance) i formatet *https://{instans värd namn}*.

> [!TIP]
> Om ett `SignalRService.subscribe` fel meddelande visas när du ansluter, se till att din Azure Digital-URL börjar med *https://*.

> [!TIP]
> Om ett autentiseringsfel visas kanske du vill kontrol lera miljövariablerna för att se till att alla autentiseringsuppgifter som ingår är giltiga för digitala Azure-nätverk. DefaultAzureCredential försöker autentisera sig mot [autentiseringstyper](/dotnet/api/overview/azure/identity-readme#defaultazurecredential) i en angiven ordning och miljövariablerna utvärderas först.

Om du ser en **behörighet som begärs** i popup-fönstret från Microsoft, bevilja du medgivande för det här programmet och accepterar att fortsätta.

>[!NOTE]
> Du kan gå tillbaka eller redigera informationen när som helst genom att välja samma ikon för att öppna **inloggnings** rutan igen. Den behåller de värden som du har skickat.

## <a name="add-the-sample-data"></a>Lägg till exempel data

Därefter importerar du exempel scenariot och grafen till Azure Digitals-Utforskaren. Exempel scenariot finns också i mappen **Azure_Digital_Twins__ADT__explorer** som du laddade ned tidigare.

### <a name="models"></a>Modeller

Det första steget i en digital Azure-lösning är att definiera ord listan för din miljö. Du skapar anpassade [modeller](concepts-models.md) som beskriver vilka typer av entiteter som finns i din miljö.

Varje modell skrivs på ett språk som JSON-LD som kallas digitalt DTDL (Digital Definition Language). Varje modell beskriver en enskild typ av entitet med avseende på dess *Egenskaper*, *telemetri*, *relationer* och *komponenter*. Senare använder du dessa modeller som grund för digitala dubbla, som representerar vissa instanser av dessa typer.

När du skapar en modell utför du normalt tre steg:

1. Skriv modell definitionen. I snabb starten har det här steget redan utförts som en del av exempel lösningen.
1. Verifiera den för att kontrol lera att syntaxen är korrekt. I snabb starten har det här steget redan utförts som en del av exempel lösningen.
1. Ladda upp den till din Azure Digital-instansen.
 
I den här snabb starten är modell filerna redan skrivna och verifierade åt dig. De ingår i den lösning som du har laddat ned. I det här avsnittet ska du ladda upp två fördefinierade modeller till din instans för att definiera dessa komponenter i en byggnads miljö:

* Floor
* Rummet

#### <a name="upload-models"></a>Ladda upp modeller

Följ de här stegen för att ladda upp modeller.

1. Välj ikonen **överför en modell** i rutan **modell vy** .

   :::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="I rutan modell visas den mittersta ikonen. Den visar en pil som pekar på ett moln." lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. I rutan fil väljare som visas går du till mappen **Azure_Digital_Twins__ADT__explorer/client/examples** i den hämtade lagrings platsen.
1. Välj **Room.jspå** och **Floor.jspå** och välj **OK**. Du kan ladda upp ytterligare modeller om du vill, men de används inte i den här snabb starten.
1. Följ dialog rutan popup där du uppmanas att logga in på ditt Azure-konto.

>[!NOTE]
>Om du ser följande fel meddelande: :::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="en popup-ruta som läser fel: fel vid hämtning av modeller: ClientAuthError: det gick inte att öppna popup-fönstret. Detta kan inträffa om du använder IE eller om popup-fönster blockeras i webbläsaren. &quot;med en stängnings knapp längst ned." border="false"::: 
> Försök inaktivera blockering av popup-fönster eller använda en annan webbläsare.

Azure Digitals sammanflätade Explorer överför nu dessa modell filer till din Azure Digital-instansen. De bör visas i rutan **modell** och visa sina egna namn och fullständiga modell-ID: n. Du kan välja **Visa modell** informations ikoner för att se DTDL-koden bakom dem.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="En vy över modell visnings rutan med två modell definitioner som anges inuti, våning (dtmi: exempel: golv; 1) och rum (dtmi: exempel: Room; 1). Ikonen Visa modell information som visar bokstaven &quot;i&quot; i en cirkel är markerad för varje modell." lightbox="media/quickstart-adt-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>Dubbla och det dubbla diagrammet

Nu när vissa modeller har laddats upp till din Azure Digital-instansen, kan du lägga till [digitala dubbla](concepts-twins-graph.md) , som följer modell definitionerna.

Digitala delar representerar faktiska entiteter i din affärs miljö. De kan vara sådant som sensorer i en Server grupp, lampor i en bil eller – i den här snabb starten – rum på en byggnads våning. Du kan skapa många multiplar av en bestämd modell typ, till exempel flera rum som alla använder *rums* modellen. Du kopplar dem till relationer i ett *dubbel diagram* som representerar hela miljön.

I det här avsnittet ska du ladda upp förskapade dubbla, som är anslutna till ett förskapat diagram. Grafen innehåller två golv och två rum, anslutna i följande layout:

* Floor0
    - Innehåller Room0
* Floor1
    - Innehåller Room1

#### <a name="import-the-graph"></a>Importera grafen

Följ dessa steg om du vill importera grafen.

1. I rutan **diagram** väljer du ikonen **Importera diagram** .

   :::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="I rutan diagram visas en ikon. Den visar en pil som pekar på ett moln." lightbox="media/quickstart-adt-explorer/import-graph.png":::

2. I rutan fil väljare går du till mappen **Azure_Digital_Twins__ADT__explorer/client/examples** och väljer kalkyl blads filen **buildingScenario.xlsx** . Den här filen innehåller en beskrivning av exempel diagrammet. Välj **OK**.

   Efter några sekunder öppnar Azure Digitals Explorer en **import** -vy som visar en för hands version av grafen som ska läsas in.

3. Om du vill bekräfta att diagrammet laddas upp väljer du ikonen **Spara** i det övre högra hörnet i rutan **diagramvy** .

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="Markera ikonen Spara i förhands gransknings fönstret för diagrammet." lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

4. Den överförda filen i Azure Digitals Internet Explorer använder nu den överförda filen för att skapa de begärda delningarna och relationerna. En dialog ruta visas när den är färdig. Välj **Stäng**.

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="En dialog ruta som visar att diagram importen lyckades. Den läser importen lyckades. 4 dubbla importer. 2 relationer har importer ATS. &quot;" lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

5. Grafen har nu laddats upp till Azure Digitals flätade Utforskare. Om du vill se grafen, väljer du knappen **Kör fråga** i rutan **diagram Utforskaren** , längst upp i fönstret Azure Digitals flätade Explorer.

   :::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="Knappen Kör fråga i det övre högra hörnet i fönstret är markerad." lightbox="media/quickstart-adt-explorer/run-query.png":::

Den här åtgärden kör standard frågan för att välja och Visa alla digitala dubbla. Azure Digitals sammanflätade Utforskare hämtar alla dubbla och relationer från tjänsten. Den ritar diagrammet som definieras av dem i rutan **diagram** .

## <a name="explore-the-graph"></a>Utforska grafen

Nu kan du se det överförda diagrammet i exempel scenariot.

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Vy av diagramvyn med en dubbel graf inuti. En cirkel med etiketten &quot;floor1&quot; är kopplad till en cirkel med etiketten &quot;contains&quot; till en cirkel med etiketten &quot;room1&quot;. En cirkel med etiketten &quot;floor0&quot; är kopplad till en cirkel med etiketten &quot;contains&quot; till en cirkel med etiketten &quot;room0&quot;.":::

Cirklarna (graf "Nodes") representerar digitala dubbla. Linjerna representerar relationer. **Floor0** -den dubbla innehåller **Room0**, och **Floor1** -den dubbla innehåller **Room1**.

Om du använder en mus kan du dra delar av grafen för att flytta runt dem.

### <a name="view-twin-properties"></a>Visa dubbla egenskaper

Du kan välja en fläta om du vill se en lista över dess egenskaper och deras värden i rutan **egenskaps Utforskaren** .

Här följer egenskaperna för Room0:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="Markera i rutan egenskaps Utforskare som visar egenskaper för Room0, vilket bland annat omfattar (bland annat) ett $dtId fält för Room0, ett temperatur fält på 70 och ett fuktighets fält på 30." lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room0 har en temperatur på 70.

Här följer egenskaperna för Room1:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="Markera i rutan egenskaps Utforskare som visar egenskaper för Room1, vilket bland annat omfattar (bland annat) ett $dtId fält för Room1, ett temperatur fält på 80 och ett fuktighets fält på 60." lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room1 har en temperatur på 80.

### <a name="query-the-graph"></a>Fråga i grafen

En huvud funktion i Azure Digitals flätas är möjligheten att [fråga](concepts-query-language.md) ditt dubbla diagram enkelt och effektivt att besvara frågor om din miljö.

Ett sätt att fråga de dubblarna i diagrammet är av deras *Egenskaper*. Frågor som baseras på egenskaper kan hjälpa dig att besvara en rad olika frågor. Du kan till exempel hitta extrem värden i din miljö som kan behöva åtgärdas.

I det här avsnittet ska du köra en fråga för att besvara frågan om hur många dubbla i din miljö som har en temperatur över 75.

Om du vill se svaret kör du följande fråga i rutan **fråga i Utforskaren** .

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="TemperatureQuery":::

Kom ihåg att visa de dubbla egenskaperna tidigare att Room0 har en temperatur på 70 och Room1 har en temperatur på 80. Därför visas endast Room1 i resultatet här.
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="Resultat för egenskaps frågan visar endast Room1." lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> Andra jämförelse operatorer (<, >, =, eller! =) stöds också i föregående fråga. Du kan försöka att ansluta de här operatörerna, olika värden eller andra dubbla egenskaper till frågan för att testa dina egna frågor.

## <a name="edit-data-in-the-graph"></a>Redigera data i grafen

Du kan använda Azure Digitals sammanflätade Utforskaren för att redigera egenskaperna för de dubbla som representeras i grafen. I det här avsnittet ska vi öka temperaturen på Room0 till 76.

Starta genom att välja **Room0** för att visa dess egenskaps lista i rutan **egenskaps Utforskaren** .

Egenskaperna i den här listan kan redige ras. Välj temperatur svärdet **70** för att aktivera ett nytt värde. Ange **76** och välj ikonen **Spara** för att uppdatera temperaturen till **76**.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="Rutan egenskaps Utforskaren visar egenskaper för Room0. Temperatur svärdet är en redigerings bar ruta som visar 76 och det finns en markering runt ikonen Spara." lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Nu visas ett fönster för **korrigerings information** där korrigerings koden visas i bakgrunden med Azure Digitals dubbla [API: er](how-to-use-apis-sdks.md) för att göra uppdateringen. Välj **Stäng**.

### <a name="query-to-see-the-result"></a>Fråga för att se resultatet

För att kontrol lera att grafen har registrerat din uppdatering på temperaturen för Room0 kör du frågan från tidigare för att hämta alla dubbla i miljön med en temperatur över 75.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="TemperatureQuery":::

Nu när temperaturen för Room0 har ändrats från 70 till 76, ska båda delarna visas i resultatet.

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="Resultat för egenskaps fråga som visar både Room0 och Room1." lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>Granska och sätta-information

I den här snabb starten skapade du en Azure Digital-instansen, anslöt den till Azure Digitals Internet Explorer och fyllde den med ett exempel scenario.

Du utforskar sedan diagrammet genom att:

* Använda en fråga för att besvara en fråga om scenariot.
* Redigera en egenskap på en digital, dubbel.
* Kör frågan igen för att se hur svaret ändrades till följd av din uppdatering.

Syftet med den här övningen är att demonstrera hur du kan använda Azure Digitals grafer för att besvara frågor om din miljö, även om miljön fortsätter att ändras.

I den här snabb starten gjorde du temperatur uppdateringen manuellt. Det är vanligt i Azure Digital-enheter för att ansluta digitala dubbla till riktiga IoT-enheter så att de får uppdateringar automatiskt, baserat på telemetridata. På så sätt kan du bygga en Live-graf som alltid återspeglar den faktiska situationen i din miljö. Du kan använda frågor för att få information om vad som händer i din miljö i real tid.

## <a name="clean-up-resources"></a>Rensa resurser

För att packa upp arbetet för den här snabb starten ska du först avsluta den aktiva konsolen. Den här åtgärden stänger av anslutningen till appen Azure Digitals-Utforskaren i webbläsaren. Du kommer inte längre att kunna visa real tids data i webbläsaren. Du kan stänga fliken webbläsare.

Sedan kan du välja vilka resurser du vill ta bort, beroende på vad du vill göra härnäst.

* **Om du planerar att fortsätta med självstudierna för Azure Digitals dubbla**, kan du återanvända instansen i den här snabb starten för dessa artiklar och du behöver inte ta bort den.

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Du kanske också vill ta bort projektmappen från den lokala datorn.

## <a name="next-steps"></a>Nästa steg

Fortsätt sedan till Azure Digitals-självstudierna för att bygga upp dina egna Azure Digital-dubblare scenario-och interaktions verktyg.

> [!div class="nextstepaction"]
> [Självstudie: koda en klient app](tutorial-code.md)
