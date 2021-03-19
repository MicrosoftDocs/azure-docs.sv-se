---
title: Logga in med din identitets autentiseringsinformation – Azure AD
description: Läs om hur du loggar in med de olika identitets verifierings metoderna i säkerhets information.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/18/2019
ms.author: curtand
ms.custom: user-help
ms.openlocfilehash: 42ced6632ebfa56af8fe13a02f531b9835e13df3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88799391"
---
# <a name="sign-in-using-two-step-verification-or-security-info"></a>Logga in med tvåstegsverifiering eller säkerhets information

När du har konfigurerat tvåstegsverifiering eller säkerhets information kan du logga in på ditt konto med hjälp av den angivna verifierings metoden.

> [!Note]
> Om du fortfarande använder tvåstegsverifiering måste du ställa in dina verifierings metoder genom att följa anvisningarna i artikeln [Konfigurera mitt konto för tvåstegsverifiering för två steg](multi-factor-authentication-end-user-first-time.md) .
>
> Om din administratör har aktiverat säkerhets informations upplevelsen måste du ange dina verifierings metoder med hjälp av dessa steg-för-steg-artiklar:<ul><li>[Konfigurera säkerhets information för att använda en app för autentisering](security-info-setup-auth-app.md)</li><li>[Konfigurera säkerhetsinformation för att använda textmeddelanden](security-info-setup-text-msg.md)</li><li>[Konfigurera säkerhets information för att använda ett telefonsamtal](security-info-setup-phone-number.md)</li><li>[Konfigurera säkerhets information för att använda en säkerhets nyckel](security-info-setup-security-key.md)</li></ul>

## <a name="sign-in-using-an-authenticator-app-notification-on-your-mobile-device"></a>Logga in med ett autentiserat meddelande på din mobila enhet

1. Logga in på ditt konto med ditt användar namn och lösen ord.

2. Välj **Godkänn** från godkännande meddelandet som skickas till din mobila enhet.

## <a name="sign-in-using-an-authenticator-app-code-on-your-mobile-device"></a>Logga in med en Authenticator-app-kod på din mobila enhet

1. Logga in på ditt konto med ditt användar namn och lösen ord.

2. Öppna din Authenticator-app och skriv den slumpmässigt genererade koden för ditt konto i rutan **Ange kod** .

## <a name="sign-in-using-your-phone-number"></a>Logga in med ditt telefonnummer

1. Logga in på ditt konto med ditt användar namn och lösen ord.

2. Besvara din telefon och följ anvisningarna.

## <a name="sign-in-using-a-text-message"></a>Logga in med ett SMS

1. Logga in på ditt konto med ditt användar namn och lösen ord.

2. Öppna textmeddelandet och skriv koden från SMS-meddelandet i rutan **Ange kod** .

## <a name="sign-in-using-a-security-key-at-the-lock-screen"></a>Logga in med en säkerhets nyckel på Lås skärmen

1. När du har registrerat din säkerhets nyckel väljer du säkerhets nyckel avbildningen på Lås skärmen för Windows 10.

2. Infoga din säkerhets nyckel i enhetens USB-port och logga in i Windows med hjälp av din PIN-kod för säkerhets nyckeln.

    ![Inloggning av säkerhets nyckel på Lås skärmen i Windows 10](./media/security-info/security-info-windows-10-lock-screen-security-key.png)

## <a name="sign-in-using-a-security-key-and-the-microsoft-edge-browser"></a>Logga in med en säkerhets nyckel och Microsoft Edge-webbläsaren

1. När du har registrerat din säkerhets nyckel öppnar du Microsoft Edge-webbläsaren.

2. När du uppmanas att logga in, infogar du säkerhets nyckeln i enhetens USB-port och loggar in i Windows med hjälp av din PIN-kod för säkerhets nyckeln.

    ![Säkerhets nyckel inloggning med Microsoft Edge-webbläsaren](./media/security-info/security-info-edge-security-key.png)

    >[!NOTE]
    >Information om hur du loggar in med Microsoft Authenticator-appen finns i artikeln [Logga in på dina konton med hjälp av Microsoft Authenticator-appen](user-help-auth-app-sign-in.md).

## <a name="sign-in-using-another-verification-method"></a>Logga in med en annan verifieringsmetod

Om du av någon anledning inte kan använda din primära inloggnings metod kan du använda en annan tidigare konfigurerad verifieringsmetod.

1. Logga in på ditt konto på vanligt sätt och välj sedan länken **Logga in på ett annat sätt** **på sidan med tvåstegsverifiering.**

    ![Ändra verifierings metod för inloggning](media/security-info/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Om du inte ser länken **Logga in på ett annat sätt** , innebär det att du inte har konfigurerat några andra verifierings metoder och att du måste kontakta administratören för att få hjälp med att logga in på ditt konto. När administratören har försäkrat dig om att logga in, måste du lägga till ytterligare verifierings metoder. Mer information om hur du lägger till verifieringsmetoder finns i artikeln [Hantera inställningar för](multi-factor-authentication-end-user-manage-settings.md) tvåstegsverifiering.
    >
    >Om du ser länken **Logga in på ett annat sätt** , men ändå inte ser några andra verifierings metoder, måste du kontakta administratören för att få hjälp med att logga in på ditt konto.

2. Välj den alternativa verifierings metoden och fortsätt med verifierings processen i två steg.

3. När du är tillbaka i ditt konto kan du uppdatera dina verifierings metoder (om det behövs). Mer information om hur du lägger till eller ändrar dina metoder finns i artikeln [Hantera inställningar för](multi-factor-authentication-end-user-manage-settings.md) tvåstegsverifiering.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om säkerhets information i [översikts artikeln om säkerhets information (för hands version)](./security-info-setup-signin.md) .

- Läs mer om tvåstegsverifiering i artikeln [Översikt över](./multi-factor-authentication-end-user-first-time.md) tvåstegsverifiering.

- Återställ ditt lösen ord om du har förlorat eller glömt bort det från [portalen för återställning av lösen ord](https://passwordreset.microsoftonline.com/)

- Få felsökningstips och hjälp om inloggningsproblem i artikeln [Det går inte att logga in på ditt Microsoft-konto](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).