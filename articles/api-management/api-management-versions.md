---
title: Versioner i Azure API Management | Microsoft Docs
description: Lär dig mer om begreppet versioner i Azure API Management.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 02/10/2021
ms.author: jodowns
ms.custom: fasttrack-new
ms.openlocfilehash: 9dc44b4b898603df88d1bdd8c36ddfb6449335ac
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547204"
---
# <a name="versions-in-azure-api-management"></a>Versioner i Azure API Management

Med versioner kan du presentera grupper av relaterade API: er för dina utvecklare. Du kan använda versioner för att hantera avbrytande ändringar i ditt API på ett säkert sätt. Klienter kan välja att använda din nya API-version när de är klara, medan befintliga klienter fortsätter att använda en äldre version. Versioner skiljer sig genom ett versions-ID (vilket är valfritt sträng värde som du väljer), och ett versions schema gör det möjligt för klienter att identifiera vilken version av ett API som de vill använda.

I de flesta fall kan varje API-version betraktas som sitt eget oberoende API. Två olika API-versioner kan ha olika uppsättningar med åtgärder och olika principer.

Med versioner kan du:

- Publicera flera versioner av ditt API på samma tidpunkt.
- Använd en sökväg, frågesträng eller rubrik för att skilja mellan olika versioner.
- Använd alla sträng värden som du vill identifiera din version, som kan vara ett tal, ett datum eller ett namn.
- Visa dina API-versioner grupperade tillsammans på Developer-portalen.
- Ta ett befintligt API (inte versions hantering) och skapa en ny version utan att behöva bryta befintliga klienter.

[Kom igång med versioner genom att följa vår genom gång.](./api-management-get-started-publish-versions.md)

## <a name="versioning-schemes"></a>Versions scheman

Olika API-utvecklare har olika krav för versions hantering. Azure API Management föreskriver inte en enda metod för versions hantering, men innehåller i stället flera alternativ.

### <a name="path-based-versioning"></a>Path-baserad versions hantering

När schema versions schema används måste versions-ID: t inkluderas i URL-sökvägen för alla API-begäranden.

Till exempel, `https://apis.contoso.com/products/v1` och `https://apis.contoso.com/products/v2` kan referera till samma `products` API, men till versioner `v1` respektive `v2` .

Formatet på en URL för API-begäran när du använder en rubrik baserad versions hantering är: `https://{yourDomain}/{apiName}/{versionIdentifier}/{operationId}` .

### <a name="header-based-versioning"></a>Rubrik baserad versions hantering

När huvud versions schemat används måste versions-ID: t inkluderas i HTTP-begärans huvud för alla API-begäranden. Du kan ange namnet på rubriken för HTTP-begäran.

Du kan till exempel skapa ett anpassat huvud namn `Api-Version` , och klienter kan ange `v1` eller `v2` i värdet för den här rubriken.

### <a name="query-string-based-versioning"></a>Fråga sträng-baserad version

När frågeuttrycket-schemat används måste versions-ID: t tas med i en frågesträngparametern för alla API-begäranden. Du kan ange namnet på frågesträngparametern.

Formatet på en URL-begäran för API-begäran när du använder Query sträng-baserad versions hantering är: `https://{yourDomain}/{apiName}/{operationId}?{queryStringParameterName}={versionIdentifier}` .

Till exempel, `https://apis.contoso.com/products?api-version=v1` och `https://apis.contoso.com/products/api-version=v2` kan referera till samma `products` API, men till versioner `v1` respektive `v2` .

## <a name="original-versions"></a>Ursprungliga versioner

Om du lägger till en version i ett icke-versions-API `Original` skapas en version automatiskt och svarar på standard-URL: en, utan att ha ett angivet versions-ID. `Original`Versionen ser till att alla befintliga anropare inte bryts genom processen för att lägga till en version. Om du skapar ett nytt API med aktiverade versioner i början `Original` skapas inte en version.

## <a name="how-versions-are-represented"></a>Hur versioner visas

Azure API Management hanterar en resurs som kallas *versions uppsättning*, som representerar en uppsättning versioner för ett enda logiskt API. En versions uppsättning innehåller visnings namnet för det versions bara API och [versions schema som används](#versioning-schemes) för att dirigera begär anden till angivna versioner.

Varje version av ett API underhålls som sin egen API-resurs, som sedan associeras med en versions uppsättning. En versions uppsättning kan innehålla API: er med olika åtgärder eller principer. Du kan göra betydande ändringar mellan versioner i en mängd.

Azure Portal skapar versions uppsättningar åt dig. Du kan ändra namnet och beskrivningen för en version som angetts i Azure Portal.

Du kan visa och hantera versions uppsättningar direkt med hjälp av [Azure CLI](/cli/azure/apim/api/versionset), [Azure PowerShell](/powershell/module/az.apimanagement/#api-management), [Resource Manager-mallar](/azure/templates/microsoft.apimanagement/service/apiversionsets)eller [Azure Resource Manager-API](/rest/api/apimanagement/2020-06-01-preview/apiversionset).

### <a name="migrating-a-non-versioned-api-to-a-versioned-api"></a>Migrera ett API som inte är versions hantering till ett API som har versions hantering

När du använder Azure Portal för att aktivera versions hantering för ett befintligt API görs följande ändringar i API Management resurser:

 * En ny versions uppsättning skapas.
 * Den befintliga versionen upprätthålls och [konfigureras som `Original` API-version](#original-versions). API: t är länkat till versions uppsättningen men kräver inte att någon versions-ID anges.
 * Den nya versionen skapas som ett nytt API och är länkad till versions uppsättningen. Detta nya API måste nås med versions schema och identifierare.

## <a name="versions-and-revisions"></a>Versioner och revideringar

Versioner och revideringar är distinkta funktioner. Varje version kan ha flera revisioner, precis som ett icke-versions-API. Du kan använda revideringar utan att använda versioner, eller tvärtom. Normalt används versioner för att separera API-versioner med större ändringar, medan revisioner kan användas för mindre och icke-brytande ändringar i ett API.

Om du upptäcker att din ändring har fått större ändringar, eller om du vill förvandla din revision till en beta-/test version, kan du skapa en version från en revidering. Använd Azure Portal och klicka på "Skapa version från revision" på snabb menyn för omarbetning på fliken revisioner.

## <a name="developer-portal"></a>Utvecklarportalen

[Developer-portalen](./api-management-howto-developer-portal.md) visar varje version av ett API separat.

![API Management Developer-portalen visar en lista över API: er som har versions hantering](media/api-management-versions/portal-list.png)

Information om ett API visar också en lista över alla versioner av detta API. En `Original` version visas utan ett versions-ID.

![API Management Developer-portalen visar en API-information och en lista över versioner för detta API](media/api-management-versions/portal-details.png)

> [!TIP]
> API-versioner måste läggas till i en produkt innan de visas på Developer-portalen.
