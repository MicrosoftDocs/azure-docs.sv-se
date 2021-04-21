---
title: Azure API Management-| Microsoft Docs
description: Läs mer om anpassade API Management
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a0ef3a2c1f2f1fc5cdf00737d1984f6cb13c40d0
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813028"
---
# <a name="backends-in-api-management"></a>Backends in API Management

En *backend* (eller *API-API Management)* är en HTTP-tjänst som implementerar ditt frontend-API och dess åtgärder.

När du importerar vissa API:API Management konfigurerar API-servern automatiskt. Exempelvis konfigurerar API Management när du importerar en [OpenAPI-specifikation,](import-api-from-oas.md) [SOAP API](import-soap-api.md)eller Azure-resurser, till exempel en HTTP-utlöst [Azure-funktionsapp](import-function-app-as-api.md) eller [logikapp](import-logic-app-as-api.md).

API Management också stöd för användning av andra [Azure-resurser, till exempel ett Service Fabric kluster](how-to-configure-service-fabric-backend.md) eller en anpassad tjänst som en API-server. Användning av dessa anpassade servertjänster kräver extra konfiguration, till exempel för att auktorisera autentiseringsuppgifter för begäranden till servertjänst och för att definiera API-åtgärder. Du konfigurerar och hanterar dessa backends i Azure Portal eller med hjälp av Azure-API:er eller verktyg.

När du har skapat en backend kan du referera till backend-URL:en i dina API:er. Använd principen för att omdirigera en inkommande API-begäran till den anpassade serverplatsen i [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) stället för standard backend för det API:et.

## <a name="benefits-of-backends"></a>Fördelar med backends

En anpassad backend har flera fördelar, bland annat:

* Abstraherar information om backend-tjänsten, främjar återanvändningsbarhet mellan API:er och förbättrad styrning  
* Enkelt att använda genom att konfigurera en transformeringsprincip på ett befintligt API
* Drar nytta av API Management för att underhålla hemligheter i Azure Key Vault namngivna [värden](api-management-howto-properties.md) har konfigurerats för autentisering med sidhuvud eller frågeparameter

## <a name="next-steps"></a>Nästa steg

* Konfigurera en [Service Fabric med hjälp](how-to-configure-service-fabric-backend.md) av Azure Portal.
* Backends kan också konfigureras med hjälp API Management [REST API](/rest/api/apimanagement), [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)eller [Azure Resource Manager mallar](../service-fabric/service-fabric-tutorial-deploy-api-management.md).

