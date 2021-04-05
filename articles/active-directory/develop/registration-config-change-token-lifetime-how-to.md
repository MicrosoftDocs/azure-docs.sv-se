---
title: Ändra livstid för token standard för anpassade Azure AD-appar
description: Så här uppdaterar du livs längd principer för ditt program som du utvecklar på Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2020
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ROBOTS: NOINDEX
ms.openlocfilehash: d39f378171443f028ef6b549b120b22f2a3405c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99582949"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Ändra standardvärdena för token för token för ett anpassat, utvecklat program

Den här artikeln visar hur du använder Azure AD PowerShell för att ange en livs längd princip för åtkomsttoken. Azure AD Premium tillåter utvecklare av appar och klient administratörer att konfigurera livs längden för token som utfärdas för icke-konfidentiella klienter. Livs längds principer för token anges per klient eller de resurser som nås.

> [!IMPORTANT]
> Efter den 2020 kommer klient organisationer inte längre att kunna konfigurera livstid för uppdatering och sessionstoken.  Azure Active Directory slutar att svara på befintlig konfiguration och sessionstoken i principer efter 30 januari 2021. Du kan fortfarande konfigurera livstid för åtkomsttoken efter utfasningen. Mer information finns i avsnittet [om livs längder för konfigurerbara token i Azure AD](./active-directory-configurable-token-lifetimes.md).
> Vi har implementerat [hanterings funktioner för autentisering](../conditional-access/howto-conditional-access-session-lifetime.md)   i Azure AD villkorlig åtkomst. Du kan använda den här nya funktionen för att konfigurera livstid för uppdateringstoken genom att ange inloggnings frekvens.  

Om du vill ange en livstids princip för en åtkomsttoken laddar du [Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureADPreview).
Kör kommandot **Connect-AzureAD-Confirm** .

Här är en exempel princip som kräver att användare autentiseras oftare i din webbapp. Den här principen anger livs längden för åtkomst till tjänstens huvud namn för din webbapp. Skapa principen och tilldela den till tjänstens huvud namn. Du måste också hämta ObjectId för tjänstens huvud namn.

```powershell
$policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"

$sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
```

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du konfigurerar livs längder för token som utfärdats av Azure AD, inklusive hur du ställer in livs längd för token för alla appar i din organisation, för en app med flera klienter, eller för ett särskilt tjänst huvud namn i din organisation, i [Azure AD](./active-directory-configurable-token-lifetimes.md) . 
* [Referens för Azure AD-token](./id-tokens.md)
