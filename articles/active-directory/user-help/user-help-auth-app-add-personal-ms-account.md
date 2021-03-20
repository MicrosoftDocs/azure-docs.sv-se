---
title: Lägg till en personlig Microsoft-konto i Microsoft Authenticator-appen – Azure AD
description: Lägg till personliga Microsoft-konton, till exempel för Outlook.com eller Xbox LIVE i Microsoft Authenticator-appen för att verifiera din identitet när du använder tvåstegsverifiering.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/08/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: ff83a9a4e4bfd4c27dd3f8d3f212e489c3772eb4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "83741617"
---
# <a name="add-personal-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Lägga till personliga Microsoft-konton i Microsoft Authenticator-appen

Lägg till dina personliga Microsoft-konton, t. ex. för Outlook.com-och Xbox LIVE-konton, i Microsoft Authenticator-appen för båda standard inloggnings processen med två faktorer och lösen ord.

- **Standard verifierings metod med två faktorer.** Ange ditt användar namn och lösen ord i enheten som du loggar in på och välj sedan om Microsoft Authenticator appen skickar ett meddelande eller om du hellre vill kopiera den tillhör ande verifierings koden från skärmen **konton** i Microsoft Authenticators appen.

- **Lösen ords registrerings metod.** Ange ditt användar namn i enheten som du loggar in på med din personliga Microsoft-konto och Använd sedan din mobila enhet för att kontrol lera att du använder ditt finger avtryck, ansikte eller PIN-kod. För den här metoden behöver du inte ange ditt lösenord.

>[!Important]
>Innan du kan lägga till ditt konto måste du ladda ned och installera Microsoft Authenticator-appen. Om du inte har gjort det än följer du stegen i artikeln [Hämta och installera appen](user-help-auth-app-download-install.md) .

Du kan lägga till din personliga Microsoft-konto genom att först aktivera tvåstegsverifiering och sedan lägga till kontot i appen. Du behöver inte aktivera tvåstegsverifiering för att bara använda lösen ords lös telefonin loggning för ditt konto, men vi rekommenderar starkt att du aktiverar tvåstegsverifiering för ytterligare konto säkerhet.

## <a name="turn-on-two-factor-verification"></a>Aktivera tvåstegsverifiering

1. På din dator går du till sidan [grundläggande om säkerhet](https://account.microsoft.com/security) och loggar in med din personliga Microsoft-konto. Till exempel alain@outlook.com.

2. Längst ned på sidan **grundläggande säkerhets** inställningar väljer du länken **fler säkerhets alternativ** .

    ![Sidan grundläggande säkerhet med länken "fler säkerhets alternativ" markerad](./media/user-help-auth-app-add-personal-ms-account/more-security-options-link.png)

3. Gå till avsnittet **tvåstegsverifiering** och välj att **aktivera funktionen.** Du kan också inaktivera det här om du inte längre vill använda det med ditt personliga konto.

## <a name="add-your-microsoft-account-to-the-app"></a>Lägg till din Microsoft-konto i appen

1. Öppna appen Microsoft Authenticator på den mobila enheten.

1. På Android väljer du **Lägg till konto** från ikonen **Anpassa och kontrol lera** längst upp till höger.

    ![Val sidor för Android-konto](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon.png)

    Välj plus ikonen i det övre högra hörnet på iOS.

    ![iOS-version av konto markerings upplevelsen](media/user-help-auth-app-add-personal-ms-account/customize-and-control-icon-ios.png)

1. På sidan **Lägg till konto** väljer du **personligt konto**.

1. Välj **Logga in med Microsoft** för att lägga till ditt konto. En QR-kod kan användas när den är tillgänglig, men du kan alltid lägga till ditt konto genom att logga in med ditt användar namn och lösen ord.

    ![Välj antingen en Microsoft-konto eller skanna en QR-kod när den är tillgänglig](media/user-help-auth-app-add-personal-ms-account/add-account-android.png)

1. Logga in på ditt personliga konto med lämplig e-postadress (till exempel alain@outlook.com ) och välj sedan **Nästa**.

    >[!Note]
    >Om du inte har någon personlig Microsoft-konto [kan du skapa ett](https://account.microsoft.com/account/Account?refd=www.bing.com&ru=https%3A%2F%2Faccount.microsoft.com%2F%3Frefd%3Dwww.bing.com&destrt=home-index).

1. Ange ditt lösen ord och välj sedan **Logga** in. Ditt personliga konto läggs till i Microsoft Authenticator-appen.

## <a name="next-steps"></a>Nästa steg

- När du har lagt till dina konton i appen kan du logga in med Authenticator-appen på din enhet. Mer information finns i [Logga in med appen](user-help-auth-app-sign-in.md).

- Om du har problem med att hämta din verifierings kod för din personliga Microsoft-konto kan du läsa avsnittet **fel sökning av verifierings kod problem** i artikeln [Microsoft-konto säkerhets information & verifierings koder](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) .

- För enheter som kör iOS kan du också säkerhetskopiera dina kontoautentiseringsuppgifter och relaterade appinställningar, till exempel ordningen på dina konton, i molnet. Mer information finns i [säkerhets kopiering och återställning med Microsoft Authenticator app](user-help-auth-app-backup-recovery.md).
