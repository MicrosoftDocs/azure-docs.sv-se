---
title: Integrera och hantera säkerhets åtgärder & Microsoft Graph säkerhet
description: Förbättra appens hot skydd, identifiering och svar med Microsoft Graph Security & Azure Logic Apps
services: logic-apps
ms.suite: integration
author: ecfan
ms.author: preetikr
ms.reviewer: v-ching, estfan, logicappspm
ms.topic: article
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: a83cd68df2f1d722517d6239bf6959075860d0b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94888546"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Förbättra hot skyddet genom att integrera säkerhets åtgärder med Microsoft Graph säkerhets & Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Microsoft Graph Security](/graph/security-concept-overview) Connector kan du förbättra hur appen identifierar, skyddar och svarar på hot genom att skapa automatiserade arbets flöden för att integrera Microsofts säkerhets produkter, tjänster och partners. Du kan till exempel skapa [Azure Security Center spel böcker](../security-center/workflow-automation.md) som övervakar och hanterar Microsoft Graph säkerhetsentiteter, till exempel aviseringar. Här följer några scenarier som stöds av Microsoft Graph Security Connector:

* Hämta aviseringar baserat på frågor eller aviserings-ID. Du kan till exempel hämta en lista som innehåller aviseringar med hög allvarlighets grad.

* Uppdatera aviseringar. Du kan till exempel uppdatera aviserings tilldelningar, lägga till kommentarer till aviseringar eller tagga aviseringar.

* Övervaka när aviseringar skapas eller ändras genom att skapa [aviserings prenumerationer (Webhooks)](/graph/api/resources/webhooks).

* Hantera dina aviserings prenumerationer. Du kan till exempel få aktiva prenumerationer, förlänga förfallo tiden för en prenumeration eller ta bort prenumerationer.

Din Logic Apps-arbetsflöde kan använda åtgärder som får svar från Microsoft Graph Security Connector och göra utdata tillgängliga för andra åtgärder i ditt arbets flöde. Du kan också använda andra åtgärder i arbets flödet för att använda utdata från åtgärder för Microsoft Graph säkerhets anslutning. Om du till exempel får aviseringar med hög allvarlighets grad via Microsoft Graph Security Connector kan du skicka aviseringarna i ett e-postmeddelande med hjälp av Outlook-anslutningen. 

Mer information om Microsoft Graph säkerhet finns i [Översikt över Microsoft Graph Security API](/graph/security-concept-overview). Om du inte har arbetat med Logic Apps läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Om du letar efter Power-eller PowerApps kan du läsa [Vad är energi automatisering?](https://flow.microsoft.com/) eller [Vad är Power Apps?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Om du vill använda anslutningsappen för Microsoft Graph-säkerhet måste du ha *uttryckligt medgivande* från innehavaradministratören för Azure Active Directory (AD), vilket är ett av [autentiseringskraven för Microsoft Graph-säkerhet](/graph/security-authorization). Detta medgivande kräver Microsoft Graph Security Connectors program-ID och namn, som du även hittar i [Azure Portal](https://portal.azure.com):

  | Egenskap | Värde |
  |----------|-------|
  | **Program namn** | `MicrosoftGraphSecurityConnector` |
  | **Program-ID** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
  |||

  För att bevilja medgivande för anslutningen kan din Azure AD-innehavaradministratör följa de här stegen:

  * [Ge innehavaradministratörens medgivande för Azure AD-program](../active-directory/develop/v2-permissions-and-consent.md).

  * Under den första körningen av din logikapp kan appen begära godkännande från Azure AD-innehavaradministratören via [funktionen för programmedgivande](../active-directory/develop/application-consent-experience.md).
   
* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic app där du vill få åtkomst till dina Microsoft Graph säkerhetsentiteter, till exempel aviseringar. Om du vill använda en Microsoft Graph säkerhets utlösare behöver du en tom Logic-app. Om du vill använda en Microsoft Graph säkerhets åtgärd behöver du en logisk app som börjar med lämplig utlösare för ditt scenario.

## <a name="connect-to-microsoft-graph-security"></a>Anslut till Microsoft Graph säkerhet 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com/)och öppna din Logic app i Logic App Designer, om du inte redan har gjort det.

1. För tomma Logi Kap par lägger du till utlösaren och andra åtgärder som du vill ha innan du lägger till en Microsoft Graph säkerhets åtgärd.

   \- eller -

   För befintliga Logi Kap par, under det sista steget där du vill lägga till en Microsoft Graph säkerhets åtgärd väljer du **nytt steg**.

   \- eller -

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Välj plus tecknet (+) som visas och välj **Lägg till en åtgärd**.

1. Skriv "Microsoft Graph Security" som filter i rutan Sök. Välj den åtgärd du vill använda i listan åtgärder.

1. Logga in med dina Microsoft Graph säkerhets uppgifter.

1. Ange nödvändig information för den valda åtgärden och fortsätt att skapa din Logic Apps-arbetsflöde.

## <a name="add-triggers"></a>Lägg till utlösare

I Azure Logic Apps måste varje Logi Kap par starta med en [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts)som utlöses när en enskild händelse inträffar eller när ett särskilt villkor uppfylls. Varje gång som utlösaren utlöses skapar Logic Apps-motorn en Logic App-instans och börjar köra appens arbets flöde.

> [!NOTE] 
> När en utlösare utlöses bearbetar utlösaren alla nya aviseringar. Om inga aviseringar tas emot hoppas utlösarens körning över. Nästa Utlös ande sökning sker baserat på upprepnings intervallet som du anger i utlösarens egenskaper.

Det här exemplet visar hur du kan starta ett Logic app-arbetsflöde när nya aviseringar skickas till din app.

1.  I Azure Portal eller Visual Studio skapar du en tom Logic-app som öppnar Logic Apps designer. I det här exemplet används Azure Portal.

1.  Skriv "Microsoft Graph Security" som filter i rutan Sök i designern. Välj den här utlösaren i listan utlösare: **på alla nya aviseringar**

1.  I utlösaren anger du information om de aviseringar som du vill övervaka. Om du vill ha fler egenskaper öppnar du listan **Lägg till ny parameter** och väljer en parameter för att lägga till egenskapen i utlösaren.

   | Egenskap | Egenskap (JSON) | Krävs | Typ | Beskrivning |
   |----------|-----------------|----------|------|-------------|
   | **Intervall** | `interval` | Ja | Integer | Ett positivt heltal som beskriver hur ofta arbets flödet körs baserat på frekvensen. Här följer de lägsta och högsta intervallen: <p><p>– Månad: 1-16 månader <br>– Dag: 1-500 dagar <br>– Timme: 1 – 12000 timmar <br>-Minute: 1 – 72000 minuter <br>-Sekund: 1 – 9999999 sekunder <p>Om intervallet till exempel är 6 och frekvensen är "månad", är upprepningen var 6: a månad. |
   | **Frekvens** | `frequency` | Ja | Sträng | Tidsenhet för upprepning: **sekund**, **minut**, **timme**, **dag**, **vecka** eller **månad** |
   | **Tidszon** | `timeZone` | Inga | Sträng | Gäller endast när du anger en start tid eftersom den här utlösaren inte accepterar [UTC-förskjutning](https://en.wikipedia.org/wiki/UTC_offset). Välj den tidszon som du vill använda. |
   | **Start tid** | `startTime` | Inga | Sträng | Ange start datum och-tid i följande format: <p><p>ÅÅÅÅ-MM-DDThh: mm: SS om du väljer en tidszon <p>\- eller - <p>ÅÅÅÅ-MM-DDThh: mm: ssZ om du inte väljer en tidszon <p>Om du till exempel vill att 18 september 2017 på 2:00 PM anger du "2017-09-18T14:00:00" och väljer en tidszon som Pacific, normal tid. Eller ange "2017-09-18T14:00:00Z" utan en tidszon. <p>**Obs:** Den här start tiden har högst 49 år i framtiden och måste följa [8601 ISO-tiden för datum/tid](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) i [UTC-datum format](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), men utan en [UTC-förskjutning](https://en.wikipedia.org/wiki/UTC_offset). Om du inte väljer en tidszon måste du lägga till bokstaven "Z" i slutet utan blank steg. Detta "Z" avser motsvarande [nautiska tid](https://en.wikipedia.org/wiki/Nautical_time). <p>För enkla scheman är start tiden den första förekomsten, medan utlösaren i komplexa scheman inte utlöses tidigare än start tiden. [*Hur kan jag använda start datum och-tid?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   ||||||

1.  När du är klar väljer du **Spara** i verktygsfältet designer.

1.  Fortsätt nu att lägga till en eller flera åtgärder i din Logic app för de uppgifter som du vill utföra med utlösnings resultaten.

## <a name="add-actions"></a>Lägg till åtgärder

Här är mer information om hur du använder de olika åtgärderna som finns tillgängliga med Microsoft Graph Security Connector.

### <a name="manage-alerts"></a>Hantera aviseringar

Om du vill filtrera, sortera eller få de senaste resultaten anger du *bara* [OData-frågeparametrar som stöds av Microsoft Graph](/graph/query-parameters). *Ange inte* den fullständiga bas-URL: en eller http-åtgärden, till exempel eller `https://graph.microsoft.com/v1.0/security/alerts` `GET` `PATCH` åtgärden eller. Här är ett speciellt exempel som visar parametrar för en åtgärd för att **Hämta aviseringar** när du vill ha en lista med aviseringar med hög allvarlighets grad:

`Filter alerts value as Severity eq 'high'`

Mer information om frågor som du kan använda med den här anslutnings tjänsten finns i [referens dokumentationen för Microsoft Graph säkerhets aviseringar](/graph/api/alert-list). Om du vill bygga förbättrade upplevelser med den här anslutningen kan du läsa mer om de [schema egenskaper](/graph/api/resources/alert) som stöds av anslutnings programmet.

| Åtgärd | Beskrivning |
|--------|-------------|
| **Hämta aviseringar** | Hämta aviseringar som filtrerats baserat på en eller flera [aviserings egenskaper](/graph/api/resources/alert), till exempel `Provider eq 'Azure Security Center' or 'Palo Alto Networks'` . | 
| **Hämta avisering efter ID** | Få en speciell avisering baserat på aviserings-ID: t. | 
| **Uppdatera avisering** | Uppdatera en speciell avisering baserat på aviserings-ID: t. För att se till att du överför de nödvändiga och redigerbara egenskaperna i din begäran, se de [redigerbara egenskaperna för aviseringar](/graph/api/alert-update). Om du till exempel vill tilldela en avisering till en säkerhetsanalytiker så att de kan undersöka, kan du uppdatera aviseringens **tilldelade** egenskap. |
|||

### <a name="manage-alert-subscriptions"></a>Hantera aviserings prenumerationer

Microsoft Graph stöder [*prenumerationer*](/graph/api/resources/subscription)eller [*Webhooks*](/graph/api/resources/webhooks). Om du vill hämta, uppdatera eller ta bort prenumerationer anger du [OData-frågeparametrar som stöds av Microsoft Graph](/graph/query-parameters) till entiteten Microsoft Graph entitets konstruktion och inkluderar `security/alerts` följt av OData-frågan. *Ta inte med* bas-URL: en, till exempel `https://graph.microsoft.com/v1.0` . Använd i stället formatet i det här exemplet:

`security/alerts?$filter=status eq 'NewAlert'`

| Åtgärd | Beskrivning |
|--------|-------------|
| **Skapa prenumerationer** | [Skapa en prenumeration](/graph/api/subscription-post-subscriptions) som meddelar dig om eventuella ändringar. Du kan filtrera den här prenumerationen för de olika aviserings typer som du vill använda. Du kan till exempel skapa en prenumeration som meddelar dig om aviseringar med hög allvarlighets grad. |
| **Hämta aktiva prenumerationer** | [Hämta prenumerationer som inte har gått ut](/graph/api/subscription-list). | 
| **Uppdatera prenumeration** | [Uppdatera en prenumeration](/graph/api/subscription-update) genom att ange prenumerations-ID: t. Om du till exempel vill utöka din prenumeration kan du uppdatera prenumerationens `expirationDateTime` egenskap. | 
| **Ta bort prenumeration** | [Ta bort en prenumeration](/graph/api/subscription-delete) genom att ange prenumerations-ID: t. | 
||| 

### <a name="manage-threat-intelligence-indicators"></a>Hantera hot informations indikatorer

Om du vill filtrera, sortera eller få de senaste resultaten anger du *bara* [OData-frågeparametrar som stöds av Microsoft Graph](/graph/query-parameters). *Ange inte* den fullständiga bas-URL: en eller http-åtgärden, till exempel eller `https://graph.microsoft.com/beta/security/tiIndicators` `GET` `PATCH` åtgärden eller. Här är ett speciellt exempel som visar parametrarna för en **Get tiIndicators** -åtgärd när du vill ha en lista med `DDoS` typen hot:

`Filter threat intelligence indicator value as threatType eq 'DDoS'`

Mer information om de frågor som du kan använda med den här anslutnings tjänsten finns [i "valfria frågeparametrar" i referens dokumentationen för Microsoft Graph Security Threat Intelligence-indikator](/graph/api/tiindicators-list). Om du vill bygga förbättrade upplevelser med den här anslutningen kan du läsa mer om den [schema egenskaper Hot information-indikator](/graph/api/resources/tiindicator) som stöds av Connector.

| Åtgärd | Beskrivning |
|--------|-------------|
| **Hämta hot informations indikatorer** | Hämta tiIndicators filtreras baserat på en eller flera [tiIndicator-egenskaper](/graph/api/resources/tiindicator), till exempel `threatType eq 'MaliciousUrl' or 'DDoS'` |
| **Hämta Hot information-indikator efter ID** | Hämta en speciell tiIndicator baserat på tiIndicator-ID: t. | 
| **Skapa hot informations indikator** | Skapa en ny tiIndicator genom att publicera i tiIndicators-samlingen. För att se till att du skickar de nödvändiga egenskaperna i din begäran, se de [egenskaper som krävs för att skapa tiIndicator](/graph/api/tiindicators-post). |
| **Skicka flera hot informations indikatorer** | Skapa flera nya tiIndicators genom att publicera en tiIndicators-samling. För att se till att du skickar de nödvändiga egenskaperna i din begäran, se de [egenskaper som krävs för att skicka flera tiIndicators](/graph/api/tiindicator-submittiindicators). |
| **Uppdatera hot informations indikator** | Uppdatera en speciell tiIndicator baserat på tiIndicator-ID: t. För att se till att du överför de nödvändiga och redigerbara egenskaperna i din begäran, se de [redigerbara egenskaperna för tiIndicator](/graph/api/tiindicator-update). Om du till exempel vill uppdatera åtgärden som ska tillämpas om indikatorn matchas från säkerhets verktyget targetProduct, kan du uppdatera tiIndicator- **åtgärdens** egenskap. |
| **Uppdatera flera hot informations indikatorer** | Uppdatera flera tiIndicators. För att se till att du överför de nödvändiga egenskaperna i din begäran, se de [obligatoriska egenskaperna för att uppdatera flera tiIndicators](/graph/api/tiindicator-updatetiindicators). |
| **Ta bort hot informations indikator efter ID** | Ta bort en speciell tiIndicator baserat på tiIndicator-ID: t. |
| **Ta bort flera hot informations indikatorer utifrån ID: n** | Ta bort flera tiIndicators med deras ID. För att se till att du skickar de nödvändiga egenskaperna i din begäran, se de [obligatoriska egenskaperna för att ta bort flera tiIndicators med ID: n](/graph/api/tiindicator-deletetiindicators). |
| **Ta bort flera hot informations indikatorer med externa ID: n** | Ta bort flera tiIndicators med de externa ID: na. För att se till att du skickar de nödvändiga egenskaperna i din begäran, se de [obligatoriska egenskaperna för att ta bort flera tiIndicators med externa ID: n](/graph/api/tiindicator-deletetiindicatorsbyexternalid). |
|||

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och gränser, som beskrivs av kopplingens OpenAPI (tidigare Swagger) Beskrivning, finns i kopplingens [referens sida](/connectors/microsoftgraphsecurity/).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
