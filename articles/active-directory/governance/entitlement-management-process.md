---
title: Begär process &-meddelanden – hantering av Azure AD-berättigande
description: Läs om process processen för ett Access-paket och när e-postmeddelanden skickas i Azure Active Directory rättighets hantering.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb84eae2db01180865db54250598f45c7b2ddca0
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/23/2020
ms.locfileid: "97746243"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Begär process-och e-postaviseringar i hantering av Azure AD-berättigande

När en användare skickar en begäran till ett Access-paket börjar processen att leverera denna åtkomstbegäran. Hantering av Azure AD-berättigande skickar e-postaviseringar till god kännare och beställare när viktiga händelser inträffar under processen. Den här artikeln beskriver begär ande processen och de e-postmeddelanden som skickas.

## <a name="request-process"></a>Begär process

En användare som behöver åtkomst till ett Access-paket kan skicka en åtkomstbegäran. Beroende på principens konfiguration kan begäran kräva ett godkännande. När en begäran godkänns börjar en process tilldela användaren åtkomst till varje resurs i åtkomst paketet. Följande diagram visar en översikt över processen och de olika tillstånden:

![Diagram över godkännande process](./media/entitlement-management-process/request-process.png)

| Stat | Beskrivning |
| --- | --- |
| Skickad | Användaren skickar en begäran. |
| Väntar på godkännande | Om principen för ett Access-paket kräver godkännande flyttas en begäran till väntar på godkännande. |
| Upphörd | Om inga god kännare godkänner en begäran inom tids gränsen för godkännandebegäran, förfaller begäran. Användaren måste skicka in begäran igen för att försöka igen. |
| Nekad | God kännaren nekar en begäran. |
| Godkända | God kännaren godkänner en begäran. |
| Kvalitativ | Användaren har **inte** tilldelats åtkomst till alla resurser i åtkomst paketet. Om det är en extern användare kanske användaren inte har till gång till resurs katalogen ännu. De kanske inte har godkänt medgivande frågan. |
| Levererade | Användaren har tilldelats åtkomst till alla resurser i åtkomst paketet. |
| Utökad åtkomst | Användaren utökat tilldelningen om tillägg tillåts i principen. |
| Åtkomst upphör | Användarens åtkomst till Access-paketet har upphört att gälla. Användaren måste skicka en begäran för att få åtkomst igen. |

## <a name="email-notifications"></a>E-postmeddelanden

Om du är en god kännare skickas e-postaviseringar när du behöver godkänna en åtkomstbegäran. Du får också meddelanden när en åtkomstbegäran har slutförts. Du har också skickat e-postmeddelanden som visar statusen för din begäran om du är en begär Ande.

Följande diagram visar när de här e-postaviseringarna skickas till god kännarna eller för beställaren. Referera till [tabellen med e-postmeddelanden](entitlement-management-process.md#email-notifications-table) för att hitta motsvarande nummer till e-postaviseringarna som visas i diagrammen.

### <a name="first-approvers-and-alternate-approvers"></a>Första god kännare och alternativa god kännare
Följande diagram visar upplevelsen av de första god kännarna och alternativa god kännare och de e-postmeddelanden som de får under processen:

![Process flöde för första och alternativa god kännare](./media/entitlement-management-process/first-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>Beställare
Följande diagram visar upplevelsen av beställare och de e-postmeddelanden som de får när de begär processen:

![Process flöde för beställare](./media/entitlement-management-process/requestor-approval-request-flow.png)

### <a name="2-stage-approval"></a>2 – steg godkännande
Följande diagram visar upplevelsen av steg-1-och steg 2-god kännare och e-postmeddelanden som de får under processen:

![process flöde för godkännande med 2 steg](./media/entitlement-management-process/2stage-approval-with-request-timeout-flow.png)

### <a name="email-notifications-table"></a>Tabellen e-postavisering
Följande tabell innehåller mer information om var och en av dessa e-postaviseringar. Du kan använda regler för att hantera dessa e-postmeddelanden. I Outlook kan du till exempel skapa regler för att flytta e-postmeddelanden till en mapp om ämnet innehåller ord från den här tabellen:

| # | E-postämne | När det skickas | Skickat till |
| --- | --- | --- | --- |
| 1 | Åtgärd krävs: Godkänn eller neka vidarebefordrad begäran senast *[datum]* | Det här e-postmeddelandet skickas till alternativa god kännare i steg 1 (när begäran har eskalerats) för att vidta åtgärder. | Steg-1 alternativa god kännare |
| 2 | Åtgärd krävs: Godkänn eller neka begäran senast *[datum]* | Det här e-postmeddelandet skickas till den första god kännaren, om eskalering är inaktive rad, för att vidta åtgärder. | Första god kännaren |
| 3 | Påminnelse: Godkänn eller neka begäran senast *[datum]* för *[begär ande]* | E-postpåminnelsen skickas till den första god kännaren, om eskaleringen är inaktive rad. E-postmeddelandet ber dem att vidta åtgärder om de inte har det. | Första god kännaren |
| 4 | Godkänn eller neka begäran senast *[tid]* den *[datum]* | Det här e-postmeddelandet skickas till den första god kännaren (om eskalering är aktiverat) för att vidta åtgärder. | Första god kännaren |
| 5 | Åtgärd krävs påminnelse: Godkänn eller neka begäran senast *[datum]* för *[begär ande]* | E-postpåminnelsen skickas till den första god kännaren om eskalering är aktiverat. E-postmeddelandet ber dem att vidta åtgärder om de inte har det. | Första god kännaren |
| 6 | Begäran har upphört att gälla *[access_package]* | Det här e-postmeddelandet skickas till de första god kännarna för god kännare och steg 1 efter att begäran har upphört att gälla. | Första god kännare, steg-1 alternativa god kännare |
| 7 | Begäran har godkänts för *[beställare]* till *[access_package]* | Det här e-postmeddelandet skickas till de första god kännarna för god kännare och steg 1 när begäran har slutförts. | Första god kännare, steg-1 alternativa god kännare |
| 8 | Begäran har godkänts för *[beställare]* till *[access_package]* | Det här e-postmeddelandet skickas till de första god kännarna för god kännare och steg 1 i en 2-fas-begäran när steg 1-begäran har godkänts. | Första god kännare, steg-1 alternativa god kännare |
| 9 | Begäran nekades till *[access_package]* | Det här e-postmeddelandet skickas till beställaren när deras begäran nekas | Requestor |
| 10 | Din begäran har upphört att gälla för *[access_package]* | Det här e-postmeddelandet skickas till beställaren i slutet av en enskild eller två-fas-begäran. E-postmeddelandet meddelar beställaren att begäran har upphört att gälla. | Requestor |
| 11 | Åtgärd krävs: Godkänn eller neka begäran senast *[datum]* | Det här e-postmeddelandet skickas till den andra god kännaren, om eskaleringen är inaktive rad, för att vidta åtgärder. | Andra god kännaren |
| 12 | Åtgärd krävs påminnelse: Godkänn eller neka begäran senast *[datum]* | E-postpåminnelsen skickas till den andra god kännaren, om eskaleringen är inaktive rad. Meddelandet uppmanar dem att vidta åtgärder om de inte ännu är det. | Andra god kännaren |
| 13 | Åtgärd krävs: Godkänn eller neka begäran senast *[datum]* för *[begär ande]* | Det här e-postmeddelandet skickas till andra god kännare, om eskalering är aktiverat, för att vidta åtgärder. | Andra god kännaren |
| 14 | Åtgärd krävs påminnelse: Godkänn eller neka begäran senast *[datum]* för *[begär ande]* | E-postpåminnelsen skickas till den andra god kännaren om eskalering är aktiverat. Meddelandet uppmanar dem att vidta åtgärder om de inte ännu är det. | Andra god kännaren |
| 15 | Åtgärd krävs: Godkänn eller neka vidarebefordrad begäran senast *[datum]* | Det här e-postmeddelandet skickas till alternativa god kännare för steg 2, om eskalering är aktiverat, för att vidta åtgärder. | Steg 2 alternativa god kännare |
| 16 | Begäran har godkänts för *[beställare]* till *[access_package]* | Det här e-postmeddelandet skickas till den andra god kännaren och de alternativa god kännarna i steg 2 vid godkännande av begäran. | Andra god kännare, steg 2 alternativa god kännare |
| 17 | En begäran har upphört att gälla för *[access_package]* | Det här e-postmeddelandet skickas till den andra god kännaren eller alternativa god kännare när begäran upphör att gälla. | Andra god kännare, steg 2 alternativa god kännare |
| 18 | Du har nu åtkomst till *[access_package]* | Det här e-postmeddelandet skickas till slutanvändarna för att börja använda sin åtkomst. | Requestor |
| 19 | Utöka åtkomsten för *[access_package]* senast *[datum]* | Det här e-postmeddelandet skickas till slutanvändarna innan deras åtkomst upphör att gälla. | Requestor |
| 20 | Åtkomst har slutförts för *[access_package]* | Det här e-postmeddelandet skickas till slutanvändarna när deras åtkomst upphör att gälla. | Requestor |

### <a name="access-request-emails"></a>E-postmeddelanden för åtkomstbegäran

När en begär ande skickar en åtkomstbegäran för ett Access-paket som kon figurer ATS för att kräva godkännande får alla god kännare som läggs till i principen ett e-postmeddelande med information om begäran. Informationen i e-postmeddelandet inkluderar: beställarens namn organisation och affärs motivering. och den begärda start-och slutdatumet för åtkomst (om det finns). Informationen kommer även att innehålla när begäran skickades och när begäran upphör att gälla.

E-postmeddelandet innehåller en länk god kännare kan klicka på för att gå till min åtkomst för att godkänna eller neka åtkomst förfrågan. Här är ett exempel på ett e-postmeddelande som skickas till den första god kännaren eller den andra god kännaren (om godkännande med två steg är aktiverat) för att slutföra en åtkomstbegäran:

![Godkänn begäran om åtkomst till paket e-post](./media/entitlement-management-shared/approver-request-email.png)

God kännare kan också få en påminnelse via e-post. E-postmeddelandet ber god kännaren att fatta ett beslut på begäran. Här är ett exempel på ett e-postmeddelande som god kännaren får för att påminna dem om att vidta åtgärder:

![Påminnelse-e-postmeddelande om åtkomstbegäran](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approvers-request-emails"></a>Alternativa god kännare begär e-postmeddelanden

Om inställningen för alternativa god kännare är aktive rad och begäran fortfarande väntar, kommer den att vidarebefordras. Alternativa god kännare får ett e-postmeddelande för att godkänna eller neka begäran. Du kan aktivera alternativa god kännare i steg-1 och steg 2. Här är ett exempel på ett meddelande om att alternativa god kännare får:

![Alternativa god kännare begär e-post](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

Både god kännaren och de alternativa god kännarna kan godkänna eller neka begäran.

### <a name="approved-or-denied-emails"></a>Godkända eller nekade e-postmeddelanden

 När en god kännare får en åtkomstbegäran som skickats av en begär Ande, kan han eller hon godkänna eller neka åtkomst förfrågan. God kännaren måste lägga till en affärs motivering för sitt beslut. Här är ett exempel på ett e-postmeddelande som skickas till god kännare och alternativa god kännare när en begäran har godkänts:

![Godkänd begäran om åtkomst till paket e-post](./media/entitlement-management-process/approver-request-email-approved.png)

När en åtkomstbegäran godkänns, och deras åtkomst är etablerad, skickas ett e-postmeddelande till den begär Ande som nu har åtkomst till Access-paketet. Här är ett exempel på ett e-postmeddelande som skickas till en beställare när de beviljas åtkomst till ett Access-paket:

![Godkänd e-postbegäran om åtkomstbegäran](./media/entitlement-management-process/requestor-email-approved.png)

När en åtkomstbegäran nekas skickas ett e-postmeddelande till beställaren. Här är ett exempel på ett e-postmeddelande som skickas till en beställare när deras åtkomstbegäran nekas:

![Begär ande nekad e-post](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="2-stage-approval-access-request-emails"></a>e-postmeddelanden för åtkomst begär Anden med 2 steg

Om ett godkännande med två steg har Aktiver ATS måste minst två god kännare godkänna begäran, en från varje steg, innan den begär Ande kan få åtkomst.

Under fasen-1 får den första god kännaren e-postmeddelandet om åtkomstbegäran och fattar ett beslut. Om de godkänner begäran får alla första god kännare och alternativa god kännare i steg-1 (om eskalering är aktiverat) ett meddelande om att steg 1 har slutförts. Här är ett exempel på ett meddelande som skickas när steg 1 är slutförd:

![e-postbegäran om 2-fas åtkomst](./media/entitlement-management-process/approver-request-email-2stage.png)

När den första eller de alternativa god kännarna godkänner begäran i steg-1 börjar Stage-2. Under fasen-2 tar den andra god kännaren emot e-postmeddelandet om åtkomstbegäran. Efter den andra god kännaren eller alternativa god kännare i steg 2 (om eskalering har Aktiver ATS) bestämmer sig för att godkänna eller neka begäran skickas e-postmeddelanden till de första och andra god kännarna, samt alla alternativa god kännare i steg-1 och steg 2, samt i beställaren.

### <a name="expired-access-request-emails"></a>Utgångna e-postbegäran

Åtkomst begär Anden kan upphöra att gälla om ingen god kännare har godkänt eller nekat begäran. 

När begäran når sitt konfigurerade förfallo datum och upphör att gälla, kan den inte längre godkännas eller nekas av god kännaren. Här är ett exempel på ett e-postmeddelande om meddelandet som skickas till den första, andra (om godkännande med två steg har Aktiver ATS) och alternativa god kännare:

![Ett e-postmeddelande om förfallo datum för god kännare](./media/entitlement-management-process/approver-request-email-expired.png)

Ett e-postmeddelande skickas också till beställaren, vilket meddelar dem att deras åtkomst förfrågan har upphört att gälla och att de behöver skicka åtkomstbegäran igen. Följande diagram visar upplevelsen av begär ande och de e-postmeddelanden som de får när de begär att utöka åtkomsten:

![Beställaren utökar åtkomst process flödet](./media/entitlement-management-process/requestor-expiration-request-flow.png) 

Här är ett exempel på ett e-postmeddelande som skickas till en beställare när deras åtkomst förfrågan har upphört att gälla:

![Begär ande e-postadress för begäran om åtkomst](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>Nästa steg

- [Begär åtkomst till ett Access-paket](entitlement-management-request-access.md)
- [Godkänn eller neka åtkomst begär Anden](entitlement-management-request-approve.md)
