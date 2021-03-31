---
title: Vad är Microsoft Authenticator-appen? - Azure Active Directory | Microsoft Docs
description: Läs mer om Microsoft Authenticator-appen, till exempel vad det är, hur det fungerar och vilken information som ingår i det här avsnittet av innehållet.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 7f4c09a32794e13b0e0041ee916401b714d8509e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90055239"
---
# <a name="what-is-the-microsoft-authenticator-app"></a>Vad är Microsoft Authenticator-appen?

Med Microsoft Authenticator-appen kan du logga in på dina konton om du använder tvåfaktorsverifiering. Med tvåfaktorsverifiering kan du komma åt dina konton säkrare, särskilt när du visar känslig information. Eftersom lösenord kan glömmas, stjälas eller komprometteras är tvåfaktorsverifiering ett ytterligare steg som hjälper dig att skydda ditt konto genom att göra det svårare för andra att logga in.

Du kan använda Microsoft Authenticator-appen på flera sätt, däribland följande:

- Svara på en prompt för autentisering när du har loggat in med ditt användar namn och lösen ord.

- Logga in utan att ange ett lösen ord, med ditt användar namn, Authenticator-appen och din mobila enhet med ditt finger avtryck, ansikte eller PIN-kod.

- Som kod generator för andra konton som stöder Authenticator-appar.

> [!Important]
> Microsoft Authenticator-appen fungerar med ett konto som använder tvåstegsverifiering och som stöder tidsbaserad eng ång slö sen ord (TOTP mobilapp).
>
>Den här artikeln är avsedd för användare som försöker hämta och använda Microsoft Authenticator-appen som en säkerhets verifierings metod. Om du är administratör för att söka efter information om hur du aktiverar lösen ords lös inloggning med hjälp av Authenticator-appen för dina anställda och andra användnings områden, se [Aktivera lösen ords lös inloggning med Microsoft Authenticator-appen (för hands version)](../authentication/howto-authentication-passwordless-phone.md).

## <a name="terminology"></a>Terminologi

| Period|Beskrivning|
| ----|-----------|
| Tvåfaktorsverifiering | En verifieringsprocess som kräver att du endast använder två typer av verifieringsinformation, t.ex. ett lösenord och en PIN-kod. Microsoft Authenticator-appen har stöd för både tvåfaktorsverifiering som är standard och lösenordsfri inloggning. |
| Multi-Factor Authentication (MFA) | All tvåfaktorsverifiering är multifaktorautentisering, och kräver att du använder *minst* två typer av information för verifiering, baserat på din organisations behov. |
| Microsoft-konto (kallas även MSA) | Du skapar egna personliga konton för att få åtkomst till dina kundorienterade Microsoft-produkter och moln tjänster, till exempel Outlook, OneDrive, Xbox LIVE eller Microsoft 365. Ditt Microsoft-konto skapas och lagras i Microsofts kontosystem för konsumentidentiteter som drivs av Microsoft. |
| Arbets- eller skolkonto | Din organisation skapar ditt arbets-eller skol konto (till exempel alain@contoso.com ) så att du kan komma åt interna och potentiellt begränsade resurser, till exempel Microsoft Azure, Windows Intune och Microsoft 365. |
| Verifieringskod | Den sexsiffriga kod som visas i autentiseringsappen under varje konto som har lagts till. Verifieringskoden ändras var 30:e sekund, vilket hindrar att samma kod används flera gånger. Detta kallas även för engångslösenord (OTP). |

## <a name="how-two-factor-verification-works-with-the-app"></a>Så här fungerar tvåfaktorsverifiering med appen

Tvåfaktorsverifiering fungerar med Microsoft Authenticator-appen på följande sätt:

- **Indikering.** Ange ditt användarnamn och lösenord i enheten som du loggar in för ditt arbets- eller skolkonto eller ditt personliga Microsoft-konto så skickar Microsoft Authenticator-appen ett meddelande som ber dig **godkänna inloggningen**. Välj **Godkänn** om du känner igen inloggningsförsöket. Annars väljer du **Neka**. Om du väljer **Neka** kan du också markera begäran som falsk.

- **Verifierings kod.** Ange ditt användarnamn och lösenord i enheten som du loggar in för ditt arbets- eller skolkonto eller ditt personliga Microsoft-konto och kopiera sedan den associerade verifieringskoden från skärmen **Konton** i Microsoft Authenticator-appen. Verifieringskoden kallas även för autentisering med engångslösenord (OTP).

- **Konfiguration av lösenordsfri inloggning.** Ange ditt användarnamn och lösenord i enheten som du loggar in för ditt arbets- eller skolkonto eller ditt personliga Microsoft-konto och använd sedan din mobila enhet för att kontrollera att det är du med hjälp av ditt fingeravtryck, ansikte eller en PIN-kod. För den här metoden behöver du inte ange ditt lösenord.

### <a name="whether-to-use-your-devices-biometric-capabilities"></a>Om du vill använda enhetens biometriska funktioner

Om du använder en PIN-kod för att slutföra autentiseringen kan du konfigurera Microsoft Authenticator-appen så att den i stället använder enhetens (biometriska) funktioner för fingeravtryck eller ansiktsigenkänning. Du kan konfigurera detta den första gången du använder Authenticator-appen för att verifiera ditt konto. Det gör du genom att välja alternativet för att använda enhetens biometriska enhetsfunktioner i stället för PIN-koden.

## <a name="who-decides-if-you-use-this-feature"></a>Vem bestämmer om du använder den här funktionen?

Beroende på din typ av konto kan organisationen välja att du måste använda tvåfaktorsverifiering, eller så kanske du kan välja själv.

- **Arbets-eller skol konto.** Om du använder ett arbets- eller skolkonto (till exempel alain@contoso.com) är det upp till din organisation om du måste använda tvåfaktorsverifiering tillsammans med de specifika verifieringsmetoderna. Läs mer om att lägga till arbets-eller skolkonton i Microsoft Authenticator-appen i [Lägg till ditt arbets- eller skolkonto](user-help-auth-app-add-work-school-account.md).

- **Personligt Microsoft-konto.** Du kan välja att ställa in tvåfaktorsverifiering för dina personliga Microsoft-konton (till exempel alain@outlook.com). Mer information om hur du lägger till ditt personliga Microsoft-konto finns i [Lägga till dina personliga konton](user-help-auth-app-add-personal-ms-account.md).

- **Icke-Microsoft-konto.** Du kan välja att ställa in tvåfaktorsverifiering för andra konton än Microsoft-konton (till exempel alain@gmail.com). Icke-Microsoft-konton kan inte använda termen tvåfaktorsverifiering, men du ska kunna hitta funktionen bland inställningarna för **säkerhet** eller **inloggning**. Microsoft Authenticator-appen fungerar med alla konton som har stöd för TOTP-standarder. Mer information om hur du lägger till dina icke-Microsoft-konton finns i [Lägga till icke-Microsoft-konton](user-help-auth-app-add-non-ms-account.md).

## <a name="in-this-section"></a>Innehåll i det här avsnittet

| Artikel | Beskrivning |
| ------ | ------------ |
| [Ladda ned och installera appen](user-help-auth-app-download-install.md) | Beskriver var och hur du skaffar samt installerar Microsoft Authenticator-appen för enheter som kör Android och iOS. |
| [Lägga till arbets- eller skolkonton](user-help-auth-app-add-work-school-account.md) | Beskriver hur du lägger till olika arbets- eller skolkonton till Microsoft Authenticator-appen. |
| [Lägga till dina personliga konton](user-help-auth-app-add-personal-ms-account.md) | Beskriver hur du lägger till personliga Microsoft-konton i Microsoft Authenticator-appen. |
| [Lägga till icke-Microsoft-konton](user-help-auth-app-add-non-ms-account.md) | Beskriver hur du lägger till icke-Microsoft-konton i Microsoft Authenticator-appen. |
| [Lägga till konton manuellt](user-help-auth-app-add-account-manual.md) | Beskriver hur du manuellt lägger till ditt konto i Microsoft Authenticator-appen om du inte kan skanna QR-koden som tillhandahålls. |
| [Logga in med appen](user-help-auth-app-sign-in.md) | Beskriver hur du loggar in på de olika kontona med Microsoft Authenticator-appen.|
| [Säkerhetskopiera och återställa kontoautentiseringsuppgifter](user-help-auth-app-backup-recovery.md) | Innehåller information om hur du säkerhetskopierar och återställer autentiseringsuppgifterna för ditt konto med hjälp av Microsoft Authenticator-appen. |
| [Vanliga frågor och svar om Microsoft Authenticator-appen](user-help-auth-app-faq.md) | Innehåller svar på vanliga frågor om appen. |