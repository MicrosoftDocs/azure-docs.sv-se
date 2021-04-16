---
title: API-autentisering och auktorisering – Azure Time Series Insights | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar autentisering och auktorisering för ett anpassat program som anropar Azure Time Series Insights API.
ms.service: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/23/2021
ms.custom: seodec18, has-adal-ref, devx-track-azurecli
ms.openlocfilehash: 225e72bc00ce0a80ff655a76562b5c6b70b7fa79
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479499"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autentisering och auktorisering för Azure Time Series Insights API

Beroende på dina affärsbehov kan din lösning innehålla ett eller flera klientprogram som du använder för att interagera med Azure Time Series Insights-miljöns [API:er.](/rest/api/time-series-insights/reference-data-access-overview) Azure Time Series Insights utför autentisering med hjälp [av Azure AD-säkerhetstoken baserat på OAUTH 2.0](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims). För att autentisera dina klienter måste du hämta en bearer-token med rätt behörigheter och skicka den tillsammans med dina API-anrop. Det här dokumentet beskriver flera metoder för att hämta autentiseringsuppgifter som du kan använda för att hämta en bearer-token och autentisera, inklusive att använda hanterad identitet Azure Active Directory en appregistrering.

## <a name="managed-identities"></a>Hanterade identiteter

I följande avsnitt beskrivs hur du använder en hanterad identitet från Azure Active Directory (Azure AD) för att komma åt Azure Time Series Insights-API:et. I Azure eliminerar hanterade identiteter behovet av att utvecklare måste hantera autentiseringsuppgifter genom att tillhandahålla en identitet för Azure-resursen i Azure AD och använda den för att hämta Azure Active Directory-token (Azure AD). Här är några av fördelarna med att använda hanterade identiteter:

- Du behöver inte hantera autentiseringsuppgifter. Autentiseringsuppgifterna är inte ens tillgängliga för dig.
- Du kan använda hanterade identiteter för att autentisera till alla Azure-tjänster som stöder Azure AD-autentisering, inklusive Azure Key Vault.
- Hanterade identiteter kan användas utan extra kostnad.

Mer information om de två typerna av hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md)

Du kan använda hanterade identiteter från:

- Virtuella Azure-datorer
- Azure App Services
- Azure Functions
- Azure Container Instances
- och mycket mer ...

En [fullständig lista finns i Azure-tjänster som stöder hanterade identiteter](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) för Azure-resurser.

## <a name="azure-active-directory-app-registration"></a>Azure Active Directory appregistrering

Vi rekommenderar att du använder hanterade identiteter när det är möjligt så att du inte behöver hantera autentiseringsuppgifter. Om klientprogrammet inte finns på en Azure-tjänst som stöder hanterade identiteter kan du registrera programmet med en Azure AD-klientorganisation. När du registrerar ditt program med Azure AD skapar du en identitetskonfiguration för ditt program som gör att det kan integreras med Azure AD. När du registrerar en app i [Azure Portal](https://portal.azure.com/)väljer du om det är en enda klient (endast tillgänglig i din klient) eller flera klienter (tillgänglig i andra klienter) och kan även ange en omdirigerings-URI (där åtkomsttoken skickas till).

När du har slutfört appregistreringen har du en globalt unik instans av appen (programobjektet) som finns i din hemklient eller katalog. Du har också ett globalt unikt ID för din app (appens eller klientens ID). I portalen kan du sedan lägga till hemligheter eller certifikat och omfång för att få appen att fungera, anpassa appanpassad anpassning i inloggningsdialogrutan med mera.

Om du registrerar ett program i portalen skapas automatiskt ett programobjekt och ett objekt för tjänstens huvudnamn i din hemklientorganisation. Om du registrerar/skapar ett program med hjälp Microsoft Graph API:er är det ett separat steg att skapa objektet för tjänstens huvudnamn. Ett objekt för tjänstens huvudnamn krävs för att begära token.

Läs säkerhetschecklistan [](../active-directory/develop/identity-platform-integration-checklist.md#security) för ditt program. Som bästa praxis bör du använda [certifikatautentiseringsuppgifter](../active-directory/develop/active-directory-certificate-credentials.md), inte lösenordsuppgifter (klienthemligheter).

Se [Program- och tjänsthuvudnamnsobjekt i Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md) mer information.

## <a name="step-1-create-your-managed-identity-or-app-registration"></a>Steg 1: Skapa din hanterade identitet eller appregistrering

När du har identifierat om du ska använda en hanterad identitet eller appregistrering är nästa steg att etablera en.

### <a name="managed-identity"></a>Hanterad identitet

De steg som du använder för att skapa en hanterad identitet varierar beroende på var koden finns och om du skapar en system tilldelad eller användar tilldelad identitet. Läs [Hanterade identitetstyper](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) för att förstå skillnaden. När du har valt identitetstyp letar du upp och följer rätt självstudie i dokumentationen om Azure AD-hanterade [identiteter.](../active-directory/managed-identities-azure-resources/index.yml) Där hittar du anvisningar för hur du konfigurerar hanterade identiteter för:

- [Virtuella Azure-datorer](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-during-creation-of-a-vm)
- [App Service och Azure Functions](../app-service/overview-managed-identity.md)
- [Azure Container Instances](../container-instances/container-instances-managed-identity.md)
- och mycket mer ...

### <a name="application-registration"></a>Programregistrering

Följ stegen i Registrera [ett program.](../active-directory/develop/quickstart-register-app.md#register-an-application)

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="step-2-grant-access"></a>Steg 2: Bevilja åtkomst

När din Azure Time Series Insights miljö tar emot en begäran verifieras först anropartoken för anroparen. Om valideringen godkänns har anroparen autentiserats och sedan görs en ny kontroll för att säkerställa att anroparen har behörighet att utföra den begärda åtgärden. Om du vill auktorisera användare eller tjänstens huvudnamn måste du först ge dem åtkomst till miljön genom att tilldela dem rollen Läsare eller Deltagare.

- Om du vill bevilja åtkomst [via Azure Portal](https://portal.azure.com/) användargränssnitt följer du anvisningarna i artikeln Bevilja data åtkomst [till en](concepts-access-policies.md) miljö. När du väljer användaren kan du söka efter den hanterade identiteten eller appregistreringen med dess namn eller ID.

- Kör följande kommando för att bevilja åtkomst med hjälp av Azure CLI. Läs dokumentationen här [för en](/cli/azure/ext/timeseriesinsights/tsi/access-policy) fullständig lista över kommandon som är tillgängliga för att hantera åtkomst.

   ```azurecli-interactive
   az tsi access-policy create --name "ap1" --environment-name "env1" --description "some description" --principal-object-id "aGuid" --roles Reader Contributor --resource-group "rg1"
   ```

> [!Note]
> Tillägget timeseriesinsights för Azure CLI kräver version 2.11.0 eller senare. Tillägget installeras automatiskt första gången du kör kommandot az tsi access-policy. [Läs mer](/cli/azure/azure-cli-extensions-overview) om tillägg.

## <a name="step-3-requesting-tokens"></a>Steg 3: Begära token

När din hanterade identitet eller appregistrering har etablerats och tilldelats en roll är du redo att börja använda den för att begära OAuth 2.0-bearer-token. Vilken metod du använder för att hämta en token varierar beroende på var koden finns och vilket språk du väljer. När du anger resursen (även kallad "målgruppen" för token) kan du identifiera Azure Time Series Insights url eller GUID:

* `https://api.timeseries.azure.com/`
* `120d688d-1518-4cf7-bd38-182f158850b6`

> [!IMPORTANT]
> Om du använder URL:en som resurs-ID måste token utfärdas exakt till `https://api.timeseries.azure.com/` . Avslutande snedstreck krävs.

> * Om du [använder Postman](https://www.getpostman.com/) **blir din AuthURL** därför: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/` är giltigt men `https://api.timeseries.azure.com` inte.

### <a name="managed-identities"></a>Hanterade identiteter

Vid åtkomst från Azure App Service eller Functions följer du riktlinjerna i [Hämta token för Azure-resurser.](../app-service/overview-managed-identity.md)

För .NET-program och -funktioner är det enklaste sättet att arbeta med en hanterad identitet via [Azure Identity-klientbiblioteket](/dotnet/api/overview/azure/identity-readme) för .NET. Det här klientbiblioteket är populärt på grund av dess enkelhet och säkerhetsfördelar. Utvecklare kan skriva kod en gång och låta klientbiblioteket bestämma hur det ska autentiseras baserat på programmiljön – oavsett om det är på en utvecklararbetsstationer som använder ett utvecklarkonto eller distribueras i Azure med hjälp av en hanterad tjänstidentitet. Migreringsvägledning från föregående AppAuthentication-bibliotek [finns i AppAuthentication to Azure.Identity Migration Guidance (Vägledning för appauthentication till Azure.Identity Migration Guidance).](/dotnet/api/overview/azure/app-auth-migration)

Begär en token för Azure Time Series Insights C# och Azure Identity-klientbiblioteket för .NET:

   ```csharp
   using Azure.Identity;
   // ...
   var credential = new DefaultAzureCredential();
   var token = credential.GetToken(
   new Azure.Core.TokenRequestContext(
       new[] { "https://api.timeseries.azure.com/" }));
   var accessToken = token.Token;
   ```

### <a name="app-registration"></a>Appregistrering

* Utvecklare kan använda [Microsoft Authentication Library](../active-directory/develop/msal-overview.md) (MSAL) för att hämta token för appregistreringar.

MSAL kan användas i många programscenarier, inklusive, men inte begränsat till:

* [Ensidesprogram (JavaScript)](../active-directory/develop/scenario-spa-overview.md)
* [Webbprogram som loggar in en användare och anropar ett webb-API för användarens räkning](../active-directory/develop/scenario-web-app-call-api-overview.md)
* [Webb-API anropar ett annat nedströms-webb-API för den inloggade användarens räkning](../active-directory/develop/scenario-web-api-call-api-overview.md)
* [Skrivbordsprogram som anropar ett webb-API för den inloggade användarens räkning](../active-directory/develop/scenario-desktop-overview.md)
* [Mobilprogram som anropar ett webb-API för den användare som är inloggad interaktivt](../active-directory/develop/scenario-mobile-overview.md).
* [Skrivbords-/tjänstdaemonprogram som anropar webb-API för sig själv](../active-directory/develop/scenario-daemon-overview.md)

För C#-exempelkod som visar hur du hämtar en token som en appregistrering och frågar efter data från en Gen2-miljö kan du visa exempelappen på [GitHub](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/Program.cs)

> [!IMPORTANT]
> Om du använder [Azure Active Directory Authentication Library (ADAL) kan](../active-directory/azuread-dev/active-directory-authentication-libraries.md) du läsa om att migrera till [MSAL](../active-directory/develop/msal-net-migration.md).

## <a name="common-headers-and-parameters"></a>Vanliga huvuden och parametrar

I det här avsnittet beskrivs vanliga HTTP-begärandehuvuden och parametrar som används för att skapa frågor mot Azure Time Series Insights Gen1- och Gen2-API:er. API-specifika krav tas upp i detalj i Azure Time Series Insights REST API [referensdokumentationen](/rest/api/time-series-insights/).

> [!TIP]
> Läs Referens [för Azure REST API om du](/rest/api/azure/) vill veta mer om hur du använder REST API:er, gör HTTP-begäranden och hanterar HTTP-svar.

### <a name="http-headers"></a>HTTP-huvuden

Obligatoriska begärandehuvuden beskrivs nedan.

| Begärandehuvud som krävs | Beskrivning |
| --- | --- |
| Auktorisering | För att autentisera med Azure Time Series Insights måste en giltig OAuth 2.0-token för bearer skickas i [auktoriseringsrubriken](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate). |

> [!TIP]
> Läs exempelvisualiseringen Azure Time Series Insights [klient-SDK](https://tsiclientsample.azurewebsites.net/) för att läsa om hur du autentiserar med Azure Time Series Insights-API:er programmatiskt med [javaScript-klient-SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) tillsammans med diagram och grafer.

Valfria begärandehuvuden beskrivs nedan.

| Valfritt begärandehuvud | Beskrivning |
| --- | --- |
| Innehållstyp | stöds `application/json` endast. |
| x-ms-client-request-id | Ett klientbegärande-ID. Tjänsten registrerar det här värdet. Tillåter att tjänsten spårar åtgärd mellan tjänster. |
| x-ms-client-session-id | Ett klientsessions-ID. Tjänsten registrerar det här värdet. Gör att tjänsten kan spåra en grupp relaterade åtgärder mellan tjänster. |
| x-ms-client-application-name | Namnet på programmet som genererade begäran. Tjänsten registrerar det här värdet. |

Valfria men rekommenderade svarshuvuden beskrivs nedan.

| Svarshuvud | Beskrivning |
| --- | --- |
| Innehållstyp | Endast `application/json` stöds. |
| x-ms-request-id | Servergenererat begärande-ID. Kan användas för att kontakta Microsoft för att undersöka en begäran. |
| x-ms-property-not-found-behavior | GA API valfritt svarshuvud. Möjliga värden är `ThrowError` (standard) eller `UseNull` . |

### <a name="http-parameters"></a>HTTP-parametrar

> [!TIP]
> Mer information om nödvändig och valfri frågeinformation finns i [referensdokumentationen](/rest/api/time-series-insights/).

Obligatoriska URL-frågesträngsparametrar beror på API-versionen.

| Frisläpp | API-versionsvärden |
| --- |  --- |
| Gen1 | `api-version=2016-12-12`|
| Gen2 | `api-version=2020-07-31`|

Valfria URL-frågesträngsparametrar omfattar att ange en tidsgräns för körningstider för HTTP-begäranden.

| Valfri frågeparameter | Beskrivning | Version |
| --- |  --- | --- |
| `timeout=<timeout>` | Tidsgräns på serversidan för körning av HTTP-begäran. Gäller endast för [API:erna Hämta miljöhändelser](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) [och Hämta miljöaggregeringar.](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api) Tidsgränsvärdet ska till exempel vara i ISO 8601-varaktighetsformat `"PT20S"` och ska vara i intervallet `1-30 s` . Standardvärdet är `30 s`. | Gen1 |
| `storeType=<storeType>` | För Gen2-miljöer med varm lagring aktiverat kan frågan köras antingen på `WarmStore` eller `ColdStore` . Den här parametern i frågan definierar vilket arkiv frågan ska köras på. Om den inte definieras körs frågan i kalllagret. Om du vill köra frågor mot det **varma arkivet** måste storeType anges till `WarmStore` . Om den inte definieras körs frågan mot kalllagret. | Gen2 |

## <a name="next-steps"></a>Nästa steg

* Exempelkod som anropar Gen1 Azure Time Series Insights-API:et finns i [Query Gen1 data using C# (Fråga Gen1-data med C#](./time-series-insights-query-data-csharp.md)).

* Exempelkod som anropar Gen2 Azure Time Series Insights API-kodexempel finns i [Query Gen2 data using C# (Fråga Gen2-data med C#](./time-series-insights-update-query-data-csharp.md)).

* Api-referensinformation finns i [referensdokumentationen för fråge-API.](/rest/api/time-series-insights/reference-query-apis)
