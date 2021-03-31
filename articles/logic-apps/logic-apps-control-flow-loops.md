---
title: Lägg till slingor för att upprepa åtgärder
description: Skapa loopar som upprepar arbetsflödesåtgärder eller processmatriser i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/05/2019
ms.openlocfilehash: aa4be5852b4f8af00346a3ea9a86b13a85f99824
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93358464"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Skapa loopar som upprepar arbetsflödesåtgärder eller processmatriser i Azure Logic Apps

Om du vill bearbeta en matris i din Logic app kan du skapa en ["förgrunds"-slinga](#foreach-loop). Den här slingan upprepar en eller flera åtgärder för varje objekt i matrisen. För att begränsa antalet mat ris objekt som en "förgrunds" slinga kan bearbeta, se [samtidighets-, loop-och avbatchorders gränser](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

Om du vill upprepa åtgärder tills ett villkor uppfylls eller ett tillstånd ändras, kan du skapa en ["till"-loop](#until-loop). Din Logic app kör först alla åtgärder i slingan och kontrollerar sedan villkoret eller tillståndet. Om villkoret är uppfyllt stoppas loopen. Annars upprepas loopen. För standard-och Max gränsen för antalet "till"-slingor som en Logic app-körning kan ha, se [samtidighets-, loop-och avbatchorders gränser](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

> [!TIP]
> Om du har en utlösare som tar emot en matris och vill köra ett arbets flöde för varje mat ris objekt, kan du *Avgruppera* matrisen med [egenskapen **SplitOn** trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>"Förgrunds"-loop

En "förgrunds åtgärd" upprepar en eller flera åtgärder på varje mat ris objekt och fungerar bara på matriser. Här följer några saker att tänka på när du använder "förgrunds" slingor:

* "Förgrunds"-loopen kan bearbeta ett begränsat antal mat ris objekt. För den här gränsen, se [samtidighets-, slingor-och debatch-gränser](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

* Som standard körs iterationer i en "förgrunds" loop vid samma tidpunkt eller parallellt. Det här beteendet skiljer sig från automatisk [energi förbrukning **för varje** slinga](/power-automate/apply-to-each) där iterationer körs en i taget eller sekventiellt. Du kan dock [ställa in sekventiella upprepningar av "förgrunds slingor"](#sequential-foreach-loop). Om du till exempel vill pausa nästa iteration i en "förgrunds" slinga med hjälp av [fördröjnings åtgärden](../connectors/connectors-native-delay.md)måste du ställa in loopen så att den körs sekventiellt.

  Undantaget till standard beteendet är kapslade slingor där iterationer alltid körs sekventiellt, inte parallellt. Om du vill köra åtgärder parallellt för objekt i en kapslad slinga skapar och [anropar du en underordnad Logic-app](../logic-apps/logic-apps-http-endpoint.md).

* För att få förutsägbara resultat från åtgärder på variabler under varje upprepnings slinga, kör du dessa slingor i tur och ordning. Till exempel, när en slinga som körs samtidigt, ökar, minskar och lägger till variabel åtgärder returnerar förutsägbara resultat. Men under varje iteration i den körnings bara loopen kan dessa åtgärder returnera oförutsägbara resultat. 

* Åtgärder i en "förgrunds åtgärd" använder [`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
uttryck för att referera till och bearbeta varje objekt i matrisen. Om du anger data som inte finns i en matris, Miss lyckas Logic app-arbetsflödet. 

I den här exempel Logic-appen skickas en daglig sammanfattning för en RSS-feed för webbplatsen. Appen använder en "förgrunds"-loop som skickar ett e-postmeddelande för varje nytt objekt.

1. [Skapa den här exempel Logic-appen](../logic-apps/quickstart-create-first-logic-app-workflow.md) med ett Outlook.com-konto eller ett arbets-eller skol konto.

2. Lägg till en "förgrunds"-loop mellan RSS-utlösare och skicka e-post. 

   1. Om du vill lägga till en slinga mellan steg flyttar du pekaren över pilen mellan stegen. 
   Välj **plus tecknet** ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

      ![Välj Lägg till en åtgärd](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. Under sökrutan väljer du **Alla**. Skriv "för varje" som filter i rutan Sök. Välj den här åtgärden i listan åtgärder: **för varje-kontroll**

      ![Lägg till "för varje"-slinga](media/logic-apps-control-flow-loops/select-for-each.png)

3. Nu ska du bygga slingan. Under **Välj utdata från föregående steg** efter listan **Lägg till dynamiskt innehåll** visas väljer du matrisen **Feed-Länkar** , som är utdata från RSS-utlösaren. 

   ![Välj från listan med dynamiskt innehåll](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Du kan *bara* välja mat ris utdata från föregående steg.

   Den valda matrisen visas nu här:

   ![Välj matris](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Om du vill köra en åtgärd på varje mat ris objekt drar du åtgärden **skicka e-post** till slingan. 

   Din Logic app kan se ut ungefär så här:

   ![Lägg till steg i "förgrunds"-loopen](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Spara logikappen. Om du vill testa din Logic app manuellt går du till verktygsfältet i designern och väljer **Kör**.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>"Förgrunds slinga"-definition (JSON)

Om du arbetar i kodvyn för din Logi Kap par kan du definiera `Foreach` slingan i din Logic Apps JSON-definition i stället, till exempel:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": {
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "@{item()}",
                  "Subject": "New CNN post @{triggerBody()?['publishDate']}",
                  "To": "me@contoso.com"
               },
               "host": {
                  "api": {
                     "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/office365"
                  },
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
      "foreach": "@triggerBody()?['links']",
      "runAfter": {}
   }
}
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>"Förgrunds"-slinga: sekventiell

Som standard körs cykler i en "förgrunds" loop parallellt. Om du vill köra varje cykel sekventiellt ställer du in loopens **sekventiella** alternativ. "Förgrunds" slingor måste köras sekventiellt när du har kapslade slingor eller variabler inuti slingor där du förväntar sig förutsägbara resultat. 

1. I loopens övre högra hörn väljer du **ellipser** (**...**) > **Inställningar**.

   ![I "förgrunds"-loopen väljer du "..." > "Inställningar"](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. Under **Samtidighets kontroll** aktiverar du **samtidighets kontroll** inställningen till **på**. Flytta skjutreglaget **för parallellitet** till **1** och välj **Slutför**.

   ![Aktivera samtidighets kontroll](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Om du arbetar med din Logic Apps JSON-definition kan du använda `Sequential` alternativet genom att lägga till `operationOptions` parametern, till exempel:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": { }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {},
      "operationOptions": "Sequential"
   }
}
```

<a name="until-loop"></a>

## <a name="until-loop"></a>"Until"-loop
  
Om du vill köra och upprepa åtgärder tills ett villkor har uppfyllts eller ett tillstånd ändras, ska du införa dessa åtgärder i en "till"-slinga. Din Logic app kör först alla åtgärder i slingan och kontrollerar sedan villkoret eller tillståndet. Om villkoret är uppfyllt stoppas loopen. Annars upprepas loopen. För standard-och Max gränsen för antalet "till"-slingor som en Logic app-körning kan ha, se [samtidighets-, loop-och avbatchorders gränser](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

Här följer några vanliga scenarier där du kan använda en "till"-slinga:

* Anropa en slut punkt tills du får det svar du önskar.

* Skapa en post i en databas. Vänta tills ett särskilt fält i posten godkänns. Fortsätt bearbetningen. 

Från och med 8:00 varje dag, ökar den här exempel Logic app en variabel tills variabelns värde är lika med 10. Logic App skickar sedan ett e-postmeddelande som bekräftar det aktuella värdet. 

> [!NOTE]
> De här stegen använder Office 365 Outlook, men du kan använda vilken e-postprovider som helst som Logic Apps stöder. 
> [Kontrol lera anslutnings listan här](/connectors/). Om du använder ett annat e-postkonto förblir de allmänna stegen desamma, men användar gränssnittet kan se lite annorlunda ut. 

1. Skapa en tom logikapp. I Logic App Designer väljer du **alla** under sökrutan. Sök efter "upprepning". 
   Välj den här utlösaren i listan utlösare: **upprepnings schema**

   ![Lägg till utlösare för upprepnings schema](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. Ange när utlösaren utlöses genom att ange intervall, frekvens och timme för dagen. Om du vill ange timmen väljer du **Visa avancerade alternativ**.

   ![Konfigurera upprepnings schema](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Egenskap | Värde |
   | -------- | ----- |
   | **Intervall** | 1 | 
   | **Frekvens** | Dag |
   | **Vid dessa timmar** | 8 |
   ||| 

1. Under utlösaren väljer du **nytt steg**. 
   Sök efter "variabler" och välj den här åtgärden: **initiera variabel-variabler**

   ![Lägg till åtgärden initiera variabel-variabler](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. Konfigurera din variabel med följande värden:

   ![Ange variabel egenskaper](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Egenskap | Värde | Beskrivning |
   | -------- | ----- | ----------- |
   | **Namn** | Gräns | Variabelns namn | 
   | **Typ** | Integer | Variabelns datatyp | 
   | **Värde** | 0 | Variabelns start värde | 
   |||| 

1. Under åtgärden **initiera variabel** väljer du **nytt steg**. 

1. Under sökrutan väljer du **Alla**. Sök efter "until" och välj den här åtgärden: **tills-Control**

   ![Lägg till loopen "till"](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. Bygg avslutnings villkoret för slingan genom att välja **Limit** -variabeln och **är Equal** -operatorn. 
   Ange **10** som jämförelse värde.

   ![Versions avslutnings villkor för att stoppa loop](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. I slingan väljer du **Lägg till en åtgärd**. 

1. Under sökrutan väljer du **Alla**. Sök efter "variabler" och välj den här åtgärden: **öka variabel-variabler**

   ![Lägg till åtgärd för att öka variabeln](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. I **namn** väljer du variabeln **Limit** . För **värde** anger du "1". 

     ![Öka "gräns" med 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. Utanför och under slingan väljer du **nytt steg**. 

1. Under sökrutan väljer du **Alla**. 
     Hitta och Lägg till en åtgärd som skickar e-post, till exempel: 

     ![Lägg till åtgärd som skickar e-post](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. Logga in på ditt e-postkonto om du uppmanas att göra det.

1. Ange egenskaperna för e-poståtgärden. Lägg till **Limit** -variabeln i ämnet. På så sätt kan du bekräfta att variabelns aktuella värde uppfyller det angivna villkoret, till exempel:

      ![Konfigurera e-postegenskaper](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | Egenskap | Värde | Beskrivning |
      | -------- | ----- | ----------- | 
      | **Till** | *\<email-address\@domain>* | Mottagarens e-postadress. För testning använder du din egen e-postadress. | 
      | **Ämne** | Det aktuella värdet för "Limit" är **begränsat** | Ange e-postmeddelandets ämne. I det här exemplet ska du se till att du inkluderar **Limit** -variabeln. | 
      | **Brödtext** | <*e-post – innehåll*> | Ange det e-postmeddelande innehåll som du vill skicka. I det här exemplet anger du vilken text du vill. | 
      |||| 

1. Spara logikappen. Om du vill testa din Logic app manuellt går du till verktygsfältet i designern och väljer **Kör**.

      När din logik börjar köras får du ett e-postmeddelande med det innehåll som du har angett:

      ![Mottagen e-post](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

<a name="prevent-endless-loops"></a>

## <a name="prevent-endless-loops"></a>Förhindra oändliga slingor

Loopen "tills" stoppar körningen baserat på dessa egenskaper, så se till att du ställer in värdena enligt följande:

* **Count**: det här värdet är det högsta antalet slingor som körs innan loopen avslutas. För standard-och Max gränsen för antalet "till"-slingor som en Logic app-körning kan ha, se [samtidighets-, loop-och avbatchorders gränser](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

* **Timeout**: det här värdet är den längsta tid som loopen körs innan den avslutas och anges i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601). För standardvärdet och Max gränsen för **timeout** -värdet, se [samtidighets-, loop-och debatch-gränser](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

  Timeout-värdet utvärderas för varje loop-cykel. Om en åtgärd i slingan tar längre tid än tids gränsen, stoppas inte den aktuella cykeln. Nästa cykel startar dock inte eftersom gräns villkoret inte är uppfyllt.

Om du vill ändra de här gränserna väljer du **ändra gränser** i instruktionen upprepa.

<a name="until-json"></a>

## <a name="until-definition-json"></a>"Until"-definition (JSON)

Om du arbetar i kodvyn för din Logi Kap par kan du definiera en `Until` slinga i din Logic Apps JSON-definition i stället, till exempel:

``` json
"actions": {
   "Initialize_variable": {
      // Definition for initialize variable action
   },
   "Send_an_email": {
      // Definition for send email action
   },
   "Until": {
      "type": "Until",
      "actions": {
         "Increment_variable": {
            "type": "IncrementVariable",
            "inputs": {
               "name": "Limit",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "expression": "@equals(variables('Limit'), 10)",
      // To prevent endless loops, an "Until" loop 
      // includes these default limits that stop the loop. 
      "limit": { 
         "count": 60,
         "timeout": "PT1H"
      },
      "runAfter": {
         "Initialize_variable": [
            "Succeeded"
         ]
      }
   }
}
```

I det här exemplet "till" tills "-loopen anropar en HTTP-slutpunkt, vilket skapar en resurs. Loopen stoppas när HTTP-svarets brödtext returnerar `Completed` status. För att förhindra oändliga slingor stoppas även loopen om något av dessa villkor inträffar:

* Loopen kördes 10 gånger som anges av `count` attributet. Standardvärdet är 60 gånger. 

* Loopen kördes i två timmar som anges av `timeout` attributet i ISO 8601-format. Standardvärdet är en timme.
  
``` json
"actions": {
   "myUntilLoopName": {
      "type": "Until",
      "actions": {
         "Create_new_resource": {
            "type": "Http",
            "inputs": {
               "body": {
                  "resourceId": "@triggerBody()"
               },
               "url": "https://domain.com/provisionResource/create-resource",
               "body": {
                  "resourceId": "@triggerBody()"
               }
            },
            "runAfter": {},
            "type": "ApiConnection"
         }
      },
      "expression": "@equals(triggerBody(), 'Completed')",
      "limit": {
         "count": 10,
         "timeout": "PT2H"
      },
      "runAfter": {}
   }
}
```

## <a name="get-support"></a>Få support

* För frågor, besök [sidan Microsoft Q&en fråga för Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* För att skicka in eller rösta på funktioner och förslag [Azure Logic Apps användarens feedback-webbplats](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Kör steg baserat på ett villkor (villkorliga uttryck)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Kör steg baserade på olika värden (Switch-instruktioner)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Köra eller sammanfoga parallella steg (grenar)](../logic-apps/logic-apps-control-flow-branches.md)
* [Kör steg baserat på grupperad åtgärds status (omfång)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
