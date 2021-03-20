---
title: Felsöka användardefinierade funktioner i Azure Stream Analytics
description: I den här artikeln beskrivs hur du felsöker användardefinierade funktioner i Azure Stream Analytics.
author: jenssuessmeyer
ms.author: jenss
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/24/2020
ms.custom: devx-track-js
ms.openlocfilehash: 7f5a24e4d351d7c133251900a80c70094b84cc4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019439"
---
# <a name="debug-user-defined-functions-in-azure-stream-analytics"></a>Felsöka användardefinierade funktioner i Azure Stream Analytics 

När användardefinierade funktioner (UDF) inte fungerar som förväntat, måste du felsöka dem för att hitta problemet. Du kan felsöka UDF: er för dina Stream Analytics jobb när du kör dina jobb lokalt med [Visual Studio Code](visual-studio-code-local-run-live-input.md) eller [Visual Studio](stream-analytics-vs-tools-local-run.md).

När du kör jobbet mot en Live-dataström lokalt, imiteras bara körningen av moln Azure Stream Analytics motorn på en nod. Det går inte att ersätta de prestanda-och skalbarhets test som du gör i molnet, men du sparar tid under funktionell testning genom att inte behöva skicka jobbet till molnet varje tillfälle som du vill testa. Dessutom inaktive ras tids principer för lokal körning med lokala eller exempel data, men tids principer stöds för testning av real tids data.

## <a name="pick-your-language"></a>Välj ditt språk

Du kan skriva UDF: er för Azure Stream Analytics med .NET (C#) eller Java Script. 

### <a name="functions-in-c"></a>Funktioner i C # 

När du [skriver .net-UDF: er med Visual Studio](stream-analytics-edge-csharp-udf-methods.md)får du samma support nivå som för alla .NET-klass projekt. Detta stöd omfattar:

* Stöd för kompilering, till exempel syntaxkontroll och kompilator stöd.

* Möjlighet att lägga till, bygga och referera till ett C#-projekt och artefakter i din Stream Analytics-lösning. 

* Enkel åter användning av kod inkapslade i ett delbar projekt. 

* Fel söknings stöd i Visual Studio direkt. Ange Stream Analytics projektet som start projekt och ange Bryt punkter i C#-kod. Tryck sedan på **F5** för att felsöka C#-koden på samma sätt som för andra C#-projekt. 

### <a name="functions-in-javascript"></a>Funktioner i Java Script

Java Script är ett annat alternativ för att skapa funktioner i Stream Analytics. JavaScript-koden placeras direkt i det Stream Analytics projektets funktions områden, vilket gör det svårare att dela projekt mellan projekt.

Kompileringen sker när det Stream Analytics projektet kompileras eller körs. Risken för att hitta ett problem vid körning är högre. Det finns inget stöd för fel sökning av JavaScript-funktioner i Stream Analytics direkt.

## <a name="debug-options-for-javascript"></a>Fel söknings alternativ för Java Script

Eftersom det inte finns något fel söknings stöd för JavaScript-funktioner i Stream Analytics direkt, kan du felsöka genom att kapsla in funktionen i en HTML-webbplats och hämta utdata därifrån.

I följande exempel visas hur du felsöker JavaScript-UDF: er med vissa begränsningar i en integrerad körnings miljö i [Visual Studio Code](quick-create-visual-studio-code.md).

### <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du se till att Azure Stream Analytics-projektet har följande objekt:

* Inmatade 
* Utdata 
* En Stream Analytics fråga (. asaql) 
* En Stream Analytics jobb konfiguration (JobConfig.jspå)
* En JavaScript UDF

### <a name="prepare-files"></a>Förbered filer

I följande bild innehåller fråge filen *. asaql* endast anropet till UDF-filen *fxcharCount*. Den här ändringen säkerställer att du fortfarande kan kompilera projektet när ändringar har gjorts.

> [!div class="mx-imgBorder"]
> ![Stream Analytics testfråga](./media/debug-user-defined-functions/asaql-file.png)

Skapa ytterligare en mapp i **tester** som är värd för test filen, som anropas för att köra testet med JavaScript-funktionen. I det här exemplet är namnet på mappen *fxcharCount* och namnet på testet är *Test_UDF.js*. 

Bilden nedan visar koden i test filen, som läser in funktions filen och kör funktionen. Det här exemplet är enkelt, men du kan läsa in ytterligare testdatafiler och loopa genom ytterligare tester för att hämta utdata. Funktions anropets notation skiljer sig något från de vanliga anropen eftersom filen refereras till och inte läses in i körningen, vilket gör det möjligt att felsöka. 

> [!div class="mx-imgBorder"]
> ![Stream Analytics test fil](./media/debug-user-defined-functions/test-file.png)

I funktionen lägger du till följande rader med kod till filen för att exponera metoderna. De påverkar inte möjligheten att kompilera koden i Visual Studio Code.

```javascript
var methods = {};
methods.fxchartCount = fxchartCount;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![Stream Analytics JavaScript UDF](./media/debug-user-defined-functions/udf-file.png)
  
## <a name="install-debug-support"></a>Installera stöd för fel sökning

För att felsöka måste du [Ladda ned](https://nodejs.org/en/download/) och installera **node.js**. Installera rätt version enligt den plattform som du använder. När du har installerat node.js runtime startar du om Visual Studio Code för att implementera ändringarna. 

Välj **Kör och Felsök** eller tryck på **CTRL + SHIFT + D** för att starta fel sökningen. En kombinations ruta visas där du kan välja **node.js** som körnings miljö. Om du bara har installerat node.js används den som standard. Du bör kunna stega igenom koden och till satellit filen om det behövs med F11. 

> [!div class="mx-imgBorder"]
> ![Stream Analytics köra och felsöka UDF](./media/debug-user-defined-functions/run-debug-udf.png)

### <a name="debug-user-defined-aggregates"></a>Felsöka användardefinierade agg regeringar 

Du kan använda fel söknings metoden för Java Script UDF: er för att felsöka användardefinierade agg regeringar (UDA). I det här exemplet läggs en UDA till i *. asaql* -mallfilen och test filen.

Som med UDF-filen innehåller du ett anrop till UDA för att se till att projektet kompileras efter att ändringarna har gjorts. 

> [!div class="mx-imgBorder"]
> ![Lägg till Uda i asaql](./media/debug-user-defined-functions/asaql-uda.png)

I *Test_UDA.js* -filen refererar du till Uda-filen som du gjorde med UDF. Du kan också anropa `main()` , `init()` och `accumulate()` . `accumulate()`Metoden anropas i en slinga för att ställa in värdena i tillstånds stacken. `computeresult()`Metoden anropas för att skapa den slutliga frågan. 

> [!div class="mx-imgBorder"]
> ![UDA test fil](./media/debug-user-defined-functions/uda-test.png)

Som i UDF-exemplet måste viss kod läggas till i själva UDA för att de ska kunna exponera de relevanta metoderna.

```javascript
var methods = {};
methods.main = main;
methods.init = main.init;
methods.accumulate = main.accumulate;
methods.computeResult = main.computeResult;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![Kod tillagt i UDA](./media/debug-user-defined-functions/uda-expose-methods.png)

Välj **Kör och Felsök** eller tryck på **CTRL + SHIFT + D** för att starta fel sökningen. En kombinations ruta visas där du kan välja **node.js** som körnings miljö. Om du bara har installerat node.js används den som standard. Du bör kunna stega igenom koden och till satellit filen om det behövs med F11.

> [!div class="mx-imgBorder"]
> ![Stream Analytics köra och felsöka Uda](./media/debug-user-defined-functions/run-debug-uda.png)


## <a name="next-steps"></a>Nästa steg

* [Utveckla och felsöka Azure Stream Analytics jobb lokalt](develop-locally.md)
* [Felsöka Azure Stream Analytics frågor lokalt med hjälp av jobb diagram i Visual Studio](debug-locally-using-job-diagram.md)
* [Användardefinierade funktioner i Azure Stream Analytics](functions-overview.md)
 
