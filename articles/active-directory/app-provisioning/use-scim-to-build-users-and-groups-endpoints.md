---
title: Bygg en SCIM-slutpunkt för användar etablering till appar från Azure Active Directory
description: Lär dig att utveckla en SCIM-slutpunkt, integrera ditt SCIM-API med Azure AD och automatiskt etablera användare och grupper i dina moln program med Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 9fac8db6747d1e081fa884ed794e55b98f44577f
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652045"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>Självstudie: utveckla en exempel SCIM-slutpunkt

Ingen vill skapa en ny slut punkt från grunden, så vi har skapat en [referens kod](https://aka.ms/scimreferencecode) som du kan använda för att komma igång med [system för scim (Cross-Domain Identity Management)](https://aka.ms/scimoverview). Du kan få din SCIM-slutpunkt igång med ingen kod på bara fem minuter.

I den här självstudien beskrivs hur du distribuerar SCIM-referens koden i Azure och testar den med hjälp av Postman eller genom att integrera med SCIM-klienten för Azure Active Directory (Azure AD). Den här självstudien är avsedd för utvecklare som vill komma igång med SCIM eller för alla som är intresserade av att testa en SCIM-slutpunkt.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Distribuera din SCIM-slutpunkt i Azure.
> * Testa din SCIM-slutpunkt.

## <a name="deploy-your-scim-endpoint-in-azure"></a>Distribuera din SCIM-slutpunkt i Azure

Stegen här distribuerar SCIM-slutpunkten till en tjänst med hjälp av [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) och [Azure App Service](https://docs.microsoft.com/azure/app-service/). Referens koden för SCIM kan också köras lokalt, finnas på en lokal server eller distribueras till en annan extern tjänst.

1. Gå till [referens koden](https://github.com/AzureAD/SCIMReferenceCode) från GitHub och välj **klona eller hämta**.

1. Välj **Öppna på Skriv bordet** eller kopiera länken, öppna Visual Studio och välj **klona eller checka ut kod** för att ange den kopierade länken och göra en lokal kopia.

1. I Visual Studio, se till att logga in på kontot som har åtkomst till dina värd resurser.

1. I Solution Explorer öppnar du *Microsoft. scim. SLN* och högerklickar på filen *Microsoft. scim. WebHostSample* . Välj **Publicera**.

    ![Skärm bild som visar exempel filen.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > Om du vill köra lösningen lokalt dubbelklickar du på projektet och väljer **IIS Express** för att starta projektet som en webb sida med en lokal värd-URL.

1. Välj **Skapa profil** och se till att **App Service** och **Skapa ny** är markerade.

    ![Skärm bild som visar publicerings fönstret.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. Gå igenom dialog alternativen och Byt namn på appen till önskat namn. Det här namnet används både i appen och SCIM slut punkts-URL.

    ![Skärm bild som visar hur du skapar en ny app service.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. Välj den resurs grupp som du vill använda och välj **publicera**.

1. Gå till programmet i **Azure App Service**  >  **konfiguration** och välj **ny program inställning** för att lägga till inställningen *Token__TokenIssuer* med värdet `https://sts.windows.net/<tenant_id>/` . Ersätt `<tenant_id>` med ditt Azure AD-klient-ID. Om du vill testa SCIM-slutpunkten med [Postman](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)lägger du till en *ASPNETCORE_ENVIRONMENT* -inställning med värdet `Development` .

   ![Skärm bild som visar fönstret program inställningar.](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   När du testar din slut punkt med ett företags program i [Azure Portal](use-scim-to-provision-users-and-groups.md#integrate-your-scim-endpoint-with-the-aad-scim-client)har du två alternativ. Du kan behålla miljön i `Development` och tillhandahålla test-token från `/scim/token` slut punkten, eller så kan du ändra miljön till `Production` och lämna fältet token tomt.

Klart! Din SCIM-slutpunkt är nu publicerad och du kan använda Azure App Service-URL: en för att testa SCIM-slutpunkten.

## <a name="test-your-scim-endpoint"></a>Testa din SCIM-slutpunkt

Begär anden till en SCIM-slutpunkt kräver auktorisering. SCIM-standarden har flera alternativ för autentisering och auktorisering, inklusive cookies, grundläggande autentisering, TLS-klientautentisering eller någon av metoderna som anges i [RFC 7644](https://tools.ietf.org/html/rfc7644#section-2).

Se till att undvika metoder som inte är säkra, till exempel användar namn och lösen ord, vilket ger en säkrare metod som OAuth. Azure AD stöder långa token för lång livs längd (för Galleri program och icke-galleriprogram) och OAuth-auktorisering (för Galleri program).

> [!NOTE]
> De autentiseringsmetoder som anges i lagrings platsen är endast för testning. När du integrerar med Azure AD kan du granska vägledningen för auktorisering. Se [Planera etablering för en scim-slutpunkt](use-scim-to-provision-users-and-groups.md).

Utvecklings miljön möjliggör funktioner som är osäkra för produktion, t. ex. referens kod som styr beteendet för verifieringen av säkerhetstoken. Token-verifierings koden använder en självsignerad säkerhetstoken och signerings nyckeln lagras i konfigurations filen. Se parametern **token: IssuerSigningKey** i *appsettings.Development.jspå* filen.

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> När du skickar en **Get** -begäran till `/scim/token` slut punkten utfärdas en token med den konfigurerade nyckeln. Denna token kan användas som en Bearer-token för efterföljande auktorisering.

Standard verifierings koden för token har kon figurer ATS för att använda en Azure AD-token och kräver att den utfärdande klienten konfigureras med hjälp av parametern **token: TokenIssuer** i filen *appsettings.js* .

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>Använd Postman för att testa slut punkter

När du har distribuerat SCIM-slutpunkten kan du testa för att säkerställa att den är kompatibel med SCIM RFC. Det här exemplet innehåller en uppsättning tester i Postman som verifierar åtgärder för CRUD (skapa, läsa, uppdatera och ta bort) för användare och grupper, filtrering, uppdateringar av grupp medlemskap och inaktive ring av användare.

Slut punkterna finns i `{host}/scim/` katalogen och du kan använda standard-HTTP-begäranden för att interagera med dem. Information om hur du ändrar `/scim/` vägen finns i *ControllerConstant.cs* i **AzureADProvisioningSCIMreference**  >  **ScimReferenceApi**  >  **controllers**.

> [!NOTE]
> Du kan bara använda HTTP-slutpunkter för lokala tester. Azure AD Provisioning-tjänsten kräver att slut punkten stöder HTTPS.

1. Hämta [Postman](https://www.getpostman.com/downloads/) och starta programmet.
1. Kopiera och klistra in länken i Postman för att importera test samlingen: `https://aka.ms/ProvisioningPostman` .

    ![Skärm bild som visar hur du importerar test samlingen i Postman.](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. Skapa en test miljö som har följande variabler:

   |Miljö|Variabel|Värde|
   |-|-|-|
   |Kör projektet lokalt genom att använda IIS Express|||
   ||**Server**|`localhost`|
   ||**Port**|`:44359`*(Glöm inte **`:`** )*|
   ||**Application**|`scim`|
   |Kör projektet lokalt genom att använda Kestrel|||
   ||**Server**|`localhost`|
   ||**Port**|`:5001`*(Glöm inte **`:`** )*|
   ||**Application**|`scim`|
   |Värd för slut punkten i Azure|||
   ||**Server**|*(mata in din SCIM-URL)*|
   ||**Port**|*(lämna tomt)*|
   ||**Application**|`scim`|

1. Använd **Hämta nyckel** från Postman-samlingen för att skicka en **Get** -begäran till token-slutpunkten och hämta en säkerhetstoken som ska lagras i variabeln **token** för efterföljande begär Anden.

   ![Skärm bild som visar mappen Postman get Key.](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > Om du vill göra en SCIM-slutpunkt säker måste du ha en säkerhetstoken innan du ansluter. I självstudien används `{host}/scim/token` slut punkten för att generera en självsignerad token.

Klart! Nu kan du köra **Postman** -samlingen för att testa scim slut punkts funktion.

## <a name="next-steps"></a>Nästa steg

För att utveckla en SCIM-kompatibel användare och grupp slut punkt med samverkan för en klient, se [scim-klient implementering](http://www.simplecloud.info/#Implementations2).

> [!div class="nextstepaction"]
> [Självstudie: utveckla och planera etablering för en scim-slutpunkt](use-scim-to-provision-users-and-groups.md) 
>  [Självstudie: Konfigurera etablering för en Galleri app](configure-automatic-user-provisioning-portal.md)
