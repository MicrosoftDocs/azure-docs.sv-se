---
title: Schema referens för utlösare och åtgärds typer
description: Schema referens guide för språk utlösare och åtgärds typer för arbets flödes definition i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: devx-track-js
ms.openlocfilehash: ea4a4a47e91e88c00ca8a4e886d0372a24482907
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784316"
---
# <a name="schema-reference-guide-for-trigger-and-action-types-in-azure-logic-apps"></a>Schema referens guide för utlösare och åtgärds typer i Azure Logic Apps

Den här referensen beskriver de allmänna typer som används för att identifiera utlösare och åtgärder i din Logic Apps underliggande arbets flödes definition, som beskrivs och verifieras av [arbets flödets definitions språk](../logic-apps/logic-apps-workflow-definition-language.md). Information om hur du hittar vissa anslutnings utlösare och åtgärder som du kan använda i dina Logi Kap par finns i listan under [Översikt över anslutnings](/connectors/)program.

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Översikt över utlösare

Alla arbets flöden innehåller en utlösare som definierar de anrop som instansierar och startar arbets flödet. Här följer de allmänna utlösnings kategorierna:

* En *avsöknings* utlösare som kontrollerar en tjänst slut punkt med jämna mellanrum

* En *push* -utlösare som skapar en prenumeration på en slut punkt och som tillhandahåller en *återanrops-URL* så att slut punkten kan meddela utlösaren när den angivna händelsen inträffar eller data är tillgängliga. Utlösaren väntar sedan på slutpunktens svar innan det utlöses.

Utlösare har dessa toppnivå element, även om några är valfria:  
  
```json
"<trigger-name>": {
   "type": "<trigger-type>",
   "inputs": { "<trigger-inputs>" },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "conditions": [ "<array-with-conditions>" ],
   "runtimeConfiguration": { "<runtime-config-options>" },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
},
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*utlösare-namn*> | Sträng | Namnet på utlösaren | 
| <*utlösare-typ*> | Sträng | Utlösarens typ, till exempel "http" eller "ApiConnection" | 
| <*trigger-Inputs*> | JSON-objekt | De indata som definierar utlösarens beteende | 
| <*tidsenhet*> | Sträng | Den tidsenhet som beskriver hur ofta utlösaren utlöses: "sekund", "minut", "timme", "dag", "vecka", "månad" | 
| <*antal-tidsenheter*> | Integer | Ett värde som anger hur ofta utlösaren utlöses utifrån frekvensen, vilket är antalet tidsenheter som ska vänta tills utlösaren utlöses igen <p>Här följer de lägsta och högsta intervallen: <p>– Månad: 1-16 månader </br>– Dag: 1-500 dagar </br>– Timme: 1 – 12000 timmar </br>-Minute: 1 – 72000 minuter </br>-Sekund: 1 – 9999999 sekunder<p>Om intervallet till exempel är 6 och frekvensen är "månad" är upprepningen var 6: a månad. | 
|||| 

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*matris – med-villkor*> | Matris | En matris som innehåller ett eller flera [villkor](#trigger-conditions) som avgör om arbets flödet ska köras. Endast tillgängligt för utlösare. | 
| <*runtime-Config-Options*> | JSON-objekt | Du kan ändra utlösarens körnings beteende genom att ange `runtimeConfiguration` Egenskaper. Mer information finns i [konfigurations inställningar för körning](#runtime-config-options). | 
| <*splitOn – uttryck*> | Sträng | För utlösare som returnerar en matris kan du ange ett uttryck som [delar upp eller *Avgruppera*](#split-on-debatch) mat ris objekt i flera arbets flödes instanser för bearbetning. | 
| <*åtgärd – alternativ*> | Sträng | Du kan ändra standard beteendet genom att ange `operationOptions` egenskapen. Mer information finns i [Åtgärds alternativ](#operation-options). | 
|||| 

## <a name="trigger-types-list"></a>Lista över utlösare

Varje utlösnings typ har ett annat gränssnitt och indata som definierar utlösarens beteende. 

### <a name="built-in-triggers"></a>Inbyggda utlösare

| Utlösartyp | Beskrivning | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | Kontrollerar eller *avsöker* varje slut punkt. Den här slut punkten måste följa ett angivet Utlösar-kontrakt antingen genom att använda ett `202` asynkront mönster eller genom att returnera en matris. | 
| [**HTTPWebhook**](#http-webhook-trigger) | Skapar en slut punkt som kan anropas för din Logic app, men anropar den angivna URL: en för registrering eller avregistrering. |
| [**Upprepning**](#recurrence-trigger) | Utlöses baserat på ett definierat schema. Du kan ange ett framtida datum och tid för att lösa den här utlösaren. Utifrån frekvensen kan du också ange tider och dagar för att köra arbets flödet. | 
| [**Förfrågan**](#request-trigger)  | Skapar en slut punkt som kan anropas för din Logic app och kallas även för en "Manuell" utlösare. Se till exempel [anrop, utlösare eller kapslade arbets flöden med HTTP-slutpunkter](../logic-apps/logic-apps-http-endpoint.md). | 
||| 

### <a name="managed-api-triggers"></a>Hanterade API-utlösare

| Utlösartyp | Beskrivning | 
|--------------|-------------| 
| [**ApiConnection**](#apiconnection-trigger) | Kontrollerar eller *avsöker* en slut punkt med hjälp av [Microsoft-hanterade API: er](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Skapar en slut punkt som kan anropas för din Logic app genom att anropa [Microsoft-hanterade API: er](../connectors/apis-list.md) för att prenumerera och avbryta prenumerationen | 
||| 

## <a name="triggers---detailed-reference"></a>Utlösare – detaljerad referens

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>APIConnection-utlösare  

Den här utlösaren kontrollerar eller *avsöker* en slut punkt med hjälp av [Microsoft-hanterade API: er](../connectors/apis-list.md) , så att parametrarna för den här utlösaren kan skilja sig åt Många avsnitt i denna utlösnings definition är valfria. Utlösningens beteende beror på om det finns avsnitt eller inte.

```json
"<APIConnection_trigger_name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": { "<retry-behavior>" },
      "queries": { "<query-parameters>" }
   },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning |
|-------|------|-------------|
| <*APIConnection_trigger_name*> | Sträng | Namnet på utlösaren |
| <*anslutnings namn*> | Sträng | Namnet på anslutningen till det hanterade API som arbets flödet använder |
| <*metod-typ*> | Sträng | HTTP-metoden för att kommunicera med hanterade API: "GET", "placera", "POST", "PATCH", "ta bort" |
| <*API-åtgärd*> | Sträng | API-åtgärden som ska anropas |
| <*tidsenhet*> | Sträng | Den tidsenhet som beskriver hur ofta utlösaren utlöses: "sekund", "minut", "timme", "dag", "vecka", "månad" |
| <*antal-tidsenheter*> | Integer | Ett värde som anger hur ofta utlösaren utlöses utifrån frekvensen, vilket är antalet tidsenheter som ska vänta tills utlösaren utlöses igen <p>Här följer de lägsta och högsta intervallen: <p>– Månad: 1-16 månader </br>– Dag: 1-500 dagar </br>– Timme: 1 – 12000 timmar </br>-Minute: 1 – 72000 minuter </br>-Sekund: 1 – 9999999 sekunder<p>Om intervallet till exempel är 6 och frekvensen är "månad" är upprepningen var 6: a månad. |
||||

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*återförsök-beteende*> | JSON-objekt | Anpassar återförsök för tillfälliga fel som har status koden 408, 429 och 5XX och eventuella anslutnings undantag. Mer information finns i [principer för återförsök](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*fråga – parametrar*> | JSON-objekt | Alla frågeparametrar som ska ingå i API-anropet. Objektet läggs till exempel `"queries": { "api-version": "2018-01-01" }` `?api-version=2018-01-01` till i anropet. | 
| <*Max-körningar*> | Integer | Som standard körs arbets flödes instanser på samma tidpunkt (samtidigt eller parallellt) upp till [standard gränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra den här gränsen genom att ange ett nytt <*antal*>s värde, se [ändra utlösare samtidighet](#change-trigger-concurrency). | 
| <*Max-körning-kö*> | Integer | När arbets flödet redan kör det maximala antalet instanser, som du kan ändra baserat på `runtimeConfiguration.concurrency.runs` egenskapen, placeras alla nya körningar i kön upp till [standard gränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra standard gränsen, se [begränsningen för ändrings väntande körningar](#change-waiting-runs). | 
| <*splitOn – uttryck*> | Sträng | För utlösare som returnerar matriser refererar det här uttrycket till den matris som ska användas så att du kan skapa och köra en arbets flödes instans för varje mat ris objekt, i stället för att använda en "for each"-loop. <p>Det här uttrycket representerar till exempel ett objekt i matrisen som returneras i utlösarens bröd innehåll: `@triggerbody()?['value']` |
| <*åtgärd – alternativ*> | Sträng | Du kan ändra standard beteendet genom att ange `operationOptions` egenskapen. Mer information finns i [Åtgärds alternativ](#operation-options). |
||||

*Utdata*
 
| Element | Typ | Beskrivning |
|---------|------|-------------|
| sidhuvud | JSON-objekt | Rubrikerna från svaret |
| body | JSON-objekt | Texten från svaret |
| statuskod | Integer | Status koden från svaret |
|||| 

*Exempel*

Denna Utlös ande definition söker efter e-post varje dag i Inkorgen för ett arbets-eller skol konto:

```json
"When_a_new_email_arrives": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "get",
      "path": "/Mail/OnNewEmail",
      "queries": {
          "fetchOnlyWithAttachment": false,
          "folderPath": "Inbox",
          "importance": "Any",
          "includeAttachments": false
      }
   },
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

<a name="apiconnectionwebhook-trigger"></a>

### <a name="apiconnectionwebhook-trigger"></a>ApiConnectionWebhook-utlösare

Den här utlösaren skickar en prenumerations förfrågan till en slut punkt med hjälp av ett [Microsoft-hanterat API](../connectors/apis-list.md), som ger en *callback-URL* till den plats där slut punkten kan skicka ett svar och väntar på att slut punkten ska svara. Mer information finns i [slut punkts prenumerationer](#subscribe-unsubscribe).

```json
"<ApiConnectionWebhook_trigger_name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
          "NotificationUrl": "@{listCallbackUrl()}"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "retryPolicy": { "<retry-behavior>" },
      "queries": "<query-parameters>"
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*anslutnings namn*> | Sträng | Namnet på anslutningen till det hanterade API som arbets flödet använder | 
| <*brödtext – innehåll*> | JSON-objekt | Meddelande innehåll som ska skickas som nytto Last till det hanterade API: et | 
||||

*Valfritt*

| Värde | Typ | Beskrivning |
|-------|------|-------------|
| <*återförsök-beteende*> | JSON-objekt | Anpassar återförsök för tillfälliga fel som har status koden 408, 429 och 5XX och eventuella anslutnings undantag. Mer information finns i [principer för återförsök](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| <*fråga – parametrar*> | JSON-objekt | Alla frågeparametrar som ska ingå i API-anropet <p>Objektet läggs till exempel `"queries": { "api-version": "2018-01-01" }` `?api-version=2018-01-01` till i anropet. |
| <*Max-körningar*> | Integer | Som standard körs arbets flödes instanser på samma tidpunkt (samtidigt eller parallellt) upp till [standard gränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra den här gränsen genom att ange ett nytt <*antal*>s värde, se [ändra utlösare samtidighet](#change-trigger-concurrency). |
| <*Max-körning-kö*> | Integer | När arbets flödet redan kör det maximala antalet instanser, som du kan ändra baserat på `runtimeConfiguration.concurrency.runs` egenskapen, placeras alla nya körningar i kön upp till [standard gränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra standard gränsen, se [begränsningen för ändrings väntande körningar](#change-waiting-runs). | 
| <*splitOn – uttryck*> | Sträng | För utlösare som returnerar matriser refererar det här uttrycket till den matris som ska användas så att du kan skapa och köra en arbets flödes instans för varje mat ris objekt, i stället för att använda en "for each"-loop. <p>Det här uttrycket representerar till exempel ett objekt i matrisen som returneras i utlösarens bröd innehåll: `@triggerbody()?['value']` |
| <*åtgärd – alternativ*> | Sträng | Du kan ändra standard beteendet genom att ange `operationOptions` egenskapen. Mer information finns i [Åtgärds alternativ](#operation-options). | 
|||| 

*Exempel*

Denna Utlös ande definition prenumererar på Office 365 Outlook API, tillhandahåller en callback-URL till API-slutpunkten och väntar på att slut punkten ska svara när ett nytt e-postmeddelande tas emot.

```json
"When_a_new_email_arrives_(webhook)": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
         "NotificationUrl": "@{listCallbackUrl()}" 
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "path": "/MailSubscription/$subscriptions",
      "queries": {
          "folderPath": "Inbox",
          "hasAttachment": "Any",
          "importance": "Any"
      }
   },
   "splitOn": "@triggerBody()?['value']"
}
```

<a name="http-trigger"></a>

### <a name="http-trigger"></a>HTTP-utlösare

Den här utlösaren skickar en begäran till den angivna HTTP-eller HTTPS-slutpunkten baserat på det angivna upprepnings schemat. Utlösaren kontrollerar sedan svaret för att avgöra om arbets flödet körs. Mer information finns i [anropa tjänst slut punkter via http eller https från Azure Logic Apps](../connectors/connectors-native-http.md).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": "<query-parameters>",
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      }
   },
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Obligatoriskt*

| Egenskap | Värde | Typ | Beskrivning |
|----------|-------|------|-------------|
| `method` | <*metod-typ*> | Sträng | Den metod som ska användas för att skicka utgående begäran: "GET", "placera", "POST", "PATCH" eller "DELETE" |
| `uri` | <*HTTP-eller-HTTPS-Endpoint-URL*> | Sträng | URL: en för HTTP-eller HTTPS-slutpunkt där du vill skicka den utgående begäran. Maximal sträng storlek: 2 KB <p>För en Azure-tjänst eller resurs inkluderar denna URI-syntax resurs-ID och sökvägen till den resurs som du vill få åtkomst till. |
| `frequency` | <*tidsenhet*> | Sträng | Den tidsenhet som beskriver hur ofta utlösaren utlöses: "sekund", "minut", "timme", "dag", "vecka", "månad" |
| `interval` | <*antal-tidsenheter*> | Integer | Ett värde som anger hur ofta utlösaren utlöses utifrån frekvensen, vilket är antalet tidsenheter som ska vänta tills utlösaren utlöses igen <p>Här följer de lägsta och högsta intervallen: <p>– Månad: 1-16 månader </br>– Dag: 1-500 dagar </br>– Timme: 1 – 12000 timmar </br>-Minute: 1 – 72000 minuter </br>-Sekund: 1 – 9999999 sekunder<p>Om intervallet till exempel är 6 och frekvensen är "månad" är upprepningen var 6: a månad. |
|||||

*Valfritt*

| Egenskap | Värde | Typ | Beskrivning |
|----------|-------|------|-------------|
| `headers` | <*rubrik – innehåll*> | JSON-objekt | Alla rubriker som du måste inkludera i begäran <p>Om du till exempel vill ange språk och typ: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*fråga – parametrar*> | JSON-objekt | Alla frågeparametrar som du behöver använda i begäran <p>Objektet läggs till exempel `"queries": { "api-version": "2018-01-01" }` `?api-version=2018-01-01` till i begäran. |
| `body` | <*brödtext – innehåll*> | JSON-objekt | Meddelande innehållet som ska skickas som nytto last med begäran |
| `authentication` | <*autentisering-typ-och-Property-Values*> | JSON-objekt | Den autentiseringsmetod som begäran använder för att autentisera utgående begär Anden. Mer information finns i [lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). Efter Scheduler `authority` stöds egenskapen. Om inget värde anges är standardvärdet `https://management.azure.com/` , men du kan använda ett annat värde. |
| `retryPolicy` > `type` | <*återförsök-beteende*> | JSON-objekt | Anpassar återförsök för tillfälliga fel som har status koden 408, 429 och 5XX och eventuella anslutnings undantag. Mer information finns i [principer för återförsök](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| `runs` | <*Max-körningar*> | Integer | Som standard körs arbets flödes instanser på samma tidpunkt (samtidigt eller parallellt) upp till [standard gränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra den här gränsen genom att ange ett nytt <*antal*>s värde, se [ändra utlösare samtidighet](#change-trigger-concurrency). |
| `maximumWaitingRuns` | <*Max-körning-kö*> | Integer | När arbets flödet redan kör det maximala antalet instanser, som du kan ändra baserat på `runtimeConfiguration.concurrency.runs` egenskapen, placeras alla nya körningar i kön upp till [standard gränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra standard gränsen, se [begränsningen för ändrings väntande körningar](#change-waiting-runs). |
| `operationOptions` | <*åtgärd – alternativ*> | Sträng | Du kan ändra standard beteendet genom att ange `operationOptions` egenskapen. Mer information finns i [Åtgärds alternativ](#operation-options). |
|||||

*Utdata*

| Element | Typ | Beskrivning |
|---------|------|-------------|
| `headers` | JSON-objekt | Rubrikerna från svaret |
| `body` | JSON-objekt | Texten från svaret |
| `status code` | Integer | Status koden från svaret |
||||

*Krav för inkommande begär Anden*

För att fungera bra med din Logi Kap par måste slut punkten följa ett särskilt utlösnings mönster eller kontrakt och identifiera dessa svars egenskaper:

| Egenskap | Krävs | Beskrivning |
|----------|----------|-------------|
| Statuskod | Ja | Status koden "200 OK" startar en körning. All annan status kod startar inte en körning. |
| Nytt försök-efter-rubrik | Nej | Antalet sekunder tills din Logic-app avsöker slut punkten igen |
| Plats rubrik | Nej | Den URL som ska anropas vid nästa avsöknings intervall. Om inget anges används den ursprungliga URL: en. |
|||| 

*Exempel beteenden för olika begär Anden*

| Statuskod | Försök igen efter | Beteende | 
|-------------|-------------|----------|
| 200 | alternativet | Kör arbets flödet och kontrol lera igen för mer data efter den definierade upprepningen. | 
| 200 | 10 sekunder | Kör arbets flödet och kontrol lera igen för mer data efter 10 sekunder. |  
| 202 | 60 sekunder | Utlös inte arbets flödet. Nästa försök görs om en minut, beroende på den definierade upprepningen. Om den definierade upprepningen är mindre än en minut, har huvudet för nytt försök att gälla företräde. Annars används den definierade upprepningen. | 
| 400 | alternativet | Felaktig begäran, kör inte arbets flödet. Om inget anges `retryPolicy` används standard principen. När antalet försök har nåtts kontrollerar utlösaren igen för data efter den definierade upprepningen. | 
| 500 | alternativet| Server fel, kör inte arbets flödet. Om inget anges `retryPolicy` används standard principen. När antalet försök har nåtts kontrollerar utlösaren igen för data efter den definierade upprepningen. | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>HTTPWebhook-utlösare  

Den här utlösaren gör din Logi ringbar genom att skapa en slut punkt som kan registrera en prenumeration genom att anropa den angivna slut punkts webb adressen. När du skapar den här utlösaren i ditt arbets flöde gör en utgående begäran anropet att registrera prenumerationen. På så sätt kan utlösaren börja lyssna efter händelser. När en åtgärd gör den här utlösaren ogiltig, gör en utgående begäran automatiskt anropet för att avbryta prenumerationen. Mer information finns i [slut punkts prenumerationer](#subscribe-unsubscribe).

Du kan också ange [asynkrona gränser](#asynchronous-limits) för en **HTTPWebhook** -utlösare. Utlösarens beteende beror på de avsnitt som du använder eller utelämnar.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<endpoint-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "retryPolicy": { "<retry-behavior>" }
         },
      },
      "unsubscribe": {
         "method": "<method-type>",
         "url": "<endpoint-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" }
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

Vissa värden, till exempel <*metod-typ*>, är tillgängliga för både- `"subscribe"` och- `"unsubscribe"` objekten.

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*metod-typ*> | Sträng | HTTP-metoden som ska användas för prenumerations förfrågan: "GET", "placera", "POST", "PATCH" eller "DELETE" | 
| <*slut punkt-prenumerations-URL*> | Sträng | Slut punkts-URL: en där prenumerations förfrågan ska skickas | 
|||| 

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*metod-typ*> | Sträng | HTTP-metoden som ska användas för avbrottsbegäran: "GET", "placera", "POST", "PATCH" eller "DELETE" | 
| <*slut punkt-Avsluta prenumeration-URL*> | Sträng | Slut punkts-URL: en dit begäran om uppsägning ska skickas | 
| <*brödtext – innehåll*> | Sträng | Meddelande innehåll som ska skickas i prenumerationen eller uppsägnings förfrågan | 
| <*typ av autentisering*> | JSON-objekt | Den autentiseringsmetod som begäran använder för att autentisera utgående begär Anden. Mer information finns i [lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). |
| <*återförsök-beteende*> | JSON-objekt | Anpassar återförsök för tillfälliga fel som har status koden 408, 429 och 5XX och eventuella anslutnings undantag. Mer information finns i [principer för återförsök](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*Max-körningar*> | Integer | Som standard körs alla arbets flödes instanser samtidigt (samtidigt eller parallellt) upp till [standard gränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra den här gränsen genom att ange ett nytt <*antal*>s värde, se [ändra utlösare samtidighet](#change-trigger-concurrency). | 
| <*Max-körning-kö*> | Integer | När arbets flödet redan kör det maximala antalet instanser, som du kan ändra baserat på `runtimeConfiguration.concurrency.runs` egenskapen, placeras alla nya körningar i kön upp till [standard gränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra standard gränsen, se [begränsningen för ändrings väntande körningar](#change-waiting-runs). | 
| <*åtgärd – alternativ*> | Sträng | Du kan ändra standard beteendet genom att ange `operationOptions` egenskapen. Mer information finns i [Åtgärds alternativ](#operation-options). | 
|||| 

*Utdata* 

| Element | Typ | Beskrivning |
|---------|------|-------------| 
| sidhuvud | JSON-objekt | Rubrikerna från svaret | 
| body | JSON-objekt | Texten från svaret | 
| statuskod | Integer | Status koden från svaret | 
|||| 

*Exempel*

Den här utlösaren skapar en prenumeration på den angivna slut punkten, tillhandahåller en unik återanrops-URL och väntar på nya publicerade teknik artiklar.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "POST",
         "uri": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{listCallbackUrl()}",
            "hub.mode": "subscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         },
      },
      "unsubscribe": {
         "method": "POST",
         "url": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
            "hub.mode": "unsubscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         }
      }
   }
}
```

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Upprepnings utlösare  

Den här utlösaren körs baserat på det angivna upprepnings schemat och ger ett enkelt sätt att skapa ett arbets flöde som körs regelbundet.

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
      "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
      "timeZone": "<time-zone>",
      "schedule": {
         // Applies only when frequency is Day or Week. Separate values with commas.
         "hours": [ <one-or-more-hour-marks> ], 
         // Applies only when frequency is Day or Week. Separate values with commas.
         "minutes": [ <one-or-more-minute-marks> ], 
         // Applies only when frequency is Week. Separate values with commas.
         "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
      }
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*tidsenhet*> | Sträng | Den tidsenhet som beskriver hur ofta utlösaren utlöses: "sekund", "minut", "timme", "dag", "vecka", "månad" | 
| <*antal-tidsenheter*> | Integer | Ett värde som anger hur ofta utlösaren utlöses utifrån frekvensen, vilket är antalet tidsenheter som ska vänta tills utlösaren utlöses igen <p>Här följer de lägsta och högsta intervallen: <p>– Månad: 1-16 månader </br>– Dag: 1-500 dagar </br>– Timme: 1 – 12000 timmar </br>-Minute: 1 – 72000 minuter </br>-Sekund: 1 – 9999999 sekunder<p>Om intervallet till exempel är 6 och frekvensen är "månad" är upprepningen var 6: a månad. | 
|||| 

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*Start-Date-Time-with-format-ÅÅÅÅ-MM-DDThh: mm: SS*> | Sträng | Start datum och-tid i det här formatet: <p>ÅÅÅÅ-MM-DDThh: mm: SS om du anger en tidszon <p>\- eller - <p>ÅÅÅÅ-MM-DDThh: mm: ssZ om du inte anger en tidszon <p>Om du till exempel vill ha 18 september 2017 på 2:00 PM anger du "2017-09-18T14:00:00" och anger en tidszon som t. ex. "Pacific Standard Time" eller anger "2017-09-18T14:00:00Z" utan en tidszon. <p>**Obs:** Den här start tiden har högst 49 år i framtiden och måste följa [8601 ISO-tiden för datum/tid](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) i [UTC-datum format](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), men utan en [UTC-förskjutning](https://en.wikipedia.org/wiki/UTC_offset). Om du inte anger en tidszon måste du lägga till bokstaven "Z" i slutet utan blank steg. Detta "Z" avser motsvarande [nautiska tid](https://en.wikipedia.org/wiki/Nautical_time). <p>För enkla scheman är start tiden den första förekomsten, medan utlösaren i komplexa scheman inte utlöses tidigare än start tiden. Mer information om start datum och tider finns i [skapa och schemalägga uppgifter som körs regelbundet](../connectors/connectors-native-recurrence.md). | 
| <*tidszon*> | Sträng | Gäller endast när du anger en start tid eftersom den här utlösaren inte accepterar [UTC-förskjutning](https://en.wikipedia.org/wiki/UTC_offset). Ange den tidszon som du vill använda. | 
| <*en-eller-fler-timmarsformat*> | Heltals-eller heltals mat ris | Om du anger "dag" eller "vecka" för `frequency` kan du ange ett eller flera heltal från 0 till 23, avgränsade med kommatecken, som de timmar på dagen då du vill köra arbets flödet. <p>Om du till exempel anger "10", "12" och "14" får du 10 AM, 12 PM och 2 PM som timvärdet. | 
| <*en eller flera minuter-märken*> | Heltals-eller heltals mat ris | Om du anger "dag" eller "vecka" för `frequency` kan du ange ett eller flera heltal från 0 till 59, avgränsade med kommatecken, som minuter i timmen när du vill köra arbets flödet. <p>Du kan till exempel ange "30" som minut märke och använda föregående exempel för timmar på dagen, du får 10:30 AM, 12:30 PM och 2:30 PM. | 
| weekDays | Sträng eller sträng mat ris | Om du anger "vecka" för `frequency` kan du ange en eller flera dagar, avgränsade med kommatecken, när du vill köra arbets flödet: "måndag", "tisdag", "onsdag", "torsdag", "fredag", "lördag" och "söndag" | 
| <*Max-körningar*> | Integer | Som standard körs alla arbets flödes instanser samtidigt (samtidigt eller parallellt) upp till [standard gränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra den här gränsen genom att ange ett nytt <*antal*>s värde, se [ändra utlösare samtidighet](#change-trigger-concurrency). | 
| <*Max-körning-kö*> | Integer | När arbets flödet redan kör det maximala antalet instanser, som du kan ändra baserat på `runtimeConfiguration.concurrency.runs` egenskapen, placeras alla nya körningar i kön upp till [standard gränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra standard gränsen, se [begränsningen för ändrings väntande körningar](#change-waiting-runs). | 
| <*åtgärd – alternativ*> | Sträng | Du kan ändra standard beteendet genom att ange `operationOptions` egenskapen. Mer information finns i [Åtgärds alternativ](#operation-options). | 
|||| 

*Exempel 1*

Den här grundläggande upprepnings utlösaren körs dagligen:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

*Exempel 2*

Du kan ange start datum och start tid för utlösaren. Den här upprepnings utlösaren startar på det angivna datumet och aktive ras dagligen:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1,
      "startTime": "2017-09-18T00:00:00Z"
   }
}
```

*Exempel 3*

Den här upprepnings utlösaren startar den 9 september 2017 vid 2:00 PM, och aktive ras varje måndag kl. 10:30 AM, 12:30 EM och 2:30 EM Pacific, normal tid:

``` json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "hours": [ 10, 12, 14 ],
         "minutes": [ 30 ],
         "weekDays": [ "Monday" ]
      },
      "startTime": "2017-09-07T14:00:00",
      "timeZone": "Pacific Standard Time"
   }
}
```

Mer information plus exempel för den här utlösaren finns i [skapa och schemalägga aktiviteter som körs regelbundet](../connectors/connectors-native-recurrence.md).

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Begär utlösare

Den här utlösaren gör din Logi ringbar genom att skapa en slut punkt som kan ta emot inkommande begär Anden. För den här utlösaren anger du ett JSON-schema som beskriver och validerar den nytto last eller de indata som utlösaren tar emot från den inkommande begäran. Schemat gör det också lättare att referera till utlösarens egenskaper från senare åtgärder i arbets flödet.

För att anropa den här utlösaren måste du använda `listCallbackUrl` API: et, som beskrivs i [REST API för arbets flödes tjänsten](/rest/api/logic/workflows). Information om hur du använder den här utlösaren som en HTTP-slutpunkt finns i [anropa, utlösa eller kapsla arbets flöden med HTTP-slutpunkter](../logic-apps/logic-apps-http-endpoint.md).

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "<method-type>",
      "relativePath": "<relative-path-for-accepted-parameter>",
      "schema": {
         "type": "object",
         "properties": { 
            "<property-name>": {
               "type": "<property-type>"
            }
         },
         "required": [ "<required-properties>" ]
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      },
   },
   "operationOptions": "<operation-option>"
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*egenskap-namn*> | Sträng | Namnet på en egenskap i JSON-schemat, som beskriver nytto lasten | 
| <*egenskap-typ*> | Sträng | Egenskapens typ | 
|||| 

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*metod-typ*> | Sträng | Metoden som inkommande begär Anden måste använda för att anropa din Logic app: "GET", "placera", "POST", "PATCH", "ta bort" |
| <*relativ sökväg-för-godtagen parameter*> | Sträng | Den relativa sökvägen för parametern som URL: en för din slut punkt kan acceptera | 
| <*obligatoriska – egenskaper*> | Matris | En eller flera egenskaper som kräver värden | 
| <*Max-körningar*> | Integer | Som standard körs alla arbets flödes instanser samtidigt (samtidigt eller parallellt) upp till [standard gränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra den här gränsen genom att ange ett nytt <*antal*>s värde, se [ändra utlösare samtidighet](#change-trigger-concurrency). | 
| <*Max-körning-kö*> | Integer | När arbets flödet redan kör det maximala antalet instanser, som du kan ändra baserat på `runtimeConfiguration.concurrency.runs` egenskapen, placeras alla nya körningar i kön upp till [standard gränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra standard gränsen, se [begränsningen för ändrings väntande körningar](#change-waiting-runs). | 
| <*åtgärd – alternativ*> | Sträng | Du kan ändra standard beteendet genom att ange `operationOptions` egenskapen. Mer information finns i [Åtgärds alternativ](#operation-options). | 
|||| 

*Exempel*

Den här utlösaren anger att en inkommande begäran måste använda HTTP POST-metoden för att anropa utlösaren och innehåller ett schema som verifierar indata från den inkommande begäran:

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "POST",
      "schema": {
         "type": "object",
         "properties": {
            "customerName": {
               "type": "String"
            },
            "customerAddress": { 
               "type": "Object",
               "properties": {
                  "streetAddress": {
                     "type": "string"
                  },
                  "city": {
                     "type": "string"
                  }
               }
            }
         }
      }
   }
}
```

<a name="trigger-conditions"></a>

## <a name="trigger-conditions"></a>Utlösarvillkor

För alla utlösare och endast utlösare kan du inkludera en matris som innehåller ett eller flera uttryck för villkor som avgör om arbets flödet ska köras. Om du vill lägga till `conditions` egenskapen i en utlösare i arbets flödet öppnar du din Logic app i kodvyn.

Du kan till exempel ange att en utlösare endast utlöses när en webbplats returnerar ett internt Server fel genom att referera till utlösarens status kod i `conditions` egenskapen:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Hour",
      "interval": 1
   },
   "conditions": [ {
      "expression": "@equals(triggers().code, 'InternalServerError')"
   } ]
}
```

Som standard utlöses en utlösare först efter att ha gett svaret "200 OK". När ett uttryck refererar till en utlösare status kod ersätts utlösarens standard beteende. Så om du vill att utlösaren ska utlösa för mer än en status kod, till exempel status koden "200" och "201", måste du ta med det här uttrycket som villkor:

`@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="trigger-multiple-runs"></a>Utlösa flera körningar

Om utlösaren returnerar en matris för din Logic app att bearbeta, kan ibland en "for each"-loop ta för lång tid att bearbeta varje mat ris objekt. I stället kan du använda egenskapen **SplitOn** i utlösaren för att *Avgruppera* matrisen. Debatchen delar upp mat ris objekten och startar en ny arbets flödes instans som körs för varje mat ris objekt. Den här metoden är användbar, till exempel när du vill avsöka en slut punkt som kan returnera flera nya objekt mellan avsöknings intervall. För det maximala antalet mat ris objekt som **SplitOn** kan bearbeta i en enda Logic app-körning, se [gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). 

> [!NOTE]
> Du kan inte använda **SplitOn** med ett synkront svars mönster. Alla arbets flöden som använder **SplitOn** och innehåller en svars åtgärd körs asynkront och skickar omedelbart ett `202 ACCEPTED` svar.
>
> När utlösarens concurrency är aktive rad minskas [SplitOn-gränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) avsevärt. Om antalet objekt överskrider den här gränsen är SplitOn-funktionen inaktive rad.
 
Om utlösaren Swagger-filen beskriver en nytto last som är en matris, läggs egenskapen **SplitOn** automatiskt till i utlösaren. Annars lägger du till den här egenskapen inuti svars nytto lasten som innehåller den matris som du vill Avgruppera.

*Exempel*

Anta att du har ett API som returnerar följande svar: 
  
```json
{
   "Status": "Succeeded",
   "Rows": [ 
      { 
         "id": 938109380,
         "name": "customer-name-one"
      },
      {
         "id": 938109381,
         "name": "customer-name-two"
      }
   ]
}
```

Din Logic app behöver bara innehållet från matrisen i `Rows` , så du kan skapa en utlösare som det här exemplet:

``` json
"HTTP_Debatch": {
   "type": "Http",
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
   "recurrence": {
      "frequency": "Second",
      "interval": 1
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> Om du använder `SplitOn` kommandot kan du inte hämta de egenskaper som ligger utanför matrisen. För det här exemplet kan du inte hämta `status` egenskapen i svaret som returnerades från API: et.
> 
> För att undvika ett haveri om `Rows` egenskapen inte finns använder det här exemplet `?` operatorn.

Din arbets flödes definition kan nu användas `@triggerBody().name` för att hämta `name` värdena, som kommer `"customer-name-one"` från den första körningen och `"customer-name-two"` från den andra körningen. Därför ser utlösare ut utdata som de här exemplen:

```json
{
   "body": {
      "id": 938109380,
      "name": "customer-name-one"
   }
}
```

```json
{
   "body": {
      "id": 938109381,
      "name": "customer-name-two"
   }
}
```

<a name="actions-overview"></a>

## <a name="actions-overview"></a>Översikt över åtgärder

Azure Logic Apps innehåller olika åtgärds typer – var och en med olika indata som definierar en åtgärds unika beteende. Åtgärder har dessa hög nivå element, men några är valfria:

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { 
      "<input-name>": { "<input-value>" },
      "retryPolicy": "<retry-behavior>" 
   },
   "runAfter": { "<previous-trigger-or-action-status>" },
   "runtimeConfiguration": { "<runtime-config-options>" },
   "operationOptions": "<operation-option>"
},
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------|
| <*åtgärds namn*> | Sträng | Åtgärdens namn | 
| <*åtgärds typ*> | Sträng | Åtgärds typ, till exempel "http" eller "ApiConnection"| 
| <*inmatat namn*> | Sträng | Namnet på en indatatyp som definierar åtgärdens beteende | 
| <*Ingående värde*> | Önskade | Indatavärdet, som kan vara en sträng, ett heltal, ett JSON-objekt och så vidare | 
| <*tidigare-utlösare-eller-åtgärd-status*> | JSON-objekt | Namn och resulterande status för utlösaren eller åtgärden som måste köras omedelbart innan denna aktuella åtgärd kan köras | 
|||| 

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------|
| <*återförsök-beteende*> | JSON-objekt | Anpassar återförsök för tillfälliga fel som har status koden 408, 429 och 5XX och eventuella anslutnings undantag. Mer information finns i principer för återförsök. | 
| <*runtime-Config-Options*> | JSON-objekt | För vissa åtgärder kan du ändra åtgärdens beteende i kör tid genom att ange `runtimeConfiguration` Egenskaper. Mer information finns i [konfigurations inställningar för körning](#runtime-config-options). | 
| <*åtgärd – alternativ*> | Sträng | För vissa åtgärder kan du ändra standard beteendet genom att ange `operationOptions` egenskapen. Mer information finns i [Åtgärds alternativ](#operation-options). | 
|||| 

## <a name="action-types-list"></a>Lista med åtgärds typer

Här följer några vanliga åtgärds typer: 

* [Inbyggda åtgärds typer](#built-in-actions) , till exempel följande exempel: 

  * [**Http**](#http-action) för anropande slut punkter över http eller https

  * [**Svar**](#response-action) för att svara på begär Anden

  * [**Köra JavaScript-kod**](#run-javascript-code) för att köra JavaScript-kodfragment

  * [**Funktion**](#function-action) för att anropa Azure Functions

  * Data åtgärds åtgärder som [](#join-action)att ansluta [**, skapa,**](#compose-action) [**tabell**](#table-action), [**välja**](#select-action)och andra som skapar eller transformerar data från olika indata

  * [**Arbets flöde**](#workflow-action) för att anropa ett annat Logic app-arbetsflöde

* [Hanterade API-åtgärds typer](#managed-api-actions) som [**ApiConnection**](#apiconnection-action) och [**ApiConnectionWebHook**](#apiconnectionwebhook-action) som anropar olika anslutningar och API: er som hanteras av Microsoft, till exempel Azure Service Bus, Office 365 Outlook, Power BI, Azure Blob Storage, OneDrive, GitHub och mycket mer

* [Kontrol lera arbets flödes åtgärds typer](#control-workflow-actions) , till exempel [**om**](#if-action), [**förgrunds**](#foreach-action), [**växling**](#switch-action), [**omfång**](#scope-action)och [**till**](#until-action), som innehåller andra åtgärder och hjälper dig att organisera arbets flödes körningen

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>Inbyggda åtgärder

| Åtgärdstyp | Beskrivning | 
|-------------|-------------| 
| [**Compose**](#compose-action) | Skapar ett enstaka utdata från indata, vilket kan ha olika typer. | 
| [**Kör JavaScript-kod**](#run-javascript-code) | Kör kods tycken med JavaScript-kod som passar inom vissa villkor. Kod krav och mer information finns i [lägga till och köra kodfragment med infogad kod](../logic-apps/logic-apps-add-run-inline-code.md). |
| [**Funktioner**](#function-action) | Anropar en Azure-funktion. | 
| [**HTTP**](#http-action) | Anropar en HTTP-slutpunkt. | 
| [**Ansluta**](#join-action) | Skapar en sträng från alla objekt i en matris och avgränsar objekten med ett angivet avgränsnings tecken. | 
| [**Parsa JSON**](#parse-json-action) | Skapar användarvänliga tokens från egenskaper i JSON-innehåll. Du kan sedan referera till dessa egenskaper genom att inkludera tokens i din Logic app. | 
| [**Söka i data**](#query-action) | Skapar en matris från objekt i en annan matris baserat på ett villkor eller filter. | 
| [**Svarsåtgärder**](#response-action) | Skapar ett svar på ett inkommande samtal eller en begäran. | 
| [**Välj**](#select-action) | Skapar en matris med JSON-objekt genom att transformera objekt från en annan matris baserat på den angivna kartan. | 
| [**Tabell**](#table-action) | Skapar en CSV-eller HTML-tabell från en matris. | 
| [**Terminate**](#terminate-action) | Stoppar ett aktivt arbets flöde som körs. | 
| [**Wait**](#wait-action) | Pausar arbets flödet under en angiven varaktighet eller till angivet datum och tid. | 
| [**Arbetsflöde**](#workflow-action) | Kapslar in ett arbets flöde i ett annat arbets flöde. | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>Hanterade API-åtgärder

| Åtgärdstyp | Beskrivning | 
|-------------|-------------|  
| [**ApiConnection**](#apiconnection-action) | Anropar en HTTP-slutpunkt genom att använda ett [Microsoft-hanterat API](../connectors/apis-list.md). | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-action) | Fungerar som HTTP-webhook men använder en [Microsoft-hanterad API](../connectors/apis-list.md). | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>Kontroll av arbets flödes åtgärder

De här åtgärderna hjälper dig att styra arbets flödes körningen och inkludera andra åtgärder. Från utanför en kontroll arbets flödes åtgärd kan du direkt referera till åtgärder i den kontrollen arbets flödes åtgärd. Om du till exempel har en `Http` åtgärd i ett omfång kan du referera till `@body('Http')` uttrycket från var som helst i arbets flödet. Åtgärder som finns i en kontroll arbets flödes åtgärd kan dock endast köras efter andra åtgärder som befinner sig i samma kontroll arbets flödes struktur.

| Åtgärdstyp | Beskrivning | 
|-------------|-------------| 
| [**ForEach**](#foreach-action) | Kör samma åtgärder i en slinga för varje objekt i en matris. | 
| [**Eventuella**](#if-action) | Kör åtgärder baserat på om det angivna villkoret är sant eller falskt. | 
| [**Omfång**](#scope-action) | Kör åtgärder baserat på grupp status från en uppsättning åtgärder. | 
| [**Switch**](#switch-action) | Kör åtgärder som är indelade i fall när värden från uttryck, objekt eller tokens matchar de värden som anges i varje fall. | 
| [**Tills**](#until-action) | Kör åtgärder i en slinga tills det angivna villkoret är sant. | 
|||  

## <a name="actions---detailed-reference"></a>Åtgärder-detaljerad referens

<a name="apiconnection-action"></a>

### <a name="apiconnection-action"></a>APIConnection-åtgärd

Den här åtgärden skickar en HTTP-begäran till ett [Microsoft-hanterat API](../connectors/apis-list.md) och kräver information om API och parametrar plus en referens till en giltig anslutning. 

``` json
"<action-name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<api-name>']['connectionId']"
         },
         "<other-action-specific-input-properties>"        
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": "<retry-behavior>",
      "queries": { "<query-parameters>" },
      "<other-action-specific-properties>"
    },
    "runAfter": {}
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*åtgärds namn*> | Sträng | Namnet på åtgärden som tillhandahålls av kopplingen | 
| <*API-namn*> | Sträng | Namnet på det Microsoft-hanterade API som används för anslutningen | 
| <*metod-typ*> | Sträng | HTTP-metoden för att anropa API: "GET", "placera", "POST", "PATCH" eller "DELETE" | 
| <*API-åtgärd*> | Sträng | API-åtgärden som ska anropas | 
|||| 

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*andra-åtgärds-Specific-in-Properties*> | JSON-objekt | Alla andra ingångs egenskaper som gäller för den aktuella åtgärden | 
| <*återförsök-beteende*> | JSON-objekt | Anpassar återförsök för tillfälliga fel som har status koden 408, 429 och 5XX och eventuella anslutnings undantag. Mer information finns i [principer för återförsök](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*fråga – parametrar*> | JSON-objekt | Alla frågeparametrar som ska ingå i API-anropet. <p>Objektet läggs till exempel `"queries": { "api-version": "2018-01-01" }` `?api-version=2018-01-01` till i anropet. | 
| <*andra-åtgärds-/regionsspecifika egenskaper*> | JSON-objekt | Andra egenskaper som gäller för den här åtgärden | 
|||| 

*Exempel*

Den här definitionen beskriver åtgärden **skicka en e-post** för Office 365 Outlook Connector, som är ett Microsoft-hanterat API: 

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "Thank you for your membership!",
         "Subject": "Hello and welcome!",
         "To": "Sophie.Owen@contoso.com"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "POST",
      "path": "/Mail"
    },
    "runAfter": {}
}
```

<a name="apiconnection-webhook-action"></a>

### <a name="apiconnectionwebhook-action"></a>APIConnectionWebhook-åtgärd

Den här åtgärden skickar en prenumerations förfrågan via HTTP till en slut punkt med hjälp av ett [Microsoft-hanterat API](../connectors/apis-list.md), som ger en *callback-URL* till den plats där slut punkten kan skicka ett svar och väntar på att slut punkten ska svara. Mer information finns i [slut punkts prenumerationer](#subscribe-unsubscribe).

```json
"<action-name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<api-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "retryPolicy": "<retry-behavior>",
         "queries": { "<query-parameters>" },
         "<other-action-specific-input-properties>"
      },
      "unsubscribe": {
         "method": "<method-type>",
         "uri": "<api-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-type>" },
         "<other-action-specific-properties>"
      },
   },
   "runAfter": {}
}
```

Vissa värden, till exempel <*metod-typ*>, är tillgängliga för både- `"subscribe"` och- `"unsubscribe"` objekten.

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*åtgärds namn*> | Sträng | Namnet på åtgärden som tillhandahålls av kopplingen | 
| <*metod-typ*> | Sträng | HTTP-metoden som ska användas för att prenumerera eller ta bort från en slut punkt: "GET", "placera", "POST", "PATCH" eller "DELETE" | 
| <*API – prenumerera-URL*> | Sträng | Den URI som ska användas för att prenumerera på API: et | 
|||| 

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*API-avprenumerera-URL*> | Sträng | Den URI som ska användas för prenumerationen från API: et | 
| <*rubrik – innehåll*> | JSON-objekt | Alla rubriker som ska skickas i begäran <p>Om du till exempel vill ange språk och typ på en begäran: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*brödtext – innehåll*> | JSON-objekt | Alla meddelande innehåll som ska skickas i begäran | 
| <*typ av autentisering*> | JSON-objekt | Den autentiseringsmetod som begäran använder för att autentisera utgående begär Anden. Mer information finns i [lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). |
| <*återförsök-beteende*> | JSON-objekt | Anpassar återförsök för tillfälliga fel som har status koden 408, 429 och 5XX och eventuella anslutnings undantag. Mer information finns i [principer för återförsök](../logic-apps/logic-apps-exception-handling.md#retry-policies). | 
| <*fråga – parametrar*> | JSON-objekt | Alla frågeparametrar som ska ingå i API-anropet <p>Objektet läggs till exempel `"queries": { "api-version": "2018-01-01" }` `?api-version=2018-01-01` till i anropet. | 
| <*andra-åtgärds-Specific-in-Properties*> | JSON-objekt | Alla andra ingångs egenskaper som gäller för den aktuella åtgärden | 
| <*andra-åtgärds-/regionsspecifika egenskaper*> | JSON-objekt | Andra egenskaper som gäller för den här åtgärden | 
|||| 

Du kan också ange gränser för en **ApiConnectionWebhook** -åtgärd på samma sätt som [asynkrona begränsningar i http](#asynchronous-limits).

<a name="compose-action"></a>

### <a name="compose-action"></a>Skriv åtgärd

Den här åtgärden skapar en enstaka utdata från flera indata, inklusive uttryck. Både utdata och indata kan ha en typ som Azure Logic Apps inbyggt stöd för, till exempel matriser, JSON-objekt, XML och Binary. Du kan sedan använda åtgärdens utdata i andra åtgärder. 

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*Obligatoriskt* 

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*indata-till-Skriv*> | Valfri | Indata för att skapa en enskild utdata | 
|||| 

*Exempel 1*

<!-- markdownlint-disable MD038 -->
Denna åtgärds definition sammanfogar `abcdefg ` med ett avslutande blank steg och värdet `1234` :
<!-- markdownlint-enable MD038 -->

```json
"Compose": {
   "type": "Compose",
   "inputs": "abcdefg 1234",
   "runAfter": {}
},
```

Här är de utdata som den här åtgärden skapar:

`abcdefg 1234`

*Exempel 2*

Den här åtgärds definitionen sammanfogar en sträng variabel som innehåller `abcdefg` och en heltals variabel som innehåller `1234` :

```json
"Compose": {
   "type": "Compose",
   "inputs": "@{variables('myString')}@{variables('myInteger')}",
   "runAfter": {}
},
```

Här är de utdata som den här åtgärden skapar:

`"abcdefg1234"`

<a name="run-javascript-code"></a>

### <a name="execute-javascript-code-action"></a>Åtgärd för att köra JavaScript-kod

Den här åtgärden kör ett JavaScript-kodfragment och returnerar resultatet via en `Result` token som senare åtgärd kan referera till.

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "<JavaScript-code-snippet>",
      "explicitDependencies": {
         "actions": [ <previous-actions> ],
         "includeTrigger": true
      }
   },
   "runAfter": {}
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning |
|-------|------|-------------|
| <*JavaScript-kodfragment*> | Det varierar | Den JavaScript-kod som du vill köra. Kod krav och mer information finns i [lägga till och köra kodfragment med infogad kod](../logic-apps/logic-apps-add-run-inline-code.md). <p>I `code` attributet kan kodfragmentet använda det skrivskyddade `workflowContext` objektet som inmatade objekt. Det här objektet har under egenskaper som ger koden åtkomst till resultaten från utlösare och tidigare åtgärder i arbets flödet. Mer information om `workflowContext` objektet finns [i referens utlösare och åtgärds resultat i din kod](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext). |
||||

*Krävs i vissa fall*

`explicitDependencies`Attributet anger att du uttryckligen vill inkludera resultat från utlösaren, tidigare åtgärder eller både och som beroenden för kodfragmentet. Mer information om hur du lägger till dessa beroenden finns i [lägga till parametrar för infogad kod](../logic-apps/logic-apps-add-run-inline-code.md#add-parameters). 

För `includeTrigger` -attributet kan du ange `true` eller- `false` värden.

| Värde | Typ | Beskrivning |
|-------|------|-------------|
| <*föregående – åtgärder*> | Strängmatris | En matris med dina angivna åtgärds namn. Använd de åtgärds namn som visas i arbets flödes definitionen där åtgärds namn använder under streck (_), inte blank steg (""). |
||||

*Exempel 1*

Den här åtgärden kör kod som hämtar din Logic Apps namn och returnerar texten "Hej världen från \<logic-app-name> " som resultatet. I det här exemplet refererar koden till arbets flödets namn genom att komma åt `workflowContext.workflow.name` egenskapen via det skrivskyddade `workflowContext` objektet. Mer information om hur du använder `workflowContext` -objektet finns [i referens utlösare och åtgärds resultat i din kod](../logic-apps/logic-apps-add-run-inline-code.md#workflowcontext).

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var text = \"Hello world from \" + workflowContext.workflow.name;\r\n\r\nreturn text;"
   },
   "runAfter": {}
}
```

*Exempel 2*

Den här åtgärden kör kod i en Logic-app som utlöses när ett nytt e-postmeddelande anländer till ett arbets-eller skol konto. Logic app använder också en e-poståtgärd för att skicka godkännande som vidarebefordrar innehållet från den mottagna e-postmeddelandet tillsammans med en begäran om godkännande.

Koden extraherar e-postadresserna från utlösarens `Body` egenskap och returnerar adresserna tillsammans med `SelectedOption` egenskap svärdet från godkännande åtgärden. Åtgärden innehåller uttryckligen e-poståtgärden skicka godkännande som ett beroende i `explicitDependencies`  >  `actions` attributet.

```json
"Execute_JavaScript_Code": {
   "type": "JavaScriptCode",
   "inputs": {
      "code": "var re = /(([^<>()\\[\\]\\\\.,;:\\s@\"]+(\\.[^<>()\\[\\]\\\\.,;:\\s@\"]+)*)|(\".+\"))@((\\[[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}])|(([a-zA-Z\\-0-9]+\\.)+[a-zA-Z]{2,}))/g;\r\n\r\nvar email = workflowContext.trigger.outputs.body.Body;\r\n\r\nvar reply = workflowContext.actions.Send_approval_email_.outputs.body.SelectedOption;\r\n\r\nreturn email.match(re) + \" - \" + reply;\r\n;",
      "explicitDependencies": {
         "actions": [
            "Send_approval_email_"
         ]
      }
   },
   "runAfter": {}
}
```



<a name="function-action"></a>

### <a name="function-action"></a>Funktions åtgärd

Den här åtgärden anropar en [Azure-funktion](../azure-functions/functions-get-started.md)som skapats tidigare.

```json
"<Azure-function-name>": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "<Azure-function-ID>"
      },
      "method": "<method-type>",
      "headers": { "<header-content>" },
      "body": { "<body-content>" },
      "queries": { "<query-parameters>" } 
   },
   "runAfter": {}
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------|  
| <*Azure-Function-ID*> | Sträng | Resurs-ID för den Azure-funktion som du vill anropa. Här är formatet för det här värdet:<p>"/Subscriptions/<*Azure-Subscription-ID*>/ResourceGroups/<*Azure-resurs-grupp*>/providers/Microsoft.Web/Sites/<*Azure-function-app-Name*>/Functions/<*Azure-Function-Name*>" | 
| <*metod-typ*> | Sträng | HTTP-metoden som används för att anropa funktionen: "GET", "placera", "POST", "PATCH" eller "DELETE" <p>Om inget värde anges används "POST"-metoden som standard. | 
||||

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------|  
| <*rubrik – innehåll*> | JSON-objekt | Alla rubriker som ska skickas med anropet <p>Om du till exempel vill ange språk och typ på en begäran: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*brödtext – innehåll*> | JSON-objekt | Alla meddelande innehåll som ska skickas i begäran | 
| <*fråga – parametrar*> | JSON-objekt | Alla frågeparametrar som ska ingå i API-anropet <p>Objektet läggs till exempel `"queries": { "api-version": "2018-01-01" }` `?api-version=2018-01-01` till i anropet. | 
| <*andra-åtgärds-Specific-in-Properties*> | JSON-objekt | Alla andra ingångs egenskaper som gäller för den aktuella åtgärden | 
| <*andra-åtgärds-/regionsspecifika egenskaper*> | JSON-objekt | Andra egenskaper som gäller för den här åtgärden | 
||||

När du sparar din Logic app utför Logic Apps-motorn dessa kontroller för den refererade funktionen:

* Arbets flödet måste ha åtkomst till funktionen.

* Arbets flödet kan bara använda en HTTP-utlösare eller en generisk JSON-webhook-utlösare. 

  Logic Apps-motorn hämtar och cachelagrar utlösarens URL, som används vid körning. Men om en åtgärd gör en ogiltig verifiering av den cachelagrade URL: en, Miss lyckas **funktions** åtgärden vid körning. Åtgärda problemet genom att spara Logic-appen igen så att Logic app hämtar och cachelagrar utlösarens URL igen.

* Ingen väg har definierats för funktionen.

* Endast behörighets nivåer "function" och "Anonym" tillåts. 

*Exempel*

Den här åtgärds definitionen anropar den tidigare skapade funktionen "GetProductID":

```json
"GetProductID": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "/subscriptions/<XXXXXXXXXXXXXXXXXXXX>/resourceGroups/myLogicAppResourceGroup/providers/Microsoft.Web/sites/InventoryChecker/functions/GetProductID"
      },
      "method": "POST",
      "headers": { 
          "x-ms-date": "@utcnow()"
       },
      "body": { 
          "Product_ID": "@variables('ProductID')"
      }
   },
   "runAfter": {}
}
```

<a name="http-action"></a>

### <a name="http-action"></a>HTTP-åtgärd

Den här åtgärden skickar en begäran till den angivna HTTP-eller HTTPS-slutpunkten och kontrollerar svaret för att avgöra om arbets flödet körs. Mer information finns i [anropa tjänst slut punkter via http eller https från Azure Logic Apps](../connectors/connectors-native-http.md).

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>",
      "headers": { "<header-content>" },
      "queries": { "<query-parameters>" },
      "body": "<body-content>",
      "authentication": { "<authentication-type-and-property-values>" },
      "retryPolicy": {
         "type": "<retry-behavior>"
      },
   },
   "runAfter": {}
}
```

*Obligatoriskt*

| Egenskap | Värde | Typ | Beskrivning |
|----------|-------|------|-------------|
| `method` | <*metod-typ*> | Sträng | Den metod som ska användas för att skicka utgående begäran: "GET", "placera", "POST", "PATCH" eller "DELETE" |
| `uri` | <*HTTP-eller-HTTPS-Endpoint-URL*> | Sträng | URL: en för HTTP-eller HTTPS-slutpunkt där du vill skicka den utgående begäran. Maximal sträng storlek: 2 KB <p>För en Azure-tjänst eller resurs inkluderar denna URI-syntax resurs-ID och sökvägen till den resurs som du vill få åtkomst till. |
|||||

*Valfritt*

| Egenskap | Värde | Typ | Beskrivning |
|----------|-------|------|-------------|
| `headers` | <*rubrik – innehåll*> | JSON-objekt | Alla rubriker som du måste inkludera i begäran <p>Om du till exempel vill ange språk och typ: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| `queries` | <*fråga – parametrar*> | JSON-objekt | Alla frågeparametrar som du behöver använda i begäran <p>Objektet läggs till exempel `"queries": { "api-version": "2018-01-01" }` `?api-version=2018-01-01` till i anropet. |
| `body` | <*brödtext – innehåll*> | JSON-objekt | Meddelande innehållet som ska skickas som nytto last med begäran |
| `authentication` | <*autentisering-typ-och-Property-Values*> | JSON-objekt | Den autentiseringsmetod som begäran använder för att autentisera utgående begär Anden. Mer information finns i [lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound). Efter Scheduler `authority` stöds egenskapen. Om inget värde anges är standardvärdet `https://management.azure.com/` , men du kan använda ett annat värde. |
| `retryPolicy` > `type` | <*återförsök-beteende*> | JSON-objekt | Anpassar återförsök för tillfälliga fel som har status koden 408, 429 och 5XX och eventuella anslutnings undantag. Mer information finns i [principer för återförsök](../logic-apps/logic-apps-exception-handling.md#retry-policies). |
| <*andra-åtgärds-Specific-in-Properties*> | <*egenskap för indatamängd*> | JSON-objekt | Alla andra ingångs egenskaper som gäller för den aktuella åtgärden |
| <*andra-åtgärds-/regionsspecifika egenskaper*> | <*egenskap-värde*> | JSON-objekt | Andra egenskaper som gäller för den här åtgärden |
|||||

*Exempel*

Den här åtgärds definitionen hämtar de senaste nyheterna genom att skicka en begäran till den angivna slut punkten:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest"
   }
}
```

<a name="join-action"></a>

### <a name="join-action"></a>Kopplings åtgärd

Den här åtgärden skapar en sträng från alla objekt i en matris och avgränsar objekten med angivet avgränsnings tecken. 

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": <array>,
      "joinWith": "<delimiter>"
   },
   "runAfter": {}
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*lagringsmatriser*> | Matris | Matrisen eller uttrycket som innehåller käll objekt. Om du anger ett uttryck omger du det uttrycket med dubbla citat tecken. | 
| <*avgränsare*> | Enkel tecken sträng | Det tecken som avgränsar varje objekt i strängen | 
|||| 

*Exempel*

Anta att du har en tidigare skapad "myIntegerArray"-variabel som innehåller denna heltals mat ris: 

`[1,2,3,4]` 

Den här åtgärds definitionen hämtar värdena från variabeln genom att använda `variables()` funktionen i ett uttryck och skapar den här strängen med dessa värden, som avgränsas med kommatecken: `"1,2,3,4"`

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": "@variables('myIntegerArray')",
      "joinWith": ","
   },
   "runAfter": {}
}
```

<a name="parse-json-action"></a>

### <a name="parse-json-action"></a>Parsa JSON-åtgärd

Den här åtgärden skapar användarvänliga fält eller *tokens* från egenskaperna i JSON-innehåll. Du kan sedan komma åt dessa egenskaper i din Logic app genom att använda tokens i stället. Om du till exempel vill använda JSON-utdata från tjänster som Azure Service Bus och Azure Cosmos DB, kan du ta med den här åtgärden i din Logic app så att du enkelt kan referera till data i dessa utdata.

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": "<JSON-source>",
         "schema": { "<JSON-schema>" }
      },
      "runAfter": {}
},
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*JSON-källa*> | JSON-objekt | Det JSON-innehåll som du vill tolka | 
| <*JSON-schema*> | JSON-objekt | JSON-schemat som beskriver det underliggande JSON-innehållet, som åtgärden använder för att parsa käll-JSON-innehåll. <p>**Tips**: i Logic Apps Designer kan du antingen ange schemat eller ange ett exempel på en nytto Last så att åtgärden kan generera schemat. | 
|||| 

*Exempel*

Den här åtgärds definitionen skapar dessa token som du kan använda i ditt arbets flöde men endast i åtgärder som körs efter åtgärden **parsa JSON** :

`FirstName`, `LastName` och `Email`

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": {
         "Member": {
            "Email": "Sophie.Owen@contoso.com",
            "FirstName": "Sophie",
            "LastName": "Owen"
         }
      },
      "schema": {
         "type": "object",
         "properties": {
            "Member": {
               "type": "object",
               "properties": {
                  "Email": {
                     "type": "string"
                  },
                  "FirstName": {
                     "type": "string"
                  },
                  "LastName": {
                     "type": "string"
                  }
               }
            }
         }
      }
   },
   "runAfter": { }
},
```

I det här exemplet anger egenskapen "content" det JSON-innehåll som ska parsas för åtgärden. Du kan också ange det här JSON-innehållet som exempel nytto last för att skapa schemat.

```json
"content": {
   "Member": { 
      "FirstName": "Sophie",
      "LastName": "Owen",
      "Email": "Sophie.Owen@contoso.com"
   }
},
```

Egenskapen "schema" anger det JSON-schema som används för att beskriva JSON-innehållet:

```json
"schema": {
   "type": "object",
   "properties": {
      "Member": {
         "type": "object",
         "properties": {
            "FirstName": {
               "type": "string"
            },
            "LastName": {
               "type": "string"
            },
            "Email": {
               "type": "string"
            }
         }
      }
   }
}
```

<a name="query-action"></a>

### <a name="query-action"></a>Fråga åtgärd

Den här åtgärden skapar en matris från objekt i en annan matris baserat på ett angivet villkor eller filter.

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": <array>,
      "where": "<condition-or-filter>"
   },
   "runAfter": {}
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*lagringsmatriser*> | Matris | Matrisen eller uttrycket som innehåller käll objekt. Om du anger ett uttryck omger du det uttrycket med dubbla citat tecken. |
| <*villkor-eller-filter*> | Sträng | Villkoret som används för att filtrera objekt i käll mat ris <p>**Obs**: om inga värden uppfyller villkoret skapar åtgärden en tom matris. |
|||| 

*Exempel*

Den här åtgärds definitionen skapar en matris som har värden som är större än det angivna värdet, vilket är två:

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": [ 1, 3, 0, 5, 4, 2 ],
      "where": "@greater(item(), 2)"
   }
}
```

<a name="response-action"></a>

### <a name="response-action"></a>Svars åtgärd  

Den här åtgärden skapar nytto lasten för svaret på en HTTP-begäran. 

```json
"Response" {
    "type": "Response",
    "kind": "http",
    "inputs": {
        "statusCode": 200,
        "headers": { <response-headers> },
        "body": { <response-body> }
    },
    "runAfter": {}
},
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*svar-status-kod*> | Integer | HTTP-statuskod som skickas till den inkommande begäran. Standard koden är "200 OK", men koden kan vara vilken giltig status kod som helst som börjar med 2xx, 4xx eller 5xx, men inte med 3xxx. | 
|||| 

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*svar – rubriker*> | JSON-objekt | En eller flera huvuden som ska inkluderas med svaret | 
| <*svars text*> | Önskade | Svars texten, som kan vara en sträng, ett JSON-objekt eller till och med binärt innehåll från en tidigare åtgärd | 
|||| 

*Exempel*

Den här åtgärds definitionen skapar ett svar på en HTTP-begäran med angiven status kod, meddelande text och meddelandehuvuden:

```json
"Response": {
   "type": "Response",
   "inputs": {
      "statusCode": 200,
      "body": {
         "ProductID": 0,
         "Description": "Organic Apples"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "content-type": "application/json"
      }
   },
   "runAfter": {}
}
```

*Begränsningar*

Till skillnad från andra åtgärder har **svars** åtgärden särskilda begränsningar: 

* Arbets flödet kan bara använda **svars** åtgärden när arbets flödet startar med en http-begäran om utlösare, vilket innebär att arbets flödet måste utlösas av en http-begäran.

* Arbets flödet kan använda **svars** åtgärden var som helst *utom* i **förgrunds** slingor, **fram till** slingor, inklusive sekventiella slingor och parallella grenar. 

* Den ursprungliga begäran får endast ditt arbets flödes svar när alla åtgärder som krävs av **svars** åtgärden har avslut ATS inom [tids gränsen för http](../logic-apps/logic-apps-limits-and-config.md#http-limits).

  Om arbets flödet till exempel anropar en annan Logic app som ett kapslat arbets flöde, väntar det överordnade arbets flödet tills det kapslade arbets flödet har slutförts, oavsett hur lång tid som passerar innan det kapslade arbets flödet slutförs.

* När arbets flödet använder **svars** åtgärden och ett synkront svars mönster kan inte arbets flödet också använda kommandot **splitOn** i utlösnings definitionen eftersom kommandot skapar flera körningar. Sök efter det här fallet när metoden för att använda och om värdet är true, returnera svaret "felaktig begäran".

  Annars, om arbets flödet använder kommandot **splitOn** och en **svars** åtgärd, körs arbets flödet asynkront och returnerar omedelbart ett "202 godkänt"-svar.

* När arbets flödets körning når **svars** åtgärden, men den inkommande begäran redan har fått ett svar, markeras **svars** åtgärden som "misslyckades" på grund av konflikten. Därför är din Logic app-körning också markerad med statusen "misslyckades".

<a name="select-action"></a>

### <a name="select-action"></a>Välj åtgärd

Den här åtgärden skapar en matris med JSON-objekt genom att transformera objekt från en annan matris baserat på den angivna kartan. Matrisen för utdata och käll mat ris har alltid samma antal objekt. Även om du inte kan ändra antalet objekt i utdatabufferten kan du lägga till eller ta bort egenskaper och deras värden i dessa objekt. `select`Egenskapen anger minst ett nyckel/värde-par som definierar kartan för omvandling av objekt i käll mat ris. Ett nyckel/värde-par representerar en egenskap och dess värde över alla objekt i den utgående matrisen.

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": <array>,
      "select": { 
          "<key-name>": "<expression>",
          "<key-name>": "<expression>"        
      }
   },
   "runAfter": {}
},
```

*Obligatoriskt* 

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*lagringsmatriser*> | Matris | Matrisen eller uttrycket som innehåller käll objekt. Se till att du omger ett uttryck med dubbla citat tecken. <p>**Obs!** om käll mat ris är tom skapar åtgärden en tom matris. | 
| <*nyckel namn*> | Sträng | Egenskaps namnet som tilldelats resultatet från <- *uttryck*> <p>Om du vill lägga till en ny egenskap över alla objekt i den utgående matrisen, anger du ett <*nyckel namn*> för egenskapen och ett <*uttryck*> för egenskap svärdet. <p>Om du vill ta bort en egenskap från alla objekt i matrisen utelämnar du <*nyckel namnet*> för den egenskapen. | 
| <*uttryck*> | Sträng | Det uttryck som transformerar objektet i käll mat ris och tilldelar resultatet till <*nyckel namn*> | 
|||| 

Åtgärden **Välj** skapar en matris som utdata, så alla åtgärder som vill använda dessa utdata måste antingen acceptera en matris, eller så måste du konvertera matrisen till den typ som konsument åtgärden accepterar. Om du till exempel vill konvertera utmatnings mat ris till en sträng kan du skicka matrisen till åtgärden **Skriv** och sedan referera till utdata från åtgärden **Skriv** i dina andra åtgärder.

*Exempel*

Den här åtgärds definitionen skapar en JSON-objekt mat ris från en heltals mat ris. Åtgärden upprepas genom käll matrisen, hämtar varje heltals värde med hjälp av `@item()` uttrycket och tilldelar varje värde till `number` egenskapen "" i varje JSON-objekt:

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": [ 1, 2, 3 ],
      "select": { 
         "number": "@item()" 
      }
   },
   "runAfter": {}
},
```

Här är den matris som den här åtgärden skapar:

`[ { "number": 1 }, { "number": 2 }, { "number": 3 } ]`

Om du vill använda mat ris utdata i andra åtgärder skickar du följande utdata till en **Skriv** åtgärd:

```json
"Compose": {
   "type": "Compose",
   "inputs": "@body('Select')",
   "runAfter": {
      "Select": [ "Succeeded" ]
   }
},
```

Du kan sedan använda utdata från åtgärden **Skriv** i andra åtgärder, till exempel **Office 365 Outlook – skicka en e-post-** åtgärd:

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "@{outputs('Compose')}",
         "Subject": "Output array from Select and Compose actions",
         "To": "<your-email@domain>"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "post",
      "path": "/Mail"
   },
   "runAfter": {
      "Compose": [ "Succeeded" ]
   }
},
```

<a name="table-action"></a>

### <a name="table-action"></a>Tabell åtgärd

Den här åtgärden skapar en CSV-eller HTML-tabell från en matris. För matriser med JSON-objekt skapar den här åtgärden automatiskt kolumn rubrikerna från objektets egenskaps namn. För matriser med andra data typer måste du ange kolumn rubriker och värden. Den här matrisen innehåller till exempel egenskaperna "ID" och "Product_Name" som den här åtgärden kan använda för kolumn rubrikernas namn:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]` 

```json
"Create_<CSV | HTML>_table": {
   "type": "Table",
   "inputs": {
      "format": "<CSV | HTML>",
      "from": <array>,
      "columns": [ 
         {
            "header": "<column-name>",
            "value": "<column-value>"
         },
         {
            "header": "<column-name>",
            "value": "<column-value>"
         } 
      ]
   },
   "runAfter": {}
}
```

*Obligatoriskt* 

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| \<CSV *or* HTML>| Sträng | Formatet för den tabell som du vill skapa | 
| <*lagringsmatriser*> | Matris | Matrisen eller uttrycket som innehåller tabellens käll objekt <p>**Obs!** om käll matrisen är tom skapar åtgärden en tom tabell. | 
|||| 

*Valfritt*

Om du vill ange eller anpassa kolumn rubriker och värden använder du `columns` matrisen. När `header-value` par har samma rubrik namn visas deras värden i samma kolumn under huvud namnet. Annars definierar varje unikt huvud en unik kolumn.

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*kolumn namn*> | Sträng | Rubrik namnet för en kolumn | 
| <*kolumn-värde*> | Valfri | Värdet i den kolumnen | 
|||| 

*Exempel 1*

Anta att du har en tidigare skapad "myItemArray"-variabel som för närvarande innehåller den här matrisen:

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

Den här åtgärds definitionen skapar en CSV-tabell från variabeln "myItemArray". Uttrycket som används av `from` egenskapen hämtar matrisen från "myItemArray" med hjälp av `variables()` funktionen:

```json
"Create_CSV_table": {
   "type": "Table",
   "inputs": {
      "format": "CSV",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Följande är CSV-tabellen som den här åtgärden skapar: 

```
ID,Product_Name 
0,Apples 
1,Oranges 
```

*Exempel 2*

Den här åtgärds definitionen skapar en HTML-tabell från variabeln "myItemArray". Uttrycket som används av `from` egenskapen hämtar matrisen från "myItemArray" med hjälp av `variables()` funktionen:

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

Här är HTML-tabellen som den här åtgärden skapar: 

<table><thead><tr><th>ID</th><th>Product_Name</th></tr></thead><tbody><tr><td>0</td><td>Apples</td></tr><tr><td>1</td><td>Apelsiner</td></tr></tbody></table>

*Exempel 3*

Den här åtgärds definitionen skapar en HTML-tabell från variabeln "myItemArray". Detta exempel åsidosätter standard kolumn rubrik namnen med "Stock_ID" och "Description", och lägger till ordet "organisk" i värdena i kolumnen "Beskrivning".

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')",
      "columns": [ 
         {
            "header": "Stock_ID",
            "value": "@item().ID"
         },
         {
            "header": "Description",
            "value": "@concat('Organic ', item().Product_Name)"
         }
      ]
    },
   "runAfter": {}
},
```

Här är HTML-tabellen som den här åtgärden skapar: 

<table><thead><tr><th>Stock_ID</th><th>Beskrivning</th></tr></thead><tbody><tr><td>0</td><td>Ekologiska äpplen</td></tr><tr><td>1</td><td>Organiska orange</td></tr></tbody></table>

<a name="terminate-action"></a>

### <a name="terminate-action"></a>Avsluta åtgärd

Den här åtgärden avbryter körningen av en arbets flödes instans, avbryter alla åtgärder som pågår, hoppar över eventuella återstående åtgärder och returnerar angiven status. Du kan till exempel använda åtgärden **Avsluta** när din Logi Kap par måste avslutas helt från ett fel tillstånd. Den här åtgärden påverkar inte redan slutförda **åtgärder och får inte förekomma i** förgrunden och **till** följd av slingor, inklusive sekventiella slingor.

```json
"Terminate": {
   "type": "Terminate",
   "inputs": {
       "runStatus": "<status>",
       "runError": {
            "code": "<error-code-or-name>",
            "message": "<error-message>"
       }
   },
   "runAfter": {}
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*statusfältet*> | Sträng | Status som ska returneras för körningen: "misslyckades", "avbruten" eller "lyckades" |
|||| 

*Valfritt*

Egenskaperna för "runStatus"-objektet gäller endast när "runStatus"-egenskapen har angetts till "misslyckades"-status.

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*fel-kod-eller-namn*> | Sträng | Fel meddelandets kod eller namn |
| <*fel-meddelande*> | Sträng | Det meddelande eller den text som beskriver felet och vilka åtgärder som app-användaren kan vidta | 
|||| 

*Exempel*

Den här åtgärds definitionen stoppar en arbets flödes körning, anger körnings statusen till "misslyckades" och returnerar status, en felkod och ett fel meddelande:

```json
"Terminate": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "Unexpected response",
            "message": "The service received an unexpected response. Please try again."
        }
   },
   "runAfter": {}
}
```

<a name="wait-action"></a>

### <a name="wait-action"></a>Vänte åtgärd

Den här åtgärden pausar arbets flödes körningen för det angivna intervallet eller till den angivna tiden, men inte båda.

*Angivet intervall*

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": <number-of-units>,
         "unit": "<interval>"
      }
   },
   "runAfter": {}
},
```

*Angiven tid*

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "<date-time-stamp>"
      }
   },
   "runAfter": {}
},
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*antal enheter*> | Integer | För **fördröjnings** åtgärden, antalet enheter att vänta | 
| <*intervall*> | Sträng | För **fördröjnings** åtgärden: vänte tiden: "sekund", "minut", "timme", "dag", "vecka", "månad" | 
| <*datum/tidstämpel*> | Sträng | För **fördröjningen tills** åtgärden, datum och tid för att återuppta körningen. Det här värdet måste använda [tids formatet UTC-datum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time). | 
|||| 

*Exempel 1*

Den här åtgärds definitionen pausar arbets flödet i 15 minuter:

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": 15,
         "unit": "Minute"
      }
   },
   "runAfter": {}
},
```

*Exempel 2*

Den här åtgärds definitionen pausar arbets flödet fram till den angivna tiden:

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "2017-10-01T00:00:00Z"
      }
   },
   "runAfter": {}
},
```

<a name="workflow-action"></a>

### <a name="workflow-action"></a>Arbets flödes åtgärd

Den här åtgärden anropar en annan tidigare skapad Logic-app, vilket innebär att du kan inkludera och återanvända andra Logic app-arbetsflöden. Du kan också använda utdata från den underordnade eller *kapslade* Logic-appen i åtgärder som följer den kapslade Logic-appen, förutsatt att den underordnade Logic-appen returnerar ett svar.

Logic Appss motorn kontrollerar åtkomsten till den utlösare som du vill anropa, så se till att du har åtkomst till den utlösaren. Den kapslade Logic-appen måste också uppfylla följande kriterier:

* En utlösare gör det kapslade Logic-appen anrops bara, till exempel en [begäran](#request-trigger) eller [http-](#http-trigger) utlösare

* Samma Azure-prenumeration som din överordnade Logic-app

* Om du vill använda utdata från den kapslade Logic-appen i din överordnade Logic-app måste den kapslade Logic-appen ha en [svars](#response-action) åtgärd

```json
"<nested-logic-app-name>": {
   "type": "Workflow",
   "inputs": {
      "body": { "<body-content" },
      "headers": { "<header-content>" },
      "host": {
         "triggerName": "<trigger-name>",
         "workflow": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group>/providers/Microsoft.Logic/<nested-logic-app-name>"
         }
      }
   },
   "runAfter": {}
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*kapslad Logic – App-Name*> | Sträng | Namnet på den Logic app som du vill anropa | 
| <*utlösare-namn*> | Sträng | Namnet på utlösaren i den kapslade Logic-app som du vill anropa | 
| <*Azure-prenumerations-ID*> | Sträng | Azure-prenumerations-ID för den kapslade Logic-appen |
| <*Azure-resurs-grupp*> | Sträng | Azure-resursens resurs grupp namn för den kapslade Logic-appen |
| <*kapslad Logic – App-Name*> | Sträng | Namnet på den Logic app som du vill anropa |
||||

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------|  
| <*rubrik – innehåll*> | JSON-objekt | Alla rubriker som ska skickas med anropet | 
| <*brödtext – innehåll*> | JSON-objekt | Meddelande innehåll som ska skickas med anropet | 
||||

*Utdata*

Den här åtgärdens utdata varierar beroende på den kapslade Logic Apps svars åtgärd. Om den kapslade Logic-appen inte innehåller någon svars åtgärd är utmatningarna tomma.

*Exempel*

När åtgärden "Start_search" har slutförts anropar den här arbets flödes åtgärds definitionen en annan Logic-app med namnet "Get_product_information", som skickar i angivna indata:

```json
"actions": {
   "Start_search": { <action-definition> },
   "Get_product_information": {
      "type": "Workflow",
      "inputs": {
         "body": {
            "ProductID": "24601",
         },
         "host": {
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/InventoryManager-RG/providers/Microsoft.Logic/Get_product_information",
            "triggerName": "Find_product"
         },
         "headers": {
            "content-type": "application/json"
         }
      },
      "runAfter": { 
         "Start_search": [ "Succeeded" ]
      }
   }
},
```

## <a name="control-workflow-action-details"></a>Åtgärds information om kontroll av arbets flöde

<a name="foreach-action"></a>

### <a name="foreach-action"></a>Vidtagen åtgärd

Den här upprepnings åtgärden upprepas genom en matris och utför åtgärder på varje mat ris objekt. Som standard körs loopen "for each" parallellt till ett maximalt antal slingor. För detta maximum, se [gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Lär dig [hur du skapar "för varje"-loopar](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

```json
"For_each": {
   "type": "Foreach",
   "actions": { 
      "<action-1>": { "<action-definition-1>" },
      "<action-2>": { "<action-definition-2>" }
   },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": <count>
      }
    },
    "operationOptions": "<operation-option>"
}
```

*Obligatoriskt* 

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*åtgärd-1... m*> | Sträng | Namnen på de åtgärder som körs på varje mat ris objekt | 
| <*åtgärds definition-1... m*> | JSON-objekt | Definitionerna för de åtgärder som körs | 
| <*för – varje-uttryck*> | Sträng | Det uttryck som refererar till varje objekt i den angivna matrisen | 
|||| 

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*reparationer*> | Integer | Som standard körs loopen för varje upprepning samtidigt (samtidigt eller parallellt) upp till [standard gränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra den här gränsen genom att ange ett nytt <*antal*>s värde, se [ändra "för varje" loop-samtidighets](#change-for-each-concurrency). | 
| <*åtgärd – alternativ*> | Sträng | Om du vill köra en "for each"-loop i tur och ordning i stället för parallellt anger du antingen <*Åtgärds alternativ*> till `Sequential` eller <*antal*> till `1` , men inte båda. Mer information finns i [Kör "för varje" slingor i turordning](#sequential-for-each). | 
|||| 

*Exempel*

Detta för varje-slinga skickar ett e-postmeddelande för varje objekt i matrisen, som innehåller bilagor från ett inkommande e-postmeddelande. Loopen skickar ett e-postmeddelande, inklusive bilagan, till en person som granskar bilagan.

```json
"For_each": {
   "type": "Foreach",
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "body": {
               "Body": "@base64ToString(items('For_each')?['Content'])",
               "Subject": "Review attachment",
               "To": "Sophie.Owen@contoso.com"
                },
            "host": {
               "connection": {
                  "id": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {}
      }
   },
   "foreach": "@triggerBody()?['Attachments']",
   "runAfter": {}
}
```

Om du bara vill ange en matris som skickas som utdata från utlösaren hämtar det här uttrycket <*mat ris namnet*> matrisen från utlösnings texten. För att undvika ett problem om matrisen inte finns använder uttrycket `?` operatorn:

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>Om åtgärd

Den här åtgärden, som är en *villkors instruktion*, utvärderar ett uttryck som representerar ett villkor och kör en annan gren baserat på om villkoret är sant eller falskt. Om villkoret är sant markeras villkoret med statusen "lyckades". Lär dig [hur du skapar villkorliga uttryck](../logic-apps/logic-apps-control-flow-conditional-statement.md).

``` json
"Condition": {
   "type": "If",
   "expression": { "<condition>" },
   "actions": {
      "<action-1>": { "<action-definition>" }
   },
   "else": {
      "actions": {
        "<action-2>": { "<action-definition" }
      }
   },
   "runAfter": {}
}
```

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*moduletype*> | JSON-objekt | Villkoret, som kan vara ett uttryck, för att utvärdera | 
| <*åtgärd-1*> | JSON-objekt | Åtgärden som ska köras när <*villkors*> utvärderas till sant | 
| <*åtgärds definition*> | JSON-objekt | Definitionen för åtgärden | 
| <*åtgärd-2*> | JSON-objekt | Åtgärden som ska köras när <*villkors*> utvärderas till falskt | 
|||| 

Åtgärderna i- `actions` eller- `else` objekten får följande status:

* "Lyckades" när de kördes och lyckas
* "Misslyckades" när de körs och Miss lyckas
* "Hoppas över" när respektive gren inte körs

*Exempel*

Det här villkoret anger att när variabeln Integer har ett värde som är större än noll, kontrollerar arbets flödet en webbplats. Om variabeln är noll eller mindre, kontrollerar arbets flödet en annan webbplats.

```json
"Condition": {
   "type": "If",
   "expression": {
      "and": [ {
         "greater": [ "@variables('myIntegerVariable')", 0 ] 
      } ]
   },
   "actions": { 
      "HTTP - Check this website": {
         "type": "Http",
         "inputs": {
         "method": "GET",
            "uri": "http://this-url"
         },
         "runAfter": {}
      }
   },
   "else": {
      "actions": {
         "HTTP - Check this other website": {
            "type": "Http",
            "inputs": {
               "method": "GET",
               "uri": "http://this-other-url"
            },
            "runAfter": {}
         }
      }
   },
   "runAfter": {}
}
```  

#### <a name="how-conditions-use-expressions"></a>Villkor använder uttryck

Här följer några exempel som visar hur du kan använda uttryck i villkor:
  
| JSON | Resultat | 
|------|--------| 
| "uttryck": " @parameters (<*hasSpecialAction*>)" | För booleska uttryck, passas villkoret för alla värden som utvärderas till sant. <p>Om du vill konvertera andra typer till booleska använder du dessa funktioner: `empty()` eller `equals()` . | 
| "uttryck": " @greater (åtgärder (<*åtgärd*>). output. Value, parametrar (' <*tröskelvärde*> '))" | För jämförelse funktioner körs åtgärden bara när den utdata från <*åtgärd*> överstiger värdet för <*tröskelvärde*>. | 
| "uttryck": " @or (större (åtgärder (<*åtgärd*>). output. Value, parametrar (' <*tröskelvärde*> ')), minus (åtgärder (<*samma åtgärd*>). output. Value, 100))" | För Logic Functions och att skapa kapslade booleska uttryck körs åtgärden när utdata från <*åtgärd*> överstiger värdet för <*tröskelvärde*> eller under 100. | 
| "uttryck": " @equals (längd (åtgärder (<*åtgärd*>). outputs. errors), 0))" | Du kan använda array Functions för att kontrol lera om matrisen innehåller några objekt. Åtgärden körs när `errors` matrisen är tom. | 
||| 

<a name="scope-action"></a>

### <a name="scope-action"></a>Omfattnings åtgärd

Med den här åtgärden grupperas åtgärder logiskt i *omfattningar*, vilket ger sin egen status när åtgärderna i det omfånget har slutförts. Du kan sedan använda Omfattningens status för att avgöra om andra åtgärder körs. Lär dig [hur du skapar omfång](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).

```json
"Scope": {
   "type": "Scope",
   "actions": {
      "<inner-action-1>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<inner-action-2>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   }
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------|  
| <*inre-åtgärd-1... m*> | JSON-objekt | En eller flera åtgärder som körs inom omfånget |
| <*åtgärds-input*> | JSON-objekt | Indata för varje åtgärd |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>Växelåtgärd

Den här åtgärden, som även kallas en *switch-instruktion*, ordnar andra åtgärder i *fall* och tilldelar ett värde till varje fall, utom för standard fallet om ett sådant finns. När arbets flödet körs jämför **växel** åtgärden värdet från ett uttryck, ett objekt eller en token mot de värden som anges för varje fall. Om åtgärden **switch** hittar ett matchande ärende, kör arbets flödet bara åtgärderna för det fallet. Varje gången som **växel** åtgärden körs finns bara ett matchande fall eller inga matchningar. Om det inte finns några matchningar kör **växel** åtgärden standard åtgärderna. Lär dig [hur du skapar switch-instruktioner](../logic-apps/logic-apps-control-flow-switch-statement.md).

``` json
"Switch": {
   "type": "Switch",
   "expression": "<expression-object-or-token>",
   "cases": {
      "Case": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      },
      "Case_2": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      }
   },
   "default": {
      "actions": {
         "<default-action-name>": { "<default-action-definition>" }
      }
   },
   "runAfter": {}
}
```

*Obligatoriskt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*uttryck – objekt-eller-token*> | Det varierar | Uttrycket, JSON-objektet eller token som ska utvärderas | 
| <*åtgärds namn*> | Sträng | Namnet på åtgärden som ska köras för matchnings ärendet | 
| <*åtgärds definition*> | JSON-objekt | Definitionen för åtgärden som ska köras för matchnings ärendet | 
| <*matchande värde*> | Det varierar | Värdet som ska jämföras med det utvärderade resultatet | 
|||| 

*Valfritt*

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*standard-åtgärds namn*> | Sträng | Namnet på standard åtgärden som ska köras när det inte finns något matchande fall | 
| <*standard-åtgärds definition*> | JSON-objekt | Definitionen för åtgärden som ska köras när det inte finns något matchande fall | 
|||| 

*Exempel*

Den här åtgärds definitionen utvärderar om personen som svarar på e-postbegäran om godkännande har valt alternativet Godkänn eller alternativet avvisa. Baserat på det här alternativet kör **växel** åtgärden åtgärder för matchnings ärendet, vilket innebär att skicka ett nytt e-postmeddelande till den svarande, men med en annan formulering i varje fall. 

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
            "Send_an_email": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your approval.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions": {
            "Send_an_email_2": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your response.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}     
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": { 
         "Send_an_email_3": { 
            "type": "ApiConnection",
            "inputs": {
               "Body": "Please respond with either 'Approve' or 'Reject'.",
               "Subject": "Please respond", 
               "To": "Sophie.Owen@contoso.com"
            },
            "host": {
               "connection": {
                  "name": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {} 
      }
   },
   "runAfter": {
      "Send_approval_email": [ 
         "Succeeded"
      ]
   }
}
```

<a name="until-action"></a>

### <a name="until-action"></a>Tills åtgärden

Den här slingan-åtgärden innehåller åtgärder som körs tills det angivna villkoret är sant. Loopen kontrollerar villkoret som det sista steget efter att alla andra åtgärder har körts. Du kan inkludera fler än en åtgärd i `"actions"` objektet och åtgärden måste definiera minst en gräns. Lär dig [hur du skapar "till"-slingor](../logic-apps/logic-apps-control-flow-loops.md#until-loop). 

```json
 "Until": {
   "type": "Until",
   "actions": {
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   },
   "expression": "<condition>",
   "limit": {
      "count": <loop-count>,
      "timeout": "<loop-timeout>"
   },
   "runAfter": {}
}
```

| Värde | Typ | Beskrivning | 
|-------|------|-------------| 
| <*åtgärds namn*> | Sträng | Namnet på den åtgärd som du vill köra inuti slingan | 
| <*åtgärds typ*> | Sträng | Den åtgärds typ som du vill köra | 
| <*åtgärds-input*> | Önskade | Indata för den åtgärd som ska köras | 
| <*moduletype*> | Sträng | Villkoret eller uttrycket som ska utvärderas efter att alla åtgärder i slingan har körts | 
| <*loop-antal*> | Integer | Gränsen för det mest antal slingor som åtgärden kan köra. Mer information om standard gränsen och den maximala gränsen finns i [gränser och konfiguration för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). | 
| <*loop-timeout*> | Sträng | Gränsen på den längsta tid som slingan kan köras. `timeout`Standardvärdet är `PT1H` , vilket är det [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601)som krävs. |
|||| 

> [!NOTE]
> Om uttrycket är beroende av utdata från vilken åtgärd som helst i slingan tills, kontrollerar du att du har ett konto för eventuella problem som uppstår från åtgärden.

*Exempel*

Den här åtgärds definitionen för loopen skickar en HTTP-begäran till angiven URL tills något av följande villkor uppfylls:

* Begäran får ett svar med status koden "200 OK".
* Loopen har körts 60 gånger.
* Loopen har körts i en timme.

```json
 "Run_until_loop_succeeds_or_expires": {
    "type": "Until",
    "actions": {
        "HTTP": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('HTTP')['statusCode'], 200)",
    "limit": {
        "count": 60,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

<a name="subscribe-unsubscribe"></a>

## <a name="webhooks-and-subscriptions"></a>Webhooks och prenumerationer

Webhook-baserade utlösare och åtgärder kontrollerar inte regelbundet slut punkter, men väntar på vissa händelser eller data på dessa slut punkter i stället. Dessa utlösare och åtgärder *prenumererar* på slut punkterna genom att ange en *återanrops-URL* där slut punkten kan skicka svar.

`subscribe`Anropet sker när arbets flödet ändras på något sätt, till exempel när autentiseringsuppgifter förnyas, eller när indataparametrarna ändras för en utlösare eller åtgärd. Det här anropet använder samma parametrar som standard-HTTP-åtgärder. 

`unsubscribe`Anropet sker automatiskt när en åtgärd gör utlösaren eller åtgärden ogiltig, till exempel:

* Tar bort eller inaktiverar utlösaren. 
* Ta bort eller inaktivera arbets flödet. 
* Ta bort eller inaktivera prenumerationen. 

För att stödja dessa anrop `@listCallbackUrl()` returnerar uttrycket en unik "återanrops-URL" för utlösaren eller åtgärden. Denna URL representerar en unik identifierare för de slut punkter som använder tjänstens REST API. Parametrarna för den här funktionen är samma som webhook-utlösaren eller åtgärden.

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>Ändra asynkron varaktighet

För både utlösare och åtgärder kan du begränsa varaktigheten för det asynkrona mönstret till ett särskilt tidsintervall genom att lägga till `limit.timeout` egenskapen. På så sätt, om åtgärden inte har avslut ATS när intervallet går, markeras åtgärdens status som `Cancelled` med `ActionTimedOut` koden. `timeout`Egenskapen använder [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).

``` json
"<trigger-or-action-name>": {
   "type": "Workflow | Webhook | Http | ApiConnectionWebhook | ApiConnection",
   "inputs": {},
   "limit": {
      "timeout": "PT10S"
   },
   "runAfter": {}
}
```

<a name="runtime-config-options"></a>

## <a name="runtime-configuration-settings"></a>Konfigurations inställningar för körning

Du kan ändra standard körnings beteendet för utlösare och åtgärder genom att lägga till dessa `runtimeConfiguration` Egenskaper i utlösaren eller åtgärds definitionen.

| Egenskap | Typ | Beskrivning | Utlösare eller åtgärd | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | Integer | Ändra [*standard gränsen*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) för antalet arbets flödes instanser som kan köras samtidigt (samtidigt eller parallellt). Genom att justera det här värdet kan du begränsa antalet begär Anden som backend-system får. <p>Att ställa in `runs` egenskapen på `1` fungerar på samma sätt som när du anger `operationOptions` egenskapen till `SingleInstance` . Du kan ange antingen en egenskap, men inte båda. <p>Om du vill ändra standard gränsen, se [ändra utlösare](#change-trigger-concurrency) eller [Utlös instanser sekventiellt](#sequential-trigger). | Alla utlösare | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | Integer | Ändra [*standard gränsen*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) för antalet arbets flödes instanser som måste vänta på att köras när din Logic app redan kör maximalt antal samtidiga instanser. <p>Om du vill ändra standard gränsen, se [begränsningen för ändrings väntande körningar](#change-waiting-runs). | Alla utlösare | 
| `runtimeConfiguration.concurrency.repetitions` | Integer | Ändra [*standard gränsen*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) för antalet upprepningar för varje loop som kan köras samtidigt (samtidigt eller parallellt). <p>Att ställa in `repetitions` egenskapen på `1` fungerar på samma sätt som när du anger `operationOptions` egenskapen till `SingleInstance` . Du kan ange antingen en egenskap, men inte båda. <p>Om du vill ändra standard gränsen läser du [ändra "för varje" samtidighet](#change-for-each-concurrency) eller [Kör "för varje" slingor i följd](#sequential-for-each). | Åtgärd: <p>[Foreach](#foreach-action) | 
| `runtimeConfiguration.paginationPolicy.minimumItemCount` | Integer | För vissa åtgärder som stöder och att sid brytning är aktiverat anger det här värdet det *minsta* antal resultat som ska hämtas. <p>Om du vill aktivera sid brytning, se [Hämta Mass data, objekt eller resultat med sid brytning](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md) | Åtgärd: varierande |
| `runtimeConfiguration.secureData.properties` | Matris | På många utlösare och åtgärder döljer de här inställningarna indata, utdata eller både och från den logiska appens körnings historik. <p>Mer information om hur du skyddar dessa data finns i [Dölj indata och utdata från körnings historik](../logic-apps/logic-apps-securing-a-logic-app.md#secure-data-code-view). | De flesta utlösare och åtgärder |
| `runtimeConfiguration.staticResult` | JSON-objekt | För åtgärder som stöder och har den [statiska resultat](../logic-apps/test-logic-apps-mock-data-static-results.md) inställningen aktive rad `staticResult` har objektet följande attribut: <p>- `name`, som refererar till den aktuella åtgärdens statiska resultat definitions namn, som visas i `staticResults` attributet i ditt Logic app-arbetsflöde- `definition` attribut. Mer information finns i [statiska resultat – schema referens för språk för arbets flödes definition](../logic-apps/logic-apps-workflow-definition-language.md#static-results). <p> - `staticResultOptions`, som anger om statiska resultat är `Enabled` eller inte för den aktuella åtgärden. <p>Information om hur du aktiverar statiska resultat finns i [testa Logic Apps med blå data genom att konfigurera statiska resultat](../logic-apps/test-logic-apps-mock-data-static-results.md) | Åtgärd: varierande |
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>Åtgärds alternativ

Du kan ändra standard beteendet för utlösare och åtgärder med `operationOptions` egenskapen i utlösare eller åtgärds definition.

| Åtgärds alternativ | Typ | Beskrivning | Utlösare eller åtgärd | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | Sträng | Kör HTTP-baserade åtgärder synkront, i stället för asynkront. <p><p>Information om hur du ställer in det här alternativet finns i [köra åtgärder synkront](#disable-asynchronous-pattern). | Action <p>[ApiConnection](#apiconnection-action), <br>[Http](#http-action), <br>[Response](#response-action) | 
| `IncludeAuthorizationHeadersInOutputs` | Sträng | För logi Kap par som [aktiverar Azure Active Directory öppna autentisering (Azure AD OAuth)](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth) för att auktorisera åtkomst för inkommande anrop till en begärd utlösare-slutpunkt, inkluderar du `Authorization` rubriken från OAuth-åtkomsttoken i utlösa utdata. Mer information finns i [Inkludera ' Authorization '-huvud i utlösare utdata](../logic-apps/logic-apps-securing-a-logic-app.md#include-auth-header). | Utlösar <p>[Begäran](#request-trigger), <br>[HTTP-webhook](#http-webhook-trigger) | 
| `Sequential` | Sträng | Kör "for each"-loopen upprepas en i taget, i stället för alla samtidigt parallellt. <p>Det här alternativet fungerar på samma sätt som när du anger `runtimeConfiguration.concurrency.repetitions` egenskapen till `1` . Du kan ange antingen en egenskap, men inte båda. <p><p>Information om hur du ställer in det här alternativet finns i [Kör "för varje" slingor i turordning](#sequential-for-each).| Åtgärd: <p>[Foreach](#foreach-action) | 
| `SingleInstance` | Sträng | Kör utlösaren för varje Logic App-instans sekventiellt och vänta tills den tidigare aktiva körningen har slutförts innan du utlöser nästa Logic App-instans. <p><p>Det här alternativet fungerar på samma sätt som när du anger `runtimeConfiguration.concurrency.runs` egenskapen till `1` . Du kan ange antingen en egenskap, men inte båda. <p>Om du vill ange det här alternativet läser du [Utlös ande instanser i tur och ordning](#sequential-trigger). | Alla utlösare | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>Ändra samtidighet för utlösare

Som standard körs Logic app-arbetsflöden på samma tidpunkt (samtidigt eller parallellt). Detta innebär att varje Utlös ande instans utlöses innan den tidigare aktiva arbets flödes instansen har körts. Det finns dock en [standard gräns](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)för antalet instanser som körs samtidigt. Om antalet arbets flödes instanser som körs samtidigt når den här gränsen måste alla nya instanser vänta på att köras. Den här gränsen hjälper till att styra antalet förfrågningar som backend-system tar emot.

När du aktiverar utlösarens samtidighets kontroll körs Utlös ande instanser parallellt med [standard gränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du vill ändra den här standard begränsningen för samtidighet kan du antingen använda kodvyn eller Logic Apps designer eftersom du ändrar samtidighets inställningen via design verktyget lägger till eller uppdaterar `runtimeConfiguration.concurrency.runs` egenskapen i den underliggande utlösaren och vice versa. Den här egenskapen styr det maximala antalet nya arbets flödes instanser som kan köras parallellt.

Här följer några saker att tänka på när du vill aktivera samtidighet i en utlösare:

* När samtidighet har Aktiver ATS minskas [SplitOn-gränsen](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) avsevärt för [debatchering av matriser](#split-on-debatch). Om antalet objekt överskrider den här gränsen är SplitOn-funktionen inaktive rad.

* Du kan inte inaktivera samtidighet när du har aktiverat samtidighets kontrollen.

* När samtidighet har Aktiver ATS kan en tids krävande Logic App-instans orsaka att nya Logic App-instanser anger ett vänte läge. Detta tillstånd förhindrar Azure Logic Apps från att skapa nya instanser och inträffar även om antalet samtidiga körningar är mindre än det angivna maximala antalet samtidiga körningar.

  * Om du vill avbryta det här läget avbryter du de tidigaste instanserna som *fortfarande körs*.

    1. På din Logic Apps-meny väljer du **Översikt**.

    1. I avsnittet **körnings historik** väljer du den tidigaste instans som fortfarande körs, till exempel:

       ![Välj tidigaste aktiva instans](./media/logic-apps-workflow-actions-triggers/waiting-runs.png)

       > [!TIP]
       > Om du bara vill visa instanser som fortfarande körs, öppnar du listan **alla** och väljer **Kör**.

    1. Under **Logic app-körning** väljer du **Avbryt körning**.

       ![Hitta tidigaste aktiva instans](./media/logic-apps-workflow-actions-triggers/cancel-run.png)

  * Undvik den här möjligheten genom att lägga till en tids gräns för alla åtgärder som kan innehålla de här körningarna. Om du arbetar i kod redigeraren, se [ändra asynkron varaktighet](#asynchronous-limits). Annars, om du använder designern, följer du dessa steg:

    1. Välj ellipserna (**...**) i det övre högra hörnet på den åtgärd där du vill lägga till en tids gräns i din Logic app och välj sedan **Inställningar**.

       ![Öppna åtgärds inställningar](./media/logic-apps-workflow-actions-triggers/action-settings.png)

    1. Under **tids gräns** anger du varaktigheten för timeout i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).

       ![Ange varaktighet för timeout](./media/logic-apps-workflow-actions-triggers/timeout.png)

* Om du vill köra din Logic app sekventiellt anger du utlösarens samtidighet till `1` antingen med hjälp av kodvyn eller designern. Se till att du inte också anger utlösarens `operationOptions` egenskap till `SingleInstance` i kodvyn. Annars får du ett verifierings fel. Mer information finns i avsnittet om [Utlös ande instanser i tur och ordning](#sequential-trigger).

#### <a name="edit-in-code-view"></a>Redigera i kodvyn 

I den underliggande utlösnings definitionen lägger du till `runtimeConfiguration.concurrency.runs` egenskapen, som kan ha ett värde som sträcker `1` sig från till `50` .

Här är ett exempel som begränsar samtidiga körningar till 10 instanser:

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 10
      }
   }
}
```

Mer information finns i [konfigurations inställningar för körning](#runtime-config-options).

#### <a name="edit-in-logic-apps-designer"></a>Redigera i Logic Apps designer

1. I utlösarens övre högra hörn väljer du knappen med tre punkter (**...**) och väljer sedan **Inställningar**.

1. Under **concurrency-kontroll** anger du **begränsa** till **på**. 

1. Dra skjutreglaget **för parallellitet** till det värde som du vill använda. Om du vill köra din Logic app i tur och ordning drar du skjutreglaget till **1**.

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>Ändra "för varje" samtidighet

Som standard upprepas alla körningar på samma tidpunkt (samtidigt eller parallellt). Detta innebär att varje iteration börjar köras innan föregående iteration slutförs. Det finns dock en [standard gräns](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)för antalet återkommande iterationer som körs. När antalet upprepningar som körs samtidigt når den här gränsen måste alla andra iterationer vänta på att köras.

Om du vill ändra standard gränsen kan du använda antingen kodvyn eller Logic Apps designer, eftersom ändringar i samtidighets inställningarna i designern lägger till eller uppdaterar `runtimeConfiguration.concurrency.repetitions` egenskapen i den underliggande "för varje" åtgärds definition och vice versa. Den här egenskapen styr det maximala antalet iterationer som kan köras parallellt.

> [!NOTE] 
> Om du anger att "för varje"-åtgärd ska köras sekventiellt antingen med hjälp av designern eller kodvyn, ställer du inte in åtgärdens `operationOptions` egenskap `Sequential` i kodvyn. Annars får du ett verifierings fel. Mer information finns i [Kör "för varje" slingor i turordning](#sequential-for-each).

#### <a name="edit-in-code-view"></a>Redigera i kodvyn 

I den underliggande "för varje"-definition lägger du till eller uppdaterar `runtimeConfiguration.concurrency.repetitions` egenskapen, som kan ha ett värde som sträcker sig från `1` och `50` .

Här är ett exempel som begränsar samtidiga körningar till 10 iterationer:

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 10
      }
   }
}
```

Mer information finns i [konfigurations inställningar för körning](#runtime-config-options).

#### <a name="edit-in-logic-apps-designer"></a>Redigera i Logic Apps designer

1. I **för varje** åtgärd, i det övre högra hörnet, väljer du knappen med tre punkter (**...**) och väljer sedan **Inställningar**.

1. Under **Samtidighets kontroll** ställer du in **Samtidighets kontroll** på **på**.

1. Dra skjutreglaget **för parallellitet** till det värde som du vill använda. Om du vill köra din Logic app i tur och ordning drar du skjutreglaget till **1**.

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>Begränsning av ändrings väntande körningar

Som standard körs Logic app-arbetsflöden på samma tidpunkt (samtidigt eller parallellt). Detta innebär att varje Utlös ande instans utlöses innan den tidigare aktiva arbets flödes instansen har körts. Det finns dock en [standard gräns](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)för antalet instanser som körs samtidigt. Om antalet arbets flödes instanser som körs samtidigt når den här gränsen måste alla nya instanser vänta på att köras.

Antalet väntande körningar har också en [standard gräns](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). När antalet väntande körningar når den här gränsen, accepterar Logic Appss motorn inte längre nya körningar. Förfrågningar och webhook-utlösare returnerar 429 fel och återkommande utlösare startar hoppar över avsöknings försök.

Du kan inte bara [ändra standard gränsen för utlösarens samtidighet](#change-trigger-concurrency), men du kan också ändra standard gränsen för väntande körningar. I den underliggande utlösnings definitionen lägger du till `runtimeConfiguration.concurrency.maximumWaitingRuns` egenskapen, som kan ha ett värde som sträcker `1` sig från till `100` .

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "maximumWaitingRuns": 50
      }
   }
}
```

Mer information finns i [konfigurations inställningar för körning](#runtime-config-options).

<a name="sequential-trigger"></a>

### <a name="trigger-instances-sequentially"></a>Utlös instanser sekventiellt

Om du vill köra varje Logic app-arbetsflöde enbart efter att den föregående instansen har slutförts, anger du att utlösaren ska köras sekventiellt. Du kan använda antingen kodvyn eller Logic Apps designer, eftersom ändringar i samtidighets inställningarna i designern också lägger till eller uppdaterar `runtimeConfiguration.concurrency.runs` egenskapen i den underliggande utlösaren och vice versa.

> [!NOTE] 
> När du ställer in en utlösare att köras sekventiellt antingen med hjälp av designern eller kodvyn, ska du inte ange utlösarens `operationOptions` egenskap till `Sequential` i kodvyn. Annars får du ett verifierings fel. 

#### <a name="edit-in-code-view"></a>Redigera i kodvyn

Ange någon av dessa egenskaper i utlösnings definitionen, men inte båda. 

Ställ in `runtimeConfiguration.concurrency.runs` egenskapen på `1` :

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 1
      }
   }
}
```

*eller*

Ställ in `operationOptions` egenskapen på `SingleInstance` :

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "operationOptions": "SingleInstance"
}
```

Mer information finns i [konfigurations inställningar](#runtime-config-options) och [Åtgärds alternativ](#operation-options)för körning.

#### <a name="edit-in-logic-apps-designer"></a>Redigera i Logic Apps designer

1. I utlösarens övre högra hörn väljer du knappen med tre punkter (**...**) och väljer sedan **Inställningar**.

1. Under **concurrency-kontroll** anger du **begränsa** till **på**. 

1. Dra skjutreglaget **för parallellitet** till talet `1` . 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>Kör "för varje" slingor i turordning

Om du vill köra en "for each"-loop enbart efter att den föregående iterationen är klar, ställer du in "for each"-åtgärden så att den körs sekventiellt. Du kan använda antingen kodvyn eller Logic Apps designer eftersom du ändrar åtgärdens samtidighet via designern även lägger till eller uppdaterar `runtimeConfiguration.concurrency.repetitions` egenskapen i den underliggande åtgärds definitionen och vice versa.

> [!NOTE] 
> När du ställer in en "för varje"-åtgärd som ska köras sekventiellt antingen med hjälp av redigeraren för designern eller kodvyn ska du inte ange `operationOptions` egenskapen som `Sequential` i kodvyn. Annars får du ett verifierings fel. 

#### <a name="edit-in-code-view"></a>Redigera i kodvyn

I åtgärds definitionen anger du någon av dessa egenskaper, men inte båda. 

Ställ in `runtimeConfiguration.concurrency.repetitions` egenskapen på `1` :

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 1
      }
   }
}
```

*eller*

Ställ in `operationOptions` egenskapen på `Sequential` :

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

Mer information finns i [konfigurations inställningar](#runtime-config-options) och [Åtgärds alternativ](#operation-options)för körning.

#### <a name="edit-in-logic-apps-designer"></a>Redigera i Logic Apps designer

1. I rutan **för varje** åtgärds övre högra hörn väljer du knappen med tre punkter (**...**) och väljer sedan **Inställningar**.

1. Under **Samtidighets kontroll** ställer du in **Samtidighets kontroll** på **på**.

1. Dra skjutreglaget **för parallellitet** till talet `1` .

<a name="disable-asynchronous-pattern"></a>

### <a name="run-actions-in-a-synchronous-operation-pattern"></a>Köra åtgärder i ett synkront åtgärds mönster

Som standard följer de HTTP-åtgärder och APIConnection-åtgärder i Azure Logic Apps som följer standard [*mönstret för asynkrona*](/azure/architecture/patterns/async-request-reply)åtgärder, medan svars åtgärden följer *synkront åtgärds mönster*. Det asynkrona mönstret anger att efter att en åtgärd anropar eller skickar en begäran till angiven slut punkt, tjänst, system eller API, returnerar mottagaren omedelbart ett ["202 accepterat"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3) -svar. Den här koden bekräftar att mottagaren har accepterat begäran men inte avslutat bearbetning. Svaret kan innehålla ett `location` huvud som anger webb adressen och ett uppdaterings-ID som anroparen kan använda för att söka efter eller kontrol lera status för den asynkrona begäran tills mottagaren slutar bearbeta och returnerar ett svar med ["200 OK"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1) eller något annat icke-202-svar. Mer information finns i avsnittet om [asynkron mikrotjänst integrering framtvingar sig oberoende av mikrotjänster](/azure/architecture/microservices/design/interservice-communication#synchronous-versus-asynchronous-messaging).

* I Logic Apps designer har HTTP-åtgärden, APIConnection-åtgärder och svars åtgärd inställningen **asynkront mönster** . När den här inställningen är aktive rad anger den att anroparen inte väntar på att bearbetningen ska slutföras och kan gå vidare till nästa åtgärd, men fortsätter att kontrol lera statusen tills bearbetningen stoppas. Om inställningen är inaktive rad anger den här inställningen att anroparen väntar på att bearbetningen ska slutföras innan den går vidare till nästa åtgärd. Du hittar den här inställningen genom att följa dessa steg:

  1. I HTTP-åtgärdens namn List väljer du knappen med tre punkter (**...**), som öppnar åtgärdens inställningar.

  1. Hitta inställningen för **asynkrona mönster** .

     ![Inställningen "asynkront mönster"](./media/logic-apps-workflow-actions-triggers/asynchronous-pattern-setting.png)

* I åtgärdens underliggande JavaScript Object Notation-definition (JSON) följer åtgärden för HTTP-åtgärder och APIConnection implicit det asynkrona åtgärds mönstret.

I vissa fall kanske du vill att en åtgärd ska följa synkront mönster i stället. Om du till exempel använder HTTP-åtgärden kanske du vill:

* [Undvik HTTP-tidsgräns för tids krävande uppgifter](../connectors/connectors-native-http.md#avoid-http-timeouts)
* [Inaktivera kontroll av plats rubriker](../connectors/connectors-native-http.md#disable-location-header-check)

I dessa fall kan du göra en åtgärd som körs synkront med hjälp av följande alternativ:

* Ersätt avsöknings versionen av åtgärden med en webhook-version, om den är tillgänglig.

* Inaktivera åtgärdens asynkrona beteende genom att följa något av alternativen:

  * [Inaktivera inställningen för **asynkront mönster**](#turn-off-asynchronous-pattern-setting)i Logic App Designer.

  * I åtgärdens underliggande JSON-definition [lägger du till `"DisableAsyncPattern"` alternativet åtgärd](#add-disable-async-pattern-option).

<a name="turn-off-asynchronous-pattern-setting"></a>

#### <a name="turn-off-asynchronous-pattern-setting"></a>Inaktivera **asynkron mönster** inställning

1. I Logic Apps designer, i åtgärdens namn List, väljer du knappen med tre punkter (**...**), som öppnar åtgärdens inställningar.

1. Hitta inställningen för **asynkront mönster** **, aktivera inställningen om den** är aktive rad och välj **färdig**.

   ![Inaktivera inställningen för asynkront mönster](./media/logic-apps-workflow-actions-triggers/disable-asynchronous-pattern-setting.png)

<a name="add-disable-async-pattern-option"></a>

#### <a name="disable-asynchronous-pattern-in-actions-json-definition"></a>Inaktivera asynkront mönster i åtgärdens JSON-definition

I åtgärdens underliggande JSON-definition lägger du till och anger [egenskapen "operationOptions"](#operation-options) till `"DisableAsyncPattern"` under åtgärdens `"inputs"` avsnitt, till exempel:

```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

<a name="authenticate-triggers-actions"></a>

## <a name="authenticate-triggers-and-actions"></a>Autentisera utlösare och åtgärder

HTTP-och HTTPS-slutpunkter stöder olika typer av autentisering. Baserat på utlösaren eller åtgärden som du använder för att göra utgående anrop eller begär Anden om åtkomst till dessa slut punkter, kan du välja mellan olika typer av autentiseringstyper. Mer information finns i [lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [språk för arbets flödes definition](../logic-apps/logic-apps-workflow-definition-language.md)