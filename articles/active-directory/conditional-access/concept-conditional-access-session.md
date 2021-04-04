---
title: Sessionsnycklar i princip för villkorlig åtkomst-Azure Active Directory
description: Vad är sessions kontroller i en princip för villkorlig åtkomst i Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a0089d246169ad4215075662500794e7143940e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90601817"
---
# <a name="conditional-access-session"></a>Villkorlig åtkomst: session

I en princip för villkorlig åtkomst kan en administratör använda sessionsnycklar för att aktivera begränsade upplevelser inom vissa moln program.

![Princip för villkorlig åtkomst med en beviljande kontroll som kräver Multi-Factor Authentication](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>Tvingande begränsningar för program

Organisationer kan använda den här kontrollen för att kräva att Azure AD skickar enhets information till de valda molnappar. Med enhets informationen kan molnappar se om en anslutning initieras från en kompatibel eller domänansluten enhet. Den här kontrollen stöder endast SharePoint Online och Exchange Online som valda molnappar. När det här alternativet är markerat använder Cloud App enhets informationen för att tillhandahålla användare, beroende på enhetens tillstånd, med en begränsad eller fullständig upplevelse.

Mer information om användning och konfiguration av tvingande begränsningar för appar finns i följande artiklar:

- [Aktivera begränsad åtkomst med SharePoint Online](/sharepoint/control-access-from-unmanaged-devices)
- [Aktivera begränsad åtkomst med Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>Program kontroll för villkorlig åtkomst

Appkontroll för villkorsstyrd åtkomst använder en arkitektur med omvänd proxy och är unikt integrerat med villkorlig åtkomst i Azure AD. Med villkorlig åtkomst i Azure AD kan du genomdriva åtkomst kontroller i din organisations appar baserat på vissa villkor. Villkoren definierar vem (användare eller grupp av användare) och vad (vilka molnappar) och var (vilka platser och nätverk) en princip för villkorlig åtkomst tillämpas på. När du har fastställt villkoren kan du dirigera användare till [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) där du kan skydda data med appkontroll för villkorsstyrd åtkomst genom att använda åtkomst-och sessionsnycklar.

Appkontroll för villkorsstyrd åtkomst gör att användarens app-åtkomst och sessioner kan övervakas och kontrol leras i real tid baserat på åtkomst-och sessionstillstånd. Åtkomst-och sessionstillstånd används i Cloud App Security Portal för att ytterligare förfina filter och ange åtgärder som ska vidtas för en användare. Med åtkomst-och sessionstillstånd kan du:

- Förhindra data exfiltrering: du kan blockera nedladdning, klipp ut, kopiera och skriva ut känsliga dokument på, till exempel ohanterade enheter.
- Skydda vid nedladdning: i stället för att blockera nedladdning av känsliga dokument kan du kräva att dokument etiketteras och skyddas med Azure Information Protection. Den här åtgärden säkerställer att dokumentet är skyddat och att användar åtkomsten är begränsad i en potentiellt riskfylld session.
- Förhindra uppladdning av omärkta filer: innan en känslig fil överförs, distribueras och används av andra, är det viktigt att se till att filen har rätt etikett och skydd. Du kan se till att omärkta filer med känsligt innehåll blockeras från att överföras tills användaren klassificerar innehållet.
- Övervaka användarsessioner för kompatibilitet: riskfyllda användare övervakas när de loggar in i appar och deras åtgärder loggas in i sessionen. Du kan undersöka och analysera användar beteende för att förstå var och under vilka omständigheter som ska användas i framtiden.
- Blockera åtkomst: du kan i detalj blockera åtkomst för vissa appar och användare beroende på flera riskfaktorer. Du kan till exempel blockera dem om de använder klient certifikat som en form av enhets hantering.
- Blockera anpassade aktiviteter: vissa appar har unika scenarier som kan medföra risker, till exempel att skicka meddelanden med känsligt innehåll i appar som Microsoft Teams eller slack. I dessa typer av scenarier kan du söka igenom meddelanden för känsligt innehåll och blockera dem i real tid.

Mer information finns i artikeln [distribuera appkontroll för villkorsstyrd åtkomst för aktuella appar](/cloud-app-security/proxy-deployment-aad).

## <a name="sign-in-frequency"></a>Inloggnings frekvens

Inloggnings frekvensen definierar den tids period innan en användare uppmanas att logga in igen vid försök att komma åt en resurs.

Inställningen för inloggnings frekvens fungerar med appar som har implementerat OAUTH2-eller OIDC-protokoll enligt standarderna. De flesta inbyggda Microsoft-appar för Windows, Mac och Mobile, inklusive följande webb program, följer inställningen.

- Word, Excel, PowerPoint Online
- OneNote online
- Office.com
- Microsoft 365 admin-portalen
- Exchange Online
- SharePoint och OneDrive
- Team webb klient
- Dynamics CRM Online
- Azure Portal

Mer information finns i artikeln [Konfigurera hantering av autentisering med villkorlig åtkomst](howto-conditional-access-session-lifetime.md#user-sign-in-frequency).

## <a name="persistent-browser-session"></a>Beständig webbläsarsession

Med en beständig webbläsarsession kan användarna vara inloggade när de har stängt och öppnat sina webbläsarfönster igen.

Mer information finns i artikeln [Konfigurera hantering av autentisering med villkorlig åtkomst](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions).

## <a name="next-steps"></a>Nästa steg

- [Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

- [Läget Endast rapport](concept-conditional-access-report-only.md)
