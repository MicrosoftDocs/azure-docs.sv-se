---
title: Konfigurera en mobil enhet som en metod för verifiering av två faktorer – Azure Active Directory | Microsoft Docs
description: Lär dig hur du konfigurerar en mobil enhet som en metod för verifiering av två faktorer.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: 40af9ac692a226b12f984db447db9635cba655e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91530263"
---
# <a name="set-up-a-mobile-device-as-your-two-factor-verification-method"></a>Konfigurera en mobil enhet som en metod för verifiering av två faktorer

Du kan konfigurera din mobila enhet så att den fungerar som en metod för verifiering av två faktorer. Din mobil telefon kan antingen ta emot ett SMS med en verifierings kod eller ett telefonsamtal.

>[!Note]
> Om telefon alternativet för autentisering är nedtonat är det möjligt att din organisation inte tillåter att du använder ett telefonnummer eller textmeddelande för verifiering. I så fall måste du välja en annan metod eller kontakta administratören om du vill ha mer hjälp.

## <a name="set-up-your-mobile-device-to-use-a-text-message-as-your-verification-method"></a>Konfigurera din mobila enhet så att ett SMS används som verifieringsmetod

1. På sidan **ytterligare säkerhets verifiering** väljer du **telefon för autentisering** från **steg 1: Hur ska vi kontakta dig** , välja land eller region i list rutan och sedan ange telefonnumret till din mobila enhet.

2. Välj **skicka mig en kod per SMS** från **metod** -arean och välj sedan **Nästa**.

    ![Skärm bild som visar sidan "ytterligare säkerhets verifiering", med "Authentication Phone" och "skicka mig en kod med textmeddelande" vald.](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message.png)

3. Skriv verifierings koden från textmeddelandet som skickas från Microsoft till **steg 2: vi har skickat ett SMS till ditt telefon** områden och väljer sedan **Verifiera**.

    ![Sidan ytterligare säkerhets verifiering med en telefon för autentisering och SMS](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message-test.png)

4. I **steg 3: Fortsätt att använda dina befintliga program** , kopierar du det angivna applösenord och klistrar in det på en säker plats.

    ![Plats för applösenord på sidan ytterligare säkerhets verifiering](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Information om hur du använder applösenord med dina äldre appar finns i [Hantera applösenord](multi-factor-authentication-end-user-app-passwords.md). Du behöver bara använda applösenord om du fortsätter att använda äldre appar som inte stöder tvåstegsverifiering.

5. Välj **Klar**.

## <a name="set-up-your-mobile-device-to-receive-a-phone-call"></a>Konfigurera din mobila enhet för att ta emot ett telefonsamtal

1. På sidan **ytterligare säkerhets verifiering** väljer du **telefon för autentisering** från **steg 1: Hur ska vi kontakta dig** , välja land eller region i list rutan och sedan ange telefonnumret till din mobila enhet.

2. Välj **anropa mig** från **metod** -ytan och välj sedan **Nästa**.

    ![Sidan ytterligare säkerhets verifiering med autentiserings telefon och telefonsamtal](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call.png)

3. Du får ett telefonsamtal från Microsoft och du uppmanas att trycka på pund tecknet (#) på din mobila enhet för att verifiera din identitet.

    ![Testar det angivna telefonnumret](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call-test.png)

4. I **steg 3: Fortsätt att använda dina befintliga program** , kopierar du det angivna applösenord och klistrar in det på en säker plats.

    ![Plats för applösenord på sidan ytterligare säkerhets verifiering](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Information om hur du använder applösenord med dina äldre appar finns i [Hantera applösenord](multi-factor-authentication-end-user-app-passwords.md). Du behöver bara använda applösenord om du fortsätter att använda äldre appar som inte stöder tvåstegsverifiering.

5. Välj **Klar**.

## <a name="next-steps"></a>Nästa steg

När du har ställt in din metod för tvåfaktorautentisering kan du lägga till ytterligare metoder, hantera dina inställningar och applösenord, logga in eller få hjälp med några vanliga problem med att verifiera två faktorer.

- [Hantera dina inställningar för verifierings metoden med två faktorer](multi-factor-authentication-end-user-manage-settings.md)

- [Hantera lösenord för appar](multi-factor-authentication-end-user-app-passwords.md)

- [Logga in med tvåstegsverifiering](multi-factor-authentication-end-user-signin.md)

- [Få hjälp med tvåstegsverifiering](multi-factor-authentication-end-user-troubleshoot.md)
