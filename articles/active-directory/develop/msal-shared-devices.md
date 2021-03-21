---
title: Översikt över delad enhets läge
titleSuffix: Microsoft identity platform | Azure
description: Lär dig mer om delad enhets läge för att aktivera enhets delning för dina Frontline-arbetare.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 9e48d8f55efa8bc4c82eaa973a6feaeb42390064
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578782"
---
# <a name="overview-of-shared-device-mode"></a>Översikt över delad enhets läge

Läget för delad enhet är en funktion i Azure Active Directory som gör att du kan skapa program som stöder Frontline-arbetare och aktivera delad enhets läge på de enheter som distribueras till dem.

>[!IMPORTANT]
> Delat enhets läge för iOS [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="what-are-frontline-workers"></a>Vad är Frontline Worker?

Frontline-anställda är detaljister, underhålls-och fält agenter, medicinsk personal och andra användare som inte sitter framför en dator eller använder företagets e-post för samarbete. I följande avsnitt beskrivs aspekter och utmaningar med stöd för Frontline-arbetskrafter, följt av en introduktion till de funktioner som tillhandahålls av Microsoft och som gör att ditt program kan användas av en organisations Frontline arbetare.

### <a name="challenges-of-supporting-frontline-workers"></a>Utmaningar med stöd för Frontline-arbetskrafter

Att aktivera Frontline Worker-arbetsflöden innehåller utmaningar som vanligt vis presenteras av vanliga informations anställda. Sådana utmaningar kan vara höga omsättnings takt och mindre bekant med en organisations kärn produktivitets verktyg. Organisationer antar olika strategier för att ge sina Frontline-arbetskrafter. Vissa använder sig av en BYOD-strategi (en egen enhet) där deras anställda använder affärsappar på sin personliga telefon, medan andra ger sina anställda till delade enheter som iPad eller Android-surfplattor.

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>Stöd för flera användare på enheter som har utformats för en användare

Eftersom mobila enheter som kör iOS eller Android har utformats för enskilda användare, optimerar de flesta program deras upplevelse för användning av en enskild användare. En del av den här optimerade upplevelsen innebär att aktivera enkel inloggning mellan program och hålla användare inloggade på sina enheter. När en användare tar bort sitt konto från ett program, anser appen vanligt vis inte en säkerhets relaterad händelse. Många appar behåller även användarens autentiseringsuppgifter för snabb inloggning. Du kan till och med ha erfarenhet av detta själv när du har tagit bort ett program från din mobila enhet och sedan installerat om det, bara för att upptäcka att du fortfarande är inloggad.

### <a name="global-sign-in-and-sign-out-sso"></a>Global inloggning och utloggning (SSO)

För att låta en organisations anställda använda sina appar i en pool med enheter som delas av de anställda måste utvecklare aktivera motsatt upplevelse. Medarbetarna bör kunna välja en enhet från poolen och utföra en enda gest för att "göra den" till deras varaktighet. I slutet av deras Skift bör de kunna utföra en annan gest för att logga ut globalt på enheten, där all personlig information och företags information tas bort, så att de kan återställas till lagringspoolen. Om en medarbetare glömmer att logga ut, ska enheten Dessutom loggas ut automatiskt i slutet av deras Skift och/eller efter en period av inaktivitet.

Azure Active Directory aktiverar de här scenarierna med en funktion som kallas **delad enhets läge**.

## <a name="introducing-shared-device-mode"></a>Introduktion till delat enhets läge

Som det nämns är delad enhets läge en funktion i Azure Active Directory som gör att du kan:

* Bygg program som stöder Frontline-arbetare
* Distribuera enheter till Frontline-arbetare och aktivera delad enhets läge

### <a name="build-applications-that-support-frontline-workers"></a>Bygg program som stöder Frontline-arbetare

Du kan stödja Frontline-arbetare i dina program med hjälp av Microsoft Authentication Library (MSAL) och [Microsoft Authenticator app](../user-help/user-help-auth-app-overview.md) för att aktivera ett enhets tillstånd som kallas *delad enhets läge*. När en enhet är i delad enhets läge ger Microsoft ditt program information så att den kan ändra dess beteende baserat på användarens tillstånd, vilket skyddar användar data.

Funktioner som stöds är:

* **Logga in en användare enhet – bred** genom alla program som stöds.
* **Logga ut en användar enhet – bred** genom alla program som stöds.
* **Fråga enhetens tillstånd** för att avgöra om programmet finns på en enhet som är i läget för delad enhet.
* **Fråga enhetens tillstånd för användaren** på enheten för att avgöra om något har ändrats sedan programmet senast användes.

Stöd för delad enhets läge bör anses vara både en säkerhets förbättring och funktions uppgradering för ditt program, och kan bidra till att öka dess införande i miljöer med hög behörighet som hälso vård och ekonomi.

Användarna är beroende av att deras data inte läcker till en annan användare. Dela enhets läge ger till gång till användbara signaler för att ange ditt program att det har skett en ändring som du ska hantera. Ditt program ansvarar för att kontrol lera status för användaren på enheten varje gång appen används, så att den tidigare användarens data rensas. Detta inkluderar om det läses in på nytt från bakgrunden vid multi-tasking. Om en användare ändras bör du se till att både den tidigare användarens data är avmarkerad och att alla cachelagrade data som visas i programmet tas bort. Vi rekommenderar att du alltid genomför en grundlig säkerhets gransknings process när du har lagt till delade enhets läges funktioner i din app.

Mer information om hur du ändrar dina program för att stödja delad enhets läge finns i avsnittet [Nästa steg](#next-steps) i slutet av den här artikeln.

### <a name="deploy-devices-to-frontline-workers-and-turn-on-shared-device-mode"></a>Distribuera enheter till Frontline-arbetare och aktivera delad enhets läge

När dina program har stöd för delad enhets läge och inkluderar nödvändiga data-och säkerhets ändringar kan du annonsera dem som användbara av Frontline-arbetarna.

En organisations enhets administratörer kan distribuera sina enheter och dina program till sina butiker och arbets platser via en lösning för hantering av mobila enheter (MDM) som Microsoft Intune. En del av etablerings processen markerar enheten som en *delad enhet*. Administratörer konfigurerar delad enhets läge genom att distribuera [Microsoft Authenticator app](../user-help/user-help-auth-app-overview.md) och ange delad enhets läge via konfigurations parametrar. När du har genomfört de här stegen kommer alla program som har stöd för delad enhets läge att använda Microsoft Authenticator programmet för att hantera dess användar tillstånd och tillhandahålla säkerhetsfunktioner för enheten och organisationen.

## <a name="next-steps"></a>Nästa steg

Vi stöder iOS-och Android-plattformar för delad enhets läge. Läs dokumentationen nedan för din plattform för att börja stödja Frontline-arbetare i dina program.

* [Stöd för delad enhets läge för iOS](msal-ios-shared-devices.md)
* [Stöd för delad enhets läge för Android](msal-android-shared-devices.md)
