---
title: Konfigurera en Authenticator-app som metod med tvåstegsverifiering – Azure Active Directory | Microsoft Docs
description: Lär dig hur du konfigurerar Microsoft Authenticator-appen som en metod för verifiering av två faktorer.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: 10b6b788077eccda522c9e8c6e631939ca1819fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91530348"
---
# <a name="set-up-an-authenticator-app-as-your-two-factor-verification-method"></a>Konfigurera en Authenticator-app som metod med tvåstegsverifiering

Du kan ställa in en Authenticator-app för att skicka ett meddelande till din mobila enhet eller skicka en verifierings kod som säkerhets verifierings metod. Du behöver inte använda Microsoft Authenticator-appen, och du kan välja en annan app under installationen. Den här artikeln använder dock Microsoft Authenticator-appen.

>[!Important]
>Innan du kan lägga till ditt konto måste du ladda ned och installera Microsoft Authenticator-appen. Om du inte har gjort det än följer du stegen i artikeln [Hämta och installera appen](user-help-auth-app-download-install.md) .

>[!Note]
> Om alternativet för mobilapp är nedtonat är det möjligt att organisationen inte tillåter att du använder en webbapp för verifiering. I så fall måste du välja en annan metod eller kontakta administratören om du vill ha mer hjälp.

## <a name="set-up-the-microsoft-authenticator-app-to-send-notifications"></a>Konfigurera Microsoft Authenticator-appen för att skicka meddelanden

1. På [sidan ytterligare säkerhets verifiering](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)väljer du **mobilapp** från **steg 1: Hur ska vi kontakta** ditt ställe.

2. Välj **ta emot aviseringar för verifiering** från rutan **Hur vill du använda** modulen för mobilapp och välj sedan **Konfigurera**.

    ![Skärm bild som visar sidan "ytterligare säkerhets verifiering", med "mobilapp" och "ta emot meddelanden för verifiering" valda.](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification.png)

    Sidan **Konfigurera mobilapp** visas.

    ![Skärm som tillhandahåller QR-koden](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Öppna appen Microsoft Authenticator, Välj **Lägg till konto** från ikonen **Anpassa och kontrol lera** längst upp till höger och välj sedan arbets- **eller skol konto**.

    >[!Note]
    >Om det här är första gången du konfigurerar Microsoft Authenticator-appen kan du få en fråga om du vill tillåta att appen får åtkomst till kameran (iOS) eller tillåter att appen tar bilder och spelar in video (Android). Du måste välja **Tillåt** så att Authenticator-appen kan komma åt kameran för att ta en bild av QR-koden i nästa steg. Om du inte tillåter kameran kan du fortfarande konfigurera Authenticator-appen, men du måste lägga till kod informationen manuellt. Information om hur du lägger till koden manuellt finns i se [lägga till ett konto manuellt i appen](user-help-auth-app-add-account-manual.md).

4. Använd enhetens kamera för att skanna QR-koden från skärmen **Konfigurera mobilappen** på datorn och välj sedan **Nästa**.

5. Gå tillbaka till datorn och sidan **ytterligare säkerhets verifiering** , se till att du får meddelandet som säger att konfigurationen lyckades och välj sedan **Nästa**.

    ![Skärm bild som visar sidan "ytterligare säkerhets verifiering" och "mobilappen har kon figurer ATS..." meddelande om lyckad har marker ATS.](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification-confirm.png)

    Authenticator-appen skickar ett meddelande till din mobila enhet som ett test.

6. Välj **Godkänn** på din mobila enhet.

7. På din dator lägger du till din mobilen hets telefonnummer i **steg 3: om du förlorar åtkomsten till modulen mobilapp** och väljer sedan **Nästa**.

    Vi rekommenderar starkt att du lägger till mobilen hets telefonnumret för att fungera som en säkerhets kopia om du inte kan komma åt eller använda mobilappen av någon anledning.

8. I **steg 4: Fortsätt att använda dina befintliga program** , kopierar du det angivna applösenord och klistrar in det på en säker plats.

    ![Plats för applösenord på sidan ytterligare säkerhets verifiering](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Information om hur du använder applösenord med dina äldre appar finns i [Hantera applösenord](multi-factor-authentication-end-user-app-passwords.md). Du behöver bara använda applösenord om du fortsätter att använda äldre appar som inte stöder tvåstegsverifiering.

9. Välj **Klar**.

## <a name="set-up-the-microsoft-authenticator-app-to-use-verification-codes"></a>Konfigurera appen Microsoft Authenticator att använda verifierings koder

1. På sidan **ytterligare säkerhets verifiering** väljer du **mobilapp** från **steg 1: Hur ska vi kontakta** ditt ställe.

2. Välj **Använd verifierings kod** från den plats där **du vill använda mobilappen** och välj sedan **Konfigurera**.

    ![Sidan ytterligare säkerhets verifiering med alternativ för mobilapp och meddelanden](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-code.png)

    Sidan **Konfigurera mobilapp** visas.

    ![Skärm som tillhandahåller QR-koden](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Öppna appen Microsoft Authenticator, Välj **Lägg till konto** från ikonen **Anpassa och kontrol lera** längst upp till höger och välj sedan arbets- **eller skol konto**.

    >[!Note]
    >Om det här är första gången du konfigurerar Microsoft Authenticator-appen kan du få en fråga om du vill tillåta att appen får åtkomst till kameran (iOS) eller tillåter att appen tar bilder och spelar in video (Android). Du måste välja **Tillåt** så att Authenticator-appen kan komma åt kameran för att ta en bild av QR-koden i nästa steg. Om du inte tillåter kameran kan du fortfarande konfigurera Authenticator-appen, men du måste lägga till kod informationen manuellt. Information om hur du lägger till koden manuellt finns i se [lägga till ett konto manuellt i appen](user-help-auth-app-add-account-manual.md).

4. Använd enhetens kamera för att skanna QR-koden från skärmen **Konfigurera mobilappen** på datorn och välj sedan **Nästa**.

5. Gå tillbaka till datorn och sidan **ytterligare säkerhets verifiering** , se till att du får meddelandet som säger att konfigurationen lyckades och välj sedan **Nästa**.

    ![Sidan ytterligare säkerhets verifiering, med meddelande om lyckad](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-confirm.png)

    Authenticator-appen ber om en verifierings kod som ett test.

6. Från Microsoft Authenticator-appen rullar du ned till ditt arbets-eller skol konto, kopierar och klistrar in den 6-siffriga koden från appen i **steg 2: Ange verifierings koden i rutan mobilapp** på datorn och välj sedan **Verifiera**.

    ![Sidan ytterligare säkerhets verifiering, med verifierings kod test](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-test.png)

7. På din dator lägger du till din mobilen hets telefonnummer i **steg 3: om du förlorar åtkomsten till modulen mobilapp** och väljer sedan **Nästa**.

    Vi rekommenderar starkt att du lägger till mobilen hets telefonnumret för att fungera som en säkerhets kopia om du inte kan komma åt eller använda mobilappen av någon anledning.

8. I **steg 4: Fortsätt att använda dina befintliga program** , kopierar du det angivna applösenord och klistrar in det på en säker plats.

    ![Plats för applösenord på sidan ytterligare säkerhets verifiering](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Information om hur du använder applösenord med dina äldre appar finns i [Hantera applösenord](multi-factor-authentication-end-user-app-passwords.md). Du behöver bara använda applösenord om du fortsätter att använda äldre appar som inte stöder tvåstegsverifiering.

9. Välj **Klar**.

## <a name="next-steps"></a>Nästa steg

När du har ställt in din metod för tvåfaktorautentisering kan du lägga till ytterligare metoder, hantera dina inställningar och applösenord, logga in eller få hjälp med några vanliga problem med att verifiera två faktorer.

- [Hantera dina inställningar för verifierings metoden med två faktorer](multi-factor-authentication-end-user-manage-settings.md)

- [Hantera lösenord för appar](multi-factor-authentication-end-user-app-passwords.md)

- [Logga in med tvåstegsverifiering](multi-factor-authentication-end-user-signin.md)

- [Få hjälp med tvåstegsverifiering](multi-factor-authentication-end-user-troubleshoot.md)
