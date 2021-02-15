---
title: Registreringsfält för Azure-portalen för egenutvecklade appar
description: Vägledning för att registrera ett anpassat utvecklat program med Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 82c3dd4ce7f5e7e9f3d5a226bfe65e27eca2d3d4
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103251"
---
# <a name="azure-portal-registration-fields-for-custom-developed-apps"></a>Registreringsfält för Azure-portalen för egenutvecklade appar

Den här artikeln innehåller en kort beskrivning av alla tillgängliga fält i program registrerings formuläret i [Azure Portal](https://portal.azure.com).

## <a name="register-a-new-application"></a>Registrera ett nytt program

-   Om du vill registrera ett nytt program går du till <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.

-   Klicka på Azure Active Directory i det vänstra navigerings fönstret **.**

-   Välj **Appregistreringar** och klicka på **Lägg till**.

-   Då öppnas formuläret för program registrering.

## <a name="fields-in-the-application-registration-form"></a>Fält i program registrerings formuläret

| Fält            | Beskrivning                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Namn             | Namnet på programmet. Det får innehålla minst fyra tecken.                |
| Kontotyper som stöds| Välj vilka konton som du vill att ditt program ska stödja: konton i den här organisations katalogen, konton i valfri organisations katalog eller konton i alla organisations kataloger och personliga Microsoft-konton.  |
| Omdirigerings-URI (valfritt) | Välj den typ av app som du skapar, **webb** eller **offentlig klient (mobilt & Desktop)** och ange omdirigerings-URI (eller svars-URL) för ditt program. För webbappar anger du grundläggande URL för appen. Till exempel kan http://localhost:31544 vara URL för en webbapp som körs på din lokala dator. Användare skulle då använda den här URL:en för att logga in till ett webbklientprogram. För offentliga klientprogram anger du den URI som används av Azure AD för att returnera tokensvar. Ange ett värde som är specifik för ditt program, till exempel myapp://auth. Om du vill se specifika exempel för webb program eller interna program kan du titta på våra [snabb starter](./index.yml).|

När du har fyllt i fälten ovan registreras programmet i Azure Portal och du omdirigeras till program översikts sidan. Sidan Inställningar i den vänstra rutan under **Hantera** har fler fält som du kan använda för att anpassa ditt program. I tabellerna nedan beskrivs alla fält. Du ser bara en delmängd av dessa fält, beroende på om du har skapat ett webb program eller ett offentligt klient program.

### <a name="overview"></a>Översikt

| Fält           | Beskrivning        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Program-ID  | När du registrerar ett program tilldelar Azure AD ditt program ett program-ID. Program-ID: t kan användas för att unikt identifiera ditt program i autentiseringsbegäranden till Azure AD, samt för att få åtkomst till resurser som Graph API.                                                          |
| URI för app-id      | Detta bör vara en unik URI, vanligt vis av formatet **https:// &lt; klient \_ namn &gt; / &lt; program \_ namn &gt; .** Detta används under flödet för auktoriserings beviljande, som en unik identifierare för att ange den resurs som token ska utfärdas för. Det blir också AUD-anspråket i den utfärdade åtkomsttoken. |

### <a name="branding"></a>Anpassning

| Fält           | Beskrivning        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ladda upp ny logo typ | Du kan använda den för att ladda upp en logo typ för ditt program. Logo typen måste vara i formatet. bmp,. jpg eller. png och fil storleken måste vara mindre än 100 KB. Måtten för bilden ska vara 215x215 pixlar, med centrala bild dimensioner för 94x94-bild punkter.|
| URL för start sidan   | Detta är inloggnings-URL: en som anges under program registreringen.|

### <a name="authentication"></a>Autentisering

| Fält           | Beskrivning        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Utloggnings-URL för frontend-kanal      | Detta är URL: en för enkel utloggning. Azure AD skickar en utloggnings förfrågan till denna URL när användaren tar bort sin session med Azure AD med hjälp av något annat registrerat program.|
| Kontotyper som stöds  | Den här växeln anger om programmet kan användas av flera klienter. Det innebär vanligt vis att externa organisationer kan använda ditt program genom att registrera det i klienten och bevilja åtkomst till organisationens data.|
| Omdirigerings-URL:er      | URL: erna för omdirigering eller svar är de slut punkter där Azure AD returnerar alla tokens som programmet begär. För interna program är det här som användaren skickas efter en lyckad auktorisering. Azure AD kontrollerar att den omdirigerings-URI som ditt program tillhandahåller i OAuth 2,0-begäran matchar ett av de registrerade värdena i portalen.|

### <a name="certificates-and-secrets"></a>Certifikat och hemligheter

| Fält           | Beskrivning        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Klienthemligheter            | Du kan skapa klient hemligheter eller nycklar för att program mässigt få åtkomst till webb-API: er som skyddas av Azure AD utan användar interaktion. På sidan **ny klient hemlighet** anger du en nyckel beskrivning och förfallo datum och spara för att generera nyckeln. Se till att spara den på ett säkert sätt, eftersom du inte kan komma åt den senare.             |

## <a name="next-steps"></a>Nästa steg

[Hantera program med Azure Active Directory](../manage-apps/what-is-application-management.md)