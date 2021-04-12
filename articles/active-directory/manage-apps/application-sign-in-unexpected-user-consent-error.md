---
title: Ett oväntat fel inträffade vid godkännande av ett program | Microsoft Docs
description: Diskuterar fel som kan uppstå under bearbetningen av ett program och vad du kan göra med dem
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f829672f88ea848e4611000b54d9cc200bc166d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99259985"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Ett oväntat fel inträffade vid godkännande av ett program

I den här artikeln beskrivs fel som kan uppstå under bearbetningen av ett program. Om du felsöker oväntade meddelanden om medgivande som inte innehåller några fel meddelanden, se [autentiserings scenarier för Azure AD](../develop/authentication-vs-authorization.md).

Många program som integreras med Azure Active Directory måste ha behörighet att komma åt andra resurser för att kunna fungera. När dessa resurser också är integrerade med Azure Active Directory begärs behörighet att komma åt dem ofta med hjälp av det gemensamma medgivande ramverket. En medgivande-prompt visas, vilket vanligt vis inträffar första gången ett program används, men det kan också ske i en senare användning av programmet.

Vissa villkor måste vara uppfyllda för att en användare ska kunna godkänna de behörigheter som ett program kräver. Om dessa villkor inte uppfylls kan följande fel uppstå.

## <a name="requesting-not-authorized-permissions-error"></a>Begär inte behörighets fel
* **AADSTS90093:** &lt; clientAppDisplayName &gt; begär en eller flera behörigheter som du inte har behörighet att bevilja. Kontakta en administratör, som kan godkänna det här programmet åt dig.
* **AADSTS90094:** &lt; clientAppDisplayName &gt; måste ha behörighet för att få åtkomst till resurser i din organisation som bara en administratör kan bevilja. Be en administratör att bevilja behörighet till den här appen innan du använder den.

Felet uppstår när en användare som inte är en global administratör försöker använda ett program som begär behörigheter som bara en administratör kan bevilja. Det här felet kan lösas av en administratör som beviljar åtkomst till programmet för organisationens räkning.

Det här felet kan också inträffa när en användare hindras från att komma åt ett program på grund av att det finns risk för behörighets förfrågan. I det här fallet loggas även en gransknings händelse med en kategori av typen "ApplicationManagement", aktivitets typen "medgivande till program" och status orsaken "riskfylld program upptäcktes".

Ett annat scenario där det här felet kan inträffa är när användar tilldelningen krävs för programmet, men inget administratörs medgivande angavs. I det här fallet måste administratören först ge administratörs tillåtelse.   

## <a name="policy-prevents-granting-permissions-error"></a>Princip förhindrar beviljande av behörighets fel
* **AADSTS90093:** En administratör för &lt; tenantDisplayName &gt; har angett en princip som förhindrar att du beviljar &lt; namn på appen &gt; de behörigheter som begärs. Kontakta en administratör för &lt; tenantDisplayName &gt; , som kan ge behörighet till den här appen för din räkning.

Det här felet uppstår när en global administratör stänger av möjligheten för användare att samtycka till program, och en icke-administratör försöker använda ett program som kräver medgivande. Det här felet kan lösas av en administratör som beviljar åtkomst till programmet för organisationens räkning.

## <a name="intermittent-problem-error"></a>Tillfälligt problem fel
* **AADSTS90090:** Det verkar som om inloggnings processen påträffade ett tillfälligt problem med att registrera de behörigheter som du försökte bevilja till &lt; clientAppDisplayName &gt; . försök igen senare.

Det här felet indikerar att det har uppstått ett problem med en service sida. Den kan lösas genom att försöka godkänna programmet igen.

## <a name="resource-not-available-error"></a>Resurs inte tillgängligt-fel
* **AADSTS65005:** Appen &lt; clientAppDisplayName &gt; begärde behörigheter för åtkomst till en resurs- &lt; resourceAppDisplayName &gt; som inte är tillgänglig. 

Kontakta apputvecklaren.

##  <a name="resource-not-available-in-tenant-error"></a>Resursen är inte tillgänglig vid klient fel
* **AADSTS65005:** &lt; clientAppDisplayName &gt; begär åtkomst till en resurs- &lt; resourceAppDisplayName &gt; som inte är tillgänglig i din organisation &lt; tenantDisplayName &gt; . 

Se till att resursen är tillgänglig eller kontakta en administratör för &lt; tenantDisplayName &gt; .

## <a name="permissions-mismatch-error"></a>Fel vid matchning av behörigheter
* **AADSTS65005:** Appen begärde medgivande för att få åtkomst till Resource &lt; resourceAppDisplayName &gt; . Den här begäran misslyckades eftersom den inte matchar hur appen förkonfigurerades under registreringen av appen. Kontakta appens tillverkare. * *

Felen inträffar när programmet som en användare försöker godkänna för att begära åtkomst till ett resurs program som inte kan hittas i organisationens katalog. Den här situationen kan uppstå av flera olika orsaker:

-   Klient program utvecklaren har konfigurerat sitt program på ett felaktigt sätt, vilket gör att det kan begära åtkomst till en ogiltig resurs. I det här fallet måste programutvecklaren uppdatera konfigurationen av klient programmet för att lösa problemet.

-   Ett huvud namn för tjänsten som representerar mål resurs programmet finns inte i organisationen, eller fanns tidigare, men har tagits bort. För att lösa det här problemet måste ett tjänst huvud namn för resurs programmet vara etablerad i organisationen så att klient programmet kan begära behörigheter till det. Tjänstens huvud namn kan tillhandahållas på flera olika sätt, beroende på typ av program, inklusive:

    -   Skaffa en prenumeration för resurs programmet (Microsoft-publicerade program)

    -   Godkänner resurs programmet

    -   Bevilja program behörigheter via Azure Portal

    -   Lägga till programmet från Azure AD-programgalleriet

## <a name="risky-app-error-and-warning"></a>Riskfylldt program fel och varning
* **AADSTS900941:** Administratörs medgivande krävs. Appen anses vara riskfylld. (AdminConsentRequiredDueToRiskyApp)
* Den här appen kan vara riskfylld. Om du litar på den här appen ber du administratören att ge dig åtkomst.
* **AADSTS900981:** En begäran om administrativt medgivande togs emot för en riskfylld app. (AdminConsentRequestRiskyAppWarning)
* Den här appen kan vara riskfylld. Fortsätt bara om du litar på den här appen.

Båda dessa meddelanden visas när Microsoft har fastställt att medgivande förfrågan kan vara riskfylld. Bland ett antal andra faktorer kan detta inträffa om en [verifierad utgivare](../develop/publisher-verification-overview.md) inte har lagts till i appens registrering. Den första felkoden och meddelandet visas för slutanvändarna när [arbets flödet för administratörs medgivande](configure-admin-consent-workflow.md) är inaktiverat. Den andra koden och meddelandet visas för slutanvändarna när arbets flödet för administratörs medgivande är aktiverat och administratörer. 

Slutanvändare kommer inte att kunna bevilja medgivande till appar som har identifierats som riskfyllda. Administratörer kan, men bör utvärdera appen mycket noggrant och fortsätta med försiktighet. Om appen verkar misstänkt vid ytterligare granskning kan den rapporteras till Microsoft från medgivande skärmen. 

## <a name="next-steps"></a>Nästa steg 

[Appar, behörigheter och medgivande i Azure Active Directory (v1-slutpunkt)](../develop/quickstart-register-app.md)<br>

[Omfång, behörigheter och medgivande i Azure Active Directory (v 2.0-slutpunkt)](../develop/v2-permissions-and-consent.md)