---
title: Automatisera uppgifter och arbetsflöden med Visual Studio
description: Skapa, schemalägga och köra automatiserade arbetsflöden för företagsintegrering med hjälp av Azure Logic Apps och Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2021
ms.openlocfilehash: 5ae67e5708a7298385a4e27d612566008884b972
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790067"
---
# <a name="quickstart-create-automated-tasks-processes-and-workflows-with-azure-logic-apps---visual-studio"></a>Snabbstart: Skapa automatiserade uppgifter, processer och arbetsflöden med Azure Logic Apps – Visual Studio

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och Visual Studio kan du skapa arbetsflöden för att automatisera uppgifter och processer som integrerar appar, data, system och tjänster i företag och organisationer. Den här snabbstarten visar hur du utformar och bygger sådana arbetsflöden genom att skapa logikappar i Visual Studio och distribuera apparna till Azure. Även om du kan utföra dessa uppgifter i Azure Portal kan Visual Studio lägga till logikappar i källkontrollen, publicera olika versioner och skapa Azure Resource Manager mallar för olika distributionsmiljöer.

Om du inte har Azure Logic Apps grundläggande begrepp kan du prova [snabbstarten](../logic-apps/quickstart-create-first-logic-app-workflow.md)för att skapa en logikapp i Azure Portal . Logikappdesignern fungerar på samma sätt i både Azure Portal och Visual Studio.

I den här snabbstarten skapar du samma logikapp med Visual Studio som Azure Portal snabbstarten. Du kan också lära dig att skapa en exempelapp [i Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)och skapa och hantera logikappar via Azure Command-Line Interface [(Azure CLI).](quickstart-logic-apps-azure-cli.md) Den här logikappen övervakar en webbplats RSS-flöde och skickar e-post för varje nytt objekt i flödet. Din färdiga logikapp ser ut som det här arbetsflödet på hög nivå:

![Skärmbild som visar det avancerade arbetsflödet för en färdig logikapp.](./media/quickstart-create-logic-apps-with-visual-studio/high-level-workflow-overview.png)

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Om du har en Azure Government prenumeration följer du dessa ytterligare steg för att konfigurera [Visual Studio för Azure Government Cloud](#azure-government).

* Hämta och installera följande verktyg, om du inte redan har dem:

  * [Visual Studio 2019, 2017 eller 2015 – Community Edition eller högre.](https://aka.ms/download-visual-studio) I den här snabbstarten används Visual Studio Community 2017.

    > [!IMPORTANT]
    > När du installerar Visual Studio 2019 eller 2017 ska du se till att du väljer **arbetsbelastningen Azure Development.**

  * [Microsoft Azure SDK för .NET (2.9.1 eller senare)](https://azure.microsoft.com/downloads/). Läs mer om [Azure SDK för .NET](/dotnet/azure/intro).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Det senaste Azure Logic Apps Tools för Visual Studio-tillägget för den version som du vill använda:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Du kan hämta och installera Azure Logic Apps Tools direkt från Visual Studio Marketplace, eller läsa mer om [hur du installerar tillägget från Visual Studio](/visualstudio/ide/finding-and-using-visual-studio-extensions). Se till att starta om Visual Studio när installationen är klar.

* Tillgång till Internet när du använder den inbäddade Logic App Designer

  Designern behöver en Internetanslutning för att skapa resurser i Azure och läsa egenskaper och data från anslutningsappar i logikappen.

* Ett e-postkonto som stöds av Logic Apps, till exempel Outlook för Microsoft 365, Outlook.com eller Gmail. För andra leverantörer kan du granska [listan över anslutningsappar här.](/connectors/) I det här exemplet används Office 365 Outlook. Om du använder en annan provider är övergripande stegen desamma, men ditt användargränssnitt kan skilja sig något.

  > [!IMPORTANT]
  > Om du vill använda Gmail-anslutningsappen kan endast G Suite-företagskonton använda den här anslutningsappen utan begränsning i logikappar. Om du har ett Gmail-konsumentkonto kan du använda den här anslutningsappen med endast specifika Google-godkända tjänster, eller så kan du skapa en Google-klientapp som ska användas för autentisering med [Gmail-anslutningsappen.](/connectors/gmail/#authentication-and-bring-your-own-application) Mer information finns i [Datasäkerhets- och sekretesspolicyer för Google-anslutningsappar i Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Om logikappen behöver kommunicera via en brandvägg som begränsar trafiken till specifika  IP-adresser måste brandväggen tillåta åtkomst för både [inkommande](logic-apps-limits-and-config.md#inbound) och utgående [IP-adresser](logic-apps-limits-and-config.md#outbound) som används av Logic Apps-tjänsten eller körningen i Azure-regionen där logikappen finns. Om logikappen även använder hanterade anslutningsappar, till exempel Office 365 Outlook-anslutningsappen eller SQL-anslutningsappen, eller använder anpassade [anslutningsappar](/connectors/custom-connectors/)måste brandväggen även tillåta åtkomst för alla utgående [IP-adresser](logic-apps-limits-and-config.md#outbound) för hanterade anslutningsappar i logikappens Azure-region. [](../connectors/managed.md) 

<a name="azure-government"></a>

## <a name="set-up-visual-studio-for-azure-government"></a>Konfigurera Visual Studio för Azure Government

### <a name="visual-studio-2017"></a>Visual Studio 2017

Du kan använda [Azure Environment Selector Visual Studio tillägget](https://devblogs.microsoft.com/azuregov/introducing-the-azure-environment-selector-visual-studio-extension/), som du kan ladda ned och installera [från Visual Studio Marketplace.](https://marketplace.visualstudio.com/items?itemName=SteveMichelotti.AzureEnvironmentSelector)

### <a name="visual-studio-2019"></a>Visual Studio 2019

Om du vill Azure Government prenumerationer i Azure Logic Apps måste du lägga till en identifieringsslutpunkt [för Azure Government Cloud till Visual Studio](../azure-government/documentation-government-connect-vs.md). Men innan du loggar in på Visual Studio med *ditt Azure Government-konto* måste du byta namn på JSON-filen som genereras när du har lagt till slutpunkten för identifiering genom att följa dessa steg:

1. Stäng Visual Studio.

1. Hitta den genererade JSON-filen `Azure U.S. Government-A3EC617673C6C70CC6B9472656832A26.Configuration` med namnet på den här platsen:

   `%localappdata%\.IdentityService\AadConfigurations`
 
1. Byt namn på JSON-filen till `AadProvider.Configuration.json` .

1. Starta om Visual Studio.

1. Fortsätt med stegen för att logga in med ditt Azure Government konto.

Om du vill återställa den här konfigurationen tar du bort JSON-filen på följande plats och startar Visual Studio:

`%localappdata%\.IdentityService\AadConfigurations\AadProvider.Configuration.json`

<a name="create-resource-group-project"></a>

## <a name="create-azure-resource-group-project"></a>Skapa ett projekt för en Azure-resursgrupp

Kom igång genom att skapa ett [projekt för en Azure-resursgrupp](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). Läs mer om [Azure-resursgrupper och resurser](../azure-resource-manager/management/overview.md).

1. Starta Visual Studio. Logga in med ditt Azure-konto.

1. Gå till **Arkiv**-menyn och välj **Nytt** > **Projekt**. (Tangentbord: Ctrl + Skift + N)

   ![Gå till Arkiv-menyn och välj Nytt > Projekt](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

1. Under **Installerad** väljer du **Visual C#** eller **Visual Basic**. Välj **Cloud**  >  **Azure-resursgrupp**. Namnge projektet, t.ex.:

   ![Skapa ett projekt för en Azure-resursgrupp](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

   > [!NOTE]
   > Resursgruppnamn får bara innehålla bokstäver, siffror, punkter ( ), understreck ( ), bindestreck ( ) och `.` `_` `-` parenteser ( `(` , `)`  ), `.` men kan inte sluta med punkter ( ).
   >
   > Om **Cloud** eller **Azure Resource Group** inte visas kontrollerar du att du installerar Azure SDK för Visual Studio.

   Om du använder Visual Studio 2019 följer du dessa steg:

   1. I rutan **Skapa ett nytt projekt** väljer du Projektet **Azure-resursgrupp** för Visual C# eller Visual Basic. Välj **Nästa**.

   1. Ange ett namn för den Azure-resursgrupp som du vill använda och annan projektinformation. Välj **Skapa**.

1. Välj mallen Logikapp i **malllistan.** Välj **OK**.

   ![Välj Logic App-mallen](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   När Visual Studio har skapat ditt projekt öppnas Solution Explorer och visar din lösning. I din lösning lagrar **LogicApp.jspå** filen inte bara logikappens definition, utan är också en Azure Resource Manager mall som du kan använda för distribution.

   ![Solution Explorer visar den nya logikapplösningen och distributionsfilen](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

## <a name="create-blank-logic-app"></a>Skapa en tom logikapp

När du har azure-resursgruppsprojektet skapar du logikappen med **mallen Tom logikapp.**

1. I Solution Explorer duLogicApp.js **på** filens snabbmeny. Välj **Öppna med Logic App Designer**. (Tangentbord: Ctrl + L)

   ![Öppna filen LogicApp.json med Logic App Designer](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Om du inte har det här kommandot i Visual Studio 2019 kontrollerar du att du har de senaste uppdateringarna för Visual Studio.

   Visual Studio dig om din Azure-prenumeration och en Azure-resursgrupp för att skapa och distribuera resurser för logikappen och anslutningarna.

1. För **Prenumeration** väljer du din Azure-prenumeration. För **Resursgrupp väljer** du Skapa **ny för att** skapa en annan Azure-resursgrupp.

   ![Välj Azure-prenumeration, resursgrupp och plats för resurser](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   | Inställning | Exempelvärde | Description |
   | ------- | ------------- | ----------- |
   | Användarkonto | Fabrikam <br> sophia-owen@fabrikam.com | Det konto som du använde när du loggade in på Visual Studio |
   | **Prenumeration** | Betala per användning <br> (sophia-owen@fabrikam.com) | Namnet på din Azure-prenumeration och det associerade kontot |
   | **Resursgrupp** | MyLogicApp-RG <br> (USA, västra) | Azure-resursgruppen och platsen för att lagra och distribuera logikappens resurser |
   | **Plats** | **Samma som resursgrupp** | Platstyp och specifik plats för distribution av logikappen. Platstypen är antingen en Azure-region eller en befintlig [integrationstjänstmiljö (ISE).](connect-virtual-network-vnet-isolated-environment.md) <p>För den här snabbstarten behåller du platstypen **inställd på Region** och platsen inställd på Samma som **resursgrupp.** <p>**Obs!** När du har skapat resursgruppsprojektet kan du ändra platstyp och [plats,](manage-logic-apps-with-visual-studio.md#change-location)men olika platstyper påverkar logikappen på olika sätt. |
   ||||

1. I Logic Apps Designer öppnas en sida som visar en introduktionsvideo och vanliga utlösare. Rulla nedåt förbi videon och utlösare till **Mallar** och välj **Tom logikapp.**

   ![Välj Tom logikapp](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>Bygg ett arbetsflöde för logikappen

Lägg sedan till en [RSS-utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts) som utlöses när ett nytt flödesobjekt visas. Varje logikapp börjar med en utlösare som utlöses när specifika villkor uppfylls. Varje gång utlösaren utlöses skapar Logic Apps-motorn en logikappinstans som kör ditt arbetsflöde.

1. I Logic App Designer går du till sökrutan och väljer **Alla.** I sökrutan anger du "rss". I listan med utlösare väljer du den här utlösaren: **När ett flödesobjekt publiceras**

   ![Bygg din logikapp genom att lägga till en utlösare och åtgärder](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

1. När utlösaren visas i designern slutför du bygget av logikappen genom att följa arbetsflödesstegen i [snabbstarten Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger)och återgå sedan till den här artikeln. När du är klar ser logikappen ut som i det här exemplet:

   ![Färdig logikapp](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app-workflow.png)

1. Spara din Visual Studio lösning. (Tangentbord: Ctrl + S)

<a name="deploy-to-Azure"></a>

## <a name="deploy-logic-app-to-azure"></a>Distribuera logikappen till Azure

Innan du kan köra och testa logikappen distribuerar du appen till Azure från Visual Studio.

1. I Solution Explorer du på projektets snabbmeny och väljer **Distribuera**  >  **ny.** Om du uppmanas logga in med ditt Azure-konto ska du göra det.

   ![Skapa en distribution av den logiska appen](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

1. För den här distributionen behåller du azure-standardprenumerationen, resursgruppen och andra inställningar. Välj **Distribuera**.

   ![Distribuera logikappen till Azure-resursgruppen](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

1. Om rutan **Redigera parametrar** visas anger du ett resursnamn för logikappen. Spara inställningarna.

   ![Ange distributionsnamn för logikapp](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   När distributionen startar visas appens distributionsstatus i Visual Studios **utdatafönster**. Om statusen inte visas ska du öppna listan **Show output from** (Visa utdata från) och välja Azure-resursgruppen.

   ![Status för distribution](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   Om dina valda anslutningsappar behöver indata från dig öppnas ett PowerShell-fönster i bakgrunden och frågar efter nödvändiga lösenord eller hemliga nycklar. När du har angett den här informationen fortsätter distributionen.

   ![PowerShell-fönster](./media/quickstart-create-logic-apps-with-visual-studio/logic-apps-powershell-window.png)

   När distributionen är klar är logikappen live i Azure Portal körs enligt det angivna schemat (varje minut). Om utlösaren hittar nya flödesobjekt utlöses utlösaren, vilket skapar en arbetsflödesinstans som kör logikappens åtgärder. Logikappen skickar e-post för varje nytt objekt. Om utlösaren inte hittar nya objekt utlöses inte utlösaren och "hoppar över" instansierar arbetsflödet. Logikappen väntar tills nästa intervall innan kontrollen.

   Här är exempel på e-postmeddelanden som den här logikappen skickar. Om du inte får e-post kan du titta i mappen Skräppost.

   ![Outlook skickar ett e-postmeddelande för varje nytt RSS-objekt](./media/quickstart-create-logic-apps-with-visual-studio/outlook-email.png)

Grattis, du har skapat och distribuerat logikappen med Visual Studio. Om du vill hantera din logikapp och granska dess körningshistorik, se [Hantera Logic Apps i Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="add-new-logic-app"></a>Lägga till ny logikapp

När du har ett befintligt Azure-resursgruppsprojekt kan du lägga till en ny tom logikapp i projektet med hjälp av fönstret JSON-disposition.

1. I Solution Explorer du `<logic-app-name>.json` filen.

1. I menyn **Visa** väljer du Annan **Windows**  >  **JSON-disposition.**

1. Om du vill lägga till en resurs i mallfilen väljer **du Lägg** till resurs överst i fönstret JSON-disposition. Eller i fönstret JSON-disposition öppnar du **snabbmenyn för** resurser och väljer Lägg **till ny resurs.**

   ![Fönstret JSON-disposition](./media/quickstart-create-logic-apps-with-visual-studio/json-outline-window-add-resource.png)

1. I dialogrutan **Lägg till** resurs letar du upp i sökrutan och `logic app` väljer **Logikapp.** Ge logikappen ett namn och välj Lägg **till.**

   ![Lägg till resurs](./media/quickstart-create-logic-apps-with-visual-studio/add-logic-app-resource.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med logikappen tar du bort resursgruppen som innehåller logikappen och relaterade resurser.

1. Logga in på [Azure Portal](https://portal.azure.com) med samma konto som användes för att skapa din logikapp.

1. På Azure Portal väljer du **Resursgrupper** eller söker efter och väljer **Resursgrupper** på valfri sida. Välj logikappens resursgrupp.

1. På sidan **Översikt** väljer du Ta **bort resursgrupp.** Ange resursgruppens namn som bekräftelse och välj Ta **bort**.

   ![”Resursgrupper” > ”Översikt” > ”Ta bort resursgrupp”](./media/quickstart-create-logic-apps-with-visual-studio/clean-up-resources.png)

1. Ta bort Visual Studio-lösningen från den lokala datorn.

## <a name="next-steps"></a>Nästa steg

Genom att läsa den här artikeln har du lärt dig att bygga, distribuera och köra din logikapp med Visual Studio. Mer information om hur du hanterar och utför avancerad distribution för logikappar med Visual Studio finns i följande artiklar:

> [!div class="nextstepaction"]
> [Hantera Logic Apps i Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
