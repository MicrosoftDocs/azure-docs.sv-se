---
title: Konfigurera en Apps utgivare domän | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du konfigurerar ett programs utgivares domän så att användare vet var deras information skickas.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/23/2020
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.openlocfilehash: 9b3840ffb39a204cfa24dcb0430f20ac16587582
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100104186"
---
# <a name="how-to-configure-an-applications-publisher-domain"></a>Gör så här: Konfigurera ett programs utgivares domän

Ett programs utgivares domän visas för användare i [appens godkännande fråga](application-consent-experience.md) för att meddela användarna om var informationen skickas. Program med flera klienter som registreras efter den 21 maj 2019 och som inte har någon utgivar domän som inte är **verifierad**. Program med flera klienter är program som stöder konton utanför en enda organisations katalog. till exempel stöd för alla Azure AD-konton eller stöd för alla Azure AD-konton och personliga Microsoft-konton.

## <a name="new-applications"></a>Nya program

När du registrerar en ny app kan utgivarens domän i appen ställas in på ett standardvärde. Värdet beror på var appen är registrerad, särskilt om appen är registrerad i en klient organisation och om klient organisationen har verifierade domäner.

Om det finns domänkontrollanter som har autentiserats av klienten, kommer appens utgivares domän som standard till den primära verifierade domänen för klient organisationen. Om det inte finns några domäner som verifieras av klienten (vilket är fallet när programmet inte är registrerat i en klient), anges appens utgivare domän till null.

I följande tabell sammanfattas standard beteendet för utgivar domänens värde.  

| Domäner som verifieras av klienter | Standardvärdet för Publisher-domän |
|-------------------------|----------------------------|
| null | null |
| *. onmicrosoft.com | *. onmicrosoft.com |
| -*. onmicrosoft.com<br/>– domain1.com<br/>-domain2.com (primär) | domain2.com |

Om ett program från en annan klient organisation inte är inställt, eller om det är inställt på en domän som slutar på. onmicrosoft.com, visas appens medgivande- **fråga i stället** för utgivarens domän.

## <a name="grandfathered-applications"></a>Grandfathered-program

Om din app registrerades före den 21 maj 2019 visas inte appens **medgivande om du** inte har angett en Publisher-domän. Vi rekommenderar att du anger utgivarens domän värde så att användarna kan se den här informationen i appens medgivande fråga.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Konfigurera Publisher-domän med hjälp av Azure Portal

Följ de här stegen om du vill ställa in din apps publicerings domän.

1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure-portalen</a>.
1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: på den översta menyn för att välja den klient som appen är registrerad i.
1. Navigera till [Azure Active Directory > Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) för att söka efter och välja den app som du vill konfigurera.

   När du har valt appen ser du appens **översikts** sida.
1. Under **Hantera** väljer du **anpassning**.
1. Hitta fältet för **utgivarens domän** och välj ett av följande alternativ:

   - Välj **Konfigurera en domän** om du inte redan har konfigurerat en domän.
   - Välj **Uppdatera domän** om en domän redan har kon figurer ATS.

Om din app är registrerad i en klient, ser du två flikar att välja mellan: **Välj en verifierad domän** och **Verifiera en ny domän**.

Om din app inte är registrerad i en klient, ser du bara alternativet för att verifiera en ny domän för programmet.

### <a name="to-verify-a-new-domain-for-your-app"></a>Så här verifierar du en ny domän för din app

1. Skapa en fil med namnet `microsoft-identity-association.json` och klistra in följande JSON-kodfragment.

   ```json
   {
      "associatedApplications": [
         {
            "applicationId": "{YOUR-APP-ID-HERE}"
         },
         {
            "applicationId": "{YOUR-OTHER-APP-ID-HERE}"
         }
      ]
    }
   ```

1. Ersätt plats hållaren *{Your-app-ID-här}* med det program-ID (klient) som motsvarar din app.

1. Var värd för filen på: `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json` . Ersätt plats hållaren *{your-domain-här}* så att den matchar den verifierade domänen.

1. Klicka på knappen **Verifiera och spara domän** .

### <a name="to-select-a-verified-domain"></a>Så här väljer du en verifierad domän

- Om klienten har verifierat domäner väljer du en av domänerna i list rutan **Välj en verifierad domän** .

>[!Note]
> Det förväntade Content-Type-huvudet som ska returneras är `application/json` . Du kan få ett fel meddelande som anges nedan om du använder något annat som `application/json; charset=utf-8` 
> 
>``` "Verification of publisher domain failed. Error getting JSON file from https:///.well-known/microsoft-identity-association. The server returned an unexpected content type header value. " ```
>

## <a name="implications-on-the-app-consent-prompt"></a>Följder för appens medgivande

Att konfigurera utgivar domänen påverkar vad användarna ser i frågan om appens medgivande. För att fullständigt förstå komponenterna i medgivande frågan, se [förstå programmets medgivande upplevelser](application-consent-experience.md).

I följande tabell beskrivs beteendet för program som skapats före den 21 maj 2019.

![Medgivande-prompt för appar som skapats före den 21 maj 2019](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

Beteendet för nya program som skapats efter den 21 maj 2019 är beroende av utgivarens domän och typen av program. I följande tabell beskrivs de ändringar som du bör förvänta dig att se med de olika kombinationerna av konfigurationer.

![Medgivande-prompt för appar som skapats efter den 21 maj 2019](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Effekter på omdirigerings-URI: er

Program som loggar in användare med ett arbets-eller skol konto eller personliga Microsoft-konton ([flera innehavare](single-and-multi-tenant-apps.md)) omfattas av några begränsningar vid specificering av omdirigerings-URI: er.

### <a name="single-root-domain-restriction"></a>Begränsning av enskild rotdomän

När utgivarens domän värde för appar för flera klienter är inställt på null, är appar begränsade för att dela en enda rot domän för omdirigerings-URI: er. Till exempel tillåts inte följande kombination av värden eftersom rot domänen contoso.com inte matchar fabrikam.com.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Begränsningar för under domän

Under domäner tillåts, men du måste explicit registrera rot domänen. Till exempel, medan följande URI: er delar en enda rot domän, är kombinationen inte tillåten.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

Men om utvecklaren explicit lägger till rot domänen tillåts kombinationen.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>Undantag

Följande fall omfattas inte av begränsningen för en rot domän:

- Appar för en enda klient eller appar som riktar sig mot konton i en enda katalog
- Användning av localhost som omdirigerings-URI: er
- Omdirigera URI: er med anpassade scheman (icke-HTTP eller HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Konfigurera Publisher-domän program mässigt

För närvarande finns det inget REST API-eller PowerShell-stöd för att konfigurera Publisher-domänen program mässigt.
