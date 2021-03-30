---
title: 'Anropa anpassade webb-API: er & REST API: er'
description: 'Anropa dina egna webb-API: er & REST API: er från Azure Logic Apps'
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 05/13/2020
ms.openlocfilehash: 7b4d00e8c0366d10fddafa66db699c1a59fd9ad7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "83659785"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>Distribuera och anropa anpassade API:er från arbetsflöden i Azure Logic Apps

När du har [skapat dina egna API: er](./logic-apps-create-api-app.md) som ska användas i dina Logic app-arbetsflöden måste du distribuera dessa API: er innan du kan anropa dem. Du kan distribuera dina API: er som [webbappar](../app-service/overview.md), men du bör överväga att distribuera dina API: er som [API Apps](../app-service/app-service-web-tutorial-rest-api.md), vilket gör jobbet enklare när du skapar, är värd för och använder API: er i molnet och lokalt. Du behöver inte ändra någon kod i dina API: er – distribuera bara din kod till en API-app. Du kan vara värd för dina API: er på [Azure App Service](../app-service/overview.md), ett PaaS-erbjudande (Platform-as-a-Service) som ger mycket skalbara, enkla API-värdar.

Även om du kan anropa API: er från en Logi Kap par, lägger du till [Swagger metadata](https://swagger.io/specification/) som beskriver ditt API: s åtgärder och parametrar. Det här Swagger-dokumentet hjälper ditt API att integrera enklare och arbeta bättre med Logic Apps.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Distribuera ditt API som en webbapp eller API-app

Innan du kan anropa ditt anpassade API från en Logic app kan du distribuera ditt API som en webbapp eller en API-app för att Azure App Service. För att göra ditt Swagger-dokument läsbart för Logic Apps designer ställer du in egenskaper för API-definitioner och aktiverar [resurs delning mellan ursprung (CORS)](../app-service/overview.md) för din WEBBAPP eller API-app.

1. I [Azure Portal](https://portal.azure.com)väljer du webbappen eller API-appen.

2. I menyn app som öppnas under **API** väljer du API- **definition**. Ange **platsen för API-definition** till URL: en för din swagger.jsi filen.

   Normalt visas URL: en i följande format: `https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Länk till Swagger-dokument för ditt anpassade API](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. Under **API** väljer du **CORS**. Ange CORS-principen för **tillåtna ursprung** till **"*"** (Tillåt alla).

   Den här inställningen tillåter förfrågningar från Logic App Designer.

   ![Tillåt förfrågningar från Logic App Designer till ditt anpassade API](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Mer information finns i [hantera en RESTful-API med CORS i Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Anropa ditt anpassade API från Logic app-arbetsflöden

När du har ställt in egenskaper och CORS för API-definitioner, ska dina anpassade API: er och åtgärder vara tillgängliga så att du kan ta med i ditt Logic app-arbetsflöde. 

*  Om du vill visa webbplatser som har OpenAPI-URL: er kan du bläddra bland dina prenumerations webbplatser i Logic Apps designer.

*  Om du vill visa tillgängliga åtgärder och indata genom att peka på ett Swagger-dokument använder du [åtgärden http + Swagger](../connectors/connectors-native-http-swagger.md).

*  Om du vill anropa API: er, inklusive API: er som inte har eller visar ett Swagger-dokument, kan du alltid skapa en begäran med [http-åtgärden](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>Nästa steg

* [Översikt över anpassad anslutningsapp](../logic-apps/custom-connector-overview.md)
