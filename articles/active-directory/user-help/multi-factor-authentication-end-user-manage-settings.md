---
title: Ändra metod och inställningar för en tvåfaktorautentisering – Azure Active Directory
description: Lär dig hur du ändrar säkerhets verifierings metoden och inställningarna för ditt arbets-eller skol konto från sidan ytterligare säkerhets verifiering.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/06/2020
ms.author: curtand
ms.openlocfilehash: e0a6c566e8e0dfb77b5899f735020d0f1facf3d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88798388"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>Ändra metod och inställningar för en tvåfaktorautentisering

När du har konfigurerat dina säkerhets verifierings metoder för ditt arbets-eller skol konto kan du uppdatera alla relaterade uppgifter, inklusive:

- Standard metod för säkerhets verifiering

- Information om säkerhets verifierings metoden, t. ex. ditt telefonnummer

- Authenticator-appens installation eller borttagning av en enhet från Authenticator-appen

## <a name="using-the-additional-security-verification-page"></a>Använda sidan ytterligare säkerhets verifiering

Om din organisation tillhandahöll ett särskilt steg om hur du aktiverar och hanterar en tvåfaktorautentisering bör du följa anvisningarna först. Annars kan du komma till inställningarna för säkerhets verifierings metoden från sidan [ytterligare säkerhets verifiering](./multi-factor-authentication-end-user-first-time.md) .

>[!Note]
>Om det du ser på skärmen inte stämmer överens med vad som beskrivs i den här artikeln, innebär det att antingen din administratör har aktiverat **säkerhets information (för hands versionen)** eller om din organisation har angett din egen anpassade Portal. Mer information om den nya säkerhets informationens upplevelse finns i [Översikt över säkerhets information (för hands version)](./security-info-setup-signin.md). Om du vill ha mer information om din organisations anpassade portal måste du kontakta din organisations supportavdelning.

### <a name="to-get-to-the-additional-security-verification-page"></a>Så här kommer du till sidan ytterligare säkerhets verifiering

Du kan följa den här länken till [sidan ytterligare säkerhets verifiering](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1).

![Sidan ytterligare säkerhets verifiering med information om den tillgängliga säkerhets verifierings metoden](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

Du kan också gå till sidan för **ytterligare säkerhets verifiering** genom att följa dessa steg:

1. Logga in på [https://myapps.microsoft.com](https://myapps.microsoft.com).

1. Välj ditt konto namn längst upp till höger och välj sedan **profil**.

1. Välj **ytterligare säkerhets verifiering**.  

    ![Länken Mina appar till sidan ytterligare säkerhets verifiering](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>Information om hur du använder **app-lösenords** -avsnittet på sidan **ytterligare säkerhets verifiering** finns i [Hantera applösenord för tvåstegsverifiering](multi-factor-authentication-end-user-app-passwords.md). Applösenord bör endast användas för appar som inte stöder tvåstegsverifiering.

## <a name="change-your-default-security-verification-method"></a>Ändra din standard metod för säkerhets verifiering

När du har loggat in på ditt arbets-eller skol konto med ditt användar namn och lösen ord visas automatiskt den säkerhets verifierings metod du valt. Beroende på organisationens krav kan detta vara ett meddelande eller en verifierings kod via en Authenticator-app, ett textmeddelande eller ett telefonsamtal.

Om du bestämmer dig för att ändra standard säkerhets verifierings metoden som du använder, kan du göra det här.

### <a name="to-change-your-default-security-verification-method"></a>Ändra din standard metod för säkerhets verifiering

1. På sidan **ytterligare säkerhets verifiering** väljer du den metod som du vill använda från listan **Vad är önskad alternativ** lista. Du ser alla alternativ, men du kan bara välja de som du har gjort tillgängliga för din organisation.

    - **Meddela mig via appen**: du meddelas via din Authenticator-app att du har en väntande verifierings fråga.

    - **Ring min telefon för autentisering**: du får ett telefonsamtal på din mobila enhet och du uppmanas att verifiera din information.

    - **Text kod till min telefon för autentisering**: du får en verifierings kod som en del av ett textmeddelande på din mobila enhet. Du måste ange den här koden i verifierings frågan för ditt arbets-eller skol konto.

    - **Ring min arbets telefon**: du får ett telefonsamtal på din arbets telefon och du uppmanas att verifiera din information.

    - **Använd verifierings koden från appen**: du använder din Authenticator-app för att få en verifierings kod som du skriver in i prompten från ditt arbets-eller skol konto.

2. Välj **Spara**.

## <a name="add-or-change-your-phone-number"></a>Lägg till eller ändra ditt telefonnummer

Du kan lägga till nya telefonnummer eller uppdatera befintliga nummer på sidan **ytterligare säkerhets verifiering** .

>[!Important]
>Vi rekommenderar starkt att du lägger till ett sekundärt telefonnummer som förhindrar att ditt konto blir utelåst om din primära telefon tappas bort eller blir stulen, eller om du får en ny telefon och inte längre har ditt ursprungliga, primära telefonnummer.

### <a name="to-change-your-phone-numbers"></a>Ändra dina telefonnummer

1. Från sidan **Hur vill du svara?** på sidan **ytterligare säkerhets verifiering** uppdaterar du telefonnumret för din **telefon för autentisering** (din primära mobila enhet) och din **arbets telefon**.

1. Markera rutan bredvid telefon alternativet för **alternativ autentisering** och skriv sedan ett sekundärt telefonnummer där du kan ta emot telefonsamtal om du inte kan komma åt din primära enhet.

1. Välj **Spara**.

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>Lägg till ett nytt konto i Microsoft Authenticator-appen

Du kan ställa in ditt arbets-eller skol konto på Microsoft Authenticator-appen för [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) eller [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).

Om du redan har konfigurerat ditt arbets-eller skol konto i Microsoft Authenticator-appen behöver du inte göra det igen.

1. I avsnittet **Hur vill du svara?** på sidan **ytterligare säkerhets verifiering** väljer du **Konfigurera Authenticator-appen**.

    ![Konfigurera ditt arbets-eller skol konto i Microsoft Authenticator-appen](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

1. Följ anvisningarna på skärmen, inklusive att använda din mobila enhet för att skanna QR-koden och välj sedan **Nästa**.

    Du uppmanas att godkänna ett meddelande via Microsoft Authenticator-appen för att verifiera din information.

1. Välj **Spara**.

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>Ta bort ditt konto eller din enhet från Microsoft Authenticator-appen

Du kan ta bort ditt konto från Microsoft Authenticator-appen, och du kan ta bort din enhet från ditt arbets-eller skol konto. Vanligt vis tar du bort enheten för att permanent ta bort en förlorad, stulen eller gammal enhet från ditt konto, och du tar bort ditt konto för att försöka åtgärda några anslutnings problem eller för att åtgärda en konto ändring, till exempel ett nytt användar namn.

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>Ta bort din enhet från ditt arbets-eller skol konto

1. I avsnittet **Hur vill du svara?** på sidan **ytterligare säkerhets verifiering** väljer du knappen **Konfigurera autentiserare-appen** .

1. Välj **Spara**.

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>Ta bort ditt konto från Microsoft Authenticator-appen

Från Microsoft Authenticator-appen väljer du knappen **ta bort** bredvid den enhet som du vill ta bort.

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>Aktivera två faktorers verifierings meddelanden på en betrodd enhet

Beroende på dina organisations inställningar kan en kryss ruta visas som säger **inte igen om X dagar** när du utför en tvåstegsverifiering i webbläsaren. Om du har valt det här alternativet för att stoppa två-Factor Verification-prompter, och du tappar bort enheten eller om enheten är potentiellt komprometterad, bör du aktivera tvåstegsverifiering igen för att skydda ditt konto. Du måste aktivera prompterna på för alla dina enheter på samma tid. Tyvärr kan du inte aktivera prompterna på bara för en speciell enhet.

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>Så här gör du om du vill aktivera tvåstegsverifiering på enheter

På sidan [ **ytterligare säkerhets verifiering**](#to-get-to-the-additional-security-verification-page)väljer du **Återställ Multi-Factor Authentication på tidigare betrodda enheter**. Nästa gången du loggar in på en enhet uppmanas du att utföra tvåstegsverifiering.

## <a name="next-steps"></a>Nästa steg

När du har lagt till eller uppdaterat dina inställningar för tvåstegsverifiering kan du hantera dina applösenord, logga in eller få hjälp med några vanliga problem som rör tvåstegsverifiering.

- [Hantera applösenord för tvåstegsverifiering](multi-factor-authentication-end-user-app-passwords.md) för alla appar som inte stöder tvåstegsverifiering.

- [Logga in med tvåstegsverifiering](multi-factor-authentication-end-user-signin.md)

- [Lös vanliga problem med tvåstegsverifiering](multi-factor-authentication-end-user-troubleshoot.md)