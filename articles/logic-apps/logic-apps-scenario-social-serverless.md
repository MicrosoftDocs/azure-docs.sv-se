---
title: Skapa en instrument panel för kund insikter
description: Hantera kundfeedback, data från sociala medier med mera genom att skapa en kund instrument panel med Azure Logic Apps och Azure Functions
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: 22e0c7304f7a53a86bc5c6739a2061352d738d29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784820"
---
# <a name="create-a-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Skapa en strömmande kund insikts instrument panel med Azure Logic Apps och Azure Functions

Azure erbjuder [Server](https://azure.microsoft.com/solutions/serverless/) fria verktyg som hjälper dig att snabbt bygga och vara värd för appar i molnet utan att behöva tänka på infrastrukturen. I den här självstudien kan du skapa en instrument panel som utlöser feedback från kunder, analyserar feedback med Machine Learning och publicerar insikter till en källa, till exempel Power BI eller Azure Data Lake.

I den här lösningen använder du dessa viktiga Azure-komponenter för appar utan server: [Azure Functions](https://azure.microsoft.com/services/functions/) och [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).
Azure Logic Apps tillhandahåller en server lös arbets flödes motor i molnet så att du kan skapa dirigeringar för komponenter utan server och ansluta till 200 + tjänster och API: er. Azure Functions tillhandahåller data behandling utan server i molnet. Den här lösningen använder Azure Functions för att flagga kund tweets baserat på fördefinierade nyckelord.

I det här scenariot skapar du en logisk app som utlöser för att hitta feedback från kunder. Vissa anslutningar som hjälper dig att svara på kundfeedback är Outlook.com, Office 365, Survey apa, Twitter och en [http-begäran från ett webb formulär](/archive/blogs/logicapps/calling-a-logic-app-from-an-html-form). Arbets flödet som du skapar övervakar en hashtagg på Twitter.

Du kan [bygga hela lösningen i Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) och [distribuera lösningen med Azure Resource Manager mall](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md). För en video genom gång som visar hur du skapar den här lösningen [tittar du på den här kanalen 9-videon](https://aka.ms/logicappsdemo). 

## <a name="trigger-on-customer-data"></a>Utlösare för kund information

1. Skapa en tom Logic-app i Azure Portal eller Visual Studio. 

   Om du inte har använt Logic Apps igen kan du läsa [snabb starten för Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md) eller [snabb starten för Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

2. I Logic app designer söker du efter och lägger till Twitter-utlösaren som har den här åtgärden: **när en ny tweet publiceras**

3. Konfigurera utlösaren så att den lyssnar efter tweets baserat på ett nyckelord eller en hashtagg.

   Vid avsöknings-baserade utlösare, till exempel Twitter-utlösaren, fastställer upprepnings egenskapen hur ofta Logic app söker efter nya objekt.

   ![Exempel på Twitter-utlösare][1]

Den här Logic-appen aktive ras nu på alla nya tweets. Du kan sedan ta och analysera tweet-data så att du bättre kan förstå sentiment som uttryckts. 

## <a name="analyze-tweet-text"></a>Analysera Tweet-text

Du kan använda [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)för att identifiera sentiment bakom viss text.

1. I Logic App Designer går du till utlösaren och väljer **nytt steg**.

2. Hitta **textanalys** -anslutningen.

3. Välj åtgärden **identifiera sentiment** .

4. Om du uppmanas att ange en giltig Cognitive Services nyckel för tjänsten Textanalys.

5. Under **brödtext i begäran** väljer du textfältet **tweet** , som tillhandahåller tweet-texten som inmatade för analys.

När du har skaffat tweet-data och insikter om tweeten kan du nu använda flera andra relevanta anslutningar och deras åtgärder:

* **Power BI – Lägg till rader i strömmande data uppsättning**: Visa inkommande tweets på en Power BI instrument panel.
* **Azure Data Lake-tilläggs fil**: Lägg till kund information till en Azure Data Lake data uppsättning som ska ingå i analys jobb.
* **SQL – Lägg till rader**: lagra data i en databas för senare hämtning.
* **Slack – skicka meddelande**: meddela en slack-kanal om negativ feedback som kan kräva åtgärder.

Du kan också skapa och en Azure-funktion så att du kan utföra anpassad bearbetning av dina data. 

## <a name="process-data-with-azure-functions"></a>Bearbeta data med Azure Functions

Innan du skapar en funktion skapar du en Function-app i din Azure-prenumeration. För att din Logi Kap par ska anropa en funktion måste funktionen dessutom ha en HTTP trigger-bindning, till exempel, använda **HttpTrigger** -mallen. Lär dig [hur du skapar din första Function-app och fungerar i Azure Portal](../azure-functions/functions-get-started.md).

I det här scenariot använder du Tweet-texten som begär ande text för din Azure-funktion. I funktions koden definierar du den logik som avgör om tweet-texten innehåller ett nyckelord eller en fras. Håll funktionen så enkel eller komplex som krävs för scenariot.
I slutet av funktionen returnerar du ett svar till Logic-appen med vissa data, till exempel ett enkelt booleskt värde, till exempel `containsKeyword` eller ett komplext objekt.

> [!TIP]
> För att få åtkomst till ett komplext svar från en funktion i en Logic app använder du åtgärden **parsa JSON** .

När du är klar sparar du funktionen och lägger sedan till funktionen som en åtgärd i den Logic-app som du skapar.

## <a name="add-azure-function-to-logic-app"></a>Lägg till Azure Function i Logic app

1. I Logic App Designer, under åtgärden **identifiera sentiment** , väljer du **nytt steg**.

2. Leta upp **Azure Functions** -anslutningen och välj sedan den funktion som du skapade.

3. Under **brödtext i begäran** väljer du **Tweet-text**.

![Konfigurerat Azure Function-steg][2]

## <a name="run-and-monitor-your-logic-app"></a>Köra och övervaka din Logic app

Om du vill granska alla aktuella eller tidigare körningar för din Logic-app kan du använda de omfattande fel söknings-och övervaknings funktionerna som Azure Logic Apps tillhandahåller i Azure Portal, Visual Studio eller via Azure REST-API: er och SDK: er.

För att enkelt testa din Logic app, i Logic App Designer, väljer du **Kör utlösare**. Utlösaren söker efter tweets baserat på det angivna schemat tills en tweet som uppfyller dina kriterier hittas. När körningen fortskrider visar designern en live-vy för den här körningen.

Visa tidigare körnings historik i Visual Studio eller Azure Portal: 

* Öppna Visual Studio Cloud Explorer. Hitta din Logi Kap par, öppna appens snabb meny. Välj **Öppna körnings historik**.

  > [!TIP]
  > Om du inte har det här kommandot i Visual Studio 2019 kontrollerar du att du har de senaste uppdateringarna för Visual Studio.

* Leta reda på din Logic app i Azure Portal. På din Logic Apps-meny väljer du **Översikt**. 

## <a name="create-automated-deployment-templates"></a>Skapa mallar för automatisk distribution

När du har skapat en Logic app-lösning kan du samla in och distribuera din app som en [Azure Resource Manager mall](../azure-resource-manager/templates/overview.md) till valfri Azure-region i världen. Du kan använda den här funktionen både för att ändra parametrar för att skapa olika versioner av din app och för att integrera din lösning i Azure-pipelines. Du kan också inkludera Azure Functions i distributions mal len så att du kan hantera hela lösningen med alla beroenden som en enda mall. Lär dig hur du [automatiserar Logic app-distributionen](logic-apps-azure-resource-manager-templates-overview.md).

En exempel distributions mall med en Azure-funktion finns i [lagrings platsen för Azure snabb starts mal len](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>Nästa steg

* [Hitta andra exempel och scenarier för Azure Logic Apps](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png