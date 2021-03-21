---
title: Översikt över Azure Media Services | Microsoft Docs
description: Microsoft Azure Media Services är en utökningsbar molnbaserad plattform som gör att utvecklare kan skapa skalbara program för mediehantering och medieleverans. Den här artikeln ger en översikt över Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 6e68e53387aa50b99bab8ed4cdba7f1e97fc48c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008248"
---
# <a name="azure-media-services-overview"></a>Översikt över Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Välj den version av Media Services som du använder:"]
> * [Version 3](../latest/media-services-overview.md)
> * [Version 2](media-services-overview.md)

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Microsoft Azure Media Services (AMS) är en utökningsbar molnbaserad plattform som gör att utvecklare kan skapa skalbara program för mediehantering och medieleverans. Media Services baseras på REST API:er som gör det möjligt att på ett säkert sätt överföra, lagra, koda och paketera video- eller ljudinnehåll för att strömma både på begäran och live till olika klienter (till exempel TV, datorer och mobila enheter).

Du kan bygga arbetsflöden för slutpunkt till slutpunkt bara med Media Services. Du kan också välja att använda komponenter från tredje part för vissa delar av arbetsflödet. Koda till exempel med hjälp av en kodare från tredje part. Därefter kan du överföra, skydda, paketera och leverera med Media Services. Du kan välja att strömma ditt innehåll live eller leverera innehåll på begäran. 


## <a name="compliance-privacy-and-security"></a>Efterlevnad, sekretess och säkerhet

Som en viktig påminnelse måste du följa alla tillämpliga lagar i din användning av Azure Media Services och du får inte använda Media Services eller någon Azure-tjänst på ett sätt som strider mot andras rättigheter eller som kan vara skadliga för andra.

Innan du laddar upp en video/bild till Media Services måste du ha alla de rättigheter som krävs för att använda videon/bilden, inklusive, vid behov enligt lag, alla nödvändiga samspelade individer (om de finns) i videon/bilden, för användning, bearbetning och lagring av data i Media Services och Azure. Vissa jurisdiktioner kan införa särskilda juridiska krav för insamling, online-bearbetning och lagring av vissa kategorier av data, t. ex. Bio metriska data. Innan du använder Media Services och Azure för bearbetning och lagring av data som omfattas av särskilda juridiska krav, måste du säkerställa efterlevnaden av sådana juridiska krav som kan gälla för dig.

Om du vill veta mer om efterlevnad, sekretess och säkerhet i Media Services går du till Microsoft [Trust Center](https://www.microsoft.com/trust-center/?rtc=1). För Microsofts sekretess krav, data hantering och bevarande praxis, inklusive hur du tar bort dina data, kan du läsa Microsofts [sekretess policy](https://privacy.microsoft.com/PrivacyStatement), [Online Services-villkoren](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("ost") och [data bearbetnings tillägget](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Genom att använda Media Services samtycker du till att vara kopplad till OST-, DPA-och sekretess policyn.
 
## <a name="prerequisites"></a>Förutsättningar

Om du vill börja använda Azure Media Services ska du ha följande:

* Ett Azure-konto. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com).
* Ett Azure Media Services-konto. Mer information finns i [Skapa konto](media-services-portal-create-account.md).
* (Valfritt) Konfigurera utvecklingsmiljön. Välj .NET eller REST API för din utvecklingsmiljö. Mer information finns i [Ställa in miljön](media-services-dotnet-how-to-use.md).

    Lär dig även att [ansluta programmässigt till AMS API](media-services-use-aad-auth-to-access-ams-api.md).
* En standard- eller premiumslutpunkt för direktuppspelning med tillståndet Startad.  Mer information finns i [Hantera strömningsslutpunkter](media-services-portal-manage-streaming-endpoints.md).

## <a name="sdks-and-tools"></a>SDK:er och verktyg

Bygg Media Services-lösningar genom att använda:

* [Media Services REST API](/rest/api/media/operations/azure-media-services-rest-api-reference)
* En av de tillgängliga klient-SDK:erna:
    * Azure Media Services SDK för .NET
    
        * [NuGet-paket](https://www.nuget.org/packages/windowsazure.mediaservices/)
        * [GitHub käll kod](https://github.com/Azure/azure-sdk-for-media-services)
    * [Azure SDK för Java](https://github.com/Azure/azure-sdk-for-java),
    * [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php),
    * [Azure Media Services för Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (detta är en icke-Microsoft-version av en Node.js SDK. Den underhålls av ett community och har för närvarande inte en 100-procentig täckning av AMS API:er).
* Befintliga verktyg:
    * [Azure-portalen](https://portal.azure.com/)
    * [Azure-Media Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure Media Services Explorer (AMSE) är ett Winforms/C#-program för Windows)

> [!NOTE]
> Information om hur du hämtar den senaste versionen av Java SDK och börjar utveckla med Java finns i [Komma igång med Java-klient-SDK för Media Services](./media-services-java-how-to-use.md). <br/>
> Om du vill hämta den senaste PHP SDK för Media Services, leta efter version 0.5.7 av Microsoft/WindowAzure-paketet i [Packagist-databasen](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="code-samples"></a>Kodexempel

Du hittar flera exempel i galleriet med **kodexempel för Azure**: [kodexempel för Azure Media Services](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0).

## <a name="concepts"></a>Begrepp

Azure Media Services-koncepten finns i [Koncept](media-services-concepts.md).

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>Scenarier som stöds och tillgänglighet för Media Services över datacenter

Mer information om vanliga scenarier för Azure finns i [AMS-scenarier](scenarios-and-availability.md).
Information om regional tillgänglighet finns i [tillgänglighet för medie tjänster](availability-regions-v-2.md).

## <a name="service-level-agreement-sla"></a>Serviceavtal (SLA)

Mer information finns i [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/).

## <a name="support"></a>Support

[Azure-supporten](https://azure.microsoft.com/support/options/) tillhandahåller support för Azure, inklusive Media Services.

## <a name="provide-feedback"></a>Ge feedback

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
