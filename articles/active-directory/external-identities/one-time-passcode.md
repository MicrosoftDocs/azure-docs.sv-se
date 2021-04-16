---
title: Autentisering med ett lösenord för B2B-gästanvändare – Azure AD
description: Så här använder du ett e-postanvändarkod för att autentisera B2B-gästanvändare utan att behöva Microsoft-konto.
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
ms.openlocfilehash: 3b4089559b341dd268928b1f150b6fc173869ead
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529919"
---
# <a name="email-one-time-passcode-authentication"></a>Autentisering med e-post med ett lösenord

I den här artikeln beskrivs hur du aktiverar autentisering med e-postkoder för B2B-gästanvändare. Funktionen för lösenord för e-post autentiserar B2B-gästanvändare när de inte kan autentiseras via andra metoder som Azure AD, en Microsoft-konto (MSA) eller Google-federation. Med autentisering med ett lösenord behöver du inte skapa en Microsoft-konto. När gästanvändaren löser in en inbjudan eller får åtkomst till en delad resurs kan han eller hon begära en tillfällig kod som skickas till deras e-postadress. Sedan anger de den här koden för att fortsätta logga in.

![Översiktsdiagram över e-post med lösenord vid en tidpunkt](media/one-time-passcode/email-otp.png)

> [!IMPORTANT]
> - **Från oktober 2021** aktiveras funktionen för e-postkoder en gång för alla befintliga klienter och aktiveras som standard för nya klienter. Om du inte vill tillåta att den här funktionen aktiveras automatiskt kan du inaktivera den. Se [Inaktivera lösenord för e-post en gång](#disable-email-one-time-passcode) nedan.
> - Inställningarna för lösenord för e-post har flyttats i Azure Portal inställningar **för externt samarbete** till Alla **identitetsproviders.**

> [!NOTE]
> Användare av ett lösenord för en gång måste logga in med en länk som innehåller klientkontexten (till exempel eller , eller i fallet med en `https://myapps.microsoft.com/?tenantid=<tenant id>` `https://portal.azure.com/<tenant id>` verifierad domän, `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com` ). Direktlänkar till program och resurser fungerar också så länge de omfattar klientkontexten. Gästanvändare kan för närvarande inte logga in med slutpunkter som inte har någon klientkontext. Om du till `https://myapps.microsoft.com` exempel använder resulterar det i ett `https://portal.azure.com` fel.

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Användarupplevelse för gästanvändare med ett lösenord

När funktionen för lösenord för e-post är [](#when-does-a-guest-user-get-a-one-time-passcode) aktiverad använder nyligen inbjudna användare som uppfyller vissa villkor autentisering med ett lösenord. Gästanvändare som löste in en inbjudan innan e-postlösenordet aktiverades fortsätter att använda samma autentiseringsmetod.

Med autentisering med ett lösenord kan gästanvändaren lösa in din inbjudan genom att klicka på en direktlänk eller genom att använda e-postinbjudan. I båda fallen anger ett meddelande i webbläsaren att en kod kommer att skickas till gästanvändarens e-postadress. Gästanvändaren väljer **Skicka kod:**

   ![Skärmbild som visar knappen Skicka kod](media/one-time-passcode/otp-send-code.png)

Ett lösenord skickas till användarens e-postadress. Användaren hämtar lösenordet från e-postmeddelandet och anger det i webbläsarfönstret:

   ![Skärmbild som visar sidan Ange kod](media/one-time-passcode/otp-enter-code.png)

Gästanvändaren autentiseras nu och kan se den delade resursen eller fortsätta att logga in.

> [!NOTE]
> Lösenordet för en gång är giltigt i 30 minuter. Efter 30 minuter är det specifika lösenordet inte längre giltigt och användaren måste begära ett nytt lösenord. Användarsessioner upphör att gälla efter 24 timmar. Efter det får gästanvändaren ett nytt lösenord när de får åtkomst till resursen. Sessionsförfallotid ger ökad säkerhet, särskilt när en gästanvändare lämnar företaget eller inte längre behöver åtkomst.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>När får en gästanvändare ett enda lösenord?

När en gästanvändare löser in en inbjudan eller använder en länk till en resurs som har delats med dem får de ett lösenord en gång om:

- De har inget Azure AD-konto
- De har ingen Microsoft-konto
- Den inbjudande klientorganisationen konfigurerade inte Google-federation för @gmail.com - och @googlemail.com -användare

Vid tidpunkten för inbjudan finns det ingen indikation på att användaren som du bjuder in använder autentisering med ett lösenord. Men när gästanvändaren loggar in är autentisering med ett lösenord reservmetoden om inga andra autentiseringsmetoder kan användas.

Du kan se om en gästanvändare autentiseras med ett enda lösenord genom att visa egenskapen **Källa** i användarens information. I Azure Portal du till **Azure Active Directory**  >  **användare** och väljer sedan användaren för att öppna informationssidan.

![Skärmbild som visar en engångslösenordsanvändare med källvärdet OTP](media/one-time-passcode/guest-user-properties.png)

> [!NOTE]
> När en användare löser in ett lösenord en gång och senare får ett MSA-, Azure AD-konto eller ett annat federerat konto fortsätter autentiseringen att ske med ett enda lösenord. Om du vill uppdatera användarens autentiseringsmetod kan du återställa [inlösningsstatusen](reset-redemption-status.md).

### <a name="example&quot;></a>Exempel

teri@gmail.comGästanvändaren bjuds in till Fabrikam, som inte har Google-federation konfigurerad. Teri har ingen Microsoft-konto. De får ett enda lösenord för autentisering.

## <a name=&quot;disable-email-one-time-passcode&quot;></a>Inaktivera lösenord för e-post en gång

Från oktober 2021 aktiveras funktionen för e-postkoder en gång för alla befintliga klienter och aktiveras som standard för nya klienter. Vid den tidpunkten kommer Microsoft inte längre att stödja inlösning av inbjudningar genom att skapa ohanterade (&quot;virala&quot; eller &quot;just-in-time") Azure AD-konton och -klienter för B2B-samarbetsscenarier. Vi aktiverar funktionen för e-postlösenord vid ett tillfälle eftersom den ger en sömlös reservautentiseringsmetod för dina gästanvändare. Du kan dock välja att inaktivera den här funktionen om du väljer att inte använda den.

> [!NOTE]
>
> Om funktionen för e-postlösenord har aktiverats i din klientorganisation och du inaktiverar den, kommer gästanvändare som har löst in ett lösenord för en gång inte att kunna logga in. Du kan [återställa deras inlösningsstatus](reset-redemption-status.md) så att de kan logga in igen med en annan autentiseringsmetod.

### <a name="to-disable-the-email-one-time-passcode-feature"></a>Så här inaktiverar du funktionen för e-postkoder en gång

1. Logga in på [Azure Portal](https://portal.azure.com/) azure AD global administratör.

2. I navigeringsfönstret väljer du **Azure Active Directory**.

3. Välj **External Identities Alla**  >  **identitetsproviders.**

4. Välj **E-post för ett lösenord och** välj sedan Inaktivera **e-post för gäster.**

   > [!NOTE]
   > Inställningarna för ett lösenord för e-post har flyttats i Azure Portal inställningar **för externt samarbete** till Alla **identitetsproviders.**
   > Om du ser en växlingsknapp i stället för alternativen för lösenord för e-post en gång innebär det att du tidigare har aktiverat, inaktiverat eller valt att använda förhandsversionen av funktionen. Välj **Nej** för att inaktivera funktionen.
   >
   >![Växlingsknapp för e-post för lösenord har inaktiverats](media/one-time-passcode/enable-email-otp-disabled.png)

5. Välj **Spara**.

## <a name="note-for-public-preview-customers"></a>Obs! För kunder med offentlig förhandsversion

Om du tidigare har valt att använda e-postmeddelandets offentliga förhandsversion gäller inte datumet för oktober 2021 för automatisk funktionsaktivering för dig, så dina relaterade affärsprocesser påverkas inte. I Azure Portal visas inte alternativet Att automatiskt aktivera lösenord för e-post för gäster från och med oktober **2021** under egenskaperna **E-post** för gäster. I stället visas följande **ja- eller** **nej-växlingsknapp:**

![Avanmält lösenord via e-post](media/one-time-passcode/enable-email-otp-opted-in.png)

Men om du föredrar att avanmäla dig från funktionen och tillåta att den aktiveras automatiskt i oktober 2021 [](/graph/api/resources/emailauthenticationmethodconfiguration)kan du återgå till standardinställningarna med hjälp av resurstypen för konfigurationsresursen för Microsoft Graph API-e-postautentiseringsmetoden. När du har återställt till standardinställningarna är följande alternativ tillgängliga under **E-post för** gäster vid ett tillfälle:

![Aktivera ett lösenord för e-post som har avanmälts](media/one-time-passcode/email-otp-options.png)

- **Aktivera automatiskt e-post med ett lösenord för gäster från och med oktober 2021.** (Standard) Om funktionen för ett lösenord för e-post inte redan är aktiverad för din klientorganisation aktiveras den automatiskt från och med oktober 2021. Ingen ytterligare åtgärd krävs om du vill att funktionen ska aktiveras vid den tidpunkten. Om du redan har aktiverat eller inaktiverat funktionen är det här alternativet inte tillgängligt.

- **Aktivera lösenord för e-post en gång för gäster som gäller nu**. Aktiverar lösenordsfunktionen för e-post en gång för din klientorganisation.

- **Inaktivera e-post med ett lösenord för gäster**. Inaktiverar funktionen för e-postkoder en gång för din klientorganisation och förhindrar att funktionen kan aktivera i oktober 2021.

## <a name="note-for-azure-us-government-customers"></a>Anmärkning för Azure US Government-kunder

Funktionen för lösenord för e-post är inaktiverad som standard i Azure US Government-molnet. Dina partner kan inte logga in om inte den här funktionen är aktiverad. Till skillnad från det offentliga Azure-molnet stöder inte Azure US Government-molnet inlösning av inbjudningar med självbetjäning Azure Active Directory-konton.

 ![E-post för ett lösenord inaktiverat](media/one-time-passcode/enable-email-otp-disabled.png)

Så här aktiverar du funktionen för e-postkoder en gång i Azure US Government-molnet:

1. Logga in på [Azure Portal](https://portal.azure.com) azure AD global administratör.
2. I navigeringsfönstret väljer du **Azure Active Directory**.
3. Välj **Organisationsrelationer**   >  **Alla identitetsproviders.**

   > [!NOTE]
   > - Om du inte ser **Organisationsrelationer** söker du efter "External Identities" i sökfältet längst upp.

4. Välj **E-post för ett lösenord och** välj sedan **Ja.**
5. Välj **Spara**.

Mer information om aktuella begränsningar finns i [Azure US Government-moln.](current-limitations.md#azure-us-government-clouds)
