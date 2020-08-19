---
title: Vad är Azure Active Directory Identity Protection?
description: Identifiera, åtgärda, undersöka och analysera risker med Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 08/15/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.custom: contperfq1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39c9522362ca6710fae5dee5d831e63cc36a3950
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509118"
---
# <a name="what-is-identity-protection"></a>Vad är Identity Protection?

Identity Protection är ett verktyg som gör det möjligt för organisationer att utföra tre viktiga uppgifter:

- Automatisera identifiering och reparation av identitetsbaserade risker.
- Undersök risker med hjälp av data i portalen.
- Exportera risk identifierings data till tredje parts verktyg för ytterligare analys.

Identitets skydd använder den information som Microsoft har förvärvat från sin ståndpunkt i organisationer med Azure AD, konsument utrymmet med Microsoft-konton och i spel med Xbox för att skydda dina användare. Microsoft analyserar 6 500 000 000 000-signaler per dag för att identifiera och skydda kunder mot hot.

Signalerna som genereras av och matas till identitets skydd kan matas in ytterligare i verktyg som villkorlig åtkomst för att fatta åtkomst beslut eller komma tillbaka till ett SIEM-verktyg (Security information and Event Management) för ytterligare undersökningar baserat på organisationens tvingade principer.

## <a name="why-is-automation-important"></a>Varför är automatisering viktigt?

I sitt [blogg inlägg i oktober 2018](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Eight-essentials-for-hybrid-identity-3-Securing-your-identity/ba-p/275843) Alex Weinert, som leder till Microsofts identitets skydds-och skydds team, förklarar varför automatisering är så viktigt vid hantering av mängden händelser:

> Varje dag ger våra maskin inlärnings-och heuristiska system risk Poäng för 18 000 000 000 inloggnings försök för över 800 000 000 distinkta konton, 300 000 000 av vilka discernibly görs av angripare (entiteter som: kriminella aktörer, hackare).
>
> Vid det senaste året har jag ekrar på de 3 främsta attackerna i våra identitets system. Här är den senaste volymen av dessa attacker
>   
>   - **Risk repetition**: 4.6 BN-attacker som identifieras i maj 2018
>   - **Lösen ords spridning**: 350K i april 2018
>   - **Nätfiske**: det är svårt att kvantifiera exakt, men vi såg 23M risk händelser i mars 2018, många av dessa är Phish relaterade

## <a name="risk-detection-and-remediation"></a>Identifiering och reparation av risker

Identitets skyddet identifierar risker i följande klassificeringar:

| Typ av risk identifiering | Beskrivning |
| --- | --- |
| Ovanlig resa | Logga in från en ovanlig-plats baserat på användarens senaste inloggningar. |
| Anonym IP-adress | Logga in från en anonym IP-adress (t. ex. Tor webbläsare, Anonymizer VPN). |
| Obekanta inloggningsegenskaper | Logga in med egenskaper som vi inte har sett nyligen för den angivna användaren. |
| Länkad IP-adress för skadlig kod | Logga in från en länkad IP-adress med skadlig kod |
| Läckta autentiseringsuppgifter | Den här risk identifieringen visar att användarens giltiga autentiseringsuppgifter har läckts |
| Azure AD Threat Intelligence | Microsofts interna och externa hot informations källor har identifierat ett känt angrepps mönster |

Mer information om dessa risker och hur/när de beräknas finns i artikeln, [Vad är risk](concept-identity-protection-risks.md).

Risk signalerna kan utlösa reparations åtgärder som att kräva att användarna ska kunna: utföra Azure-Multi-Factor Authentication, återställa sina lösen ord med hjälp av lösen ords återställning via självbetjäning eller blockera tills en administratör vidtar åtgärder.

## <a name="risk-investigation"></a>Riskbedömning

Administratörer kan granska identifieringar och vidta manuella åtgärder på dem om det behövs. Det finns tre viktiga rapporter som administratörer använder för att utredningar i identitets skydd:

- Riskfyllda användare
- Riskfyllda inloggningar
- Riskidentifieringar

Mer information finns i artikeln [så här gör du: Undersök risker](howto-identity-protection-investigate-risk.md).

### <a name="risk-levels"></a>Risk nivåer

Identitets skyddet kategoriserar risker i tre nivåer: låg, medel och hög. 

Även om Microsoft inte tillhandahåller detaljerad information om hur risken beräknas, säger vi att varje nivå ger högre förtroende för att användaren eller inloggningen ska komprometteras. Till exempel kanske något som liknar en instans av okända inloggnings egenskaper för en användare inte är lika hotade som läckage av autentiseringsuppgifter för en annan användare.

## <a name="exporting-risk-data"></a>Exportera risk data

Data från identitets skydd kan exporteras till andra verktyg för arkivering och ytterligare undersökning och korrelation. Microsoft Graph baserade API: er gör det möjligt för organisationer att samla in data för vidare bearbetning i ett verktyg som deras SIEM. Information om hur du kommer åt Identity Protection-API: et finns i artikeln, [Kom igång med Azure Active Directory Identity Protection och Microsoft Graph](howto-identity-protection-graph-api.md)

Information om hur du integrerar identitets skydds information med Azure Sentinel finns i artikeln [koppla data från Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md).

## <a name="permissions"></a>Behörigheter

Identitets skydd kräver att användare är en säkerhets läsare, säkerhets operatör, säkerhets administratör, global läsare eller global administratör för att få åtkomst.

| Roll | Kan göra | Kan inte göra |
| --- | --- | --- |
| Global administratör | Fullständig åtkomst till identitets skydd |   |
| Säkerhetsadministratör | Fullständig åtkomst till identitets skydd | Återställ lösen ord för en användare |
| Säkerhets operatör | Visa alla identitets skydds rapporter och översikts blad <br><br> Ignorera användar risk, bekräfta säker inloggning, bekräfta kompromettera | Konfigurera eller ändra principer <br><br> Återställ lösen ord för en användare <br><br> Konfigurera varningar |
| Säkerhetsläsare | Visa alla identitets skydds rapporter och översikts blad | Konfigurera eller ändra principer <br><br> Återställ lösen ord för en användare <br><br> Konfigurera varningar <br><br> Ge feedback om identifieringar |

För närvarande går det inte att komma åt rapporten över riskfyllda inloggningar i rollen säkerhets operatör.

Administratörer för villkorlig åtkomst kan också skapa principer som faktorr i inloggnings risker som ett villkor. mer information finns i artikeln [villkorlig åtkomst: villkor](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk).

## <a name="license-requirements"></a>Licenskrav

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| Kapacitet | Information | Azure AD Premium P2 | Azure AD Premium P1 | Azure AD Free/Office 365-appar |
| --- | --- | --- | --- | --- |
| Risk principer | Användar risk princip (via identitets skydd) | Ja | Nej | Nej |
| Risk principer | Inloggnings risk princip (via identitets skydd eller villkorlig åtkomst) | Ja | Nej | Nej |
| Säkerhetsrapporter | Översikt | Ja | Nej | Nej |
| Säkerhetsrapporter | Riskfyllda användare | Fullständig åtkomst | Begränsad information | Begränsad information |
| Säkerhetsrapporter | Riskfyllda inloggningar | Fullständig åtkomst | Begränsad information | Begränsad information |
| Säkerhetsrapporter | Riskidentifieringar | Fullständig åtkomst | Begränsad information | Nej |
| Meddelanden | Varningar för användare med risk identifiering | Ja | Nej | Nej |
| Meddelanden | Vecko sammandrag | Ja | Nej | Nej |
| | Registreringsprincip för multifaktorautentisering | Ja | Nej | Nej |

## <a name="next-steps"></a>Nästa steg

- [Säkerhetsöversikt](concept-identity-protection-security-overview.md)

- [Vad är risk?](concept-identity-protection-risks.md)

- [Principer som är tillgängliga för att minimera risker](concept-identity-protection-policies.md)
