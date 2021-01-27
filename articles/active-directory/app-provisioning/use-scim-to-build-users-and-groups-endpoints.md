---
title: Bygg en SCIM-slutpunkt för användar etablering till appar från Azure AD
description: System för SCIM (Cross-Domain Identity Management) standardiserar automatisk användar etablering. Lär dig att utveckla en SCIM-slutpunkt, integrera ditt SCIM-API med Azure Active Directory och börja automatisera etableringen av användare och grupper i dina moln program.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ae36af981b113d44ac1b8fd45a1d084760b0294
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900247"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>Självstudie: utveckla en exempel SCIM-slutpunkt

Ingen vill skapa en ny slut punkt från grunden, så vi har skapat en [referens kod](https://aka.ms/scimreferencecode) som hjälper dig att komma igång med [scim](https://aka.ms/scimoverview). I den här självstudien beskrivs hur du distribuerar SCIM-referens koden i Azure och testar den med Postman eller genom att integrera med Azure AD SCIM-klienten. Du kan få din SCIM-slutpunkt igång med ingen kod på bara 5 minuter. Den här självstudien är avsedd för utvecklare som vill komma igång med SCIM eller andra som vill testa en SICM-slutpunkt. 

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Hämta referens koden
> * Distribuera din SCIM-slutpunkt i Azure
> * Testa din SCIM-slutpunkt

Slut punkts funktionerna som ingår:

|Slutpunkt|Beskrivning|
|---|---|
|`/User`|Utföra CRUD-åtgärder på en användar resurs: **skapa**, **Uppdatera**, **ta bort**, **Hämta**, **lista**, **filtrera**|
|`/Group`|Utföra CRUD-åtgärder på en grupp resurs: **skapa**, **Uppdatera**, **ta bort**, **Hämta**, **lista**, **filtrera**|
|`/Schemas`|Hämta ett eller flera scheman som stöds.<br/><br/>En uppsättning attribut för en resurs som stöds av varje tjänst leverantör kan variera, t. ex. om service providern har stöd för "namn", "title" och "e-post" medan Service Provider B stöder "namn", "title" och "phoneNumbers" för användare.|
|`/ResourceTypes`|Hämta resurs typer som stöds.<br/><br/>Antalet och typerna av resurser som stöds av varje tjänst leverantör kan variera, t. ex. att tjänst leverantören har stöd för användare medan Service Provider B stöder användare och grupper.|
|`/ServiceProviderConfig`|Hämta tjänst leverantörens SCIM-konfiguration<br/><br/>De SCIM-funktioner som stöds av varje tjänst leverantör kan variera, t. ex. Service Provider A stöder korrigerings åtgärder medan Service Provider B stöder korrigerings åtgärder och schema identifiering.|

## <a name="download-the-reference-code"></a>Hämta referens koden

Den [referens kod](https://github.com/AzureAD/SCIMReferenceCode) som ska hämtas omfattar följande projekt:

- **Microsoft.SystemForCrossDomainIdentityManagement**, .net Core MVC Web API för att bygga och etablera ett scim-API
- **Microsoft. scim. WebHostSample**, ett arbets exempel för en scim-slutpunkt

Projekten innehåller följande mappar och filer:

|Fil/mapp|Description|
|-|-|
|Mappen **scheman**| Modellerna för **användare** och **grupp** resurser tillsammans med några abstrakta klasser som schematiserade för delade funktioner.<br/><br/> En mapp för **attribut** som innehåller klass definitionerna för komplexa attribut för **användare** och **grupper** , t. ex. adresser.|
|**Tjänstmall** | Innehåller logik för åtgärder som rör hur resurser efter frågas och uppdateras.<br/><br/> Referens koden innehåller tjänster för att returnera användare och grupper.<br/><br/>Mappen **kontrollanter** innehåller olika scim-slutpunkter. Resurs kontroller innehåller HTTP-verb för att utföra CRUD-åtgärder på resursen (**Get**, post **,** **Box**, **patch**, **Delete**). Kontrollanter förlitar sig på tjänster för att utföra åtgärderna.|
|**Protokoll** -mapp|Innehåller logik för åtgärder som rör hur resurser returneras enligt SCIM RFC, till exempel:<br/><ul><li>Returnerar flera resurser som en lista.</li><li>Returnerar endast vissa resurser baserat på ett filter.</li><li>Omvandla en fråga till en lista över länkade listor över enskilda filter.</li><li>Att omvandla en PATCH-begäran till en åtgärd med attribut som hör till värde Sök vägen.</li><li>Definiera den typ av åtgärd som kan användas för att tillämpa ändringar på resurs objekt.</li></ul>|
|`Microsoft.SystemForCrossDomainIdentityManagement`| Exempel käll kod.|
|`Microsoft.SCIM.WebHostSample`| Exempel på implementering av SCIM-biblioteket.|
|*.gitignore*|Definiera vad som ska ignoreras vid genomförande tillfället.|
|*CHANGELOG.md*|Lista över ändringar i exemplet.|
|*CONTRIBUTING.md*|Rikt linjer för att bidra till exemplet.|
|*README.md*|Den här **README** -filen.|
|*LICENSAVTALET*|Licens för exemplet.|

> [!NOTE]
> Den här koden är avsedd att hjälpa till att börja skapa en SCIM-slutpunkt och tillhandahålls **i befintligt skick**. De referenser som ingår har ingen garanti för aktiva Maintainence eller support.
>
> Det här projektet använder sig av [Microsofts uppförandekod för öppen källkod](https://opensource.microsoft.com/codeofconduct/). Eftersom sådana [bidrag](https://github.com/AzureAD/SCIMReferenceCode/wiki/Contributing-Overview) från communityn är välkommen att hjälpa till att bygga och underhålla lagrings platsen, liksom andra bidrag med öppen källkod, kommer du att godkänna ett licens avtal för bidrag (CLA). Detta avtal förklarar att du har och beviljar behörighet att använda ditt bidrag, mer information finns i [Microsoft öppen källkod](https://cla.opensource.microsoft.com).
>
> Du hittar mer information i [Vanliga frågor om uppförandekod](https://opensource.microsoft.com/codeofconduct/faq/) eller kontakta [opencode@microsoft.com](mailto:opencode@microsoft.com) för ytterligare frågor eller kommentarer.

###  <a name="use-multiple-environments"></a>Använda flera miljöer

Den inkluderade SCIM-koden använder en ASP.NET Core miljö för att kontrol lera att den är auktoriserad för användning i utveckling och efter distributionen, se [använda flera miljöer i ASP.net Core](https://docs.microsoft.com/aspnet/core/fundamentals/environments?view=aspnetcore-3.1).

```csharp
private readonly IWebHostEnvironment _env;
...

public void ConfigureServices(IServiceCollection services)
{
    if (_env.IsDevelopment())
    {
        ...
    }
    else
    {
        ...
    }
```

## <a name="deploy-your-scim-endpoint-in-azure"></a>Distribuera din SCIM-slutpunkt i Azure

De steg som beskrivs här distribuerar SCIM-slutpunkten till en tjänst med hjälp av [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) och [Azure App Services](https://docs.microsoft.com/azure/app-service/). Referens koden för SCIM kan också köras lokalt, finnas på en lokal server eller distribueras till en annan extern tjänst. 

### <a name="open-solution-and-deploy-to-azure-app-service"></a>Öppna lösning och distribuera till Azure App Service

1. Gå till [referens koden](https://github.com/AzureAD/SCIMReferenceCode) från GitHub och välj **klona eller hämta**.

1. Välj antingen **Öppna på Skriv bordet**, eller kopiera länken, öppna **Visual Studio** och välj **klona eller checka ut kod** för att ange den kopierade länken och göra en lokal kopia.

1. I **Visual Studio** måste du vara noga med att logga in på kontot som har åtkomst till dina värd resurser.

1. I **Solution Explorer** öppnar du *Microsoft. scim. SLN* och högerklickar på filen *Microsoft. scim. WebHostSample* . Välj **Publicera**.

    ![moln publicering](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > Om du vill köra lösningen lokalt dubbelklickar du på projektet och väljer **IIS Express** för att starta projektet som en webb sida med en lokal värd-URL.

1. Välj **Skapa profil** och se till att **App Service** och **Skapa ny** är markerade.

    ![moln publicering 2](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. Gå igenom dialog alternativen och Byt namn på appen till önskat namn. Det här namnet används både i appen och SCIM slut punkts-URL.

    ![moln publicering 3](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. Välj den resurs grupp som du vill använda och välj **publicera**.

1. Navigera till programmet i **Azure App Services**-  >  **konfigurationen** och välj **ny program inställning** för att lägga till inställningen *Token__TokenIssuer* med värdet `https://sts.windows.net/<tenant_id>/` . Ersätt `<tenant_id>` med din Azure AD-tenant_id och om du vill testa scim-slutpunkten med [Postman](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)lägger du också till en *ASPNETCORE_ENVIRONMENT* -inställning med värdet `Development` . 

   ![AppService-inställningar](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   När du testar din slut punkt med ett företags program i Azure Portal kan du välja att behålla miljön som `Development` och ange den token som genereras från `/scim/token` slut punkten för testning eller ändra miljön till `Production` och lämna fältet token tomt i företags programmet i [Azure Portal](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client). 

Klart! Din SCIM-slutpunkt är nu publicerad och du kan använda Azure App Service-URL: en för att testa SCIM-slutpunkten.

## <a name="test-your-scim-endpoint"></a>Testa din SCIM-slutpunkt

Begär anden till en SCIM-slutpunkt kräver auktorisering och SCIM-standarden lämnar flera alternativ för autentisering och auktorisering, till exempel cookies, grundläggande autentisering, TLS-klientautentisering eller någon av metoderna som anges i [RFC 7644](https://tools.ietf.org/html/rfc7644#section-2).

Se till att undvika osäkra metoder, t. ex. användar namn/lösen ord, som prioriteras av en säkrare metod som OAuth. Azure AD stöder långa token för lång livs längd (för Galleri program och icke-galleriprogram) och OAuth-auktorisering (för program som publiceras i app-galleriet).

> [!NOTE]
> De autentiseringsmetoder som anges i lagrings platsen är endast för testning. När du integrerar med Azure AD kan du läsa om auktoriseringsbeslut, se [Planera etablering för en scim-slutpunkt](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#authorization-for-provisioning-connectors-in-the-application-gallery). 

Utvecklings miljön aktiverar funktioner som inte är säkra för produktion, till exempel referens kod som styr beteendet för verifiering av säkerhetstoken. Verifierings koden för token har kon figurer ATS för att använda en självsignerad säkerhetstoken och signerings nyckeln lagras i konfigurations filen, se parametern **token: IssuerSigningKey** i filen *appsettings.Development.js* .

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> Genom att skicka en **Get** -begäran till `/scim/token` slut punkten utfärdas en token med den konfigurerade nyckeln och kan användas som Bearer-token för efterföljande auktorisering.

Standard verifierings koden för token har kon figurer ATS för att använda en token som utfärdats av Azure Active Directory och kräver att den utfärdande klienten konfigureras med parametern **token: TokenIssuer** i *appsettings.jspå* filen.

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>Använd Postman för att testa slut punkter

När SCIM-slutpunkten har distribuerats kan du testa för att säkerställa att den är SCIM RFC-kompatibel. Det här exemplet innehåller en uppsättning tester i **Postman** för att verifiera CRUD-åtgärder för användare och grupper, filtrering, uppdateringar av grupp medlemskap och inaktive ring av användare.

Slut punkterna finns i `{host}/scim/` katalogen och kan interagera med att använda HTTP-standardbegäranden. Information om hur du ändrar `/scim/` vägen finns i *ControllerConstant.cs* i **AzureADProvisioningSCIMreference**  >  **ScimReferenceApi**  >  **controllers**.

> [!NOTE]
> Du kan bara använda HTTP-slutpunkter för lokala test eftersom Azure AD Provisioning-tjänsten kräver slut punkts stöd för HTTPS.

#### <a name="open-postman-and-run-tests"></a>Öppna Postman och kör tester

1. Hämta [Postman](https://www.getpostman.com/downloads/) och starta program.
1. Kopiera länken [https://aka.ms/ProvisioningPostman](https://aka.ms/ProvisioningPostman) och klistra in i Postman för att importera test samlingen.

    ![Postman-samling](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. Skapa en test miljö med variablerna nedan:

   |Miljö|Variabel|Värde|
   |-|-|-|
   |Kör projektet lokalt med IIS Express|||
   ||**Server**|`localhost`|
   ||**Port**|`:44359`*(Glöm inte **:**)*|
   ||**Application**|`scim`|
   |Kör projektet lokalt med Kestrel|||
   ||**Server**|`localhost`|
   ||**Port**|`:5001`*(Glöm inte **:**)*|
   ||**Application**|`scim`|
   |Värd för slut punkten i Azure|||
   ||**Server**|*(mata in din SCIM-URL)*|
   ||**Port**|*(lämna tomt)*|
   ||**Application**|`scim`|

1. Använd **Hämta nyckel** från Postman-samlingen för att skicka en **Get** -begäran till token-slutpunkten och hämta en säkerhetstoken som ska lagras i variabeln **token** för efterföljande begär Anden. 

   ![Hämta nyckel för Postman](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > Om du vill göra en SCIM-slutpunkt säker måste du ha en säkerhetstoken innan du ansluter och självstudien använder `{host}/scim/token` slut punkten för att generera en självsignerad token.

Klart! Nu kan du köra **Postman** -samlingen för att testa scim slut punkts funktion.

## <a name="next-steps"></a>Nästa steg

För att utveckla en SCIM-kompatibel användare och grupp slut punkt med interoperabilitet för en klient, se [klient implementeringen för scim](http://www.simplecloud.info/#Implementations2).

> [!div class="nextstepaction"]
> [Självstudie: utveckla och planera etablering för en scim-slutpunkt](use-scim-to-provision-users-and-groups.md) 
>  [Självstudie: Konfigurera etablering för en Galleri app](configure-automatic-user-provisioning-portal.md)