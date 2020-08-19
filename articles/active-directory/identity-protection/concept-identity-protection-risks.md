---
title: Vad är risker? Azure AD Identity Protection
description: Att förklara risker i Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 08/15/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c92994fee6de4c56257343af2ef418393b505ad
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88507441"
---
# <a name="what-is-risk"></a>Vad är risker?

Risk identifieringar i Azure AD Identity Protection innehåller identifierade misstänkta åtgärder relaterade till användar konton i katalogen.

Identitets skydd ger organisationer till gång till kraftfulla resurser för att kunna se och svara snabbt på dessa misstänkta åtgärder. 

![Säkerhets översikt som visar riskfyllda användare och inloggningar](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

## <a name="risk-types-and-detection"></a>Risk typer och identifiering

Det finns två typer av risk **användare** och **inloggning** och två typer av identifiering eller beräkning i **real tid** och **offline**.

### <a name="user-risk"></a>Användarrisk

En användar risk representerar sannolikheten att en specifik identitet eller ett konto har komprometterats. 

Dessa risker beräknas offline med Microsofts interna och externa hot informations källor, inklusive säkerhets forskare, lag verk ställande personal, säkerhets team på Microsoft och andra betrodda källor.

| Identifiering av risker | Beskrivning |
| --- | --- |
| Läckta autentiseringsuppgifter | Den här typen av risk identifiering anger att användarens giltiga autentiseringsuppgifter har läckts. När cyberbrottslingar kompromettera giltiga lösen ord för legitima användare delar de ofta dessa autentiseringsuppgifter. Den här delningen görs vanligt vis genom att publicera offentligt på den mörka webbplatsen, klistra in webbplatser eller genom handel och sälja autentiseringsuppgifterna på den svarta marknaden. När tjänsten Microsoft läcker autentiseringsuppgifter hämtar användarautentiseringsuppgifter från den mörka webben, klistra in webbplatser eller andra källor, kontrol leras de mot Azure AD-användares aktuella giltiga autentiseringsuppgifter för att hitta giltiga matchningar. Mer information om läckage av autentiseringsuppgifter finns i [vanliga frågor](#common-questions). |
| Azure AD Threat Intelligence | Den här typen av risk identifiering indikerar användar aktivitet som är ovanlig för den aktuella användaren eller som är konsekvent med kända angrepps mönster baserade på Microsofts interna och externa hot informations källor. |

### <a name="sign-in-risk"></a>Inloggningsrisk

En inloggnings risk representerar sannolikheten att en begäran om autentisering inte har behörighet av identitets ägaren. 

Dessa risker kan beräknas i real tid eller beräknas offline med hjälp av Microsofts interna och externa hot informations källor, till exempel säkerhets forskare, juridiska tekniker, säkerhets team på Microsoft och andra betrodda källor.

| Identifiering av risker | Identifierings typ | Beskrivning |
| --- | --- | --- |
| Anonym IP-adress | Real tids | Den här typen av risk identifiering indikerar inloggningar från en anonym IP-adress (till exempel Tor webbläsare eller anonym VPN). Dessa IP-adresser används vanligt vis av aktörer som vill dölja sin login telemetri (IP-adress, plats, enhet osv.) för potentiellt skadliga avsikter. |
| Ovanlig resa | Offline | Den här typen av risk identifiering identifierar två inloggningar som härstammar från geografiskt avlägsna platser, där minst en av platserna också kan vara ovanlig för användaren, med hänsyn till tidigare beteende. Bland flera andra faktorer tar den här Machine Learning-algoritmen hänsyn till tiden mellan de två inloggningarna och den tid det skulle ha tagit för användaren att resa från den första platsen till den andra, vilket indikerar att en annan användare använder samma autentiseringsuppgifter. <br><br> Algoritmen ignorerar uppenbara "falska positiva identifieringar" som bidrar till de omöjliga rese villkoren, till exempel VPN och platser som regelbundet används av andra användare i organisationen. Systemet har en inledande inlärnings period på tidigast 14 dagar eller 10 inloggningar, under vilken den lär sig en ny användares inloggnings beteende. |
| Länkad IP-adress för skadlig kod | Offline | Den här typen av risk identifiering indikerar inloggningar från IP-adresser som har infekterats med skadlig kod som är känt för att aktivt kommunicera med en bot-Server. Den här identifieringen bestäms genom att IP-adresserna för användarens enhet korreleras mot IP-adresser som var i kontakt med en bot-server medan bot-servern var aktiv. |
| Obekanta inloggningsegenskaper | Real tids | Den här typen av risk identifiering förväntar sig tidigare inloggnings historik (IP, latitud/longitud och ASN) för att söka efter avvikande inloggningar. Systemet lagrar information om tidigare platser som används av en användare och betraktar dessa "välkända" platser. Identifieringen av risker utlöses när inloggningen sker från en plats som inte redan finns i listan över välbekanta platser. Nyskapade användare är i "inlärnings läge" under en tids period då okända inloggnings egenskaper risk identifieringar kommer att inaktive ras medan algoritmerna lär sig användarens beteende. Varaktigheten för utbildnings läge är dynamisk och beror på hur lång tid det tar för algoritmen att samla in tillräckligt med information om användarens inloggnings mönster. Den minsta varaktigheten är fem dagar. En användare kan gå tillbaka till inlärnings läget efter en lång tids inaktivitet. Systemet ignorerar också inloggningar från välbekanta enheter och platser som är geografiskt nära en bekant plats. <br><br> Vi kör även den här identifieringen för grundläggande autentisering (eller äldre protokoll). Eftersom dessa protokoll inte har moderna egenskaper, t. ex. klient-ID, finns det begränsad telemetri för att minska antalet falska positiva identifieringar. Vi rekommenderar våra kunder att gå över till modern autentisering. |
| Administratören bekräftade att användaren har komprometterats | Offline | Den här identifieringen anger att en administratör har valt "bekräfta användare som har komprometterats" i användar gränssnittet för riskfyllda användare eller med riskyUsers-API. Om du vill se vilken administratör som har bekräftat att den här användaren har fått problem, kontrollerar du användarens risk historik (via UI eller API). |
| Skadlig IP-adress | Offline | Den här identifieringen indikerar inloggning från en skadlig IP-adress. En IP-adress betraktas som skadlig baserat på hög fel frekvens på grund av ogiltiga autentiseringsuppgifter som tagits emot från IP-adressen eller andra IP-ryktes källor. |
| Ändrings regler för misstänkt inkorg | Offline | Den här identifieringen upptäcks av [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules). Identifierings profilerna i din miljö och utlöser aviseringar när misstänkta regler som tar bort eller flyttar meddelanden eller mappar anges i en användares inkorg. Den här identifieringen kan tyda på att användarens konto har komprometterats, att meddelanden avsiktligt döljs och att post lådan används för att distribuera skräp post eller skadlig kod i din organisation. |
| Omöjlig resa | Offline | Den här identifieringen upptäcks av [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#impossible-travel). Den här identifieringen identifierar två användar aktiviteter (är en eller flera sessioner) som härstammar från geografiskt avlägsna platser inom en tids period som är kortare än den tid då den skulle ha tagit användaren att resa från den första platsen till den andra, vilket indikerar att en annan användare använder samma autentiseringsuppgifter. |

### <a name="other-risk-detections"></a>Andra risk identifieringar

| Identifiering av risker | Identifierings typ | Beskrivning |
| --- | --- | --- |
| Ytterligare risk upptäckt | I real tid eller offline | Den här identifieringen anger att ett av ovanstående Premium-identifieringar upptäcktes. Eftersom Premium-identifieringar endast är synliga för Azure AD Premium P2-kunder kallas de "ytterligare risk upptäckt" för kunder utan Azure AD Premium P2-licenser. |

## <a name="common-questions"></a>Vanliga frågor

### <a name="risk-levels"></a>Risk nivåer

Identitets skyddet kategoriserar risker i tre nivåer: låg, medel och hög. 

Även om Microsoft inte tillhandahåller detaljerad information om hur risken beräknas, säger vi att varje nivå ger högre förtroende för att användaren eller inloggningen ska komprometteras. Till exempel kanske något som liknar en instans av okända inloggnings egenskaper för en användare inte är lika hotade som läckage av autentiseringsuppgifter för en annan användare.

### <a name="leaked-credentials"></a>Läckta autentiseringsuppgifter

#### <a name="where-does-microsoft-find-leaked-credentials"></a>Var hittar Microsoft en läckande autentiseringsuppgifter?

Microsoft hittar läckta autentiseringsuppgifter på en rad olika platser, inklusive:

- Offentliga klistra in webbplatser som pastebin.com och paste.ca där dåliga aktörer vanligt vis publicerar sådant material. Den här platsen är de mest dåliga aktörerna första gången de söker efter stulna autentiseringsuppgifter.
- Juridiska myndigheter.
- Andra grupper med Microsoft gör mörk webb forskning.

#### <a name="why-arent-i-seeing-any-leaked-credentials"></a>Varför ser jag inte några läcka autentiseringsuppgifter?

Läckta autentiseringsuppgifter bearbetas när Microsoft hittar en ny offentligt tillgänglig batch. På grund av känslig natur tas läckta autentiseringsuppgifter bort strax efter bearbetningen. Det går bara att bearbeta nya läckta autentiseringsuppgifter efter att du aktiverat PHS (Password hash Synchronization) för din klient. Det går inte att verifiera mot tidigare Funna autentiseringsuppgifter. 

#### <a name="i-havent-seen-any-leaked-credential-risk-events-for-quite-some-time"></a>Jag har inte sett några läckta risk händelser för en viss tid?

Om du inte har sett några läckta autentiseringsuppgifter, beror det på följande orsaker:

- Du har inte aktiverat PHS för din klient.
- Microsoft har inte hittat några läcka autentiseringsuppgifter som matchar dina användare.

#### <a name="how-often-does-microsoft-process-new-credentials"></a>Hur ofta bearbetar Microsoft nya autentiseringsuppgifter?

Autentiseringsuppgifterna bearbetas direkt efter att de har hittats, vanligt vis i flera batchar per dag.

## <a name="next-steps"></a>Nästa steg

- [Principer som är tillgängliga för att minimera risker](concept-identity-protection-policies.md)
- [Säkerhetsöversikt](concept-identity-protection-security-overview.md)
