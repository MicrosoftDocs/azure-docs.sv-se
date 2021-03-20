---
title: Lägg till konton som inte kommer från Microsoft i Microsoft Authenticator app – Azure AD
description: Lägg till konton som inte kommer från Microsoft, till exempel för Google eller Facebook till Microsoft Authenticator-appen för att verifiera din identitet när du använder tvåstegsverifiering.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 11/02/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 21c8e75ac81a443b1dd9d4a0f43263bbf40bee88
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359208"
---
# <a name="add-non-microsoft-accounts-to-the-microsoft-authenticator-app"></a>Lägg till konton som inte kommer från Microsoft i Microsoft Authenticator-appen

Om du har konton som inte kommer från Microsoft, t. ex. för Google, Facebook eller GitHub, kan du lägga till dem i Microsoft Authenticator-appen för tvåstegsverifiering. Microsoft Authenticator-appen fungerar med en app som använder tvåstegsverifiering och alla konton som stöder tidsbaserad eng ång slö sen ord (TOTP mobilapp)-standarder.

>[!Important]
>Innan du kan lägga till ditt konto måste du ladda ned och installera Microsoft Authenticator-appen. Om du inte har gjort det än följer du stegen i artikeln [Hämta och installera appen](user-help-auth-app-download-install.md) .

## <a name="add-personal-accounts"></a>Lägg till personliga konton

För alla dina personliga konton måste du i allmänhet:

1. Logga in på ditt konto och aktivera tvåstegsverifiering genom att använda antingen din enhet eller din dator.

2. Lägg till kontot i Microsoft Authenticator-appen. Du kan bli ombedd att skanna en QR-kod som en del av den här processen.

    >[!Note]
    >Om det här är första gången du konfigurerar Microsoft Authenticator-appen kan du få en fråga om du vill tillåta att appen får åtkomst till kameran (iOS) eller tillåter att appen tar bilder och spelar in video (Android). Du måste välja **Tillåt** så att Authenticator-appen kan komma åt kameran för att ta en bild av QR-koden i nästa steg. Om du inte tillåter kameran kan du fortfarande konfigurera Authenticator-appen, men du måste lägga till kod informationen manuellt. Information om hur du lägger till koden manuellt finns i se [lägga till ett konto manuellt i appen](user-help-auth-app-add-account-manual.md).

Vi tillhandahåller processen för dina Facebook-, Google-, GitHub-och Amazon-konton, men processen är densamma för andra appar, till exempel Instagram och Adobe.

## <a name="add-your-google-account"></a>Lägg till ditt Google-konto

Lägg till ditt Google-konto genom att aktivera tvåstegsverifiering och sedan lägga till kontot i appen.

### <a name="turn-on-two-factor-verification"></a>Aktivera tvåstegsverifiering

1. Gå till på datorn, https://myaccount.google.com/signinoptions/two-step-verification/enroll-welcome Välj **Kom igång** och kontrol lera din identitet.

2. Följ stegen på sidan för att aktivera tvåstegsverifiering för ditt personliga Google-konto.

### <a name="add-your-google-account-to-the-app"></a>Lägg till ditt Google-konto i appen

1. På sidan med Google-kontots säkerhet på din dator ( https://myaccount.google.com/security) går du till avsnittet **Lägg till fler andra steg för att kontrol lera att det är du** , väljer du **Konfigurera** i avsnittet **Authenticator-app** .

2. På sidan **Hämta koder från Authentication-appen** väljer du antingen **Android** eller **iPhone** baserat på telefon typen och väljer sedan **Nästa**.

    Du får en QR-kod som du kan använda för att automatiskt associera ditt konto med Microsoft Authenticator-appen. Stäng inte det här fönstret.

3. Öppna appen Microsoft Authenticator, Välj **Lägg till konto** från ikonen **Anpassa och kontrol lera** längst upp till höger och välj sedan **annat konto (Google, Facebook osv.)**.

4. Använd enhetens kamera för att skanna QR-koden från sidan **Konfigurera autentisering** på din dator.

    >[!Note]
    >Om kameran inte fungerar som den ska kan du ange QR-koden och URL: en manuellt.

5. Granska sidan **konton** i Microsoft Authenticator-appen på enheten för att se till att din konto information är rätt och att det finns en tillhör ande sexsiffriga verifierings kod.

    För ytterligare säkerhet ändras verifierings koden var 30: e sekund som hindrar någon från att använda en kod flera gånger.

6. Välj **Nästa** på sidan **Konfigurera autentisering** på datorn, ange den sex-siffriga verifierings koden som anges i appen för ditt Google-konto och välj sedan **Verifiera**.

7. Ditt konto har verifierats och du kan välja **klart** för att stänga sidan **Konfigurera autentisering** .

    >[!NOTE]
    >Mer information om tvåstegsverifiering och ditt Google-konto finns i [Aktivera tvåstegsverifiering](https://support.google.com/accounts/answer/185839) och [Lär dig mer om tvåstegsverifiering](https://www.google.com/landing/2step/help.html).

## <a name="add-your-facebook-account"></a>Lägg till ditt Facebook-konto

Lägg till ditt Facebook-konto genom att aktivera tvåstegsverifiering och sedan lägga till kontot i appen.

### <a name="turn-on-two-factor-verification"></a>Aktivera tvåstegsverifiering

1. Öppna Facebook på din dator, Välj den nedrullningsbara menyn i det övre högra hörnet och gå sedan till **Inställningar**  >  **säkerhet och inloggning**.

    Sidan **säkerhet och inloggning** visas.

2. Gå ned till alternativet **Använd tvåfaktorautentisering** i avsnittet **tvåfaktorautentisering** och välj sedan **Redigera**.

    Sidan med **tvåfaktorautentisering** visas.

3. Välj **Aktivera**.

### <a name="add-your-facebook-account-to-the-app"></a>Lägg till ditt Facebook-konto i appen

1. På Facebook-sidan på din dator går du till avsnittet **Lägg till en säkerhets kopia** och väljer sedan **installation** från området för **autentiserings-app** .

    Du får en QR-kod som du kan använda för att automatiskt associera ditt konto med Microsoft Authenticator-appen. Stäng inte det här fönstret.

2. Öppna appen Microsoft Authenticator, Välj **Lägg till konto** från ikonen **Anpassa och kontrol lera** längst upp till höger och välj sedan **annat konto (Google, Facebook osv.)**.

3. Använd enhetens kamera för att skanna QR-koden från sidan med **två Factor Authentication** på din dator.

    >[!Note]
    >Om kameran inte fungerar som den ska kan du ange QR-koden och URL: en manuellt.

4. Granska sidan **konton** i Microsoft Authenticator-appen på enheten för att se till att din konto information är rätt och att det finns en tillhör ande sexsiffriga verifierings kod.

    För ytterligare säkerhet ändras verifierings koden var 30: e sekund som hindrar någon från att använda en kod flera gånger.

5. Välj **Nästa** på sidan med **två Factor Authentication** på datorn och ange sedan den sexsiffriga verifierings koden som anges i appen för ditt Facebook-konto.

    Ditt konto har verifierats och du kan nu använda appen för att verifiera ditt konto.

    >[!NOTE]
    >Mer information om tvåstegsverifiering och ditt Facebook-konto finns i [Vad är tvåfaktorautentisering och hur fungerar det?](https://www.facebook.com/help/148233965247823).

## <a name="add-your-github-account"></a>Lägg till ditt GitHub-konto

Lägg till GitHub-kontot genom att aktivera tvåstegsverifiering och sedan lägga till kontot i appen.

### <a name="turn-on-two-factor-verification"></a>Aktivera tvåstegsverifiering

1. Öppna GitHub på datorn, Välj bilden från det övre högra hörnet och välj sedan **Inställningar**.

    Sidan med **tvåfaktorautentisering** visas.

2. Välj **säkerhet** på panelen **personliga inställningar** och välj sedan **Aktivera tvåfaktorautentisering** från det **två faktort för autentisering** .

### <a name="add-your-github-account-to-the-app"></a>Lägg till ditt GitHub-konto i appen

1. På sidan med **tvåfaktorautentisering** på datorn väljer du **Konfigurera med hjälp av en app**.

2. Spara återställnings koderna så att du kan gå tillbaka till ditt konto om du förlorar åtkomsten och välj sedan **Nästa**.

    Du kan spara dina koder genom att hämta dem till enheten, genom att skriva ut en hård kopia eller genom att kopiera dem till ett verktyg för lösen ords hanteraren.

3. På sidan med **tvåfaktorautentisering** väljer du **Konfigurera med hjälp av en app**.

    Sidan ändras och visar dig en QR-kod. Stäng inte den här sidan.

4. Öppna appen Microsoft Authenticator, Välj **Lägg till konto** från ikonen **Anpassa och kontrol lera** längst upp till höger, Välj **annat konto (Google, Facebook osv.)** och välj sedan **Ange den här text koden** från texten överst på sidan.

    Microsoft Authenticator-appen kan inte skanna QR-koden, så du måste ange koden manuellt.

5. Ange ett **konto namn** (till exempel GitHub) och skriv den **hemliga nyckeln** från steg 4 och välj sedan **Slutför**.

6. På sidan med **dubbelriktad autentisering** på datorn anger du den sexsiffriga verifierings koden som finns i appen för ditt GitHub-konto och väljer sedan **Aktivera**.

    Sidan **konton** i appen visar ditt konto namn och en 6-siffrig verifierings kod. För ytterligare säkerhet ändras verifierings koden var 30: e sekund som hindrar någon från att använda en kod flera gånger.

    >[!NOTE]
    >Mer information om tvåstegsverifiering och ditt GitHub-konto finns i [om tvåfaktorautentisering](https://help.github.com/articles/about-two-factor-authentication/).

## <a name="add-your-amazon-account"></a>Lägg till ditt Amazon-konto

Lägg till ditt Amazon-konto genom att aktivera tvåstegsverifiering och sedan lägga till kontot i appen.

### <a name="turn-on-two-factor-verification"></a>Aktivera tvåstegsverifiering

1. På datorn öppnar du Amazon, väljer List rutan **konto & listor** och väljer sedan **ditt konto**.

2. Välj **Logga in & säkerhet**, logga in på ditt Amazon-konto och välj sedan **Redigera** i avsnittet **avancerade säkerhets inställningar** .

    Sidan **avancerade säkerhets inställningar** visas.

3. Välj **Kom igång**.

4. Välj **Authenticator-appen** på sidan **Välj hur du ska ta emot koder** .

    Sidan ändras och visar dig en QR-kod. Stäng inte den här sidan.

5. Öppna appen Microsoft Authenticator, Välj **Lägg till konto** från ikonen **Anpassa och kontrol lera** längst upp till höger och välj sedan **annat konto (Google, Facebook osv.)**.

6. Använd enhetens kamera för att skanna QR-koden från sidan **Välj hur du ska ta emot koder** på din dator.

    >[!Note]
    >Om kameran inte fungerar som den ska kan du ange QR-koden och URL: en manuellt.

7. Granska sidan **konton** i Microsoft Authenticator-appen på enheten för att se till att din konto information är rätt och att det finns en tillhör ande sexsiffriga verifierings kod.

    För ytterligare säkerhet ändras verifierings koden var 30: e sekund som hindrar någon från att använda en kod flera gånger.

8. På sidan **Välj hur du ska ta emot koder** på datorn anger du den sex-siffriga verifierings koden som finns i appen för ditt Amazon-konto och väljer sedan **verifiera kod och fortsätt**.

9. Slutför resten av registrerings processen, inklusive att lägga till en metod för säkerhets kopiering, till exempel ett textmeddelande, och välj sedan **Skicka kod**.

10. På sidan **Lägg till en verifierings metod för säkerhets kopiering** på datorn anger du den sexsiffriga verifierings koden som du fick från verifierings metoden för ditt Amazon-konto och väljer sedan **verifiera kod och fortsätt**.

11. På sidan **nästan klar** bestämmer du om du vill göra datorn till en betrodd enhet och välj sedan **Skaffa den. Aktivera Two-Step verifiering**.

    Sidan **avancerade säkerhets inställningar** visas och visar din uppdaterade information om tvåstegsverifiering.

    >[!NOTE]
    >Mer information om tvåstegsverifiering och ditt Amazon-konto finns i [om Two-Step verifiering](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) och [inloggning med Two-Step verifiering](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440).

## <a name="next-steps"></a>Nästa steg

- När du har lagt till dina konton i appen kan du logga in med Authenticator-appen på din enhet. Mer information finns i [Logga in med appen](user-help-auth-app-sign-in.md).

- För enheter som kör iOS kan du också säkerhetskopiera dina kontoautentiseringsuppgifter och relaterade appinställningar, till exempel ordningen på dina konton, i molnet. Mer information finns i [säkerhets kopiering och återställning med Microsoft Authenticator app](user-help-auth-app-backup-recovery.md).
