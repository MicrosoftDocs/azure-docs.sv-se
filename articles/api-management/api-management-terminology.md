---
title: Terminologi för Azure API Management | Microsoft Docs
description: Den här artikeln innehåller definitioner av de termer som är speciella för API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/11/2017
ms.author: apimpm
ms.openlocfilehash: 002ae9f99865114dd8bf52b53efc9303a0706a82
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99491832"
---
# <a name="azure-api-management-terminology"></a>Azure API Management terminologi

Den här artikeln innehåller definitioner av de termer som är speciella för API Management (APIM).

## <a name="term-definitions"></a>Term definitioner

* **Server dels-API** – en http-tjänst som implementerar ditt API och dess åtgärder. Mer information [finns i Server](backends.md)delar.
* **Frontend-API** / **APIM-API** – ett APIM-API som inte är värd för API: er skapar façades för dina API: er. Du kan anpassa fasad efter dina behov utan att behöva röra Server dels-API: et. Mer information finns i [Importera och publicera ett API](import-and-publish.md).
* **APIM-produkt** – en produkt innehåller en eller flera API: er samt användnings kvot och användnings villkor. Du kan inkludera ett antal API: er och erbjuda dem till utvecklare via Developer-portalen. Mer information finns i [skapa och publicera en produkt](api-management-howto-add-products.md).
* **APIM API-åtgärd** – varje APIM-API representerar en uppsättning åtgärder som är tillgängliga för utvecklare. Varje APIM-API innehåller en referens till backend-tjänsten som implementerar API: et och dess åtgärder mappar till de åtgärder som implementeras av backend-tjänsten. Mer information finns i avsnittet om [modeller av API-svar](mock-api-responses.md).
* **Version** – ibland vill du publicera nya eller andra API-funktioner för vissa användare, medan andra vill använda det API som för närvarande fungerar för dem. Mer information finns i [publicera flera versioner av ditt API](api-management-get-started-publish-versions.md).
* **Revision** – när ditt API är klart att användas och börjar användas av utvecklare, behöver du vanligt vis inte tänka på att göra ändringar i detta API och samtidigt inte störa anropen av ditt API. Det är också bra att informera utvecklarna om de ändringar du gjort. Mer information finns i [använda revisioner](api-management-get-started-revise-api.md).
* **Developer-portalen** – dina kunder (utvecklare) bör använda Developer-portalen för att få åtkomst till dina API: er. Developer-portalen kan anpassas. Mer information finns i [Anpassa Developer-portalen](api-management-customize-styles.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en instans](get-started-create-service-instance.md)

