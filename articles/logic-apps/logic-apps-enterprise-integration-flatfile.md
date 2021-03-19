---
title: Koda eller avkoda flata filer
description: Koda eller avkoda flata filer för företags integrering i Azure Logic Apps med hjälp av Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 05/09/2020
ms.openlocfilehash: aebce8f284ed4bb21d99efffc8dd6d0c51b39533
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87001493"
---
# <a name="encode-and-decode-flat-files-in-azure-logic-apps-by-using-the-enterprise-integration-pack"></a>Koda och avkoda flata filer i Azure Logic Apps med Enterprise-integrationspaketet

Innan du skickar XML-innehåll till en affärs partner i ett affärs-till-företag-scenario (B2B), kanske du vill koda innehållet först. Genom att skapa en Logic-app kan du koda och avkoda flata filer med hjälp av den **flata fil** anslutningen. Din Logi Kap par kan hämta det här XML-innehållet från olika källor, till exempel utlösare för begäran, en annan app eller andra [anslutningar som stöds av Azure Logic Apps](../connectors/apis-list.md). Mer information finns i [Vad är Azure Logic Apps](logic-apps-overview.md)?

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Den Logic app där du vill använda den **flata fil** anslutningen och en utlösare som startar din Logic app-arbetsflöde. Den **flata fil** kopplingen tillhandahåller endast åtgärder, inte utlösare. Du kan antingen använda utlösaren eller en annan åtgärd för att mata in XML-innehållet i din Logic app för kodning eller avkodning. Om du inte har använt Logic Apps igen går du igenom [snabb start: skapa din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Ett [integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) som är associerat med din Azure-prenumeration och [länkat till Logic-appen](./logic-apps-enterprise-integration-create-integration-account.md#link-account) där du planerar att använda den **flata fil** anslutningen. Både din Logic app och ditt integrations konto måste finnas på samma plats eller i Azure-regionen.

* Ett flat- [filschema](logic-apps-enterprise-integration-schemas.md) som du har laddat upp till ditt integrations konto för kodning eller avkodning av XML-innehållet

* Minst två [handels partner](logic-apps-enterprise-integration-partners.md) som du redan har definierat i ditt integrations konto

## <a name="add-flat-file-encode-action"></a>Lägg till kodningsåtgärd för flat fil

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic Apps designer.

1. Under utlösare eller åtgärd i din Logic app väljer du **nytt steg**  >  **Lägg till en åtgärd**. I det här exemplet används utlösaren för förfrågningar, som namnges **när en HTTP-begäran tas emot** och hanterar inkommande förfrågningar från utanför Logic-appen.

   > [!TIP]
   > Det är valfritt att ange ett JSON-schema. Om du har ett exempel på en nytto Last från den inkommande begäran väljer du **Använd exempel nytto last för att skapa schema**, anger exempel nytto last och väljer sedan **slutfört**. Schemat visas i rutan **JSON-schema för begär ande text** .

1. Under **Välj en åtgärd** anger du `flat file` . I listan åtgärder väljer du den här åtgärden: **flat fil kodning**

   ![Välj åtgärden "flat fil encoding"](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-encoding.png)

1. Klicka i **innehålls** rutan så att listan med dynamiskt innehåll visas. I listan i avsnittet **när en HTTP-begäran tas emot** väljer du egenskapen **brödtext** som innehåller den begär ande texten från utlösaren och innehållet som ska kodas.

   ![Välj innehåll som ska kodas från listan med dynamiskt innehåll](./media/logic-apps-enterprise-integration-flatfile/select-content-to-encode.png)

   > [!TIP]
   > Om du inte ser egenskapen **brödtext** i listan med dynamiskt innehåll, väljer du **Läs mer** bredvid avsnittet **när en http-begäran tas emot** .
   > Du kan också ange det innehåll som ska avkodas direkt i **innehålls** rutan.

1. I listan **schema namn** väljer du det schema som är i det länkade integrations kontot som ska användas för kodning, till exempel:

   ![Välj schema som ska användas för kodning](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-encoding.png)

   > [!NOTE]
   > Om inget schema visas i listan innehåller ditt integrations konto inte några schemafiler att använda för kodning. Ladda upp det schema som du vill använda för ditt integrations konto.

1. Spara logikappen. Testa din anslutning genom att göra en begäran till HTTPS-slutpunkten, som visas i den begär ande egenskapen **http post URL** och inkludera det XML-innehåll som du vill koda i begär ande texten.

Nu är du klar med att ställa in din Flat File encoding-åtgärd. I en verklig värld kanske du vill lagra kodade data i en affärsrelaterad app (LOB), till exempel Salesforce. Du kan också skicka kodade data till en handels partner. Om du vill skicka utdata från encoding-åtgärden till Salesforce eller till din handels partner använder du de andra [anslutningarna som är tillgängliga i Azure Logic Apps](../connectors/apis-list.md).

## <a name="add-flat-file-decode-action"></a>Lägg till avkodningsåtgärd för flat fil

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic Apps designer.

1. Under utlösare eller åtgärd i din Logic app väljer du **nytt steg**  >  **Lägg till en åtgärd**. I det här exemplet används utlösaren för förfrågningar, som namnges **när en HTTP-begäran tas emot** och hanterar inkommande förfrågningar från utanför Logic-appen.

   > [!TIP]
   > Det är valfritt att ange ett JSON-schema. Om du har ett exempel på en nytto Last från den inkommande begäran väljer du **Använd exempel nytto last för att skapa schema**, anger exempel nytto last och väljer sedan **slutfört**. Schemat visas i rutan **JSON-schema för begär ande text** .

1. Under **Välj en åtgärd** anger du `flat file` . I listan åtgärder väljer du den här åtgärden: **flat fil avkodning**

   ![Välj åtgärden "flat fil avkodning"](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-decoding.png)

1. Klicka i **innehålls** rutan så att listan med dynamiskt innehåll visas. I listan i avsnittet **när en HTTP-begäran tas emot** väljer du egenskapen **brödtext** som innehåller den begär ande texten från utlösaren och innehållet som ska avkodas.

   ![Välj innehåll att avkoda från listan med dynamiskt innehåll](./media/logic-apps-enterprise-integration-flatfile/select-content-to-decode.png)

   > [!TIP]
   > Om du inte ser egenskapen **brödtext** i listan med dynamiskt innehåll, väljer du **Läs mer** bredvid avsnittet **när en http-begäran tas emot** . Du kan också ange det innehåll som ska avkodas direkt i **innehålls** rutan.

1. I listan **schema namn** väljer du det schema som är i det länkade integrations kontot som ska användas för avkodning, till exempel:

   ![Välj schema som ska användas för avkodning](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-decoding.png)

   > [!NOTE]
   > Om inget schema visas i listan innehåller ditt integrations konto inte några schemafiler att använda för avkodning. Ladda upp det schema som du vill använda för ditt integrations konto.

1. Spara logikappen. Testa din anslutning genom att göra en begäran till HTTPS-slutpunkten, som visas i den begär ande egenskapen **http post URL** och inkludera det XML-innehåll som du vill avkoda i begär ande texten.

Du är nu klar med att ställa in din flata fil avkodnings åtgärd. I en verklig värld kan du vilja lagra de avkodade data som finns i en affärsrelaterad app (LOB), till exempel Salesforce. Du kan också skicka de avkodade data till en handels partner. Om du vill skicka utdata från avkodnings åtgärden till Salesforce eller till din handels partner använder du andra [anslutningar som är tillgängliga i Azure Logic Apps](../connectors/apis-list.md).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Enterprise-integrationspaket](logic-apps-enterprise-integration-overview.md)
