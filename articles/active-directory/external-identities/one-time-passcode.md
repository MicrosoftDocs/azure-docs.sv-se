---
title: Autentisering med eng ång slö sen ord för B2B-gäst användare – Azure AD
description: Använda email eng ång slö sen ord för att autentisera B2B-gäst användare utan att behöva en Microsoft-konto.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 12/18/2020
ms.author: mimart
author: msmimart
manager: CelesteDG
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1aebfaa176992b7e20824518bc214a6688ae493
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2020
ms.locfileid: "97703593"
---
# <a name="email-one-time-passcode-authentication"></a>E-postautentisering med eng ång slö sen ord

Den här artikeln beskriver hur du aktiverar autentisering med eng ång slö sen ord för B2B-gäst användare. Med funktionen för eng ång slö sen ord autentiseras B2B-gäst användare när de inte kan autentiseras via andra sätt som Azure AD, en Microsoft-konto (MSA) eller Google Federation. Med autentisering med eng ång slö sen ord behöver du inte skapa en Microsoft-konto. När gäst användaren löser in en inbjudan eller får åtkomst till en delad resurs, kan de begära en tillfällig kod som skickas till deras e-postadress. Sedan anger de den här koden för att fortsätta logga in.

![Översikts diagram över e-post med eng ång slö sen ord](media/one-time-passcode/email-otp.png)

> [!IMPORTANT]
> **Från mars 2021** aktive ras funktionen för eng ång slö sen ord för alla befintliga klienter och aktive ras som standard för nya klienter. Om du inte vill att den här funktionen ska aktive ras automatiskt kan du inaktivera den. Se [inaktivera e-post med eng ång slö sen ord](#disable-email-one-time-passcode) nedan.

> [!NOTE]
> Användare av eng ång slö sen ord måste logga in med en länk som innehåller klient kontexten (till exempel `https://myapps.microsoft.com/?tenantid=<tenant id>` eller `https://portal.azure.com/<tenant id>` , eller, om det är en verifierad domän `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com` ). Direkt länkar till program och resurser fungerar även så länge de omfattar klient kontexten. Gäst användare kan för närvarande inte logga in med slut punkter som inte har någon klient kontext. Om du till exempel `https://myapps.microsoft.com` använder `https://portal.azure.com` resulterar det i ett fel.

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Användar upplevelse för gäst användare med eng ång slö sen ord

När e-postfunktionen för eng ång slö sen ord är aktive rad kommer nyligen inbjudna användare [som uppfyller vissa villkor](#when-does-a-guest-user-get-a-one-time-passcode) att använda autentisering med eng ång slö sen ord. Gäst användare som har löst en inbjudan innan e-post med eng ång slö sen ord har Aktiver ATS kommer att fortsätta använda samma autentiseringsmetod.

Med autentisering med eng ång slö sen ord kan gäst användaren lösa in din inbjudan genom att klicka på en direkt länk eller via e-postinbjudan. I båda fallen indikerar ett meddelande i webbläsaren att en kod skickas till gäst användarens e-postadress. Gäst användaren väljer **Skicka kod**:

   ![Skärm bild som visar knappen Skicka kod](media/one-time-passcode/otp-send-code.png)

Ett lösen ord skickas till användarens e-postadress. Användaren hämtar lösen ordet från e-postmeddelandet och anger det i webbläsarfönstret:

   ![Skärm bild som visar sidan Ange tecken](media/one-time-passcode/otp-enter-code.png)

Gäst användaren är nu autentiserad och kan se den delade resursen eller fortsätta att logga in.

> [!NOTE]
> Lösen ord för eng ång slö sen ord är giltiga i 30 minuter. Efter 30 minuter är det angivna eng ång slö sen ord inte längre giltigt och användaren måste begära en ny. Användarsessioner upphör att gälla efter 24 timmar. Efter det tar gäst användaren emot ett nytt lösen ord när de får åtkomst till resursen. Sessionen upphör att gälla ger ytterligare säkerhet, särskilt när en gäst användare lämnar företaget eller inte längre behöver åtkomst.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>När får en gäst användare en eng ång slö sen ord?

När en gäst användare löser in en inbjudan eller använder en länk till en resurs som har delats med dem får de ett eng ång slö sen ord om:

- De har inget Azure AD-konto
- De har inte någon Microsoft-konto
- Den bjudande klienten har inte konfigurerat Google Federation för @gmail.com och @googlemail.com användare

Vid tidpunkten för inbjudan finns det ingen indikation på att användaren som du bjuder in ska använda autentisering med eng ång slö sen ord. Men när gäst användaren loggar in blir autentiseringen med eng ång slö sen ord reserv metoden om inga andra autentiseringsmetoder kan användas.

Du kan se om en gäst användare autentiseras med eng ång slö sen ord genom att visa **käll** egenskapen i användarens information. Gå till **Azure Active Directory**  >  **användare** i Azure Portal och välj sedan användaren för att öppna informations sidan.

![Skärm bild som visar en eng ång slö sen ord med käll värde för eng ång slö sen ord](media/one-time-passcode/guest-user-properties.png)

> [!NOTE]
> När en användare löser ett eng ång slö sen ord och senare hämtar ett MSA, ett Azure AD-konto eller ett annat federerat konto, fortsätter de att autentiseras med ett eng ång slö sen ord. Om du vill uppdatera deras autentiseringsmetod kan du ta bort deras gäst användar konto och bjuda in dem på samma sätt.

### <a name="example"></a>Exempel

Gäst användare teri@gmail.com bjuds in till Fabrikam, som inte har konfigurerat Google Federation. Teri har inte någon Microsoft-konto. De får ett eng ång slö sen ord för autentisering.

## <a name="disable-email-one-time-passcode"></a>Inaktivera e-post med eng ång slö sen ord

Från mars 2021 aktive ras funktionen för eng ång slö sen ord för alla befintliga klienter och aktive ras som standard för nya klienter. Vid detta tillfälle kommer Microsoft inte längre att stödja inlösen av inbjudningar genom att skapa ohanterade ("virus" eller "just-in-Time") Azure AD-konton och-klienter för B2B-samarbets scenarier. Vi aktiverar e-postfunktionen för eng ång slö sen ord eftersom den ger en sömlös reserv metod för gäst användare. Men du kan inaktivera den här funktionen om du väljer att inte använda den.

> [!NOTE]
>
> Om e-postfunktionen för eng ång slö sen ord har Aktiver ATS i din klient och du stänger av den, kommer alla gäst användare som har löst ett eng ång slö sen ord inte att kunna logga in. Du kan ta bort gäst användaren och bjuda in dem igen så att de kan logga in igen med en annan autentiseringsmetod.

### <a name="to-disable-the-email-one-time-passcode-feature"></a>Så här inaktiverar du e-postfunktionen för eng ång slö sen ord

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD.

2. I navigerings fönstret väljer du **Azure Active Directory**.

3. Välj **externa identiteter**  >  **externa samarbets inställningar**.

4. Under **email eng ång slö sen ord för gäster** väljer du **inaktivera e-post med eng ång slö sen ord för gäster**.

    ![E-postinställningar för eng ång slö sen ord](media/one-time-passcode/otp-admin-settings.png)

   > [!NOTE]
   > Om du ser följande växling i stället för de alternativ som visas ovan innebär det att du tidigare har aktiverat, inaktiverat eller valt i för hands versionen av funktionen. Välj **Nej** om du vill inaktivera funktionen.
   >
   >![Aktivera e-post för eng ång slö sen ord](media/delegate-invitations/enable-email-otp-opted-in.png)

5. Välj **Spara**.

## <a name="note-for-public-preview-customers"></a>Obs! för kunder med offentlig för hands version

Om du tidigare har valt att använda e-postlösenordet med eng ång slö sen ord, är det datum mars 2021 för automatisk funktions aktivering inte tillämpligt för dig, så dina relaterade affärs processer påverkas inte. Dessutom visas inte alternativet för att automatiskt aktivera e-postlösenord med **eng ång slö sen ord för gäster i mars 2021**, under e-postlösenord med **eng ång slö sen ord för gäster** i Azure Portal. I stället visas följande **Ja** eller **ingen** växling:

![Aktivera e-post för eng ång slö sen ord](media/delegate-invitations/enable-email-otp-opted-in.png)

Men om du hellre vill inaktivera funktionen och tillåta att den automatiskt aktive ras i mars 2021, kan du återgå till standardinställningarna med hjälp av [resurs typen konfiguration av Microsoft Graph-API email Authentication](https://aka.ms/exid-graphemailauth). När du har återställt standardinställningarna kommer följande alternativ att vara tillgängliga under **email eng ång slö sen ord för gäster**:

- **Aktivera automatiskt e-postlösenord för gäster i mars 2021**. Objekt Om e-postfunktionen för eng ång slö sen ord inte redan är aktive rad för din klient, aktive ras den automatiskt den 1 mars 2021. Ingen ytterligare åtgärd krävs om du vill att funktionen ska vara aktive rad vid den tiden. Om du redan har aktiverat eller inaktiverat funktionen kommer det här alternativet att vara otillgängligt.

- **Aktivera e-post med eng ång slö sen ord för gäster gällande nu**. Aktiverar funktionen email eng ång slö sen ord för din klient.

- **Inaktivera e-post med eng ång slö sen ord för gäster**. Inaktiverar funktionen för eng ång slö sen ord för din klient och förhindrar att funktionen aktive ras i mars 2021.
