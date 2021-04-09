---
title: 'API: er för SaaS-utförande v2 i Microsofts kommersiella marknads platser'
description: 'Lär dig hur du skapar och hanterar ett SaaS-erbjudande på Microsoft AppSource och Azure Marketplace genom att använda API: erna version 2.'
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 2acf5178e7d1cfdf907146d733150a48e9696a5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101712359"
---
# <a name="saas-fulfillment-apis-version-2-in-the-commercial-marketplace"></a>SaaS-API version 2 på den kommersiella marknaden

Den här artikeln beskriver de API: er som gör det möjligt för partner att sälja sina SaaS-erbjudanden (Software as a Service) i Microsoft AppSource och Azure Marketplace. En utgivare krävs för att implementera integrering med dessa API: er för att publicera ett SaaS-erbjudande som går att använda i Partner Center.

## <a name="managing-the-saas-subscription-life-cycle"></a>Hantera livs cykeln för SaaS-prenumeration

Den kommersiella Marketplace hanterar hela livs cykeln för en SaaS-prenumeration efter köpet av slutanvändaren. Den använder landnings sidan, API: er för utförande, åtgärds-API: er och webhook som en mekanism för att driva den faktiska aktiveringen av SaaS-prenumeration, användning, uppdateringar och uppsägning. Slutanvändarens faktura baseras på statusen för den SaaS-prenumeration som Microsoft underhåller. 

### <a name="states-of-a-saas-subscription"></a>Tillstånd för en SaaS-prenumeration

Följande diagram visar tillstånden för en SaaS-prenumeration och lämpliga åtgärder.

![Diagram som visar livs cykeln för en program vara som en tjänst prenumeration i Marketplace.](./media/saas-subscription-lifecycle-api-v2.png)

#### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>Köpt men ännu inte aktive rad (*PendingFulfillmentStart*)

När en slutanvändare (eller CSP) köper ett SaaS-erbjudande på den kommersiella marknaden bör utgivaren meddelas om köpet. Utgivaren kan sedan skapa och konfigurera ett nytt SaaS-konto på utgivarens sida för slutanvändaren.

För att skapa konton:

1. Kunden väljer den **konfigurations** knapp som är tillgänglig för ett SaaS-erbjudande efter det att köpet i Microsoft AppSource eller Azure Portal har genomförts. Alternativt kan kunden använda knappen **Konfigurera** i e-postmeddelandet som de kommer att få strax efter köpet.
2. Microsoft meddelar sedan partnern om köpet genom att öppna den nya webb sidan URL: en med parametern token (inköps-ID-token från den kommersiella Marketplace).

Ett exempel på ett sådant anrop är `https://contoso.com/signup?token=<blob>` att URL: en för landnings sidan för det här SaaS-erbjudandet i Partner Center har kon figurer ATS som `https://contoso.com/signup` . Denna token tillhandahåller utgivaren ett ID som unikt identifierar SaaS inköp och kunden.

>[!NOTE]
>Utgivaren meddelas inte om SaaS-köpet förrän kunden initierar konfigurations processen från Microsoft-sidan.

Landnings sidans URL måste vara igång hela dagen, varje dag och redo att ta emot nya samtal från Microsoft hela tiden. Om landnings sidan blir otillgänglig kan kunderna inte registrera sig för SaaS-tjänsten och börja använda den.

Därefter måste utgivaren skicka tillbaka *token* till Microsoft genom att anropa [SaaS lösnings-API](#resolve-a-purchased-subscription)och ange token som värde för `x-ms-marketplace-token header` parametern header. Till följd av anropet till API-anropet, utbyts token för information om SaaS-inköpet, till exempel unikt ID för köpet, inköpt erbjudande-ID och inköps plan-ID.

På landnings sidan ska kunden vara inloggad på det nya eller befintliga SaaS-kontot via Azure Active Directory (enkel inloggning) i Azure AD.

Utgivaren bör implementera SSO för att tillhandahålla den användar upplevelse som krävs av Microsoft för det här flödet. Se till att använda Azure AD-programmet med flera innehavare och Tillåt både arbets-och skol konton eller personliga Microsoft-konton vid konfigurering av SSO. Detta krav gäller endast landnings sidan för användare som omdirigeras till SaaS-tjänsten när de redan har loggat in med Microsoft-autentiseringsuppgifter. SSO krävs inte för alla inloggningar till SaaS-tjänsten.

> [!NOTE]
>Om SSO kräver att en administratör måste bevilja behörighet till en app, måste beskrivningen av erbjudandet i Partner Center avslöja att åtkomst på administratörs nivå krävs. Den här informationen är att följa de [kommersiella certifierings principerna för marknads platsen](/legal/marketplace/certification-policies#10003-authentication-options).

Efter inloggningen bör kunden fylla i SaaS-konfigurationen på utgivarens sida. Sedan måste utgivaren anropa [API: et för att aktivera prenumerationen](#activate-a-subscription) för att skicka en signal till Azure Marketplace som etableringen av SaaS-kontot har slutförts.
Den här åtgärden startar kundens fakturerings period. Om API-anropet för aktivering av prenumeration inte lyckas faktureras kunden inte för köpet.


![Diagram som visar ett P I-anrop för ett etablerings scenario.](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="active-subscribed"></a>Aktiv (*prenumererad*)

*Active (prenumererat)* är det stabila läget för en etablerad SaaS-prenumeration. När Microsoft-sidan har bearbetat det [aktiva prenumerations-API](#activate-a-subscription) -anropet markeras SaaS-prenumerationen som *prenumererad*. Kunden kan nu använda SaaS-tjänsten på utgivarens sida och kommer att faktureras.

När en SaaS-prenumeration redan är aktiv kan kunden välja **Hantera SaaS-upplevelse** från Azure Portal eller Microsoft 365 administrations Center. Den här åtgärden gör också att Microsoft anropar **URL: en för landnings sidan** med parametern *token* , som inträffar i aktiverings flödet. Utgivaren bör skilja mellan nya köp och hantering av befintliga SaaS-konton och hantera URL-anropet för landnings sidan.

#### <a name="being-updated-subscribed"></a>Uppdateras (*prenumererar*)

Den här åtgärden innebär att en uppdatering av en befintlig aktiv SaaS-prenumeration bearbetas av både Microsoft och utgivaren. En sådan uppdatering kan initieras av:

- Kunden från den kommersiella marknads platsen.
- CSP: n från den kommersiella marknads platsen.
- Kunden från utgivarens SaaS-webbplats (men inte för CSP-gjort inköp).

Det finns två typer av uppdateringar för en SaaS-prenumeration:

- Uppdatera plan när kunden väljer ett annat abonnemang för prenumerationen.
- Uppdatera kvantitet när kunden ändrar antalet köpta platser för prenumerationen.

Det går bara att uppdatera en aktiv prenumeration. När prenumerationen uppdateras är dess tillstånd fortfarande aktivt på Microsoft-sidan.

##### <a name="update-initiated-from-the-commercial-marketplace"></a>Uppdatering initierad från den kommersiella marknads platsen

I det här flödet ändrar kunden prenumerations planen eller antalet platser från Azure Portal eller Microsoft 365 administrations Center.

1. När en uppdatering har angetts anropar Microsoft utgivarens webhook-URL, som kon figurer ATS i fältet **anslutnings-webhook** i Partner Center, med ett lämpligt värde för *Action* och andra relevanta parametrar. 
1. Utgivarens sida bör göra nödvändiga ändringar i SaaS-tjänsten och meddela Microsoft när du är färdig genom att anropa [uppdaterings status för åtgärds-API](#update-the-status-of-an-operation).
1. Om korrigeringen skickas med statusen *misslyckad* slutförs inte uppdaterings processen på Microsoft-sidan. SaaS-prenumerationen behåller den befintliga planen och antalet platser.

> [!NOTE]
> Utgivaren ska starta KORRIGERINGen för att [Uppdatera status för åtgärds-API](#update-the-status-of-an-operation) med ett misslyckat/framgångs svar *inom en tids period på 10 sekunder* efter att ha tagit emot webhook-meddelandet. Om korrigering av åtgärds status inte tas emot inom 10 sekunder, korrigeras ändrings planen *automatiskt som lyckad*. 

Sekvensen med API-anrop för ett uppdaterings scenario som initieras från den kommersiella marknads platsen visas i följande diagram.

![Diagram som visar ett P I-anrop för en uppdatering av Marketplace som har startats.](./media/saas-update-status-api-v2-calls-marketplace-side.png)

##### <a name="update-initiated-from-the-publisher"></a>Uppdatering initierad från utgivaren

I det här flödet ändrar kunden prenumerations planen eller antalet platser som köpts från själva SaaS-tjänsten. 

1. Utgivar koden måste anropa [API för ändrings plan](#change-the-plan-on-the-subscription) och/eller [API för ändrings antal](#change-the-quantity-of-seats-on-the-saas-subscription) innan du gör den begärda ändringen på utgivarens sida. 

1. Microsoft kommer att tillämpa ändringen på prenumerationen och sedan meddela utgivaren via **anslutningens webhook** att tillämpa samma ändring.

1. Först om utgivaren gör den nödvändiga ändringen i SaaS-prenumerationen och meddela Microsoft när ändringen görs genom att anropa [uppdaterings status för åtgärds-API](#update-the-status-of-an-operation).

Sekvensen med API-anrop för ett uppdaterings scenario som initieras från utgivarens sida visas i följande diagram.

![Diagram som visar ett P I-anrop för en initierad uppdatering av en Publisher-sida.](./media/saas-update-status-api-v2-calls-publisher-side.png)

#### <a name="suspended-suspended"></a>Pausad (*inaktive* rad)

Det här läget anger att kundens betalning för SaaS-tjänsten inte har tagits emot. Utgivaren kommer att meddelas om den här ändringen av Microsoft i SaaS prenumerations status. Meddelandet görs via ett anrop till webhook med *Åtgärds* parametern inställd på *pausad*.

Utgivaren kan eventuellt göra ändringar i SaaS-tjänsten på utgivarens sida. Vi rekommenderar att utgivaren gör informationen tillgänglig för den inaktiverade kunden och begränsar eller blockerar kundens åtkomst till SaaS-tjänsten. Det finns en sannolikhet att betalningen aldrig tas emot.

Microsoft ger kunden en 30-dagars Grace-period innan prenumerationen avbryts automatiskt. När en prenumeration har tillståndet *Suspended* :

* Partnern eller ISV: en måste ha SaaS-kontot i ett återställnings Bart tillstånd, så att full funktionalitet kan återställas utan att data eller inställningar går förlorade.
* Partnern eller ISV: en begäran om att återställa prenumerationen, om betalningen tas emot under respitperioden, eller en begäran om att avetablera prenumerationen i slutet av respitperioden. Båda förfrågningarna skickas via webhook-mekanismen.

Prenumerations tillståndet ändras till inaktive rad på Microsoft-sidan innan utgivaren vidtar åtgärder. Det går bara att pausa aktiva prenumerationer.

#### <a name="reinstated-suspended"></a>Återupprättad (*inaktive* rad)

Den här åtgärden anger att kundens betalnings instrument har blivit giltigt igen, en betalning har gjorts för SaaS-prenumerationen och att prenumerationen återställs. Om så är fallet: 

1. Microsoft anropar webhook med en *Åtgärds* parameter inställd på värdet *Återställ* .
1. Utgivaren ser till att prenumerationen fungerar som den ska på utgivarens sida.
1. Utgivaren anropar [API: et för korrigerings åtgärden](#update-the-status-of-an-operation) med statusen lyckades.
1. Återställningen lyckas och Kunden debiteras igen för SaaS-prenumerationen. 

Om korrigeringen skickas med statusen *Miss* slutförs återställnings processen på Microsoft-sidan och prenumerationen förblir *inaktive* rad.

Endast en pausad prenumeration kan återställas. Den inaktiverade SaaS-prenumerationen förblir i ett *inaktiverat* tillstånd när den återställs. När den här åtgärden har slutförts *aktive* ras prenumerationens status.

#### <a name="renewed-subscribed"></a>Förnyad (*prenumererad*)

SaaS-prenumerationen förnyas automatiskt av Microsoft i slutet av prenumerations perioden på en månad eller ett år. Standardinställningen för inställningen automatisk förnyelse är *True* för alla SaaS-prenumerationer. Aktiva SaaS-prenumerationer fortsätter att förnyas med en vanlig takt. Microsoft meddelar inte utgivaren när en prenumeration förnyas. En kund kan inaktivera automatisk förnyelse för en SaaS-prenumeration via Microsoft 365 admin-portalen. I det här fallet avbryts SaaS-prenumerationen automatiskt när den aktuella fakturerings perioden är slut. Kunder kan också avbryta SaaS-prenumerationen när som helst.

Endast aktiva prenumerationer förnyas automatiskt. Prenumerationerna förblir aktiva under förnyelse processen och om automatisk förnyelse har slutförts. Efter förnyelsen uppdateras start-och slutdatumen för prenumerations perioden till den nya termen datum.

Om en automatisk förnyelse Miss lyckas på grund av ett problem med betalningen kommer prenumerationen att *inaktive* ras och utgivaren kommer att meddelas.

#### <a name="canceled-unsubscribed"></a>Avbruten (avbruten av *prenumeration*) 

Prenumerationerna når detta tillstånd som svar på en explicit kund-eller CSP-åtgärd genom att avbryta en prenumeration från utgivarens webbplats, Azure Portal eller Microsoft 365 administrations Center. En prenumeration kan också avbrytas implicit, som ett resultat av inbetalning av avgifter, efter att ha *avbrutit* tillståndet i 30 dagar.

När utgivaren får ett uppsägnings-webhook-anrop bör de behålla kund information för återställning på begäran under minst sju dagar. Sedan kan kunddata tas bort.

En SaaS-prenumeration kan avbrytas när som helst i dess livs cykel. När en prenumeration har avbrutits går det inte att återaktivera den.

## <a name="api-reference"></a>API-referens

I det här avsnittet dokumenteras API: er för SaaS-prenumeration och åtgärder.

**Prenumerations-API: er** ska användas för att hantera livs cykeln för SaaS-prenumeration från köpet till annullering.

**API: er för åtgärder** ska användas för att:

* Verifiera och bekräfta de bearbetade webhook-anropen.
* Hämta en lista över väntande program åtgärder som väntar på att bekräftas av utgivaren.

> [!NOTE]
> TLS version 1,2-versionen kommer att verkställas snart den lägsta versionen för HTTPS-kommunikation. Se till att du använder den här TLS-versionen i din kod. TLS-versionerna 1,0 och 1,1 kommer snart att bli föråldrade.

### <a name="subscription-apis"></a>Prenumerations-API: er

#### <a name="resolve-a-purchased-subscription"></a>Lösa en inköpt prenumeration

Med hjälp av matchnings slut punkten kan utgivaren byta ut identifierings-token för köpet från den kommersiella Marketplace (kallas *token* i [inköpt men inte aktiverat](#purchased-but-not-yet-activated-pendingfulfillmentstart)) till ett beständigt inköpt SaaS prenumerations-ID och dess information.

När en kund omdirigeras till partnerns URL för landnings sidan skickas kundens ID-token som *token* -parameter i detta URL-anrop. Partnern förväntas använda denna token och göra en begäran om att lösa den. Matchnings-API-svaret innehåller prenumerations-ID: t för SaaS och annan information som unikt identifierar köpet. Den *token* som tillhandahålls med landnings SIDANs URL-anrop är vanligt vis giltig i 24 timmar. Om den *token* du tar emot redan har gått ut rekommenderar vi att du ger följande vägledning för slutanvändaren:

"Vi kunde inte identifiera det här köpet. Öppna den här SaaS-prenumerationen igen i Azure Portal eller i Microsoft 365 administrations Center och välj "Konfigurera konto" eller "hantera konto" igen. "

Om du anropar lösnings-API: t returneras prenumerations information och status för SaaS-prenumerationer i alla status värden som stöds.

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Efter `https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Frågeparametrar:*

|  Parameter         | Värde            |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Använd 2018-08-31.   |

*Begärandehuvuden:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json` |
|  `x-ms-requestid`    |  Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
|  `x-ms-correlationid` |  Ett unikt sträng värde för åtgärden på klienten. Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan. Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  `authorization`     |  En unik åtkomsttoken som identifierar utgivaren som gör detta API-anrop. Formatet är `"Bearer <accessaccess_token>"` när token-värdet hämtas av utgivaren enligt beskrivningen i [Hämta en token baserat på Azure AD-appen](./pc-saas-registration.md#get-the-token-with-an-http-post). |
|  `x-ms-marketplace-token`  | Parametern för att köpa identifierings- *token* som ska matchas.  Token skickas i landnings sidans URL-anrop när kunden omdirigeras till SaaS-partnerns webbplats (till exempel: `https://contoso.com/signup?token=<token><authorization_token>` ). <br> <br>  Observera att *token* -värdet som kodas är en del av URL: en för landnings sidan, så att det måste avkodas innan det används som en parameter i detta API-anrop.  <br> <br> Här är ett exempel på en kodad sträng i URL: en: `contoso.com/signup?token=ab%2Bcd%2Fef` , där *token* är `ab%2Bcd%2Fef` .  Samma token avkodad är: `Ab+cd/ef` |
| | |

*Svars koder:*

Kod: 200 returnerar unika prenumerations-ID: n för SaaS baserat på den `x-ms-marketplace-token` angivna.

Exempel på svars text:

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "subscriptionName": "Contoso Cloud Solution", // SaaS subscription name
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased offer's plan ID
  "quantity": "20", // number of purchased seats, might be empty if the plan is not per seat
  "subscription": { // full SaaS subscription details, see Get Subscription APIs response body for full description
    "id": "<guid>",
    "publisherId": "contoso",
    "offerId": "offer1",
    "name": "Contoso Cloud Solution",
    "saasSubscriptionStatus": " PendingFulfillmentStart ",
    "beneficiary": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "purchaser": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "planId": "silver",
    "term": {
      "termUnit": "P1M",
      "startDate": "2019-05-31",
      "endDate": "2019-06-29"
    },
    "isTest": true,
    "isFreeTrial": false,
    "allowedCustomerOperations": ["Delete", "Update", "Read"],
    "sandboxType": "None",
    "sessionMode": "None"
  }
}

```

Felkod: 400 Felaktig begäran. `x-ms-marketplace-token` saknas, är felaktigt, ogiltigt eller har upphört att gälla.

Kod: 403 förbjuden. Autentiseringstoken är ogiltig, upphört eller har inte angetts.  Begäran försöker komma åt en SaaS-prenumeration för ett erbjudande som har publicerats med ett annat Azure AD App-ID från den som användes för att skapa autentiseringstoken.

Det här felet är ofta ett symtom på att inte utföra [SaaS-registreringen](pc-saas-registration.md) korrekt.

Kod: 500 internt Server fel.  Gör om API-anropet.  Kontakta [Microsoft-supporten](https://partner.microsoft.com/support/v2/?stage=1)om felet kvarstår.

#### <a name="activate-a-subscription"></a>Aktivera en prenumeration

När SaaS-kontot har kon figurer ATS för en slutanvändare måste utgivaren anropa API för aktiverings prenumeration på Microsoft-sidan.  Kunden debiteras inte om inte API-anropet lyckas.

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Efter `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Frågeparametrar:*

|  Parameter         | Värde             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Använd 2018-08-31.   |
| `subscriptionId` | Den unika identifieraren för den köpta SaaS-prenumerationen.  Detta ID hämtas efter att du har löst den kommersiella Marketplace-autentiseringstoken med hjälp av [lösnings-API: et](#resolve-a-purchased-subscription).
 |

*Begärandehuvuden:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID.  Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
| `x-ms-correlationid` |  Ett unikt sträng värde för åtgärden på klienten.  Den här strängen korrelerar alla händelser från klient åtgärden med händelser på Server sidan.  Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
| `authorization`      |  En unik åtkomsttoken som identifierar utgivaren som gör detta API-anrop. Formatet är `"Bearer <access_token>"` när token-värdet hämtas av utgivaren enligt beskrivningen i [Hämta en token baserat på Azure AD-appen](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Exempel på begäran om nytto last:*

```json
{  // needed for validation of the activation request
  "planId": "gold", // purchased plan, cannot be empty
  "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*Svars koder:*

Kod: 200 prenumerationen har marker ATS som prenumererad på Microsoft-sidan.

Det finns ingen svars text för det här anropet.

Felkod: 400 Felaktig begäran: verifieringen misslyckades.

* `planId` finns inte i nytto lasten för begäran.
* `planId` i nytto lasten för begäran stämmer inte med den som har köpts.
* `quantity` i nytto lasten för begäran stämmer inte med den som har köpts
* SaaS-prenumerationen *har ett* *inaktiverat* tillstånd eller har pausats.

Kod: 403 förbjuden. Autentiseringstoken är ogiltig, upphört eller har inte angetts. Begäran försöker komma åt en SaaS-prenumeration för ett erbjudande som har publicerats med ett annat Azure AD App-ID från den som användes för att skapa autentiseringstoken.

Det här felet är ofta ett symtom på att inte utföra [SaaS-registreringen](pc-saas-registration.md) korrekt.

Kod: 404 hittades inte. SaaS-prenumerationen har avslutit *prenumerations tillstånd.*

Kod: 500 internt Server fel.  Gör om API-anropet.  Kontakta [Microsoft-supporten](https://partner.microsoft.com/support/v2/?stage=1)om felet kvarstår.

#### <a name="get-list-of-all-subscriptions"></a>Hämta lista över alla prenumerationer

Detta API hämtar en lista över alla inköpta SaaS-prenumerationer för alla erbjudanden som utgivaren publicerar på den kommersiella marknaden.  SaaS-prenumerationer i alla möjliga statusar returneras. Prenumerationer som prenumererar på SaaS returneras också, eftersom den här informationen inte tas bort på Microsoft-sidan.

API: n returnerar sid brytnings resultat på 100 per sida.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Ta `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Frågeparametrar:*

|  Parameter         | Värde             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Använd 2018-08-31.  |
| `continuationToken`  | Valfri parameter. Lämna tomt om du vill hämta den första resultat sidan.  Använd värdet som returnerades i `@nextLink` parametern för att hämta nästa sida. |

*Begärandehuvuden:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
| `x-ms-correlationid` |  Ett unikt sträng värde för åtgärden på klienten.  Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan.  Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
| `authorization`      |  En unik åtkomsttoken som identifierar utgivaren som gör detta API-anrop.  Formatet är `"Bearer <access_token>"` när token-värdet hämtas av utgivaren enligt beskrivningen i [Hämta en token baserat på Azure AD-appen](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Svars koder:*

Kod: 200 returnerar listan över alla befintliga prenumerationer för alla erbjudanden som gjorts av den här utgivaren, baserat på utgivarens autentiseringstoken.

*Exempel på svars text:*

```json
{
  "subscriptions": [
    {
      "id": "<guid>", // purchased SaaS subscription ID
      "name": "Contoso Cloud Solution", // SaaS subscription name
      "publisherId": "contoso", // publisher ID
      "offerId": "offer1", // purchased offer ID
      "planId": "silver", // purchased plan ID
      "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
      "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription was purchased.
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": { // email address, user ID and tenant ID that purchased the SaaS subscription. These could be different from beneficiary information for reseller (CSP) purchase
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": { // The period for which the subscription was purchased.
        "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
        "endDate": "2019-06-30", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
        "termUnit": "P1M" // where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values
      },
      "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary. For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
      "sessionMode": "None", // not relevant
      "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. (Optional field -– if not returned, the value is false.)
      "isTest": false, // not relevant
      "sandboxType": "None", // not relevant
      "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. Can be one of the following: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
    },
    // next SaaS subscription details, might be a different offer
    {
      "id": "<guid1>",
      "name": "Contoso Cloud Solution1",
      "publisherId": "contoso",
      "offerId": "offer2",
      "planId": "gold",
      "quantity": "",
      "beneficiary": {
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": {
        "emailId": "purchase@csp.com ",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": {
        "startDate": "2019-05-31",
        "endDate": "2020-04-30",
        "termUnit": "P1Y"
      },
      "allowedCustomerOperations": ["Read"],
      "sessionMode": "None",
      "isFreeTrial": false,
      "isTest": false,
      "sandboxType": "None",
      "saasSubscriptionStatus": "Suspended"
    }
  ],
  "@nextLink": "https:// https://marketplaceapi.microsoft.com/api/saas/subscriptions/?continuationToken=%5b%7b%22token%22%3a%22%2bRID%3a%7eYeUDAIahsn22AAAAAAAAAA%3d%3d%23RT%3a1%23TRC%3a2%23ISV%3a1%23FPC%3aAgEAAAAQALEAwP8zQP9%2fFwD%2b%2f2FC%2fwc%3d%22%2c%22range%22%3a%7b%22min%22%3a%22%22%2c%22max%22%3a%2205C1C9CD673398%22%7d%7d%5d&api-version=2018-08-31" // url that contains continuation token to retrieve next page of the SaaS subscriptions list, if empty or absent, this is the last page. ISV can use this url as is to retrieve the next page or extract the value of continuation token from this url.
}
```

Om det inte finns några inköpta SaaS-prenumerationer för den här utgivaren returneras tom svars text.

Kod: 403 förbjuden. Autentiseringstoken är inte tillgänglig, ogiltig eller har upphört att gälla.

Det här felet är ofta ett symtom på att inte utföra [SaaS-registreringen](pc-saas-registration.md) korrekt. 

Kod: 500 internt Server fel. Gör om API-anropet.  Kontakta [Microsoft-supporten](https://partner.microsoft.com/support/v2/?stage=1)om felet kvarstår.

#### <a name="get-subscription"></a>Hämta prenumeration

Detta API hämtar en angiven inköpt SaaS-prenumeration för ett SaaS-erbjudande som utgivaren publicerar på den kommersiella marknaden. Använd det här anropet för att hämta all tillgänglig information för en speciell SaaS-prenumeration i stället för genom att anropa API: et som används för att hämta en lista över alla prenumerationer.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Ta `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   Använd 2018-08-31. |
| `subscriptionId`     |  Den unika identifieraren för den köpta SaaS-prenumerationen.  Detta ID hämtas efter att du har löst den kommersiella Marketplace-autentiseringstoken med hjälp av lösnings-API: et. |

*Begärandehuvuden:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
|  `x-ms-correlationid` |  Ett unikt sträng värde för åtgärden på klienten.  Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan.  Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
|  `authorization`     | En unik åtkomsttoken som identifierar utgivaren som gör detta API-anrop. Formatet är `"Bearer <access_token>"` när token-värdet hämtas av utgivaren enligt beskrivningen i [Hämta en token baserat på Azure AD-appen](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Svars koder:*

Kod: 200 returnerar information om en SaaS-prenumeration baserat på den `subscriptionId` angivna.

*Exempel på svars text:*

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "name": "Contoso Cloud Solution", // SaaS subscription name
  "publisherId": "contoso", // publisher ID
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased plan ID
  "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
  "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription is purchased.
    "emailId": "test@contoso.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "purchaser": { // email address ,user ID and tenant ID that purchased the SaaS subscription.  These could be different from beneficiary information for reseller (CSP) scenario
    "emailId": "test@test.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary.  For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
  "sessionMode": "None", // not relevant
  "isFreeTrial": false, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. Optional field – if not returned the value is false.
  "isTest": false, // not relevant
  "sandboxType": "None", // not relevant
  "saasSubscriptionStatus": " Subscribed ", // Indicates the status of the operation: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
  "term": { // the period for which the subscription was purchased
    "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
    "endDate": "2019-06-29", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
    "termUnit": "P1M" //where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values.
  }
}
```

Kod: 403 förbjuden. Autentiseringstoken är ogiltig, upphört eller har inte angetts. Begäran försöker komma åt en SaaS-prenumeration för ett erbjudande som har publicerats med ett annat Azure AD App-ID från det som används för att skapa autentiseringstoken.

Det här felet är ofta ett symtom på att inte utföra [SaaS-registreringen](pc-saas-registration.md) korrekt. 

Kod: 404 hittades inte.  Det går inte att hitta SaaS-prenumerationen med det angivna värdet `subscriptionId` .

Kod: 500 internt Server fel.  Gör om API-anropet.  Kontakta [Microsoft-supporten](https://partner.microsoft.com/support/v2/?stage=1)om felet kvarstår.

#### <a name="list-available-plans"></a>Lista tillgängliga planer

Detta API hämtar alla planer för ett SaaS-erbjudande som identifieras av `subscriptionId` ett speciellt köp av det här erbjudandet.  Använd det här anropet för att hämta en lista över alla privata och offentliga planer som mottagaren av en SaaS-prenumeration kan uppdatera för prenumerationen.  De inköps planer som returneras är tillgängliga i samma geografi som den redan köpta planen.

Det här anropet returnerar en lista över planer som är tillgängliga för kunden utöver det som redan har köpts.  Listan kan visas för en slutanvändare på utgivarens webbplats.  En användare kan ändra prenumerations planen till något av planerna i den returnerade listan.  Det går inte att ändra planen till en som inte visas i listan.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Ta `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Frågeparametrar:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Använd 2018-08-31.  |
|  `subscriptionId`    |  Den unika identifieraren för den köpta SaaS-prenumerationen.  Detta ID hämtas efter att du har löst den kommersiella Marketplace-autentiseringstoken med hjälp av lösnings-API: et. |

*Begärandehuvuden:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID.  Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
|  `x-ms-correlationid`  |  Ett unikt sträng värde för åtgärden på klienten.  Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan.  Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
|  `authorization`     |  En unik åtkomsttoken som identifierar utgivaren som gör detta API-anrop.  Formatet är `"Bearer <access_token>"` när token-värdet hämtas av utgivaren enligt beskrivningen i [Hämta en token baserat på Azure AD-appen](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Svars koder:*

Kod: 200 returnerar en lista med alla tillgängliga planer för en befintlig SaaS-prenumeration, inklusive den som redan har köpts.

Exempel på svars text:

```json
{
  "plans": [
    {
      "planId": "Platinum001",
      "displayName": "Private platinum plan for Contoso", // display name of the plan as it appears in the marketplace
      "isPrivate": true //true or false
    },
    {
      "planId": "gold",
      "displayName": "Gold plan for Contoso",
      "isPrivate": false //true or false
    }
  ]
}
```

Om `subscriptionId` inte hittas returneras tom svars text.

Kod: 403 förbjuden. Autentiseringstoken är ogiltig, upphört eller har inte angetts.  Begäran kanske försöker få åtkomst till en SaaS-prenumeration för ett erbjudande som publiceras med ett annat Azure AD App-ID från det som används för att skapa en autentiseringstoken.

Det här felet är ofta ett symtom på att inte utföra [SaaS-registreringen](pc-saas-registration.md) korrekt. 

Kod: 500 internt Server fel.  Gör om API-anropet.  Kontakta [Microsoft-supporten](https://partner.microsoft.com/support/v2/?stage=1)om felet kvarstår.

#### <a name="change-the-plan-on-the-subscription"></a>Ändra planen för prenumerationen

Använd det här API: et för att uppdatera den befintliga prenumerationen som har köpts för en SaaS-prenumeration till en ny plan (offentlig eller privat).  Utgivaren måste anropa detta API när en plan ändras på utgivarens sida för en SaaS-prenumeration som har köpts på den kommersiella marknads platsen.

Detta API kan endast anropas för *aktiva* prenumerationer.  Eventuella planer kan ändras till alla befintliga prenumerationer (offentliga eller privata) men inte till sig själva.  För privata planer måste kundens klient organisation definieras som en del av planens mål grupp i Partner Center.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>9.0a `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Använd 2018-08-31.  |
| `subscriptionId`     | Den unika identifieraren för den köpta SaaS-prenumerationen.  Detta ID hämtas efter att du har löst den kommersiella Marketplace-autentiseringstoken med hjälp av lösnings-API: et. |

*Begärandehuvuden:*
 
|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID. Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  `x-ms-correlationid`  | Ett unikt sträng värde för åtgärden på klienten.  Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan.  Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  `authorization`     |  En unik åtkomsttoken som identifierar utgivaren som gör detta API-anrop.  Formatet är `"Bearer <access_token>"` när token-värdet hämtas av utgivaren enligt beskrivningen i [Hämta en token baserat på Azure AD-appen](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Exempel på begäran om nytto last:*

```json
{
  "planId": "gold" // the ID of the new plan to be purchased
}
```

*Svars koder:*

Kod: 202 begäran om ändrings plan har accepterats och hanteras asynkront.  Partnern förväntas söka i **URL: en för åtgärds platsen** för att fastställa om begäran om ändrings planen lyckades eller inte.  Avsökningen bör göras var några: e sekund tills den slutliga statusen *misslyckades*, *lyckades* eller *krocken* tas emot för åtgärden.  Den slutgiltiga åtgärds statusen returneras snabbt, men det kan ta flera minuter i vissa fall.

Partnern får också ett webhook-meddelande när åtgärden är redo att slutföras på den kommersiella Marketplace-sidan.  Först ska utgivaren göra planen oförändrad på utgivarens sida.

*Svars rubriker:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  URL för att hämta åtgärdens status.  Till exempel `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Felkod: 400 Felaktig begäran: verifierings fel.

* Det nya schemat finns inte eller är inte tillgängligt för den här SaaS-prenumerationen.
* Den nya planen är samma som den aktuella planen.
* Prenumerations status för SaaS har inte *registrerats*.
* Uppdaterings åtgärden för en SaaS-prenumeration ingår inte i `allowedCustomerOperations` .

Kod: 403 förbjuden. Autentiseringstoken är ogiltig, upphört eller har inte angetts.  Begäran försöker komma åt en SaaS-prenumeration för ett erbjudande som har publicerats med ett annat Azure AD App-ID från det som används för att skapa autentiseringstoken.

Det här felet är ofta ett symtom på att inte utföra [SaaS-registreringen](pc-saas-registration.md) korrekt.

Kod: 404 hittades inte.  Det gick inte att hitta SaaS-prenumerationen `subscriptionId` .

Kod: 500 internt Server fel.  Gör om API-anropet.  Kontakta [Microsoft-supporten](https://partner.microsoft.com/support/v2/?stage=1)om felet kvarstår.

>[!NOTE]
>Du kan ändra antingen planen eller antalet platser samtidigt, inte båda.

>[!Note]
>Detta API kan endast anropas när explicit godkännande har hämtats för ändringen från slutanvändaren.

#### <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>Ändra antalet platser i SaaS-prenumerationen

Använd det här API: et för att uppdatera (öka eller minska) antalet platser som har köpts för en SaaS-prenumeration.  Utgivaren måste anropa detta API när antalet platser ändras från utgivarens sida för en SaaS-prenumeration som har skapats på den kommersiella Marketplace.

Antalet platser får inte vara större än antalet som tillåts i den aktuella planen.  I det här fallet bör utgivaren ändra planen innan antalet platser ändras.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>9.0a `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Använd 2018-08-31.  |
|  `subscriptionId`     | En unik identifierare för den köpta SaaS-prenumerationen.  Detta ID hämtas efter att du har löst den kommersiella Marketplace-autentiseringstoken med hjälp av lösnings-API: et.  |

*Begärandehuvuden:*
 
|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID.  Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  `x-ms-correlationid`  | Ett unikt sträng värde för åtgärden på klienten.  Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan.  Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  `authorization`     | En unik åtkomsttoken som identifierar utgivaren som gör detta API-anrop.  Formatet är `"Bearer <access_token>"` när token-värdet hämtas av utgivaren enligt beskrivningen i [Hämta en token baserat på Azure AD-appen](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Exempel på begäran om nytto last:*

```json
{
  "quantity": 5 // the new amount of seats to be purchased
}
```

*Svars koder:*

Kod: 202 begäran om att ändra kvantitet har accepterats och hanteras asynkront. Partnern förväntas söka på **Åtgärds platsens URL** för att fastställa om begäran om ändrings antal lyckades eller inte.  Avsökningen bör göras var några: e sekund tills den slutliga statusen *misslyckades*, *lyckades* eller *krocken* tas emot för åtgärden.  Den slutgiltiga åtgärds statusen returneras snabbt men kan ta flera minuter i vissa fall.

Partnern får också ett webhook-meddelande när åtgärden är redo att slutföras på den kommersiella Marketplace-sidan.  Först ska utgivaren göra ändringen på utgivarens sida.

*Svars rubriker:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Länka till en resurs för att få åtgärdens status.  Till exempel `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`.  |

Felkod: 400 Felaktig begäran: verifierings fel.

* Den nya kvantiteten är större eller lägre än den aktuella plan gränsen.
* Den nya kvantiteten saknas.
* Den nya kvantiteten är samma som den aktuella kvantiteten.
* Prenumerations status för SaaS prenumererar inte.
* Uppdaterings åtgärden för en SaaS-prenumeration ingår inte i `allowedCustomerOperations` .

Kod: 403 förbjuden.  Autentiseringstoken är ogiltig, upphört eller har inte angetts.  Begäran försöker komma åt en prenumeration som inte tillhör den aktuella utgivaren.

Det här felet är ofta ett symtom på att inte utföra [SaaS-registreringen](pc-saas-registration.md) korrekt. 

Kod: 404 hittades inte.  Det gick inte att hitta SaaS-prenumerationen `subscriptionId` .

Kod: 500 internt Server fel.  Gör om API-anropet.  Kontakta [Microsoft-supporten](https://partner.microsoft.com/support/v2/?stage=1)om felet kvarstår.

>[!Note]
>Det går bara att ändra en plan eller kvantitet åt gången, inte båda.

>[!Note]
>Detta API kan endast anropas när explicit godkännande har hämtats från slutanvändaren för ändringen.

#### <a name="cancel-a-subscription"></a>Avbryta en prenumeration

Använd det här API: et för att avbryta prenumerationen på en angiven SaaS-prenumeration.  Utgivaren behöver inte använda detta API och vi rekommenderar att kunderna dirigeras till den kommersiella marknads platsen för att avbryta SaaS-prenumerationer.

Om utgivaren beslutar att implementera annulleringen av en SaaS-prenumeration som har köpts på den kommersiella marknads platsen på utgivarens sida, måste de anropa detta API.  När det här anropet har slutförts blir prenumerationens status *avkopplad* från Microsoft-sidan.

Kunden debiteras inte om en prenumeration annulleras inom följande Grace-perioder:

* 24 timmar för en månatlig prenumeration efter aktiveringen.
* Fjorton dagar för en års prenumeration efter aktiveringen.

Kunden debiteras om en prenumeration annulleras efter föregående Grace-perioder.  Kunden kommer att förlora åtkomsten till SaaS-prenumerationen på Microsoft-sidan omedelbart efter annullering. 

##### <a name="delete-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Ta bort `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Använd 2018-08-31.  |
|  `subscriptionId`     | Den unika identifieraren för den köpta SaaS-prenumerationen.  Detta ID hämtas efter att du har löst den kommersiella Marketplace-autentiseringstoken med hjälp av lösnings-API: et.  |

*Begärandehuvuden:*
 
|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID.  Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  `x-ms-correlationid`  | Ett unikt sträng värde för åtgärden på klienten.  Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan.  Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  `authorization`     |  En unik åtkomsttoken som identifierar utgivaren som gör detta API-anrop.  Formatet är `"Bearer <access_token>"` när token-värdet hämtas av utgivaren enligt beskrivningen i [Hämta en token baserat på Azure AD-appen](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Svars koder:*

Kod: 202 begäran om att avbryta prenumerationen har accepterats och hanteras asynkront.  Partnern förväntas söka i **URL: en för åtgärds platsen** för att fastställa om begäran lyckades eller inte.  Avsökningen bör göras var några: e sekund tills den slutliga statusen *misslyckades*, *lyckades* eller *krocken* tas emot för åtgärden.  Den slutgiltiga åtgärds statusen returneras snabbt men kan ta flera minuter i vissa fall.

Partnern får också ett webhook-meddelande när åtgärden har slutförts på den kommersiella Marketplace-sidan.  Sedan ska utgivaren avbryta prenumerationen på utgivarens sida.

*Svars rubriker:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Länka till en resurs för att få åtgärdens status.  Till exempel `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Felkod: 400 Felaktig begäran.  Ta bort finns inte i `allowedCustomerOperations` listan för den här SaaS-prenumerationen.

Kod: 403 förbjuden.  Autentiseringstoken är ogiltig, upphört eller är inte tillgänglig. Begäran försöker komma åt en SaaS-prenumeration för ett erbjudande som har publicerats med ett annat Azure AD App-ID från det som används för att skapa autentiseringstoken.

Det här felet är ofta ett symtom på att inte utföra [SaaS-registreringen](pc-saas-registration.md) korrekt.

Kod: 404 hittades inte.  Det gick inte att hitta SaaS-prenumerationen `subscriptionId` .

Kod: 500 internt Server fel. Gör om API-anropet.  Kontakta [Microsoft-supporten](https://partner.microsoft.com/support/v2/?stage=1)om felet kvarstår.

### <a name="operations-apis"></a>API: er för åtgärder

#### <a name="list-outstanding-operations"></a>Lista över utestående åtgärder 

Hämta lista över väntande åtgärder för den angivna SaaS-prenumerationen.  Utgivaren bör bekräfta returnerade åtgärder genom att anropa [API för åtgärds korrigering](#update-the-status-of-an-operation).

För närvarande returneras bara **åtgärder** som svar på det här API-anropet.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Ta `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Frågeparametrar:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  Använd 2018-08-31.         |
|    `subscriptionId` | Den unika identifieraren för den köpta SaaS-prenumerationen.  Detta ID hämtas efter att du har löst den kommersiella Marketplace-autentiseringstoken med hjälp av lösnings-API: et.  |

*Begärandehuvuden:*
 
|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `content-type`     |  `application/json` |
|  `x-ms-requestid`    |  Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID.  Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  `x-ms-correlationid` |  Ett unikt sträng värde för åtgärden på klienten.  Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan.  Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  `authorization`     |  Formatet är `"Bearer <access_token>"` när token-värdet hämtas av utgivaren enligt beskrivningen i [Hämta en token baserat på Azure AD-appen](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Svars koder:*

Kod: 200 returnerar väntande återställnings åtgärd på den angivna SaaS-prenumerationen.

*Exempel på svars nytto last:*

```json
{
  "operations": [
    {
      "id": "<guid>", //Operation ID, should be provided in the operations patch API call
      "activityId": "<guid>", //not relevant
      "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription that is being reinstated
      "offerId": "offer1", // purchased offer ID
      "publisherId": "contoso",
      "planId": "silver", // purchased plan ID
      "quantity": "20", // purchased amount of seats, will be empty is not relevant
      "action": "Reinstate",
      "timeStamp": "2018-12-01T00:00:00", // UTC
      "status": "InProgress" // the only status that can be returned in this case
    }
  ]
}
```

Returnerar tom JSON om inga återställnings åtgärder väntar.

Felkod: 400 Felaktig begäran: verifierings fel.

Kod: 403 förbjuden. Autentiseringstoken är ogiltig, upphört eller har inte angetts.  Begäran försöker komma åt en SaaS-prenumeration för ett erbjudande som har publicerats med ett annat Azure AD App-ID från det som används för att skapa autentiseringstoken.

Det här felet är ofta ett symtom på att inte utföra [SaaS-registreringen](pc-saas-registration.md) korrekt. 

Kod: 404 hittades inte.  Det gick inte att hitta SaaS-prenumerationen `subscriptionId` .

Kod: 500 internt Server fel. Gör om API-anropet.  Kontakta [Microsoft-supporten](https://partner.microsoft.com/support/v2/?stage=1)om felet kvarstår.

#### <a name="get-operation-status"></a>Hämta åtgärds status

Med det här API: et kan utgivare spåra statusen för den angivna asynkrona åtgärden:  **Avsluta prenumeration**, **ChangePlan** eller **ChangeQuantity**.

`operationId`För det här API-anropet kan hämtas från det värde som returneras av **Åtgärds platsen**, API-anropet get väntar på åtgärder eller det `<id>` parameter värde som togs emot i ett webhook-anrop.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Ta `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Använd 2018-08-31.  |
|  `subscriptionId`    |  Den unika identifieraren för den köpta SaaS-prenumerationen.  Detta ID hämtas efter att du har löst den kommersiella Marketplace-autentiseringstoken med hjälp av lösnings-API: et. |
|  `operationId`       |  Den unika identifieraren för den åtgärd som hämtas. |

*Begärandehuvuden:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`   |
|  `x-ms-requestid`    |  Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID.  Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
|  `x-ms-correlationid` |  Ett unikt sträng värde för åtgärden på klienten.  Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan.  Om det här värdet inte anges genereras och anges ett i svarshuvuden.  |
|  `authorization`     |  En unik åtkomsttoken som identifierar utgivaren som gör detta API-anrop.  Formatet är `"Bearer <access_token>"` när token-värdet hämtas av utgivaren enligt beskrivningen i [Hämta en token baserat på Azure AD-appen](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Svars koder:*

Kod: 200 hämtar information om den angivna SaaS-åtgärden. 

*Exempel på svars nytto last:*

```json
Response body:
{
  "id  ": "<guid>", //Operation ID, should be provided in the patch operation API call
  "activityId": "<guid>", //not relevant
  "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription for which this operation is relevant
  "offerId": "offer1", // purchased offer ID
  "publisherId": "contoso",
  "planId": "silver", // purchased plan ID
  "quantity": "20", // purchased amount of seats
  "action": "ChangePlan", // Can be ChangePlan, ChangeQuantity or Reinstate
  "timeStamp": "2018-12-01T00:00:00", // UTC
  "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeeded, Conflict (new quantity / plan is the same as existing)
  "errorStatusCode": "",
  "errorMessage": ""
}
```

Kod: 403 förbjuden. Autentiseringstoken är ogiltig, upphört eller har inte angetts.  Begäran försöker komma åt en SaaS-prenumeration för ett erbjudande som har publicerats med ett annat Azure AD App-ID från det som används för att skapa autentiseringstoken.

Det här felet är ofta ett symtom på att inte utföra [SaaS-registreringen](pc-saas-registration.md) korrekt. 

Kod: 404 hittades inte.  

* `subscriptionId`Det gick inte att hitta prenumerationen.
* Åtgärden med `operationId` hittades inte.

Kod: 500 internt Server fel.  Gör om API-anropet.  Kontakta [Microsoft-supporten](https://partner.microsoft.com/support/v2/?stage=1)om felet kvarstår.

#### <a name="update-the-status-of-an-operation"></a>Uppdatera status för en åtgärd

Använd det här API: et för att uppdatera statusen för en väntande åtgärd för att ange att åtgärden lyckades eller misslyckades på utgivarens sida.

`operationId`För det här API-anropet kan hämtas från det värde som returneras av **Åtgärds platsen**, API-anropet get väntar på åtgärder eller det `<id>` parameter värde som togs emot i ett webhook-anrop.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>9.0a `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Frågeparametrar:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  Använd 2018-08-31.  |
|   `subscriptionId`   |  Den unika identifieraren för den köpta SaaS-prenumerationen.  Detta ID hämtas efter att du har löst den kommersiella Marketplace-autentiseringstoken med hjälp av lösnings-API: et.  |
|   `operationId`      |  Den unika identifieraren för åtgärden som slutförs. |

*Begärandehuvuden:*

|  Parameter         | Värde             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  Ett unikt sträng värde för att spåra begäran från klienten, helst en GUID.  Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
|   `x-ms-correlationid` |  Ett unikt sträng värde för åtgärden på klienten.  Den här parametern korrelerar alla händelser från klient åtgärden med händelser på Server sidan.  Om det här värdet inte anges genereras och anges ett i svarshuvuden. |
|  `authorization`     |  En unik åtkomsttoken som identifierar utgivaren som gör detta API-anrop.  Formatet är `"Bearer <access_token>"` när token-värdet hämtas av utgivaren enligt beskrivningen i [Hämta en token baserat på Azure AD-appen](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Exempel på begäran om nytto last:*

```json
{
  "status": "Success" // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*Svars koder:*

Kod: 200 ett anrop för att informera om att en åtgärd slutförts på partner sidan.  Svaret kan till exempel signalera att ändringar av platser eller planer på utgivarens sida har ändrats.

Kod: 403
- Ej tillåtet.  Autentiseringstoken är inte tillgänglig, är ogiltig eller har upphört att gälla. Begäran kanske försöker komma åt en prenumeration som inte tillhör den aktuella utgivaren.
- Ej tillåtet.  Autentiseringstoken är ogiltig, upphört eller har inte angetts.  Begäran försöker komma åt en SaaS-prenumeration för ett erbjudande som har publicerats med ett annat Azure AD App-ID från det som används för att skapa autentiseringstoken.

Det här felet är ofta ett symtom på att inte utföra [SaaS-registreringen](pc-saas-registration.md) korrekt.

Kod: 404 hittades inte.

* `subscriptionId`Det gick inte att hitta prenumerationen.
* Åtgärden med `operationId` hittades inte.

Kod: 409-konflikt.  Till exempel är en nyare uppdatering redan uppfylld.

Kod: 500 internt Server fel.  Gör om API-anropet.  Kontakta [Microsoft-supporten](https://partner.microsoft.com/support/v2/?stage=1)om felet kvarstår.

## <a name="implementing-a-webhook-on-the-saas-service"></a>Implementera en webhook på SaaS-tjänsten

När du skapar ett transactable SaaS-erbjudande i Partner Center tillhandahåller partnern den **anslutning-webhook** -URL som ska användas som en http-slutpunkt.  Denna webhook anropas av Microsoft med hjälp av POST HTTP-anropet för att meddela utgivarens sida om följande händelser som inträffar på Microsoft-sidan:

* När SaaS-prenumerationen är i *prenumerations* status:
    * ChangePlan 
    * ChangeQuantity
    * Suspend
    * Avbryt prenumeration
* När SaaS-prenumerationen har statusen *pausad* :
    * Återinför
    * Avbryt prenumeration

Utgivaren måste implementera en webhook i SaaS-tjänsten för att SaaS-prenumerationens status ska vara konsekvent med Microsoft-sidan.  SaaS-tjänsten krävs för att anropa API: et get-åtgärd för att verifiera och auktorisera webhook-anropet och nytto Last data innan du vidtar åtgärder baserat på webhook-aviseringen.  Utgivaren ska returnera HTTP 200 till Microsoft så snart webhook-anropet bearbetas.  Det här värdet bekräftar att webhook-anropet har tagits emot av utgivaren.

>[!Note]
>Webb adress tjänsten för webhook måste vara igång dygnet runt och redo att ta emot nya samtal från Microsoft tid.  Microsoft har en princip för återförsök för webhook-anropet (500 nya försök över 8 timmar), men om utgivaren inte accepterar anropet och returnerar ett svar, kommer åtgärden som webhooken att meddela sig att sluta fungera på Microsoft-sidan.

*Exempel på webhook-nytto last:*

```json
// end user changed a quantity of purchased seats for a plan on Microsoft side
{
  "id": "<guid>", // this is the operation ID to call with get operation API
  "activityId": "<guid>", // do not use
  "subscriptionId": "guid", // The GUID identifier for the SaaS resource which status changes
  "publisherId": "contoso", // A unique string identifier for each publisher
  "offerId": "offer1", // A unique string identifier for each offer
  "planId": "silver", // the most up-to-date plan ID
  "quantity": " 25", // the most up-to-date number of seats, can be empty if not relevant
  "timeStamp": "2019-04-15T20:17:31.7350641Z", // UTC time when the webhook was called
  "action": "ChangeQuantity", // the operation the webhook notifies about
  "status": "Success" // Can be either InProgress or Success
}
```

```json
// end user's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": "<guid>",
  "activityId": "<guid>",
  "subscriptionId": "<guid>",
  "publisherId": "contoso",
  "offerId": "offer2 ",
  "planId": "gold",
  "quantity": " 20",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Reinstate",
  "status": "In Progress"
}
```

## <a name="development-and-testing"></a>Utveckling och testning

För att starta utvecklings processen rekommenderar vi att du skapar dummy API-svar på utgivarens sida.  Svaren kan baseras på exempel svar som anges i den här artikeln.

När utgivaren är klar för testningen av slut punkt till slut punkt:

* Publicera ett SaaS-erbjudande till en begränsad förhands gransknings publik och behåll den i för hands versions fasen.
* Ange plan priset till 0 för att undvika att den faktiska fakturerings kostnaden utlöses under testningen.  Ett annat alternativ är att ange ett pris som inte är noll och avbryta alla test inköp inom 24 timmar.
* Se till att alla flöden anropas från slut punkt till slut punkt för att simulera ett verkligt kund scenario.
* Om partnern vill testa hela inköps-och fakturerings flödet gör du det med erbjudandet som kostar över $0.  Inköpet faktureras och en faktura skapas.

Ett inköps flöde kan utlösas från Azure Portal eller Microsoft AppSource platser, beroende på var erbjudandet publiceras.

Åtgärder för att *ändra plan*, *ändra antal* och *avbryta prenumerationer* testas från utgivarens sida.  På Microsoft-sidan kan du *avbryta prenumerationen* från både Azure Portal-och administrations Center (portalen där Microsoft AppSource inköp hanteras).  *Ändra kvantitet och plan* kan bara utlösas från administrations Center.

## <a name="get-support"></a>Få support

Se [Support för det kommersiella Marketplace-programmet i Partner Center](../support.md) for Publisher support options.

## <a name="next-steps"></a>Nästa steg

Mer alternativ för SaaS-erbjudanden finns i den kommersiella Marketplace för [API: er för avläsning av marknads](marketplace-metering-service-apis.md) plats.

Granska och Använd [klienterna för olika programmeringsspråk och exempel](https://github.com/microsoft/commercial-marketplace-samples).
