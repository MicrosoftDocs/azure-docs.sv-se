---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 04/16/2021
ms.author: vlvinogr
ms.openlocfilehash: 329ea156b296810395eb7b8e8310bed5ee0ee4c9
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107601916"
---
## <a name="append-other-apis"></a>Lägg till andra API:er

Du kan skapa ett API med API:er som exponeras av olika tjänster, inklusive:
* OpenAPI-specifikationen
* ETT SOAP-API
* Funktionen API Apps i Azure App Service
* Azure-funktionsapp
* Azure Logic Apps
* Azure Service Fabric

Lägg till ett annat API i ditt befintliga API med hjälp av följande steg. 

>[!NOTE] 
> När du importerar ett annat API läggs åtgärderna i ditt aktuella API.

1. Öppna Azure API Management-instansen i Azure-portalen.

    :::image type="content" source="./media/api-management-append-apis/service-page.png" alt-text="Gå till Azure API Mgmt-instans":::

1. Välj **API:er** i menyn till vänster.

    :::image type="content" source="./media/api-management-append-apis/api-select.png" alt-text="Välj API:er":::

1. Klicka på **...** intill det API som du vill lägga till ett annat API till.
1. Välj **Import** från den nedrullningsbara menyn.

    :::image type="content" source="./media/api-management-append-apis/append-01.png" alt-text="Välj importera":::

1. Välj en tjänst som ett API ska importeras från.

    :::image type="content" source="./media/api-management-append-apis/select-to-import.png" alt-text="Välj tjänst":::