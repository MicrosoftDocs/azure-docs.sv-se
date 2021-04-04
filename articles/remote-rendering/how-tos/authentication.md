---
title: Autentisering
description: Förklarar hur autentisering fungerar
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: how-to
ms.openlocfilehash: 35fd78a9d55dc684045fdb4b83691c1613801421
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "97724891"
---
# <a name="configure-authentication"></a>Konfigurera autentisering

Azure fjärrrendering använder samma autentiseringsmekanism som [Azure spatiala ankare (ASA)](../../spatial-anchors/concepts/authentication.md?tabs=csharp). Klienterna måste ange *något* av följande för att anropa REST-API: er:

* **AccountKey**: kan hämtas på fliken "nycklar" för det fjärranslutna åter givnings kontot på Azure Portal. Konto nycklar rekommenderas endast för utveckling/prototypering.
    ![Konto-ID](./media/azure-account-primary-key.png)

* **AccountDomain**: kan hämtas på fliken "Översikt" för fjärråter givnings kontot på Azure Portal.
    ![Konto domän](./media/azure-account-domain.png)

* **AuthenticationToken**: är en Azure AD-token som kan hämtas med hjälp av [MSAL-biblioteket](../../active-directory/develop/msal-overview.md). Det finns flera olika flöden som kan användas för att acceptera användarautentiseringsuppgifter och använda dessa autentiseringsuppgifter för att hämta en åtkomsttoken.

* **MRAccessToken**: är en Mr-token som kan hämtas från Azure Mixed Reality Security Token Service (STS). Hämtas från `https://sts.<accountDomain>` slut punkten med ett rest-anrop som liknar följande:

    ```rest
    GET https://sts.southcentralus.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
    Host: sts.southcentralus.mixedreality.azure.com
    Connection: Keep-Alive

    HTTP/1.1 200 OK
    Date: Tue, 24 Mar 2020 09:09:00 GMT
    Content-Type: application/json; charset=utf-8
    Content-Length: 1153
    Accept: application/json
    MS-CV: 05JLqWeKFkWpbdY944yl7A.0
    {"AccessToken":"eyJhbGciOiJSUzI1<truncated>uLkO2FvA"}
    ```

    Där Authorization-huvudet är formaterat på följande sätt: `Bearer <Azure_AD_token>` eller `Bearer <accoundId>:<accountKey>` . Den förra är att föredra för säkerhet. Den token som returnerades från detta REST-anrop är MR-åtkomsttoken.

## <a name="authentication-for-deployed-applications"></a>Autentisering för distribuerade program

Konto nycklar rekommenderas endast för snabb prototyper under utveckling. Vi rekommenderar att du inte levererar ditt program till produktion med hjälp av en inbäddad konto nyckel i det. Den rekommenderade metoden är att använda en användar-eller tjänstebaserad Azure AD-autentisering.

 Azure AD-autentisering beskrivs i avsnittet [Azure AD-användarautentisering](../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication) i tjänsten [Azure spatial ankare (ASA)](../../spatial-anchors/index.yml) .

 Mer information finns i [självstudien: skydda Azure-Fjärråter givning och modell lagring – Azure Active Directory autentisering](../tutorials/unity/security/security.md#azure-active-directory-azure-ad-authentication)

## <a name="azure-role-based-access-control"></a>Rollbaserad Azure-åtkomstkontroll

Använd följande roller när du beviljar rollbaserad åtkomst för att kontrol lera åtkomst nivån för tjänsten:

* **Administratören för fjärran sluten åter givning**: ger användare med konvertering, hantering av sessioner, åter givning och diagnostik för Azure-fjärrrendering.
* **Fjärran sluten klient**: ger användaren möjlighet att hantera sessioner, åter givning och diagnostik för Azure-fjärrrendering.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett konto](create-an-account.md)
* [Använda Azure-frontend-API: er för autentisering](frontend-apis.md)
* [PowerShell-exempelskript](../samples/powershell-example-scripts.md)