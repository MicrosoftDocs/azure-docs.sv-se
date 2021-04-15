---
title: Vad är risker? Azure AD Identity Protection
description: Förklara risken i Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87516ddcce32ab205b13139c057a2ab999146b74
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376366"
---
# <a name="what-is-risk"></a>Vad är risker?

Riskidentifiering i Azure AD Identity Protection omfattar alla identifierade misstänkta åtgärder relaterade till användarkonton i katalogen.

Identity Protection ger organisationer åtkomst till kraftfulla resurser för att se och svara snabbt på dessa misstänkta åtgärder. 

![Säkerhetsöversikt som visar riskfyllda användare och inloggningar](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

> [!NOTE]
> Identity Protection genererar endast riskidentifiering när rätt autentiseringsuppgifter används. Om felaktiga autentiseringsuppgifter används vid inloggning innebär det inte risk för att autentiseringsuppgifter komprometterar.

## <a name="risk-types-and-detection"></a>Risktyper och identifiering

Det finns två typer av **riskanvändare** och **inloggning och** två typer av identifiering eller beräkning **i realtid** och **offline**.

Realtidsidentifieringarna kanske inte visas i rapporteringen på fem till tio minuter. Offlineidentifieringarna kanske inte visas i rapporteringen på två till tjugofyra timmar.

### <a name="user-risk"></a>Användarrisk

En användarrisk representerar sannolikheten att en viss identitet eller ett konto komprometteras. 

Dessa risker beräknas offline med hjälp av Microsofts interna och externa hotinformationskällor, inklusive säkerhetsforskare, polis och säkerhetsåtgärder, säkerhetsteam på Microsoft och andra betrodda källor.

| Riskidentifiering | Beskrivning |
| --- | --- |
| Läckta autentiseringsuppgifter | Den här riskidentifieringstypen anger att användarens giltiga autentiseringsuppgifter har läckts. När cyberbrott komprometterar giltiga lösenord för legitima användare delar de ofta dessa autentiseringsuppgifter. Den här delningen görs vanligtvis genom att publicera offentligt på den mörka webben, klistra in webbplatser eller genom att byta och sälja autentiseringsuppgifterna på den svarta marknaden. När Microsofts tjänst för läckta autentiseringsuppgifter hämtar användarautentiseringsuppgifter från den mörka webben, klistrar in webbplatser eller andra källor, kontrolleras de mot Azure AD-användarnas aktuella giltiga autentiseringsuppgifter för att hitta giltiga matchningar. Mer information om läckta autentiseringsuppgifter finns i [Vanliga frågor.](#common-questions) |
| Azure AD-hotinformation | Den här riskidentifieringstypen anger användaraktivitet som är ovanlig för den angivna användaren eller som är konsekvent med kända angreppsmönster baserat på Microsofts interna och externa hotinformationskällor. |

### <a name="sign-in-risk"></a>Inloggningsrisk

En inloggningsrisk representerar sannolikheten att en viss autentiseringsbegäran inte auktoriserats av identitetsägaren. 

Dessa risker kan beräknas i realtid eller beräknas offline med hjälp av Microsofts interna och externa källor för hotinformation, inklusive säkerhetsforskare, polis och rättstekniker, säkerhetsteam på Microsoft och andra betrodda källor.

| Riskidentifiering | Identifieringstyp | Beskrivning |
| --- | --- | --- |
| Anonym IP-adress | Realtid | Den här riskidentifieringstypen anger inloggningar från en anonym IP-adress (till exempel Tor-webbläsare eller anonym VPN). Dessa IP-adresser används vanligtvis av aktörer som vill dölja sin inloggningtelemetri (IP-adress, plats, enhet osv.) för potentiellt skadlig avsikt. |
| Ovanlig resa | Offline | Den här riskidentifieringstypen identifierar två inloggningar som kommer från geografiskt avlägsna platser, där minst en av platserna också kan vara ovanliga för användaren, givet tidigare beteende. Bland flera andra faktorer tar den här maskininlärningsalgoritmen hänsyn till tiden mellan de två inloggningarna och den tid det skulle ha tagit för användaren att resa från den första platsen till den andra, vilket indikerar att en annan användare använder samma autentiseringsuppgifter. <br><br> Algoritmen ignorerar uppenbara "falska positiva resultat" som bidrar till de omöjliga resevillkoren, till exempel VPN:er och platser som regelbundet används av andra användare i organisationen. Systemet har en inledande inlärningsperiod på de tidigaste 14 dagarna eller 10 inloggningar, under vilken den lär sig en ny användares inloggningsbeteende. |
| IP-adress länkad till skadlig kod | Offline | Den här riskidentifieringstypen anger inloggningar från IP-adresser som har infekterats av skadlig kod och som aktivt kommunicerar med en robotserver. Den här identifieringen bestäms genom korrelering av IP-adresser för användarens enhet mot IP-adresser som var i kontakt med en robotserver medan robotservern var aktiv. |
| Obekanta inloggningsegenskaper | Realtid | Den här riskidentifieringstypen tar hänsyn till tidigare inloggningshistorik (IP, latitud/longitud och ASN) för att leta efter avvikande inloggningar. Systemet lagrar information om tidigare platser som används av en användare och tar hänsyn till dessa "bekanta" platser. Riskidentifieringen utlöses när inloggningen sker från en plats som inte redan finns i listan över välbekanta platser. Nyligen skapade användare kommer att vara i "inlärningsläge" under en tidsperiod då okända inloggningsegenskaper inaktiveras medan våra algoritmer lär sig användarens beteende. Inlärningslägets varaktighet är dynamisk och beror på hur lång tid det tar för algoritmen att samla in tillräckligt med information om användarens inloggningsmönster. Den minsta varaktigheten är fem dagar. En användare kan gå tillbaka till inlärningsläget efter en lång tids inaktivitet. Systemet ignorerar även inloggningar från välbekanta enheter och platser som är geografiskt nära en bekant plats. <br><br> Vi kör även den här identifieringen för grundläggande autentisering (eller äldre protokoll). Eftersom dessa protokoll inte har moderna egenskaper, till exempel klient-ID, finns det begränsad telemetri för att minska falska positiva identifieringar. Vi rekommenderar att våra kunder flyttar till modern autentisering. |
| Administratörsbekräftad komprometterad användare | Offline | Den här identifieringen anger att en administratör har valt "Bekräfta att användaren har komprometterats" i användargränssnittet för riskfyllda användare eller använder API:et riskyUsers. Om du vill se vilken administratör som har bekräftat att den här användaren har komprometterats kontrollerar du användarens riskhistorik (via användargränssnitt eller API). |
| Skadlig IP-adress | Offline | Den här identifieringen anger inloggning från en skadlig IP-adress. En IP-adress betraktas som skadlig baserat på höga felfrekvenser på grund av ogiltiga autentiseringsuppgifter som tagits emot från IP-adressen eller andra IP-rykteskällor. |
| Misstänkta regler för inkorgsmanipulering | Offline | Den här identifieringen identifieras [av Microsoft Cloud App Security (MCAS).](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules) Den här identifieringen profilerar din miljö och utlöser aviseringar när misstänkta regler som tar bort eller flyttar meddelanden eller mappar har angetts på en användares inkorg. Den här identifieringen kan indikera att användarkontot har komprometterats, att meddelanden avsiktligt döljs och att postlådan används för att distribuera skräppost eller skadlig kod i din organisation. |
| Lösenordsattack | Offline | En attack med lösenordsattacker är när flera användarnamn attackeras med vanliga lösenord på ett enhetligt råstyrt sätt för att få obehörig åtkomst. Den här riskidentifiering utlöses när en attack med lösenordsattacker har utförts. |
| Omöjlig resa | Offline | Den här identifieringen identifieras [av Microsoft Cloud App Security (MCAS).](/cloud-app-security/anomaly-detection-policy#impossible-travel) Den här identifieringen identifierar två användaraktiviteter (en eller flera sessioner) som kommer från geografiskt avlägsna platser inom en kortare tidsperiod än den tid det skulle ha tagit för användaren att resa från den första platsen till den andra, vilket indikerar att en annan användare använder samma autentiseringsuppgifter. |
| Nytt land | Offline | Den här identifieringen identifieras [av Microsoft Cloud App Security (MCAS).](/cloud-app-security/anomaly-detection-policy#activity-from-infrequent-country) Den här identifieringen tar hänsyn till tidigare aktivitetsplatser för att fastställa nya och ovanliga platser. Avvikelseidentifieringsmotorn lagrar information om tidigare platser som används av användare i organisationen. |
| Aktivitet från anonym IP-adress | Offline | Den här identifieringen identifieras [av Microsoft Cloud App Security (MCAS).](/cloud-app-security/anomaly-detection-policy#activity-from-anonymous-ip-addresses) Den här identifieringen identifierar att användare var aktiva från en IP-adress som har identifierats som en anonym proxy-IP-adress. |
| Misstänkt vidarebefordring i inkorgen | Offline | Den här identifieringen identifieras [av Microsoft Cloud App Security (MCAS).](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-forwarding) Den här identifieringen söker efter misstänkta regler för vidarebefordran av e-post, till exempel om en användare har skapat en inkorgsregel som vidarebefordrar en kopia av alla e-postmeddelanden till en extern adress. |

### <a name="other-risk-detections"></a>Andra riskidentifieringar

| Riskidentifiering | Identifieringstyp | Beskrivning |
| --- | --- | --- |
| Ytterligare risk identifierad | Realtid eller offline | Den här identifieringen anger att någon av ovanstående premiumidentifieringar har identifierats. Eftersom premiumidentifieringarna endast visas för Azure AD Premium P2-kunder får de titeln "ytterligare risk har identifierats" för kunder utan Azure AD Premium P2-licenser. |

## <a name="common-questions"></a>Vanliga frågor

### <a name="risk-levels"></a>Risknivåer

Identity Protection kategoriserar risker i tre nivåer: låg, medel och hög. När du [konfigurerar anpassade identitetsskyddsprinciper](./concept-identity-protection-policies.md#custom-conditional-access-policy)kan du också konfigurera den så att den utlöses **på ingen risknivå.** Ingen risk innebär att det inte finns någon aktiv indikation på att användarens identitet har komprometterats.

Även om Microsoft inte ger specifik information om hur risker beräknas, kommer vi att säga att varje nivå ger högre förtroende för att användaren eller inloggningen komprometteras. Till exempel kanske något som liknar en instans av okända inloggningsegenskaper för en användare inte är lika hotande som att autentiseringsuppgifter har läckts för en annan användare.

### <a name="password-hash-synchronization"></a>Synkronisering av lösenordshash

Riskidentifiering, till exempel läckta autentiseringsuppgifter, kräver att det finns lösenordshashar för att identifieringen ska ske. Mer information om synkronisering av lösenordshashar finns i artikeln [Implementera synkronisering av lösenordshashar Azure AD Connect synkronisering.](../hybrid/how-to-connect-password-hash-synchronization.md)

### <a name="leaked-credentials"></a>Läckta autentiseringsuppgifter

#### <a name="where-does-microsoft-find-leaked-credentials"></a>Var hittar Microsoft läckta autentiseringsuppgifter?

Microsoft hittar läckta autentiseringsuppgifter på en mängd olika platser, inklusive:

- Offentliga inklistringswebbplatser som pastebin.com och paste.ca där dåliga aktörer vanligtvis publicerar sådant material. Den här platsen är den mest dåliga aktörens första stopp på sin jakt efter stulna autentiseringsuppgifter.
- Polis och rättsvårdande myndigheter.
- Andra grupper på Microsoft utför mörk webbforskning.

#### <a name="why-arent-i-seeing-any-leaked-credentials"></a>Varför ser jag inga läckta autentiseringsuppgifter?

Läckta autentiseringsuppgifter bearbetas varje gång Microsoft hittar en ny offentligt tillgänglig batch. På grund av den känsliga naturen raderas de läckta autentiseringsuppgifterna strax efter bearbetningen. Endast nya läckta autentiseringsuppgifter som hittas när du aktiverar synkronisering av lösenordshashar (PHS) bearbetas mot din klientorganisation. Verifiering mot tidigare hittade autentiseringspar utförs inte. 

#### <a name="i-havent-seen-any-leaked-credential-risk-events-for-quite-some-time"></a>Jag har inte sett några läckta riskhändelser för autentiseringsuppgifter på ett tag?

Om du inte har sett några läckta riskhändelser för autentiseringsuppgifter beror det på följande orsaker:

- Du har inte AKTIVERAT PHS för din klientorganisation.
- Microsoft har inte hittat några läckta autentiseringspar som matchar dina användare.

#### <a name="how-often-does-microsoft-process-new-credentials"></a>Hur ofta bearbetar Microsoft nya autentiseringsuppgifter?

Autentiseringsuppgifter bearbetas omedelbart efter att de har hittats, vanligtvis i flera batchar per dag.

## <a name="next-steps"></a>Nästa steg

- [Principer som är tillgängliga för att minimera risker](concept-identity-protection-policies.md)
- [Säkerhetsöversikt](concept-identity-protection-security-overview.md)