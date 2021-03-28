---
title: Redigera och hantera Logic Apps med hjälp av Visual Studio med Cloud Explorer
description: Redigera, uppdatera, hantera, lägga till i käll kontroll och distribuera Logi Kap par med hjälp av Visual Studio med Cloud Explorer
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/29/2020
ms.openlocfilehash: 56b74e440fcb09ab206bbb069517dd756221f809
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639558"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Hantera Logic Apps i Visual Studio

Även om du kan skapa, redigera, hantera och distribuera Logi Kap par i [Azure Portal](https://portal.azure.com)kan du också använda Visual Studio när du vill lägga till dina Logi Kap par i käll kontrollen, publicera olika versioner och skapa [Azure Resource Manager](../azure-resource-manager/management/overview.md) mallar för olika distributions miljöer. Med Visual Studio Cloud Explorer kan du hitta och hantera dina Logi Kap par tillsammans med andra Azure-resurser. Du kan till exempel öppna, Hämta, redigera, köra, Visa körnings historik, inaktivera och aktivera Logi Kap par som redan har distribuerats i Azure Portal. Om du inte har arbetat med Azure Logic Apps i Visual Studio kan du lära dig [hur du skapar Logi Kap par med Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

Du kan också [Hantera dina](manage-logic-apps-with-azure-portal.md)Logi Kap par i Azure Portal.

> [!IMPORTANT]
> Om du distribuerar eller publicerar en Logic app från Visual Studio skrivs versionen av appen över i Azure Portal. Om du gör ändringar i Azure Portal som du vill behålla, se till att du [uppdaterar Logic-appen i Visual Studio](#refresh) från Azure Portal innan du distribuerar eller publicerar från Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Hämta och installera följande verktyg, om du inte redan har dem:

  * [Visual Studio 2019, 2017 eller 2015 – Community Edition eller senare](https://aka.ms/download-visual-studio). I denna snabbstart används Visual Studio Community 2017 som är tillgängligt utan kostnad.

    > [!IMPORTANT]
    > När du installerar Visual Studio 2019 eller 2017 ser du till att du väljer arbets belastningen **Azure Development** .
    > Mer information finns i [Hantera resurser som är kopplade till dina Azure-konton i Visual Studio Cloud Explorer](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer).

    Om du vill installera Cloud Explorer för Visual Studio 2015 [laddar du ned Cloud Explorer från Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). Mer information finns i [Hantera resurser som är associerade med dina Azure-konton i Visual Studio Cloud Explorer (2015)](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015&preserve-view=true).

  * [Azure SDK (2.9.1 eller senare)](https://azure.microsoft.com/downloads/)

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * De senaste Azure Logic Apps verktygen för Visual Studio-tillägget för den version du vill använda:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Du kan hämta och installera Azure Logic Apps Tools direkt från Visual Studio Marketplace, eller läsa mer om [hur du installerar tillägget från Visual Studio](/visualstudio/ide/finding-and-using-visual-studio-extensions). Se till att starta om Visual Studio när installationen är klar.

  * Information om hur du använder Azure Government-prenumerationer med Visual Studio finns i följande avsnitt om ytterligare konfiguration:

    * Visual Studio 2019: [snabb start: Anslut till Azure Government med Visual Studio](../azure-government/documentation-government-connect-vs.md)

    * Visual Studio 2017: [Introduktion till Visual Studio-tillägget för Azure-miljön](https://devblogs.microsoft.com/azuregov/introducing-the-azure-environment-selector-visual-studio-extension/), som du kan hämta och installera från [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=SteveMichelotti.AzureEnvironmentSelector).

* Åtkomst till webben när du använder den inbäddade Logic Apps designer

  Designer kräver en Internetanslutning för att kunna skapa resurser i Azure och läsa in egenskaper och data från anslutningarna i din logikapp.

<a name="find-logic-apps-vs"></a>

## <a name="find-logic-apps"></a>Hitta Logic Apps

I Visual Studio kan du hitta alla Logic Apps som är associerade med din Azure-prenumeration och distribueras i Azure Portal med hjälp av Cloud Explorer.

1. Öppna Visual Studio. I menyn **Visa** väljer du **Cloud Explorer**.

1. I Cloud Explorer väljer du ikonen **konto hantering** . Välj den Azure-prenumeration som är associerad med dina Logi Kap par och välj **Använd**. Exempel:

   ![Välj "konto hantering"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. Välj **resurs typer** bredvid ikonen **konto hantering** . Under din Azure-prenumeration expanderar du **Logic Apps** så att du kan visa alla distribuerade Logic-appar som är associerade med din prenumeration.

Öppna sedan din Logic-app i Logic app-redigeraren.

<a name="open-designer"></a>

## <a name="open-logic-apps-in-visual-studio"></a>Öppna Logic Apps i Visual Studio

I Visual Studio kan du öppna Logi Kap par som tidigare har skapats och distribuerats antingen direkt via Azure Portal eller som Azure Resource Group-projekt med Visual Studio.

1. [Öppna Cloud Explorer och hitta din Logic app](#find-logic-apps-vs).

1. Från den logiska appens snabb meny väljer du **Öppna med Logic app Editor**.

   > [!TIP]
   > Om du inte har det här kommandot i Visual Studio 2019 kontrollerar du att du har de senaste uppdateringarna för Visual Studio.

   ![Öppna distribuerad Logic-app från Azure Portal](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   När Logic-appen öppnas i Logic Apps designer, längst ned i designern, kan du välja **kodvyn** så att du kan granska den underliggande strukturen för Logic app-definitionen. Om du vill skapa en Distributionsmall för Logic-appen, lär du dig [att ladda ned en Azure Resource Manager mall](#download-logic-app) för den Logic-appen. Lär dig mer om [Resource Manager-mallar](../azure-resource-manager/templates/overview.md).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Ladda ned från Azure

Du kan [Ladda ned](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource) Logic apps från [Azure Portal](https://portal.azure.com) och spara dem som [Azure Resource Manager](../azure-resource-manager/management/overview.md) mallar. Du kan sedan lokalt redigera mallarna med Visual Studio och anpassa Logic Apps för olika distributions miljöer.  Hämtning av Logic Apps *parameterizes* automatiskt sina definitioner inuti [Resource Manager-mallar](../azure-resource-manager/templates/overview.md), som också använder JavaScript Object Notation (JSON).

1. [Öppna den Logic-app som du vill ladda ned från Azure](#open-designer)i Visual Studio med hjälp av Cloud Explorer.

1. Från den logiska appens snabb meny väljer du **Öppna med Logic app Editor**.

   > [!TIP]
   > Om du inte har det här kommandot i Visual Studio 2019 kontrollerar du att du har de senaste uppdateringarna för Visual Studio.

   Logic app öppnas i Logic App Designer.

1. I verktygsfältet designer väljer du **Hämta**.

   ![Ladda ned Logic app från Azure Portal](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. När du uppmanas att ange en plats kan du bläddra till den platsen och spara Resource Manager-mallen för Logic app-definitionen i JSON-filformat (. JSON).

   Din Logic app-definition visas i `resources` underavsnittet i Resource Manager-mallen. Nu kan du redigera Logic app-definitionen och Resource Manager-mallen med Visual Studio. Du kan också lägga till mallen som ett [Azure Resource Group-projekt](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) i en Visual Studio-lösning. Lär dig mer om [Azure Resource Group-projekt för Logic Apps i Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>Länk till integrations konto

För att skapa Logi Kap par för företag-till-företag (B2B) företags integrations scenarier, kan du länka din Logic app till ett tidigare skapat [integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) som finns i samma region som din Logic app. Ett integrations konto innehåller B2B-artefakter, till exempel handels partner, avtal, scheman och kartor, och tillåter att din Logic app använder B2B-kopplingar för XML-verifiering och flat fil kodning eller avkodning. Även om du kan [skapa den här länken med hjälp av Azure Portal](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account), kan du också använda Visual Studio när du uppfyller [kraven](#requirements)och din Logic app finns som en JSON-fil (. JSON) i ett [Azure Resource Group-projekt](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). Lär dig mer om [Azure Resource Group-projekt för Logic Apps i Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project).

1. Öppna Azure Resource Group-projektet som innehåller din Logic app i Visual Studio.

1. I Solution Explorer öppnar du snabb menyn **<Logic-App-Name # C1.jspå** filens snabb meny och väljer **Öppna med Logic App Designer**. (Tangent bord: Ctrl + L)

   ![Öppna Logic Apps. JSON-fil med Logic App Designer](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Om du inte har det här kommandot i Visual Studio 2019 kontrollerar du att du har de senaste uppdateringarna för Visual Studio och tillägget Azure Logic Apps tools.

1. Kontrol lera att Logic App Designer har fokus genom att välja designerns flik eller yta så att Fönstret Egenskaper visar **integrations konto** egenskapen för din Logic app.

   ![Fönstret Egenskaper-egenskapen för integrations kontot](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > Om Fönstret Egenskaper inte redan är öppen går du till menyn **Visa** och väljer **Egenskaper-fönstret**. (Tangent bord: Tryck F4)

1. Öppna egenskaps listan **integrations konto** och välj det integrations konto som du vill länka till din Logic app, till exempel:

   ![Öppna egenskaps listan för integrations kontot](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. Kom ihåg att spara din Visual Studio-lösning när du är klar.

När du ställer in **integrations konto** egenskapen i Visual Studio och sparar din Logic app som en Azure Resource Manager mall, innehåller mallen även en parameter deklaration för det valda integrations kontot. Mer information om mallparametrar och Logic Apps finns i [Översikt: automatisera Logic app-distribution](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="change-location"></a>

## <a name="change-deployment-location"></a>Ändra distributions plats

I Visual Studio, om din Logic app finns som en JSON-fil (. JSON) i ett [Azure-resurs grupp projekt](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) som du använder för att automatisera distributionen, är den logiska appen inställd på en plats typ och en speciell plats. Den här platsen är antingen en Azure-region eller en befintlig [integrerings tjänst miljö (ISE)](connect-virtual-network-vnet-isolated-environment.md).

Om du vill ändra din Logi Kap par plats typ eller plats måste du öppna din Logic Apps-fil med arbets flödes definition (. JSON) från Solution Explorer med hjälp av Logic Apps designer. Du kan inte ändra dessa egenskaper med hjälp av Cloud Explorer.

> [!IMPORTANT]
> Att ändra plats typen från **region** till [**integration service Environment**](connect-virtual-network-vnet-isolated-environment-overview.md) påverkar din Logic Apps [pris modell](logic-apps-pricing.md#fixed-pricing) som används för fakturering, [gränser](logic-apps-limits-and-config.md#integration-account-limits), [integrations konto support](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus)och så vidare. Innan du väljer en annan typ av plats, se till att du förstår den resulterande påverkan i din Logic app.

1. Öppna Azure Resource Group-projektet som innehåller din Logic app i Visual Studio.

1. I Solution Explorer öppnar du `<logic-app-name>.json` filens snabb meny och väljer **Öppna med Logic App Designer**. (Tangent bord: Ctrl + L)

   ![Öppna Logic Apps. JSON-fil med Logic App Designer](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Om du inte har det här kommandot i Visual Studio 2019 kontrollerar du att du har de senaste uppdateringarna för Visual Studio och tillägget Azure Logic Apps tools.

1. Kontrol lera att Logic App Designer har fokus genom att välja designerns flik eller yta så att Fönstret Egenskaper visar egenskaperna **Välj plats typ** och **plats** för din Logic app. Projektets plats typ har angetts till antingen **region** eller **integration service Environment**.

   ![Fönstret Egenskaper-egenskaper för "Välj plats typ" & "plats"](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > Om Fönstret Egenskaper inte redan är öppen går du till menyn **Visa** och väljer **Egenskaper-fönstret**. (Tangent bord: Tryck F4)

1. Om du vill ändra plats typen öppnar du egenskaps listan **Välj platstyp** och väljer den typ av plats som du vill använda.

   Om plats typen till exempel är **integration service Environment**, kan du välja **region**.

   !["Välj typ av plats"-egenskap-ändra platstyp](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. Om du vill ändra den angivna platsen öppnar du egenskaps listan **plats** . Välj den plats som du vill använda baserat på typ av plats, till exempel:

   * Välj en annan Azure-region:

     ![Öppna egenskaps listan "plats", Välj en annan Azure-region](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * Välj en annan ISE:

     ![Öppna egenskaps listan "plats", Välj en annan ISE](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. Kom ihåg att spara din Visual Studio-lösning när du är klar.

När du ändrar plats typen eller platsen i Visual Studio och sparar din Logic app som en Azure Resource Manager mall, innehåller mallen även parameter deklarationer för den plats typen och platsen. Mer information om mallparametrar och Logic Apps finns i [Översikt: automatisera Logic app-distribution](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Uppdatera från Azure

Om du redigerar din Logi Kap par i Azure Portal och vill behålla ändringarna, måste du uppdatera appens version i Visual Studio med dessa ändringar.

* I Visual Studio i verktygsfältet Logic App Designer väljer du **Uppdatera**.

  \- eller -

* Öppna din Logic Apps snabb meny i Visual Studio Cloud Explorer och välj **Uppdatera**.

![Uppdatera Logic app med uppdateringar](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>Publicera uppdateringar av logikappen

När du är redo att distribuera dina Logic app-uppdateringar från Visual Studio till Azure väljer du **publicera** i Logic App Designer-verktygsfältet.

![Publicera uppdaterad Logic app till Azure Portal](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>Köra logikappen manuellt

Du kan utlösa en logisk app som distribuerats i Azure från Visual Studio manuellt. I verktygsfältet Logic App Designer väljer du **Kör utlösare**.

![Kör utlösaren manuellt för din Logic app](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Granska körningshistorik

Om du vill kontrol lera statusen och diagnostisera problem med att Logic App körs kan du granska informationen, till exempel indata och utdata, för de som körs i Visual Studio.

1. Öppna din Logic Apps snabb meny i Cloud Explorer och välj **Öppna körnings historik**.

   ![Öppna körnings historiken för din Logic app](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. Om du vill visa information om en speciell körning dubbelklickar du på en körning. Exempel:

   ![Visa information om en speciell körning](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Om du vill sortera tabellen efter egenskap väljer du kolumn rubriken för den egenskapen.

1. Expandera stegen vars indata och utdata du vill granska, till exempel:

   ![Visa indata och utdata för varje steg](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Inaktivera eller aktivera logikappen

Utan att ta bort din Logic app kan du stoppa utlösaren från att starta vid nästa tillfälle när utlösarens villkor är uppfyllt. Om du inaktiverar din Logic app förhindrar Logic Appss motorn att skapa och köra framtida arbets flödes instanser för din Logic app. Öppna din Logic Apps snabb meny i Cloud Explorer och välj **inaktivera**.

![Inaktivera din Logic app i Cloud Explorer](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

> [!NOTE]
> När du inaktiverar en Logic app instansieras inga nya körningar. Alla pågående och väntande körningar fortsätter tills de har slutförts, vilket kan ta lång tid att slutföra.

Om du vill återaktivera din Logic App öppnar du din Logic Apps snabb meny i Cloud Explorer och väljer **Aktivera**.

![Aktivera Logic app i Cloud Explorer](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

## <a name="delete-your-logic-app"></a>Ta bort din logikapp

För att ta bort din Logic app från Azure Portal, i Cloud Explorer, öppnar du din Logic Apps snabb meny och väljer **ta bort**.

![Ta bort din Logic app från Azure Portal](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

> [!NOTE]
> När du tar bort en logikapp instantieras inga nya körningar. Alla pågående och väntande körningar avbryts. Om du har flera tusen körningar kan det ta relativt lång tid att avbryta dem.

> [!NOTE]
> Om du tar bort och återskapar en underordnad Logic-app måste du spara om den överordnade Logic-appen. Den omskapade underordnade appen kommer att ha olika metadata.
> Om du inte sparar om den överordnade Logic-appen när du har återskapat dess underordnade Logic-program, kommer anropen till den underordnade Logic-appen att Miss varnas med fel "obehörig". Det här beteendet gäller för över-underordnade Logic Apps, till exempel de som använder artefakter i integrations konton eller anropar Azure Functions.

## <a name="troubleshooting"></a>Felsökning

När du öppnar ditt Logic Apps-projekt i Logic Apps designer kanske du inte får möjlighet att välja din Azure-prenumeration. I stället öppnas din Logic-app med en Azure-prenumeration som inte är den som du vill använda. Detta beror på att när du har öppnat en Logic Apps. JSON-fil cachelagrar Visual Studio den första valda prenumerationen för framtida användning. Försök med något av följande steg för att lösa problemet:

* Byt namn på Logic Apps. JSON-filen. Prenumerationens cacheminne beror på fil namnet.

* Ta bort tidigare valda prenumerationer för *alla* Logi Kap par i lösningen genom att ta bort den dolda mappen Visual Studio Settings (. vs) i din lösnings katalog. Den här platsen lagrar din prenumerations information.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du hanterar distribuerade Logic Apps med Visual Studio. Sedan kan du läsa mer om att anpassa definitioner för Logic app för distribution:

> [!div class="nextstepaction"]
> [Redigera definitioner för Logic app i JSON](../logic-apps/logic-apps-author-definitions.md)
