---
title: Konfigurera resurs delning mellan ursprung i Azure API för FHIR
description: Den här artikeln beskriver hur du konfigurerar resurs delning mellan ursprung i Azure API för FHIR.
author: matjazl
ms.author: matjazl
ms.date: 3/11/2019
ms.topic: reference
ms.service: healthcare-apis
ms.subservice: fhir
ms.openlocfilehash: dc4c034b0821f1fe5ecb940591fca77d61edc3ce
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019891"
---
# <a name="configure-cross-origin-resource-sharing-in-azure-api-for-fhir"></a>Konfigurera resurs delning mellan ursprung i Azure API för FHIR

Azure API för snabba hälso-och sjukvårds resurser (FHIR) stöder [resurs delning mellan ursprung (CORS)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing). Med CORS kan du konfigurera inställningar så att program från en domän (ursprung) kan komma åt resurser från en annan domän, som kallas för en begäran mellan domäner.

CORS används ofta i en app med en enda sida som måste anropa en RESTful-API till en annan domän.

Om du vill konfigurera en CORS-inställning i Azure-API: et för FHIR anger du följande inställningar:

- **Ursprung (åtkomst-kontroll-Tillåt-ursprung)**. En lista över domäner som får göra cross-origin-begäranden till Azure API för FHIR. Varje domän (ursprung) måste anges på en separat rad. Du kan ange en asterisk (*) för att tillåta anrop från vilken domän som helst, men vi rekommenderar inte det eftersom det är en säkerhets risk.

- **Rubriker (Access-Control-Allow-headers)**. En lista med rubriker som ursprungs förfrågan kommer att innehålla. Ange en asterisk (*) om du vill tillåta alla rubriker.

- **Metoder (åtkomst-kontroll-Allow-Methods)**. De tillåtna metoderna (placera, Hämta, skicka och så vidare) i ett API-anrop. Välj **Välj alla** för alla metoder.

- **Högsta ålder (åtkomst kontroll-max ålder)**. Värdet i sekunder för cachelagring av preflight-begäranden för Access-Control-Allow-header och Access-Control-Allow-Methods.

- **Tillåt autentiseringsuppgifter (åtkomst-Control-Allow-credentials)**. CORS-begäranden inkluderar vanligt vis inte cookies för att förhindra [CSRF-attacker (Cross-Site request förfalskning)](https://en.wikipedia.org/wiki/Cross-site_request_forgery) . Om du väljer den här inställningen kan förfrågan göras för att inkludera autentiseringsuppgifter, till exempel cookies. Du kan inte konfigurera den här inställningen om du redan har angett ursprung med en asterisk (*).

![Inställningar för resurs delning mellan ursprung (CORS)](media/cors/cors.png)

>[!NOTE]
>Du kan inte ange olika inställningar för olika domän ursprung. Alla inställningar (**rubriker**, **metoder**, **högsta ålder** och **Tillåt autentiseringsuppgifter**) gäller för alla ursprung som anges i inställningen ursprung.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du konfigurerar delning av kors ursprung i Azure API för FHIR. Distribuera sedan ett fullständigt hanterat Azure API för FHIR:
 
>[!div class="nextstepaction"]
>[Distribuera Azure API för FHIR](fhir-paas-portal-quickstart.md)