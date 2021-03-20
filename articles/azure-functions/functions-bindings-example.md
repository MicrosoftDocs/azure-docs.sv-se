---
title: Exempel på utlösare och bindning i Azure Functions
description: Lär dig att konfigurera Azure Function-bindningar
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: c95524a5de6696bd0ffe7463451d152a9d3a19b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88205965"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Exempel på utlösare och bindning i Azure Functions

Den här artikeln visar hur du konfigurerar en [utlösare och bindningar](./functions-triggers-bindings.md) i en Azure-funktion.

Anta att du vill skriva en ny rad i Azure Table Storage varje gång ett nytt meddelande visas i Azure Queue Storage. Det här scenariot kan implementeras med hjälp av en Azure Queue Storage-utlösare och en Azure Table Storage-utdatabindning. 

Här är en *function.jspå* filen för det här scenariot. 

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "direction": "in",
      "name": "order",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "direction": "out",
      "name": "$return",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Det första elementet i `bindings` matrisen är kön Storage-utlösare. - `type` Och- `direction` egenskaperna identifierar utlösaren. `name`Egenskapen identifierar funktions parametern som tar emot köns meddelande innehåll. Namnet på kön som ska övervakas är i `queueName` och anslutnings strängen är i den app-inställning som identifieras av `connection` .

Det andra elementet i `bindings` matrisen är Azure-Table Storage utgående bindning. - `type` Och- `direction` egenskaperna identifierar bindningen. `name`Egenskapen anger hur funktionen tillhandahåller den nya tabell raden, i det här fallet genom att använda funktionens retur värde. Namnet på tabellen är i `tableName` och anslutnings strängen är i den app-inställning som identifieras av `connection` .

Om du vill visa och redigera innehållet i *function.js* i Azure Portal klickar du på alternativet **avancerad redigerare** på fliken **integrera** i din funktion.

> [!NOTE]
> Värdet för `connection` är namnet på en app-inställning som innehåller anslutnings strängen, inte själva anslutnings strängen. Bindningar använder anslutnings strängar som är lagrade i appinställningar för att säkerställa bästa praxis som *function.jspå* inte innehåller tjänst hemligheter.

## <a name="c-script-example"></a>C#-skript exempel

Här är C#-skript koden som fungerar med denna utlösare och bindning. Observera att namnet på parametern som tillhandahåller innehållet i kön är `order` . det här namnet är obligatoriskt eftersom `name` egenskap svärdet i *function.jspå* är `order` 

```cs
#r "Newtonsoft.Json"

using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, ILogger log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

## <a name="javascript-example"></a>JavaScript-exempel

Samma *function.jsi* filen kan användas med en JavaScript-funktion:

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

## <a name="class-library-example"></a>Exempel på klass bibliotek

I ett klass bibliotek tillhandahålls samma Utlös ande och bindande informations &mdash; kön och tabell namn, lagrings konton, funktions parametrar för indata och utdata från &mdash; attribut i stället för en function.jspå filen. Här är ett exempel:

```csharp
public static class QueueTriggerTableOutput
{
    [FunctionName("QueueTriggerTableOutput")]
    [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
    public static Person Run(
        [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order,
        ILogger log)
    {
        return new Person() {
                PartitionKey = "Orders",
                RowKey = Guid.NewGuid().ToString(),
                Name = order["Name"].ToString(),
                MobileNumber = order["MobileNumber"].ToString() };
    }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

Nu har du en fungerande funktion som utlöses av en Azure-kö och matar ut data till Azure Table Storage.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Mönster för Azure Functions bindnings uttryck](./functions-bindings-expressions-patterns.md)
