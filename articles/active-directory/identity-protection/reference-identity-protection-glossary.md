---
title: Azure Active Directory Identity Protection ord lista
description: Azure Active Directory Identity Protection ord lista
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a17f52e519a84c58c9ed5b75259cc0b96d0a175
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88949182"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Azure Active Directory Identity Protection ord lista

### <a name="at-risk-user"></a>I risk (användare)
En användare med en eller flera aktiva risk identifieringar. 

### <a name="atypical-sign-in-location"></a>Ovanlig-inloggnings plats
En inloggning från en geografisk plats som inte är typisk för den specifika användaren, liknande användare eller klienten.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
En säkerhetsmodul med Azure Active Directory som ger en sammanställd vy över risk identifieringar och potentiella sårbarheter som påverkar organisationens identiteter.

### <a name="conditional-access"></a>Villkorlig åtkomst
En princip för att skydda åtkomsten till resurser. Regler för villkorlig åtkomst lagras i Azure Active Directory och utvärderas av Azure AD innan åtkomst beviljas till resursen.  Exempel regler är att begränsa åtkomsten baserat på användarens plats, enhetens hälso tillstånd eller autentiseringsmetod för användare.

### <a name="credentials"></a>Autentiseringsuppgifter
Information som innehåller identifiering och identifierings bevis som används för att få åtkomst till lokala resurser och nätverks resurser. Exempel på autentiseringsuppgifter är användar namn och lösen ord, smartkort och certifikat.

### <a name="event"></a>Händelse
En post för en aktivitet i Azure Active Directory.

### <a name="false-positive-risk-detection"></a>Falskt-positivt (risk identifiering)
En identifierings status för risker som anges manuellt av en identitets skydds användare, vilket indikerar att riskbedömningen har undersökts och flaggats felaktigt som en risk identifiering.

### <a name="identity"></a>Identitet
En person eller enhet som måste verifieras med hjälp av autentisering, baserat på kriterier som lösen ord eller certifikat.

### <a name="identity-risk-detection"></a>Identifiering av identitets risker
Azure AD-händelse som flaggats som avvikande av identitets skydd och kan tyda på att en identitet har komprometterats.

### <a name="ignored-risk-detection"></a>Ignoreras (risk identifiering)
En identifierings status för risker som anges manuellt av en identitets skydds användare, vilket indikerar att risk identifieringen stängs utan att vidta en åtgärd.

### <a name="impossible-travel-from-atypical-locations"></a>Omöjlig resa från ovanlig platser
En risk identifiering utlöses när två inloggningar för samma användare upptäcks, där minst en av dem är från en ovanlig-inloggnings plats, och där tiden mellan inloggningarna är kortare än den minsta tid det tar för att fysiskt förflytta sig mellan dessa platser.  

### <a name="investigation"></a>Undersökning
Processen för att granska aktiviteter, loggar och annan relevant information som rör en risk identifiering för att avgöra om det krävs åtgärder för att åtgärda eller minska problemet, förstå om och hur identiteten har komprometterats och förstå hur den komprometterade identiteten användes.

### <a name="leaked-credentials"></a>Läckta autentiseringsuppgifter
En risk identifiering som utlöses när aktuella användarautentiseringsuppgifter (användar namn och lösen ord) upptäcks offentligt på den mörka webben av våra forskare.

### <a name="mitigation"></a>Åtgärd
En åtgärd för att begränsa eller eliminera möjligheten för en angripare att utnyttja en komprometterad identitet eller enhet utan att återställa identiteten eller enheten till ett säkert tillstånd. En minskning löser inte tidigare risk identifieringar som är associerade med identiteten eller enheten.

### <a name="multi-factor-authentication"></a>Multifaktorautentisering
En autentiseringsmetod som kräver två eller fler autentiseringsmetoder, som kan innehålla något som användaren har, ett sådant certifikat. något som användaren känner till, t. ex. användar namn, lösen ord eller pass fraser; fysiska attribut, t. ex. ett tumavtryck, och personliga attribut, till exempel en personlig signatur.

### <a name="offline-detection"></a>Identifiering av offline
Identifiering av avvikelser och utvärdering av risken för en händelse, till exempel inloggnings försök efter faktum, för en händelse som redan har inträffat.

### <a name="policy-condition"></a>Princip villkor
En del av en säkerhets princip, som definierar entiteterna (grupper, användare, appar, enhets plattformar, enhets tillstånd, IP-intervall, klient typer) som ingår i principen eller som undantas från den.

### <a name="policy-rule"></a>Principregel
Den del av en säkerhets princip som beskriver de omständigheter som skulle utlösa principen och de åtgärder som vidtas när principen utlöses.

### <a name="prevention"></a>Prevention (Skydd)
En åtgärd för att förhindra att organisationen skadas genom missbruk av en identitet eller en enhet som misstänks vara intrång eller som är medveten om att bli komprometterad. En förebyggande åtgärd skyddar inte enheten eller identiteten och löser inte tidigare identifieringar av risker.

### <a name="privileged-user"></a>Privilegie rad (användare)
En användare som vid en risk identifiering har permanent eller temporär administratörs behörighet till en eller flera resurser i Azure Active Directory, till exempel en global administratör, fakturerings administratör, tjänst administratör, användar administratör och lösen ords administratör. 

### <a name="real-time"></a>Real tids
Se real tids identifiering.

### <a name="real-time-detection"></a>Real tids identifiering
Identifiering av avvikelser och utvärdering av risken för en händelse som inloggnings försök innan händelsen tillåts fortsätta.

### <a name="remediated-risk-detection"></a>Åtgärdad (identifiering av risker)
En status för identifiering av risker anges automatiskt av identitets skydd, vilket indikerar att risk identifieringen reparerades med hjälp av standard åtgärds åtgärden för den här typen av risk identifiering. Till exempel, när användar lösen ordet återställs, åtgärdas många risk identifieringar som indikerar att det tidigare lösen ordet komprometteras automatiskt.

### <a name="remediation"></a>Åtgärder
En åtgärd för att skydda en identitet eller en enhet som tidigare misstänkts eller var känd för att komprometteras. En reparations åtgärd återställer identiteten eller enheten till ett säkert tillstånd och löser tidigare risk identifieringar som är associerade med identiteten eller enheten.

### <a name="resolved-risk-detection"></a>Löst (identifiering av risker)
En identifierings status för risker som anges manuellt av en identitets skydds användare, vilket indikerar att användaren vidtog en lämplig reparations åtgärd utanför identitets skyddet och att identifieringen av risker bör anses vara stängd.

### <a name="risk-detection-status"></a>Riskidentifieringsstatus
En egenskap för en risk identifiering som anger om händelsen är aktiv och om den stängs, orsaken till stängningen.

### <a name="risk-detection-type"></a>Typ av risk identifiering
En kategori för risk identifiering, som anger typen av avvikelse som orsakade att händelsen ansågs vara riskfylld.

### <a name="risk-level-risk-detection"></a>Risk nivå (identifiering av risker)
En indikation (hög, medel eller låg) av risk identifieringens allvarlighets grad för att hjälpa identitets skydds användare att prioritera de åtgärder som de vidta för att minska risken för deras organisation. 

### <a name="risk-level-sign-in"></a>Risk nivå (inloggning)
En indikation (hög, medel eller låg) av sannolikheten för en speciell inloggning, som någon annan försöker använda användarens identitet.

### <a name="risk-level-user-compromise"></a>Risk nivå (användarens kompromisser)
En indikation (hög, medel eller låg) av sannolikheten för att en identitet har komprometterats.

### <a name="risk-level-vulnerability"></a>Risk nivå (sårbarhet)
En indikation (hög, medel eller låg) av säkerhets riskens allvarlighets grad för att hjälpa identitets skydds användare att prioritera de åtgärder som de vidta för att minska risken för organisationen.

### <a name="secure-identity"></a>Skydda (identitet)
Vidta reparations åtgärder, till exempel en ändring av lösen ord eller dator avbildning för att återställa en potentiellt komprometterad identitet till ett kompromisslös tillstånd.

### <a name="security-policy"></a>Säkerhetsprincip
En samling av princip regler och villkor. En princip kan tillämpas på entiteter som användare, grupper, appar, enheter, enhets plattformar, enhets tillstånd, IP-intervall och auth 2.0-klient typer. När en princip har Aktiver ATS utvärderas den när en entitet som ingår i principen utfärdas en token för en resurs.

### <a name="sign-in-v"></a>Logga in (v)
För att autentisera till en identitet i Azure Active Directory.

### <a name="sign-in-n"></a>Inloggning (n)
Processen eller åtgärden att autentisera en identitet i Azure Active Directory och händelsen som fångar upp den här åtgärden.

### <a name="sign-in-from-anonymous-ip-address"></a>Logga in från anonym IP-adress
En risk identifiering utlöses efter en lyckad inloggning från IP-adress som har identifierats som en anonym proxy-IP-adress.

### <a name="sign-in-from-infected-device"></a>Logga in från angripen enhet
En risk identifiering som utlöses när en inloggning härstammar från en IP-adress, som är känd att användas av en eller flera komprometterade enheter, som aktivt försöker kommunicera med en bot-Server.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Logga in från IP-adress med misstänkt aktivitet
En risk identifiering utlöses efter en lyckad inloggning från en IP-adress med ett stort antal misslyckade inloggnings försök över flera användar konton under en kort tids period.

### <a name="sign-in-from-unfamiliar-location"></a>Logga in från en okänd plats
En risk identifiering som utlöses när en användare har loggat in från en ny plats (IP, latitud/longitud och ASN).

### <a name="sign-in-risk"></a>Inloggningsrisk
Se risk nivå (inloggning)

### <a name="sign-in-risk-policy"></a>Princip för inloggningsrisk
En princip för villkorlig åtkomst som utvärderar risken för en särskilt inloggning och tillämpar åtgärder baserat på fördefinierade villkor och regler.

### <a name="user-compromise-risk"></a>Risk för användar kompromisser
Se risk nivå (användar kompromisser)

### <a name="user-risk"></a>Användarrisk
Se risk nivå (användar kompromisser).

### <a name="user-risk-policy"></a>Princip för användarrisk
En princip för villkorlig åtkomst som beaktar inloggningen och tillämpar åtgärder baserat på fördefinierade villkor och regler.

### <a name="users-flagged-for-risk"></a>Användare som har flaggats för risk
Användare som har risk identifieringar, som antingen är aktiva eller åtgärdade

### <a name="vulnerability"></a>Säkerhetsrisk
En konfiguration eller ett villkor i Azure Active Directory, vilket gör att katalogen är mottaglig för att utnyttja eller drabbas av hot.

## <a name="see-also"></a>Se även

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)