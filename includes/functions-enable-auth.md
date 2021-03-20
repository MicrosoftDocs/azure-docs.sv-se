---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/21/2020
ms.author: glenga
ms.openlocfilehash: 380878fedaa2f7ea6160c3c4801c8dece6e1a9ff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "83648944"
---
#### <a name="enable-app-service-authenticationauthorization"></a>Aktivera App Service autentisering/auktorisering

App Services plattformen gör att du kan använda Azure Active Directory (AAD) och flera identitets leverantörer från tredje part för att autentisera klienter. Du kan använda den här strategin för att implementera anpassade auktoriseringsregler för dina funktioner och du kan arbeta med användar information från funktions koden. Mer information finns i [autentisering och auktorisering i Azure App Service](../articles/app-service/overview-authentication-authorization.md) och [arbeta med klient identiteter](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities).

#### <a name="use-azure-api-management-apim-to-authenticate-requests"></a>Använda Azure API Management (APIM) för att autentisera begär Anden

APIM tillhandahåller flera olika API-säkerhetsalternativ för inkommande begär Anden. Läs mer i [API Management autentiseringsprinciper](../articles/api-management/api-management-authentication-policies.md). Med APIM på plats kan du konfigurera Function-appen så att den endast accepterar begär Anden från IP-adressen för din APIM-instans. Mer information finns i [begränsningar för IP-adresser](../articles/azure-functions/ip-addresses.md#ip-address-restrictions).
