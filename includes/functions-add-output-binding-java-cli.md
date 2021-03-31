---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 2b2c043e70aac14c7fc6f0b58aae257624b05d13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "80673260"
---
I ett Java-projekt definieras bindningarna som bindnings anteckningar i funktions metoden. *function.js* filen skapas sedan automatiskt baserat på dessa anteckningar.

Bläddra till platsen för funktions koden under _src/main/Java_, öppna filen *Function. java* -projekt och Lägg till följande parameter i `run` metod definitionen:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

`msg`Parametern är en [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) typ som representerar en samling strängar som skrivs som meddelanden till en utgående bindning när funktionen slutförs. I det här fallet är utdata en lagrings kö med namnet `outqueue` . Anslutnings strängen för lagrings kontot anges av- `connection` metoden. I stället för själva anslutnings strängen skickar du den program inställning som innehåller anslutnings strängen för lagrings kontot.

`run`Metod definitionen bör nu se ut som i följande exempel:  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```