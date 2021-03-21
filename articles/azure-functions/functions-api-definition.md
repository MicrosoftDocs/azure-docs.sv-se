---
title: OpenAPI metadata i Azure Functions
description: Översikt över OpenAPI-stöd i Azure Functions
ms.topic: conceptual
ms.date: 03/23/2017
ms.openlocfilehash: 499d4f685e3802fcc37c8a3050ae367207f192d2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000221"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Stöd för OpenAPI 2,0-metadata i Azure Functions (för hands version)
OpenAPI 2,0 (tidigare Swagger) stöd för metadata i Azure Functions är en förhands gransknings funktion som du kan använda för att skriva en OpenAPI 2,0-definition i en Function-app. Du kan sedan vara värd för filen med hjälp av Function-appen.

> [!IMPORTANT]
> Förhandsgranskningsfunktionen OpenAPI är i dag endast tillgänglig i 1.x-körningen. Information om hur du skapar en 1.x-funktionsapp [hittar du här](./functions-versions.md#creating-1x-apps).

Med [openapi metadata](https://swagger.io/) kan en funktion som är värd för en REST API användas av en mängd olika program varor. I den här program varan ingår Microsoft-erbjudanden som PowerApps och den [API Apps funktionen i Azure App Service](../app-service/overview.md), verktyg från tredje parts utvecklare som [Postman](https://www.getpostman.com/docs/importing_swagger)och [många fler paket](https://swagger.io/tools/).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Vi rekommenderar att du börjar med [kursen komma igång](./functions-openapi-definition.md) och sedan återgår till det här dokumentet för att lära dig mer om de olika funktionerna.

## <a name="enable-openapi-definition-support"></a><a name="enable"></a>Aktivera stöd för OpenAPI-definition
Du kan konfigurera alla OpenAPI-inställningar på sidan **API-definition** i funktions programmets **plattforms funktioner**.

> [!NOTE]
> Funktions-API definitions funktionen stöds inte för närvarande för beta körning.

Om du vill aktivera genereringen av en definition för värdbaserade OpenAPI och en snabb starts definition anger du **API-definition** till **funktion (för hands version)**. **Extern URL** gör att din funktion kan använda en openapi-definition som är värdbaserad någon annan stans.

## <a name="generate-a-swagger-skeleton-from-your-functions-metadata"></a><a name="generate-definition"></a>Generera en Swagger-Skeleton från din funktions metadata
En mall kan hjälpa dig att börja skriva din första OpenAPI-definition. Med funktionen definitions mal len skapar du en OpenAPI-definition med hjälp av alla metadata i function.jspå filen för var och en av dina HTTP-utlösare. Du måste fylla i mer information om ditt API från [openapi-specifikationen](https://swagger.io/specification/), till exempel mallar för begäran och svar.

Stegvisa instruktioner finns i [komma igång-kursen](./functions-openapi-definition.md).

### <a name="available-templates"></a><a name="templates"></a>Tillgängliga mallar

|Name| Beskrivning |
|:-----|:-----|
|Definition som skapats|En OpenAPI-definition med den maximala mängd information som kan härledas från funktionens befintliga metadata.|

### <a name="included-metadata-in-the-generated-definition"></a><a name="quickstart-details"></a>Inkluderade metadata i den genererade definitionen

I följande tabell visas de Azure Portal inställningar och motsvarande data i function.jssom mappas till den genererade Swagger-Skeleton.

|Swagger.jspå|Portal gränssnitt|Function.jspå|
|:----|:-----|:-----|
|[Värd](https://swagger.io/specification/#fixed-fields-15)|**Funktions program inställningar**  >  **App Service inställningar**  >  **Översikt**  >  **URL**|*Finns inte*
|[Sökvägar](https://swagger.io/specification/#paths-object-29)|**Integrera**  >  **Valda HTTP-metoder**|Bindningar: väg
|[Sök vägs objekt](https://swagger.io/specification/#path-item-object-32)|**Integrera**  >  **Route-mall**|Bindningar: metoder
|[Säkerhet](https://swagger.io/specification/#security-scheme-object-112)|**Nycklar**|*Finns inte*|
|OperationID|**Route + tillåtna verb**|Route + tillåtna verb|

\*Åtgärds-ID krävs endast för integrering med PowerApps och Flow.
> [!NOTE]
> Tillägget x-MS-Summary ger ett visnings namn i Logic Apps, PowerApps och Flow.
>
> Mer information finns i [Anpassa din Swagger-definition för PowerApps](/connectors/custom-connectors/openapi-extensions).

## <a name="use-cicd-to-set-an-api-definition"></a><a name="CICD"></a>Använd CI/CD för att ange en API-definition

 Du måste aktivera API-definitionens värd i portalen innan du aktiverar käll kontroll för att ändra din API-definition från käll kontroll. Följ dessa anvisningar:

1. Bläddra till **API-definition (för hands version)** i inställningar för Function-appen.
   1. Ange **källa för API-definition** till **funktion**.
   1. Klicka på **skapa definitions definitions mall** och **Spara** för att skapa en mall definition för ändring senare.
   1. Notera API-definitionens URL och nyckel.
1. [Konfigurera kontinuerlig integrering/kontinuerlig distribution (CI/CD)](./functions-continuous-deployment.md#requirements-for-continuous-deployment).
2. Ändra swagger.jspå i käll kontroll på \site\wwwroot \.azurefunctions\swagger\swagger.jspå.

Nu finns ändringar i swagger.jspå lagrings platsen av din funktions app i URL: en för API-definitionen och nyckeln som du noterade i steg 1. c.

## <a name="next-steps"></a>Nästa steg
* [Själv studie kurs om att komma igång](./functions-openapi-definition.md). Prova vår genom gång för att se en OpenAPI-definition i praktiken.
* [Azure Functions GitHub-lagringsplatsen](https://github.com/Azure/Azure-Functions/). Kolla in Functions-lagringsplatsen för att ge oss feedback om stöd för för hands versionen av API-definition. Gör ett GitHub-ärende för allt du vill se uppdaterat.
* [Referens för Azure Functions-utvecklare](functions-reference.md). Lär dig mer om att koda funktioner och definiera utlösare och bindningar.
