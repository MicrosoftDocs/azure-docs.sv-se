---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 2325509f68ced7c66d9f733b07247ae01301b565
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "82181551"
---
## <a name="test-your-code"></a>Testa koden

Om du vill köra ditt projekt i Visual Studio väljer du **F5**. Ditt program **MainWindow** visas, som du ser här:

![Testa ditt program](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

Första gången du kör programmet och väljer **anrops Microsoft Graph API** -knappen uppmanas du att logga in. Använd ett Azure Active Directory konto (arbets-eller skol konto) eller ett Microsoft-konto (live.com, outlook.com) för att testa det.

![Logga in på programmet](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Ge tillstånd för program åtkomst

Första gången du loggar in på ditt program uppmanas du också att ange medgivande för att ge programmet åtkomst till din profil och logga in dig, som du ser här:

![Ge ditt medgivande för program åtkomst](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Visa program resultat

När du har loggat in bör du se den användar profil information som returneras av anropet till Microsoft Graph API. Resultaten visas i **resultat rutan resultat för API-anrop** . Grundläggande information om den token som hämtades via anropet till `AcquireTokenInteractive` eller `AcquireTokenSilent` ska visas i rutan **token-information** . Resultaten innehåller följande egenskaper:

|Egenskap  |Format  |Beskrivning |
|---------|---------|---------|
|**Användarnamn** |<span>user@domain.com</span> |Det användar namn som används för att identifiera användaren.|
|**Token upphör att gälla** |DateTime |Tiden då token upphör att gälla. MSAL utökar förfallo datumet genom att förnya token vid behov.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om omfattningar och delegerade behörigheter

Microsoft Graph-API: t kräver att *User. Read* -omfånget läser en användar profil. Det här omfånget läggs automatiskt till som standard i alla program som är registrerade i program registrerings portalen. Andra API: er för Microsoft Graph, samt anpassade API: er för backend-servern, kan kräva ytterligare omfång. Microsoft Graph-API: n kräver *kalendrar. Läs* omfattning för att lista användarens kalendrar.

Om du vill komma åt användarens kalendrar i ett programs kontext lägger du till *kalendrarna. Läs* behörighet för program registrerings informationen. Lägg sedan till *kalendrarna. Läs* omfång till `acquireTokenSilent` anropet.

>[!NOTE]
>Användaren kan tillfrågas om ytterligare medgivanden när du ökar antalet omfång.

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
