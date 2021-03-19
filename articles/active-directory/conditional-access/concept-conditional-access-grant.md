---
title: Bevilja kontroller i princip för villkorlig åtkomst – Azure Active Directory
description: Vad är beviljande av kontroller i en princip för villkorlig åtkomst för Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d01a750948f8e3c264b9bcffdaad3ae72fa40ac0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579118"
---
# <a name="conditional-access-grant"></a>Villkorlig åtkomst: bevilja

I en princip för villkorlig åtkomst kan en administratör använda åtkomst kontroller för att antingen bevilja eller blockera åtkomst till resurser.

![Princip för villkorlig åtkomst med en beviljande kontroll som kräver Multi-Factor Authentication](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Blockera åtkomst

Block tar hänsyn till eventuella tilldelningar och förhindrar åtkomst baserat på konfiguration av villkorlig åtkomst princip.

Block är en kraftfull kontroll som bör vara wielded med lämplig kunskap. Principer med block-instruktioner kan ha oönskade sid effekter. Korrekt testning och validering är avgörande innan du aktiverar dem i stor skala. Administratörer bör använda verktyg som t. ex. [endast rapport läge för villkorlig åtkomst](concept-conditional-access-report-only.md) och [What If verktyget i villkorlig åtkomst när du](what-if-tool.md) gör ändringar.

## <a name="grant-access"></a>Bevilja åtkomst

Administratörer kan välja att tillämpa en eller flera kontroller när de beviljar åtkomst. Dessa kontroller innehåller följande alternativ: 

- [Kräv Multi-Factor Authentication (Azure AD Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)
- [Kräv att enheten är markerad som kompatibel (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Kräv hybrid Azure AD-ansluten enhet](../devices/concept-azure-ad-join-hybrid.md)
- [Kräv godkänd klientapp](app-based-conditional-access.md)
- [Kräva appskyddsprincip](app-protection-based-conditional-access.md)
- [Kräv lösen ords ändring](#require-password-change)

När administratörer väljer att kombinera de här alternativen kan de välja följande metoder:

- Kräv alla markerade kontroller (kontroll **och** kontroll)
- Kräv en av de valda kontrollerna (kontroll **eller** kontroll)

Som standard kräver villkorlig åtkomst alla valda kontroller.

### <a name="require-multi-factor-authentication"></a>Kräv Multi-Factor Authentication

Om du markerar den här kryss rutan krävs det att användarna utför Azure AD-Multi-Factor Authentication. Mer information om hur du distribuerar Azure AD Multi-Factor Authentication finns i artikeln [Planera en molnbaserad Azure AD Multi-Factor Authentication-distribution](../authentication/howto-mfa-getstarted.md).

[Windows Hello för företag](/windows/security/identity-protection/hello-for-business/hello-overview) uppfyller kraven för Multi-Factor Authentication i principer för villkorlig åtkomst. 

### <a name="require-device-to-be-marked-as-compliant"></a>Kräv att enheten ska markeras som kompatibel

Organisationer som har distribuerat Microsoft Intune kan använda den information som returneras från sina enheter för att identifiera enheter som uppfyller särskilda krav för efterlevnad. Den här policyn för efterlevnad vidarebefordras från Intune till Azure AD där villkorlig åtkomst kan fatta beslut om att bevilja eller blockera åtkomst till resurser. Mer information om efterlevnadsprinciper finns i artikeln [Ange regler på enheter för att tillåta åtkomst till resurser i din organisation med hjälp av Intune](/intune/protect/device-compliance-get-started).

En enhet kan markeras som kompatibel av Intune (för alla enhetens operativ system) eller MDM-system från tredje part för Windows 10-enheter. JAMF Pro är det enda MDM-system som stöds av tredje part. Mer information om integrering hittar du i artikeln [integrera JAMF Pro med Intune för efterlevnad](/intune/protect/conditional-access-integrate-jamf).

Enheter måste vara registrerade i Azure AD innan de kan markeras som kompatibla. Mer information om enhets registrering finns i artikeln, [Vad är en enhets identitet](../devices/overview.md).

### <a name="require-hybrid-azure-ad-joined-device"></a>Kräv hybrid Azure AD-ansluten enhet

Organisationer kan välja att använda enhets identiteten som en del av den villkorliga åtkomst principen. Organisationer kan kräva att enheterna är hybrid Azure AD-anslutna med den här kryss rutan. Mer information om enhets identiteter finns i artikeln [Vad är en enhets identitet?](../devices/overview.md).

När du använder [enhets kod OAuth-flödet](../develop/v2-oauth2-device-code.md), så stöds inte kontrollen Kräv hanterad enhets beviljande kontroll eller enhets tillstånd. Detta beror på att enheten som utför autentisering inte kan ge enhets status till enheten som tillhandahåller en kod och enhetens tillstånd i token är låst till enheten som utför autentiseringen. Använd kontrollen Kräv Multi-Factor Authentication i stället.

### <a name="require-approved-client-app"></a>Kräv godkänd klientapp

Organisationer kan kräva att ett åtkomst försök till de valda molnappar måste göras från en godkänd klient app. Dessa godkända klient program har stöd för [Intune App Protection-principer](/intune/app-protection-policy) oberoende av alla lösningar för hantering av mobila enheter (MDM).

För att kunna utnyttja denna beviljande kontroll, kräver villkorlig åtkomst att enheten registreras i Azure Active Directory som kräver att en Service Broker-app används. Service Broker-appen kan vara Microsoft Authenticator för iOS eller antingen Microsoft Authenticator eller Microsofts företags Portal för Android-enheter. Om en Service Broker-app inte är installerad på enheten när användaren försöker autentisera, omdirigeras användaren till lämpligt App Store för att installera den begärda Service Broker-appen.

Den här inställningen gäller för följande iOS-och Android-appar:

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

- Godkända klient program har stöd för funktionen för hantering av mobil program i Intune.
- **Kräv godkänd klient program** krav:
   - Stöder endast iOS och Android för enhets plattforms villkor.
   - En Broker-app krävs för att registrera enheten. Service Broker-appen kan vara Microsoft Authenticator för iOS eller antingen Microsoft Authenticator eller Microsofts företags Portal för Android-enheter.
- Villkorlig åtkomst kan inte beakta Microsoft Edge i InPrivate-läge en godkänd klient app.
- Att använda Azure AD-programproxy för att aktivera Power BI mobilapp för att ansluta till lokala Power BI-rapportserver stöds inte med principer för villkorlig åtkomst som kräver att Microsoft Power BI-appen är godkänd klient app.

Se artikeln gör så [här: Kräv godkända klient program för åtkomst till Cloud App med villkorlig åtkomst](app-based-conditional-access.md) för konfigurations exempel.

### <a name="require-app-protection-policy"></a>Kräva appskyddsprincip

I din princip för villkorlig åtkomst kan du kräva att en [Intune App Protection-princip](/intune/app-protection-policy) finns i klient programmet innan åtkomst är tillgänglig för de valda molnappar. 

För att kunna utnyttja denna beviljande kontroll, kräver villkorlig åtkomst att enheten registreras i Azure Active Directory som kräver att en Service Broker-app används. Koordinatorappen kan antingen vara Microsoft Authenticator för iOS eller Microsofts företagsportal för Android-enheter. Om en Service Broker-app inte är installerad på enheten när användaren försöker autentisera, omdirigeras användaren till App Store för att installera Service Broker-appen.

Det krävs program för att **Intune SDK: n** ska **vara implementerad** och uppfylla vissa andra krav för att stödja den här inställningen. Utvecklare som implementerar program med Intune SDK hittar mer information i SDK-dokumentationen om dessa krav.

Följande klient program har bekräftats för att stödja den här inställningen:

- Microsoft Cortana
- Microsoft Edge
- Microsoft Excel
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Word
- Flera rader för Intune
- Nio e-post & kalender

> [!NOTE]
> Microsoft Teams, Microsoft Kaizala, Microsoft Skype för företag och Microsoft Visio stöder inte beviljande av **skydds princip för appar** . Om du vill att de här apparna ska fungera använder du enbart Granting **Kräv godkända appar** . Användningen av or-satsen mellan de båda bidragen fungerar inte för de här tre programmen.

**Kommentarer**

- Appar för skydds principer för appar stöder funktionen för hantering av mobil program i Intune med princip skydd.
- **Kräv skydds princip** krav för appar:
    - Stöder endast iOS och Android för enhets plattforms villkor.
    - En Broker-app krävs för att registrera enheten. I iOS är Service Broker-appen Microsoft Authenticator och på Android är den Intune-företagsportal app.

Se artikeln [så här gör du: Kräv app Protection-princip och en godkänd klient app för åtkomst till Cloud App med villkorlig åtkomst](app-protection-based-conditional-access.md) för konfigurations exempel.

### <a name="require-password-change"></a>Kräv lösen ords ändring 

När användar risken upptäcks, med hjälp av villkor för användar risk, kan administratörer välja att användaren säkert ska kunna ändra lösen ordet med hjälp av lösen ords återställning via självbetjäning i Azure AD. Om användar risken upptäcks kan användare utföra en lösen ords återställning via självbetjäning för att själv åtgärda problemet. då stängs användar risk händelsen för att förhindra onödig brus för administratörer. 

När en användare uppmanas att ändra sina lösen ord, krävs de först för att slutföra Multi-Factor Authentication. Du bör se till att alla användare har registrerat sig för Multi-Factor Authentication, så att de är för beredda för fall då risken har identifierats för deras konto.  

> [!WARNING]
> Användare måste tidigare ha registrerats för lösen ords återställning via självbetjäning innan användaren kan utlösa principen. 

Det finns ett par begränsningar på plats när du konfigurerar en princip med hjälp av lösen ords ändrings kontrollen.  

1. Principen måste tilldelas till alla molnappar. Detta förhindrar att en angripare använder en annan app för att ändra användarens lösen ord och återställa konto risken genom att bara logga in på en annan app. 
1. Kräv lösen ords ändring kan inte användas med andra kontroller, t. ex. krav på en kompatibel enhet.  
1. Kontrollen för lösen ords ändring kan bara användas med villkoret användare och grupp tilldelning, Cloud App tilldelnings villkor (som måste anges till alla) och användar risk villkor. 

### <a name="terms-of-use"></a>Villkor för användning

Om din organisation har skapat användnings villkor kan ytterligare alternativ visas under bevilja kontroller. Med de här alternativen kan administratörer kräva bekräftelse av användnings villkoren som ett villkor för att komma åt de resurser som skyddas av principen. Mer information om användnings villkoren finns i artikeln Azure Active Directory användnings [villkor](terms-of-use.md).

## <a name="next-steps"></a>Nästa steg

- [Villkorlig åtkomst: kontrol lera sessioner](concept-conditional-access-session.md)

- [Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

- [Läget Endast rapport](concept-conditional-access-report-only.md)
