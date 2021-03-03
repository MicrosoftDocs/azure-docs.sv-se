---
title: Ansluta till REST-slutpunkter från Azure Logic Apps
description: Övervaka REST-slutpunkter i automatiserade uppgifter, processer och arbets flöden med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: 810aaae9634a7de8d07b6d49edd0c6c2eda96754
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730889"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Anropa REST-slutpunkter genom att använda Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och den inbyggda http + Swagger-anslutningen kan du automatisera arbets flöden som regelbundet anropar en REST-slutpunkt via en [Swagger-fil](https://swagger.io) genom att skapa Logi Kap par. HTTP + Swagger-utlösare och åtgärder fungerar på samma sätt som [http-utlösaren och åtgärden,](connectors-native-http.md) men ger en bättre upplevelse i Logic App Designer genom att exponera API-strukturen och utdata som beskrivs av Swagger-filen. Om du vill implementera en avsöknings utlösare följer du det avsöknings mönster som beskrivs i [skapa anpassade API: er som anropar andra API: er, tjänster och system från Logic Apps](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* URL: en för Swagger-filen (inte OpenAPI) som beskriver mål REST-slutpunkten

  Normalt måste REST-slutpunkten uppfylla det här villkoret för att kopplingen ska fungera:

  * Swagger-filen måste finnas på en HTTPS-URL som är offentligt tillgänglig.
  
  * Swagger-filen måste innehålla en `operationID` för varje åtgärd i definitionen. Om inte, visar kopplingen bara den sista åtgärden i Swagger-filen. 

  * Swagger-filen måste ha en aktive rad [resurs delning mellan ursprung (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) .

  Om du vill referera till en Swagger-fil som inte är värdbaserad eller som inte uppfyller kraven på säkerhet och kors ursprung kan du [överföra Swagger-filen till en BLOB-behållare i ett Azure Storage-konto](#host-swagger)och aktivera CORS på det lagrings kontot så att du kan referera till filen.

  I exemplen i det här avsnittet används [Cognitive Services ansikts-API](../cognitive-services/face/overview.md), vilket kräver ett [Cognitive Services konto och en åtkomst nyckel](../cognitive-services/cognitive-services-apis-create-account.md).

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Den Logic-app från vilken du vill anropa mål slut punkten. Börja med HTTP + Swagger-utlösaren genom att [skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda åtgärden HTTP + Swagger startar du din Logic-app med valfri utlösare som du vill använda. I det här exemplet används HTTP + Swagger-utlösaren som det första steget.

## <a name="add-an-http--swagger-trigger"></a>Lägg till en HTTP + Swagger-utlösare

Den här inbyggda utlösaren skickar en HTTP-begäran till en URL för en Swagger-fil som beskriver en REST API och returnerar ett svar som innehåller filens innehåll.

1. Logga in på [Azure-portalen](https://portal.azure.com). Öppna din tomma Logic-app i Logic App Designer.

1. Skriv "Swagger" som filter i rutan Sök i designern. Välj **http + Swagger-** utlösare i listan **utlösare** .

   ![Välj HTTP + Swagger-utlösare](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. I rutan **Swagger Endpoint URL** anger du URL: en för Swagger-filen och väljer **Nästa**.

   I det här exemplet används den Swagger-URL som finns i regionen USA, västra för [Cognitive Services ansikts-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Skärm bild som visar Logic app designer med utlösaren "H T T P + Swagger" och "Swagger Endpoint U R L" inställd på ett U R L-värde.](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. När designern visar de åtgärder som beskrivs av Swagger-filen väljer du den åtgärd som du vill använda.

   ![Skärm bild som visar Logic app designer med utlösaren "H T T P + Swagger" och en lista som visar Swagger-åtgärder.](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Ange värdena för utlösarens parametrar, som varierar beroende på den valda åtgärden, som du vill inkludera i slut punkts anropet. Konfigurera upprepningen för hur ofta du vill att utlösaren ska anropa slut punkten.

   I det här exemplet byter du ut utlösaren till "HTTP + Swagger-utlösare: Face-detect" så att steget har ett mer beskrivande namn.

   ![Skärm bild som visar Logic app designer med utlösaren "H T T P + Swagger" som visar åtgärden "ansikts identifiering".](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Om du vill lägga till andra tillgängliga parametrar öppnar du listan **Lägg till ny parameter** och väljer de parametrar som du vill använda.

   Mer information om autentiseringstyper som är tillgängliga för HTTP + Swagger finns i [lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Fortsätt att skapa ditt Logic Apps-arbetsflöde med åtgärder som körs när utlösaren utlöses.

1. Kom ihåg att spara din Logic app när du är klar. I verktygsfältet designer väljer du **Spara**.

## <a name="add-an-http--swagger-action"></a>Lägg till en HTTP + Swagger-åtgärd

Den här inbyggda åtgärden gör en HTTP-begäran till URL: en för Swagger-filen som beskriver en REST API och returnerar ett svar som innehåller filens innehåll.

1. Logga in på [Azure-portalen](https://portal.azure.com). Öppna din Logic app i Logic App Designer.

1. Under steget där du vill lägga till åtgärden HTTP + Swagger väljer du **nytt steg**.

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. Skriv "Swagger" som filter i rutan Sök i designern. I listan **åtgärder** väljer du åtgärden **http + Swagger** .

    ![Välj HTTP + Swagger-åtgärd](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. I rutan **Swagger Endpoint URL** anger du URL: en för Swagger-filen och väljer **Nästa**.

   I det här exemplet används den Swagger-URL som finns i regionen USA, västra för [Cognitive Services ansikts-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Ange URL för Swagger-slutpunkt](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. När designern visar de åtgärder som beskrivs av Swagger-filen väljer du den åtgärd som du vill använda.

   ![Åtgärder i Swagger-fil](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Ange värdena för åtgärds parametrarna, som varierar beroende på den valda åtgärden, som du vill inkludera i slut punkts anropet.

   Det här exemplet har inga parametrar, men byter namn på åtgärden till "HTTP + Swagger-åtgärd: Face-Identify", så att steget har ett mer beskrivande namn.

   ![Åtgärdsinformation](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Om du vill lägga till andra tillgängliga parametrar öppnar du listan **Lägg till ny parameter** och väljer de parametrar som du vill använda.

   Mer information om autentiseringstyper som är tillgängliga för HTTP + Swagger finns i [lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Kom ihåg att spara din Logic app när du är klar. I verktygsfältet designer väljer du **Spara**.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Värd Swagger i Azure Storage

Du kan referera till en Swagger-fil som inte finns eller som inte uppfyller kraven på säkerhet och kors ursprung genom att överföra filen till BLOB-behållare i ett Azure Storage-konto och aktivera CORS på det lagrings kontot. Följ dessa steg för att skapa, konfigurera och lagra Swagger-filer i Azure Storage:

1. [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md).

1. Aktivera nu CORS för blobben. På lagrings kontots meny väljer du **CORS**. På fliken **BLOB service** anger du dessa värden och väljer sedan **Spara**.

   | Egenskap | Värde |
   |----------|-------|
   | **Tillåtna ursprung** | `*` |
   | **Tillåtna metoder** | `GET`, `HEAD`, `PUT` |
   | **Tillåtna rubriker** | `*` |
   | **Exponerade rubriker** | `*` |
   | **Högsta ålder** (i sekunder) | `200` |
   |||

   Även om det här exemplet använder [Azure Portal](https://portal.azure.com), kan du använda ett verktyg som [Azure Storage Explorer](https://storageexplorer.com/)eller automatiskt konfigurera den här inställningen genom att använda det här [PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1)-exemplet.

1. [Skapa en BLOB-behållare](../storage/blobs/storage-quickstart-blobs-portal.md). I behållarens **översikts** fönster väljer du **ändra åtkomst nivå**. I listan **offentlig åtkomst nivå** väljer du **BLOB (endast anonym Läs åtkomst för blobbar)** och väljer **OK**.

1. [Ladda upp Swagger-filen till BLOB-behållaren](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob), antingen via [Azure Portal](https://portal.azure.com) eller [Azure Storage Explorer](https://storageexplorer.com/).

1. Om du vill referera till filen i BLOB-behållaren hämtar du HTTPS-URL: en som följer det här formatet, vilket är Skift läges känsligt, från Azure Storage Explorer:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<complete-swagger-file-name>?<query-parameters>`

## <a name="connector-reference"></a>Referens för anslutningsapp

Här är mer information om utdata från en HTTP + Swagger-utlösare eller åtgärd. HTTP + Swagger-anropet returnerar denna information:

| Egenskapsnamn | Typ | Beskrivning |
|---------------|------|-------------|
| sidhuvud | objekt | Huvudena från begäran |
| body | objekt | JSON-objekt | Objektet med bröd text innehållet från begäran |
| statuskod | int | Status koden från begäran |
|||

| Statuskod | Description |
|-------------|-------------|
| 200 | OK |
| 202 | Har godkänts |
| 400 | Felaktig begäran |
| 401 | Behörighet saknas |
| 403 | Förbjudet |
| 404 | Hittades inte |
| 500 | Internt serverfel. Ett okänt fel uppstod. |
|||

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
