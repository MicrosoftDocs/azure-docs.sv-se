---
title: Hantera fel och undantag i arbets flöden
description: Lär dig hur du hanterar fel och undantag som inträffar i automatiserade uppgifter och arbets flöden som skapats med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: klam, estfan, logicappspm
ms.date: 01/11/2020
ms.topic: article
ms.openlocfilehash: a0c8286b2fb36642723ae28b8bc88e9e49f8a8fb
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577945"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Hantera fel och undantag i Azure Logic Apps

Sättet att integrera en integrerings arkitektur på lämpligt sätt hanterar avbrott eller problem som orsakas av beroende system kan medföra en utmaning. För att hjälpa dig att skapa robusta och elastiska integreringar som hanterar problem och fel på ett smidigt sätt ger Logic Apps en förstklassig upplevelse för att hantera fel och undantag.

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Principer för återförsök

För flest grundläggande undantag och fel hantering kan du använda en *princip för återförsök* i alla åtgärder eller utlösare där stöd finns, till exempel se [http-åtgärd](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger). En princip för återförsök anger om och hur åtgärden eller utlösaren försöker att utföra en begäran när den ursprungliga tids gränsen för begäran går ut eller Miss lyckas, vilket är en begäran som resulterar i ett 408-, 429-eller 5XX-svar. Om ingen annan princip för återförsök används används standard principen.

Här är princip typerna för återförsök:

| Typ | Description |
|------|-------------|
| **Standardvärde** | Den här principen skickar upp till fyra återförsök med *exponentiellt ökande* intervall, som skalas med 7,5 sekunder, men är ett tak mellan 5 och 45 sekunder. |
| **Exponentiellt intervall**  | Den här principen väntar ett slumpmässigt intervall som väljs från ett exponentiellt växande intervall innan nästa förfrågan skickas. |
| **Fast intervall**  | Den här principen väntar det angivna intervallet innan nästa förfrågan skickas. |
| **Ingen**  | Skicka inte begäran igen. |
|||

Information om begränsningar för återförsök finns i [Logic Apps gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md#http-limits).

### <a name="change-retry-policy"></a>Ändra princip för återförsök

Följ dessa steg om du vill välja en annan princip för återförsök:

1. Öppna din Logic app i Logic App Designer.

1. Öppna **inställningarna** för en åtgärd eller utlösare.

1. Om åtgärden eller utlösaren stöder principer för återförsök väljer du den typ som du vill använda under **princip för återförsök**.

Eller så kan du manuellt ange principen för återförsök i `inputs` avsnittet för en åtgärd eller utlösare som stöder principer för återförsök. Om du inte anger en princip för återförsök använder åtgärden standard principen.

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": {
      "<action-specific-inputs>",
      "retryPolicy": {
         "type": "<retry-policy-type>",
         "interval": "<retry-interval>",
         "count": <retry-attempts>,
         "minimumInterval": "<minimum-interval>",
         "maximumInterval": "<maximum-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*Obligatoriskt*

| Värde | Typ | Description |
|-------|------|-------------|
| <*återförsök-princip-typ*> | Sträng | Den princip typ för återförsök som du vill använda: `default` , `none` , `fixed` eller `exponential` |
| <*retry-intervall*> | Sträng | Återförsöksintervall där värdet måste använda [ISO 8601-formatet](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Standardvärdet för minimi intervallet är `PT5S` och det maximala intervallet är `PT1D` . När du använder exponentiell intervall princip kan du ange olika minimi-och max värden. |
| <*försök-försök*> | Integer | Antalet återförsök som måste vara mellan 1 och 90 |
||||

*Valfritt*

| Värde | Typ | Description |
|-------|------|-------------|
| <*lägsta-intervall*> | Sträng | För principen för exponentiella intervall, det minsta intervallet för det slumpmässigt valda intervallet i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
| <*högsta-intervall*> | Sträng | För principen för exponentiella intervall är det största intervallet för det slumpmässigt valda intervallet i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) |
||||

Här är mer information om de olika princip typerna.

<a name="default-retry"></a>

### <a name="default"></a>Standardvärde

Om du inte anger en princip för återförsök använder åtgärden standard principen, vilket faktiskt är en [exponentiell intervall princip](#exponential-interval) som skickar upp till fyra återförsök med exponentiellt ökande intervall som skalas med 7,5 sekunder. Intervallet är ett tak mellan 5 och 45 sekunder.

Även om det inte uttryckligen definieras i din åtgärd eller utlösare, så är det här hur standard principen beter sig i ett exempel på HTTP-åtgärd:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://myAPIendpoint/api/action",
      "retryPolicy" : {
         "type": "exponential",
         "interval": "PT7S",
         "count": 4,
         "minimumInterval": "PT5S",
         "maximumInterval": "PT1H"
      }
   },
   "runAfter": {}
}
```

### <a name="none"></a>Inget

Om du vill ange att åtgärden eller utlösaren inte ska försöka utföra misslyckade förfrågningar igen, anger du <*återförsök-princip-typ*> till `none` .

### <a name="fixed-interval"></a>Fast intervall

Om du vill ange att åtgärden eller utlösaren ska vänta det angivna intervallet innan du skickar nästa begäran, anger du <*återförsök-princip-typ*> till `fixed` .

*Exempel*

Den här principen för återförsök försöker hämta de senaste nyheterna två gånger efter den första misslyckade begäran med 30 sekunders fördröjning mellan varje försök:

```json
"Get_latest_news": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest",
      "retryPolicy": {
         "type": "fixed",
         "interval": "PT30S",
         "count": 2
      }
   }
}
```

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>Exponentiellt intervall

Om du vill ange att åtgärden eller utlösaren ska vänta ett slumpmässigt intervall innan nästa förfrågan skickas, anger du <*återförsök-princip-typ*> till `exponential` . Det slumpmässiga intervallet väljs från ett exponentiellt växande intervall. Alternativt kan du åsidosätta standard minimi-och Max intervallen genom att ange egna minsta och högsta intervall.

**Slumpmässiga variabel intervall**

I den här tabellen visas hur Logic Apps genererar en enhetlig slumpmässig variabel i det angivna intervallet för varje nytt försök upp till och med antalet återförsök:

| Antal återförsök | Minsta intervall | Maximalt intervall |
|--------------|------------------|------------------|
| 1 | Max (0, <*lägsta intervall*>) | min (intervall, <*maximalt intervall*>) |
| 2 | Max (intervall, <*lägsta intervall*>) | min (2 * intervall, <*maximalt intervall*>) |
| 3 | Max (2 * intervall, <*lägsta-intervall*>) | minsta (4 * intervall, <*maximalt intervall*>) |
| 4 | Max (4 * intervall, <*lägsta-intervall*>) | min (8 * intervall, <*maximalt intervall*>) |
| .... | .... | .... |
||||

<a name="control-run-after-behavior"></a>

## <a name="catch-and-handle-failures-by-changing-run-after-behavior"></a>Fånga och hantera problem genom att ändra "kör efter"-beteende

När du lägger till åtgärder i Logic App Designer, deklarerar du implicit den ordning som ska användas för att köra dessa åtgärder. När en åtgärd har slutförts markeras åtgärden med en status som `Succeeded` ,, `Failed` `Skipped` eller `TimedOut` . I varje åtgärds definition `runAfter` anger egenskapen den föregående åtgärden som måste slutföras först och de statusar som tillåts för den föregående aktiviteten innan den efterföljande åtgärden kan köras. Som standard körs en åtgärd som du lägger till i designern bara när den föregående aktiviteten har slutförts med `Succeeded` status.

När en åtgärd genererar ett ohanterat fel eller undantag, markeras åtgärden `Failed` och eventuella efterföljande åtgärder markeras `Skipped` . Om det här beteendet inträffar för en åtgärd som har parallella grenar, följer Logic Appss motorn de andra grenarna för att fastställa deras slut för ande status. Om en gren till exempel slutar med en `Skipped` åtgärd, baseras den här grenens slut för ande status på den överhoppade åtgärdens föregående status. När Logic app-körningen är klar, fastställer motorn hela körningens status genom att utvärdera alla gren statusar. Om en gren slutar fungera markeras hela körningen av Logic app `Failed` .

![Exempel som visar hur körnings status utvärderas](./media/logic-apps-exception-handling/status-evaluation-for-parallel-branches.png)

För att se till att en åtgärd fortfarande kan köras trots dess föregångare status [anpassar du beteendet "kör efter"-beteende](#customize-run-after) för att hantera de föregående aktiviteternas status som misslyckades.

<a name="customize-run-after"></a>

### <a name="customize-run-after-behavior"></a>Anpassa "kör efter"-beteende

Du kan anpassa åtgärdens "kör efter"-beteende så att åtgärden körs när den föregående personens status är antingen `Succeeded` , `Failed` , `Skipped` `TimedOut` eller någon av dessa status värden. Om du till exempel vill skicka ett e-postmeddelande när den föregående åtgärden i Excel Online `Add_a_row_into_a_table` är markerad `Failed` , i stället för `Succeeded` , ändrar du beteendet "kör efter" genom att följa antingen steg:

* I designvyn väljer du knappen med ellipser (**...**) och väljer sedan **Konfigurera kör efter**.

  ![Konfigurera kör efter-beteende för en åtgärd](./media/logic-apps-exception-handling/configure-run-after-property-setting.png)

  Åtgärds formen visar den standard status som krävs för den föregående åtgärden, som **lägger till en rad i en tabell** i det här exemplet:

  ![Standard beteendet "kör efter" för en åtgärd](./media/logic-apps-exception-handling/change-run-after-property-status.png)

  Ändra beteendet "kör efter" till den status som du vill ha, vilket **har misslyckats** i det här exemplet:

  ![Ändra "kör efter"-beteende till "har misslyckats"](./media/logic-apps-exception-handling/run-after-property-status-set-to-failed.png)

  Om du vill ange att åtgärden ska köras om den föregående åtgärden har marker ATS som `Failed` `Skipped` eller `TimedOut` väljer du de andra statusvärdena:

  ![Ändra "kör efter"-beteendet till att ha någon annan status](./media/logic-apps-exception-handling/run-after-property-multiple-statuses.png)

* I kodvyn, i åtgärdens JSON-definition, redigerar du `runAfter` egenskapen, som följer den här syntaxen:

  ```json
  "<action-name>": {
     "inputs": {
        "<action-specific-inputs>"
     },
     "runAfter": {
        "<preceding-action>": [
           "Succeeded"
        ]
     },
     "type": "<action-type>"
  }
  ```

  I det här exemplet ändrar du `runAfter` egenskapen från `Succeeded` till `Failed` :

  ```json
  "Send_an_email_(V2)": {
     "inputs": {
        "body": {
           "Body": "<p>Failed to&nbsp;add row to &nbsp;@{body('Add_a_row_into_a_table')?['Terms']}</p>",,
           "Subject": "Add row to table failed: @{body('Add_a_row_into_a_table')?['Terms']}",
           "To": "Sophia.Owen@fabrikam.com"
        },
        "host": {
           "connection": {
              "name": "@parameters('$connections')['office365']['connectionId']"
           }
        },
        "method": "post",
        "path": "/v2/Mail"
     },
     "runAfter": {
        "Add_a_row_into_a_table": [
           "Failed"
        ]
     },
     "type": "ApiConnection"
  }
  ```

  Om du vill ange att åtgärden ska köras om den föregående åtgärden har marker ATS som `Failed` `Skipped` eller `TimedOut` lägger du till de andra statusvärdena:

  ```json
  "runAfter": {
     "Add_a_row_into_a_table": [
        "Failed", "Skipped", "TimedOut"
     ]
  },
  ```

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Utvärdera åtgärder med omfattningar och deras resultat

Precis som när du kör steg efter enskilda åtgärder med `runAfter` egenskapen, kan du gruppera åtgärder tillsammans i ett [omfång](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Du kan använda omfattningar när du vill gruppera åtgärder logiskt, utvärdera Omfattningens sammanställda status och utföra åtgärder baserat på denna status. När alla åtgärder i en omfattning har slutförts, får själva omfattningen sin egen status.

Om du vill kontrol lera Omfattningens status kan du använda samma villkor som du använder för att kontrol lera appens körnings status, till exempel, `Succeeded` `Failed` och så vidare.

Som standard markeras omfångets status som standard när alla åtgärder i omfånget lyckas `Succeeded` . Om den slutliga åtgärden i ett omfång resulterar i `Failed` eller `Aborted` , markeras omfångets status `Failed` .

Om du vill fånga undantag i ett `Failed` omfång och köra åtgärder som hanterar dessa fel kan du använda `runAfter` egenskapen för den `Failed` omfattningen. På så sätt kan du, om *några* åtgärder i omfånget Miss lyckas, och du använder `runAfter` egenskapen för den omfattningen, skapa en enskild åtgärd för att fånga upp felen.

Begränsningar för omfång finns i [gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md).

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>Få kontext och resultat för problem

Även om det är praktiskt att fånga fel från ett omfång, kan du också behöva kontext för att förstå exakt vilka åtgärder som misslyckats plus eventuella fel eller status koder som returnerades.

[`result()`](../logic-apps/workflow-definition-language-functions-reference.md#result)Funktionen ger kontext om resultatet från alla åtgärder i ett omfång. `result()`Funktionen accepterar en enda parameter, som är omfångets namn och returnerar en matris som innehåller alla åtgärds resultat inom det omfånget. Dessa åtgärds objekt innehåller samma attribut som `actions()` objektet, till exempel start tid, slut tid, status, indata, korrelations-ID och utdata. Om du vill skicka kontext för åtgärder som misslyckats inom ett omfång kan du enkelt para ihop ett `@result()` uttryck med `runAfter` egenskapen.

Om du vill köra en åtgärd för varje åtgärd i ett omfång som har ett `Failed` resultat, och för att filtrera matrisen med resultat nedåt till de misslyckade åtgärderna, kan du koppla ett `@result()` uttryck med en [**filter mat ris**](logic-apps-perform-data-operations.md#filter-array-action) åtgärd och en [**for each**](../logic-apps/logic-apps-control-flow-loops.md) -slinga. Du kan ta den filtrerade resultat mat ris och utföra en åtgärd för varje haveri med hjälp av `For_each` slingan.

Här är ett exempel, följt av en detaljerad förklaring, som skickar en HTTP POST-begäran med svars texten för åtgärder som misslyckats inom omfånget "My_Scope":

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": "@result('My_Scope')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "My_Scope": [
         "Failed"
      ]
    }
},
"For_each": {
   "type": "foreach",
   "actions": {
      "Log_exception": {
         "type": "Http",
         "inputs": {
            "method": "POST",
            "body": "@item()['outputs']['body']",
            "headers": {
               "x-failed-action-name": "@item()['name']",
               "x-failed-tracking-id": "@item()['clientTrackingId']"
            },
            "uri": "http://requestb.in/"
         },
         "runAfter": {}
      }
   },
   "foreach": "@body('Filter_array')",
   "runAfter": {
      "Filter_array": [
         "Succeeded"
      ]
   }
}
```

Här är en detaljerad genom gång som beskriver vad som händer i det här exemplet:

1. För att få resultatet från alla åtgärder i "My_Scope" använder **filter mat ris** åtgärden detta filter uttryck: `@result('My_Scope')`

1. Villkoret för **filter mat ris** är ett `@result()` objekt som har en status som är lika med `Failed` . Det här villkoret filtrerar matrisen som innehåller alla åtgärds resultat från "My_Scope" ned till en matris med endast misslyckade åtgärds resultat.

1. Utför en `For_each` loop-åtgärd på *filtrerade mat ris* utdata. Det här steget utför en åtgärd för varje misslyckat åtgärds resultat som tidigare har filtrerats.

   Om en enskild åtgärd i omfånget Miss lyckas körs åtgärderna i `For_each` slingan bara en gång. Flera misslyckade åtgärder orsakar en åtgärd per fel.

1. Skicka ett HTTP-inlägg på `For_each` objektets svars text, vilket är `@item()['outputs']['body']` uttrycket.

   `@result()`Objekt formen är samma som `@actions()` formen och kan parsas på samma sätt.

1. Inkludera två anpassade huvuden med det misslyckade åtgärds namnet ( `@item()['name']` ) och klient spårnings-ID: t för misslyckad körning ( `@item()['clientTrackingId']` ).

För referens är här ett exempel på ett enda `@result()` objekt `name` som visar `body` egenskaperna, och `clientTrackingId` som är parsade i föregående exempel. Utanför en `For_each` åtgärd `@result()` returnerar en matris med dessa objekt.

```json
{
   "name": "Example_Action_That_Failed",
   "inputs": {
      "uri": "https://myfailedaction.azurewebsites.net",
      "method": "POST"
   },
   "outputs": {
      "statusCode": 404,
      "headers": {
         "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
         "Server": "Microsoft-IIS/8.0",
         "X-Powered-By": "ASP.NET",
         "Content-Length": "68",
         "Content-Type": "application/json"
      },
      "body": {
         "code": "ResourceNotFound",
         "message": "/docs/folder-name/resource-name does not exist"
      }
   },
   "startTime": "2016-08-11T03:18:19.7755341Z",
   "endTime": "2016-08-11T03:18:20.2598835Z",
   "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
   "clientTrackingId": "08587307213861835591296330354",
   "code": "NotFound",
   "status": "Failed"
}
```

Om du vill utföra olika mönster för undantags hantering kan du använda de uttryck som tidigare beskrivits i den här artikeln. Du kan välja att köra en enskild undantags hanterings åtgärd utanför den omfattning som godkänner hela den filtrerade matrisen med fel och ta bort `For_each` åtgärden. Du kan också inkludera andra användbara egenskaper från `\@result()` svaret enligt beskrivningen ovan.

## <a name="set-up-azure-monitor-logs"></a>Konfigurera Azure Monitor loggar

De tidigare mönstren är ett bra sätt att hantera fel och undantag i en körning, men du kan också identifiera och svara på fel oberoende av själva körningen. [Azure Monitor](../azure-monitor/overview.md) är ett enkelt sätt att skicka alla arbets flödes händelser, inklusive körnings-och åtgärds status, till en [Log Analytics arbets yta](../azure-monitor/logs/data-platform-logs.md), [Azure Storage-konto](../storage/blobs/storage-blobs-overview.md)eller [Azure-Event Hubs](../event-hubs/event-hubs-about.md).

Om du vill utvärdera körnings status kan du övervaka loggarna och måtten eller publicera dem i alla övervaknings verktyg som du föredrar. Ett möjligt alternativ är att strömma alla händelser genom att Event Hubs i [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). I Stream Analytics kan du skriva Live-frågor baserat på eventuella avvikelser, genomsnitt eller fel från diagnostikloggar. Du kan använda Stream Analytics för att skicka information till andra data källor, till exempel köer, ämnen, SQL, Azure Cosmos DB eller Power BI.

## <a name="next-steps"></a>Nästa steg

* [Se hur en kund skapar fel hantering med Azure Logic Apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Hitta fler Logic Apps exempel och scenarier](../logic-apps/logic-apps-examples-and-scenarios.md)
