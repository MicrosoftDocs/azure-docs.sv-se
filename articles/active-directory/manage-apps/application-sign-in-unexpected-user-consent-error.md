---
title: Oväntat fel vid utförande av medgivande till ett | Microsoft Docs
description: Beskriver fel som kan uppstå under medgivandeprocessen för ett program och vad du kan göra åt dem
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad216d0062928fc16b0f2226daabb6258d09063c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378133"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Oväntat fel när jag ger ett medgivande för ett program

Den här artikeln beskriver fel som kan uppstå under medgivandeprocessen för ett program. Om du felsöker oväntade frågor om medgivande som inte innehåller några felmeddelanden kan du gå till [Autentiseringsscenarier för Azure AD.](../develop/authentication-vs-authorization.md)

Många program som integreras med Azure Active Directory kräver behörighet att komma åt andra resurser för att fungera. När dessa resurser också är integrerade med Azure Active Directory begärs ofta behörighet att komma åt dem med hjälp av det gemensamma ramverket för medgivande. En fråga om medgivande visas, vilket vanligtvis inträffar första gången ett program används men kan även ske vid en efterföljande användning av programmet.

Vissa villkor måste vara sanna för att en användare ska kunna godkänna de behörigheter som ett program kräver. Om dessa villkor inte uppfylls kan följande fel inträffa.

## <a name="requesting-not-authorized-permissions-error"></a>Fel vid begäran av ej auktoriserade behörigheter
* **AADSTS90093:** &lt; clientAppDisplayName &gt; begär en eller flera behörigheter som du inte har behörighet att bevilja. Kontakta en administratör som kan samtycka till det här programmet för din räkning.
* **AADSTS90094:** &lt; clientAppDisplayName &gt; behöver behörighet att komma åt resurser i din organisation som endast en administratör kan bevilja. Be en administratör att bevilja behörighet till den här appen innan du använder den.

Det här felet uppstår när en användare som inte är global administratör försöker använda ett program som begär behörigheter som endast en administratör kan bevilja. Det här felet kan lösas genom att en administratör beviljar åtkomst till programmet för organisationens räkning.

Det här felet kan också inträffa när en användare hindras från att godkänna ett program på grund av att Microsoft upptäcker att behörighetsbegäran är riskabel. I det här fallet loggas även en granskningshändelse med kategorin "ApplicationManagement", aktivitetstypen "Medgivande till program" och statusorsaken för "riskfyllda program har identifierats".

Ett annat scenario där det här felet kan inträffa är när användartilldelningen krävs för programmet, men inget administratörsmedgivande har angetts. I det här fallet måste administratören först ge administratören tillstånd.   

## <a name="policy-prevents-granting-permissions-error"></a>Principen förhindrar behörighetsfel
* **AADSTS90093:** En administratör för tenantDisplayName har angett en princip som förhindrar att du beviljar namnet på &lt; appen de behörigheter som &gt; &lt; &gt; begärs. Kontakta en administratör för &lt; tenantDisplayName &gt; , som kan bevilja behörigheter till den här appen för din räkning.

Det här felet uppstår när en global administratör inaktiverar möjligheten för användare att godkänna program och en icke-administratör försöker använda ett program som kräver godkännande. Det här felet kan lösas av en administratör som beviljar åtkomst till programmet för organisationens räkning.

## <a name="intermittent-problem-error"></a>Tillfälligt problemfel
* **AADSTS90090:** Det verkar som inloggningsprocessen stötte på ett tillfälligt problem med att registrera de behörigheter som du försökte bevilja &lt; till clientAppDisplayName &gt; . försök igen senare.

Det här felet indikerar att ett tillfälligt problem har uppstått på tjänstsidan. Det kan lösas genom att försöka godkänna programmet igen.

## <a name="resource-not-available-error"></a>Felet Resursen är inte tillgänglig
* **AADSTS65005:** &lt;AppklientenAppDisplayName &gt; begärde behörighet att komma åt en &lt; resursresursAppDisplayName &gt; som inte är tillgänglig. 

Kontakta apputvecklaren.

##  <a name="resource-not-available-in-tenant-error"></a>Resursen är inte tillgänglig i klientorganisationsfel
* **AADSTS65005:** &lt; clientAppDisplayName begär åtkomst till en resurs &gt; &lt; resourceAppDisplayName som inte är tillgänglig i din &gt; organisation &lt; tenantDisplayName &gt; . 

Kontrollera att den här resursen är tillgänglig eller kontakta en administratör för &lt; tenantDisplayName. &gt;

## <a name="permissions-mismatch-error"></a>Felmatchade behörigheter
* **AADSTS65005:** Appen begärde medgivande för åtkomst till resursen &lt; resourceAppDisplayName &gt; . Den här begäran misslyckades eftersom den inte matchar hur appen förkonfigureras under appregistreringen. Kontakta appleverantören.**

Alla dessa fel inträffar när programmet som en användare försöker godkänna begär behörighet att komma åt ett resursprogram som inte kan hittas i organisationens katalog (klientorganisation). Den här situationen kan inträffa av flera orsaker:

-   Klientprogramutvecklaren har konfigurerat sitt program felaktigt, vilket gör att det begär åtkomst till en ogiltig resurs. I det här fallet måste programutvecklaren uppdatera klientprogrammets konfiguration för att lösa problemet.

-   Ett huvudnamn för tjänsten som representerar målresursprogrammet finns inte i organisationen eller fanns tidigare men har tagits bort. För att lösa det här problemet måste ett tjänsthuvudnamn för resursprogrammet etableras i organisationen så att klientprogrammet kan begära behörighet till det. Tjänstens huvudnamn kan etableras på flera olika sätt, beroende på typ av program, inklusive:

    -   Skaffa en prenumeration för resursprogrammet (Microsoft publicerade program)

    -   Medgivande till resursprogrammet

    -   Bevilja programbehörigheter via Azure Portal

    -   Lägga till programmet från Azure AD-programgalleriet

## <a name="risky-app-error-and-warning"></a>Riskfyllda appfel och varningar
* **AADSTS900941:** Administratörsmedgivande krävs. Appen anses vara riskabel. (AdminConsentRequiredDueToRiskyApp)
* Den här appen kan vara riskabel. Om du litar på den här appen ber du administratören att ge dig åtkomst.
* **AADSTS900981:** En begäran om administratörsmedgivande togs emot för en riskabel app. (AdminConsentRequestRiskyAppWarning)
* Den här appen kan vara riskabel. Fortsätt bara om du litar på den här appen.

Båda dessa meddelanden visas när Microsoft har fastställt att medgivandebegäran kan vara riskabel. Bland ett antal andra faktorer kan detta inträffa om en [verifierad utgivare](../develop/publisher-verification-overview.md) inte har lagts till i appregistreringen. Den första felkoden och meddelandet visas för slutanvändarna när arbetsflödet för [administratörsmedgivande är](configure-admin-consent-workflow.md) inaktiverat. Den andra koden och meddelandet visas för slutanvändarna när arbetsflödet för administratörsmedgivande är aktiverat och för administratörer. 

Slutanvändare kan inte bevilja medgivande till appar som har identifierats som riskfyllda. Administratörer kan, men bör utvärdera appen mycket noggrant och vara försiktig. Om appen verkar misstänkt vid ytterligare granskning kan den rapporteras till Microsoft från skärmen för medgivande. 

## <a name="next-steps"></a>Nästa steg 

[Appar, behörigheter och medgivande i Azure Active Directory (v1-slutpunkt)](../develop/quickstart-register-app.md)<br>

[Omfång, behörigheter och medgivande i Azure Active Directory (v2.0-slutpunkt)](../develop/v2-permissions-and-consent.md)