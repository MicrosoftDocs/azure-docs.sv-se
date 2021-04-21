---
title: Skapa Logic Apps förhandsgranskningsarbetsflöden i Visual Studio Code
description: Skapa och kör arbetsflöden för automatiserings- och integreringsscenarier i Visual Studio Code med Azure Logic Apps -tillägget (förhandsversion).
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/30/2021
ms.openlocfilehash: 4010f7e2d0d20216107a45109056478694c940ca
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772513"
---
# <a name="create-stateful-and-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Skapa tillståndsfulla och tillståndslösa arbetsflöden i Visual Studio Code med Azure Logic Apps (förhandsversion)

> [!IMPORTANT]
> Den här funktionen är tillgänglig som en offentlig förhandsversion utan servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med [Azure Logic Apps Preview](logic-apps-overview-preview.md)kan du skapa automatiserings- och integreringslösningar för appar, data, molntjänster och system genom att skapa och köra logikappar som innehåller tillståndslösa och [  tillståndslösa  ](logic-apps-overview-preview.md#stateful-stateless) arbetsflöden i Visual Studio Code med hjälp av Azure Logic Apps-tillägget (förhandsversion). Med den här nya logikapptypen kan du skapa flera arbetsflöden som drivs av den omdesignade körningsmiljön för Azure Logic Apps Preview, som ger portabilitet, bättre prestanda och flexibilitet för distribution och körning i olika värdmiljöer, inte bara Azure, utan även Docker-containrar. Mer information om den nya logikapptypen finns i [Översikt för Azure Logic Apps förhandsversion.](logic-apps-overview-preview.md)

![Skärmbild som visar Visual Studio kod, logikappsprojekt och arbetsflöde.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

I Visual Studio Code kan du börja med att skapa  ett projekt där du lokalt kan skapa och köra logikappens arbetsflöden i utvecklingsmiljön med hjälp av Azure Logic Apps-tillägget (förhandsversion). Du kan också börja med att skapa en ny logikappresurs [ **(förhandsversion)**](create-stateful-stateless-workflows-azure-portal.md)i Azure Portal, men båda metoderna ger dig möjlighet att distribuera och köra logikappen i samma typer av värdmiljöer.

Under tiden kan du fortfarande skapa den ursprungliga logikapptypen. Även om utvecklingsupplevelserna i Visual Studio Code skiljer sig mellan de ursprungliga och nya logikapptyperna kan din Azure-prenumeration innehålla båda typerna. Du kan visa och komma åt alla distribuerade logikappar i din Azure-prenumeration, men apparna är ordnade i egna kategorier och avsnitt.

Den här artikeln visar hur du skapar din logikapp och ett arbetsflöde i Visual Studio Code med hjälp av Azure Logic Apps-tillägget (förhandsversion) och utför dessa uppgifter på hög nivå:

* Skapa ett projekt för logikappen och arbetsflödet.

* Lägg till en utlösare och en åtgärd.

* Kör, testa, felsök och granska körningshistoriken lokalt.

* Hitta domännamnsinformation för brandväggsåtkomst.

* Distribuera till Azure, vilket innefattar att aktivera Application Insights.

* Hantera din distribuerade logikapp i Visual Studio Code och Azure Portal.

* Aktivera körningshistorik för tillståndslösa arbetsflöden.

* Aktivera eller öppna Application Insights efter distributionen.

* Distribuera till en Docker-container som du kan köra var som helst.

> [!NOTE]
> Information om aktuella kända problem finns på sidan [Logic Apps offentliga förhandsversionen av kända problem i GitHub.](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

## <a name="prerequisites"></a>Förutsättningar

### <a name="access-and-connectivity"></a>Åtkomst och anslutning

* Åtkomst till Internet så att du kan ladda ned kraven, ansluta från Visual Studio Code till ditt Azure-konto och publicera från Visual Studio Code till Azure, en Docker-container eller en annan miljö.

* Ett Azure-konto och prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Om du vill skapa samma exempellogikapp i den här artikeln behöver du ett Office 365 Outlook-e-postkonto som använder ett Microsoft-arbets- eller skolkonto för att logga in.

  Om du väljer att använda en annan e-postanslutning som stöds av [Azure Logic Apps](/connectors/), till exempel Outlook.com eller [Gmail,](../connectors/connectors-google-data-security-privacy-policy.md)kan du fortfarande följa exemplet och de allmänna övergripande stegen är desamma, men användargränssnittet och alternativen kan variera på vissa sätt. Om du till exempel använder anslutningsappen för Outlook.com använder du din personliga Microsoft-konto för att logga in.

<a name="storage-requirements"></a>

### <a name="storage-requirements"></a>Lagringskrav

#### <a name="windows"></a>Windows

Om du vill skapa och köra logikappsprojektet lokalt i Visual Studio Code när du använder Windows följer du dessa steg för att konfigurera Azure Storage emulatorn:

1. Ladda ned och [installera Azure Storage Emulator 5.10](https://go.microsoft.com/fwlink/p/?linkid=717179).

1. Om du inte redan har en måste du ha en lokal SQL DB-installation, till exempel den [kostnadsfria versionen SQL Server 2019 Express Edition](https://go.microsoft.com/fwlink/p/?linkid=866658), så att emulatorn kan köras.

   Mer information finns i Använda [Azure Storage för utveckling och testning.](../storage/common/storage-use-emulator.md)

1. Innan du kan köra projektet måste du starta emulatorn.

   ![Skärmbild som visar den Azure Storage emulatorn som körs.](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

#### <a name="macos-and-linux"></a>macOS och Linux

Om du vill skapa och köra logikappsprojektet lokalt i Visual Studio Code när du använder macOS eller Linux följer du dessa steg för att skapa och konfigurera ett Azure Storage konto.

> [!NOTE]
> Designern i Visual Studio Code fungerar för närvarande inte i Linux OS, men du kan fortfarande köra skapa, köra och distribuera logikappar som använder Logic Apps Preview-körningen till Linux-baserade virtuella datorer. För tillfället kan du skapa dina logikappar i Visual Studio Code på Windows eller macOS och sedan distribuera till en Linux-baserad virtuell dator.

1. Logga in på [Azure Portal](https://portal.azure.com)och skapa [ett Azure Storage ,](../storage/common/storage-account-create.md?tabs=azure-portal)vilket är en förutsättning för att [Azure Functions](../azure-functions/storage-considerations.md).

1. På lagringskontots meny går du **till Inställningar** och väljer **Åtkomstnycklar.**

1. I fönstret **Åtkomstnycklar** letar du upp och kopierar lagringskontots anslutningssträng, som ser ut ungefär som i det här exemplet:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Skärmbild som visar Azure Portal lagringskontots åtkomstnycklar och anslutningssträngen kopierade.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Mer information finns i Hantera [lagringskontonycklar.](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)

1. Spara anslutningssträngen på en säker plats. När du har skapat logikappsprojektet i Visual Studio Code måste du lägga till strängen **ilocal.settings.jspå** filen i projektets rotnivåmapp.

   > [!IMPORTANT]
   > Om du planerar att distribuera till en Docker-container måste du också använda den här anslutningssträngen med den Docker-fil som du använder för distribution. I produktionsscenarier bör du skydda och skydda sådana hemligheter och känslig information, till exempel med hjälp av ett nyckelvalv.
  
### <a name="tools"></a>Verktyg

* [Visual Studio kod 1.30.1 (januari 2019)](https://code.visualstudio.com/)eller senare, vilket är kostnadsfritt. Ladda även ned och installera dessa verktyg för Visual Studio Code om du inte redan har dem:

  * [Azure-kontotillägget](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account), som ger en gemensam filtreringsupplevelse för Azure-inloggning och -prenumerationer för alla andra Azure-tillägg i Visual Studio Code.

  * [C# för Visual Studio Code som](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)gör att F5-funktioner kan köra logikappen.

  * [Azure Functions Core Tools 3.0.3245](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.3245) eller senare med hjälp av Microsoft Installer-versionen (MSI), som är `func-cli-3.0.3245-x*.msi` .

    Dessa verktyg innehåller en version av samma körning som driver Azure Functions runtime, som förhandsgranskningstillägget använder i Visual Studio Code.

    > [!IMPORTANT]
    > Om du har en installation som är tidigare än dessa versioner avinstallerar du den versionen först eller kontrollerar att miljövariabeln PATH pekar på den version som du laddar ned och installerar.

  * [Azure Logic Apps (förhandsversion) för Visual Studio Code](https://go.microsoft.com/fwlink/p/?linkid=2143167). Det här tillägget ger dig möjlighet att skapa logikappar där du kan skapa tillståndslösa och tillståndslösa arbetsflöden som körs lokalt i Visual Studio Code och sedan distribuerar dessa logikappar direkt till Azure eller till Docker-containrar.

    För närvarande kan du ha både det ursprungliga Azure Logic Apps tillägget och det allmänt tillgängliga förhandsversionstillägget installerat i Visual Studio Code. Även om utvecklingsupplevelserna skiljer sig åt på vissa sätt mellan tilläggen kan din Azure-prenumeration innehålla båda logikapptyperna som du skapar med tilläggen. Visual Studio Code visar alla distribuerade logikappar i din Azure-prenumeration, men ordnar dem i olika avsnitt efter tilläggsnamn, **Logic Apps** **och Azure Logic Apps (förhandsversion).**

    > [!IMPORTANT]
    > Om du har skapat logikappprojekt med det tidigare privata förhandsgranskningstillägget fungerar dessa projekt inte med tillägget för offentlig förhandsversion. Du kan dock migrera dessa projekt när du har avinstallerat det privata förhandsgranskningstillägget, tagit bort de associerade filerna och installerat tillägget för offentlig förhandsversion. Sedan skapar du ett nytt projekt i Visual Studio Code och kopierar logikappens **workflow.definition-fil** som du skapade tidigare till det nya projektet. Mer information finns i Migrera [från det privata förhandsgranskningstillägget](#migrate-private-preview).
    > 
    > Om du har skapat logikappprojekt med det tidigare offentliga förhandsversionstillägget kan du fortsätta att använda dessa projekt utan några migreringssteg.

    **Följ dessa **steg Azure Logic Apps installera tillägget** för Azure Logic Apps (förhandsversion):**

    1. I Visual Studio Code går du till det vänstra verktygsfältet och väljer **Tillägg**.

    1. I sökrutan för tillägg anger du `azure logic apps preview` . I resultatlistan väljer du **Azure Logic Apps (förhandsversion)** **>** **Installera**.

       När installationen är klar visas förhandsgranskningstillägget i **listan Tillägg: Installerade.**

       ![Skärmbild som visar Visual Studio Code-listan över installerade tillägg med tillägget "Azure Logic Apps (förhandsversion)" understruket.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

       > [!TIP]
       > Om tillägget inte visas i listan över installerade kan du prova att starta om Visual Studio Code.

* Om du vill [använda åtgärden Inline Code Operations](../logic-apps/logic-apps-add-run-inline-code.md) som kör JavaScript installerar duNode.js [versionerna 10.x.x, 11.x.x eller 12.x.x.](https://nodejs.org/en/download/releases/)

  > [!TIP] 
  > För Windows laddar du ned MSI-versionen. Om du använder ZIP-versionen i stället måste du manuellt göra Node.js tillgänglig med hjälp av en PATH-miljövariabel för ditt operativsystem.

* Om du vill köra webhook-baserade utlösare och åtgärder lokalt, till exempel den [inbyggda HTTP Webhook-utlösaren](../connectors/connectors-native-webhook.md)i Visual Studio Code, måste du konfigurera vidarebefordran för motringnings-URL:en . [](#webhook-setup)

* Om du vill testa exempellogikappen som du skapar i den här artikeln behöver du ett verktyg som kan skicka anrop till begärandeutlösaren, vilket är det första steget i exempellogikappen. Om du inte har något sådant verktyg kan du ladda ned, installera och använda [Postman.](https://www.postman.com/downloads/)

* Om du skapar din logikapp och distribuerar med inställningar som stöder [Application Insights](../azure-monitor/app/app-insights-overview.md)kan du även aktivera diagnostikloggning och spårning för logikappen. Du kan göra det antingen när du distribuerar logikappen från Visual Studio Code eller efter distributionen. Du måste ha en Application Insights-instans, men du kan skapa den här resursen antingen i [förväg,](../azure-monitor/app/create-workspace-resource.md)när du distribuerar logikappen eller efter distributionen.

<a name="migrate-private-preview"></a>

## <a name="migrate-from-private-preview-extension"></a>Migrera från privat förhandsversionstillägg

Alla logikappprojekt som du har skapat **med Azure Logic Apps -tillägget (privat förhandsversion)** fungerar inte med tillägget för offentlig förhandsversion. Du kan dock migrera dessa projekt till nya projekt genom att följa dessa steg:

1. Avinstallera det privata förhandsgranskningstillägget.

1. Ta bort eventuella associerade tilläggspaket och NuGet-paketmappar på följande platser:

   * Mappen **Microsoft.Azure.Functions.ExtensionBundle.Workflows,** som innehåller tidigare tilläggspaket och finns längs någon av sökvägarna här:

     * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`

     * `C:\Users\{userName}\.azure-functions-core-tools\Functions\ExtensionBundles`

   * Mappen **microsoft.azure.workflows.webjobs.extension,** som är [NuGet-cachen](/nuget/what-is-nuget) för det privata förhandsgranskningstillägget och finns längs den här sökvägen:

     `C:\Users\{userName}\.nuget\packages`

1. Installera **Azure Logic Apps (förhandsversion).**

1. Skapa ett nytt projekt i Visual Studio Code.

1. Kopiera logikappens **workflow.definition-fil som du skapade** tidigare till det nya projektet.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Konfigurera Visual Studio Code

1. Kontrollera att alla tillägg är korrekt installerade genom att läsa in eller starta om Visual Studio Code.

1. Bekräfta att Visual Studio Code automatiskt hittar och installerar tilläggsuppdateringar så att ditt förhandsversionstillägg får de senaste uppdateringarna. Annars måste du avinstallera den inaktuella versionen manuellt och installera den senaste versionen.

   1. På **Arkiv-menyn** går du till **Inställningar** **>** **Inställningar.**

   1. På fliken **Användare** går du till **Funktioner** **>** **Tillägg**.

   1. Kontrollera att **Automatisk kontroll av** uppdateringar och Automatisk **uppdatering** är markerade.

Som standard är följande inställningar också aktiverade och inställda för Logic Apps förhandsversionstillägget:

* **Azure Logic Apps V2: Project Runtime**, som är inställd på version **~3**

  > [!NOTE]
  > Den här versionen krävs för att använda [åtgärderna för infogade kodåtgärder.](../logic-apps/logic-apps-add-run-inline-code.md)

* **Azure Logic Apps V2: Experimental View Manager**, som aktiverar den senaste designern i Visual Studio Code. Om du får problem med designern, till exempel att dra och släppa objekt, inaktiverar du den här inställningen.

Följ dessa steg för att hitta och bekräfta inställningarna:

1. Gå till **Inställningar** på **Arkiv-menyn.** **>** 

1. På fliken **Användare** går du till **>** **Tillägg Azure Logic Apps** **>** **(förhandsversion).**

   Du kan till exempel hitta inställningen **Azure Logic Apps V2: Project Runtime** här eller använda sökrutan för att hitta andra inställningar:

   ![Skärmbild som visar Visual Studio kodinställningar för tillägget "Azure Logic Apps (förhandsversion)".](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Anslut till ditt Azure-konto

1. På Visual Studio Code Activity Bar (Kodaktivitetsfält) väljer du Azure-ikonen.

   ![Skärmbild som visar Visual Studio Code Activity Bar (Kodaktivitetsfält) och vald Azure-ikon.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. I Azure-fönstret, under **Azure: Logic Apps (förhandsversion)** väljer **du Logga in på Azure**. När sidan Visual Studio Code-autentisering visas loggar du in med ditt Azure-konto.

   ![Skärmbild som visar Azure-fönstret och den valda länken för Azure-inloggning.](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   När du har loggat in visar Azure-fönstret prenumerationerna i ditt Azure-konto. Om du även har det offentligt utgivna tillägget hittar du alla logikappar som du har skapat med tillägget i **avsnittet Logic Apps,** inte **avsnittet Logic Apps (förhandsversion).**
   
   Om de förväntade prenumerationerna inte visas, eller om du vill att fönstret endast ska visa specifika prenumerationer, följer du dessa steg:

   1. I prenumerationslistan flyttar du pekaren bredvid den första prenumerationen tills **knappen Välj** prenumerationer (filterikonen) visas. Välj filterikonen.

      ![Skärmbild som visar Azure-fönstret och den valda filterikonen.](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      Eller välj ditt Azure-Visual Studio i statusfältet kod. 

   1. När en annan prenumerationslista visas väljer du de prenumerationer som du vill ha och kontrollerar sedan att du väljer **OK.**

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Skapa ett lokalt projekt

Innan du kan skapa logikappen måste du skapa ett lokalt projekt så att du kan hantera, köra och distribuera logikappen från Visual Studio Code. Det underliggande projektet liknar ett Azure Functions projekt, även kallat ett funktionsappsprojekt. Dessa projekttyper är dock separata från varandra, så logikappar och funktionsappar kan inte finnas i samma projekt.

1. På datorn skapar du en *tom lokal* mapp som ska användas för projektet som du senare skapar i Visual Studio Code.

1. I Visual Studio Code stänger du alla öppna mappar.

1. I Azure-fönstret bredvid **Azure: Logic Apps (förhandsversion)** väljer du **Skapa** nytt projekt (ikon som visar en mapp och blixt).

   ![Skärmbild som visar verktygsfältet i Azure-fönstret med "Skapa nytt projekt" markerat.](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Om Windows Defender-brandväggen uppmanar dig att bevilja nätverksåtkomst för , vilket är Visual Studio Code och för , som är Azure Functions Core Tools, väljer du `Code.exe` `func.exe` Privata **nätverk,** till exempel mitt hem- eller arbetsnätverk **>** **Tillåt åtkomst**.

1. Bläddra till den plats där du skapade projektmappen, välj mappen och fortsätt.

   ![Skärmbild som visar dialogrutan Välj mapp med en nyligen skapad projektmapp och knappen "Välj" markerad.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. I listan med mallar som visas väljer du antingen **Tillståndsful Workflow (Tillståndsful Workflow)** **eller Stateless Workflow (Tillståndslöst arbetsflöde).** I det här exemplet väljer **du Tillståndsful Workflow**.

   ![Skärmbild som visar listan med arbetsflödesmallar med "Tillståndsful Workflow" valt.](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. Ange ett namn för arbetsflödet och tryck på Retur. I det här exemplet `Fabrikam-Stateful-Workflow` används som namn.

   ![Skärmbild som visar rutan "Skapa nytt tillståndslöst arbetsflöde (3/4)" och "Fabrikam-Stateful-Workflow" som arbetsflödesnamn.](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

   Visual Studio Code har skapat projektet och öppnar **filenworkflow.jsför** arbetsflödet i kodredigeraren.

   > [!NOTE]
   > Om du uppmanas att välja hur projektet ska öppnas väljer du **Öppna** i det aktuella fönstret om du vill öppna projektet i det Visual Studio kodfönstret. Om du vill öppna en ny instans Visual Studio Code väljer du **Öppna i nytt fönster.**

1. Från verktygsfältet Visual Studio du Explorer-fönstret, om det inte redan är öppet.

   Explorer-fönstret visar projektet, som nu innehåller automatiskt genererade projektfiler. Projektet har till exempel en mapp som visar arbetsflödets namn. I den här mappen **innehållerworkflow.jspå** filen arbetsflödets underliggande JSON-definition.

   ![Skärmbild som visar Explorer-fönstret med projektmapp, arbetsflödesmapp och "workflow.jspå"-fil.](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

1. Om du använder macOS eller Linux ställer du in åtkomst till ditt lagringskonto genom att följa dessa steg, som krävs för att köra projektet lokalt:

   1. I projektets rotmapp öppnar du filen **local.settings.jsfilen.**

      ![Skärmbild som visar Explorer-fönstret och "local.settings.json"-filen i projektet.](./media/create-stateful-stateless-workflows-visual-studio-code/local-settings-json-files.png)

   1. Ersätt `AzureWebJobsStorage` egenskapsvärdet med lagringskontots anslutningssträng som du sparade tidigare, till exempel:

      Innan:

      ```json
      {
         "IsEncrypted": false,
         "Values": {
            "AzureWebJobsStorage": "UseDevelopmentStorage=true",
            "FUNCTIONS_WORKER_RUNTIME": "dotnet"
          }
      }
      ```

      Efter:

      ```json
      {
         "IsEncrypted": false,
         "Values": {
            "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net",
           "FUNCTIONS_WORKER_RUNTIME": "dotnet"
         }
      }
      ```

      > [!IMPORTANT]
      > I produktionsscenarier bör du skydda och skydda sådana hemligheter och känslig information, till exempel med hjälp av ett nyckelvalv.

   1. När du är klar ser du till att spara ändringarna.

<a name="enable-built-in-connector-authoring"></a>

## <a name="enable-built-in-connector-authoring"></a>Aktivera redigering av inbyggd anslutningsapp

Du kan skapa egna inbyggda anslutningsappar för alla tjänster du behöver med hjälp av [förhandsversionens utökningsramverk](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272). På samma sätt som inbyggda anslutningsappar som Azure Service Bus och SQL Server ger dessa anslutningsappar högre dataflöde, låg svarstid, lokal anslutning och körs inbyggt i samma process som förhandsgranskningskörningen.

Redigeringsfunktionerna är för närvarande endast tillgängliga i Visual Studio Code, men är inte aktiverat som standard. För att skapa de här anslutningsapparna måste du först konvertera projektet från tilläggssamlingsbaserad (Node.js) till NuGet-paketbaserad (.NET).

> [!IMPORTANT]
> Den här åtgärden är en envägsåtgärd som du inte kan ångra.

1. I explorer-fönstret i projektets rot flyttar du muspekaren över ett tomt område under alla andra filer och mappar, öppnar snabbmenyn och väljer Konvertera till **Nuget-baserat logikappsprojekt.**

   ![Skärmbild som visar Explorer-fönstret med projektets snabbmeny öppen från ett tomt område i projektfönstret.](./media/create-stateful-stateless-workflows-visual-studio-code/convert-logic-app-project.png)

1. När frågan visas bekräftar du projektkonverteringen.

1. Om du vill fortsätta granskar du och följer stegen i artikeln Azure Logic Apps Running Anywhere - Built-in connector extensibility ( Kör [överallt – inbyggd anslutningsapps utökningsbarhet).](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-the-designer"></a>Öppna definitionsfilen för arbetsflödet i designern

1. Kontrollera vilka versioner som är installerade på datorn genom att köra det här kommandot:

   `..\Users\{yourUserName}\dotnet --list-sdks`

   Om du har .NET Core SDK 5.x kan den här versionen hindra dig från att öppna logikappens underliggande arbetsflödesdefinition i designern. I stället för att avinstallera den här versionen skapar du i projektets rotmapp en **global.jspå-fil** som refererar till den .NET Core runtime 3.x-version som du har som är senare än 3.1.201, till exempel:

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   > [!IMPORTANT]
   > Se till att du uttryckligenglobal.js **på** filen i projektets rotmapp inifrån Visual Studio Code. Annars öppnas inte designern.

1. Expandera projektmappen för arbetsflödet. Öppnaworkflow.js **på** filens snabbmeny och välj **Öppna i Designer.**

   ![Skärmbild som visar Explorer-fönstret och genvägsfönstret för workflow.jspå filen med "Öppna i Designer" valt.](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

1. I listan **Aktivera anslutningsappar** i Azure väljer du Använd anslutningsappar från **Azure**, som gäller för alla hanterade anslutningsappar som är tillgängliga och distribuerade i Azure, inte bara anslutningsappar för Azure-tjänster.

   ![Skärmbild som visar Explorer-fönstret med listan "Aktivera anslutningsappar i Azure" öppen och "Använd anslutningsappar från Azure" valt.](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > Tillståndslösa arbetsflöden stöder för närvarande *endast* åtgärder [för hanterade anslutningsappar](../connectors/managed.md), som distribueras i Azure och inte utlösare. Även om du har möjlighet att aktivera anslutningsappar i Azure för ditt tillståndslösa arbetsflöde, visar inte designern några hanterade anslutningsutlösare som du kan välja.

1. I listan **Välj prenumeration** väljer du den Azure-prenumeration som ska användas för logikappsprojektet.

   ![Skärmbild som visar Explorer-fönstret med rutan "Välj prenumeration" och din prenumeration vald.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-subscription.png)

1. I listan med resursgrupper väljer du **Skapa ny resursgrupp.**

   ![Skärmbild som visar Explorer-fönstret med listan över resursgrupper och "Skapa ny resursgrupp" valt.](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. Ange ett namn för resursgruppen och tryck på Retur. I det här exemplet används `Fabrikam-Workflows-RG`.

   ![Skärmbild som visar fönstret Utforskaren och rutan resursgruppnamn.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. I listan över platser hittar och väljer du den Azure-region som ska användas när du skapar din resursgrupp och dina resurser. I det här exemplet **används USA, västra centrala.**

   ![Skärmbild som visar Explorer-fönstret med platslistan och "USA, västra centrala" valt.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   När du har utför det här Visual Studio Code arbetsflödesdesignern.

   > [!NOTE]
   > När Visual Studio Code startar API:et för designtid för arbetsflödet kan du få ett meddelande om att starten kan ta några sekunder. Du kan ignorera det här meddelandet eller välja **OK.**
   >
   > Om designern inte öppnas kan du läsa felsökningsavsnittet. [Designern kan inte öppna](#designer-fails-to-open).

   När designern visas visas **uppmaningen Välj** en åtgärd i designern och är markerad som standard, vilket visar **fönstret Lägg till en** åtgärd.

   ![Skärmbild som visar arbetsflödesdesignern.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. Lägg sedan [till en utlösare och åtgärder](#add-trigger-actions) i arbetsflödet.

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Lägga till en utlösare och åtgärder

När du har öppnat designern **visas prompten Välj** en åtgärd i designern och är markerad som standard. Nu kan du börja skapa arbetsflödet genom att lägga till en utlösare och åtgärder.

Arbetsflödet i det här exemplet använder den här utlösaren och följande åtgärder:

* Den inbyggda [begärandeutlösaren](../connectors/connectors-native-reqres.md), , när en **HTTP-begäran** tas emot , som tar emot inkommande anrop eller begäranden och skapar en slutpunkt som andra tjänster eller logikappar kan anropa.

* Office [365 Outlook-åtgärden ,](../connectors/connectors-create-api-office365-outlook.md)Skicka **ett e-postmeddelande**.

* Den inbyggda [svarsåtgärden](../connectors/connectors-native-reqres.md), som du använder för att skicka ett svar och returnera data tillbaka till anroparen.

### <a name="add-the-request-trigger"></a>Lägg till begärandeutlösaren

1. Bredvid designern i  fönstret Lägg till en  utlösare under sökrutan Välj en åtgärd kontrollerar du att Inbyggd är **markerad** så att du kan välja en utlösare som körs inbyggt.

1. I **sökrutan Välj en** åtgärd anger du och väljer den inbyggda begärandeutlösaren med namnet `when a http request` När en **HTTP-begäran tas emot.**

   ![Skärmbild som visar arbetsflödesdesignern och fönstret **Lägg till en utlösare** med utlösaren "När en HTTP-begäran tas emot" markerad.](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   När utlösaren visas i designern öppnas utlösarens informationsfönster för att visa utlösarens egenskaper, inställningar och andra åtgärder.

   ![Skärmbild som visar arbetsflödesdesignern med utlösaren "När en HTTP-begäran tas emot" markerad och fönstret med utlösarinformation är öppet.](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Om informationsfönstret inte visas kontrollerar du att utlösaren är markerad i designern.

1. Om du behöver ta bort ett objekt från designern följer du [dessa steg för att ta bort objekt från designern](#delete-from-designer).

### <a name="add-the-office-365-outlook-action"></a>Lägga till åtgärden Office 365 Outlook

1. Välj Nytt steg under utlösaren som du lade till **i designern.**

   Uppmaningen **Välj en** åtgärd visas i designern och fönstret **Lägg** till en åtgärd öppnas igen så att du kan välja nästa åtgärd.

1. I fönstret **Lägg till** en  åtgärd går du till sökrutan Välj en åtgärd och väljer **Azure** så att du kan hitta och välja en åtgärd för en hanterad anslutningsapp som har distribuerats i Azure.

   Det här exemplet väljer och använder Åtgärden Office 365 Outlook, **Skicka ett e-postmeddelande (V2).**

   ![Skärmbild som visar arbetsflödesdesignern och fönstret **Lägg till en åtgärd** med office 365 Outlook-åtgärden "Skicka ett e-postmeddelande" markerad.](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. I åtgärdens informationsfönster väljer du Logga **in så** att du kan skapa en anslutning till ditt e-postkonto.

   ![Skärmbild som visar arbetsflödesdesignern och fönstret **Skicka ett e-postmeddelande (V2)** med "Logga in" markerat.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

1. När Visual Studio Code uppmanar dig att ge ditt e-postkonto tillstånd väljer du **Öppna.**

   ![Skärmbild som visar Visual Studio code-kommandotolken för att tillåta åtkomst.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > Om du vill förhindra framtida uppmaningar väljer **du Konfigurera betrodda** domäner så att du kan lägga till autentiseringssidan som en betrodd domän.

1. Följ de efterföljande anvisningarna för att logga in, tillåta åtkomst och tillåta att du återgår till Visual Studio Code.

   > [!NOTE]
   > Om det tar för lång tid innan du slutför prompterna går autentiseringsprocessen ut och misslyckas. I det här fallet går du tillbaka till designern och försöker logga in igen för att skapa anslutningen.

1. När tillägget Azure Logic Apps (förhandsversion) uppmanar dig att ge ditt e-postkonto tillstånd väljer du **Öppna.** Följ den efterföljande uppmaningen för att tillåta åtkomst.

   ![Skärmbild som visar uppmaningen om att tillåta åtkomst för förhandsversionstillägget.](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > Om du vill förhindra framtida uppmaningar **väljer du Fråga inte igen för det här tillägget**.

   När Visual Studio Code har skapat anslutningen visar vissa anslutningsappar meddelandet att `The connection will be valid for {n} days only` . Den här tidsgränsen gäller endast varaktigheten när du skapar logikappen i Visual Studio Code. Efter distributionen gäller den här gränsen inte längre eftersom logikappen kan autentisera vid körning med hjälp av dess automatiskt aktiverade [system tilldelade hanterade identitet](../logic-apps/create-managed-service-identity.md). Den här hanterade identiteten skiljer sig från autentiseringsuppgifterna eller anslutningssträngen som du använder när du skapar en anslutning. Om du inaktiverar den här system tilldelade hanterade identiteten fungerar inte anslutningarna vid körning.

1. Om åtgärden Skicka ett **e-postmeddelande** inte visas markerad i designern väljer du den åtgärden.

1. Ange nödvändig information för åtgärden på fliken **Parametrar** i åtgärdens informationsfönster, till exempel:

   ![Skärmbild som visar arbetsflödesdesignern med information om åtgärden "Skicka ett e-postmeddelande" i Office 365 Outlook.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Till** | Yes | <*din e-postadress*> | E-postmottagaren, som kan vara din e-postadress i testsyfte. I det här exemplet används den fiktiva e-postadressen `sophiaowen@fabrikam.com` . |
   | **Ämne** | Yes | `An email from your example workflow` | E-postämnet |
   | **Brödtext** | Yes | `Hello from your example workflow!` | Innehållet i e-postmeddelandet |
   ||||

   > [!NOTE]
   > Om du vill göra några ändringar i informationsfönstret på  fliken **Inställningar,** Statiskt resultat  eller Kör efter ser du till att du väljer Klar för att genomföra ändringarna innan du växlar flikar eller ändrar fokus till designern.  Annars Visual Studio Code inte dina ändringar. Mer information finns på sidan [Logic Apps offentliga förhandsversionen av kända problem i GitHub.](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

1. Välj Spara i **designern.**

> [!IMPORTANT]
> Om du vill köra ett arbetsflöde lokalt som använder en webhook-baserad utlösare eller åtgärder, till exempel den [inbyggda HTTP Webhook-utlösaren](../connectors/connectors-native-webhook.md)eller åtgärden , måste du aktivera den här funktionen genom att konfigurera vidarebefordran för [webhookens](#webhook-setup)återanrops-URL.

<a name="webhook-setup"></a>

## <a name="enable-locally-running-webhooks"></a>Aktivera webhooks som körs lokalt

När du använder en webhook-baserad utlösare eller -åtgärd, till exempel **HTTP-webhook**, med en logikapp som körs i Azure, prenumererar Logic Apps-körningen på tjänstslutpunkten genom att generera och registrera en motringnings-URL med den slutpunkten. Utlösaren eller åtgärden väntar sedan på att tjänstslutpunkten ska anropa URL:en. Men när du arbetar i Visual Studio Code börjar den genererade återanrops-URL:en med `http://localhost:7071/...` . Den här URL:en är för din localhost-server, som är privat så att tjänstslutpunkten inte kan anropa den här URL:en.

Om du vill köra webhook-baserade utlösare och åtgärder lokalt i Visual Studio Code måste du konfigurera en offentlig URL som exponerar din localhost-server och på ett säkert sätt vidarebefordrar anrop från tjänstslutpunkten till webhookens återanrops-URL. Du kan använda en vidarebefordranstjänst och ett verktyg som [**ngrok**](https://ngrok.com/), som öppnar en HTTP-tunnel till din localhost-port, eller så kan du använda ditt eget verktyg.

#### <a name="set-up-call-forwarding-using-ngrok"></a>Konfigurera vidarebefordran av anrop med **ngrok**

1. [Registrera dig för ett **ngrok-konto**](https://dashboard.ngrok.com/signup) om du inte har ett. Annars loggar [du in på ditt konto](https://dashboard.ngrok.com/login).

1. Hämta din personliga autentiseringstoken som **ngrok-klienten** behöver för att ansluta och autentisera åtkomst till ditt konto.

   1. Du hittar sidan [med autentiseringstoken](https://dashboard.ngrok.com/auth/your-authtoken)genom att gå till menyn på instrumentpanelen för ditt **konto,** expandera Autentisering och välja **Din Autentiseringtoken.**

   1. I rutan **Your Authtoken** kopierar du token till en säker plats.

1. Från [ **nedladdningssidan för ngrok**](https://ngrok.com/download) eller instrumentpanelen för ditt konto [laddar](https://dashboard.ngrok.com/get-started/setup)du ned **den ngrok-version** som du vill ha och extraherar .zip-filen. Mer information finns i [Steg 1: Packa upp för att installera](https://ngrok.com/download).

1. Öppna kommandotolken på datorn. Bläddra till den plats där du har **ngrok.exe** fil.

1. Anslut **ngrok-klienten** till **ditt ngrok-konto** genom att köra följande kommando. Mer information finns i [Steg 2: Anslut ditt konto.](https://ngrok.com/download)

   `ngrok authtoken <your_auth_token>`

1. Öppna HTTP-tunneln till localhost-port 7071 genom att köra följande kommando. Mer information finns i [Steg 3: Öppna den.](https://ngrok.com/download)

   `ngrok http 7071`

1. Leta reda på följande rad i utdata:

   `http://<domain>.ngrok.io -> http://localhost:7071`

1. Kopiera och spara den URL som har det här formatet: `http://<domain>.ngrok.io`

#### <a name="set-up-the-forwarding-url-in-your-app-settings"></a>Konfigurera vidarebefordran av URL:en i appinställningarna

1. I Visual Studio Code går du till designern och lägger till **HTTP + Webhook-utlösaren** eller åtgärden.

1. När frågan visas för platsen för värdslutpunkten anger du url:en för vidarebefordran (omdirigering) som du skapade tidigare.

   > [!NOTE]
   > Om du ignorerar uppmaningen visas en varning om att du måste ange vidarespolnings-URL:en, så välj **Konfigurera** och ange webbadressen. När du är klar med det här steget visas inte frågan igen för efterföljande webhook-utlösare eller åtgärder som du kan lägga till.
   >
   > Om du vill att prompten ska visas igen öppnar du på **projektets rotnivålocal.settings.js** på filens snabbmeny och väljer **Konfigurera webhookens omdirigeringsslutpunkt.** Uppmaningen visas nu så att du kan ange vidare vidarebefordrande-URL:en.

   Visual Studio Code lägger till url:en för **vidarebefordranlocal.settings.jsi** filen i projektets rotmapp. I `Values` -objektet visas egenskapen med `Workflows.WebhookRedirectHostUri` namnet nu och är inställd på vidarespolnings-URL:en, till exempel:
   
   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "node",
         "FUNCTIONS_V2_COMPATIBILITY_MODE": "true",
         <...>
         "Workflows.WebhookRedirectHostUri": "http://xxxXXXXxxxXXX.ngrok.io",
         <...>
      }
   }
   ```

Första gången du startar en lokal felsökningssession eller kör arbetsflödet utan felsökning registrerar Logic Apps-körningen arbetsflödet med tjänstslutpunkten och prenumererar på den slutpunkten för att meddela webhook-åtgärderna. Nästa gång arbetsflödet körs registreras inte körningen eller återprenumerationen eftersom prenumerationsregistreringen redan finns i lokal lagring.

När du stoppar felsökningssessionen för en arbetsflödeskörning som använder webhook-baserade utlösare eller åtgärder lokalt tas inte befintliga prenumerationsregistreringar bort. Om du vill avregistrera dig måste du manuellt ta bort eller ta bort prenumerationsregistreringarna.

> [!NOTE]
> När arbetsflödet börjar köras kan terminalfönstret visa fel som i det här exemplet:
>
> `message='Http request failed with unhandled exception of type 'InvalidOperationException' and message: 'System.InvalidOperationException: Synchronous operations are disallowed. Call ReadAsync or set AllowSynchronousIO to true instead.`
>
> I det här fallet öppnar **local.settings.jsfilen** i projektets rotmapp och kontrollerar att egenskapen är inställd på `true` :
>
> `"FUNCTIONS_V2_COMPATIBILITY_MODE": "true"`

<a name="manage-breakpoints"></a>

## <a name="manage-breakpoints-for-debugging"></a>Hantera brytpunkter för felsökning

Innan du kör och testar logikappens arbetsflöde genom att starta en [felsökningssession](https://code.visualstudio.com/docs/editor/debugging#_breakpoints) kan du ange brytpunkter iworkflow.js **på** filen för varje arbetsflöde. Ingen annan installation krävs. 

För närvarande stöds brytpunkter endast för åtgärder, inte utlösare. Varje åtgärdsdefinition har dessa brytpunktsplatser:

* Ange startbrytpunkten på raden som visar åtgärdens namn. När brytpunkten når under felsökningssessionen kan du granska åtgärdens indata innan de utvärderas.

* Ange den sista brytpunkten på den rad som visar åtgärdens avslutande kparentes (**}**). När den här brytpunkten når under felsökningssessionen kan du granska åtgärdens resultat innan åtgärden har körts klart.

Följ dessa steg om du vill lägga till en brytpunkt:

1. Öppna filen **workflow.js** för det arbetsflöde som du vill felsöka.

1. Välj inuti den kolumnen i den vänstra kolumnen på den rad där du vill ange brytpunkten. Om du vill ta bort brytpunkten väljer du brytpunkten.

   När du startar felsökningssessionen visas körningsvyn till vänster i kodfönstret, medan verktygsfältet Felsökning visas längst upp.

   > [!NOTE]
   > Om vyn Kör inte visas automatiskt trycker du på Ctrl+Skift+D.

1. Om du vill granska den tillgängliga informationen när en brytpunkt når går du till vyn Kör och **undersöker fönstret** Variabler.

1. Om du vill fortsätta att utföra arbetsflödet går du till verktygsfältet Felsöka och **väljer Fortsätt** (uppspelningsknappen).

Du kan lägga till och ta bort brytpunkter när som helst under arbetsflödeskörningen. Men om du uppdaterar **workflow.jspå** filen när körningen startar uppdateras inte brytpunkter automatiskt. Starta om logikappen för att uppdatera brytpunkterna.

Allmän information finns i [Brytpunkter – Visual Studio Kod](https://code.visualstudio.com/docs/editor/debugging#_breakpoints).

<a name="run-test-debug-locally"></a>

## <a name="run-test-and-debug-locally"></a>Köra, testa och felsöka lokalt

Testa logikappen genom att följa dessa steg för att starta en felsökningssession och hitta URL:en för slutpunkten som skapas av begärandeutlösaren. Du behöver den här URL:en så att du senare kan skicka en begäran till den slutpunkten.

1. Om du vill felsöka ett tillståndslöst arbetsflöde enklare kan du [aktivera körningshistoriken för arbetsflödet](#enable-run-history-stateless).

1. På Visual Studio Code-aktivitetsfältet öppnar du **menyn Run** (Kör) och väljer Start **Debugging** (F5).

   **Terminalfönstret** öppnas så att du kan granska felsökningssessionen.

   > [!NOTE]
   > Om du får felmeddelandet "Felet finns efter **körningen preLaunchTask 'generateDebugSymbols'"** finns i felsökningsavsnittet Felsökningssessionen kan inte [starta](#debugging-fails-to-start).

1. Leta nu reda på motringningens URL för slutpunkten på begärandeutlösaren.

   1. Öppna Explorer-fönstret igen så att du kan visa projektet.

   1. Frånworkflow.js **på** filens snabbmeny väljer du **Översikt.**

      ![Skärmbild som visar Explorer-fönstret och genvägsfönstret för workflow.jsfil med "Översikt" valt.](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. Leta reda **på värdet för motringning-URL,** som ser ut ungefär som den här URL:en för exemplet Begärandeutlösare:

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![Skärmbild som visar arbetsflödets översiktssida med återanrops-URL](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. Om du vill testa motringnings-URL:en genom att utlösa logikappens arbetsflöde öppnar du [Postman](https://www.postman.com/downloads/) eller önskat verktyg för att skapa och skicka begäranden.

   Det här exemplet fortsätter med postman. Mer information finns i [Postman Komma igång](https://learning.postman.com/docs/getting-started/introduction/).

   1. Välj Nytt i **Postman-verktygsfältet.**

      ![Skärmbild som visar Postman med knappen Ny markerad](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. I fönstret **Skapa ny** går du till **Byggstenar och** väljer **Begär.**

   1. I fönstret **Spara begäran,** under **Begäransnamn,** anger du ett namn för begäran, till exempel `Test workflow trigger` .

   1. Under **Välj en samling eller mapp som du vill spara till** väljer du Skapa **samling.**

   1. Under **Alla samlingar** anger du ett namn för samlingen som ska skapas för att organisera dina begäranden, trycker på Retur och väljer Spara för att <***samlingsnamnet* >**. I det här exemplet `Logic Apps requests` används som samlingsnamn.

      Postman-fönstret för begäran öppnas så att du kan skicka en begäran till motringning-URL:en för begärandeutlösaren.

      ![Skärmbild som visar Postman med fönstret öppnad begäran](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Gå tillbaka till Visual Studio Code. från arbetsflödets översiktssida kopierar du egenskapsvärdet **för motringning-URL.**

   1. Gå tillbaka till Postman. I fönstret begäran bredvid metodlistan, som för närvarande visar **GET** som standardmetod för begäran, klistrar du in motringnings-URL:en som du tidigare kopierade i adressrutan och **väljer Skicka**.

      ![Skärmbild som visar Postman och motringnings-URL i adressrutan med knappen Skicka markerad](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      Logikappens exempelarbetsflöde skickar ett e-postmeddelande som liknar det här exemplet:

      ![Skärmbild som visar e-post i Outlook enligt beskrivningen i exemplet](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. I Visual Studio Code går du tillbaka till arbetsflödets översiktssida.

   Om du har skapat ett tillståndslöst arbetsflöde visas arbetsflödets körningsstatus och historik efter den begäran som du skickade utlöste arbetsflödet.

   > [!TIP]
   > Om körningsstatusen inte visas kan du försöka uppdatera översiktssidan genom att välja **Uppdatera**. Ingen körning sker för en utlösare som hoppas över på grund av ej använda kriterier eller att inga data har hittats.

   ![Skärmbild som visar arbetsflödets översiktssida med körningsstatus och historik](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   | Körningsstatus | Description |
   |------------|-------------|
   | **Avbruten** | Körningen stoppades eller slutförs inte på grund av externa problem, till exempel ett systemavbrott eller en bortställd Azure-prenumeration. |
   | **Annullerat** | Körningen utlöstes och startades men tog emot en begäran om annullering. |
   | **Misslyckades** | Minst en åtgärd i körningen misslyckades. Inga efterföljande åtgärder i arbetsflödet har ställts in för att hantera felet. |
   | **Körs** | Körningen utlöstes och pågår, men den här statusen kan också [](logic-apps-limits-and-config.md) visas för en körning som begränsas på grund av åtgärdsgränser eller [den aktuella prisplanen](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Tips:** Om du ställer in [diagnostikloggning](monitor-logic-apps-log-analytics.md)kan du få information om eventuella begränsningshändelser som inträffar. |
   | **Lyckades** | Körningen lyckades. Om någon åtgärd misslyckades hanterade en efterföljande åtgärd i arbetsflödet felet. |
   | **Time out** | Tidsgränsen för körningen överskreds eftersom den aktuella varaktigheten överskred körningens varaktighetsgräns, vilket styrs av inställningen [ **Kvarhållning av körningshistorik i** dagar.](logic-apps-limits-and-config.md#run-duration-retention-limits) En körnings varaktighet beräknas med hjälp av körningens starttid och körningens varaktighetsgräns vid den starttiden. <p><p>**Obs!** Om körningens varaktighet också överskrider kvarhållningsgränsen för den aktuella körningshistoriken *,* som också styrs av inställningen Kvarhållning av körningshistorik i [  dagar,](logic-apps-limits-and-config.md#run-duration-retention-limits)rensas körningen från körningshistoriken av ett dagligt rensningsjobb. Oavsett om tidsgränsen för körningen har slutförts eller inte beräknas kvarhållningsperioden alltid med hjälp av körningens starttid och den *aktuella kvarhållningsgränsen.* Så om du minskar varaktighetsgränsen för en körning som körs under flygning går tidsgränsen ut. Körningen stannar dock kvar eller rensas från körningshistoriken baserat på om körningens varaktighet överskred kvarhållningsgränsen. |
   | **Väntar** | Körningen har inte startat eller har pausats, till exempel på grund av en tidigare arbetsflödesinstans som fortfarande körs. |
   |||

1. Om du vill granska statusen för varje steg i en viss körning och stegets indata och utdata väljer du ellipsknappen (**...**) för körningen och väljer **Visa kör.**

   ![Skärmbild som visar raden för arbetsflödets körningshistorik med ellipsknappen och "Visa körning" valt](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code öppnar övervakningsvyn och visar status för varje steg i körningen.

   ![Skärmbild som visar varje steg i arbetsflödeskörningen och deras status](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-action-status.png)

   > [!NOTE]
   > Om en körning misslyckades och ett steg i övervakningsvyn visar felet kan det här problemet uppstå från ett längre utlösarnamn eller åtgärdsnamn som gör att den underliggande `400 Bad Request` Uniform Resource Identifier (URI) överskrider standardteckengränsen. Mer information finns i ["400 Bad Request" (Felaktig begäran 400).](#400-bad-request)

   Här är de möjliga statusar som varje steg i arbetsflödet kan ha:

   | Åtgärdsstatus | Ikon | Description |
   |---------------|------|-------------|
   | **Avbruten** | ![Ikon för åtgärdsstatusen "Avbruten"][aborted-icon] | Åtgärden stoppades eller har inte avslutats på grund av externa problem, till exempel ett systemavbrott eller en stoppad Azure-prenumeration. |
   | **Annullerat** | ![Ikon för åtgärdsstatusen "Avbruten"][cancelled-icon] | Åtgärden kördes men tog emot en begäran om att avbryta. |
   | **Misslyckades** | ![Ikon för åtgärdsstatusen "Misslyckades"][failed-icon] | Åtgärden misslyckades. |
   | **Körs** | ![Ikon för åtgärdsstatusen "Körs"][running-icon] | Åtgärden körs för närvarande. |
   | **Överhoppad** | ![Ikon för åtgärdsstatusen "Överhoppad"][skipped-icon] | Åtgärden hoppas över eftersom den omedelbart föregående åtgärden misslyckades. En åtgärd har `runAfter` ett villkor som kräver att föregående åtgärd slutförs korrekt innan den aktuella åtgärden kan köras. |
   | **Lyckades** | ![Ikon för åtgärdsstatusen "Lyckades"][succeeded-icon] | Åtgärden lyckades. |
   | **Lyckades med återförsök** | ![Ikon för åtgärdsstatusen "Lyckades med återförsök"][succeeded-with-retries-icon] | Åtgärden lyckades men bara efter ett eller flera återförsök. Om du vill granska historiken för återförsök väljer du åtgärden i vyn körningshistorikinformation så att du kan visa indata och utdata. |
   | **Time out** | ![Ikon för åtgärdsstatusen "Time out"][timed-out-icon] | Åtgärden stoppades på grund av den tidsgräns som anges av den åtgärdens inställningar. |
   | **Väntar** | ![Ikon för åtgärdsstatusen "Väntar"][waiting-icon] | Gäller för en webhook-åtgärd som väntar på en inkommande begäran från en anropare. |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/waiting.png

1. Om du vill granska indata och utdata för varje steg väljer du det steg som du vill granska.

   ![Skärmbild som visar status för varje steg i arbetsflödet plus indata och utdata i den expanderade åtgärden "Skicka ett e-postmeddelande"](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. Om du vill granska råa indata och utdata för det steget ytterligare väljer du **Visa råa indata** eller **Visa råutdata**.

1. Om du vill stoppa felsökningssessionen går du **till menyn** Kör och väljer **Stoppa felsökning** (Skift + F5).

<a name="return-response"></a>

## <a name="return-a-response"></a>Returnera ett svar

Om du vill returnera ett svar till anroparen som skickade en begäran till logikappen kan du använda den [inbyggda](../connectors/connectors-native-reqres.md) svarsåtgärden för ett arbetsflöde som börjar med begärandeutlösaren.

1. I arbetsflödesdesignern går du till **åtgärden Skicka ett e-postmeddelande** och väljer **Nytt steg.**

   Uppmaningen **Välj en** åtgärd visas i designern och fönstret **Lägg** till en åtgärd öppnas igen så att du kan välja nästa åtgärd.

1. I fönstret **Lägg till en** åtgärd under **sökrutan Välj en** åtgärd kontrollerar du att **Inbyggd** har valts. I sökrutan anger du `response` och väljer **åtgärden** Svar.

   ![Skärmbild som visar arbetsflödesdesignern med åtgärden Svar vald.](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   När **åtgärden** Svar visas i designern öppnas åtgärdens informationsfönster automatiskt.

   ![Skärmbild som visar arbetsflödesdesignern med informationsfönstret för åtgärden "Svar" öppet och egenskapen "Brödtext" inställd på egenskapsvärdet "Skicka ett e-postmeddelande" för åtgärden "Brödtext".](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. På fliken **Parametrar** anger du nödvändig information för den funktion som du vill anropa.

   Det här exemplet returnerar **egenskapsvärdet** Brödtext som är utdata från **åtgärden Skicka ett e-postmeddelande.**

   1. Klicka i **egenskapsrutan** Brödtext så att listan med dynamiskt innehåll visas och visar tillgängliga utdatavärden från den föregående utlösaren och åtgärderna i arbetsflödet.

      ![Skärmbild som visar informationsfönstret för åtgärden "Svar" med muspekaren inuti egenskapen "Brödtext" så att listan med dynamiskt innehåll visas.](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. I listan med dynamiskt innehåll går du till **Skicka ett e-postmeddelande och** väljer **Brödtext.**

      ![Skärmbild som visar den öppna listan med dynamiskt innehåll. I listan under rubriken "Skicka ett e-postmeddelande" väljs utdatavärdet "Brödtext".](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      När du är klar är egenskapen  Brödtext för åtgärdsåtgärden Brödtext nu inställd på utdatavärdet för **åtgärden** Skicka ett e-postmeddelande. 

      ![Skärmbild som visar status för varje steg i arbetsflödet plus indata och utdata i den expanderade åtgärden "Svar".](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. Välj Spara i **designern.**

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>Testa logikappen igen

När du har uppdaterat logikappen kan du köra ett nytt test genom att köra felsökaren i Visual Studio igen och skicka en annan begäran för att utlösa den uppdaterade logikappen, ungefär som stegen i [Kör, testa och felsöka lokalt.](#run-test-debug-locally)

1. På Visual Studio Code-aktivitetsfältet öppnar du **menyn Run** (Kör) och väljer Start **Debugging** (F5).

1. Skicka en ny begäran för att utlösa arbetsflödet i Postman eller ditt verktyg för att skapa och skicka begäranden.

1. Om du har skapat ett tillståndslöst arbetsflöde kontrollerar du statusen för den senaste körningen på arbetsflödets översiktssida. Om du vill visa status, indata och utdata för varje steg i körningen väljer du ellipsknappen (**...**) för körningen och väljer **Visa körning.**

   Här är till exempel den stegvisa statusen för en körning efter att exempelarbetsflödet har uppdaterats med åtgärden Svar.

   ![Skärmbild som visar status för varje steg i det uppdaterade arbetsflödet plus indata och utdata i den expanderade åtgärden "Svar".](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. Om du vill stoppa felsökningssessionen går du **till menyn Kör** och väljer Stoppa **felsökning** (Skift + F5).

<a name="firewall-setup"></a>

##  <a name="find-domain-names-for-firewall-access"></a>Hitta domännamn för brandväggsåtkomst

Innan du distribuerar och kör logikappens arbetsflöde i Azure Portal måste du, om din miljö har strikta nätverkskrav eller brandväggar som begränsar trafiken, konfigurera behörigheter för alla utlösare eller åtgärdsanslutningar som finns i arbetsflödet.

Följ dessa steg om du vill hitta fullständigt kvalificerade domännamn (FQDN) för dessa anslutningar:

1. I logikappsprojektet öppnar du **filenconnections.js** som skapas när du har lagt till den första anslutningsbaserade utlösaren eller åtgärden i arbetsflödet och hittar `managedApiConnections` objektet.

1. För varje anslutning som du har skapat söker du efter, kopierar och sparar egenskapsvärdet på en säker plats så att du kan `connectionRuntimeUrl` konfigurera brandväggen med den här informationen.

   Det här **connections.jsfil** innehåller två anslutningar, en AS2-anslutning och en Office 365-anslutning med följande `connectionRuntimeUrl` värden:

   * AS2: `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba`

   * Office 365: `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f`

   ```json
   {
      "managedApiConnections": {
         "as2": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/as2"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         },
         "office365": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/office365"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         }
      }
   }
   ```

<a name="deploy-azure"></a>

## <a name="deploy-to-azure"></a>Distribuera till Azure

Från Visual Studio Code kan du publicera projektet direkt till Azure, som distribuerar logikappen med hjälp av den nya **resurstypen Logikapp (förhandsversion).** Precis som med funktionsappresursen i Azure Functions kräver distributionen för den här nya resurstypen att du väljer en [värdplan](../app-service/overview-hosting-plans.md)och prisnivå som du kan konfigurera under distributionen. Mer information om värdplaner och priser finns i följande avsnitt:

* [Skala upp en i Azure App Service](../app-service/manage-scale-up.md)
* [Skala och var värd i Azure Functions](../azure-functions/functions-scale.md)

Du kan publicera logikappen som en ny resurs, som automatiskt skapar nödvändiga resurser, till exempel ett Azure Storage-konto, på ett sätt [som liknar funktionsappens krav.](../azure-functions/storage-considerations.md) Eller så kan du publicera logikappen till en tidigare distribuerad **logikappresurs (förhandsversion)** som skriver över den logikappen.

### <a name="publish-to-a-new-logic-app-preview-resource"></a>Publicera till en ny logikappresurs (förhandsversion)

1. På Visual Studio Code Activity Bar (Kodaktivitetsfält) väljer du Azure-ikonen.

1. I verktygsfältet **i fönstret Azure: Logic Apps (förhandsversion)** väljer du **Distribuera till logikapp.**

   ![Skärmbild som visar fönstret "Azure: Logic Apps (förhandsversion)" och fönstrets verktygsfält med "Distribuera till logikapp" markerat.](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. Om du uppmanas väljer du den Azure-prenumeration som ska användas för distributionen av logikappen.

1. I listan som Visual Studio Code väljer du bland följande alternativ:

   * **Skapa ny logikapp (förhandsversion) i Azure** (snabb)
   * **Skapa ny logikapp (förhandsversion) i Azure Advanced**
   * En tidigare distribuerad **logikappresurs (förhandsversion),** om det finns någon

   Det här exemplet fortsätter **med Skapa ny logikapp (förhandsversion) i Azure Advanced**.

   ![Skärmbild som visar fönstret "Azure: Logic Apps (förhandsversion)" med en lista med "Skapa ny logikapp (förhandsversion) i Azure" valt.](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. Följ dessa steg om **du vill skapa en ny logikappresurs (förhandsversion):**

   1. Ange ett globalt unikt namn för din nya logikapp, vilket är det namn som ska användas för **logikappresursen (förhandsversion).** I det här exemplet används `Fabrikam-Workflows-App`.

      ![Skärmbild som visar fönstret "Azure: Logic Apps (förhandsversion)" och en uppmaning om att ange ett namn för den nya logikapp som ska skapas.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. Välj en [värdplan](../app-service/overview-hosting-plans.md) för din nya logikapp, [ **antingen App Service Plan** (Dedikerad)](../azure-functions/dedicated-plan.md) eller [**Premium.**](../azure-functions/functions-premium-plan.md)

      > [!IMPORTANT]
      > Förbrukningsplaner stöds inte eller är inte tillgängliga för den här resurstypen. Den valda planen påverkar de funktioner och prisnivåer som senare är tillgängliga för dig. Mer information finns i följande avsnitt: 
      >
      > * [Skala och var värd i Azure Functions](../azure-functions/functions-scale.md)
      > * [App Service prisinformation](https://azure.microsoft.com/pricing/details/app-service/)
      >
      > Premium-planen ger till exempel åtkomst till nätverksfunktioner, till exempel att ansluta och integrera privat med virtuella Azure-nätverk, på samma sätt som Azure Functions när du skapar och distribuerar dina logikappar. 
      > Mer information finns i följande avsnitt:
      > 
      > * [Nätverksalternativ för Azure Functions](../azure-functions/functions-networking-options.md)
      > * [Azure Logic Apps var som helst – nätverksmöjligheter med Azure Logic Apps förhandsversion](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

      I det här exemplet **används App Service Plan**.

      ![Skärmbild som visar fönstret "Azure: Logic Apps (förhandsversion)" och en uppmaning om att välja "App Service Plan" eller "Premium".](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. Skapa en ny App Service plan eller välj en befintlig plan. I det här exemplet **väljer du Skapa App Service Plan**.

      ![Skärmbild som visar fönstret "Azure: Logic Apps (förhandsversion)" och en uppmaning om att skapa en ny App Service-plan eller välja en befintlig App Service plan.](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. Ange ett namn för App Service plan och välj sedan [en prisnivå](../app-service/overview-hosting-plans.md) för planen. I det här exemplet väljer du **den kostnadsfria F1-planen.**

      ![Skärmbild som visar fönstret "Azure: Logic Apps (förhandsversion)" och en uppmaning om att välja en prisnivå.](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. För optimala prestanda hittar och väljer du samma resursgrupp som projektet för distributionen.

      > [!NOTE]
      > Även om du kan skapa eller använda en annan resursgrupp kan det påverka prestandan. Om du skapar eller väljer en annan resursgrupp, men avbryter när bekräftelsemeddelandet visas, avbryts även distributionen.

   1. För tillståndsfulla arbetsflöden väljer du **Skapa nytt lagringskonto** eller ett befintligt lagringskonto.

      ![Skärmbild som visar fönstret "Azure: Logic Apps (förhandsversion)" och en uppmaning om att skapa eller välja ett lagringskonto.](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. Om logikappens inställningar för skapande och distribution stöder användning [Application Insights](../azure-monitor/app/app-insights-overview.md)kan du även aktivera diagnostikloggning och spårning för logikappen. Du kan göra det antingen när du distribuerar logikappen från Visual Studio Code eller efter distributionen. Du måste ha en Application Insights instans, men du kan skapa den här resursen antingen i förväg [,](../azure-monitor/app/create-workspace-resource.md)när du distribuerar logikappen eller efter distributionen.

      Följ dessa steg om du vill aktivera loggning och spårning nu:

      1. Välj antingen en Application Insights resurs eller **Skapa Application Insights resurs**.

      1. I [Azure Portal](https://portal.azure.com)går du till Application Insights resurs.

      1. På resursmenyn väljer du **Översikt.** Leta upp och kopiera **värdet för instrumenteringsnyckeln.**

      1. I Visual Studio Code går du till projektets rotmapp och öppnar **local.settings.jspå** filen.

      1. I `Values` -objektet lägger du `APPINSIGHTS_INSTRUMENTATIONKEY` till egenskapen och anger värdet till instrumenteringsnyckeln, till exempel:

         ```json
         {
            "IsEncrypted": false,
            "Values": {
               "AzureWebJobsStorage": "UseDevelopmentStorage=true",
               "FUNCTIONS_WORKER_RUNTIME": "dotnet",
               "APPINSIGHTS_INSTRUMENTATIONKEY": <instrumentation-key>
            }
         }
         ```

         > [!TIP]
         > Du kan kontrollera om utlösaren och åtgärdsnamnen visas korrekt i Application Insights instansen.
         >
         > 1. I Azure Portal du till din Application Insights resurs.
         >
         > 2. På resursresursmenyn under Undersök **väljer** du **Programkarta**.
         >
         > 3. Granska åtgärdsnamnen som visas på kartan.
         >
         > Vissa inkommande begäranden från inbyggda utlösare kan visas som dubbletter i programkartan. 
         > I stället för att `WorkflowName.ActionName` använda formatet använder dessa dubbletter arbetsflödesnamnet som åtgärdsnamn och kommer från Azure Functions värden.

      1. Därefter kan du justera allvarlighetsgraden för spårningsdata som logikappen samlar in och skickar till din Application Insights instans.

         Varje gång en arbetsflödesrelaterad händelse inträffar, till exempel när ett arbetsflöde utlöses eller när en åtgärd körs, skickar körningen olika spårningar. Dessa spårningar omfattar arbetsflödets livslängd och omfattar, men är inte begränsade till, följande händelsetyper:

         * Tjänstaktivitet, till exempel start, stopp och fel.
         * Jobb- och dispatcher-aktivitet.
         * Arbetsflödesaktivitet, till exempel utlösare, åtgärd och körning.
         * Aktivitet för lagringsbegäran, till exempel lyckad eller misslyckad.
         * HTTP-begäransaktivitet, till exempel inkommande, utgående, lyckad och misslyckad.
         * Alla utvecklingsspårningar, till exempel felsökningsmeddelanden.

         Varje händelsetyp tilldelas en allvarlighetsgrad. Nivån samlar till exempel in de mest detaljerade meddelandena, medan nivån samlar in allmän aktivitet i arbetsflödet, till exempel när logikappen, arbetsflödet, utlösaren och `Trace` `Information` åtgärderna startar och stoppar. I den här tabellen beskrivs allvarlighetsgraderna och deras spårningstyper:

         | Allvarlighetsgrad | Spårningstyp |
         |----------------|------------|
         | Kritiskt | Loggar som beskriver ett oåterkalleligt fel i logikappen. |
         | Felsöka | Loggar som du kan använda för undersökning under utvecklingen, till exempel inkommande och utgående HTTP-anrop. |
         | Fel | Loggar som indikerar ett fel i arbetsflödeskörningen, men inte ett allmänt fel i logikappen. |
         | Information | Loggar som spårar den allmänna aktiviteten i logikappen eller arbetsflödet, till exempel: <p><p>– När en utlösare, åtgärd eller körning startar och slutar. <br>– När logikappen startar eller slutar. |
         | Spårning | Loggar som innehåller de mest detaljerade meddelandena, till exempel lagringsbegäranden eller dispatcher-aktivitet, plus alla meddelanden som är relaterade till körningsaktiviteten för arbetsflöden. |
         | Varning | Loggar som markerar ett onormalt tillstånd i logikappen men som inte förhindrar att det körs. |
         |||

         Om du vill ange allvarlighetsgrad öppnar du filen på **projektets rotnivåhost.js** filen och hittar `logging` objektet. Det här objektet styr loggfiltreringen för alla arbetsflöden i logikappen och [följer ASP.NET Core-layouten för loggtypsfiltrering.](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&preserve-view=true#log-filtering)

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               }
            }
         }
         ```

         Om objektet `logging` inte innehåller något objekt som innehåller egenskapen lägger du till dessa `logLevel` `Host.Triggers.Workflow` objekt. Ange egenskapen till allvarlighetsgrad för den spårningstyp som du vill ha, till exempel:

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               },
               "logLevel": {
                  "Host.Triggers.Workflow": "Information"
               }
            }
         }
         ```

   När du är klar med distributionsstegen börjar Visual Studio Code att skapa och distribuera de resurser som krävs för att publicera logikappen.

1. Om du vill granska och övervaka distributionsprocessen går du till **menyn Visa** och väljer **Utdata.** I verktygsfältslistan Utdatafönster väljer **du Azure Logic Apps**.

   ![Skärmbild som visar fönstret Utdata med "Azure Logic Apps" valt i verktygsfältets lista tillsammans med distributionsförloppet och statusen.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   När Visual Studio Code har distribuerat logikappen till Azure visas följande meddelande:

   ![Skärmbild som visar ett meddelande om att distributionen till Azure har slutförts.](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   Grattis, logikappen är nu live i Azure och aktiverad som standard.

Därefter kan du lära dig hur du utför dessa uppgifter:

* [Lägg till ett tomt arbetsflöde i projektet](#add-workflow-existing-project).

* [Hantera distribuerade logikappar i Visual Studio Code](#manage-deployed-apps-vs-code) eller med hjälp av [Azure Portal](#manage-deployed-apps-portal).

* [Aktivera körningshistorik för tillståndslösa arbetsflöden](#enable-run-history-stateless).

* [Aktivera övervakningsvyn i Azure Portal för en distribuerad logikapp](#enable-monitoring).

<a name="add-workflow-existing-project"></a>

## <a name="add-blank-workflow-to-project"></a>Lägga till tomt arbetsflöde i projektet

Du kan ha flera arbetsflöden i logikappsprojektet. Följ dessa steg om du vill lägga till ett tomt arbetsflöde i projektet:

1. I aktivitetsfältet Visual Studio kod väljer du Azure-ikonen.

1. I Azure-fönstret bredvid **Azure: Logic Apps (förhandsversion)** väljer du **Skapa arbetsflöde** (ikon för Azure Logic Apps).

1. Välj den arbetsflödestyp som du vill lägga till: **Tillståndsful (tillståndsful)** **eller Stateless (Tillståndslös)**

1. Ange ett namn för arbetsflödet.

När du är klar visas en ny arbetsflödesmapp i projektet tillsammans med en **workflow.jspå** fil för arbetsflödesdefinitionen.

<a name="manage-deployed-apps-vs-code"></a>

## <a name="manage-deployed-logic-apps-in-visual-studio-code"></a>Hantera distribuerade logikappar i Visual Studio Code

I Visual Studio Code kan du visa alla distribuerade logikappar i din Azure-prenumeration, oavsett om de är den ursprungliga **resurstypen Logic Apps** eller logikappen **(förhandsversion)** och välja uppgifter som hjälper dig att hantera dessa logikappar. För att komma åt båda resurstyperna behöver du dock både **Azure Logic Apps** och **Azure Logic Apps (förhandsversion)** för Visual Studio Code.

1. Välj Azure-ikonen i det vänstra verktygsfältet. I fönstret **Azure: Logic Apps (förhandsversion)** expanderar du din prenumeration, som visar alla distribuerade logikappar för den prenumerationen.

1. Öppna logikappen som du vill hantera. På logikappens snabbmeny väljer du den uppgift som du vill utföra.

   Du kan till exempel välja uppgifter som att stoppa, starta, starta om eller ta bort din distribuerade logikapp.

   ![Skärmbild som visar Visual Studio Code med det öppna tilläggsfönstret "Azure Logic Apps (förhandsversion)" och det distribuerade arbetsflödet.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. Om du vill visa alla arbetsflöden i logikappen expanderar du logikappen och expanderar sedan **noden Arbetsflöden.**

1. Om du vill visa ett specifikt arbetsflöde öppnar du arbetsflödets snabbmeny och väljer Öppna i **designer,** vilket öppnar arbetsflödet i skrivskyddade läge.

   Om du vill redigera arbetsflödet har du följande alternativ:

   * I Visual Studio Code öppnar du projektets **filworkflow.js** i arbetsflödesdesignern, redigerar och distribuerar om logikappen till Azure.

   * I Azure Portal [du och öppnar logikappen](#manage-deployed-apps-portal). Hitta, redigera och spara arbetsflödet.

1. Öppna den distribuerade logikappen i Azure Portal genom att öppna logikappens snabbmeny och välja **Öppna i portalen.**

   Den Azure Portal öppnas i webbläsaren, loggar in dig på portalen automatiskt om du är inloggad på Visual Studio Code och visar logikappen.

   ![Skärmbild som visar Azure Portal för logikappen i Visual Studio Kod.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   Du kan också logga in separat på Azure Portal, använda sökrutan i portalen för att hitta logikappen och sedan välja logikappen i resultatlistan.

   ![Skärmbild som visar Azure Portal och sökfältet med sökresultat för distribuerad logikapp som visas markerad.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="manage-deployed-apps-portal"></a>

## <a name="manage-deployed-logic-apps-in-the-portal"></a>Hantera distribuerade logikappar i portalen

I Azure Portal kan du visa alla distribuerade logikappar som finns i din Azure-prenumeration, oavsett om de är den ursprungliga **Logic Apps-resurstypen** eller resurstypen **Logikapp (förhandsversion).** För närvarande organiseras och hanteras varje resurstyp som separata kategorier i Azure. Följ dessa steg om du vill hitta logikappar som har resurstypen **Logikapp (förhandsversion):**

1. I sökrutan Azure Portal du `logic app preview` . När resultatlistan visas går du till **Tjänster** och väljer **Logikapp (förhandsversion).**

   ![Skärmbild som visar Azure Portal sökrutan med söktexten "logic app preview".](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. I fönstret **Logikapp (förhandsversion)** hittar och väljer du den logikapp som du distribuerade från Visual Studio Code.

   ![Skärmbild som visar Azure Portal och de Logic App-resurser (förhandsversion) som distribuerats i Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   Den Azure Portal öppnar sidan för enskilda resurser för den valda logikappen.

   ![Skärmbild som visar resurssidan för logikapparbetsflödet i Azure Portal.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. Om du vill visa arbetsflödena i den här logikappen går du till logikappens meny och väljer **Arbetsflöden.**

   Fönstret **Arbetsflöden** visar alla arbetsflöden i den aktuella logikappen. Det här exemplet visar arbetsflödet som du skapade i Visual Studio Code.

   ![Skärmbild som visar resurssidan "Logikapp (förhandsversion)" med fönstret "Arbetsflöden" öppet och det distribuerade arbetsflödet](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. Om du vill visa ett arbetsflöde går **du till fönstret** Arbetsflöden och väljer det arbetsflödet.

   Arbetsflödesfönstret öppnas och visar mer information och uppgifter som du kan utföra i arbetsflödet.

   Om du till exempel vill visa stegen i arbetsflödet väljer du **Designer**.

   ![Skärmbild som visar det valda arbetsflödets "Översikt"-fönster, medan arbetsflödesmenyn visar det valda kommandot "Designer".](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   Arbetsflödesdesignern öppnas och visar det arbetsflöde som du skapade i Visual Studio Code. Nu kan du göra ändringar i det här arbetsflödet i Azure Portal.

   ![Skärmbild som visar arbetsflödesdesignern och arbetsflödet som distribuerats från Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflow-portal"></a>

## <a name="add-another-workflow-in-the-portal"></a>Lägga till ytterligare ett arbetsflöde i portalen

Via Azure Portal kan du lägga till tomma arbetsflöden till en logikappresurs **(förhandsversion)** som du har distribuerat från Visual Studio Code och skapa dessa arbetsflöden i Azure Portal.

1. I [Azure Portal](https://portal.azure.com)du och väljer den distribuerade logikappresursen **(förhandsversion).**

1. På logikappmenyn väljer du **Arbetsflöden.** I fönstret **Arbetsflöden** väljer du Lägg **till**.

   ![Skärmbild som visar den valda logikappens fönster "Arbetsflöden" och verktygsfältet med kommandot "Lägg till" valt.](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. I fönstret **Nytt arbetsflöde** anger du namnet på arbetsflödet. Välj **antingen Tillståndsful** **(Tillståndsful) eller Stateless** **>** **Create (Tillståndslös).**

   När Azure har distribuerat ditt nya  arbetsflöde, som visas i fönstret Arbetsflöden, väljer du det arbetsflödet så att du kan hantera och utföra andra uppgifter, till exempel öppna designern eller kodvyn.

   ![Skärmbild som visar det valda arbetsflödet med hanterings- och granskningsalternativ.](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   Om du till exempel öppnar designern för ett nytt arbetsflöde visas en tom arbetsyta. Nu kan du skapa det här arbetsflödet i Azure Portal.

   ![Skärmbild som visar arbetsflödesdesignern och ett tomt arbetsflöde.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Aktivera körningshistorik för tillståndslösa arbetsflöden

Om du vill felsöka ett tillståndslöst arbetsflöde enklare kan du aktivera körningshistoriken för arbetsflödet och sedan inaktivera körningshistoriken när du är klar. Följ de här stegen Visual Studio Code, eller om du arbetar i Azure Portal, se Skapa [tillståndslösa och tillståndslösa](create-stateful-stateless-workflows-azure-portal.md#enable-run-history-stateless)arbetsflöden i Azure Portal .

1. I ditt Visual Studio Code-projekt expanderar du mappen **workflow-designtime** och **öppnarlocal.settings.jspå** filen.

1. Lägg till `Workflows.{yourWorkflowName}.operationOptions` egenskapen och ange värdet till , till `WithStatelessRunHistory` exempel:

   **Windows**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

   **macOS eller Linux**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct; \
             AccountKey=<access-key>;EndpointSuffix=core.windows.net",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

1. Om du vill inaktivera körningshistoriken när du är klar anger du `Workflows.{yourWorkflowName}.OperationOptions` antingen egenskapen till `None` eller tar bort egenskapen och dess värde.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-view-in-the-azure-portal"></a>Aktivera övervakningsvyn i Azure Portal

När du har distribuerat en **logikappresurs (förhandsversion)** från Visual Studio Code till Azure kan du granska all tillgänglig körningshistorik och information om ett arbetsflöde i resursen med hjälp av Azure Portal och **övervakningsupplevelsen** för det arbetsflödet. Du måste dock först aktivera funktionen **Övervaka vy** för den logikappresursen.

1. I [Azure Portal](https://portal.azure.com)du den distribuerade logikappresursen **(förhandsversion).**

1. Välj CORS under **API** på **resursens meny.**

1. Lägg till jokertecknet (*)under **Tillåtna ursprung** i **fönstret CORS.**

1. När du är klar väljer du Spara i **CORS-verktygsfältet.** 

   ![Skärmbild som visar Azure Portal med en distribuerad logikappresurs (förhandsversion). På resursmenyn väljs "CORS" med en ny post för "Tillåtna ursprung" inställd på jokertecknet "*".](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Aktivera eller öppna Application Insights efter distributionen

Under körningen av arbetsflödet skickar logikappen telemetri tillsammans med andra händelser. Du kan använda den här telemetrin för att få bättre insyn i hur bra arbetsflödet körs och hur Logic Apps fungerar på olika sätt. Du kan övervaka arbetsflödet med [hjälp Application Insights](../azure-monitor/app/app-insights-overview.md), som tillhandahåller telemetri i nästan realtid (livemått). Den här funktionen kan hjälpa dig att undersöka fel och prestandaproblem enklare när du använder dessa data för att diagnostisera problem, konfigurera aviseringar och skapa diagram.

Om logikappens inställningar för att skapa och distribuera stöder [användning Application Insights](../azure-monitor/app/app-insights-overview.md)kan du även aktivera diagnostikloggning och spårning för logikappen. Du kan göra det antingen när du distribuerar logikappen från Visual Studio Code eller efter distributionen. Du måste ha en Application Insights-instans, men du kan skapa den här resursen antingen i förväg [,](../azure-monitor/app/create-workspace-resource.md)när du distribuerar logikappen eller efter distributionen.

Följ dessa steg Application Insights aktivera funktionen för en distribuerad logikapp eller granska Application Insights data när de redan är aktiverade:

1. I Azure Portal du den distribuerade logikappen.

1. På logikappmenyn går du till **Inställningar** och väljer **Application Insights**.

1. Om Application Insights inte är aktiverat går du **till Application Insights** och väljer **Aktivera Application Insights**. När fönstret har uppdateras väljer du Tillämpa längst **ned.**

   Om Application Insights är aktiverat går du **till Application Insights** och väljer Visa **Application Insights data.**

När Application Insights öppnas kan du granska olika mått för logikappen. Mer information finns i följande avsnitt:

* [Azure Logic Apps Kör var som helst – Övervaka med Application Insights – del 1](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [Azure Logic Apps Kör var som helst – Övervaka med Application Insights – del 2](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker"></a>Distribuera till Docker

Du kan distribuera logikappen till en [Docker-container](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) som värdmiljö med hjälp av [.NET CLI.](/dotnet/core/tools/) Med dessa kommandon kan du skapa och publicera logikappens projekt. Du kan sedan skapa och köra dockercontainern som mål för distribution av logikappen.

Om du inte är bekant med Docker kan du läsa följande avsnitt:

* [Vad är Docker?](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)
* [Introduktion till containrar och Docker](/dotnet/architecture/microservices/container-docker-introduction/)
* [Introduktion till .NET och Docker](/dotnet/core/docker/introduction)
* [Docker-containrar, avbildningar och register](/dotnet/architecture/microservices/container-docker-introduction/docker-containers-images-registries)
* [Självstudie: Komma igång med Docker (Visual Studio Code)](/visualstudio/docker/tutorials/docker-tutorial)

### <a name="requirements"></a>Krav

* Det Azure Storage-konto som logikappen använder för distribution

* En Docker-fil för arbetsflödet som du använder när du skapar dockercontainern

  Den här Docker-exempelfilen distribuerar till exempel en logikapp och anger anslutningssträngen som innehåller åtkomstnyckeln för det Azure Storage-konto som användes för att publicera logikappen till Azure Portal. Information om hur du hittar den här strängen finns [i Hämta anslutningssträngen för lagringskontot.](#find-storage-account-connection-string) Mer information finns i [Metodtips för att skriva Docker-filer.](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
  
  > [!IMPORTANT]
  > I produktionsscenarier bör du skydda och skydda sådana hemligheter och känslig information, till exempel med hjälp av ett nyckelvalv. Mer specifikt för Docker-filer kan [du läsa Skapa avbildningar med BuildKit](https://docs.docker.com/develop/develop-images/build_enhancements/) och [Hantera känsliga data med Docker Secrets.](https://docs.docker.com/engine/swarm/secrets/)

   ```text
   FROM mcr.microsoft.com/azure-functions/node:3.0

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
       AzureFunctionsJobHost__Logging__Console__IsEnabled=true \
       FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY . /home/site/wwwroot

   RUN cd /home/site/wwwroot
   ```

<a name="find-storage-account-connection-string"></a>

### <a name="get-storage-account-connection-string"></a>Hämta anslutningssträng för lagringskonto

Innan du kan skapa och köra dockercontaineravbildningen måste du hämta anslutningssträngen som innehåller åtkomstnyckeln till ditt lagringskonto. Tidigare skapade du det här lagringskontot som att använda tillägget på macOS eller Linux, eller när du distribuerade logikappen till Azure Portal.

Följ dessa steg om du vill hitta och kopiera anslutningssträngen:

1. På Azure Portal lagringskontomenyn under Inställningar **väljer** du **Åtkomstnycklar.** 

1. I fönstret **Åtkomstnycklar** letar du upp och kopierar lagringskontots anslutningssträng, som ser ut ungefär som i det här exemplet:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Skärmbild som visar Azure Portal lagringskontots åtkomstnycklar och anslutningssträngen kopierade.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Mer information finns i Hantera [lagringskontonycklar.](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)

1. Spara anslutningssträngen på en säker plats så att du kan lägga till den här strängen i Docker-filen som du använder för distribution. 

<a name="find-storage-account-master-key"></a>

### <a name="find-master-key-for-storage-account"></a>Hitta huvudnyckel för lagringskonto

När arbetsflödet innehåller en Request-utlösare måste du hämta utlösarens [motringnings-URL](#get-callback-url-request-trigger) när du har byggt och kört Docker-containeravbildningen. För den här uppgiften måste du också ange huvudnyckelvärdet för det lagringskonto som du använder för distributionen.

1. Du hittar huvudnyckeln genom att öppna **filen azure-webjobs-secrets/{deployment-name}/host.jsi** projektet.

1. Leta upp `AzureWebJobsStorage` egenskapen och kopiera nyckelvärdet från det här avsnittet:

   ```json
   {
      <...>
      "masterKey": {
         "name": "master",
         "value": "<master-key>",
         "encrypted": false
      },
      <...>
   }
   ```

1. Spara nyckelvärdet på en säker plats så att du kan använda det senare.

<a name="build-run-docker-container-image"></a>

### <a name="build-and-run-your-docker-container-image"></a>Skapa och kör dockercontaineravbildningen

1. Skapa dockercontaineravbildningen med hjälp av Docker-filen och kör följande kommando:

   `docker build --tag local/workflowcontainer .`

   Mer information finns i [docker build](https://docs.docker.com/engine/reference/commandline/build/).

1. Kör containern lokalt med hjälp av det här kommandot:

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   Mer information finns i [docker run](https://docs.docker.com/engine/reference/commandline/run/).

<a name="get-callback-url-request-trigger"></a>

### <a name="get-callback-url-for-request-trigger"></a>Hämta motringning-URL för begärandeutlösare

För ett arbetsflöde som använder begärandeutlösaren hämtar du utlösarens återanrops-URL genom att skicka den här begäran:

`POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2020-05-01-preview&code={master-key}`

Värdet `{trigger-name}` är namnet på den begärandeutlösare som visas i arbetsflödets JSON-definition. Värdet definieras i det Azure Storage-konto som du anger för egenskapen i filen `{master-key}` `AzureWebJobsStorage` **azure-webjobs-secrets/{deployment-name}/host.jspå**. Mer information finns i Hitta [huvudnyckeln för lagringskontot.](#find-storage-account-master-key)

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Ta bort objekt från designern

Följ dessa steg om du vill ta bort ett objekt i arbetsflödet från designern:

* Välj objektet, öppna objektets snabbmeny (Skift + F10) och välj Ta **bort**. Bekräfta genom att välja **OK**.

* Markera objektet och tryck på delete-tangenten. Bekräfta genom att välja **OK**.

* Markera objektet så att informationsfönstret öppnas för objektet. I fönstrets övre högra hörn öppnar du ellipsmenyn (**...**) och väljer Ta **bort**. Bekräfta genom att välja **OK**.

  ![Skärmbild som visar ett markerat objekt i designern med det öppna informationsfönstret plus den valda ellipsknappen och kommandot "Ta bort".](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

  > [!TIP]
  > Om ellipsmenyn inte visas expanderar du Visual Studio Code-fönstret så att informationsfönstret visar ellipserna (**...**) i det övre högra hörnet.

<a name="troubleshooting"></a>

## <a name="troubleshoot-errors-and-problems"></a>Felsöka fel och problem

<a name="designer-fails-to-open"></a>

### <a name="designer-fails-to-open"></a>Designern kan inte öppnas

När du försöker öppna designern får du följande felmeddelande: "Det gick inte att starta **designtiden för arbetsflödet".** Om du tidigare försökte öppna designern och sedan avbröt eller tog bort projektet kanske tilläggspaketet inte laddas ned korrekt. Följ dessa steg om du vill kontrollera om problemet beror på detta:

  1. I Visual Studio Code öppnar du fönstret Utdata. Välj **Utdata** på **visa-menyn.**

  1. I listan i namnlisten i utdatafönstret väljer **du Azure Logic Apps (förhandsversion)** så att du kan granska utdata från tillägget, till exempel:

     ![Skärmbild som visar fönstret Utdata med "Azure Logic Apps" valt.](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

  1. Granska utdata och kontrollera om det här felmeddelandet visas:

     ```text
     A host error has occurred during startup operation '{operationID}'.
     System.Private.CoreLib: The file 'C:\Users\{userName}\AppData\Local\Temp\Functions\
     ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.7\bin\
     DurableTask.AzureStorage.dll' already exists.
     Value cannot be null. (Parameter 'provider')
     Application is shutting down...
     Initialization cancellation requested by runtime.
     Stopping host...
     Host shutdown completed.
     ```

   Lös det här felet genom att ta bort **mappen ExtensionBundles** på den här platsen **...\Users \{ your-username}\AppData\Local\Temp\Functions\ExtensionBundles** och försök öppna **workflow.js** på filen i designern igen.

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>Nya utlösare och åtgärder saknas i designerväljaren för tidigare skapade arbetsflöden

Azure Logic Apps Preview stöder inbyggda åtgärder för Azure Function Operations, Liquid Operations och XML-åtgärder, till exempel **XML-validering** och **Transformera XML.** Men för logikappar som skapats tidigare kanske dessa åtgärder inte visas i designerväljaren så att du kan välja om Visual Studio Code använder en inaktuell version av tilläggspaketet, `Microsoft.Azure.Functions.ExtensionBundle.Workflows` .

**Anslutningsappen och åtgärderna för Azure Function Operations** visas inte i designerväljaren såvida du inte har aktiverat eller valt Använd anslutningsappar från **Azure** när du skapade logikappen. Om du inte aktiverar de Azure-distribuerade anslutningsapparna när appen skapas kan du aktivera dem från ditt projekt i Visual Studio Code. Öppnaworkflow.js **på** snabbmenyn och välj **Använd anslutningsappar från Azure**.

Om du vill åtgärda det inaktuella paketet följer du dessa steg för att ta bort det inaktuella paketet, vilket gör att Visual Studio Code uppdaterar tilläggspaketet automatiskt till den senaste versionen.

> [!NOTE]
> Den här lösningen gäller endast för logikappar som du skapar och distribuerar med Visual Studio Code med tillägget Azure Logic Apps (förhandsversion), inte de logikappar som du skapade med hjälp av Azure Portal. Se [Utlösare och åtgärder som stöds saknas i designern i Azure Portal](create-stateful-stateless-workflows-azure-portal.md#missing-triggers-actions).

1. Spara allt arbete som du inte vill förlora och stäng Visual Studio.

1. På datorn bläddrar du till följande mapp, som innehåller versionsmappar för det befintliga paketet:

   `...\Users\{your-username}\.azure-functions-core-tools\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Ta bort versionsmappen för det tidigare paketet, till exempel om du har en mapp för version 1.1.3, ta bort mappen.

1. Bläddra nu till följande mapp, som innehåller versionsmappar för det NuGet-paket som krävs:

   `...\Users\{your-username}\.nuget\packages\microsoft.azure.workflows.webjobs.extension`

1. Ta bort versionsmappen för det tidigare paketet, till exempel om du har en mapp för version 1.0.0.8-preview, och ta bort mappen.

1. Öppna Visual Studio Kod, ditt projekt **ochworkflow.jspå filen** i designern.

De utlösare och åtgärder som saknas visas nu i designern.

<a name="400-bad-request"></a>

### <a name="400-bad-request-appears-on-a-trigger-or-action"></a>"400 Felaktig begäran" visas på en utlösare eller åtgärd

När en körning misslyckas och du inspekterar körningen i övervakningsvyn kan det här felet visas på en utlösare eller åtgärd som har ett längre namn, vilket gör att den underliggande Uniform Resource Identifier -URI:n överskrider standardteckengränsen.

Lös problemet och justera för den längre URI:n genom att redigera registernycklarna och på `UrlSegmentMaxCount` datorn genom att följa stegen `UrlSegmentMaxLength` nedan. Standardvärdena för dessa nycklar beskrivs i det här avsnittet, [Http.sys registerinställningar för Windows](/troubleshoot/iis/httpsys-registry-windows).

> [!IMPORTANT]
> Se till att spara ditt arbete innan du börjar. Den här lösningen kräver att du startar om datorn när du är klar så att ändringarna kan börja gälla.

1. Öppna fönstret Kör på **datorn** och kör kommandot `regedit` som öppnar registereditorn.

1. I rutan **User Account Control** du Ja **för** att tillåta dina ändringar på datorn.

1. I den vänstra rutan under **Dator** expanderar du noderna längs sökvägen, **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters** och väljer sedan **Parametrar**.

1. Leta reda på registernycklarna `UrlSegmentMaxCount` och i den högra `UrlSegmentMaxLength` rutan.

1. Öka de här nyckelvärdena tillräckligt så att URI:erna kan hantera de namn som du vill använda. Om nycklarna inte finns lägger du till dem i mappen **Parametrar** genom att följa dessa steg:

   1. På **snabbmenyn Parametrar** väljer du **Nytt**  >  **DWORD-värde (32-bitars).**

   1. I redigeringsrutan som visas anger du `UrlSegmentMaxCount` som det nya nyckelnamnet.

   1. Öppna snabbmenyn för den nya nyckeln och välj **Ändra**.

   1. I rutan **Redigera sträng** som visas anger du värdet för **den värdedatanyckel** som du vill använda i hexadecimalt eller decimalformat. I till exempel `400` hexadecimalt motsvarar i `1024` decimaltal.

   1. Upprepa dessa steg `UrlSegmentMaxLength` för att lägga till nyckelvärdet.

   När du har ökat eller lagt till dessa nyckelvärden ser registereditorn ut som i det här exemplet:

   ![Skärmbild som visar registereditorn.](media/create-stateful-stateless-workflows-visual-studio-code/edit-registry-settings-uri-length.png)

1. När du är klar startar du om datorn så att ändringarna börjar gälla.

<a name="debugging-fails-to-start"></a>

### <a name="debugging-session-fails-to-start"></a>Det går inte att starta felsökningssessionen

När du försöker starta en felsökningssession får du felet **"Error exists after running preLaunchTask 'generateDebugSymbols'" (Felet finns efter körningen preLaunchTask 'generateDebugSymbols').** Lös problemet genom att redigera filen **tasks.jsi** projektet för att hoppa över symbolgenereringen.

1. I projektet expanderar du **mappen .vscode** och öppnar mappen **tasks.jspå** filen.

1. I följande uppgift tar du bort raden `"dependsOn: "generateDebugSymbols"` , tillsammans med kommatecken som slutar på föregående rad, till exempel:

   Innan:

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true,
      "dependsOn": "generateDebugSymbols"
    }
   ```

   Efter:

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true
    }
   ```

## <a name="next-steps"></a>Nästa steg

Vi vill veta mer om dina erfarenheter av Azure Logic Apps (förhandsversion) !

* För buggar eller problem skapar [du dina problem i GitHub.](https://github.com/Azure/logicapps/issues)
* Använd det här feedbackformuläret för frågor, begäranden, kommentarer [och annan feedback.](https://aka.ms/lafeedback)
