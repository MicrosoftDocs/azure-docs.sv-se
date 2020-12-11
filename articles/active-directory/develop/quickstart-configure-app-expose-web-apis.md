---
title: 'Snabb start: registrera och exponera ett webb-API | Azure'
titleSuffix: Microsoft identity platform
description: 'I den här snabb starten ska du registrera ett webb-API med Microsoft Identity Platform och konfigurera dess scope, exponeras för klienter för behörighets-baserad åtkomst till API: ns resurser.'
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, contperf-fy21q1
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 4ebae7e97f9128bb9302e9076e71dd3164f0c29e
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030968"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>Snabb start: Konfigurera ett program för att exponera ett webb-API

I den här snabb starten registrerar du ett webb-API med Microsoft Identity Platform och exponerar det för klient program genom att lägga till ett exempel omfång. Genom att registrera ditt webb-API och exponera det via omfång kan du ge behörighet till sina resurser till behöriga användare och klient program som har åtkomst till ditt API.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration – [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Slut för ande av [snabb start: Konfigurera en klient](quickstart-create-new-tenant.md)

## <a name="register-the-web-api"></a>Registrera webb-API: et

Om du vill ge begränsad åtkomst till resurserna i ditt webb-API måste du först registrera API: t med Microsoft Identity Platform.

1. Utför stegen i avsnittet **Registrera ett program** i [snabb start: registrera en app med Microsoft Identity Platform](quickstart-register-app.md).
1. Hoppa över avsnittet **Lägg till en omdirigerings-URI** och **Konfigurera plattforms inställningar** . Du behöver inte konfigurera en omdirigerings-URI för ett webb-API eftersom ingen användare har loggat in interaktivt.
1. Hoppa över avsnittet **Lägg till autentiseringsuppgifter** för tillfället. Endast om ditt API har åtkomst till ett underordnat API skulle det behöva sina egna autentiseringsuppgifter, ett scenario som inte beskrivs i den här artikeln.

Med ditt webb-API registrerat är du redo att lägga till de omfattningar som din API-kod kan använda för att ge detaljerad behörighet till konsumenter av ditt API.

## <a name="add-a-scope"></a>Lägg till ett omfång

Koden i ett klient program begär behörighet att utföra åtgärder som definierats av ditt webb-API genom att skicka en åtkomsttoken tillsammans med dess begär anden till den skyddade resursen (webb-API). Ditt webb-API utför sedan den begärda åtgärden endast om åtkomsttoken den tar emot innehåller de omfattningar som krävs för åtgärden.

Börja med att följa de här stegen för att skapa ett exempel omfång med namnet `Employees.Read.All` :

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/quickstart-configure-app-expose-web-apis/portal-01-directory-subscription-filter.png" border="false"::: på den översta menyn och väljer den klient som innehåller klient appens registrering.
1. Välj **Azure Active Directory**  >  **Appregistreringar** och välj sedan ditt API: s app-registrering.
1. Välj **exponera ett API**  >  **Lägg till ett omfång**.

    :::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-02-expose-api.png" alt-text="En app-registrering visar ett API-fönster i Azure Portal":::

1. Du uppmanas att ange en **program-ID-URI** om du ännu inte har konfigurerat en.

   App-ID-URI: n fungerar som prefix för de omfattningar som du refererar till i din API-kod och måste vara globalt unik. Du kan använda standardvärdet som angetts, som är i formuläret `api://<application-client-id>` eller ange en mer LÄSBAR URI som `https://contoso.com/api` .

1. Ange sedan omfångets attribut i fönstret **Lägg till ett omfång** . I den här genom gången kan du använda exempel värden eller ange egna.

    | Fält | Beskrivning | Exempel |
    |-------|-------------|---------|
    | **Namn på sökomfång** | Namnet på ditt omfång. En vanlig namngivnings konvention för omfång är `resource.operation.constraint` . | `Employees.Read.All` |
    | **Vem kan ge medgivande** | Anger om det här omfånget kan skickas till av användare eller om administratörs medgivande krävs. Välj **Endast administratörer** för behörigheter på högre nivå. | **Administratörer och användare** |
    | **Visningsnamn för administratörsmedgivande** | En kort beskrivning av Omfattningens syfte som endast administratörer ser. | `Read-only access to Employee records` |
    | **Beskrivning av administratörsmedgivande** | En mer detaljerad beskrivning av behörigheten som beviljats av omfånget som endast administratörer ser. | `Allow the application to have read-only access to all Employee data.` |
    | **Visningsnamn för användarmedgivande** | En kort beskrivning av Omfattningens syfte. Visas endast för användare om du anger **vem som kan** godkänna **Administratörer och användare**. | `Read-only access to your Employee records` |
    | **Beskrivning av användarmedgivande** | En mer detaljerad beskrivning av behörigheten som beviljats av omfånget. Visas endast för användare om du anger **vem som kan** godkänna **Administratörer och användare**. | `Allow the application to have read-only access to your Employee data.` |

1. Ange **statusen** **aktive rad** och välj sedan **Lägg till omfång**.

1. Valfritt Om du vill förhindra att användare av din app godkänner medgivande till de omfattningar som du har definierat kan du *förauktorisera* klient programmet för åtkomst till ditt webb-API. Förauktoriserar *bara* de klient program som du litar på eftersom användarna inte har möjlighet att avstå från medgivande.
    1. Under **auktoriserade klient program** väljer du **Lägg till ett klient program**
    1. Ange **program-ID** för det klient program som du vill förauktorisera. Till exempel för ett webb program som du har registrerat tidigare.
    1. Under **godkända omfattningar** väljer du de omfattningar som du vill utelämna medgivande frågor för och väljer sedan **Lägg till program**.

    Om du följde det här valfria steget är klient appen nu en förauktoriserad klient app (PCA) och användarna uppmanas inte att ange sitt medgivande när de loggar in på den.

## <a name="add-a-scope-requiring-admin-consent"></a>Lägg till ett omfång som kräver administratörs medgivande

Lägg sedan till ett annat exempel-omfång `Employees.Write.All` som heter att endast administratörer kan godkänna. Omfattningar som kräver administratörs medgivande används vanligt vis för att ge åtkomst till högre privilegierade åtgärder och ofta av klient program som körs som backend-tjänster eller daemons som inte loggar in en användare interaktivt.

Om du vill lägga till `Employees.Write.All` exempel området följer du stegen i avsnittet [Lägg till ett område](#add-a-scope) och anger dessa värden i fönstret **Lägg till ett scope** :

| Fält                          | Exempelvärde                                                      |
|--------------------------------|--------------------------------------------------------------------|
| **Namn på sökomfång**                 | `Employees.Write.All`                                              |
| **Vem kan ge medgivande**            | **Endast administratörer**                                                    |
| **Visningsnamn för administratörsmedgivande** | `Write access to Employee records`                                 |
| **Beskrivning av administratörsmedgivande**  | `Allow the application to have write access to all Employee data.` |
| **Visningsnamn för användarmedgivande**  | *Ingen (lämna tomt)*                                               |
| **Beskrivning av användarmedgivande**   | *Ingen (lämna tomt)*                                               |

## <a name="verify-the-exposed-scopes"></a>Verifiera de exponerade omfattningarna

Om du har lagt till båda exempel omfattningarna som beskrivs i föregående avsnitt visas de i fönstret **exponera ett API** för din webb-API: s app-registrering, ungefär som den här avbildningen:

:::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-03-scopes-list.png" alt-text="Skärm bild av fönstret exponera ett API som visar två exponerade omfång.":::

Som du ser i bilden är en omfattnings fullständiga sträng sammanfogningen av URL: en för webb-API **: n** och omfångets omfångs **namn**.

Exempel: om webb-API: ns program-ID är `https://contoso.com/api` och omfångs namnet är `Employees.Read.All` , är det fullständiga omfånget:

`https://contoso.com/api/Employees.Read.All`

## <a name="using-the-exposed-scopes"></a>Använda de exponerade omfattningarna

I nästa artikel i serien konfigurerar du klient appens registrering med åtkomst till ditt webb-API och de omfattningar som du definierade genom att följa anvisningarna i den här artikeln.

När en klient program registrering beviljas behörighet att komma åt ditt webb-API kan klienten utfärda en OAuth 2,0-åtkomsttoken från Microsoft Identity Platform. När klienten anropar webb-API: et visas en åtkomsttoken vars omfång ( `scp` )-anspråk anges till de behörigheter som du har angett i klientens app-registrering.

Du kan göra ytterligare omfång tillgängliga senare, vid behov. Tänk på att ditt webb-API kan exponera flera scope som är kopplade till flera åtgärder. Din resurs kan styra åtkomsten till webb-API: n vid körning genom att utvärdera omfattning ( `scp` )-anspråk i OAuth 2,0-åtkomsttoken som den tar emot.

## <a name="next-steps"></a>Nästa steg

Nu när du har exponerat ditt webb-API genom att konfigurera dess omfång konfigurerar du klient appens registrering med behörighet att komma åt omfattningarna.

> [!div class="nextstepaction"]
> [Konfigurera en app-registrering för webb-API-åtkomst](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application
