---
title: Gruppera och kör åtgärder efter omfattning
description: Skapa begränsade åtgärder som körs baserat på grupp status i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: 95b5cc191ac6857bf8e1b09e70b22d928473fe03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92314846"
---
# <a name="run-actions-based-on-group-status-by-using-scopes-in-azure-logic-apps"></a>Kör åtgärder baserat på grupp status med hjälp av omfång i Azure Logic Apps

Om du vill köra åtgärder endast efter att en annan åtgärds grupp lyckades eller Miss lyckas, kan du gruppera dessa åtgärder i ett *omfång*. Den här strukturen är användbar när du vill organisera åtgärder som en logisk grupp, utvärdera gruppens status och utföra åtgärder som baseras på Omfattningens status. När alla åtgärder i en omfattning har slutförts får omfånget också sin egen status. Du kan till exempel använda omfattningar när du vill implementera [undantag och fel hantering](../logic-apps/logic-apps-exception-handling.md#scopes). 

Om du vill kontrol lera en omfattnings status kan du använda samma kriterier som du använder för att fastställa körnings status för logi Kap par, till exempel **lyckad**, **misslyckad**, **avbruten** och så vidare. Som standard markeras omfångets status som **slutförd** när alla åtgärder i omfånget lyckas. Men när en åtgärd i omfånget Miss lyckas eller avbryts, markeras omfångets status som **misslyckad**. Begränsningar för omfång finns i [gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md). 

Här är till exempel en övergripande Logic-app som använder en omfattning för att köra vissa åtgärder och ett villkor för att kontrol lera Omfattningens status. Om några åtgärder i omfånget Miss lyckas eller avslutas utan problem, markeras omfånget som **misslyckat** eller **avbrutet** , och Logic-appen skickar ett meddelande om att det inte gick att skicka ett meddelande. Om alla åtgärder som omfattas är slutförda, skickar Logic-appen ett meddelande med omfattningen "scope lyckades".

![Diagrammet visar Logic app-omfångets flöde med exempel på "omfånget misslyckades" och "omfattning lyckades".](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa exemplet i den här artikeln behöver du följande objekt:

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Ett e-postkonto från valfri e-postleverantör som stöds av Logic Apps. I det här exemplet används Outlook.com. Om du använder en annan provider förblir det allmänna flödet detsamma, men ditt användar gränssnitt ser annorlunda ut.

* En Bing Maps-nyckel. För att hämta den här nyckeln, se <a href="/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key" target="_blank">Hämta en Bing Maps-nyckel</a>.

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Skapa exempel på Logic-appen

Börja med att skapa den här exempel Logic-appen så att du kan lägga till ett scope senare:

![Skapa exempel på Logic-appen](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* En utlösare för **schemalagda händelser** som kontrollerar Bing Maps-tjänsten med ett intervall som du anger
* En **Bing Maps – Hämta flödes** åtgärd som kontrollerar res tiden mellan två platser
* En villkors instruktion som kontrollerar om res tiden överskrider din angivna res tid
* En åtgärd som skickar ett e-postmeddelande om att den aktuella res tiden överskrider din angivna tid

Du kan spara din Logi Kap par när som helst, så spara arbetet ofta.

1. Logga in på <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, om du inte redan gjort det. Skapa en tom logikapp.

1. Lägg till utlösaren **schema-upprepning** med följande inställningar: **Interval** = "1" och **frekvens** = "minut"

   ![Ställ in utlösare för "schema upprepning"](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > För att visuellt förenkla vyn och dölja varje åtgärds information i designern, komprimera varje åtgärds form när du går igenom de här stegen.

1. Lägg till **Bing Maps – Hämta flödes** åtgärd. 

   1. Om du inte redan har en Bing Maps-anslutning uppmanas du att skapa en anslutning.

      | Inställning | Värde | Beskrivning |
      | ------- | ----- | ----------- |
      | **Anslutnings namn** | BingMapsConnection | Ange ett namn på anslutningen. | 
      | **API-nyckel** | <*din-Bing-Maps-nyckel*> | Ange Bing Maps-nyckeln som du fick tidigare. | 
      ||||  

   1. Konfigurera åtgärden **Hämta väg** så som visas i tabellen under den här bilden:

      ![Konfigurera "Bing Maps-get Route"-åtgärd](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Mer information om dessa parametrar finns [Calculate a route](/bingmaps/rest-services/routes/calculate-a-route) (Beräkna en resväg).

      | Inställning | Värde | Beskrivning |
      | ------- | ----- | ----------- |
      | **Waypoint 1** (Platsmarkör 1) | <*har*> | Ange din flödes ursprung. | 
      | **Waypoint 2** (Platsmarkör 2) | <*ändamål*> | Ange vägens mål. | 
      | **Undvik** | Inget | Ange objekt som ska undvikas på vägen, till exempel vägar, väg tullar och så vidare. För möjliga värden, se [Beräkna en väg](/bingmaps/rest-services/routes/calculate-a-route). | 
      | **Optimera** | timeWithTraffic | Välj en parameter för att optimera vägen, till exempel avstånd, tid med aktuell trafik information och så vidare. I det här exemplet används detta värde: "timeWithTraffic" | 
      | **Avståndsenhet** | <*dina inställningar*> | Ange avstånds enheten för att beräkna din väg. I det här exemplet används detta värde: "mil" | 
      | **Travel mode** (Färdsätt) | Driving (Bil) | Ange rese läge för vägen. I det här exemplet används det här värdet "driver" | 
      | **Transit Date-Time** (Tid/datum för kollektivtrafik) | Inget | Gäller endast för överförings läge. | 
      | **Typ av överförings Date-Type** | Inget | Gäller endast för överförings läge. | 
      ||||  

1. [Lägg till ett villkor](../logic-apps/logic-apps-control-flow-conditional-statement.md) som kontrollerar om den aktuella res tiden med trafik överskrider den angivna tiden. 
   I det här exemplet följer du dessa steg:

   1. Byt namn på villkoret med den här beskrivningen: **om trafik tiden är mer än den angivna tiden**

   1. I kolumnen längst till vänster klickar du i rutan **Välj ett värde** så att listan med dynamiskt innehåll visas. I listan väljer du fältet **res varaktighets trafik** , som är i sekunder. 

      ![Skapa villkor](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. I rutan i mitten väljer du den här operatorn: **är större än**

   1. I kolumnen längst till höger anger du detta jämförelse värde, som är i sekunder och motsvarar 10 minuter: **600**

      När du är klar ser villkoret ut som i det här exemplet:

      ![Slut villkor](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. Lägg till åtgärden "skicka e-post" för din e-postprovider i listan **om sant** . 
   Konfigurera den här åtgärden genom att följa stegen under den här avbildningen:

   ![Lägg till åtgärden "Skicka ett e-postmeddelande" till grenen "om sant"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. I fältet **till** anger du din e-postadress i test syfte.

   1. I fältet **ämne** anger du den här texten:

      ```Time to leave: Traffic more than 10 minutes```

   1. I fältet **brödtext** anger du den här texten med ett avslutande blank steg: 

      ```Travel time:```

      När markören visas i fältet **brödtext** förblir den dynamiska innehålls listan öppen så att du kan välja alla parametrar som är tillgängliga i det här läget.

   1. Välj **Expression** (Uttryck) i listan med dynamiskt innehåll.

   1. Hitta och välj **div ()-** funktionen. 
      Placera markören inuti funktionens parenteser.

   1. När markören är inuti funktionens parenteser väljer du **dynamiskt innehåll** så att listan med dynamiskt innehåll visas. 
   
   1. I avsnittet **Hämta väg** väljer du trafikens **varaktighets trafik** .

      ![Välj "trafik varaktighets trafik"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. När fältet har lösts till JSON-format lägger du till ett **kommatecken** ( ```,``` ) följt av siffran ```60``` så att du konverterar värdet i **trafikens varaktighets trafik** från sekunder till minuter. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      Ditt uttryck ser nu ut som i det här exemplet:

      ![Slut uttryck](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. När du är klar väljer du **OK**.

   <!-- markdownlint-disable MD038 -->
   1. När uttrycket har lösts lägger du till den här texten med ett inledande blank steg: ``` minutes```
  
       Ditt **Body** -fält ser nu ut så här:

       ![Fältet "brödtext" är klart](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)
   <!-- markdownlint-enable MD038 -->

1. Spara logikappen.

Lägg sedan till ett omfång så att du kan gruppera vissa åtgärder och utvärdera deras status.

## <a name="add-a-scope"></a>Lägg till ett omfång

1. Om du inte redan har gjort det öppnar du din Logic app i Logic App Designer. 

1. Lägg till ett omfång på den arbets flödes plats som du vill ha. Om du till exempel vill lägga till ett omfång mellan befintliga steg i Logic app-arbetsflödet följer du dessa steg: 

   1. Flytta pekaren över den pil där du vill lägga till omfånget. 
   Välj **plus tecknet** ( **+** ) > **Lägg till en åtgärd**.

      ![Lägg till ett omfång](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. I rutan Sök anger du "omfång" som filter. 
   Välj **omfattnings** åtgärd.

## <a name="add-steps-to-scope"></a>Lägg till steg i omfång

1. Lägg nu till stegen eller dra befintliga steg som du vill köra inuti omfånget. I det här exemplet drar du dessa åtgärder till omfånget:
      
   * **Hämta väg**
   * **Om trafik tiden är mer än den angivna tiden**, som innehåller både de **sanna** och **falskt** grenarna

   Din Logic app ser nu ut som i det här exemplet:

   ![Omfattning tillagt](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. Under omfånget lägger du till ett villkor som kontrollerar Omfattningens status. Byt namn på villkoret med den här beskrivningen: **om omfånget misslyckades**

   ![Lägg till villkor för att kontrol lera omfattnings status](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. I villkoret lägger du till dessa uttryck som kontrollerar om omfångets status är lika med "Misslyckad" eller "avbruten". 

   1. Välj **Lägg** till om du vill lägga till ytterligare en rad. 

   1. I varje rad klickar du i den vänstra rutan så visas listan med dynamiskt innehåll. 
   Välj **uttryck** i listan med dynamiskt innehåll. I rutan Redigera anger du det här uttrycket och väljer sedan **OK**: 
   
      `result('Scope')[0]['status']`

      ![Skärm bild som visar rutan "uttryck" med resultat uttrycket markerat.](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. Välj **är lika** med som operator för båda raderna. 
   
   1. För jämförelse värdena i den första raden anger du `Failed` . 
   På den andra raden anger du `Aborted` . 

      När du är klar ser villkoret ut som i det här exemplet:

      ![Lägg till uttryck som kontrollerar Omfattningens status](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      Ange nu villkorets `runAfter` egenskap så att villkoret kontrollerar Omfattningens status och kör den matchande åtgärden som du definierar i senare steg.

   1. Välj knappen med **tre punkter** (...) på villkoret **om omfånget misslyckades** och välj sedan **Konfigurera kör efter**.

      ![Konfigurera egenskapen runAfter](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. Välj alla dessa scope-status: **är lyckad**, **har misslyckats**, **hoppas över** och **har nått tids gränsen**

      ![Välj omfattnings status](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. När du är klar väljer du **Klar**. 
   Villkoret visar nu ikonen "information".

1. I **IF True** och **IF false** grenar lägger du till de åtgärder som du vill utföra baserat på varje omfattnings status, till exempel skicka ett e-postmeddelande eller ett meddelande.

   ![Lägg till åtgärder som ska vidtas baserat på omfattnings status](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. Spara logikappen.

Din färdiga Logic-app ser nu ut som i det här exemplet:

![Den färdiga Logic-appen med omfång](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Testa ditt arbete

I verktygsfältet designer väljer du **Kör**. Om alla åtgärder som omfattas har slutförts får du ett meddelande om att "omfattning lyckades". Om några begränsade åtgärder inte lyckas får du ett meddelande om att det inte går att hitta ett omfång. 

<a name="scopes-json"></a>

## <a name="json-definition"></a>JSON-definition

Om du arbetar i kodvyn kan du definiera en omfattnings struktur i din Logic Apps JSON-definition i stället. Här är till exempel JSON-definitionen för utlösare och åtgärder i föregående Logic-app:

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    }
  }
}
```

```json
"actions": {
  "If_scope_failed": {
    "type": "If",
    "actions": {
      "Scope_failed": {
        "type": "ApiConnection",
        "inputs": {
          "body": {
            "Body": "Scope failed. Scope status: @{result('Scope')[0]['status']}",
            "Subject": "Scope failed",
            "To": "<your-email@domain.com>"
          },
          "host": {
            "connection": {
              "name": "@parameters('$connections')['outlook']['connectionId']"
            }
          },
          "method": "post",
          "path": "/Mail"
        },
        "runAfter": {}
      }
    },
    "else": {
      "actions": {
        "Scope_succeded": {
          "type": "ApiConnection",
          "inputs": {
            "body": {
              "Body": "Scope succeeded. Scope status: @{result('Scope')[0]['status']}",
              "Subject": "Scope succeeded",
              "To": "<your-email@domain.com>"
            },
            "host": {
              "connection": {
               "name": "@parameters('$connections')['outlook']['connectionId']"
              }
            },
            "method": "post",
            "path": "/Mail"
          },
          "runAfter": {}
        }
      }
    },
    "expression": {
      "or": [ 
         {
            "equals": [ 
              "@result('Scope')[0]['status']", 
              "Failed"
            ]
         },
         {
            "equals": [
               "@result('Scope')[0]['status']", 
               "Aborted"
            ]
         } 
      ]
    },
    "runAfter": {
      "Scope": [
        "Failed",
        "Skipped",
        "Succeeded",
        "TimedOut"
      ]
    }
  },
  "Scope": {
    "type": "Scope",
    "actions": {
      "Get_route": {
        "type": "ApiConnection",
        "inputs": {
          "host": {
            "connection": {
              "name": "@parameters('$connections')['bingmaps']['connectionId']"
            }
          },
          "method": "get",
          "path": "/REST/V1/Routes/Driving",
          "queries": {
            "distanceUnit": "Mile",
            "optimize": "timeWithTraffic",
            "travelMode": "Driving",
            "wp.0": "<start>",
            "wp.1": "<end>"
          }
        },
        "runAfter": {}
      },
      "If_traffic_time_is_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'],60)} minutes",
                 "Subject": "Time to leave: Traffic more than 10 minutes",
                 "To": "<your-email@domain.com>"
              },
              "host": {
                "connection": {
                   "name": "@parameters('$connections')['outlook']['connectionId']"
                }
              },
              "method": "post",
              "path": "/Mail"
            },
            "runAfter": {}
          }
        },
        "expression": {
          "and" : [
            {
               "greater": [ 
                  "@body('Get_route')?['travelDurationTraffic']", 
                  600
               ]
            }
          ]
        },
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Få support

* För frågor, besök [sidan Microsoft Q&en fråga för Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Om du vill skicka in eller rösta på funktioner och förslag går du till [webbplatsen för Azure Logic Apps feedback från användare](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Kör steg baserat på ett villkor (villkorliga uttryck)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Kör steg baserade på olika värden (Switch-instruktioner)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Köra och upprepa steg (slingor)](../logic-apps/logic-apps-control-flow-loops.md)
* [Köra eller sammanfoga parallella steg (grenar)](../logic-apps/logic-apps-control-flow-branches.md)