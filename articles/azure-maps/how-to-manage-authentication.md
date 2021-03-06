---
title: Hantera autentisering
titleSuffix: Azure Maps
description: Bekanta dig med Azure Maps autentisering. Se vilken metod som fungerar bäst i det här scenariot. Lär dig hur du använder portalen för att Visa autentiseringsinställningar.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 955b541bdb4ae38066f1eb4d2f09363ec51be1d2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864082"
---
# <a name="manage-authentication-in-azure-maps"></a>Hantera autentisering i Azure Maps

När du har skapat ett Azure Maps-konto skapas ett klient-ID och nycklar som stöder Azure Active Directory (Azure AD)-autentisering och autentisering med delad nyckel.

## <a name="view-authentication-details"></a>Visa information om autentisering

När du har skapat ett Azure Maps-konto genereras de primära och sekundära nycklarna. Vi rekommenderar att du använder en primär nyckel som en prenumerations nyckel när du [använder autentisering med delad nyckel för att anropa Azure Maps](./azure-maps-authentication.md#shared-key-authentication). Du kan använda en sekundär nyckel i scenarier som löpande nyckel ändringar. Mer information finns i [autentisering i Azure Maps](./azure-maps-authentication.md).

Du kan visa information om autentiseringen i Azure Portal. I ditt konto går du till menyn **Inställningar** och väljer **autentisering**.

> [!div class="mx-imgBorder"]
> ![Autentiseringsinformation](./media/how-to-manage-authentication/how-to-view-auth.png)

## <a name="discover-category-and-scenario"></a>Identifiera kategori och scenario

Beroende på programmets behov finns det specifika vägar för att skydda programmet. Azure AD definierar kategorier för att stödja en mängd olika autentiserings flöden. Se [program kategorier](../active-directory/develop/authentication-flows-app-scenarios.md#application-categories) för att förstå vilken kategori programmet passar.

> [!NOTE]
> Även om du använder autentisering med delad nyckel kan du använda kategorier och scenarier för att skydda programmet.

## <a name="determine-authentication-and-authorization"></a>Fastställa autentisering och auktorisering

I följande tabell beskrivs vanliga scenarier för autentisering och auktorisering i Azure Maps. Tabellen innehåller en jämförelse av de typer av skydd som varje scenario erbjuder.

> [!IMPORTANT]
> Microsoft rekommenderar att du implementerar Azure Active Directory (Azure AD) med rollbaserad åtkomst kontroll i Azure (Azure RBAC) för produktions program.

| Scenario                                                                                    | Autentisering | Auktorisering | Utvecklings arbete | Drift ansträngning |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [Trusted daemon/icke-interaktiv klient program](./how-to-secure-daemon-app.md)        | Delad nyckel     | Ej tillämpligt           | Medel             | Högt               |
| [Trusted daemon/icke-interaktiv klient program](./how-to-secure-daemon-app.md)        | Azure AD       | Högt          | Lågt                | Medel             |
| [Webb program med en enda sida med interaktiv enkel inloggning](./how-to-secure-spa-users.md) | Azure AD       | Högt          | Medel             | Medel             |
| [Webb program med en enda sida med icke-interaktiv inloggning](./how-to-secure-spa-app.md)      | Azure AD       | Högt          | Medel             | Medel             |
| [Webb program med interaktiv enkel inloggning](./how-to-secure-webapp-users.md)          | Azure AD       | Högt          | Högt               | Medel             |
| [IoT-enhet/indatamängds begränsad enhet](./how-to-secure-device-code.md)                     | Azure AD       | Högt          | Medel             | Medel             |

Länkarna i tabellen tar dig till detaljerad konfigurations information för varje scenario.

## <a name="view-role-definitions"></a>Visa roll definitioner

Om du vill visa Azure-roller som är tillgängliga för Azure Maps går du till **åtkomst kontroll (IAM)**. Välj **roller** och Sök sedan efter roller som börjar med *Azure Maps*. Dessa Azure Maps roller är de roller som du kan bevilja åtkomst till.

> [!div class="mx-imgBorder"]
> ![Visa tillgängliga roller](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

## <a name="view-role-assignments"></a>Visa rolltilldelningar

Om du vill visa användare och appar som har beviljats åtkomst för Azure Maps går du till **Access Control (IAM)**. Där väljer du **roll tilldelningar** och filtrerar sedan efter **Azure Maps**.

> [!div class="mx-imgBorder"]
> ![Visa användare och appar som har beviljats åtkomst](./media/how-to-manage-authentication/how-to-view-amrbac.png)

## <a name="request-tokens-for-azure-maps"></a>Begär token för Azure Maps

Begär en token från Azure AD-token-slutpunkten. Använd följande information i din Azure AD-begäran:

| Azure-miljö      | Azure AD-token-slutpunkt             | Resurs-ID för Azure              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Offentligt Azure-moln     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure Government molnet | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

Mer information om hur du begär åtkomsttoken från Azure AD för användare och tjänst huvud namn finns i [scenarier för Azure AD](../active-directory/develop/authentication-vs-authorization.md) och visa särskilda scenarier i tabellen med [scenarier](./how-to-manage-authentication.md#determine-authentication-and-authorization).

## <a name="manage-and-rotate-shared-keys"></a>Hantera och rotera delade nycklar

Dina Azure Maps prenumerations nycklar liknar ett rot lösen ord för ditt Azure Maps-konto. Var alltid noga med att skydda dina prenumerations nycklar. Använd Azure Key Vault för att hantera och rotera dina nycklar på ett säkert sätt. Undvik att distribuera åtkomst nycklar till andra användare, hårdkoda dem eller spara dem var som helst i oformaterad text som är tillgängliga för andra. Rotera dina nycklar om du misstänker att de kan ha komprometterats.

> [!NOTE]
> Microsoft rekommenderar att du använder Azure Active Directory (Azure AD) för att godkänna begär Anden om möjligt, i stället för delad nyckel. Azure AD ger överlägsen säkerhet och enkel användning över delad nyckel.

### <a name="manually-rotate-subscription-keys"></a>Rotera prenumerations nycklar manuellt

Microsoft rekommenderar att du roterar dina prenumerations nycklar regelbundet för att skydda ditt Azure Maps konto. Använd om möjligt Azure Key Vault för att hantera dina åtkomst nycklar. Om du inte använder Key Vault måste du rotera dina nycklar manuellt.

Två prenumerations nycklar tilldelas så att du kan rotera dina nycklar. Att ha två nycklar säkerställer att ditt program behåller åtkomsten till Azure Maps under hela processen.

Så här roterar du dina Azure Maps prenumerations nycklar i Azure Portal:

1. Uppdatera program koden så att den refererar till den sekundära nyckeln för det Azure Maps kontot och distribuera.
2. Navigera till ditt Azure Maps konto i [Azure Portal](https://portal.azure.com/).
3. Under **Inställningar** väljer du **autentisering**.
4. Om du vill återskapa den primära nyckeln för ditt Azure Maps-konto väljer du knappen **skapa** igen bredvid primär nyckeln.
5. Uppdatera program koden så att den refererar till den nya primära nyckeln och distribuera.
6. Återskapa den sekundära nyckeln på samma sätt.

> [!WARNING]
> Microsoft rekommenderar att du bara använder en av nycklarna i alla dina program på samma tidpunkt. Om du använder nyckel 1 på vissa platser och nyckel 2 i andra kommer du inte att kunna rotera dina nycklar utan att några program förlorar åtkomst.

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Azure AD och Azure Maps Web SDK](./how-to-use-map-control.md).

Hitta API-användnings mått för ditt Azure Maps-konto:
> [!div class="nextstepaction"]
> [Visa användningsstatistik](how-to-view-api-usage.md)

Utforska exempel som visar hur du integrerar Azure AD med Azure Maps:

> [!div class="nextstepaction"]
> [Exempel på Azure AD-autentisering](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)