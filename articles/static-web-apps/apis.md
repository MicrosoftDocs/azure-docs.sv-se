---
title: API-stöd i Azure Static Web Apps med Azure Functions
description: Lär dig vilka API-funktioner Azure Static Web Apps stöder
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 1fc5e1e6982686e7042e5b8ad55d72a4560b6aee
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107737486"
---
# <a name="api-support-in-azure-static-web-apps-preview-with-azure-functions"></a>API-stöd i Azure Static Web Apps förhandsversion med Azure Functions

Azure Static Web Apps tillhandahåller serverlösa API-slutpunkter via [Azure Functions](../azure-functions/functions-overview.md). Genom att Azure Functions kan API:er skalas dynamiskt baserat på efterfrågan och innehåller följande funktioner:

- **Integrerad säkerhet** med direkt åtkomst till användarautentisering [och rollbaserade auktoriseringsdata.](user-information.md)
- **Sömlös routning** som gör _API-vägen_ tillgänglig för webbappen på ett säkert sätt utan att kräva anpassade CORS-regler.
- **HTTP-utlösare** och indata-/utdatabindningar.

## <a name="configuration"></a>Konfiguration

API-slutpunkter är tillgängliga för webbappen via _API-vägen._ När den här vägen är fast har du kontroll över mappen och projektet där du hittar den associerade Azure Functions appen. Du kan ändra den här platsen genom [att redigera YAML-arbetsflödesfilen](github-actions-workflow.md#build-and-deploy) som finns i lagringsplatsens _mapp .github/workflows._

## <a name="constraints"></a>Villkor

Azure Static Web Apps tillhandahåller ett API via Azure Functions. Funktionerna i Azure Functions fokuserar på en specifik uppsättning funktioner som gör att du kan skapa ett API för en webbapp och tillåta att webbappen ansluter till API på ett säkert sätt. Dessa funktioner har vissa begränsningar, bland annat:

- API-vägprefixet måste vara _api_.
- API:et måste antingen vara Node.js 12, .NET Core 3.1 eller Python 3.8 Azure Functions appen.
- Vägregler för API-funktioner stöder endast [omdirigeringar](routes.md#redirects) [och skydd av vägar med roller](routes.md#securing-routes-with-roles).
- Utlösare är begränsade till [HTTP](../azure-functions/functions-bindings-http-webhook.md).
  - Indata- och [utdatabindningar](../azure-functions/functions-triggers-bindings.md#supported-bindings) stöds.
- Loggar är bara tillgängliga om du lägger [till Application Insights](../azure-functions/functions-monitoring.md) i Functions-appen.
- Vissa programinställningar hanteras av tjänsten. Du kan inte konfigurera appinställningar som börjar med följande prefix: `APPSETTING_` , , , , , , , , `AZUREBLOBSTORAGE_` , , `AZUREFILESSTORAGE_` , , , `AZURE_FUNCTION_` , , `CONTAINER_` , , `DIAGNOSTICS_` , , `DOCKER_` `FUNCTIONS_` , `IDENTITY_` `MACHINEKEY_` `MAINSITE_` `MSDEPLOY_` `SCMSITE_` `SCM_` `WEBSITES_` `WEBSITE_` `WEBSOCKET_` `AzureWeb` .

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägga till ett API](add-api.md)
