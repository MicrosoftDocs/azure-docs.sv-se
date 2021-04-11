---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med mobil program för arbets dagar | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och arbets dagarnas mobil program.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: 94ab61a5c6335d43842ada48f17572c186af773c
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077201"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med mobil program för arbets dagar

I den här självstudien får du lära dig hur du integrerar Azure Active Directory (Azure AD), villkorlig åtkomst och Intune med mobil program arbets dagar. När du integrerar ett Workday-mobil program med Microsoft kan du:

* Se till att enheterna är kompatibla med dina principer innan du loggar in.
* Lägg till kontroller i arbets dagar mobil program för att säkerställa att användarna på ett säkert sätt kommer åt företags data. 
* Kontroll i Azure AD som har åtkomst till Workday.
* Gör det möjligt för användarna att logga in automatiskt på Workday med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

Så här kommer du igång:

* Integrera arbets dagar med Azure AD.
* Läs [Azure Active Directory enkel inloggning (SSO) med Workday](./workday-tutorial.md).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du principer för villkorlig åtkomst för Azure AD och Intune med mobil program för arbets dagar.

För att aktivera enkel inloggning (SSO) kan du konfigurera ett externt arbets dag program med Azure AD. Mer information finns i [Azure Active Directory integration med enkel inloggning (SSO) med Workday](./workday-tutorial.md).

> [!NOTE] 
> Workday stöder inte app Protection-principerna i Intune. Du måste använda hantering av mobila enheter för att använda villkorlig åtkomst.


## <a name="ensure-users-have-access-to-workday-mobile-application"></a>Se till att användarna har åtkomst till mobil programmet för arbets dagar

Konfigurera arbets dagar för att tillåta åtkomst till sina mobilappar. Du måste konfigurera följande principer för arbets dagarnas mobila enheter:

1. Gå till säkerhets principer för domäner för funktionellt Area-rapport.
1. Välj lämplig säkerhets princip:
    * Mobil användning – Android
    * Mobil användning – iPad
    * Mobil användning – iPhone
1. Välj **Redigera behörigheter**.
1. Markera kryss rutan **Visa eller ändra** om du vill ge säkerhets grupperna åtkomst till rapporten eller uppgiften skydds bara objekt.
1. Markera kryss rutan **Hämta eller placera** för att ge säkerhets grupper åtkomst till integrering och rapport-eller aktivitets skydds bara åtgärder.

Aktivera väntande säkerhets princip ändringar genom att köra **Aktivera väntande säkerhets princip ändringar**.

## <a name="open-workday-sign-in-page-in-workday-mobile-browser"></a>Öppna inloggnings sidan för arbets dagar i mobil arbets dag mobil webbläsare

Du måste öppna appen i en extern webbläsare för att kunna använda villkorlig åtkomst för mobil program arbets dagar. I **Redigera klient konfiguration – säkerhet** väljer du **Aktivera mobil webbläsarens SSO för inbyggda appar**. Detta kräver att en webbläsare som godkänts av Intune installeras på enheten för iOS och i arbets profilen för Android.

![Skärm bild av inloggning för mobil webbläsare i arbets dagar.](./media/workday-tutorial/mobile-browser.png)

## <a name="set-up-conditional-access-policy"></a>Konfigurera en princip för villkorlig åtkomst

Den här principen påverkar bara inloggning på en iOS-eller Android-enhet. Om du vill utöka den till alla plattformar väljer du **vilken enhet som helst**. Den här principen kräver att enheten är kompatibel med principen och verifierar detta via Intune. Eftersom Android har arbets profiler, blockerar detta alla användare från att logga in på Workday, om de inte loggar in via sin arbets profil och har installerat appen via företags portalen för Intune. Det finns ett ytterligare steg för iOS för att se till att samma situation gäller.

Workday stöder följande åtkomst kontroller:
* Kräv multifaktorautentisering
* Kräv att enheten ska markeras som kompatibel

Workday-appen stöder inte följande:
* Kräv godkänd klientapp
* Kräv app Protection-princip (förhands granskning)

Utför följande steg för att konfigurera en arbets dag som en hanterad enhet:

![Skärm bild av enbart hanterade enheter och molnappar eller-åtgärder.](./media/workday-tutorial/managed-devices-only.png)

1. Välj **Start**  >  **Microsoft Intune**  >  **villkorlig åtkomst-principer**. Välj **endast hanterade enheter**. 

1. I **hanterade enheter** väljer du endast **hanterade enheter** under **namn** och väljer sedan **molnappar eller åtgärder**.

1. I **molnappar eller-åtgärder**:

    a. Växla **Välj vad den här principen gäller** för **molnappar**.

    b. I **Inkludera** väljer du **Välj appar**.

    c. Välj **arbets dag** i listan **Välj** .

    d. Välj **Klar**.

1. Växla **Aktivera princip** till **på**.

1. Välj **Spara**.

Utför följande steg för att **bevilja** åtkomst:

![Skärm bild av enbart hanterade enheter och beviljande.](./media/workday-tutorial/managed-devices-only-2.png)

1. Välj **Start**  >  **Microsoft Intune**  >  **villkorlig åtkomst-principer**. Välj **endast hanterade enheter**. 

1. Välj endast **hanterade enheter** under **namn** i **hanterade enheter**. Under **Åtkomstkontroller** väljer du **Bevilja**.

1. I **bevilja**:

    a. Välj de kontroller som ska tillämpas som **beviljad åtkomst**.

    b. Välj **Kräv att enheten är markerad som kompatibel**.

    c. Välj **Kräv en av de valda kontrollerna**.

    d. Välj **Välj**.

1. Växla **Aktivera princip** till **på**.

1. Välj **Spara**.

## <a name="set-up-device-compliance-policy"></a>Konfigurera efterlevnadsprincip för enheter

För att säkerställa att iOS-enheter bara kan logga in via arbets dagar som hanteras av hantering av mobila enheter, måste du blockera appen App Store genom att lägga till **com. workday. workdayapp** i listan över begränsade appar. Detta säkerställer att endast enheter som har arbets dagar som har installerats via företags portalen kan komma åt Workday. För webbläsaren kan enheterna bara komma åt Workday om enheten hanteras av Intune och använder en hanterad webbläsare.

![Skärm bild av policyn för efterlevnadsprinciper för iOS-enheter.](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-intune-app-configuration-policies"></a>Konfigurera konfigurations principer för Intune-appar

| Scenario | Nyckel värdes par |
|----------------------------------------------------------------------------------------   |-----------|
| Fyll i fälten för klient och webb adress automatiskt för:<br>● Arbets dag på Android när du aktiverar Android for Work-profiler.<br>● Arbets dag på iPad och iPhone.     | Använd de här värdena för att konfigurera din klient: <br>● Konfigurations nyckel = `UserGroupCode`<br>● Värde typ = sträng <br>● Konfigurations värde = ditt klient namn. Exempel: `gms`<br>Använd följande värden för att konfigurera webb adressen:<br>● Konfigurations nyckel = `AppServiceHost`<br>● Värde typ = sträng<br>● Konfigurations värde = den grundläggande URL: en för din klient. Exempel: `https://www.myworkday.com`                                | 
| Inaktivera de här åtgärderna för Workday på iPad och iPhone:<br>● Klipp ut, kopiera och klistra in<br>● Utskrift                       | Ange värdet (Boolean) till `False` på dessa nycklar för att inaktivera funktionen:<br>●   `AllowCutCopyPaste`<br>●    `AllowPrint`    |
| Inaktivera skärm bilder för arbets dagar på Android. |Ange värdet (Boolean) till `False` på `AllowScreenshots` nyckeln för att inaktivera funktionen.|
| Inaktivera föreslagna uppdateringar för dina användare.|Ange värdet (Boolean) till `False` på `AllowSuggestedUpdates` nyckeln för att inaktivera funktionen.|
|Anpassa App Store-URL: en för att dirigera mobila användare till valfri App Store.|Använd de här värdena för att ändra App Store-URL:<br>● Konfigurations nyckel = `AppUpdateURL`<br>● Värde typ = sträng<br> ● Konfigurations värde = App Store-URL|

## <a name="ios-configuration-policies"></a>Principer för iOS-konfiguration

1. Gå till [Azure-portalen](https://portal.azure.com/) och logga in.
1. Sök efter **Intune** eller Välj widgeten i listan.
1. Gå till **klient appar**  >  **appar**  >  **konfigurations principer** för appar. Välj sedan **+ Lägg till**  >  **hanterade enheter**.
1. Ange ett namn.
1. Under **plattform** väljer du **iOS/iPad**.
1. Under **associerad app** väljer du den arbets dag för iOS-app som du har lagt till.
1. Välj **konfigurations inställningar**. Under **konfigurations inställnings format** väljer du **Ange XML-data**.
1. Här är en exempel-XML-fil. Lägg till de konfigurationer som du vill använda. Ersätt `STRING_VALUE` med den sträng som du vill använda. Ersätt `<true /> or <false />` med `<true />` eller  `<false />` . Om du inte lägger till en konfiguration fungerar det här exemplet som det är inställt på `True` .

    ```
    <dict>
    <key>UserGroupCode</key>
    <string>STRING_VALUE</string>
    <key>AppServiceHost</key>
    <string>STRING_VALUE</string>
    <key>AllowCutCopyPaste</key>
    <true /> or <false />
    <key>AllowPrint</key>
    <true /> or <false />
    <key>AllowSuggestedUpdates</key>
    <true /> or <false />
    <key>AppUpdateURL</key>
    <string>STRING_VALUE</string>
    </dict>

    ```
1. Välj **Lägg till**.
1. Uppdatera sidan och välj den nyligen skapade principen.
1. Välj **tilldelningar** och välj vem du vill att appen ska gälla för.
1. Välj **Spara**.

## <a name="android-configuration-policies"></a>Principer för Android-konfiguration

1. Gå till [Azure-portalen](https://portal.azure.com/) och logga in.
2. Sök efter **Intune** eller Välj widgeten i listan.
3. Gå till **klient appar**  >  **appar**  >  **konfigurations principer** för appar. Välj sedan **+ Lägg till**  >  **hanterade enheter**.
5. Ange ett namn. 
6. Under **plattform** väljer du **Android**.
7. Under **associerad app** väljer du den arbets dag för Android-app som du har lagt till.
8. Välj **konfigurations inställningar**. Under **konfigurations inställnings format** väljer du **Ange JSON-data**.
