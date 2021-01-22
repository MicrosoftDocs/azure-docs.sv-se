---
title: Check lista för Media Services v2 till v3-migrering | Microsoft Docs
description: Den här artikeln är en check lista som hjälper dig att få minsta migrering från Azure Media Services v2 till v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: Azure Media Services, migrering, Stream, broadcast, Live, SDK
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2021
ms.author: inhenkel
ms.openlocfilehash: 7d9597c16778a4ded7c1e2a7ed3ad5535b75cfde
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690482"
---
# <a name="step-3---set-up-to-migrate-to-the-v3-rest-api-or-client-sdk"></a>Steg 3 – Konfigurera för att migrera till v3-REST API eller klient-SDK

![logo typ för migrations guide](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![steg 2 i migreringen](./media/migration-guide/steps-3.svg)

Här följer en beskrivning av de steg som krävs för att konfigurera din miljö för att använda Media Services v3-API.

## <a name="sdk-model"></a>SDK-modell

I v2-API: t fanns det två olika klient-SDK: er, en för hanterings-API, som tillät programmerings skapande av konton och en för resurs hantering.

Tidigare fungerade utvecklare med ett klient-ID och klient hemlighet för Azure-tjänstens huvud namn, tillsammans med en speciell v2 REST API-slutpunkt för sitt AMS-konto.

V3-API: et är Azure Resource Management (ARM) baserad. Den använder Azure Active Directory (Azure AD) tjänstens huvud namns-ID och nycklar för att ansluta till API: et. Utvecklare måste skapa tjänstens huvud namn eller hanterade identiteter för att ansluta till API: et. I v3-API: et använder API: t standard ARM-slutpunkter, använder en liknande och konsekvent modell för alla andra Azure-tjänster.

Kunder som tidigare använde 2015-10-01-versionen av hanterings-API: et för att hantera sina v2-konton bör använda 2020-05-01-versionen av ARM Management-API: et som stöds för v3 API-åtkomst.

## <a name="create-a-new-media-services-account-for-testing"></a>Skapa ett nytt Media Services-konto för testning

Följ anvisningarna för snabb start för att konfigurera [din miljö](how-to-set-azure-subscription.md?tabs=portal) med hjälp av Azure Portal. Välj API-åtkomst och autentisering av tjänstens huvud namn för att generera ett nytt Azure AD-program-ID och hemligheter för användning med det här test kontot.

[Skapa ett Media Services-konto](create-account-howto.md?tabs=portal).
[Få autentiseringsuppgifter för åtkomst till Media Services API](access-api-howto.md?tabs=portal).

## <a name="download-client-sdk-of-your-choice-and-set-up-your-environment"></a>Ladda ned valfri klient-SDK och konfigurera din miljö

- SDK: er är tillgängliga för [.net](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet&preserve-view=true), .net Core, [Node.js](https://docs.microsoft.com/javascript/api/overview/azure/mediaservices/management?view=azure-node-latest&preserve-view=true), [python](https://docs.microsoft.com/python/api/overview/azure/mediaservices/management?view=azure-python&preserve-view=true), [Java](https://docs.microsoft.com/java/api/overview/azure/mediaservices/management?view=azure-java-stable&preserve-view=true), [Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media)och [ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md).
- [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest&preserve-view=true)   integrering för enkel skript support.

> [!NOTE]
> Det finns inte längre någon community PHP-SDK tillgänglig för Azure Media Services på v3. Om du använder PHP på v2 bör du migrera till REST API direkt i din kod.

## <a name="open-api-specifications"></a>Öppna API-specifikationer

- V3 baseras på en enhetlig API-yta som visar både hanterings-och drift funktioner som bygger på Azure Resource Manager. Azure Resource Manager mallar kan användas för att skapa och distribuera transformeringar, slut punkter för direkt uppspelning, Live-händelser och mycket annat.

- [Openapi specifikation](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01) (tidigare kallat Swagger) dokument förklarar schemat för alla tjänst komponenter.

- Alla klient-SDK: er härleds och genereras från Open API-specifikationen som publicerats på GitHub. Vid tidpunkten för publiceringen av den här artikeln underhålls de senaste öppna API-specifikationerna offentligt i GitHub. 2020-05-01-versionen är den [senaste stabila versionen](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01).

## <a name="rest"></a>[REST](#tab/rest)

Använd [Postman](https://docs.microsoft.com/azure/media-services/latest/media-rest-apis-with-postman) för Media Services v3 REST API-anrop.
Läs [REST API referens sidor](https://docs.microsoft.com/rest/api/media/).

Du bör använda versions strängen 2020-05-01 i Postman-samlingen.

## <a name="net"></a>[.NET](#tab/net)

Läs artikeln, [Anslut till Media Services v3 API med .net](configure-connect-dotnet-howto.md) för att konfigurera din miljö.

Om du bara vill installera den senaste SDK: n med hjälp av PackageManager, använder du följande kommando:

```Install-Package Microsoft.Azure.Management.Media```

Eller installera den senaste SDK: n med .NET CLI med hjälp av följande kommando:

```dotnet add package Microsoft.Azure.Management.Media```

Dessutom är fullständiga .NET-exempel tillgängliga i [Azure-samples/Media-Services-v3-dotNet](https://github.com/Azure-Samples/media-services-v3-dotnet) för olika scenarier. Projekten i den här lagrings platsen visar hur du implementerar olika Azure Media Services scenarier med hjälp av v3-versionen.

### <a name="get-started-adjusting-your-code"></a>Kom igång med att justera koden

Sök i kodbasen efter instanser av `CloudMediaContext` användning för att starta uppgraderings processen till v3-API: et.

Följande kod visar hur v2-API: et tidigare användes med v2 .NET SDK. Utvecklare börjar med att skapa en `CloudMediaContext` och skapa en instans med ett `AzureAdTokenCredentials` objekt.

```dotnet

class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

```

## <a name="java"></a>[Java](#tab/java)

Läs artikeln, [Anslut till Media Services v3 API med Java](configure-connect-java-howto.md) för att konfigurera din miljö.

## <a name="python"></a>[Python](#tab/python)

Läs artikeln, [Anslut till Azure Media Services v3 API-python](configure-connect-python-howto.md) för att konfigurera din miljö.

## <a name="nodejs"></a>[Node.js](#tab/nodejs)

Läs artikeln [Anslut till Azure Media Services v3 API-Node.js](configure-connect-nodejs-howto.md) för att konfigurera din miljö.

## <a name="ruby"></a>[Ruby](#tab/ruby)

- Hämta [ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md) SDK på GitHub.

## <a name="go"></a>[Kör](#tab/go)

Ladda ned [Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media) SDK.

---

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]