---
title: Metod för Microsoft Authenticator app-autentisering – Azure Active Directory
description: Lär dig mer om att använda Microsoft Authenticator-appen i Azure Active Directory för att förbättra och säkra inloggnings händelser
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fafeae02bce001d473b0ed916624046a559a795
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648806"
---
# <a name="authentication-methods-in-azure-active-directory---microsoft-authenticator-app"></a>Autentiseringsmetoder i Azure Active Directory-Microsoft Authenticator-appen

Microsoft Authenticator-appen ger dig ytterligare en säkerhets nivå för ditt Azure AD-arbets-eller skol konto eller din Microsoft-konto och är tillgänglig för [Android](https://go.microsoft.com/fwlink/?linkid=866594) och [iOS](https://go.microsoft.com/fwlink/?linkid=866594). Med Microsoft Authenticator-appen kan användare autentiseras på ett lösen ords effektivt sätt under inloggningen eller som ett ytterligare verifierings alternativ under självbetjäning för återställning av lösen ord (SSPR) eller Azure AD Multi-Factor Authentication-händelser.

Användare kan få ett meddelande via mobilappen för att godkänna eller neka eller använda Authenticator-appen för att generera en OATH-verifierings kod som kan anges i ett inloggnings gränssnitt. Om du aktiverar både en meddelande-och verifierings kod kan användare som registrerar Authenticator-appen använda någon av metoderna för att verifiera sin identitet.

Om du vill använda Authenticator-appen i en inloggnings tolk i stället för en kombination av användar namn och lösen ord, se [Aktivera lösen ords lös inloggning med Microsoft Authenticator-appen](howto-authentication-passwordless-phone.md).

> [!NOTE]
> Användarna har inte möjlighet att registrera sina mobilappar när de aktiverar SSPR. I stället kan användarna registrera sin mobilapp på [https://aka.ms/mfasetup](https://aka.ms/mfasetup) eller som en del av den kombinerade registreringen av säkerhets information på [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

## <a name="passwordless-sign-in"></a>Lösen ords avsignering

I stället för att visa en uppfråga efter ett lösen ord när du har angett ett användar namn, ser en användare som har aktiverat telefonin loggning från Microsoft Authenticator-appen ett meddelande om att trycka ett nummer i appen. När rätt nummer är markerat är inloggnings processen slutförd.

![Exempel på en webb läsar inloggning som ber användaren att godkänna inloggningen](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Den här autentiseringsmetoden ger en hög säkerhets nivå och tar bort behovet av att användaren anger ett lösen ord vid inloggning. 

Information om hur du kommer igång med lösen ords lös inloggning finns i [Aktivera lösen ords lös inloggning med Microsoft Authenticator-appen](howto-authentication-passwordless-phone.md).

## <a name="notification-through-mobile-app"></a>Meddelande via mobilapp

Authenticator-appen kan hjälpa till att förhindra obehörig åtkomst till konton och stoppa bedrägliga transaktioner genom att skicka ett meddelande till din smartphone eller surfplatta. Användare visar meddelandet, och om det är legitimt väljer du **Verifiera**. Annars kan de välja **neka**.

![Skärm bild av exempel på en webbläsares fråga om meddelandeautentisering för att slutföra inloggnings processen](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Om din organisation har personal som arbetar i eller reser till Kina, fungerar inte *meddelandet via mobilappen* på Android-enheter i landet/regionen som Google Play-tjänster (inklusive push-meddelanden) är blockerade i regionen. IOS-avisering fungerar dock. För Android-enheter ska alternativa autentiseringsmetoder göras tillgängliga för dessa användare.

## <a name="verification-code-from-mobile-app"></a>Verifierings kod från mobilapp

Authenticator-appen kan användas som en programvarubaserad token för att generera en OATH-verifierings kod. När du har angett ditt användar namn och lösen ord anger du koden från Authenticator-appen i inloggnings gränssnittet. Verifieringskoden utgör en andra form av autentisering.

Användare kan ha en kombination av upp till fem OATH-token eller Authenticator-program, till exempel Microsoft Authenticator-appen, som har kon figurer ATS för användning när som helst.

> [!WARNING]
> För att säkerställa den högsta säkerhets nivån för lösen ords återställning via självbetjäning när endast en metod krävs för återställning är en verifierings kod det enda alternativet som är tillgängligt för användarna.
>
> När två metoder krävs kan användare återställa med hjälp av ett meddelande eller en verifierings kod utöver andra aktiverade metoder.

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång med lösen ords lös inloggning finns i [Aktivera lösen ords lös inloggning med Microsoft Authenticator-appen](howto-authentication-passwordless-phone.md).

Läs mer om hur du konfigurerar autentiseringsmetoder med hjälp av [Microsoft Graph REST API beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).
