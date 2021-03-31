---
title: 'Skapa webb-API: er & REST API: er för Azure Logic Apps'
description: 'Skapa webb-API: er & REST API: er för att anropa dina API: er, tjänster eller system för system integrering i Azure Logic Apps'
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/26/2017
ms.openlocfilehash: 3fe98160cc10eb3607b8309a9a263d63380dcfb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89073224"
---
# <a name="create-custom-apis-you-can-call-from-azure-logic-apps"></a>Skapa anpassade API: er som du kan anropa från Azure Logic Apps

Även om Azure Logic Apps erbjuder [hundratals kopplingar](../connectors/apis-list.md) som du kan använda i Logic app-arbetsflöden, kanske du vill anropa API: er, system och tjänster som inte är tillgängliga som kopplingar. Du kan skapa dina egna API: er som tillhandahåller åtgärder och utlösare som ska användas i Logic Apps. Här följer andra orsaker till varför du kanske vill skapa egna API: er som du kan anropa från Logic app-arbetsflöden:

* Utöka dina aktuella system integrerings-och data integrerings arbets flöden.
* Hjälp kunderna att använda tjänsten för att hantera professionella eller personliga uppgifter.
* Utöka räckvidden, identifierings möjligheten och användningen för din tjänst.

I princip är kopplingar webb-API: er som använder REST för anslutnings bara gränssnitt, [Swagger metadata-format](https://swagger.io/specification/) för dokumentation och JSON som sitt data utbytes format. Eftersom kopplingar är REST-API: er som kommunicerar via HTTP-slutpunkter kan du använda valfritt språk, t. ex. .NET, Java, python eller Node.js, för att skapa anslutningar. Du kan också vara värd för dina API: er på [Azure App Service](../app-service/overview.md), ett PaaS-erbjudande (Platform-as-a-Service) som tillhandahåller ett av de bästa, enklaste och mest skalbara sätten för API-värd. 

För att anpassade API: er ska fungera med Logic Apps kan ditt API tillhandahålla [*åtgärder*](./logic-apps-overview.md#logic-app-concepts) som utför vissa uppgifter i Logic app-arbetsflöden. Ditt API kan också fungera som en [*utlösare*](./logic-apps-overview.md#logic-app-concepts) som startar ett Logic app-arbetsflöde när nya data eller en händelse uppfyller ett visst villkor. I det här avsnittet beskrivs vanliga mönster som du kan följa för att skapa åtgärder och utlösare i ditt API, baserat på vilket beteende du vill att ditt API ska tillhandahålla.

Du kan vara värd för dina API: er på [Azure App Service](../app-service/overview.md), ett PaaS-erbjudande (Platform-as-a-Service) som ger mycket skalbara, enkla API-värdar.

> [!TIP] 
> Även om du kan distribuera dina API: er som webbappar, bör du överväga att distribuera dina API: er som API Apps, vilket kan göra jobbet enklare när du skapar, är värd för och använder API: er i molnet och lokalt. Du behöver inte ändra någon kod i dina API: er – distribuera bara din kod till en API-app. Lär dig till exempel hur du skapar API-appar som skapats med följande språk: 
> 
> * [ASP.net](../app-service/quickstart-dotnetcore.md). 
> * [Java](../app-service/quickstart-java.md)
> * [Node.js](../app-service/quickstart-nodejs.md)
> * [PHP](../app-service/quickstart-php.md)
> * [Python](../app-service/quickstart-python.md)
> * [Ruby](../app-service/quickstart-ruby.md)
>
> För API-AppData som skapats för Logic Apps, besök [Azure Logic Apps GitHub-lagringsplatsen](https://github.com/logicappsio).

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>Hur skiljer sig anpassade API: er från anpassade anslutningar?

Anpassade API: er och [anpassade anslutningar](../logic-apps/custom-connector-overview.md) är webb-API: er som använder rest för anslutnings bara gränssnitt, [Swagger metadata-format](https://swagger.io/specification/) för dokumentation och JSON som deras data utbytes format. Eftersom dessa API: er och anslutningar är REST-API: er som kommunicerar via HTTP-slutpunkter kan du använda valfritt språk, som .NET, Java, python eller Node.js, för att skapa anpassade API: er och anslutningar.

Med anpassade API: er kan du anropa API: er som inte är kopplingar och tillhandahålla slut punkter som du kan anropa med HTTP + Swagger, Azure API Management eller App Services. Anpassade anslutningar fungerar som anpassade API: er men har även följande attribut:

* Registreras som Logic Apps anslutnings resurser i Azure.
* Visas med ikoner tillsammans med Microsoft-hanterade kopplingar i Logic Apps designer.
* Endast tillgängligt för anslutningens författare och Logic App-användare som har samma Azure Active Directory klient organisation och Azure-prenumeration i den region där Logic Apps distribueras.

Du kan också utse registrerade anslutningar för Microsoft-certifiering. Den här processen verifierar att registrerade anslutningar uppfyller villkoren för offentligt bruk och gör dessa anslutningar tillgängliga för användare i Energis par och Microsoft Power Apps.

Mer information om anpassade anslutningar finns i 

* [Översikt över anpassade anslutningar](../logic-apps/custom-connector-overview.md)
* [Skapa anpassade anslutningar från webb-API: er](/connectors/custom-connectors/create-web-api-connector)
* [Registrera anpassade anslutningar i Azure Logic Apps](/connectors/custom-connectors/)

## <a name="helpful-tools"></a>Användbara verktyg

Ett anpassat API fungerar bäst med logi Kap par när API: et också har ett [Swagger-dokument](https://swagger.io/specification/) som beskriver API: s åtgärder och parametrar.
Många bibliotek, som [swashbuckle](https://github.com/domaindrivendev/Swashbuckle), kan automatiskt generera Swagger-filen åt dig. Om du vill kommentera Swagger-filen för visnings namn, egenskaps typer och så vidare kan du också använda [TRex](https://github.com/nihaue/TRex) så att Swagger-filen fungerar bra med Logic Apps.

<a name="actions"></a>

## <a name="action-patterns"></a>Åtgärds mönster

För att Logi Kap par ska kunna utföra uppgifter bör ditt anpassade API tillhandahålla [*åtgärder*](./logic-apps-overview.md#logic-app-concepts). Varje åtgärd i ditt API mappar till en åtgärd. En grundläggande åtgärd är en kontrollant som accepterar HTTP-begäranden och returnerar HTTP-svar. Till exempel skickar en Logic app en HTTP-begäran till din webbapp eller API-app. Din app returnerar sedan ett HTTP-svar, tillsammans med innehåll som Logic-appen kan bearbeta.

För en standard åtgärd kan du skriva en HTTP-metod för begäran i ditt API och beskriva metoden i en Swagger-fil. Du kan sedan anropa ditt API direkt med en [http-åtgärd](../connectors/connectors-native-http.md) eller en [http + Swagger-](../connectors/connectors-native-http-swagger.md) åtgärd. Som standard måste svar returneras inom tids gränsen för [begäran](./logic-apps-limits-and-config.md). 

![Standard åtgärds mönster](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a> Om du vill göra en logisk app vänta medan ditt API Slutför längre aktiviteter som körs kan ditt API följa det [asynkrona avsöknings mönstret](#async-pattern) eller det [asynkrona webhook-mönstret](#webhook-actions) som beskrivs i det här avsnittet. För en analog som hjälper dig att visualisera dessa mönster med olika beteenden kan du föreställa dig processen för att beställa en anpassad tårta från en bageri. Avsöknings mönstret speglar beteendet där du ringer bak sidan var 20: e minut för att kontrol lera om tårtan är klar. Webhook-mönstret speglar beteendet där bagerien ber dig om ditt telefonnummer så att de kan anropa dig när tårtan är klar.

Exempel finns på [Logic Apps GitHub-lagringsplatsen](https://github.com/logicappsio). Lär dig även mer om [användnings mätning för åtgärder](logic-apps-pricing.md).

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>Utföra långvariga uppgifter med mönstret för avsöknings åtgärd

Om du vill att ditt API utför uppgifter som kan köras längre än tids gränsen för [förfrågningar](./logic-apps-limits-and-config.md), kan du använda det asynkrona avsöknings mönstret. Det här mönstret har ditt API fungerar i en separat tråd, men du måste ha en aktiv anslutning till Logic Apps-motorn. På så sätt går det inte längre att använda den logiska appen eller fortsätta med nästa steg i arbets flödet innan ditt API har slutfört arbetet.

Här är det allmänna mönstret:

1. Kontrol lera att motorn vet att ditt API har accepterat begäran och börjat fungera.
2. När motorn gör efterföljande begär Anden om jobb status, så låt motorn se när ditt API Slutför aktiviteten.
3. Returnera relevanta data till motorn så att Logic app-arbetsflödet kan fortsätta.

<a name="bakery-polling-action"></a> Använd nu den tidigare bagerien till avsöknings mönstret och Föreställ dig att du kallar en bageri och beställer en anpassad tårta för leverans. Processen för att göra tårtan tar tid och du vill inte vänta på telefonen medan bagerien fungerar på tårtan. Bagerien bekräftar din beställning och har du ringt var 20: e minut för Tårtans status. Efter 20 minuter kan du kalla bagerien, men de talar om att din tårta inte är klar och att du bör ringa in en till 20 minuter. Den här processen fortsätter tills du ringer, och bagerien visar att din beställning är klar och levererar din tårta. 

Därför ska vi mappa detta avsöknings mönster tillbaka. Bagerien representerar ditt anpassade API, medan du, tårta-kunden, representerar Logic Apps motor. När motorn anropar API: et med en begäran, bekräftar ditt API begäran och svarar med tidsintervallet när motorn kan kontrol lera jobb status. Motorn fortsätter att kontrol lera jobb status tills ditt API svarar på att jobbet görs och returnerar data till din Logi Kap par, som sedan fortsätter arbets flödet. 

![Åtgärds mönster för avsökning](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

Här följer de olika stegen för ditt API att följa, som beskrivs i API: s perspektiv:

1. När ditt API hämtar en HTTP-begäran för att starta arbetet, returnerar omedelbart ett HTTP- `202 ACCEPTED` svar med `location` rubriken som beskrivs senare i det här steget. Med det här svaret kan Logic Appss motorn veta att ditt API fick begäran, godkänt nytto lasten för begäran (indata) och nu bearbetas. 
   
   `202 ACCEPTED`Svaret måste innehålla följande rubriker:
   
   * *Obligatorisk*: en `location` rubrik som anger den absoluta sökvägen till en URL där Logic Appss motorn kan kontrol lera ditt API: s jobb status

   * *Valfritt*: en `retry-after` rubrik som anger antalet sekunder som motorn ska vänta innan den kontrollerar `location` URL: en för jobb status. 

     Som standard kontrollerar motorn var 20: e sekund. Om du vill ange ett annat intervall inkluderar du `retry-after` rubriken och antalet sekunder tills nästa omröstning.

2. När den angivna tiden har passerat avsöker Logic Appss motorn `location` URL: en för att kontrol lera jobb status. Ditt API bör utföra dessa kontroller och returnera dessa svar:
   
   * Om jobbet är färdigt returnerar du ett HTTP- `200 OK` svar, tillsammans med svarets nytto Last (ininformation för nästa steg).

   * Om jobbet fortfarande bearbetas returnerar du ett annat HTTP- `202 ACCEPTED` svar, men med samma rubriker som det ursprungliga svaret.

När ditt API följer det här mönstret behöver du inte göra något i Logic app Workflow-definitionen för att fortsätta kontrol lera jobb status. När motorn hämtar ett HTTP- `202 ACCEPTED` svar och en giltig `location` rubrik, respekterar motorn det asynkrona mönstret och kontrollerar `location` rubriken tills ditt API returnerar ett svar som inte är 202.

> [!TIP]
> För ett exempel på asynkront mönster, granska [svars exemplet för asynkron kontrollant i GitHub](https://github.com/logicappsio/LogicAppsAsyncResponseSample).

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>Utföra tids krävande uppgifter med åtgärds mönstret webhook

Alternativt kan du använda webhook-mönstret för långvariga uppgifter och asynkron bearbetning. Det här mönstret har en paus för logi Kap par och väntar på ett "återanrop" från ditt API för att slutföra bearbetningen innan arbets flödet fortsätter. Detta återanrop är ett HTTP-inlägg som skickar ett meddelande till en URL när en händelse inträffar. 

<a name="bakery-webhook-action"></a> Använd nu den tidigare bagerien för webhook-mönstret och Föreställ dig att du kallar en bageri och beställer en anpassad tårta för leverans. Processen för att göra tårtan tar tid och du vill inte vänta på telefonen medan bagerien fungerar på tårtan. Bagerien bekräftar din beställning, men den här gången ger du dem ditt telefonnummer så att de kan ringa dig när tårtan är färdig. Den här gången visar bagerien när din beställning är klar och levererar din tårta.

När vi mappar detta webhook-mönster, representerar bagerien ditt anpassade API, medan du, tårta-kunden, representerar Logic Apps motor. Motorn anropar API: et med en begäran och innehåller en "återanrops-URL".
När jobbet är färdigt använder ditt API URL: en för att meddela motorn och returnera data till din Logi Kap par, som sedan fortsätter arbets flödet. 

För det här mönstret ställer du in två slut punkter på din styrenhet: `subscribe` och `unsubscribe`

*  `subscribe` slut punkt: när körningen når ditt API: s åtgärd i arbets flödet anropar Logic Apps-motorn `subscribe` slut punkten. Det här steget gör att Logic app skapar en återanrops-URL som din API lagrar och väntar sedan på motringning från ditt API när arbetet är slutfört. Ditt API anropar sedan med ett HTTP-inlägg till URL: en och skickar tillbaka innehåll och rubriker som indata till Logic-appen.

* `unsubscribe` slut punkt: om Logic app-körningen avbryts anropar Logic Apps-motorn `unsubscribe` slut punkten. Ditt API kan sedan avregistrera återanrops-URL: en och stoppa eventuella processer vid behov.

![Åtgärds mönster för webhook](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

Logic App Designer stöder för närvarande inte identifiering av webhook-slutpunkter via Swagger. Så för det här mönstret måste du lägga till en [ **webhook** -åtgärd](../connectors/connectors-native-webhook.md) och ange URL, rubriker och brödtext för din begäran. Se även [arbets flödes åtgärder och utlösare](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action). Om du vill se ett exempel på ett webhook-mönster kan du läsa detta [exempel på webhook-utlösare](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs)

Här följer några andra tips och anmärkningar:

* Om du vill skicka in återanrops-URL: en kan du använda `@listCallbackUrl()` arbets flödes funktionen i något av de föregående fälten om det behövs.

* Om du äger både Logic app och den prenumererade tjänsten behöver du inte anropa `unsubscribe` slut punkten efter att återanrops-URL: en har anropats. Annars måste Logic Apps runtime anropa `unsubscribe` slut punkten för att signalera att inga fler anrop förväntas och för att göra det möjligt att rensa resursen på Server sidan.

<a name="triggers"></a>

## <a name="trigger-patterns"></a>Utlös mönster

Ditt anpassade API kan fungera som en [*utlösare*](./logic-apps-overview.md#logic-app-concepts) som startar en Logic app när nya data eller en händelse uppfyller ett visst villkor. Den här utlösaren kan antingen kontrol lera regelbundet eller vänta och lyssna efter nya data eller händelser på tjänstens slut punkt. Om nya data eller en händelse uppfyller det angivna villkoret utlöses utlösaren och startar Logic-appen, som lyssnar på den utlösaren. För att starta Logic Apps på det här sättet kan ditt API följa [*avsöknings utlösaren*](#polling-triggers) eller [*webhook-utlösaren*](#webhook-triggers) . Dessa mönster liknar deras motsvarigheter för [avsökning av åtgärder](#async-pattern) och [webhook-åtgärder](#webhook-actions). Lär dig också mer om [användnings mätning för utlösare](logic-apps-pricing.md).

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>Sök efter nya data eller händelser regelbundet med utlösare för avsöknings utlösare

En *avsöknings utlösare* fungerar ungefär som den [avsöknings åtgärd](#async-pattern) som tidigare beskrivits i det här avsnittet. Logic Apps-motorn anropar regelbundet och kontrollerar utlösnings slut punkten för nya data eller händelser. Om motorn hittar nya data eller en händelse som uppfyller det angivna villkoret utlöses utlösaren. Sedan skapar motorn en Logic App-instans som bearbetar data som indata. 

![Utlösnings mönster för avsökning](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> Varje avsöknings förfrågan räknas som en åtgärds körning, även när ingen Logic App-instans skapas. För att förhindra bearbetning av samma data flera gånger bör utlösaren rensa data som redan har lästs och skickats till Logic app.

Här följer några steg för en avsöknings utlösare som beskrivs i API: s perspektiv:

| Hittade du nya data eller händelser?  | API-svar | 
| ------------------------- | ------------ |
| Hittade | Returnera en HTTP- `200 OK` status med svarets nytto Last (ininformation för nästa steg). <br/>Det här svaret skapar en Logic App-instans och startar arbets flödet. | 
| Hittades inte | Returnera en HTTP- `202 ACCEPTED` status med ett `location` sidhuvud och en `retry-after` rubrik. <br/>För utlösare `location` bör rubriken även innehålla en `triggerState` frågeparameter, som vanligt vis är en "tidstämpel". Ditt API kan använda den här identifieraren för att spåra den senaste gången Logic-appen utlöstes. | 
||| 

Om du till exempel regelbundet vill kontrol lera din tjänst för nya filer kan du bygga en avsöknings utlösare som har följande beteenden:

| Förfrågan ingår `triggerState` ? | API-svar | 
| -------------------------------- | -------------| 
| Inga | Returnera en HTTP- `202 ACCEPTED` status plus ett `location` sidhuvud med `triggerState` angivet till aktuell tid och `retry-after` intervallet till 15 sekunder. | 
| Ja | Kontrol lera om det finns filer som har lagts till efter for i-tjänsten `DateTime` `triggerState` . | 
||| 

| Antal filer som hittades | API-svar | 
| --------------------- | -------------| 
| Enskild fil | Returnera en HTTP- `200 OK` status och innehålls nytto lasten, uppdatera `triggerState` till `DateTime` för den returnerade filen och ange `retry-after` intervall till 15 sekunder. | 
| Flera filer | Returnera en fil i taget och en HTTP- `200 OK` status, uppdatera `triggerState` och ange `retry-after` intervallet till 0 sekunder. </br>De här stegen gör att motorn vet att fler data är tillgängliga och att motorn omedelbart ska begära data från URL: en i `location` rubriken. | 
| Inga filer | Returnera en HTTP- `202 ACCEPTED` status, ändra inte `triggerState` och ange `retry-after` intervallet till 15 sekunder. | 
||| 

> [!TIP]
> För ett exempel på ett utlösnings mönster utlösare, granska det här [exemplet på kontroll utlösare i GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs).

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>Vänta och lyssna efter nya data eller händelser med utlösare för webhook

En webhook-utlösare är en *push-utlösare* som väntar och lyssnar efter nya data eller händelser på tjänstens slut punkt. Om nya data eller en händelse uppfyller det angivna villkoret utlöses utlösaren och skapar en Logic App-instans, som sedan bearbetar data som indata.
Webhook-utlösare fungerar ungefär som [webhook-åtgärder](#webhook-actions) som tidigare beskrivs i det här avsnittet och har kon figurer ATS med `subscribe` och `unsubscribe` slut punkter. 

* `subscribe` slut punkt: när du lägger till och sparar en webhook-utlösare i din Logic app anropar Logic Apps-motorn `subscribe` slut punkten. Det här steget gör att Logic app skapar en återanrops-URL som din API lagrar. När det finns nya data eller en händelse som uppfyller det angivna villkoret, anropar API: et igen med ett HTTP-inlägg till URL: en. Innehålls nytto lasten och huvudena skickas som inmatade i Logic-appen.

* `unsubscribe` slut punkt: om webhook-utlösaren eller hela Logic-appen tas bort anropar Logic Apps-motorn `unsubscribe` slut punkten. Ditt API kan sedan avregistrera återanrops-URL: en och stoppa eventuella processer vid behov.

![Utlösnings mönster för webhook](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

Logic App Designer stöder för närvarande inte identifiering av webhook-slutpunkter via Swagger. Så för det här mönstret måste du lägga till en [ **webhook** -utlösare](../connectors/connectors-native-webhook.md) och ange URL, rubriker och brödtext för din begäran. Se även [HTTPWebhook-utlösare](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger). Om du vill se ett exempel på ett webhook-mönster kan du läsa detta [exempel på en utlösare i GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

Här följer några andra tips och anmärkningar:

* Om du vill skicka in återanrops-URL: en kan du använda `@listCallbackUrl()` arbets flödes funktionen i något av de föregående fälten om det behövs.

* För att förhindra bearbetning av samma data flera gånger bör utlösaren rensa data som redan har lästs och skickats till Logic app.

* Om du äger både Logic app och den prenumererade tjänsten behöver du inte anropa `unsubscribe` slut punkten efter att återanrops-URL: en har anropats. Annars måste Logic Apps runtime anropa `unsubscribe` slut punkten för att signalera att inga fler anrop förväntas och för att göra det möjligt att rensa resursen på Server sidan.

## <a name="improve-security-for-calls-to-your-apis-from-logic-apps"></a>Förbättra säkerheten för anrop till dina API: er från Logic Apps

När du har skapat dina anpassade API: er konfigurerar du autentisering för dina API: er så att du kan anropa dem säkert från Logic Apps. Lär dig [hur du kan förbättra säkerheten för anrop till anpassade API: er från Logic Apps](../logic-apps/logic-apps-custom-api-authentication.md).

## <a name="deploy-and-call-your-apis"></a>Distribuera och anropa dina API: er

När du har konfigurerat autentisering konfigurerar du distribution för dina API: er. Lär dig [att distribuera och anropa anpassade API: er från Logic Apps](../logic-apps/logic-apps-custom-api-host-deploy-call.md).

## <a name="publish-custom-apis-to-azure"></a>Publicera anpassade API: er till Azure

Om du vill göra dina anpassade API: er tillgängliga för andra Logic Apps användare i Azure måste du lägga till säkerhet och registrera dem som Logic app-kopplingar. Mer information finns i [Översikt över anpassade anslutningsappar](../logic-apps/custom-connector-overview.md). 

Om du vill göra dina anpassade API: er tillgängliga för alla användare i Logic Apps, Power automatisering och Microsoft Power Apps, måste du lägga till säkerhet, registrera dina API: er som Logic app-kopplingar och utse dina anslutningar för det [Microsoft Azure certifierade programmet](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/). 

## <a name="get-support"></a>Få support

* Kontakta om du vill ha mer information om anpassade API: er [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com) .

* För frågor, besök [sidan Microsoft Q&en fråga för Azure Logic Apps](/answers/topics/azure-logic-apps.html).

* På [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish) kan du hjälpa till med att förbättra Logic Apps genom att rösta på förslag eller komma med egna förslag på förbättringar. 

## <a name="next-steps"></a>Nästa steg

* [Hantera fel och undantag](../logic-apps/logic-apps-exception-handling.md)
* [Anropa, utlösa eller kapsla Logic Apps med HTTP-slutpunkter](../logic-apps/logic-apps-http-endpoint.md)
* [Användnings mätning för åtgärder och utlösare](../logic-apps/logic-apps-pricing.md)
