---
title: Bygg den första appen utan server i Visual Studio
description: Bygg, distribuera och hantera en app utan server genom att använda Azure Logic Apps och Azure Functions i Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: 1758cca902eb77ffc66824cb56b8add9446fabf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96749155"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Bygg din första app utan server genom att använda Azure Logic Apps och Azure Functions i Visual Studio

Du kan snabbt utveckla och distribuera molnappar med hjälp av Server lös verktyg och funktioner i Azure, till exempel [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Azure Functions](../azure-functions/functions-overview.md). Den här artikeln visar hur du börjar skapa en app utan server, som använder en Logic app som anropar en Azure-funktion i Visual Studio. Om du vill veta mer om lösningar utan server i Azure kan du läsa mer i [Azure server med Functions och Logic Apps](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill bygga en app utan server i Visual Studio behöver du:

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Följande verktyg. Om du inte redan har gjort det kan du ladda ned och installera dem.

  * [Visual Studio 2019, 2017 eller 2015 (community eller annan utgåva)](https://aka.ms/download-visual-studio). 
  I denna snabbstart används Visual Studio Community 2017 som är tillgängligt utan kostnad.

    > [!IMPORTANT]
    > När du installerar Visual Studio 2019 eller 2017 ser du till att du väljer arbets belastningen **Azure Development** .

  * [Microsoft Azure SDK för .net (version 2.9.1 eller senare)](https://azure.microsoft.com/downloads/). 
  Läs mer om [Azure SDK för .NET](/dotnet/azure/intro).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * Azure Logic Apps verktyg för Visual Studio-versionen som du vill använda:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Du kan hämta och installera Azure Logic Apps Tools direkt från Visual Studio Marketplace, eller läsa mer om [hur du installerar tillägget från Visual Studio](/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Se till att starta om Visual Studio när installationen är klar.

  * [Azure Functions Core tools](https://www.npmjs.com/package/azure-functions-core-tools) för lokalt fel söknings funktioner.

* Åtkomst till webben när du använder den inbäddade Logic App Designer.

  Designer kräver en Internetanslutning för att kunna skapa resurser i Azure och läsa in egenskaper och data från anslutningarna i din logikapp. 
  Om du t.ex. använder Dynamics CRM Online-anslutningen kontrollerar Designer om CRM-instansen har några standardegenskaper och anpassade egenskaper.

## <a name="create-a-resource-group-project"></a>Skapa ett resurs grupps projekt

Kom igång genom att skapa ett [Azure Resource Group-projekt](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) för appen utan server. I Azure skapar du resurser i en *resurs grupp*, vilket är en logisk samling som du använder för att organisera, hantera och distribuera resurser för en hel app som en enda till gång. För en app utan server i Azure inkluderar resurs gruppen resurser för både Azure Logic Apps och Azure Functions. Läs mer om [Azure-resursgrupper och resurser](../azure-resource-manager/management/overview.md).

1. Starta Visual Studio och logga in med ditt Azure-konto.

1. Gå till **Arkiv**-menyn och välj **Nytt** > **Projekt**.

   ![Skapa ett nytt projekt i Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. Under **Installerad** väljer du **Visual C#** eller **Visual Basic**. Välj sedan **moln**  >  **resurs grupp i Azure**.

   > [!NOTE]
   > Om **moln** kategorin eller **Azures resurs grupps** projekt inte finns kontrollerar du att du har installerat Azure SDK för Visual Studio.

   Följ dessa steg om du använder Visual Studio 2019:

   1. I rutan **skapa ett nytt projekt** väljer du projekt mal len **Azure-resurs grupp** för antingen Visual C# eller Visual Basic och väljer sedan **Nästa**.

   1. Ange det namn och den andra projekt information som du vill använda för Azure-resurs gruppen. När du är färdig väljer du **Skapa**.

1. Ge ditt projekt ett namn och en plats och välj sedan **OK**.

   Med Visual Studio kan du välja en mall från listan mallar. 
   I det här exemplet används en Azure snabb starts mall så att du kan bygga en app utan server som innehåller en logisk app och ett anrop till en Azure-funktion.

   > [!TIP]
   > I scenarier där du inte vill fördistribuera lösningen i en Azure-resurs grupp kan du använda den tomma **Logic app** -mallen, som bara skapar en tom Logic app.

1. I listan **Visa mallar från den här platsen** väljer du **Azure snabb start (GitHub.com/Azure/Azure-QuickStart-templates)**.

1. I rutan Sök anger du "Logic-app" som filter. Välj mallen **101-Logic-app-och-Function-app** från resultaten.

   ![Välj Azure snabb starts mall](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio skapar och öppnar en lösning för resurs grupps projektet. 
   Den Azure snabb starts mall som du har valt skapar en distributionsmall som heter azuredeploy.jsinuti resurs grupps projektet. Den här distributions mal len innehåller definitionen för en enkel Logic-app som utlöses av en HTTP-begäran, anropar en Azure-funktion och returnerar resultatet som ett HTTP-svar.

   ![Ny lösning utan Server](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Distribuera sedan din lösning till Azure. Du måste göra detta innan du kan öppna distributions mal len och granska resurserna för appen utan server.

## <a name="deploy-your-solution"></a>Distribuera din lösning

Innan du kan öppna din Logic app i Logic App Designer i Visual Studio måste du ha en Azure-resurs grupp som redan har distribuerats i Azure. Designern kan sedan skapa anslutningar till resurser och tjänster i din Logic app. För den här uppgiften följer du de här stegen för att distribuera lösningen från Visual Studio till Azure Portal:

1. I Solution Explorer, från resurs projektets snabb meny, väljer du **distribuera**  >  **ny**.

   ![Skapa ny distribution för resurs grupp](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Om de inte redan är markerade väljer du din Azure-prenumeration och den resurs grupp som du vill distribuera till. Välj sedan **distribuera**.

   ![Distributionsinställningar](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Om rutan **Redigera parametrar** visas, anger du de resurs namn som ska användas för din Logic app och din Azure Function-app vid distributionen och sparar sedan inställningarna. Se till att du använder ett globalt unikt namn för din Function-app.

   ![Ange namn för din Logic app-och Function-app](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   När Visual Studio startar distributionen till din angivna resurs grupp visas din lösnings distributions status i fönstret Visual Studio **output** . 
   När distributionen är klar är din Logic app Live i Azure Portal.

## <a name="edit-your-logic-app-in-visual-studio"></a>Redigera din Logic app i Visual Studio

Om du vill redigera din Logic app efter distributionen öppnar du din Logic app med hjälp av Logic Apps designer i Visual Studio.

1. I Solution Explorer väljer du **Öppna med Logic App Designer** från snabb menyn i azuredeploy.jspå filen.

   ![Öppna azuredeploy.jspå i Logic App Designer](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Om du inte har det här kommandot i Visual Studio 2019 kontrollerar du att du har de senaste uppdateringarna för Visual Studio.

1. När rutan **Egenskaper för Logic app** visas, under **prenumeration**, väljer du din Azure-prenumeration om den inte redan är vald. Under **resurs grupp** väljer du den resurs grupp och plats där du distribuerade lösningen. Välj sedan **OK**.

   ![Egenskaper för Logic app](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   När Logic Apps designer öppnas kan du fortsätta att lägga till steg eller ändra arbets flödet och spara dina uppdateringar.

   ![Öppna Logic app i Logic App Designer](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Skapa ett Azure Functions-projekt

Om du vill skapa ett Functions-projekt och en funktion med hjälp av Java Script, python, F #, PowerShell, batch eller bash följer du stegen i [arbeta med Azure Functions Core tools](../azure-functions/functions-run-local.md). Om du vill utveckla din Azure-funktion genom att använda C# i lösningen använder du ett C#-klass bibliotek genom att följa stegen i [publicera ett .NET-klass bibliotek som en Funktionsapp](https://azure.microsoft.com/blog/).

## <a name="deploy-functions-from-visual-studio"></a>Distribuera funktioner från Visual Studio

Distributions mal len distribuerar alla Azure-funktioner som du har i din lösning från git-lagrings platsen som anges av variabler i azuredeploy.jsi filen. Om du skapar och redigerar funktions projekt i din lösning kan du kontrol lera att projektet är i git-källdokument (till exempel GitHub eller Azure DevOps) och sedan uppdatera `repo` variabeln så att mallen distribuerar din Azure-funktion.

## <a name="manage-logic-apps-and-view-run-history"></a>Hantera Logic Apps och Visa körnings historik

För logi Kap par som redan har distribuerats i Azure kan du fortfarande redigera, hantera, Visa körnings historik för och inaktivera dessa appar från Visual Studio.

1. Öppna **Cloud Explorer** från menyn **Visa** i Visual Studio.

1. Under **alla prenumerationer** väljer du den Azure-prenumeration som är associerad med de Logic Apps som du vill hantera och väljer sedan **Använd**.

1. Under **Logic Apps** väljer du din Logic app. Från den appens snabb meny väljer du **Öppna med Logic app Editor**.

   > [!TIP]
   > Om du inte har det här kommandot i Visual Studio 2019 kontrollerar du att du har de senaste uppdateringarna för Visual Studio.

Nu kan du ladda ned den redan publicerade Logic-appen till resurs grupps projektet. Även om du kanske har startat en Logic-app i Azure Portal kan du fortfarande importera och hantera den appen i Visual Studio. Mer information finns i [Hantera Logic Apps med Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Nästa steg

* [Hantera Logic Apps i Visual Studio](manage-logic-apps-with-visual-studio.md)
