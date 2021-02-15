---
title: Exportera flöden från Power automatisera till Azure Logic Apps
description: Migrera flöden från Power automatisera till Azure Logic Apps genom att exportera som Azure Resource Manager mallar
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, sneshaf, pinath, logicappspm
ms.topic: conceptual
ms.date: 01/20/2021
ms.openlocfilehash: a90f75db6961ea06b1cf9c2958556c1c2ef24805
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380142"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Exportera flöden från Power Automate och distribuera till Azure Logic Apps

> För flöden som skapats efter den 2020 oktober kan du inte längre exportera från automatisk start och distribution till Azure Logic Apps.

Om du vill utöka och utöka ditt flödes funktioner kan du migrera flödet från [energi spar läge](https://flow.microsoft.com) till [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Du kan exportera ditt flöde som en Azure Resource Manager mall för en Logic app, distribuera Logic app-mallen till en Azure-resurs grupp och sedan öppna den Logic-appen i Logic App Designer.

> [!NOTE]
> Alla Power inautomatiserade anslutningar är inte tillgängliga i Azure Logic Apps. Du kan bara migrera energi automatiserade flöden som har motsvarande kopplingar i Azure Logic Apps. Till exempel är knapp utlösaren, godkännande Connector och meddelande Connector särskilt för automatisk energi förbrukning. För närvarande stöds inte OpenAPI-baserade flöden i energi spar läge för export och distribution som Logic app-mallar.
>
> * Om du vill ta reda på vilka PowerPivot-kopplingar som inte har Logic Apps motsvarigheter, se Power automater- [kopplingar](/connectors/connector-reference/connector-reference-powerautomate-connectors).
>
> * Om du vill ta reda på vilka Logic Apps kopplingar inte har Energis par motsvarigheter, se [Logic Apps kopplingar](/connectors/connector-reference/connector-reference-logicapps-connectors).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Flödet som du vill exportera från Energis par

## <a name="export-your-flow"></a>Exportera ditt flöde

1. Logga in för att [Automatisera energi](https://flow.microsoft.com)och välj **mina flöden**. Hitta och välj ditt flöde. I verktygsfältet väljer du knappen med tre punkter (**...**) > **Exportera**  >  **Logic Apps mall (. JSON)**.

   ![Exportera flöde från energi automatisering](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Spara mallen. JSON-fil på den plats som du vill använda.

Mer information finns i [öka upp till Azure Logic Apps](https://flow.microsoft.com/blog/grow-up-to-logic-apps/).

## <a name="deploy-template-by-using-the-azure-portal"></a>Distribuera mallen med hjälp av Azure Portal

1. Logga in [Azure Portal](https://portal.azure.com) med ditt Azure-konto.

1. Skriv i rutan Sök på Azures start sida `custom template` . Välj **distribuera en anpassad mall**  >  **skapa** i resultaten.

   ![Sök efter och välj "Malldistribution"](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. Under **Anpassad distribution** väljer du **Bygg en egen mall i redigeraren**.

   ![Välj "Bygg din egen mall i redigeraren"](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. I verktygsfältet **Redigera mall** väljer du **Läs in fil**.

   ![Välj Läs in fil](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Bläddra till den plats där du sparade JSON-mallfilen som du exporterade från Energis par automatisering. Välj mallfilen > **Öppna**.

1. När redigeraren visar JSON, parametrar och resurser i mallen väljer du **Spara**.

   ![Spara mallen](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Ange nu mer information som din Logic app.

   1. Välj eller ange värden för indataparametrar för din mall.

      | Egenskap | Beskrivning |
      |----------|-------------|
      | **Prenumeration** | Azure-prenumerationen som ska användas för fakturering |
      | **Resursgrupp** | Den Azure-resurs grupp som ska användas för din Logic app. Du kan använda en befintlig grupp eller skapa en ny grupp. |
      | **Plats** | Den Azure-region som ska användas om du skapar en ny resurs grupp |
      | **Namn på Logic app** | Namnet som ska användas för din Logic app-resurs |
      | **Plats för Logic app** | Den Azure-region där du vill skapa en Logic app-resurs, om den skiljer sig från Azures resurs grupp |
      | <*anslutnings namn*> | Ett eller flera namn för alla tidigare skapade anslutningar som Logic-appen kan återanvända <p><p>**Obs**: om den här Logic-appen är din första skapas alla anslutningar som nya, så att du kan acceptera standard namnen. Annars kan du ange namnen på tidigare skapade anslutningar, som du kan använda i flera Logic Apps. |
      |||

      Exempel:

      ![Ange indataparametrar för mall](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   1. När du är klar kan du läsa igenom **villkoren** för att skapa nödvändiga Azure-resurser och fakturera din Azure-prenumeration enligt detta.

   1. När du är klar väljer du **Jag accepterar villkoren som anges ovan**  >  **köpet**.

      Azure distribuerar din mall som en Logic app till den angivna resurs gruppen.

1. Alla Logi Kap par som du migrerar från energi spar läge distribueras i inaktiverat tillstånd. Innan du aktiverar din Logi Kap par måste du godkänna alla nya anslutningar genom att följa dessa steg:

   1. Öppna den Logic-app som du skapade i Azure Portal. På menyn Logic Apps väljer du **Logic App Designer**.

      Varje anslutning som kräver auktorisering visar en varnings ikon:

      ![Varningsikon](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Expandera det steget för varje steg som kräver en auktoriserad anslutning och välj **Lägg till ny**.

      ![Skärm bild som visar knappen "Lägg till ny" markerad i fönstret för Outlook-anslutningar.](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Logga in på varje tjänst eller ange de autentiseringsuppgifter som krävs för att auktorisera anslutningen.

   1. När du har uppdaterat anslutningarna väljer du **Spara** i verktygsfältet designer.

1. När du är redo att aktivera din Logi Kap par väljer du **Översikt** på menyn Logic Apps och väljer sedan **Aktivera**.

   ![Aktivera Logic app](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Undvik att köra dubbla arbets flöden genom att inaktivera eller ta bort det ursprungliga flödet.

## <a name="deploy-template-by-using-visual-studio"></a>Distribuera mall med hjälp av Visual Studio

Om du har konfigurerat Visual Studio med [förutsättningarna](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) för att skapa Logi Kap par kan du distribuera den exporterade mallen till Azure Logic Apps med hjälp av Visual Studio.

1. I Visual Studio bläddrar du till och öppnar. JSON-filen för den Logic app-mall som du exporterade från energi automatisering.

1. I Visual Studio skapar du ett **Azure-resurs grupps** projekt som använder **Logic app** -mallen genom att följa stegen i [snabb start: skapa automatiserade uppgifter, processer och arbets flöden med Azure Logic Apps-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

   I det här exemplet skapas en Visual Studio-lösning med namnet "ImportedLogicApp".

   ![Skapa ett projekt för en Azure-resursgrupp](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. När lösningen har skapats öppnar du **LogicApp.jspå** filen i Solution Explorer, om filen inte redan är öppen.

1. Kopiera innehållet från den exporterade mallen och skriv över innehållet i **LogicApp.js** filen.

1. Innan du distribuerar din Logic app måste du godkänna alla nya anslutningar genom att följa dessa steg:

   1. Öppna snabb menyn **LogicApp.jspå** och välj sedan **Öppna med Logic App Designer**.

      ![Öppna mall med Logic App Designer](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Om du uppmanas väljer du den Azure-prenumeration och resurs grupp som du vill använda för att distribuera din Logic app.

      ![Välj Azure-prenumeration och resurs grupp](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      När din Logi Kap par har visats i designern visas varnings ikoner i alla anslutningar som kräver auktorisering:

      ![Anslutningar med varnings ikoner](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Expandera det steget för varje steg som kräver en auktoriserad anslutning och välj **Lägg till ny**.

      ![Lägg till ny anslutning](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Logga in på varje tjänst eller ange de autentiseringsuppgifter som krävs för att auktorisera anslutningen.

   1. Spara lösningen innan du distribuerar Logic-appen.

1. I Solution Explorer öppnar du projektets snabb meny och väljer **distribuera**  >  **ny**. Om du uppmanas logga in med ditt Azure-konto ska du göra det.

1. När du uppmanas till det bekräftar du Azure-prenumerationen, Azure-resurs gruppen och eventuella andra inställningar som du vill använda för distribution, till exempel en [parameter fil](../azure-resource-manager/templates/parameter-files.md) som ska användas för att skicka parameter värden för en mall och väljer sedan **distribuera**.

   ![Bekräfta distributions inställningar](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Om rutan **Redigera parametrar** visas anger du namnet på din Logic app-resurs i Azure och väljer **Spara**.  

   ![Redigera distributions parametrar](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   När distributionen startar visas appens distributionsstatus i Visual Studios **utdatafönster**. Om statusen inte visas ska du öppna listan **Show output from** (Visa utdata från) och välja Azure-resursgruppen. Exempel:

   ![Utdatafönster](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   Om några anslutningar i din Logi Kap par behöver inmatas från dig, öppnas ett PowerShell-fönster i bakgrunden och du uppmanas att ange nödvändiga lösen ord eller hemliga nycklar. När du har angett den här informationen fortsätter distributionen.

   ![Autentisera anslutningar](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   När distributionen är klar publiceras din Logic app, men den aktive ras inte i Azure Portal.

1. När du är redo att aktivera din Logi Kap par i Azure Portal kan du söka efter och öppna din Logic app i Logic App Designer. Välj **Översikt** på menyn Logic Apps och välj sedan **Aktivera**.

1. Undvik att köra dubbla arbets flöden genom att inaktivera eller ta bort det ursprungliga flödet.

Mer information om dessa distributions steg finns i [snabb start: skapa automatiserade uppgifter, processer och arbets flöden med Azure Logic Apps-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [anslutningar för Azure Logic Apps](../connectors/apis-list.md)
* Läs mer om [Azure Logic Apps](../logic-apps/logic-apps-overview.md)
