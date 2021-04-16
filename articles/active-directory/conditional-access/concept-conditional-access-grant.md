---
title: Bevilja kontroller i principen för villkorlig åtkomst – Azure Active Directory
description: Vad är beviljandekontroller i en princip för villkorsstyrd åtkomst i Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: bce54bb845e3085d654e3980123ef5c8a856fd98
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530193"
---
# <a name="conditional-access-grant"></a>Villkorlig åtkomst: Bevilja

I en princip för villkorlig åtkomst kan en administratör använda åtkomstkontroller för att antingen bevilja eller blockera åtkomst till resurser.

![Princip för villkorlig åtkomst med en beviljandekontroll som kräver multifaktorautentisering](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Blockera åtkomst

Blockera tar hänsyn till alla tilldelningar och förhindrar åtkomst baserat på konfigurationen av principen för villkorlig åtkomst.

Block är en kraftfull kontroll som bör ha lämplig kunskap. Principer med blocksatser kan ha oönskade sidoeffekter. Korrekt testning och validering är viktiga innan du aktiverar i stor skala. Administratörer bör använda verktyg som endast [rapportläge för villkorsstyrd åtkomst](concept-conditional-access-report-only.md) och [What If i villkorlig åtkomst när](what-if-tool.md) de gör ändringar.

## <a name="grant-access"></a>Bevilja åtkomst

Administratörer kan välja att framtvinga en eller flera kontroller när de beviljar åtkomst. Dessa kontroller innehåller följande alternativ: 

- [Kräv multifaktorautentisering (Azure AD Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)
- [Kräv att enheten är markerad som kompatibel (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Kräv Hybrid Azure AD-ansluten enhet](../devices/concept-azure-ad-join-hybrid.md)
- [Kräv godkänd klientapp](app-based-conditional-access.md)
- [Kräva appskyddsprincip](app-protection-based-conditional-access.md)
- [Kräv lösenordsändring](#require-password-change)

När administratörer väljer att kombinera dessa alternativ kan de välja följande metoder:

- Kräv alla valda kontroller (kontroll **OCH** kontroll)
- Kräv en av de valda kontrollerna (kontroll **ELLER** kontroll)

Som standard kräver villkorsstyrd åtkomst alla valda kontroller.

### <a name="require-multi-factor-authentication"></a>Kräv multifaktorautentisering

Om du markerar den här kryssrutan måste användarna utföra Azure AD Multi-Factor Authentication. Mer information om hur du distribuerar Azure AD Multi-Factor Authentication finns i artikeln Planera en molnbaserad [Azure AD Multi-Factor Authentication-distribution.](../authentication/howto-mfa-getstarted.md)

[Windows Hello för företag](/windows/security/identity-protection/hello-for-business/hello-overview) uppfyller kravet på multifaktorautentisering i principer för villkorsstyrd åtkomst. 

### <a name="require-device-to-be-marked-as-compliant"></a>Kräv att enheten är markerad som kompatibel

Organisationer som har distribuerat Microsoft Intune kan använda informationen som returneras från sina enheter för att identifiera enheter som uppfyller specifika efterlevnadskrav. Den här informationen om principefterlevnad vidarebefordras från Intune till Azure AD där villkorlig åtkomst kan fatta beslut om att bevilja eller blockera åtkomst till resurser. Mer information om efterlevnadsprinciper finns i artikeln Set rules on devices to allow access to resources in your organization using Intune (Ange regler för enheter för att tillåta åtkomst till resurser i din [organisation med intune).](/intune/protect/device-compliance-get-started)

En enhet kan markeras som kompatibel med Intune (för alla enhetsoperativsystem) eller av MDM-system från tredje part för Windows 10 enheter. Jamf Pro är det enda MDM-system från tredje part som stöds. Mer information om integrering finns i artikeln Integrera [Jamf Pro med Intune för efterlevnad.](/intune/protect/conditional-access-integrate-jamf)

Enheter måste vara registrerade i Azure AD innan de kan markeras som kompatibla. Mer information om enhetsregistrering finns i artikeln Vad [är en enhetsidentitet?](../devices/overview.md)

### <a name="require-hybrid-azure-ad-joined-device"></a>Kräv Hybrid Azure AD-ansluten enhet

Organisationer kan välja att använda enhetsidentiteten som en del av sin princip för villkorlig åtkomst. Organisationer kan kräva att enheter är Hybrid Azure AD-anslutna med hjälp av den här kryssrutan. Mer information om enhetsidentiteter finns i artikeln [Vad är en enhetsidentitet?](../devices/overview.md).

När du använder [OAuth-flödet för enhetskod](../develop/v2-oauth2-device-code.md)stöds inte beviljandekontroll för hanterad enhet eller ett villkor för enhetstillstånd. Det beror på att enheten som utför autentiseringen inte kan ge enheten sitt enhetstillstånd genom att ange en kod och enhetens tillstånd i token är låst till den enhet som utför autentiseringen. Använd grant-kontrollen kräv multifaktorautentisering i stället.

### <a name="require-approved-client-app"></a>Kräv godkänd klientapp

Organisationer kan kräva att ett åtkomstförsök till de valda molnapparna måste göras från en godkänd klientapp. Dessa godkända klientappar stöder [Intune-appskyddsprinciper oberoende](/intune/app-protection-policy) av alla MDM-lösningar (hantering av mobila enheter).

För att kunna utnyttja den här beviljandekontrollen kräver villkorsstyrd åtkomst att enheten registreras i Azure Active Directory som kräver användning av en koordinatorapp. Koordinatorappen kan vara Microsoft Authenticator för iOS, eller antingen Microsoft Authenticator eller Microsoft-företagsportalen för Android-enheter. Om en koordinatorapp inte installeras på enheten när användaren försöker autentiseras omdirigeras användaren till lämplig appbutik för att installera den koordinatorapp som krävs.

Den här inställningen gäller för följande iOS- och Android-appar:

- Microsoft Azure Information Protection
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Power Automate
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype för företag
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer
- Microsoft Whiteboard
- Microsoft 365 Admin

**Kommentarer**

- De godkända klientapparna stöder funktionen för hantering av mobilprogram i Intune.
- Krav **för Kräv godkänd klientapp:**
   - Stöder endast iOS och Android för enhetsplattformsvillkor.
   - En koordinatorapp krävs för att registrera enheten. Koordinatorappen kan vara Microsoft Authenticator för iOS, eller antingen Microsoft Authenticator eller Microsoft-företagsportalen för Android-enheter.
- Villkorlig åtkomst kan inte Microsoft Edge en godkänd klientapp i InPrivate-läge.
- Användning av Azure AD Programproxy för att göra så att Power BI-mobilappen kan ansluta till en lokal Power BI-rapportserver stöds inte med principer för villkorlig åtkomst som kräver att Microsoft Power BI-appen är en godkänd klientapp.

Se artikeln How to: Require approved client apps for cloud app access with Conditional Access (Så här gör du: Kräv godkända klientappar för åtkomst [till molnappar med villkorsstyrd åtkomst)](app-based-conditional-access.md) för konfigurationsexempel.

### <a name="require-app-protection-policy"></a>Kräva appskyddsprincip

I din princip för villkorlig åtkomst kan du kräva att en [Intune-appskyddsprincip](/intune/app-protection-policy) finns i klientappen innan åtkomst är tillgänglig för de valda molnapparna. 

För att kunna utnyttja den här beviljandekontrollen kräver villkorsstyrd åtkomst att enheten registreras i Azure Active Directory som kräver användning av en koordinatorapp. Koordinatorappen kan antingen vara Microsoft Authenticator för iOS eller Microsofts företagsportal för Android-enheter. Om en koordinatorapp inte installeras på enheten när användaren försöker autentiseras omdirigeras användaren till App Store för att installera koordinatorappen.

Program måste ha **Intune SDK med** **Policy Assurance implementerat** och uppfylla vissa andra krav för att stödja den här inställningen. Utvecklare som implementerar program med Intune SDK kan hitta mer information i SDK-dokumentationen om dessa krav.

Följande klientappar har bekräftats för att stödja den här inställningen:

- Microsoft Cortana
- Microsoft Edge
- Microsoft Excel
- Microsoft-listor (iOS)
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Word
- MultiLine för Intune
- Nine Mail – e-& kalender

> [!NOTE]
> Microsoft Teams, Microsoft Kaizala, Microsoft Skype för företag och Microsoft Visio stöder inte **beviljandet av Kräv appskyddsprincip.** Om du kräver att de här apparna ska fungera kan du använda **bevilja endast Kräv** godkända appar. Användningen av or-satsen mellan de två bidragen fungerar inte för dessa tre program.

**Kommentarer**

- Appar för appskyddsprincip stöder Intunes hanteringsfunktion för mobilprogram med principskydd.
- Krav **för Kräv appskyddsprincip:**
    - Stöder endast iOS och Android för enhetsplattformsvillkor.
    - En koordinatorapp krävs för att registrera enheten. På iOS är koordinatorappen Microsoft Authenticator och på Android är den Intune-företagsportal appen.

Konfigurationsexempel finns [i artikeln How to: Require app protection policy and an approved client app for cloud app access with Conditional Access](app-protection-based-conditional-access.md) (Så här gör du: Kräv appskyddsprincip och en godkänd klientapp för åtkomst till molnapp med villkorlig åtkomst).

### <a name="require-password-change"></a>Kräv lösenordsändring 

När användarrisk identifieras kan administratörer med hjälp av villkor för användarriskprincip välja att användaren på ett säkert sätt ska ändra lösenordet med hjälp av självbetjäning av lösenordsåterställning i Azure AD. Om användarrisken upptäcks kan användarna utföra en lösenordsåterställning via självbetjäning för självbetjäning. Detta stänger användarriskhändelsen för att förhindra onödigt brus för administratörer. 

När en användare uppmanas att ändra sitt lösenord måste de först slutföra multifaktorautentisering. Du bör se till att alla användare har registrerats för multifaktorautentisering, så att de är förberedda om risk identifieras för deras konto.  

> [!WARNING]
> Användare måste tidigare ha registrerat sig för lösenordsåterställning via självbetjäning innan riskprincipen utlöses. 

Det finns en par begränsning på plats när du konfigurerar en princip med hjälp av kontrollen för lösenordsändring.  

1. Principen måste tilldelas till "alla molnappar". Detta förhindrar att en angripare använder en annan app för att ändra användarens lösenord och återställa kontorisken genom att helt enkelt logga in i en annan app. 
1. Kräv lösenordsändring kan inte användas med andra kontroller, till exempel att kräva en kompatibel enhet.  
1. Kontrollen för lösenordsändring kan bara användas med villkor för användar- och grupptilldelning, tilldelningsvillkor för molnapp (som måste anges till alla) och användarriskvillkor. 

### <a name="terms-of-use"></a>Villkor för användning

Om din organisation har skapat användningsvillkor kan ytterligare alternativ visas under beviljandekontroller. Med dessa alternativ kan administratörer kräva bekräftelse av användningsvillkor som ett villkor för åtkomst till de resurser som skyddas av principen. Mer information om användningsvillkor finns i artikeln Azure Active Directory [användningsvillkor](terms-of-use.md).

## <a name="next-steps"></a>Nästa steg

- [Villkorlig åtkomst: Sessionskontroller](concept-conditional-access-session.md)

- [Vanliga principer för villkorsstyrd åtkomst](concept-conditional-access-policy-common.md)

- [Läget Endast rapport](concept-conditional-access-report-only.md)
