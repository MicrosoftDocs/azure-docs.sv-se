---
title: Lägg till ett konto manuellt i appen-Azure Active Directory | Microsoft Docs
description: Hur du lägger till dina konton manuellt i Microsoft Authenticator-appen för tvåstegsverifiering.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 3838234f9f65fcf91f8de33e3d7196aa13f2b903
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "83741725"
---
# <a name="manually-add-an-account-to-the-app"></a>Lägg till ett konto i appen manuellt

Om kameran inte kan avbilda QR-koden kan du manuellt lägga till din konto information i Microsoft Authenticator-appen för tvåstegsverifiering. Detta fungerar för arbets-eller skol konton och konton som inte kommer från Microsoft.

De koder som anges för dina konton är inte Skift läges känsliga och kräver inte blank steg när de läggs till i Microsoft Authenticator-appen.

>[!Important]
>Innan du kan lägga till ditt konto måste du ladda ned och installera Microsoft Authenticator-appen. Om du inte har gjort det än följer du stegen i artikeln [Hämta och installera appen](user-help-auth-app-download-install.md) .

## <a name="add-your-work-or-school-account"></a>Lägga till ditt arbets- eller skolkonto

1. På datorn noterar du **koden** och **URL** -informationen på sidan **Konfigurera mobilapp** . Behåll sidan öppen så att du kan se koden och URL: en.

    ![Skärm som tillhandahåller QR-koden](./media/user-help-auth-app-add-account-manual/auth-app-barcode.png)

2. Öppna appen Microsoft Authenticator, Välj **Lägg till konto** från ikonen **Anpassa och kontrol lera** längst upp till höger och välj sedan arbets- **eller skol konto**.

3. Välj **eller ange koden manuellt**.

    ![Skärm för att skanna en QR-kod](./media/user-help-auth-app-add-account-manual/auth-app-manual-code.png)

4. Ange **koden** och **URL: en** från steg 1 och välj sedan **Slutför**.

    ![Skärm för att ange kod och URL](./media/user-help-auth-app-add-account-manual/auth-app-code-url.png)

    Skärmen **konton** i appen visar ditt konto namn och en sex-siffrig verifierings kod. För ytterligare säkerhet ändras verifierings koden var 30: e sekund som hindrar någon från att använda en kod flera gånger.

## <a name="add-your-google-account"></a>Lägg till ditt Google-konto

1. På din dator kan du välja **att inte söka igenom den** från sidan **Konfigurera autentisering** med QR-koden.

    Sidan för **att skanna streckkoder** visas med den hemliga koden. Behåll den här sidan öppen så att du kan se den hemliga koden.

2. Öppna appen Microsoft Authenticator, Välj **Lägg till konto** från ikonen **Anpassa och kontrol lera** längst upp till höger, Välj **annat konto (Google, Facebook osv.)** och välj sedan **eller ange kod manuellt**.

3. Ange ett **konto namn** (till exempel Google) och skriv den **hemliga nyckeln** från steg 1 och välj sedan **Slutför**.

4. På sidan **Konfigurera autentiserare** på datorn anger du den sex-siffriga verifierings koden som anges i appen för ditt Google-konto och väljer sedan **Verifiera**.

    Skärmen **konton** i appen visar ditt konto namn och en sex-siffrig verifierings kod. För ytterligare säkerhet ändras verifierings koden var 30: e sekund som hindrar någon från att använda en kod flera gånger.

    >[!NOTE]
    >Mer information om tvåstegsverifiering och ditt Google-konto finns i [Aktivera tvåstegsverifiering](https://support.google.com/accounts/answer/185839) och [Lär dig mer om tvåstegsverifiering](https://www.google.com/landing/2step/help.html).

## <a name="add-your-facebook-account"></a>Lägg till ditt Facebook-konto

1. På sidan **Konfigurera via tredjeparts verifiering** , som innehåller QR-koden, och en kod som skrivs ut för inmatning i din app. Behåll sidan öppen så att du kan se koden.

2. Öppna appen Microsoft Authenticator, Välj **Lägg till konto** från ikonen **Anpassa och kontrol lera** längst upp till höger, Välj **annat konto (Google, Facebook osv.)** och välj sedan **eller ange kod manuellt**.

3. Ange ett **konto namn** (till exempel Facebook) och skriv den **hemliga nyckeln** från steg 1 och välj sedan **Slutför**.

4. På sidan med **tvåstegsverifiering** på datorn anger du den sex-siffriga verifierings koden som finns i appen för ditt Facebook-konto och väljer sedan **Verifiera**.

    Skärmen **konton** i appen visar ditt konto namn och en sex-siffrig verifierings kod. För ytterligare säkerhet ändras verifierings koden var 30: e sekund som hindrar någon från att använda en kod flera gånger.

    >[!NOTE]
    >Mer information om tvåstegsverifiering och ditt Facebook-konto finns i [Vad är tvåfaktorautentisering och hur fungerar det?](https://www.facebook.com/help/148233965247823).

## <a name="add-your-amazon-account"></a>Lägg till ditt Amazon-konto

Du kan lägga till ditt Amazon-konto genom att aktivera tvåstegsverifiering och sedan lägga till kontot i appen.

1. På datorn väljer du **kan inte skanna streckkoden** från sidan **Välj hur du tar emot koder** med QR-koden.

    Det **går inte att skanna streckkods** meddelandet visas med den hemliga koden. Se till att det här meddelandet är öppet så att du kan se den hemliga koden.

2. Öppna appen Microsoft Authenticator, Välj **Lägg till konto** från ikonen **Anpassa och kontrol lera** längst upp till höger, Välj **annat konto (Google, Facebook osv.)** och välj sedan **eller ange kod manuellt**.

3. Ange ett **konto namn** (till exempel Amazon) och skriv den **hemliga nyckeln** från steg 1 och välj sedan **Slutför**.

4. Slutför resten av registrerings processen, inklusive att lägga till en metod för säkerhets kopiering, till exempel ett textmeddelande, och välj sedan **Skicka kod**.

5. På sidan **Lägg till en verifierings metod för säkerhets kopiering** på datorn anger du den sexsiffriga verifierings koden som du fick från verifierings metoden för ditt Amazon-konto och väljer sedan **verifiera kod och fortsätt**.

6. På sidan nästan klar bestämmer du om du vill göra datorn till en betrodd enhet och välj sedan **Skaffa den. Aktivera Two-Step verifiering**.

    Sidan **avancerade säkerhets inställningar** visas och visar din uppdaterade information om tvåstegsverifiering.

    >[!NOTE]
    >Mer information om tvåstegsverifiering och ditt Amazon-konto finns i [om Two-Step verifiering](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) och [inloggning med Two-Step verifiering](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440).    

## <a name="next-steps"></a>Nästa steg

- När du har lagt till dina konton i appen kan du logga in med Microsoft Authenticator-appen på enheten. Mer information finns i [Logga in med appen](user-help-auth-app-sign-in.md).

- Om du har problem med att hämta din verifierings kod för din personliga Microsoft-konto kan du läsa avsnittet **fel sökning av verifierings kod problem** i artikeln [Microsoft-konto säkerhets information & verifierings koder](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) .

- För enheter som kör iOS kan du också säkerhetskopiera dina kontoautentiseringsuppgifter och relaterade appinställningar, till exempel ordningen på dina konton, i molnet. Mer information finns i [säkerhets kopiering och återställning med Microsoft Authenticator app](user-help-auth-app-backup-recovery.md).
