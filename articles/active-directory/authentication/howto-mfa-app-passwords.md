---
title: Konfigurera applösenord för Azure AD Multi-Factor Authentication – Azure Active Directory
description: Lär dig hur du konfigurerar och använder applösenord för äldre program i Azure AD Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfb38f9fcdba6898b690d0af68b715fea07e80bb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96743113"
---
# <a name="enable-and-use-azure-ad-multi-factor-authentication-with-legacy-applications-using-app-passwords"></a>Aktivera och använda Azure AD Multi-Factor Authentication med äldre program med hjälp av applösenord

Vissa äldre appar än webb läsar program, t. ex. Office 2010 eller tidigare och Apple Mail före iOS 11, förstår inte pauser eller avbrott i autentiseringsprocessen. Om en användare är aktive rad för Azure AD Multi-Factor Authentication och försöker använda en av dessa äldre appar, som inte är webbläsarbaserade, kan de inte autentiseras. Om du vill använda dessa program på ett säkert sätt med Azure AD Multi-Factor Authentication aktiverat för användar konton kan du använda applösenord. Dessa applösenord ersatte ditt traditionella lösen ord för att tillåta att en app kringgår Multi-Factor Authentication och fungerar som den ska.

Modern autentisering stöds för Microsoft Office 2013-klienter och senare. Office 2013-klienter, inklusive Outlook, stöder moderna autentiseringsprotokoll och kan aktive ras för att fungera med tvåstegsverifiering. När klienten har Aktiver ATS krävs inte applösenord för klienten.

Den här artikeln visar hur du aktiverar och använder applösenord för äldre program som inte stöder Multi-Factor Authentication-prompter.

>[!NOTE]
> Applösenord fungerar inte med villkorlig åtkomst baserade Multi-Factor Authentication-principer och modern autentisering.

## <a name="overview-and-considerations"></a>Översikt och överväganden

När ett användar konto är aktiverat för Azure AD Multi-Factor Authentication avbryts den vanliga inloggnings frågan med en begäran om ytterligare verifiering. Vissa äldre program förstår inte den här rasten i inloggnings processen, så autentiseringen Miss lyckas. Om du vill upprätthålla säkerhet för användar konton och låta Azure AD Multi-Factor Authentication aktive rad kan du använda applösenord i stället för användarens vanliga användar namn och lösen ord. När ett applösenord används under inloggningen finns det ingen ytterligare verifierings fråga, så autentiseringen lyckades.

Applösenord genereras automatiskt och anges inte av användaren. Detta automatiskt genererade lösen ord gör det svårare för en angripare att gissa, vilket är säkrare. Användarna behöver inte hålla reda på lösen orden eller ange dem varje gång som applösenord bara anges en gång per program.

När du använder applösenord gäller följande saker:

* Det finns en gräns på 40 app-lösenord per användare.
* Program som cachelagrar lösen ord och använder dem i lokala scenarier kan inte köras på grund av att appens lösen ord inte är känt utanför arbets-eller skol kontot. Ett exempel på det här scenariot är Exchange-e-post som är lokalt, men det arkiverade e-postmeddelandet finns i molnet. I det här scenariot fungerar inte samma lösen ord.
* När Azure AD Multi-Factor Authentication har Aktiver ATS på ett användar konto kan applösenord användas med de flesta klienter som inte är webbläsare, till exempel Outlook och Microsoft Skype för företag. Administrativa åtgärder kan dock inte utföras med hjälp av applösenord via appar som inte är webb läsar program, t. ex. Windows PowerShell. Det går inte att utföra åtgärderna även om användaren har ett administratörs konto.
    * Om du vill köra PowerShell-skript skapar du ett tjänst konto med ett starkt lösen ord och aktiverar inte kontot för tvåstegsverifiering.
* Om du misstänker att ett användar konto har komprometterats och återkallar lösen ordet för kontot bör även applösenord uppdateras. Applösenord återkallas inte automatiskt när ett användar konto lösen ord återkallas/återställs. Användaren bör ta bort befintliga applösenord och skapa nya.
   * Mer information finns i [skapa och ta bort applösenord från sidan ytterligare säkerhets verifiering](../user-help/multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page).

>[!WARNING]
> Applösenord fungerar inte i hybrid miljöer där klienter kommunicerar med både lokala och molnbaserade slut punkter för automatisk identifiering. Domän lösen ord krävs för autentisering lokalt. Applösenord krävs för att autentisera med molnet.

### <a name="app-password-names"></a>Lösen ords namn för appen

Appens lösen ords namn ska motsvara den enhet där de används. Om du har en bärbar dator som har icke-webbläsarbaserade program som Outlook, Word och Excel, skapar du ett applösenord med namnet **bärbar dator** för dessa appar. Skapa ett annat applösenord med namnet **Desktop** för samma program som körs på din station ära dator.

Vi rekommenderar att du skapar ett applösenord per enhet, i stället för ett applösenord per program.

## <a name="federated-or-single-sign-on-app-passwords"></a>Applösenord för federerad eller enkel inloggning

Azure AD stöder Federation eller enkel inloggning (SSO) med lokala Active Directory Domain Services (AD DS). Om din organisation är federerad med Azure AD och du använder Azure AD Multi-Factor Authentication, gäller följande lösen ords överväganden för appen:

>[!NOTE]
> Följande punkter gäller endast för federerade (SSO)-kunder.

* Applösenord verifieras av Azure AD och kan därför kringgå Federation. Federation används aktivt bara när du konfigurerar applösenord.
* Identitets leverantören (IdP) kontaktas inte för federerade (SSO)-användare, till skillnad från det passiva flödet. Applösenord lagras på arbets-eller skol kontot. Om en användare lämnar företaget kan användarens information flöda till arbets-eller skol kontot med hjälp av **DirSync** i real tid. Det kan ta upp till tre timmar innan kontot kan synkroniseras, vilket kan försena inaktive ring/borttagning av applösenord i Azure AD.
* Lokala klient Access Controls inställningar hanteras inte av appens lösen ord.
* Ingen lokal autentiserings-eller gransknings funktion är tillgänglig med appens lösen ords funktioner.

Vissa avancerade arkitekturer kräver en kombination av autentiseringsuppgifter för Multi-Factor Authentication med-klienter. Dessa autentiseringsuppgifter kan omfatta ett arbets-eller skol konto användar namn och lösen ord samt applösenord. Kraven beror på hur autentiseringen utförs. För klienter som autentiseras mot en lokal infrastruktur krävs ett användar namn och lösen ord för arbets-eller skol kontot. För klienter som autentiseras mot Azure AD krävs ett applösenord.

Anta till exempel att du har följande arkitektur:

* Din lokala instans av Active Directory är federerad med Azure AD.
* Du använder Exchange Online.
* Du använder Skype för företag lokalt.
* Du använder Azure AD Multi-Factor Authentication.

I det här scenariot använder du följande autentiseringsuppgifter:

* Om du vill logga in på Skype för företag använder du användar namnet och lösen ordet för ditt arbets-eller skol konto.
* Använd ett applösenord för att få åtkomst till adress boken från en Outlook-klient som ansluter till Exchange Online.

## <a name="allow-users-to-create-app-passwords"></a>Tillåt användare att skapa applösenord

Som standard kan användarna inte skapa applösenord. Funktionen applösenord måste vara aktive rad innan användarna kan använda dem. För att ge användarna möjlighet att skapa applösenord, utför följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Sök efter och välj **Azure Active Directory** och välj sedan **användare**.
3. Välj **Multi-Factor Authentication** i navigerings fältet högst upp i fönstret *användare* .
4. Under Multi-Factor Authentication väljer du **tjänst inställningar**.
5. På sidan **tjänst inställningar** väljer du alternativet **Tillåt användare att skapa applösenord för att logga in på icke-webbläsarbaserade appar** .

    ![Skärm bild av Azure Portal som visar tjänst inställningarna för Multi-Factor Authentication för att tillåta användare av applösenord](media/concept-authentication-methods/app-password-authentication-method.png)
    
> [!NOTE]
>
> När du inaktiverar möjligheten för användare att skapa applösenord, fortsätter befintliga applösenord att fungera. Användare kan dock inte hantera eller ta bort de befintliga applösenord när du har inaktiverat den här funktionen.
>
> När du inaktiverar möjligheten att skapa applösenord, rekommenderar vi också att du [skapar en princip för villkorlig åtkomst för att inaktivera användningen av äldre autentisering](../conditional-access/block-legacy-authentication.md). Den här metoden förhindrar att befintliga applösenord fungerar och tvingar användningen av moderna autentiseringsmetoder.

## <a name="create-an-app-password"></a>Skapa ett applösenord

När användarna har slutfört den första registreringen för Azure AD Multi-Factor Authentication finns det ett alternativ för att skapa applösenord i slutet av registrerings processen.

Användare kan också skapa applösenord efter registreringen. Mer information och detaljerade anvisningar för dina användare finns i [Vad är app-lösenord i Azure AD Multi-Factor Authentication?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du gör det möjligt för användare att snabbt registrera för Azure AD Multi-Factor Authentication finns i [Översikt över kombinerade säkerhets informations registrering](concept-registration-mfa-sspr-combined.md).
