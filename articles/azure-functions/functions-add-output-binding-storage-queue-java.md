---
title: Anslut din Java-funktion till Azure Storage
description: Lär dig hur du ansluter en HTTP-utlöst Java-funktion till Azure Storage med hjälp av en kö Storage utgående bindning.
author: KarlErickson
ms.custom: devx-track-java
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: b4381c4acbea8a3b7d86d9c32aaf9cea24cf15fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422662"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Anslut din Java-funktion till Azure Storage

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Den här artikeln visar hur du integrerar funktionen som du skapade i [föregående snabb starts artikel](./create-first-function-cli-java.md?tabs=bash,browser) med en Azure Storage kö. Den utgående bindning som du lägger till i den här funktionen skriver data från en HTTP-begäran till ett meddelande i kön.

De flesta bindningar kräver en lagrad anslutnings sträng som används för att få åtkomst till den kopplade tjänsten. För att göra anslutningen enklare använder du det lagrings konto som du skapade med din Function-app. Anslutningen till det här kontot är redan lagrad i en app-inställning med namnet `AzureWebJobsStorage` .  

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här artikeln slutför du stegen i [del 1 av Java-snabb](./create-first-function-cli-java.md?tabs=bash,browser)starten.

## <a name="download-the-function-app-settings"></a>Ladda ned appens funktions inställningar

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Aktivera tilläggs paket

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Nu kan du lägga till bindningen för Storage-utdata i projektet.

## <a name="add-an-output-binding"></a>Lägg till en utdatabindning

[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Lägg till kod som använder utdatabindning

[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]

Nu är du redo att testa den nya utgående bindningen lokalt.

## <a name="run-the-function-locally"></a>Köra funktionen lokalt

Som tidigare använder du följande kommando för att skapa projektet och starta Functions-körningen lokalt:

# <a name="maven"></a>[Maven](#tab/maven)
```bash
mvn clean package 
mvn azure-functions:run
```
# <a name="gradle"></a>[Gradle](#tab/gradle) 
```bash
gradle jar --info
gradle azureFunctionsRun
```
---

> [!NOTE]  
> Eftersom du har aktiverat tilläggs paket i host.jspå har [lagrings bindnings tillägget](functions-bindings-storage-blob.md#add-to-your-functions-app) laddats ned och installerats åt dig under starten, tillsammans med de andra Microsoft binding-tilläggen.

Som tidigare utlöser funktionen från kommando raden med hjälp av sväng i ett nytt terminalfönster:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Den här gången skapar bindningen för utdata också en kö med namnet `outqueue` i ditt lagrings konto och lägger till ett meddelande med samma sträng.

Sedan använder du Azure CLI för att visa den nya kön och kontrol lera att ett meddelande har lagts till. Du kan också visa din kö med hjälp av [Microsoft Azure Storage Explorer][Azure Storage Explorer] eller i [Azure Portal](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Distribuera om projektet 

Uppdatera den publicerade appen genom att köra följande kommando igen:  

# <a name="maven"></a>[Maven](#tab/maven)  
```bash
mvn azure-functions:deploy
```
# <a name="gradle"></a>[Gradle](#tab/gradle)  
```bash
gradle azureFunctionsDeploy
```
---

Igen kan du använda sväng för att testa den distribuerade funktionen. Som tidigare skickar du värdet `AzureFunctions` i bröd texten i post-begäran till URL: en, som i det här exemplet:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Du kan [Granska lagrings köns meddelande](#query-the-storage-queue) igen för att kontrol lera att utmatnings bindningen genererar ett nytt meddelande i kön som förväntat.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Nästa steg

Du har uppdaterat din HTTP-utlöst funktion för att skriva data till en lagrings kö. Mer information om hur du utvecklar Azure Functions med Java finns i [Azure Functions Java Developer Guide](functions-reference-java.md) och [Azure Functions utlösare och bindningar](functions-triggers-bindings.md). Exempel på kompletta funktions projekt i Java finns i [exemplen för Java Functions](/samples/browse/?products=azure-functions&languages=Java). 

Sedan bör du aktivera Application Insights övervakning för din Function-app:

> [!div class="nextstepaction"]
> [Aktivera Application Insights-integrering](configure-monitoring.md#add-to-an-existing-function-app)


[Azure Storage Explorer]: https://storageexplorer.com/
