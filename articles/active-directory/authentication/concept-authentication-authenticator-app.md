---
title: Microsoft Authenticator-appautentiseringsmetod – Azure Active Directory
description: Lär dig mer om Microsoft Authenticator-appen i Azure Active Directory för att förbättra och skydda inloggningshändelser
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3175b1292a7e69506b9193d1182e184e257ebda3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530499"
---
# <a name="authentication-methods-in-azure-active-directory---microsoft-authenticator-app"></a>Autentiseringsmetoder i Azure Active Directory – Microsoft Authenticator app

Appen Microsoft Authenticator ger ytterligare en säkerhetsnivå för ditt arbets- eller skolkonto i Azure AD eller ditt Microsoft-konto och är tillgänglig för [Android](https://go.microsoft.com/fwlink/?linkid=866594) [och iOS.](https://go.microsoft.com/fwlink/?linkid=866594) Med Microsoft Authenticator-appen kan användarna autentisera på ett lösenordsfritt sätt under inloggningen eller som ytterligare ett verifieringsalternativ vid självbetjäning av lösenordsåterställning (SSPR) eller Azure AD Multi-Factor Authentication-händelser.

Användare kan få ett meddelande via mobilappen för att godkänna eller neka, eller använda Authenticator-appen för att generera en OAUTH-verifieringskod som kan anges i ett inloggningsgränssnitt. Om du aktiverar både en meddelande- och verifieringskod kan användare som registrerar Authenticator-appen använda någon av metoderna för att verifiera sin identitet.

Om du vill använda Authenticator-appen vid en inloggningsuppmaning i stället för en kombination av användarnamn och lösenord kan du gå till Aktivera lösenordslös inloggning [med Microsoft Authenticator appen](howto-authentication-passwordless-phone.md).

> [!NOTE]
> Användarna har inte möjlighet att registrera sin mobilapp när de aktiverar SSPR. I stället kan användare registrera sin mobilapp på [https://aka.ms/mfasetup](https://aka.ms/mfasetup) eller som en del av den kombinerade registreringen av säkerhetsinformation på [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

## <a name="passwordless-sign-in"></a>Lösenordsfri inloggning

I stället för att se en uppmaning om ett lösenord efter att ha angett ett användarnamn ser en användare som har aktiverat telefon inloggning från Microsoft Authenticator-appen ett meddelande om att trycka på ett nummer i appen. När rätt nummer har valts är inloggningsprocessen klar.

![Exempel på en webbläsar inloggning där användaren uppmanas att godkänna inloggningen](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Den här autentiseringsmetoden ger en hög säkerhetsnivå och tar bort behovet av att användaren anger ett lösenord vid inloggningen. 

Information om hur du kommer igång med lösenordsfri inloggning finns i Aktivera [lösenordsfri inloggning med Microsoft Authenticator appen](howto-authentication-passwordless-phone.md).

## <a name="notification-through-mobile-app"></a>Meddelande via mobilapp

Authenticator-appen kan hjälpa till att förhindra obehörig åtkomst till konton och stoppa bedrägliga transaktioner genom att push-meddela din smartphone eller surfplatta. Användarna visar meddelandet och om det är giltigt väljer du **Verifiera**. Annars kan de välja **Neka**.

![Skärmbild av exempelwebbläsaren som uppmanar Authenticator-appen att slutföra inloggningsprocessen](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Om din organisation har personal som arbetar  i eller reser till Kina fungerar inte metoden Meddelande via mobilapp på Android-enheter i det landet/regionen eftersom Google Play-tjänster (inklusive push-meddelanden) blockeras i regionen. IOS-meddelanden fungerar dock. För Android-enheter bör alternativa autentiseringsmetoder göras tillgängliga för dessa användare.

## <a name="verification-code-from-mobile-app"></a>Verifieringskod från mobilapp

Authenticator-appen kan användas som en programvarutoken för att generera en OATH-verifieringskod. När du har angett ditt användarnamn och lösenord anger du koden som tillhandahålls av Authenticator-appen i inloggningsgränssnittet. Verifieringskoden utgör en andra form av autentisering.

Användare kan ha en kombination av upp till fem OATH-maskinvarutoken eller autentiseringsprogram, till exempel Microsoft Authenticator-appen, som konfigurerats för användning när som helst.

> [!WARNING]
> För att säkerställa den högsta säkerhetsnivån för självbetjäning av lösenordsåterställning när endast en metod krävs för återställning är en verifieringskod det enda alternativet som är tillgängligt för användarna.
>
> När två metoder krävs kan användare återställa med hjälp av antingen en meddelande- eller verifieringskod utöver andra aktiverade metoder.

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång med lösenordsfri inloggning finns i Aktivera [lösenordsfri inloggning med Microsoft Authenticator appen](howto-authentication-passwordless-phone.md).

Läs mer om hur du konfigurerar autentiseringsmetoder med [hjälp av Microsoft Graph REST API](/graph/api/resources/authenticationmethods-overview).
