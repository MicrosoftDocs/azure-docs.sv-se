---
title: Lösenordsfri inloggning med appen Microsoft Authenticator – Azure Active Directory
description: Aktivera lösenordsfri inloggning i Azure AD med hjälp av Microsoft Authenticator appen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/21/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: d586294f101c271f139867d0046576dc9a32f076
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861795"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app"></a>Aktivera lösenordsfri inloggning med Microsoft Authenticator appen 

Appen Microsoft Authenticator kan användas för att logga in på ett Azure AD-konto utan att använda ett lösenord. Microsoft Authenticator använder nyckelbaserad autentisering för att aktivera en användares autentiseringsuppgifter som är kopplad till en enhet, där enheten använder en PIN-kod eller biometri. [Windows Hello för företag](/windows/security/identity-protection/hello-for-business/hello-identity-verification) använder en liknande teknik.

Den här autentiseringstekniken kan användas på valfri enhetsplattform, inklusive mobil. Den här tekniken kan också användas med alla appar eller webbplatser som integreras med Microsoft Authentication Libraries.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png" alt-text="Exempel på en webbläsar inloggning där användaren uppmanas att godkänna inloggningen.":::

Personer som har aktiverat telefon inloggning från Microsoft Authenticator ser ett meddelande som ber dem att trycka på ett nummer i appen. Inget användarnamn eller lösenord efterfrågas. För att slutföra inloggningsprocessen i appen måste användaren sedan vidta följande åtgärder:

1. Matcha talet.
2. Välj **Godkänn**.
3. Ange pin-kod eller biometrik.

## <a name="prerequisites"></a>Förutsättningar

Följande krav måste vara uppfyllda för att du Microsoft Authenticator använda lösenordsfri telefon med appen:

- Azure AD Multi-Factor Authentication, med push-meddelanden tillåts som en verifieringsmetod.
- Den senaste versionen Microsoft Authenticator på enheter som kör iOS 8.0 eller senare, eller Android 6.0 eller senare.
- Enheten som appen Microsoft Authenticator installerad på måste vara registrerad i Azure AD-klientorganisationen för en enskild användare. 

> [!NOTE]
> Om du Microsoft Authenticator lösenordsfri inloggning med Azure AD PowerShell har den aktiverats för hela katalogen. Om du aktiverar den här nya metoden ersätter den PowerShell-principen. Vi rekommenderar att du aktiverar för alla  användare i din klientorganisation via den nya menyn Autentiseringsmetoder, annars kan användare som inte är i den nya principen inte längre logga in utan lösenord.

## <a name="enable-passwordless-authentication-methods"></a>Aktivera lösenordsfri autentisering

Om du vill använda lösenordslös autentisering i Azure AD aktiverar du först den kombinerade registreringsupplevelsen och aktiverar sedan användare för metoden med lösenordslösenord.

### <a name="enable-the-combined-registration-experience"></a>Aktivera den kombinerade registreringsupplevelsen

Registreringsfunktioner för lösenordslösa autentiseringsmetoder förlitar sig på den kombinerade registreringsfunktionen. Om du vill låta användarna slutföra den kombinerade registreringen själva följer du stegen för [att aktivera kombinerad registrering av säkerhetsinformation.](howto-registration-mfa-sspr-combined.md)

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Aktivera autentiseringsmetoder för lösenordslös telefonautentisering

Med Azure AD kan du välja vilka autentiseringsmetoder som kan användas under inloggningsprocessen. Användarna registrerar sig sedan för de metoder som de vill använda.

Utför följande steg för att aktivera autentiseringsmetoden för inloggning via lösenordslös telefon:

1. Logga in på [Azure Portal](https://portal.azure.com) med ett globalt *administratörskonto.*
1. Sök efter och välj *Azure Active Directory* och bläddra sedan till Principer för   >  **säkerhetsautentiseringsmetoder.**  >  
1. Under **Microsoft Authenticator** väljer du följande alternativ:
   1. **Aktivera** – Ja eller Nej
   1. **Mål** – Alla användare eller Välj användare
1. Varje tillagd grupp eller användare är aktiverad som standard för att använda Microsoft Authenticator både lösenordsfritt läge och push-meddelandeläge ("Valfritt" läge). Så här ändrar du detta för varje rad:
   1. Bläddra till **...**  >  **Konfigurera**.
   1. För **autentiseringsläge** – valfritt, lösenordslöst eller push-meddelande
1. Om du vill tillämpa den nya principen väljer du **Spara**.

## <a name="user-registration-and-management-of-microsoft-authenticator"></a>Användarregistrering och hantering av Microsoft Authenticator

Användarna registrerar sig själva för autentiseringsmetoden utan lösenord i Azure AD med hjälp av följande steg:

1. Bläddra till [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo).
1. Logga in och lägg sedan till Authenticator-appen genom att välja **Lägg till metod > Authenticator-app** och sedan Lägg **till**.
1. Följ instruktionerna för att installera och konfigurera Microsoft Authenticator på enheten.
1. Välj **Klar för** att slutföra Authenticator-konfigurationen.
1. I **Microsoft Authenticator** väljer du **Aktivera telefon inloggning från** den nedrullningsna menyn för det registrerade kontot.
1. Följ instruktionerna i appen för att slutföra registreringen av kontot för lösenordsfri telefon inloggning.

En organisation kan dirigera sina användare att logga in med sina telefoner utan att använda ett lösenord. Mer hjälp med att konfigurera Microsoft Authenticator och aktivera telefon-inloggning finns i Logga in på dina konton med hjälp av [Microsoft Authenticator appen](../user-help/user-help-auth-app-sign-in.md).

> [!NOTE]
> Användare som inte tillåts av principen att använda telefon inloggning kan inte längre aktivera det i Microsoft Authenticator appen.

## <a name="sign-in-with-passwordless-credential"></a>Logga in med lösenordslösa autentiseringsuppgifter

En användare kan börja använda lösenordsfri inloggning när alla följande åtgärder har slutförts:

- En administratör har aktiverat användarens klientorganisation.
- Användaren har uppdaterat sin Microsoft Authenticator för att aktivera telefon-inloggning.

Första gången en användare startar inloggningsprocessen för telefonen utför användaren följande steg:

1. Anger sitt namn på inloggningssidan.
2. Väljer **Nästa.**
3. Om det behövs väljer du **Andra sätt att logga in på**.
4. Väljer Godkänn **en begäran på min Microsoft Authenticator app .**

Användaren får sedan ett tal. Appen uppmanar användaren att autentisera genom att välja rätt nummer, i stället för att ange ett lösenord.

När användaren har använt lösenordslös telefon inloggning fortsätter appen att vägleda användaren genom den här metoden. Användaren ser dock alternativet att välja en annan metod.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png" alt-text="Exempel på en webbläsar inloggning med hjälp av Microsoft Authenticator appen.":::

## <a name="known-issues"></a>Kända problem

Följande kända problem finns.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>Alternativet för lösenordslös telefon inloggning visas inte

I ett scenario kan en användare ha en obesvarad lösenordsfri telefon som väntar på inloggning. Användaren kan dock försöka logga in igen. När detta inträffar kanske användaren bara ser alternativet att ange ett lösenord.

För att lösa det här scenariot kan du använda följande steg:

1. Öppna Microsoft Authenticator-appen.
2. Svara på eventuella aviseringsmeddelanden.

Användaren kan sedan fortsätta att använda lösenordslös telefon inloggning.

### <a name="federated-accounts"></a>Federerade konton

När en användare har aktiverat lösenordslösa autentiseringsuppgifter slutar Azure AD-inloggningsprocessen att använda \_ inloggningstipset. Processen påskyndar därför inte längre användaren mot en federerad inloggningsplats.

Den här logiken förhindrar vanligtvis att en användare i en hybridklientorganisation dirigeras till Active Directory Federated Services (AD FS) för inloggningsverifiering. Användaren behåller dock alternativet att klicka på **Använd ditt lösenord i stället.**

### <a name="azure-mfa-server"></a>Azure MFA-server

En slutanvändare kan aktiveras för multifaktorautentisering (MFA) via en lokal Azure MFA-server. Användaren kan fortfarande skapa och använda autentiseringsuppgifter för inloggning med enkel lösenordsfri telefon.

Om användaren försöker uppgradera flera installationer (5+) av Microsoft Authenticator-appen med autentiseringsuppgifter för inloggning via lösenordslös telefon kan den här ändringen resultera i ett fel.

### <a name="device-registration"></a>Enhetsregistrering

Innan du kan skapa dessa nya starka autentiseringsuppgifter finns det förutsättningar. Ett krav är att enheten där Microsoft Authenticator är installerad måste registreras i Azure AD-klientorganisationen för en enskild användare.

För närvarande kan en enhet bara registreras i en enda klientorganisation. Den här gränsen innebär att endast ett arbets- eller skolkonto i Microsoft Authenticator kan aktiveras för telefon-inloggning.

> [!NOTE]
> Enhetsregistreringen är inte samma som enhetshantering eller hantering av mobila enheter (MDM). Enhetsregistrering associerar endast ett enhets-ID och ett användar-ID tillsammans i Azure AD-katalogen.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD-autentisering och lösenordslösa metoder finns i följande artiklar:

- [Lär dig hur lösenordsfri autentisering fungerar](concept-authentication-passwordless.md)
- [Läs mer om enhetsregistrering](../devices/overview.md#getting-devices-in-azure-ad)
- [Läs mer om Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
