---
title: Server delar för Azure API Management | Microsoft Docs
description: Lär dig mer om anpassade Server delar i API Management
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.openlocfilehash: 54a46e999391507f5ec7d927f62b88fcd2169b75
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99500622"
---
# <a name="backends-in-api-management"></a>Server delar i API Management

En *Server* del (eller *API-backend*) i API Management är en http-tjänst som implementerar frontend-API: et och dess åtgärder.

När du importerar vissa API: er konfigurerar API Management API-Server delen automatiskt. API Management konfigurerar till exempel Server delen när du importerar en [openapi-specifikation](import-api-from-oas.md), [SOAP-API](import-soap-api.md)eller Azure-resurser som en http-utlöst Azure- [Funktionsapp](import-function-app-as-api.md) eller [Logic-app](import-logic-app-as-api.md).

API Management också stöd för användning av andra Azure-resurser, till exempel ett [Service Fabric kluster](how-to-configure-service-fabric-backend.md) eller en anpassad tjänst som en API-Server del. Att använda dessa anpassade Server delar kräver extra konfiguration, till exempel för att auktorisera autentiseringsuppgifter för begär anden till backend-tjänsten och för att definiera API-åtgärder. Du konfigurerar och hanterar dessa Server delar i Azure Portal eller använder Azure-API: er eller verktyg.

När du har skapat en server del kan du referera till backend-URL: en i dina API: er. Använd [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) principen för att omdirigera en inkommande API-begäran till den anpassade Server delen i stället för standard server delen för detta API.

## <a name="benefits-of-backends"></a>Fördelar med Server delar

En anpassad server del har flera fördelar, inklusive:

* Sammanfattar information om backend-tjänsten, befordra åter användning över API: er och förbättrad styrning  
* Används enkelt genom att konfigurera en omvandlings princip på ett befintligt API
* Drar nytta av API Management-funktioner för att bevara hemligheter i Azure Key Vault om [namngivna värden](api-management-howto-properties.md) har kon figurer ATS för sidhuvud-eller frågeparameter-autentisering

## <a name="next-steps"></a>Nästa steg

* Konfigurera en [Service Fabric Server](how-to-configure-service-fabric-backend.md) del med hjälp av Azure Portal.
* Server delar kan också konfigureras med hjälp av API Management [REST API](/rest/api/apimanagement), [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)eller [Azure Resource Manager mallar](../service-fabric/service-fabric-tutorial-deploy-api-management.md).

