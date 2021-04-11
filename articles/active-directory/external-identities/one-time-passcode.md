---
title: Autentisering med eng ång slö sen ord för B2B-gäst användare – Azure AD
description: Använda email eng ång slö sen ord för att autentisera B2B-gäst användare utan att behöva en Microsoft-konto.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/06/2021
ms.author: mimart
author: msmimart
manager: CelesteDG
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30f22282b00a7ead2e19805f32d78338126e8087
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552758"
---
# <a name="email-one-time-passcode-authentication"></a>E-postautentisering med eng ång slö sen ord

Den här artikeln beskriver hur du aktiverar autentisering med eng ång slö sen ord för B2B-gäst användare. Med funktionen för eng ång slö sen ord autentiseras B2B-gäst användare när de inte kan autentiseras via andra sätt som Azure AD, en Microsoft-konto (MSA) eller Google Federation. Med autentisering med eng ång slö sen ord behöver du inte skapa en Microsoft-konto. När gäst användaren löser in en inbjudan eller får åtkomst till en delad resurs, kan de begära en tillfällig kod som skickas till deras e-postadress. Sedan anger de den här koden för att fortsätta logga in.

![Översikts diagram över e-post med eng ång slö sen ord](media/one-time-passcode/email-otp.png)

> [!IMPORTANT]
> - Från och med 1 **oktober 2021** aktive ras funktionen för eng ång slö sen ord för alla befintliga klienter och aktive ras som standard för nya klienter. Om du inte vill att den här funktionen ska aktive ras automatiskt kan du inaktivera den. Se [inaktivera e-post med eng ång slö sen ord](#disable-email-one-time-passcode) nedan.
> - E-postinställningar för eng ång slö sen ord har flyttats i Azure Portal från **externa samarbets inställningar** till **alla identitets leverantörer**.

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
> När en användare löser ett eng ång slö sen ord och senare hämtar ett MSA, ett Azure AD-konto eller ett annat federerat konto, fortsätter de att autentiseras med ett eng ång slö sen ord. Om du vill uppdatera användarens autentiseringsmetod kan du [återställa deras inlösnings status](reset-redemption-status.md).

### <a name="example&quot;></a>Exempel

Gäst användare teri@gmail.com bjuds in till Fabrikam, som inte har konfigurerat Google Federation. Teri har inte någon Microsoft-konto. De får ett eng ång slö sen ord för autentisering.

## <a name=&quot;disable-email-one-time-passcode&quot;></a>Inaktivera e-post med eng ång slö sen ord

Från och med 1 oktober 2021 aktive ras funktionen för eng ång slö sen ord för alla befintliga klienter och aktive ras som standard för nya klienter. Vid detta tillfälle kommer Microsoft inte längre att stödja inlösen av inbjudningar genom att skapa ohanterade (&quot;virus&quot; eller &quot;just-in-Time") Azure AD-konton och-klienter för B2B-samarbets scenarier. Vi aktiverar e-postfunktionen för eng ång slö sen ord eftersom den ger en sömlös reserv metod för gäst användare. Men du kan inaktivera den här funktionen om du väljer att inte använda den.

> [!NOTE]
>
> Om e-postfunktionen för eng ång slö sen ord har Aktiver ATS i din klient och du stänger av den, kommer alla gäst användare som har löst ett eng ång slö sen ord inte att kunna logga in. Du kan [återställa deras inlösnings status](reset-redemption-status.md) så att de kan logga in igen med en annan autentiseringsmetod.

### <a name="to-disable-the-email-one-time-passcode-feature"></a>Så här inaktiverar du e-postfunktionen för eng ång slö sen ord

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD.

2. I navigerings fönstret väljer du **Azure Active Directory**.

3. Välj **externa identiteter**  >  **alla identitets leverantörer**.

4. Välj **e-post med eng ång slö sen ord** och välj sedan **inaktivera e-post med eng ång slö sen ord för gäster**.

   > [!NOTE]
   > E-postinställningar för eng ång slö sen ord har flyttats i Azure Portal från **externa samarbets inställningar** till **alla identitets leverantörer**.
   > Om du ser en växling i stället för e-postalternativ med eng ång slö sen ord innebär detta att du tidigare har aktiverat, inaktiverat eller valt att förhandsgranska funktionen. Välj **Nej** om du vill inaktivera funktionen.
   >
   >![Växla e-post med eng ång slö sen ord](media/one-time-passcode/enable-email-otp-disabled.png)

5. Välj **Spara**.

## <a name="note-for-public-preview-customers"></a>Obs! för kunder med offentlig för hands version

Om du tidigare har valt att använda e-postlösenordet med eng ång slö sen ord, är datumet för automatisk funktion i oktober 2021 för automatisk funktion inte tillämpligt, så dina relaterade affärs processer påverkas inte. Dessutom visas inte alternativet för att automatiskt aktivera e-postlösenord med **eng ång slö sen ord för gäster som börjar i oktober 2021**, under e-postlösenorden **eng ång slö sen ord för gäster** i Azure Portal. I stället visas följande **Ja** eller **ingen** växling:

![E-post med eng ång slö sen ord](media/one-time-passcode/enable-email-otp-opted-in.png)

Men om du hellre vill inaktivera funktionen och tillåta att den aktive ras automatiskt i oktober 2021, kan du återgå till standardinställningarna med hjälp av [resurs typen konfiguration av Microsoft Graph-API email Authentication](/graph/api/resources/emailauthenticationmethodconfiguration). När du har återställt standardinställningarna kommer följande alternativ att vara tillgängliga under **email eng ång slö sen ord för gäster**:

![Aktivera e-post för eng ång slö sen ord](media/one-time-passcode/email-otp-options.png)

- **Aktivera e-post med eng ång slö sen ord för gäster som börjar i oktober 2021**. Objekt Om e-postfunktionen för eng ång slö sen ord inte redan är aktive rad för din klient, aktive ras den automatiskt första oktober 2021. Ingen ytterligare åtgärd krävs om du vill att funktionen ska vara aktive rad vid den tiden. Om du redan har aktiverat eller inaktiverat funktionen kommer det här alternativet att vara otillgängligt.

- **Aktivera e-post med eng ång slö sen ord för gäster gällande nu**. Aktiverar funktionen email eng ång slö sen ord för din klient.

- **Inaktivera e-post med eng ång slö sen ord för gäster**. Inaktiverar funktionen för eng ång slö sen ord för din klient och förhindrar att funktionen aktive ras i oktober 2021.

## <a name="note-for-azure-us-government-customers"></a>Observera för Azure-kunder med amerikanska myndigheter

Funktionen email eng ång slö sen ord är inaktive rad som standard i Azure-molnet för amerikanska myndigheter.  

 ![E-post med eng ång slö sen ord](media/one-time-passcode/enable-email-otp-disabled.png)

Aktivera funktionen för eng ång slö sen ord i Azure-molnet för amerikanska myndigheter:

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör för Azure AD.
2. I navigerings fönstret väljer du **Azure Active Directory**.
3. Välj inställningar för **organisations relationer**   >  ****.

   > [!NOTE]
   > - Om du inte ser **organisations relationer** söker du efter "externa identiteter" i Sök fältet längst upp.

4. Välj **e-post med eng ång slö sen ord** och välj sedan **Ja**.
5. Välj **Spara**.

Mer information om aktuella begränsningar finns i [Azures moln för amerikanska myndigheter](current-limitations.md#azure-us-government-clouds).