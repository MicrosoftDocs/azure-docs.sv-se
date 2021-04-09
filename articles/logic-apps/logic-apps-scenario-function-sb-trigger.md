---
title: Anropa Logic Apps med Azure Functions
description: Anropa eller utlös Logi Kap par genom att använda Azure Functions och Azure Service Bus
services: logic-apps
ms.suite: integration
ms.reviewer: jehollan, klam, logicappspm
ms.topic: article
ms.date: 11/08/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: a7df9ba1318f40de8af392cfaedbe51d7a5df755
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784943"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Anropa eller utlös Logi Kap par genom att använda Azure Functions och Azure Service Bus

Du kan använda [Azure Functions](../azure-functions/functions-overview.md) för att utlösa en Logic app när du behöver distribuera en långvarig lyssnare eller uppgift. Du kan till exempel skapa en funktion som lyssnar i i en [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) kö och som omedelbart utlöser en Logic app som en push-utlösare.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett Azure Service Bus namn område. Om du inte har ett namn område måste du [först skapa ditt namn område](../service-bus-messaging/service-bus-create-namespace-portal.md).

* En Function-app, som är en behållare för dina funktioner. Om du inte har en Function-app, [skapar du först din Function-app](../azure-functions/functions-get-started.md)och ser till att du väljer .net som körnings stack.

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Skapa en logikapp

I det här scenariot har du en funktion som kör varje Logic-app som du vill utlösa. Börja med att skapa en logisk app som börjar med en utlösare för HTTP-begäran. Funktionen anropar slut punkten när ett köat meddelande tas emot.

1. Logga in på [Azure Portal](https://portal.azure.com)och skapa en tom Logic-app.

   Om du inte har använt Logic Apps igen går du igenom [snabb start: skapa din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Skriv `http request` i sökrutan. I listan utlösare väljer du alternativet **när en HTTP-begäran tas emot** .

   ![Välj utlösare](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   Med utlösaren för förfrågningar kan du välja att ange ett JSON-schema som ska användas med meddelandet i kön. JSON-scheman hjälper Logic App Designer att förstå strukturen för indata och göra utmatningarna enklare att använda i ditt arbets flöde.

1. Om du vill ange ett schema anger du schemat i rutan **JSON-schema för begär ande text** , till exempel:

   ![Ange JSON-schema](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Om du inte har ett schema, men du har ett exempel på en nytto Last i JSON-format, kan du generera ett schema från den nytto lasten.

   1. I utlösaren för begäran väljer **du Använd exempel nytto last för att generera schemat**.

   1. Under **Ange eller klistra in en exempel-JSON-nyttolast** anger du exempel nytto lasten och väljer sedan **slutförd**.

      ![Ange exempel på nytto Last](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   Detta exempel på en nytto Last genererar detta schema som visas i utlösaren:

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "number": {
                  "type": "integer"
               },
               "street": {
                  "type": "string"
               },
               "city": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "integer"
               },
               "country": {
                  "type": "string"
               }
            }
         }
      }
   }
   ```

1. Lägg till andra åtgärder som du vill köra efter att du har tagit emot Queue-meddelandet.

   Du kan till exempel skicka ett e-postmeddelande med Office 365 Outlook Connector.

1. Spara din Logic app, som genererar återanrops-URL: en för utlösaren i den här Logic-appen. Senare använder du den här återanrops-URL: en i koden för Azure Service Bus Queue-utlösaren.

   Återanrops-URL: en visas i egenskapen **http post URL** .

   ![Återanrops-URL skapades för utlösaren](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-a-function"></a>Skapa en funktion

Därefter skapar du funktionen som fungerar som utlösare och lyssnar på kön.

1. Öppna och expandera din Function-app i Azure Portal, om den inte redan är öppen. 

1. Under namnet på din funktion i appen expanderar du **Functions**. I fönstret **funktioner** väljer du **ny funktion**.

   ![Expandera "Functions" och välj "ny funktion"](./media/logic-apps-scenario-function-sb-trigger/add-new-function-to-function-app.png)

1. Välj den här mallen baserat på om du har skapat en ny function-app där du valde .NET som körnings stack, eller om du använder en befintlig Function-app.

   * För nya Function-appar väljer du den här mallen: **Service Bus köa utlösare**

     ![Välj mall för ny function-app](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * För en befintlig Function-app väljer du den här mallen: **Service Bus Queue trigger-C#**

     ![Välj mall för befintlig Function-app](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. Ange ett namn för utlösaren i rutan **Azure Service Bus kön utlösare** och konfigurera **Service Bus anslutning** för kön, som använder Azure Service Bus SDK `OnMessageReceive()` -lyssnare och välj **skapa**.

1. Skriv en grundläggande funktion för att anropa den tidigare skapade Logic app-slutpunkten genom att använda Queue meddelandet som en utlösare. Innan du skriver din funktion bör du gå igenom följande överväganden:

   * I det här exemplet används `application/json` meddelandets innehålls typ, men du kan ändra den här typen efter behov.
   
   * På grund av möjlig körning av funktioner, höga volymer eller tung belastning, Undvik att instansiera [klassen HTTPClient](/dotnet/api/system.net.http.httpclient) med `using` instruktionen och direkt skapa HTTPClient-instanser per begäran. Mer information finns i [använda HttpClientFactory för att implementera elastiska HTTP-begäranden](/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests#issues-with-the-original-httpclient-class-available-in-net-core).
   
   * Återanvänd om möjligt instansen av HTTP-klienter. Mer information finns i [hantera anslutningar i Azure Functions](../azure-functions/manage-connections.md).

   I det här exemplet används [ `Task.Run` metoden](/dotnet/api/system.threading.tasks.task.run) i [asynkront](/dotnet/csharp/language-reference/keywords/async) läge. Mer information finns i [asynkron programmering med async och await](/dotnet/csharp/programming-guide/concepts/async/).

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Can also fetch from App Settings or environment variable
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible: https://docs.microsoft.com/azure/azure-functions/manage-connections
   private static HttpClient httpClient = new HttpClient();

   public static async Task Run(string myQueueItem, TraceWriter log) 
   {
      log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
      var response = await httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")); 
   }
   ```

1. Testa funktionen genom att lägga till ett Queue-meddelande med hjälp av ett verktyg som [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

   Logic app utlöser omedelbart när funktionen tar emot meddelandet.

## <a name="next-steps"></a>Nästa steg

* [Anropa, utlösa eller kapsla arbets flöden med hjälp av HTTP-slutpunkter](../logic-apps/logic-apps-http-endpoint.md)