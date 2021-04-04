---
title: 'API: er för avläsning av tjänst – Microsoft Commercial Marketplace'
description: Med API för användnings händelser kan du generera användnings händelser för SaaS-erbjudanden i Microsoft AppSource och Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/26/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: b75964f8cfc41efc35858284dbffded3aa406eb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96436070"
---
# <a name="marketplace-metered-billing-apis"></a>API: er för avläsning av Marketplace

De avgiftsbelagda API: erna för fakturering ska användas när en utgivare skapar anpassade mätnings dimensioner för ett erbjudande som ska publiceras i Partner Center. Integrering med API: er för avgiftsbelagda fakturering krävs för alla inköpta erbjudanden som har en eller flera planer med anpassade dimensioner för att generera användnings händelser.

Mer information om hur du skapar anpassade mätnings dimensioner för SaaS finns i [SaaS-mätare fakturering](saas-metered-billing.md).

Mer information om hur du skapar anpassade mätnings dimensioner för ett Azure Application erbjudande med en hanterad app-plan finns i [Konfigurera installations information för Azure-programerbjudande](../create-new-azure-apps-offer.md#configure-your-azure-application-offer-setup-details).

## <a name="enforcing-tls-12-note"></a>Framtvingar TLS 1,2-anteckning

TLS version 1,2-versionen tillämpas som den lägsta versionen för HTTPS-kommunikation. Se till att du använder den här TLS-versionen i din kod. TLS version 1,0 och 1,1 är föråldrade och anslutnings försök kommer att nekas.

## <a name="metered-billing-single-usage-event"></a>Händelse för avläsning av fakturerings bara användning

API för användnings händelser ska anropas av utgivaren för att generera användnings händelser mot en aktiv resurs (prenumererar på) för den plan som köpts av den specifika kunden. Användnings händelsen genereras separat för varje anpassad dimension i den plan som definieras av utgivaren när erbjudandet publiceras.

Det går bara att generera en användnings händelse för varje timme i en kalender dag. Till exempel, med 8:05:15 idag, kan du generera en användnings händelse. Om den här händelsen godkänns kommer nästa användnings händelse att accepteras från 9:00 am idag. Om du skickar ytterligare en händelse mellan 8:15 och 8:59:59 idag avvisas den som en dubblett. Du bör samla alla enheter som förbrukas i en timme och sedan generera dem i en enda händelse.

Det går bara att generera en användnings händelse för varje timme i en kalender dag per resurs. Om fler än en enhet används i en timme ackumulerar du alla enheter som förbrukas i timmen och genererar dem sedan i en enda händelse. Användnings händelser kan bara genereras under de senaste 24 timmarna. Om du genererar en användnings händelse när som helst mellan 8:00 och 8:59:59 (och godkänns) och skickar ytterligare en händelse för samma dag mellan 8:00 och 8:59:59, avvisas den som en dubblett.

**Publicera**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Frågeparametrar:*

| Parameter | Rekommendation          |
| ---------- | ---------------------- |
| `ApiVersion` | Använd 2018-08-31. |
| | |

*Begärandehuvuden:*

| Innehålls typ       | Använda `application/json`  |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Unikt sträng värde för spårning av begäran från klienten, helst en GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
| `x-ms-correlationid` | Unikt sträng värde för åtgärden på klienten. Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan. Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
| `authorization`   | En unik åtkomsttoken som identifierar den ISV som gör detta API-anrop. Formatet är `"Bearer <access_token>"` när token-värdet hämtas av utgivaren enligt beskrivningen för <br> <ul> <li> SaaS i [Hämta token med ett HTTP-inlägg](./pc-saas-registration.md#get-the-token-with-an-http-post). </li> <li> Hanterat program i [strategier för autentisering](./marketplace-metering-service-authentication.md). </li> </ul> |
| | |

*Exempel på begär ande text:*

```json
{
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. 
  "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, from now and until 24 hours back
  "planId": "plan1", // id of the plan purchased for the offer
}
```

>[!NOTE]
>`resourceId` har en annan betydelse för SaaS-appen och för en hanterad app som avger anpassad mätare. 

För Azure Application hanterade appar planeraren `resourceId` är den hanterade appen `resource group Id` . Ett exempel skript för att hämta det finns i [använda Azure-Managed Identities-token](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token). 

För SaaS `resourceId` -erbjudanden är prenumerations-ID: t för SaaS. Mer information om SaaS-prenumerationer finns i [lista prenumerationer](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions).

### <a name="responses"></a>Svar

Kod: 200<br>
OK. Användnings utsläppen godtogs och registrerades på Microsoft för ytterligare bearbetning och fakturering.

Exempel på svars nytto last: 

```json
{
  "usageEventId": <guid>, // unique identifier associated with the usage event in Microsoft records
  "status": "Accepted" // this is the only value in case of single usage event
  "messageTime": "2020-01-12T13:19:35.3458658Z", // time in UTC this event was accepted
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. For SaaS it's the subscriptionId.
  "quantity": 5.0, // amount of emitted units as recorded by Microsoft
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, as sent by the ISV
  "planId": "plan1", // id of the plan purchased for the offer
}
```

Kod: 400 <br>
Felaktig begäran.

* Begär ande data som saknas eller är ogiltiga.
* `effectiveStartTime` är mer än 24 timmar tidigare. Händelsen har upphört att gälla.
* SaaS-prenumerationen har inte prenumerations status.

Exempel på svars nytto last: 

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "The resourceId is required.",
      "target": "ResourceId",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```

Kod: 403<br>

Ej tillåtet. Autentiseringstoken har inte angetts, är ogiltig eller har upphört att gälla.  Eller så försöker begäran att komma åt en prenumeration för ett erbjudande som har publicerats med ett annat Azure AD App-ID från den som användes för att skapa autentiseringstoken.

Kod: 409<br>
Uppstod. En användnings händelse har redan rapporter ATS för angivet resurs-ID, effektivt användnings datum och timma.

Exempel på svars nytto last: 

```json
{
  "additionalInfo": {
    "acceptedMessage": {
      "usageEventId": "<guid>", //unique identifier associated with the usage event in Microsoft records
      "status": "Duplicate",
      "messageTime": "2020-01-12T13:19:35.3458658Z",
      "resourceId": "<guid>", //unique identifier of the resource against which usage is emitted.
      "quantity": 1.0,
      "dimension": "dim1",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "plan1"
    }
  },
  "message": "This usage event already exist.",
  "code": "Conflict"
}
```

## <a name="metered-billing-batch-usage-event"></a>Användnings händelse för avgiftsbelagd fakturering

Med event API för batch-användning kan du generera användnings händelser för fler än en inköpt resurs samtidigt. Du kan också generera flera användnings händelser för samma resurs så länge de är för olika kalender timmar. Det maximala antalet händelser i en enskild batch är 25.

**Publicera:**`https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Frågeparametrar:*

| Parameter  | Rekommendation     |
| ---------- | -------------------- |
| `ApiVersion` | Använd 2018-08-31. |

*Begärandehuvuden:*

| Innehålls typ       | Använda `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Unikt sträng värde för spårning av begäran från klienten, helst en GUID. Om det här värdet inte anges genereras ett och anges i svarshuvuden. |
| `x-ms-correlationid` | Unikt sträng värde för åtgärden på klienten. Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan. Om det här värdet inte anges genereras ett och anges i svarshuvuden. |
| `authorization`      | En unik åtkomsttoken som identifierar den ISV som gör detta API-anrop. Formatet är `Bearer <access_token>` när token-värdet hämtas av utgivaren enligt beskrivningen för <br> <ul> <li> SaaS i [Hämta token med ett HTTP-inlägg](./pc-saas-registration.md#get-the-token-with-an-http-post). </li> <li> Hanterat program i [strategier för autentisering](./marketplace-metering-service-authentication.md). </li> </ul> |
| | |


*Exempel på begär ande text:*

```json
{
  "request": [ // list of usage events for the same or different resources of the publisher
    { // first event
      "resourceId": "<guid1>", // Unique identifier of the resource against which usage is emitted. 
      "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
      "dimension": "dim1", //Custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",//Time in UTC when the usage event occurred, from now and until 24 hours back
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // next event
      "resourceId": "<guid2>", 
      "quantity": 39.0, 
      "dimension": "email", 
      "effectiveStartTime": "2018-11-01T23:33:10
      "planId": "gold", // id of the plan purchased for the offer
    }
  ]
}
```

>[!NOTE]
>`resourceId` har en annan betydelse för SaaS-appen och för en hanterad app som avger anpassad mätare. 

För Azure Application hanterade appar planeraren `resourceId` är den hanterade appen `resource group Id` . Ett exempel skript för att hämta det finns i [använda Azure-Managed Identities-token](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token). 

För SaaS `resourceId` -erbjudanden är prenumerations-ID: t för SaaS. Mer information om SaaS-prenumerationer finns i [lista prenumerationer](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions).

### <a name="responses"></a>Svar

Kod: 200<br>
OK. Användningen av batch-användningen godtogs och registrerades på Microsoft för ytterligare bearbetning och fakturering. Svars listan returneras med status för varje enskild händelse i batchen. Du bör iterera igenom svarets nytto last för att förstå Svaren för varje enskild användnings händelse som skickas som en del av batch-händelsen.

Exempel på svars nytto last: 

```json
{
  "count": 2, // number of records in the response
  "result": [
    { // first response
      "usageEventId": "<guid>", // unique identifier associated with the usage event in Microsoft records
      "status": "Accepted" // see list of possible statuses below,
      "messageTime": "2020-01-12T13:19:35.3458658Z", // Time in UTC this event was accepted by Microsoft,
      "resourceId": "<guid1>", // unique identifier of the resource against which usage is emitted.
      "quantity": 5.0, // amount of emitted units as recorded by Microsoft 
      "dimension": "dim1", // custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",// time in UTC when the usage event occurred, as sent by the ISV
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // second response
      "status": "Duplicate",
      "messageTime": "0001-01-01T00:00:00",
      "error": {
        "additionalInfo": {
          "acceptedMessage": {
            "usageEventId": "<guid>",
            "status": "Duplicate",
            "messageTime": "2020-01-12T13:19:35.3458658Z",
            "resourceId": "<guid2>",
            "quantity": 1.0,
            "dimension": "email",
            "effectiveStartTime": "2020-01-12T11:03:28.14Z",
            "planId": "gold"
          }
        },
        "message": "This usage event already exist.",
        "code": "Conflict"
      },
      "resourceId": "<guid2>",
      "quantity": 1.0,
      "dimension": "email",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "gold"
    }
  ]
}
```

Beskrivning av status kod som refereras i `BatchUsageEvent` API-svar:

| Statuskod  | Description |
| ---------- | -------------------- |
| `Accepted` | Accept. |
| `Expired` | Användning har gått ut. |
| `Duplicate` | Duplicerad användning har angetts. |
| `Error` | Felkod. |
| `ResourceNotFound` | Den tillhandahållna användnings resursen är ogiltig. |
| `ResourceNotAuthorized` | Du har inte behörighet att tillhandahålla användning för den här resursen. |
| `InvalidDimension` | Dimensionen för vilken användningen skickas är ogiltig för det här erbjudandet/den här prenumerationen. |
| `InvalidQuantity` | Den överförda kvantiteten är lägre eller lika med 0. |
| `BadArgument` | Inmatad information saknas eller är felaktig. |

Kod: 400<br>
Felaktig begäran. Batchen innehöll fler än 25 användnings händelser.

Kod: 403<br>
Ej tillåtet. Autentiseringstoken har inte angetts, är ogiltig eller har upphört att gälla.  Eller så försöker begäran att komma åt en prenumeration för ett erbjudande som har publicerats med ett annat Azure AD App-ID från den som användes för att skapa autentiseringstoken.

## <a name="development-and-testing-best-practices"></a>Bästa metoder för utveckling och testning

Om du vill testa det anpassade mätar utsläppet implementerar du integrering med API för avläsning och skapar en plan för det publicerade SaaS-erbjudandet med anpassade dimensioner som definieras i den med noll pris per enhet. Och publicera det här erbjudandet som för hands version så att endast begränsade användare kan komma åt och testa integreringen.

Du kan också använda privat plan för ett befintligt Live-erbjudande för att begränsa åtkomsten till den här planen under testning till begränsad mål grupp.

## <a name="get-support"></a>Få support

Följ anvisningarna [för programmet för kommersiella marknads platser i Partner Center](../support.md) för att förstå support alternativen för Publisher och öppna ett support ärende med Microsoft.

## <a name="next-steps"></a>Nästa steg

Mer information om API: er för mätnings tjänsten finns i [vanliga frågor och svar om API för avläsning av service](./marketplace-metering-service-apis-faq.md).
