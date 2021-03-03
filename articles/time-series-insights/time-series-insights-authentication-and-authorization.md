---
title: API-autentisering och auktorisering – Azure Time Series Insights | Microsoft Docs
description: 'Den här artikeln beskriver hur du konfigurerar autentisering och auktorisering för ett anpassat program som anropar Azure Time Series Insights-API: et.'
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/23/2021
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 58c0f408e3ad80109efd3db79d6e4a0d881aed78
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724188"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autentisering och auktorisering för Azure Time Series Insights API

Beroende på dina affärs behov kan din lösning innehålla ett eller flera klient program som du använder för att interagera med din Azure Time Series Insights-Miljös [API: er](https://docs.microsoft.com/en-us/rest/api/time-series-insights/reference-data-access-overview). Azure Time Series Insights utför autentisering med [Azure AD-säkerhetstoken baserat på OAUTH 2,0](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims). För att autentisera dina klienter måste du skaffa en Bearer-token med rätt behörigheter och skicka den tillsammans med dina API-anrop. I det här dokumentet beskrivs flera autentiseringsuppgifter för att hämta metoder som du kan använda för att hämta en Bearer-token och autentisera.


  registrera en app i Azure Active Directory att använda bladet ny Azure Active Directory. Appar som registrerats i Azure Active Directory gör det möjligt för användare att autentisera till och ha behörighet att använda Azure Time Series Insight-API som är associerat med en Azure Time Series Insights miljö.

## <a name="managed-identities"></a>Hanterade identiteter

I följande avsnitt beskrivs hur du använder en hanterad identitet från Azure Active Directory (Azure AD) för att få åtkomst till Azure Time Series Insights API. I Azure eliminerar Managed identiteter behovet av utvecklare som behöver hantera autentiseringsuppgifter genom att tillhandahålla en identitet för Azure-resursen i Azure AD och använda den för att hämta Azure Active Directory (Azure AD)-token. Här följer några av fördelarna med att använda hanterade identiteter:

- Du behöver inte hantera autentiseringsuppgifter. Autentiseringsuppgifterna är inte ens tillgängliga för dig.
- Du kan använda hanterade identiteter för att autentisera till valfri Azure-tjänst som stöder Azure AD-autentisering, inklusive Azure Key Vault.
- Hanterade identiteter kan användas utan ytterligare kostnader.

Mer information om de två typerna av hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Du kan använda hanterade identiteter från din:

- Virtuella Azure-datorer
- Azure App Services
- Azure Functions
- Azure Container instances
- med mera...

Se [Azure-tjänster som har stöd för hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-managed-identities-for-azure-resources) för den fullständiga listan.

## <a name="azure-active-directory-app-registration"></a>Azure Active Directory app-registrering

Vi rekommenderar att du använder hanterade identiteter närhelst det är möjligt, så att du inte behöver hantera autentiseringsuppgifter. Om klient programmet inte finns på en Azure-tjänst som har stöd för hanterade identiteter kan du registrera ditt program med en Azure AD-klient. När du registrerar ditt program med Azure AD skapar du en identitets konfiguration för ditt program som gör det möjligt att integrera med Azure AD. När du registrerar en app i [Azure Portal](https://portal.azure.com/)väljer du om det är en enskild klient (endast tillgänglig i din klient) eller flera innehavare (tillgänglig i andra klienter) och om du vill kan du ange en omdirigerings-URI (där åtkomsttoken skickas till).

När du har slutfört registreringen av appen har du en globalt unik instans av appen (programobjektet) som finns i din hem klient eller katalog. Du har också ett globalt unikt ID för din app (appen eller klient-ID: t). I portalen kan du sedan lägga till hemligheter eller certifikat och omfattningar för att din app ska fungera, anpassa appens anpassning i dialog rutan för inloggning med mera.

Om du registrerar ett program i portalen skapas ett program objekt och ett huvud namn för tjänsten automatiskt i din hem klient. Om du registrerar/skapar ett program med hjälp av Microsoft Graph-API: erna, är det ett separat steg att skapa tjänstens huvud namns objekt. Ett huvud objekt för tjänsten krävs för att begära token.

Se till att granska [säkerhets](https://docs.microsoft.com/azure/active-directory/develop/identity-platform-integration-checklist#security) check listan för ditt program. Som bästa praxis bör du använda autentiseringsuppgifter för [certifikat](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials), inte autentiseringsuppgifter för lösen ord (klient hemligheter).

Se [program-och tjänst huvud objekt i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) för mer information.

## <a name="step-1-create-your-managed-identity-or-app-registration"></a>Steg 1: skapa din hanterade identitet eller registrera din app

När du har identifierat om du ska använda en hanterad identitets-eller app-registrering är nästa steg att etablera en.

### <a name="managed-identity"></a>Hanterad identitet

De steg som du ska använda för att skapa en hanterad identitet varierar beroende på var koden finns och huruvida du skapar ett systemtilldelat eller en tilldelad identitet. Läs [hanterade identitets typer](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview#managed-identity-types) för att förstå skillnaden. När du har valt din identitets typ letar du reda på och följer rätt självstudie i [dokumentationen](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/)för Azure AD-hanterade identiteter. Där hittar du instruktioner för hur du konfigurerar hanterade identiteter för:

- [Virtuella Azure-datorer](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#enable-system-assigned-managed-identity-during-creation-of-a-vm)
- [App Service och Azure Functions](https://docs.microsoft.com/azure/app-service/overview-managed-identity)
- [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-managed-identity)
- med mera...

### <a name="application-registration"></a>Programregistrering

Följ stegen som visas i [Registrera ett program](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app#register-an-application).

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="step-2-grant-access"></a>Steg 2: bevilja åtkomst

När din Azure Time Series Insights-miljö tar emot en begäran, verifieras först anroparens token-token. Om verifieringen lyckas har anroparen autentiserats och en annan kontroll görs för att se till att anroparen har behörighet att utföra den begärda åtgärden. Om du vill auktorisera användarens eller tjänstens huvud namn måste du först ge dem åtkomst till miljön genom att tilldela dem antingen rollen läsare eller deltagare.

- Om du vill bevilja åtkomst via [Azure Portal](https://portal.azure.com/) användar gränssnitt, följer du instruktionerna som anges i artikeln [ge åtkomst till en miljö](https://docs.microsoft.com/azure/time-series-insights/concepts-access-policies) . När du väljer användaren kan du söka efter den hanterade identiteten eller appens registrering med hjälp av namnet eller med ID.

- Kör följande kommando för att bevilja åtkomst med hjälp av Azure CLI. Läs dokumentationen [här](https://docs.microsoft.com/cli/azure/ext/timeseriesinsights/tsi/access-policy?view=azure-cli-latest) för en fullständig lista över kommandon som är tillgängliga för att hantera åtkomst.

   ```azurecli-interactive
   az tsi access-policy create --name "ap1" --environment-name "env1" --description "some description" --principal-object-id "aGuid" --roles Reader Contributor --resource-group "rg1"
   ```

> [!Note]
> Timeseriesinsights-tillägget för Azure CLI kräver version 2.11.0 eller senare. Tillägget installeras automatiskt första gången du kör ett AZ-kommando för åtkomst princip för TSD. [Läs mer](https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview) om tillägg.

## <a name="step-3-requesting-tokens"></a>Steg 3: begära token

När din hanterade identitet eller app-registrering har etablerats och tilldelats en roll, är du redo att börja använda den för att begära OAuth 2,0 Bearer-token. Vilken metod du använder för att hämta en token varierar beroende på var din kod finns och vilket språk du väljer. När du anger resursen (kallas även "mål grupp" för token) kan du identifiera Azure Time Series Insights via URL: en eller GUID:

* `https://api.timeseries.azure.com/`
* `120d688d-1518-4cf7-bd38-182f158850b6`

> [!IMPORTANT]
> Om du använder webb adressen som resurs-ID måste token utfärdas exakt till `https://api.timeseries.azure.com/` . Avslutande snedstreck krävs.

> * Om du använder [Postman](https://www.getpostman.com/) kommer din **AuthURL** därför att vara: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/` är giltig men `https://api.timeseries.azure.com` inte.

### <a name="managed-identities"></a>Hanterade identiteter

När du kommer åt från Azure App Service eller funktioner följer du rikt linjerna i [Hämta token för Azure-resurser](https://docs.microsoft.com/azure/app-service/overview-managed-identity).

> [!TIP]
> För .NET-program och-funktioner är det enklaste sättet att arbeta med en hanterad identitet via [klient biblioteket för Azure Identity](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme) för .net. 

För .NET-program och-funktioner är det enklaste sättet att arbeta med en hanterad identitet via paketet Microsoft. Azure. Services. AppAuthentication. Det här paketet är populärt på grund av dess enkelhet-och säkerhets förmåner. Utvecklare kan skriva kod en gång och låta klient biblioteket bestämma hur de ska autentiseras baserat på program miljön – om en utvecklares arbets Station använder ett utvecklares konto eller distribueras i Azure med hjälp av en hanterad tjänst identitet. För information om migrering från det föregående AppAuthentication-biblioteket läser [du AppAuthentication till Azure. vägledning för identitets migrering](https://docs.microsoft.com/dotnet/api/overview/azure/app-auth-migration?view=azure-dotnet).

Begär en token för Azure Time Series Insights med C# och klient biblioteket för Azure Identity för .NET:

    ```csharp
    using Azure.Identity;
    // ...
    var credential = new DefaultAzureCredential();
    var token = credential.GetToken(
    new Azure.Core.TokenRequestContext(
        new[] { "https://api.timeseries.azure.com/" }));
   var accessToken = token. Åtkomsttokenbegäran
    ```

### <a name="app-registration"></a>Appregistrering

* Utvecklare kan använda [Microsoft Authentication Library](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) (MSAL) för att hämta token för registrerings program.

MSAL kan användas i många program scenarier, inklusive, men inte begränsat till:

* [Program med en sida (Java Script)](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-overview.md)
* [Webb program som loggar in en användare och anropar ett webb-API för användarens räkning](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-overview.md)
* [Webb-API som anropar ett annat underordnat webb-API å den inloggade användarens vägnar](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-overview.md)
* [Skriv bords program som anropar ett webb-API å den inloggade användarens vägnar](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-overview.md)
* [Mobil program som anropar ett webb-API åt användaren som är inloggad interaktivt](https://docs.microsoft.com/azure/active-directory/develop/scenario-mobile-overview.md).
* [Desktop/Service daemon-program som anropar webb-API: et åt sig själv](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-overview.md)

För exempel C#-kod som visar hur man hämtar en token som en app-registrering och fråga data från en Gen2-miljö, se exempel appen på [GitHub](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/Program.cs)

> [!IMPORTANT]
> Om du använder [Azure Active Directory Authentication Library (ADAL)](../active-directory/azuread-dev/active-directory-authentication-libraries.md) läser du om [migrering till MSAL](../active-directory/develop/msal-net-migration.md).

## <a name="common-headers-and-parameters"></a>Vanliga rubriker och parametrar

I det här avsnittet beskrivs vanliga HTTP-begärandehuvuden och parametrar som används för att skapa frågor mot Azure Time Series Insights gen1-och Gen2-API: er. API-särskilda krav beskrivs mer detaljerat i [Azure Time Series Insights REST API referens dokumentation](/rest/api/time-series-insights/).

> [!TIP]
> Läs [Azure REST API-referensen](/rest/api/azure/) om du vill veta mer om hur du använder REST-API: er, gör HTTP-begäranden och hanterar http-svar.

### <a name="http-headers"></a>HTTP-rubriker

Obligatoriska begärandehuvuden beskrivs nedan.

| Nödvändigt begär ande huvud | Beskrivning |
| --- | --- |
| Auktorisering | För att autentisera med Azure Time Series Insights måste en giltig OAuth 2,0 Bearer-token skickas i [Authorization-huvudet](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate). |

> [!TIP]
> Läs den värdbaserade Azure Time Series Insights [SDK-exemplet för klient-SDK](https://tsiclientsample.azurewebsites.net/) för att lära dig att autentisera med Azure Time Series Insights-API: er program mässigt med hjälp av [Java Script client SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) tillsammans med diagram och diagram.

Valfria begärandehuvuden beskrivs nedan.

| Valfritt begärandehuvud | Beskrivning |
| --- | --- |
| Innehålls typ | `application/json`stöds endast. |
| x-MS-client-Request-ID | En klientbegärans-ID. Tjänsten registrerar det här värdet. Tillåter att tjänsten spårar åtgärder mellan tjänster. |
| x-MS-client-session-ID | Ett klientsession-ID. Tjänsten registrerar det här värdet. Tillåter tjänsten att spåra en grupp relaterade åtgärder mellan tjänster. |
| x-MS-client-program-Name | Namnet på programmet som skapade den här begäran. Tjänsten registrerar det här värdet. |

Valfria men rekommenderade svarshuvuden beskrivs nedan.

| Svars huvud | Beskrivning |
| --- | --- |
| Innehålls typ | `application/json`Stöds endast. |
| x-MS-Request-ID | Server-genererat fråge-ID. Kan användas för att kontakta Microsoft för att undersöka en begäran. |
| x-MS-Property-not-found-Behavior | Huvud alternativ svars rubrik för GA API. Möjliga värden är `ThrowError` (standard) eller `UseNull` . |

### <a name="http-parameters"></a>HTTP-parametrar

> [!TIP]
> Hitta mer information om obligatoriska och valfria frågedata i [referens dokumentationen](/rest/api/time-series-insights/).

Obligatoriska URL-frågeparametrar är beroende av API-versionen.

| Frisläpp | API-versions värden |
| --- |  --- |
| Gen1 | `api-version=2016-12-12`|
| Gen2 | `api-version=2020-07-31`|

Valfria URL-parametrar för frågesträngar inkluderar att ange en tids gräns för körnings tider för HTTP-begäran.

| Valfri frågeparameter | Beskrivning | Version |
| --- |  --- | --- |
| `timeout=<timeout>` | Tids gräns på Server sidan för körning av HTTP-begäran. Gäller endast för [Hämta miljö händelser](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) och hämta API: er för [samling av miljö](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api) . Timeout-värdet ska vara i ISO 8601-varaktighets format, till exempel `"PT20S"` och måste vara inom intervallet `1-30 s` . Standardvärdet är `30 s`. | Gen1 |
| `storeType=<storeType>` | För Gen2-miljöer där varm lagring är aktiverat kan frågan köras antingen på `WarmStore` eller `ColdStore` . Den här parametern i frågan definierar i vilken lagring frågan ska köras. Om den inte är definierad körs frågan på kall butiken. **StoreType** måste ställas in på för att kunna fråga det varmt arkivet `WarmStore` . Om den inte är definierad utförs frågan mot kyl lagret. | Gen2 |

## <a name="next-steps"></a>Nästa steg

* För exempel kod som anropar gen1 Azure Time Series Insights API läser du [fråga gen1 data med C#](./time-series-insights-query-data-csharp.md).

* För exempel kod som anropar Gen2 Azure Time Series Insights API-kod exempel läser du [fråga Gen2 data med C#](./time-series-insights-update-query-data-csharp.md).

* Information om API-referensinformation finns i [referens](/rest/api/time-series-insights/reference-query-apis) dokumentationen för fråge-API.