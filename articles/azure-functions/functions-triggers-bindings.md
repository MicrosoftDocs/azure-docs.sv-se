---
title: Utlösare och bindningar i Azure Functions
description: Lär dig att använda utlösare och bindningar för att ansluta din Azure-funktion till online-händelser och molnbaserade tjänster.
author: craigshoemaker
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 4cafe9af1eb5a765ab86bafb63cc9ab7d0889dc8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627607"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Utlösare och bindningar i Azure Functions

I den här artikeln lär du dig de övergripande koncepten som omger funktions utlösare och bindningar.

Utlösare är orsaken till att en funktion körs. En utlösare definierar hur en funktion anropas och en funktion måste ha exakt en utlösare. Utlösare har associerade data, vilka vanligtvis är nyttolasten för funktionen. 

Bindning till en funktion är ett sätt att på ett sätt ansluta en annan resurs till funktionen. bindningar kan vara anslutna som *indata-bindningar*, *utgående bindningar* eller både och. Data från bindningar skickas som parametrar till funktionen.

Du kan blanda och matcha olika bindningar så att de passar dina behov. Bindningar är valfria och en funktion kan ha en eller flera indata- och/eller utdatabindningar.

Med utlösare och bindningar kan du undvika hårdkoda åtkomst till andra tjänster. Funktionen tar emot data (till exempel innehållet i ett kömeddelande) i funktionsparametrar. Du kan skicka data (t.ex. för att skapa ett kömeddelande) med hjälp av returvärdet för funktionen. 

Tänk på följande exempel på hur du kan implementera olika funktioner.

| Exempelscenario | Utlösare | Binda in | Utgående bindning |
|-------------|---------|---------------|----------------|
| Ett nytt Queue-meddelande anländer som kör en funktion för att skriva till en annan kö. | Köjobb<sup>*</sup> | *Ingen* | Köjobb<sup>*</sup> |
|Ett schemalagt jobb läser Blob Storage innehåll och skapar ett nytt Cosmos DB-dokument. | Timer | Blob Storage | Cosmos DB |
|Event Grid används för att läsa en avbildning från Blob Storage och ett dokument från Cosmos DB för att skicka ett e-postmeddelande. | Event Grid | Blob Storage och Cosmos DB | SendGrid |
| En webhook som använder Microsoft Graph för att uppdatera ett Excel-blad. | HTTP | *Ingen* | Microsoft Graph |

<sup>\*</sup> Representerar olika köer

Dessa exempel är inte avsedda att vara uttömmande, men de tillhandahålls för att illustrera hur du kan använda utlösare och bindningar tillsammans.

###  <a name="trigger-and-binding-definitions"></a>Utlösare och bindnings definitioner

Utlösare och bindningar definieras på olika sätt beroende på utvecklings språket.

| Språk | Utlösare och bindningar konfigureras av... |
|-------------|--------------------------------------------|
| C#-klass bibliotek | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;metoder och parametrar för dekorera med C#-attribut |
| Java | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;metoder och parametrar för dekorera med Java-anteckningar  | 
| Java Script/PowerShell/python/TypeScript | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;uppdatera [function.jspå](./functions-reference.md) ([schema](http://json.schemastore.org/function)) |

För språk som förlitar sig på function.jspå, ger portalen ett användar gränssnitt för att lägga till bindningar på fliken **integration** . Du kan också redigera filen direkt i portalen på fliken **kod + test** i din funktion. Med Visual Studio Code kan du enkelt [lägga till en bindning till en function.jspå en fil](functions-develop-vs-code.md?tabs=nodejs#add-a-function-to-your-project) genom att följa en behändig uppsättning prompter. 

I .NET och Java definierar parameter typen data typen för indata. Använd till exempel för `string` att binda till texten i en Queue-utlösare, en byte mat ris som ska läsas som binär och en anpassad typ för att deserialisera till ett objekt. Eftersom funktioner i .NET-klass bibliotek och Java-funktioner inte förlitar sig på *function.js* för bindnings definitioner, kan de inte skapas och redige ras i portalen. C#-portalen redigerar baseras på C#-skript, som använder *function.jsi* stället för attribut.

Mer information om hur du lägger till bindningar i befintliga funktioner finns i [ansluta funktioner till Azure-tjänster med hjälp av bindningar](add-bindings-existing-function.md).

För språk som är dynamiskt skrivna, till exempel Java Script, använder du `dataType` egenskapen i *function.jsi* filen. Om du till exempel vill läsa innehållet i en HTTP-begäran i binärt format, ange `dataType` till `binary` :

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Andra alternativ för `dataType` är `stream` och `string` .

## <a name="binding-direction"></a>Bindnings riktning

Alla utlösare och bindningar har en `direction` egenskap i [function.jspå](./functions-reference.md) filen:

- För utlösare är riktningen alltid `in`
- Indata och utgående bindningar använder `in` och `out`
- Vissa bindningar har stöd för en speciell riktning `inout` . Om du använder `inout` är det bara **avancerad redigerare** som är tillgänglig via fliken **integrera** i portalen.

När du använder [attribut i ett klass bibliotek](functions-dotnet-class-library.md) för att konfigurera utlösare och bindningar, anges riktningen i en attributhierarki eller härleds från parameter typen.

## <a name="add-bindings-to-a-function"></a>Lägga till bindningar till en funktion

Du kan ansluta din funktion till andra tjänster med hjälp av indata eller utgående bindningar. Lägg till en bindning genom att lägga till dess speciella definitioner i din funktion. Mer information finns [i lägga till bindningar till en befintlig funktion i Azure Functions](add-bindings-existing-function.md).  

## <a name="supported-bindings"></a>Bindningar som stöds

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Information om vilka bindningar som är i för hands version eller som godkänns för användning av produktion finns i [språk som stöds](supported-languages.md).

## <a name="bindings-code-examples"></a>Exempel på bindnings koder

Använd följande tabell för att hitta exempel på olika typer av bindningar som visar hur du arbetar med bindningar i dina funktioner. Först väljer du fliken språk som motsvarar ditt projekt. 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="custom-bindings"></a>Anpassade bindningar

Du kan skapa anpassade bindningar för indata och utdata. Bindningar måste vara skapade i .NET, men kan användas på alla språk som stöds. Mer information om hur du skapar anpassade bindningar finns i [skapa anpassade bindningar för indata och utdata](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings).

## <a name="resources"></a>Resurser
- [Bindnings uttryck och mönster](./functions-bindings-expressions-patterns.md)
- [Använda Azures funktions retur värde](./functions-bindings-return-value.md)
- [Så här registrerar du ett bindnings uttryck](./functions-bindings-register.md)
- Prestandatester
  - [Strategier för att testa din kod i Azure Functions](functions-test-a-function.md)
  - [Köra en funktion som inte utlösts av HTTP manuellt](functions-manually-run-non-http.md)
- [Hanterar bindnings fel](./functions-bindings-errors.md)

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Registrera Azure Functions bindnings tillägg](./functions-bindings-register.md)
