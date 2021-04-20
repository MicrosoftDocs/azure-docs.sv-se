---
title: Utlösare och bindningar i Azure Functions
description: Lär dig hur du använder utlösare och bindningar för att ansluta din Azure-funktion till onlinehändelser och molnbaserade tjänster.
author: craigshoemaker
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 8648a52c58929983070b9a89c8fe946b89d37385
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739412"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Utlösare och bindningar i Azure Functions

I den här artikeln får du lära dig mer om de avancerade begreppen kring utlösare och bindningar för funktioner.

Utlösare är det som gör att en funktion körs. En utlösare definierar hur en funktion anropas och en funktion måste ha exakt en utlösare. Utlösare har associerade data, vilka vanligtvis är nyttolasten för funktionen. 

Bindning till en funktion är ett sätt att deklarativt ansluta en annan resurs till funktionen. bindningar kan anslutas som *indatabindningar,* *utdatabindningar* eller både och. Data från bindningar skickas som parametrar till funktionen.

Du kan blanda och matcha olika bindningar så att de passar dina behov. Bindningar är valfria och en funktion kan ha en eller flera indata- och/eller utdatabindningar.

Med utlösare och bindningar kan du undvika att hårdkoda åtkomsten till andra tjänster. Funktionen tar emot data (till exempel innehållet i ett kömeddelande) i funktionsparametrar. Du kan skicka data (t.ex. för att skapa ett kömeddelande) med hjälp av returvärdet för funktionen. 

Titta på följande exempel på hur du kan implementera olika funktioner.

| Exempelscenario | Utlösare | Indatabindning | Utdatabindning |
|-------------|---------|---------------|----------------|
| Ett nytt kömeddelande tas emot som kör en funktion för att skriva till en annan kö. | Kö<sup>*</sup> | *Ingen* | Kö<sup>*</sup> |
|Ett schemalagt jobb läser Blob Storage innehåll och skapar ett nytt Cosmos DB dokument. | Timer | Blob Storage | Cosmos DB |
|Den Event Grid används för att läsa en bild från Blob Storage och ett dokument från Cosmos DB skicka ett e-postmeddelande. | Event Grid | Blob Storage och Cosmos DB | SendGrid |
| En webhook som använder Microsoft Graph för att uppdatera ett Excel-blad. | HTTP | *Ingen* | Microsoft Graph |

<sup>\*</sup> Representerar olika köer

De här exemplen är inte avsedda att vara fullständiga, utan tillhandahålls för att illustrera hur du kan använda utlösare och bindningar tillsammans.

###  <a name="trigger-and-binding-definitions"></a>Utlösar- och bindningsdefinitioner

Utlösare och bindningar definieras olika beroende på utvecklingsspråk.

| Språk | Utlösare och bindningar konfigureras av... |
|-------------|--------------------------------------------|
| C#-klassbibliotek | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;methods and parameters med C#-attribut |
| Java | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;methods and parameters med Java-anteckningar  | 
| JavaScript/PowerShell/Python/TypeScript | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;uppdatera [function.jspå](./functions-reference.md) ([schema](http://json.schemastore.org/function)) |

För språk som är function.jspå tillhandahåller portalen ett användargränssnitt för att lägga till bindningar på **fliken** Integrering. Du kan också redigera filen direkt i portalen på **fliken Kod + test** för funktionen. Visual Studio Code kan du enkelt lägga till en [bindning function.jsen](functions-develop-vs-code.md?tabs=nodejs#add-a-function-to-your-project) fil genom att följa en lämplig uppsättning prompter. 

I .NET och Java definierar parametertypen datatypen för indata. Använd till exempel för att binda till texten i en köutlösare, en bytematris som ska läsas som binär och en anpassad typ för `string` att de-serialisera till ett objekt. Eftersom .NET-klassbiblioteksfunktioner och *Java-funktioner inte förlitar sig påfunction.jsför* bindningsdefinitioner kan de inte skapas och redigeras i portalen. Redigering i C#-portalen baseras på C#-skript, som *använderfunction.jspå* i stället för attribut.

Mer information om hur du lägger till bindningar till befintliga funktioner finns i [Ansluta funktioner till Azure-tjänster med hjälp av bindningar.](add-bindings-existing-function.md)

För språk som har dynamiskt typat, till exempel JavaScript, använder `dataType` du egenskapen ifunction.js *på* filen. Om du till exempel vill läsa innehållet i en HTTP-begäran i binärt format anger `dataType` du till `binary` :

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Andra alternativ för `dataType` är `stream` och `string` .

## <a name="binding-direction"></a>Bindningsriktning

Alla utlösare och bindningar har `direction` en egenskap ifunction.js[ på](./functions-reference.md) filen:

- För utlösare är riktningen alltid `in`
- Indata- och utdatabindningar `in` använder och `out`
- Vissa bindningar stöder en särskild `inout` riktning. Om du använder `inout` är endast **avancerad redigerare** tillgänglig via **fliken** Integrera i portalen.

När du använder [attribut i ett klassbibliotek](functions-dotnet-class-library.md) för att konfigurera utlösare och bindningar anges riktningen i en attributkonstruktor eller här härledas från parametertypen.

## <a name="add-bindings-to-a-function"></a>Lägga till bindningar i en funktion

Du kan ansluta din funktion till andra tjänster med hjälp av indata- eller utdatabindningar. Lägg till en bindning genom att lägga till dess specifika definitioner i funktionen. Mer information finns i Lägga [till bindningar till en befintlig funktion i Azure Functions](add-bindings-existing-function.md).  

## <a name="supported-bindings"></a>Bindningar som stöds

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Information om vilka bindningar som är i förhandsversion eller är godkända för produktionsanvändning finns i [Språk som stöds.](supported-languages.md)

## <a name="bindings-code-examples"></a>Kodexempel för bindningar

Använd följande tabell för att hitta exempel på specifika bindningstyper som visar hur du arbetar med bindningar i dina funktioner. Välj först den språkflik som motsvarar ditt projekt. 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="custom-bindings"></a>Anpassade bindningar

Du kan skapa anpassade indata- och utdatabindningar. Bindningar måste redigeras i .NET, men kan användas från alla språk som stöds. Mer information om hur du skapar anpassade bindningar finns i [Skapa anpassade indata- och utdatabindningar.](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings)

## <a name="resources"></a>Resurser
- [Bindningsuttryck och mönster](./functions-bindings-expressions-patterns.md)
- [Använda returvärdet för Azure-funktionen](./functions-bindings-return-value.md)
- [Registrera ett bindningsuttryck](./functions-bindings-register.md)
- Testning:
  - [Strategier för att testa din kod i Azure Functions](functions-test-a-function.md)
  - [Köra en funktion som inte utlösts av HTTP manuellt](functions-manually-run-non-http.md)
- [Hantera bindningsfel](./functions-bindings-errors.md)

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Registrera Azure Functions bindningstillägg](./functions-bindings-register.md)
