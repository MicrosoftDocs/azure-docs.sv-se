---
title: Lösen ords återsignering med Microsoft Authenticator app-Azure Active Directory
description: Aktivera lösen ords lös inloggning till Azure AD med hjälp av Microsoft Authenticator-appen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/11/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51e6cd7efcd0e851c15975aba5ff9b99c615eb7d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653481"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app"></a>Aktivera inloggning med lösen ord med Microsoft Authenticator-appen 

Microsoft Authenticator-appen kan användas för att logga in på ett Azure AD-konto utan att använda ett lösen ord. Microsoft Authenticator använder nyckelbaserad autentisering för att aktivera en användares autentiseringsuppgifter som är knutna till en enhet, där enheten använder en PIN-kod eller bio metrisk. [Windows Hello för företag](/windows/security/identity-protection/hello-for-business/hello-identity-verification) använder en liknande teknik.

Denna teknik för autentisering kan användas på alla enhets plattformar, inklusive Mobile. Den här tekniken kan också användas med alla appar eller webbplatser som integreras med Microsoft Authentication Libraries.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png" alt-text="Exempel på en webb läsar inloggning som ber användaren att godkänna inloggningen.":::

Användare som har aktiverat telefonin loggning från Microsoft Authenticator appen ser ett meddelande som uppmanar dem att trycka på ett nummer i appen. Inget användar namn eller lösen ord efter frågas. För att slutföra inloggnings processen i appen måste en användare härnäst vidta följande åtgärder:

1. Matcha talet.
2. Välj **Godkänn**.
3. Ange PIN-kod eller bio metriskt.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda lösen ords lös inloggning med Microsoft Authenticator-appen måste följande krav vara uppfyllda:

- Azure AD Multi-Factor Authentication, med push-meddelanden som är tillåtna som en verifieringsmetod.
- Den senaste versionen av Microsoft Authenticator installerad på enheter som kör iOS 8,0 eller senare, eller Android 6,0 eller senare.

> [!NOTE]
> Om du har aktiverat Microsoft Authenticator lösen ords löst inloggning med hjälp av Azure AD PowerShell var det aktiverat för hela katalogen. Om du aktiverar med den här nya metoden ersätter den PowerShell-principen. Vi rekommenderar att du aktiverar för alla användare i din klient via menyn nya *autentiseringsmetoder* , annars kommer användare som inte är i den nya principen inte längre att kunna logga in utan lösen ord.

## <a name="enable-passwordless-authentication-methods"></a>Aktivera metoder för lösen ords kryptering

Om du vill använda lösenordsbaserad autentisering i Azure AD måste du först aktivera den kombinerade registrerings upplevelsen och sedan Aktivera användare för metoden lösen ord mindre.

### <a name="enable-the-combined-registration-experience"></a>Aktivera kombinerad registrerings upplevelse

Registrerings funktioner för metoder för lösen ords skydd förlitar sig på den kombinerade registrerings funktionen. Om du vill låta användarna slutföra den kombinerade registreringen själva följer du stegen för att [Aktivera kombinerad registrering av säkerhets information](howto-registration-mfa-sspr-combined.md).

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Aktivera autentiseringsmetoder för inloggning med lösen ord

Med Azure AD kan du välja vilka autentiseringsmetoder som ska användas under inloggnings processen. Användarna registrerar sig sedan för de metoder som de vill använda.

Utför följande steg för att aktivera autentiseringsmetoden för inloggning med lösen ord utan lösen ord:

1. Logga in på [Azure Portal](https://portal.azure.com) med ett *globalt administratörs* konto.
1. Sök efter och välj *Azure Active Directory* och bläddra sedan till principer för **säkerhets**  >  **autentisering**  >  .
1. Under **Microsoft Authenticator** väljer du följande alternativ:
   1. **Aktivera** – Ja eller Nej
   1. **Mål** – alla användare eller Välj användare
1. Varje tillagd grupp eller användare är aktive rad som standard för att använda Microsoft Authenticator i både lösen ords lös och push-meddelande läge ("valfritt"). För att ändra detta, för varje rad:
   1. Bläddra till **...**  >  **Konfigurera**.
   1. För **autentiseringsläge** – alla, lösen ords fritt eller push
1. Välj **Spara** om du vill tillämpa den nya principen.

## <a name="user-registration-and-management-of-microsoft-authenticator"></a>Användar registrering och hantering av Microsoft Authenticator

Användarna registrerar sig själva för den lösenordsskyddade autentiseringsmetoden i Azure AD med hjälp av följande steg:

1. Bläddra till [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo).
1. Logga in och Lägg sedan till Authenticator-appen genom att välja **Lägg till metod > Authenticator-app** och sedan **lägga till**.
1. Följ anvisningarna för att installera och konfigurera appen Microsoft Authenticator på enheten.
1. Välj **klar** för att slutföra konfigurationen av autentisering.
1. I **Microsoft Authenticator** väljer du **Aktivera telefonin loggning** på den nedrullningsbara menyn för det konto som har registrerats.
1. Följ instruktionerna i appen för att slutföra registreringen av kontot för inloggning med lösen ord.

En organisation kan dirigera sina användare att logga in med sina telefoner utan att använda ett lösen ord. Mer hjälp om hur du konfigurerar Microsoft Authenticator-appen och aktiverar telefonin loggning finns i [Logga in på dina konton med hjälp av Microsoft Authenticator-appen](../user-help/user-help-auth-app-sign-in.md).

> [!NOTE]
> Användare som inte tillåts enligt principen att använda telefonin loggning kan inte längre aktivera det i Microsoft Authenticator-appen.

## <a name="sign-in-with-passwordless-credential"></a>Logga in med autentiseringsuppgifter för lösen ord

En användare kan börja använda lösen ords lös inloggning när alla följande åtgärder har slutförts:

- En administratör har aktiverat användarens klient organisation.
- Användaren har uppdaterat sin Microsoft Authenticator-app för att aktivera telefonin loggning.

Första gången en användare startar telefon inloggnings processen utför användaren följande steg:

1. Anger hennes namn på inloggnings sidan.
2. Väljer **Nästa**.
3. Vid behov kan **du välja andra sätt att logga in på**.
4. Väljer **Godkänn en begäran på min Microsoft Authenticator-app**.

Användaren visas sedan med ett tal. Appen meddelar användaren att autentisering genom att välja rätt nummer, i stället för att ange ett lösen ord.

När användaren har använt inloggning med lösen ord fortsätter appen att vägleda användaren genom den här metoden. Användaren kommer dock att se alternativet för att välja en annan metod.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png" alt-text="Exempel på en webbläsares inloggning med hjälp av Microsoft Authenticator-appen.":::

## <a name="known-issues"></a>Kända problem

Följande kända problem finns.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>Kan inte se alternativ för telefonin loggning för lösen ord

I ett scenario kan en användare ha en svars bara inloggnings verifiering som väntar på lösen ord. Användaren kan ändå försöka logga in igen. När detta inträffar kan användaren bara se alternativet för att ange ett lösen ord.

Följande steg kan användas för att lösa det här scenariot:

1. Öppna Microsoft Authenticator-appen.
2. Svara på eventuella meddelande-prompter.

Sedan kan användaren fortsätta att använda lösen ords lös telefonin loggning.

### <a name="federated-accounts"></a>Federerade konton

När en användare har aktiverat autentiseringsuppgifter för lösen ord slutar processen för Azure AD-inloggningen att använda inloggnings \_ tipset. Processen påskyndar därför inte längre användaren mot en federerad inloggnings plats.

Den här logiken förhindrar vanligt vis att en användare i en hybrid klient dirigeras till Active Directory federerade tjänster (AD FS) för inloggnings verifiering. Användaren behåller dock alternativet att klicka på **Använd lösen ord i stället**.

### <a name="azure-mfa-server"></a>Azure MFA-Server

En användare kan aktive ras för Multi-Factor Authentication (MFA) via en lokal Azure MFA-Server. Användaren kan fortfarande skapa och använda ett lösen ord för en enkel inloggning med lösen ord.

Om användaren försöker uppgradera flera installationer (5 +) av Microsoft Authenticator-appen med lösen ordet för lösen ords lös inloggning, kan den här ändringen resultera i ett fel.

### <a name="device-registration"></a>Enhetsregistrering

Innan du kan skapa den här nya starka autentiseringsuppgiften finns det krav. En förutsättning är att den enhet där Microsoft Authenticator-appen installeras måste registreras i Azure AD-klienten till en enskild användare.

För närvarande kan en enhet bara registreras i en enda klient. Den här gränsen innebär att endast ett arbets-eller skol konto i Microsoft Authenticator-appen kan aktive ras för telefonin loggning.

> [!NOTE]
> Enhets registrering är inte detsamma som enhets hantering eller MDM (Mobile Device Management). Enhets registreringen associerar bara ett enhets-ID och ett användar-ID tillsammans i Azure AD-katalogen.

## <a name="next-steps"></a>Nästa steg

Information om Azure AD-autentisering och lösen ords lös metoder finns i följande artiklar:

- [Lär dig hur lösen ords ingen autentisering fungerar](concept-authentication-passwordless.md)
- [Läs mer om enhets registrering](../devices/overview.md#getting-devices-in-azure-ad)
- [Lär dig mer om Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
