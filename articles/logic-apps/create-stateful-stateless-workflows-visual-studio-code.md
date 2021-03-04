---
title: Skapa Logic Apps för hands versioner av arbets flöden i Visual Studio Code
description: Skapa och kör arbets flöden för Automation-och integrations scenarier i Visual Studio Code med tillägget Azure Logic Apps (för hands version).
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 0850830e6f8101feae80154a0e245196a690f276
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050247"
---
# <a name="create-stateful-and-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Skapa tillstånds känsliga och tillstånds lösa arbets flöden i Visual Studio Code med tillägget Azure Logic Apps (förhands granskning)

> [!IMPORTANT]
> Den här funktionen är tillgänglig som en offentlig förhandsversion utan servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med [Azure Logic Apps för hands](logic-apps-overview-preview.md)version kan du skapa Automation-och integrations lösningar mellan appar, data, moln tjänster och system genom att skapa och köra Logi Kap par som innehåller [ *tillstånds känsliga* och *tillstånds lösa* arbets flöden](logic-apps-overview-preview.md#stateful-stateless) i Visual Studio Code med hjälp av tillägget Azure Logic Apps (förhands granskning). Genom att använda den här nya typen av Logic-appar kan du skapa flera arbets flöden som drivs av den omdesignade Azure Logic Apps för hands versions körning, vilket ger portabilitet, bättre prestanda och flexibilitet för att distribuera och köra i olika värd miljöer, inte bara Azure, utan även Docker-behållare. Mer information om den nya typen av Logic-appar finns i [Översikt för Azure Logic Apps för hands version](logic-apps-overview-preview.md).

![Skärm bild som visar Visual Studio Code, Logic app Project och Workflow.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

I Visual Studio Code kan du börja med att skapa ett projekt där du kan skapa och köra din Logic Apps-arbets flöden *lokalt* i utvecklings miljön med hjälp av tillägget Azure Logic Apps (för hands version). Även om du kan börja med att [skapa en ny resurs för logi Kap par **(förhands granskning)** i Azure Portal](create-stateful-stateless-workflows-azure-portal.md), ger båda metoderna möjlighet att distribuera och köra din Logi Kap par i samma typer av värd miljöer.

Under tiden kan du fortfarande skapa den ursprungliga typen av Logic-appar. Även om utvecklings upplevelser i Visual Studio Code skiljer sig mellan de ursprungliga och nya Logic Apps-typerna, kan din Azure-prenumeration innehålla båda typerna. Du kan visa och komma åt alla distribuerade Logi Kap par i din Azure-prenumeration, men apparna organiseras i sina egna kategorier och avsnitt.

Den här artikeln visar hur du skapar din Logi Kap par och ett arbets flöde i Visual Studio Code genom att använda tillägget Azure Logic Apps (för hands version) och utföra följande uppgifter på hög nivå:

* Skapa ett projekt för din Logic app och ditt arbets flöde.

* Lägg till en utlösare och en åtgärd.

* Kör, testa, Felsök och granska körnings historik lokalt.

* Distribuera till Azure, som innehåller alternativ för att aktivera Application Insights.

* Hantera din distribuerade Logic-app i Visual Studio Code och Azure Portal.

* Aktivera körnings historik för tillstånds lösa arbets flöden.

* Aktivera eller öppna Application Insights efter distribution.

* Distribuera till en Docker-behållare som du kan köra var som helst.

> [!NOTE]
> Om du vill ha mer information om aktuella kända problem granskar du [sidan Logic Apps offentliga kända problem i GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

## <a name="prerequisites"></a>Förutsättningar

### <a name="access-and-connectivity"></a>Åtkomst och anslutning

* Åtkomst till Internet så att du kan hämta kraven, ansluta från Visual Studio Code till ditt Azure-konto och publicera från Visual Studio Code till Azure, en Docker-behållare eller en annan miljö.

* Ett Azure-konto och prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Om du vill bygga samma exempel på Logic-appen i den här artikeln behöver du ett Office 365 Outlook-e-postkonto som använder ett arbets-eller skol konto från Microsoft för att logga in.

  Om du väljer att använda en annan [e-postanslutning som stöds av Azure Logic Apps](/connectors/), till exempel Outlook.com eller [Gmail](../connectors/connectors-google-data-security-privacy-policy.md), kan du fortfarande följa exemplet och de allmänna övergripande stegen är desamma, men användar gränssnittet och alternativen kan variera på vissa sätt. Om du till exempel använder Outlook.com-anslutningsprogrammet använder du din personliga Microsoft-konto i stället för att logga in.

<a name="storage-requirements"></a>

### <a name="storage-requirements"></a>Lagrings krav

#### <a name="windows"></a>Windows

För att skapa och köra ditt Logic app-projekt lokalt i Visual Studio Code när du använder Windows, följer du dessa steg för att konfigurera Azure Storage emulatorn:

1. Hämta och installera [Azure Storage Emulator 5,10](https://go.microsoft.com/fwlink/p/?linkid=717179).

1. Om du inte redan har en, måste du ha en lokal SQL DB-installation, till exempel den kostnads fria [SQL Server 2019 Express-versionen](https://go.microsoft.com/fwlink/p/?linkid=866658), så att emulatorn kan köras.

   Mer information finns i [använda Azure Storage emulatorn för utveckling och testning](../storage/common/storage-use-emulator.md).

1. Innan du kan köra ditt projekt ska du kontrol lera att du startar emulatorn.

   ![Skärm bild som visar Azure Storage-emulatorn som körs.](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

#### <a name="macos-and-linux"></a>macOS och Linux

Om du vill skapa och köra ditt Logic app-projekt lokalt i Visual Studio Code när du använder macOS eller Linux, följer du dessa steg för att skapa och konfigurera ett Azure Storage konto.

> [!NOTE]
> För närvarande fungerar inte designern i Visual Studio Code på Linux OS, men du kan fortfarande köra skapa, köra och distribuera Logi Kap par som använder Logic Apps för hands versions körning till Linux-baserade virtuella datorer. Nu kan du bygga dina Logi Kap par i Visual Studio Code på Windows eller macOS och sedan distribuera till en Linux-baserad virtuell dator.

1. Logga in på [Azure Portal](https://portal.azure.com)och [skapa ett Azure Storage-konto](../storage/common/storage-account-create.md?tabs=azure-portal), vilket är ett [krav för Azure Functions](../azure-functions/storage-considerations.md).

1. Välj **åtkomst nycklar** under **Inställningar** på menyn lagrings konto.

1. I fönstret **åtkomst nycklar** letar du reda på och kopierar lagrings kontots anslutnings sträng, som ser ut ungefär som i det här exemplet:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Skärm bild som visar Azure Portal med åtkomst nycklar för lagrings kontot och en anslutnings sträng som har kopierats.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Mer information finns i [Hantera lagrings konto nycklar](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys).

1. Spara anslutnings strängen någon annan stans. När du har skapat ditt Logic app-projekt i Visual Studio Code, måste du lägga till strängen i **local.settings.js** filen i ditt projekts rotmapp.

   > [!IMPORTANT]
   > Om du planerar att distribuera till en Docker-behållare måste du också lägga till den här anslutnings strängen i Docker-filen som du använder för att distribuera.

### <a name="tools"></a>Verktyg

* [Visual Studio Code 1.30.1 (januari 2019) eller högre](https://code.visualstudio.com/), som är kostnads fri. Hämta och installera även dessa verktyg för Visual Studio Code om du inte redan har dem:

  * [Tillägg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)för Azure-konto, som ger en enda gemensam Azure-inloggning och prenumerations filtrerings upplevelse för alla andra Azure-tillägg i Visual Studio Code.

  * [C# för kod tillägg för Visual Studio](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp), som gör att F5-funktionen kan köra din Logic app.

  * [Azure Functions Core Tools 3.0.3245 eller senare](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.3245) med hjälp av Microsoft Installer (MSI)-versionen, som är `func-cli-3.0.3245-x*.msi` .

    Dessa verktyg innehåller en version av samma körnings miljö som ger Azure Functions runtime, som används i Visual Studio Code.

    > [!IMPORTANT]
    > Om du har en tidigare installation än dessa versioner måste du först avinstallera den versionen eller kontrol lera att sökvägen till miljövariabeln pekar på den version som du hämtar och installerar.

  * [Azure Logic Apps (för hands version)-tillägget för Visual Studio Code](https://go.microsoft.com/fwlink/p/?linkid=2143167). Tillägget ger dig möjlighet att skapa Logi Kap par där du kan skapa tillstånds känsliga och tillstånds lösa arbets flöden som körs lokalt i Visual Studio Code och sedan distribuera dessa Logic Apps direkt till Azure eller till Docker-behållare.

    För närvarande kan du ha både det ursprungliga Azure Logic Apps tillägget och det offentliga för hands versions tillägget installerat i Visual Studio Code. Även om utvecklings upplevelsen skiljer sig på några sätt mellan tilläggen, kan din Azure-prenumeration innehålla både typer av logiska appar som du skapar med tilläggen. Visual Studio Code visar alla distribuerade Logic Apps i din Azure-prenumeration, men ordnar dem i olika avsnitt efter tilläggs namn, **Logic Apps** och **Azure Logic Apps (förhands granskning)**.

    > [!IMPORTANT]
    > Om du har skapat Logic Apps-projekt med det tidigare privata förhands gransknings tillägget fungerar inte dessa projekt med det offentliga för hands versions tillägget. Du kan dock migrera dessa projekt när du har avinstallerat tillägget för privat förhands granskning, tagit bort de associerade filerna och installerat det offentliga för hands versions tillägget. Sedan skapar du ett nytt projekt i Visual Studio Code och kopierar din tidigare skapade Logic Apps **. definitions** fil till det nya projektet. Mer information finns i [Migrera från det privata förhands gransknings tillägget](#migrate-private-preview).
    > 
    > Om du har skapat Logic Apps-projekt med det tidigare offentliga för hands versions tillägget kan du fortsätta att använda dessa projekt utan att migrera några steg.

    **Följ dessa steg om du vill installera **Azure Logic Apps (för hands version)** -tillägget:**

    1. Välj **tillägg** i Visual Studio Code i det vänstra verktygsfältet.

    1. I sökrutan tillägg anger du `azure logic apps preview` . Välj **Azure Logic Apps (för hands version)** i listan resultat **>** .

       När installationen är klar visas förhands gransknings tillägget i listan **tillägg: installerad** .

       ![Skärm bild som visar Visual Studio Codes installerade tillägg-lista med tillägget "Azure Logic Apps (för hands version)" understruket.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

       > [!TIP]
       > Om tillägget inte visas i den installerade listan kan du prova att starta om Visual Studio Code.

* Om du vill använda den [infogade kod åtgärder](../logic-apps/logic-apps-add-run-inline-code.md) som kör Java Script, installerar du [Node.js version 10. x, 11. x. x eller 12. x. x](https://nodejs.org/en/download/releases/).

  > [!TIP] 
  > För Windows laddar du ned MSI-versionen. Om du använder ZIP-versionen i stället måste du manuellt göra Node.js tillgänglig med hjälp av en Sök VÄGS miljö variabel för ditt operativ system.

* För att lokalt köra webhook-baserade utlösare och åtgärder, till exempel den [inbyggda HTTP-webhook-utlösaren](../connectors/connectors-native-webhook.md), i Visual Studio Code, måste du [Konfigurera vidarebefordran för återanrops-URL: en](#webhook-setup).

* Om du vill testa den exempel Logic-app som du skapar i den här artikeln behöver du ett verktyg som kan skicka anrop till utlösaren för begäran, vilket är det första steget i exempel Logic app. Om du inte har ett sådant verktyg kan du ladda ned, installera och använda [Postman](https://www.postman.com/downloads/).

* Om du skapar din Logic app och distribuerar med inställningar som stöder användning av [Application Insights](../azure-monitor/app/app-insights-overview.md), kan du välja att aktivera diagnostikloggning och spårning för din Logic app. Du kan göra det antingen när du distribuerar din Logic app från Visual Studio Code eller efter distributionen. Du måste ha en Application Insights instans, men du kan skapa den här resursen [i förväg](../azure-monitor/app/create-workspace-resource.md)när du distribuerar din Logic app eller efter distributionen.

<a name="migrate-private-preview"></a>

## <a name="migrate-from-private-preview-extension"></a>Migrera från privat för hands versions tillägg

Alla Logic app-projekt som du skapade med tillägget **Azure Logic Apps (privat förhands granskning)** fungerar inte med det offentliga för hands versions tillägget. Du kan dock migrera dessa projekt till nya projekt genom att följa dessa steg:

1. Avinstallera privat förhands gransknings tillägg.

1. Ta bort alla associerade tilläggs paket och NuGet på följande platser:

   * Mappen **Microsoft. Azure. functions. ExtensionBundle.** Workflows, som innehåller tidigare anknytnings paket och finns antingen i sökvägen här:

     * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`

     * `C:\Users\{userName}\.azure-functions-core-tools\Functions\ExtensionBundles`

   * Mappen **Microsoft. Azure. Workflows. WebJobs. extension** , som är [NuGet](/nuget/what-is-nuget) -cache för det privata förhands gransknings tillägget och finns på den här sökvägen:

     `C:\Users\{userName}\.nuget\packages`

1. Installera tillägget **Azure Logic Apps (förhands granskning)** .

1. Skapa ett nytt projekt i Visual Studio Code.

1. Kopiera din tidigare skapade Logic app- **arbetsflöde. definitions** fil till det nya projektet.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Konfigurera Visual Studio Code

1. Kontrol lera att alla tillägg är korrekt installerade genom att läsa in eller starta om Visual Studio Code.

1. Bekräfta att Visual Studio Code automatiskt hittar och installerar tilläggs uppdateringar så att ditt för hands versions tillägg får de senaste uppdateringarna. Annars måste du avinstallera den inaktuella versionen manuellt och installera den senaste versionen.

   1. På **Arkiv** -menyn går **du till inställningar** **>** **Inställningar**.

   1. På fliken **användare** går du till **funktioner** **>** **tillägg**.

   1. Bekräfta att **automatisk kontroll av uppdateringar** och **Automatisk uppdatering** är markerat.

Som standard är följande inställningar aktiverade och inställda för Logic Apps för hands versions tillägg:

* **Azure Logic Apps v2: Project runtime**, som är inställt på version **~ 3**

  > [!NOTE]
  > Den här versionen krävs för att använda de [infogade kod](../logic-apps/logic-apps-add-run-inline-code.md)åtgärderna.

* **Azure Logic Apps v2: experimentell visnings hanterare**, som aktiverar den senaste designern i Visual Studio Code. Om du får problem i designern, till exempel dra och släppa objekt, inaktiverar du den här inställningen.

Följ dessa steg om du vill söka efter och bekräfta de här inställningarna:

1. På **Arkiv** -menyn går **du till inställningar** **>** **Inställningar**.

1. På fliken **användare** går du till **>** **tillägg** **>** **Azure Logic Apps (för hands version)**.

   Du kan till exempel hitta **Azure Logic Apps v2: projekt körnings** inställningen här eller använda sökrutan för att hitta andra inställningar:

   ![Skärm bild som visar tillägg för Visual Studio-kod för tillägget Azure Logic Apps (för hands version).](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Anslut till ditt Azure-konto

1. Välj Azure-ikonen i aktivitets fältet i Visual Studio Code.

   ![Skärm bild som visar aktivitets fältet i Visual Studio Code och den valda Azure-ikonen.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. I fönstret Azure under **Azure: Logic Apps (för hands version)** väljer **du logga in på Azure**. När sidan Visual Studio Code Authentication visas loggar du in med ditt Azure-konto.

   ![Skärm bild som visar Azure-fönstret och den valda länken för Azure-inloggning.](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   När du har loggat in visar Azure-fönstret prenumerationerna på ditt Azure-konto. Om du också har det offentligt utgivna tillägget kan du hitta alla Logi Kap par som du har skapat med tillägget i avsnittet **Logic Apps** , inte avsnittet **Logic Apps (för hands version)** .
   
   Om de förväntade prenumerationerna inte visas, eller om du vill att fönstret endast ska visa vissa prenumerationer, följer du dessa steg:

   1. I listan prenumerationer flyttar du pekaren bredvid den första prenumerationen tills knappen **Välj prenumerationer** (filter ikon) visas. Välj filter ikonen.

      ![Skärm bild som visar Azure-fönstret och den valda filter ikonen.](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      Alternativt väljer du ditt Azure-konto i statusfältet i Visual Studio Code. 

   1. När en annan prenumerations lista visas väljer du de prenumerationer som du vill använda och kontrollerar sedan att du väljer **OK**.

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Skapa ett lokalt projekt

Innan du kan skapa din Logi Kap par skapar du ett lokalt projekt så att du kan hantera, köra och distribuera din Logic app från Visual Studio Code. Det underliggande projektet liknar ett Azure Functions projekt, även kallat ett Function Apps-projekt. Dessa projekt typer skiljer sig dock från varandra, så Logic Apps och Function-appar kan inte finnas i samma projekt.

1. På din dator skapar du en *Tom* lokal mapp som ska användas för projektet som du senare skapar i Visual Studio Code.

1. Stäng alla öppna mappar i Visual Studio Code.

1. I Azure-fönstret, bredvid **Azure: Logic Apps (för hands version)** väljer du **Skapa nytt projekt** (ikon som visar en mapp och blixt).

   ![Skärm bild som visar verktygsfältet i Azure-fönstret med alternativet "Skapa nytt projekt" markerat.](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Om Windows Defender-brandväggen uppmanas att bevilja nätverks åtkomst för `Code.exe` , som är Visual Studio Code och för `func.exe` , som är Azure Functions Core tools, väljer du **privata nätverk, till exempel mitt hem-eller arbets nätverk** **>** .

1. Bläddra till den plats där du skapade projektmappen, Välj mappen och fortsätt.

   ![Skärm bild som visar dialog rutan Välj mapp med en nyligen skapad projektmapp och knappen Välj markerad.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. Välj antingen **tillstånds känsligt arbets flöde** eller **arbets flöde för tillstånds lösa** i listan mallar som visas. I det här exemplet väljs **tillstånds känsligt arbets flöde**.

   ![Skärm bild som visar listan över arbetsflödesmallar med "tillstånds känsligt arbets flöde" valt.](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. Ange ett namn för arbets flödet och tryck på RETUR. I det här exemplet används `Fabrikam-Stateful-Workflow` som namn.

   ![Skärm bild som visar rutan "Skapa nytt tillstånds känsligt arbets flöde (3/4)" och "Fabrikam-Stateable-Workflow" som arbets flödes namn.](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

   Visual Studio Code Slutför skapandet av ditt projekt och öppnar **workflow.js** filen för ditt arbets flöde i kod redigeraren.

   > [!NOTE]
   > Om du uppmanas att välja hur du vill öppna projektet väljer du **Öppna i aktuellt fönster** om du vill öppna projektet i det aktuella Visual Studio Code-fönstret. Om du vill öppna en ny instans för Visual Studio Code väljer du **Öppna i nytt fönster**.

1. Öppna fönstret Utforskaren i verktygsfältet i Visual Studio, om det inte redan är öppet.

   Explorer-fönstret visar projektet, som nu innehåller automatiskt genererade projektfiler. Projektet har till exempel en mapp som visar ditt arbets flödes namn. I den här mappen innehåller **workflow.jsfilen i** arbets flödets underliggande JSON-definition.

   ![Skärm bild som visar Explorer-fönstret med projektmappen, arbetsflödes mapp och "workflow.jspå"-filen.](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

1. Om du använder macOS eller Linux kan du konfigurera åtkomst till ditt lagrings konto genom att följa dessa steg, som krävs för att lokalt köra ditt projekt:

   1. Öppna den **local.settings.js** filen i ditt projekts rotmapp.

      ![Skärm bild som visar Explorer-fönstret och local.settings.jspå-filen i projektet.](./media/create-stateful-stateless-workflows-visual-studio-code/local-settings-json-files.png)

   1. Ersätt `AzureWebJobsStorage` egenskap svärdet med lagrings kontots anslutnings sträng som du sparade tidigare, till exempel:

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

   1. När du är klar kontrollerar du att du sparar ändringarna.

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-the-designer"></a>Öppna arbets flödets definitions fil i designern

1. Kontrol lera de versioner som är installerade på datorn genom att köra det här kommandot:

   `..\Users\{yourUserName}\dotnet --list-sdks`

   Om du har .NET Core SDK 5. x kan den här versionen förhindra att du öppnar logik appens underliggande arbets flödes definition i designern. I stället för att avinstallera den här versionen går du till projektets rotmapp och skapar en **global.jspå** en fil som refererar till .net Core runtime 3. x-versionen som är senare än 3.1.201, till exempel:

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   > [!IMPORTANT]
   > Se till att du uttryckligen lägger till **global.jspå** filen i projektets rotmapp från Visual Studio Code. Annars öppnas inte designern.

1. Expandera projektmappen för arbets flödet. Öppna snabb menyn **workflow.jspå** filen och välj **Öppna i designern**.

   ![Skärm bild som visar Explorer-fönstret och gen vägs fönstret för workflow.jspå filen med alternativet öppna i designer valt.](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

1. I listan **aktivera anslutningar i Azure** väljer du **Använd anslutningar från Azure**, som gäller för alla hanterade anslutningar som är tillgängliga och distribuerade i Azure, inte bara anslutningar för Azure-tjänster.

   ![Skärm bild som visar Explorer-fönstret med alternativet "aktivera anslutningar i Azure" och "Använd anslutningar från Azure" markerat.](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > Tillstånds lösa arbets flöden stöder för närvarande endast *åtgärder* för [hanterade anslutningar](../connectors/apis-list.md#managed-api-connectors), som distribueras i Azure och inte utlösare. Även om du har möjlighet att aktivera anslutningar i Azure för ditt tillstånds lösa arbets flöde, visar inte designern några hanterade kopplings utlösare som du kan välja.

1. I listan **Välj prenumeration** väljer du den Azure-prenumeration som ska användas för ditt Logic Apps-projekt.

   ![Skärm bild som visar Explorer-fönstret med rutan "Välj prenumeration" och din prenumeration har valts.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-subscription.png)

1. I listan resurs grupper väljer du **Skapa ny resurs grupp**.

   ![Skärm bild som visar fönstret Utforskaren med listan resurs grupper och "Skapa ny resurs grupp" markerat.](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. Ange ett namn för resurs gruppen och tryck på RETUR. I det här exemplet används `Fabrikam-Workflows-RG`.

   ![Skärm bild som visar Explorer-fönstret och namn rutan för resurs gruppen.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. I listan platser söker du efter och väljer den Azure-region som du vill använda när du skapar resurs gruppen och resurserna. I det här exemplet används **västra centrala USA**.

   ![Skärm bild som visar Explorer-fönstret med plats listan och "västra centrala USA" valt.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   När du har gjort det här steget öppnas arbetsflödesdesignern i Visual Studio Code.

   > [!NOTE]
   > När Visual Studio Code startar API för arbets flödets design tid kan det hända att du får ett meddelande om att starten kan ta några sekunder. Du kan ignorera det här meddelandet eller välja **OK**.
   >
   > Om designern inte öppnas går du igenom fel söknings avsnittet. det [går inte att öppna designern](#designer-fails-to-open).

   När designern visas visas **åtgärden Välj en åtgärd** i designern och är markerad som standard, vilket visar fönstret **Lägg till en åtgärd** .

   ![Skärm bild som visar Workflow Designer.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. Lägg sedan [till en utlösare och åtgärder](#add-trigger-actions) i arbets flödet.

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Lägg till en utlösare och åtgärder

När du har öppnat designern visas **åtgärden Välj en åtgärd** i designern och är markerad som standard. Nu kan du börja skapa arbets flödet genom att lägga till en utlösare och åtgärder.

Arbets flödet i det här exemplet använder utlösaren och följande åtgärder:

* Den inbyggda [begär ande utlösaren](../connectors/connectors-native-reqres.md), **när en http-begäran tas emot**, som tar emot inkommande samtal eller begär Anden och skapar en slut punkt som andra tjänster eller Logi Kap par kan anropa.

* [Office 365 Outlook-åtgärd](../connectors/connectors-create-api-office365-outlook.md), **Skicka ett e-postmeddelande**.

* Den inbyggda [svars åtgärden](../connectors/connectors-native-reqres.md)som du använder för att skicka ett svar och returnera data tillbaka till anroparen.

### <a name="add-the-request-trigger"></a>Lägg till begär ande utlösare

1. Bredvid designern, i rutan **Lägg till utlösare** , under Sök i rutan **Välj en åtgärd** , kontrollerar du att **inbyggda** är markerat så att du kan välja en utlösare som körs internt.

1. I sökrutan **Välj en åtgärd** anger `when a http request` du och väljer den inbyggda begär ande utlösaren som är namngiven **när en http-begäran tas emot**.

   ![Skärm bild som visar arbets flödes utformaren och * * Lägg till en utlösare * * fönstret med alternativet "när en HTTP-förfrågan tas emot" är valt.](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   När utlösaren visas i designern öppnas utlösarens informations fönster för att Visa utlösarens egenskaper, inställningar och andra åtgärder.

   ![Skärm bild som visar arbets flödes utformaren med alternativet "när en HTTP-begäran tas emot" utlösare vald och utlösnings information öppen.](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Om informations fönstret inte visas ser du till att utlösaren är markerad i designern.

1. Om du behöver ta bort ett objekt från designern [följer du stegen nedan för att ta bort objekt från designern](#delete-from-designer).

### <a name="add-the-office-365-outlook-action"></a>Lägg till åtgärden Office 365 Outlook

1. Välj **nytt steg** under den utlösare som du har lagt till i designern.

   Frågan **Välj en åtgärd** visas i designern och fönstret **Lägg till en åtgärd** öppnas så att du kan välja nästa åtgärd.

1. I fönstret **Lägg till en åtgärd** går du till rutan **Välj en åtgärd** och väljer **Azure** så att du kan hitta och välja en åtgärd för en hanterad koppling som distribueras i Azure.

   Det här exemplet väljer och använder åtgärden Office 365 Outlook, **Skicka ett e-postmeddelande (v2)**.

   ![Skärm bild som visar arbets flödes design och * * Lägg till åtgärd * * fönstret med Office 365 Outlook "Skicka ett e-postmeddelande" valt.](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. I åtgärdens informations fönster väljer du **Logga** in så att du kan skapa en anslutning till ditt e-postkonto.

   ![Skärm bild som visar arbets flödes design och * * skicka ett e-postmeddelande (v2) * * fönstret med "logga in" markerat.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

1. När Visual Studio Code efterfrågar dig att du får åtkomst till ditt e-postkonto väljer du **Öppna**.

   ![Skärm bild som visar Visual Studio Code-prompten för att tillåta åtkomst.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > Om du vill förhindra framtida prompter väljer du **Konfigurera betrodda domäner** så att du kan lägga till sidan autentisering som en betrodd domän.

1. Följ de efterföljande anvisningarna för att logga in, tillåta åtkomst och tillåta att du återgår till Visual Studio Code.

   > [!NOTE]
   > Om tiden är för lång tid innan du slutför prompterna går det att göra en timeout och Miss lyckas med autentiseringsprocessen. I det här fallet går du tillbaka till designern och försöker logga in igen för att skapa anslutningen.

1. När tillägget Azure Logic Apps (förhands granskning) efterfrågar dig att du får åtkomst till ditt e-postkonto väljer du **Öppna**. Följ efterföljande prompt för att tillåta åtkomst.

   ![Skärm bild som visar utöknings meddelandet för förhands granskning för att tillåta åtkomst.](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > Om du vill förhindra framtida prompter väljer du **fråga inte igen för det här tillägget**.

   När Visual Studio Code skapar anslutningen visar vissa kopplingar meddelandet som `The connection will be valid for {n} days only` . Denna tids gräns gäller endast för varaktigheten när du redigerar din Logic app i Visual Studio Code. Efter distributionen gäller den här gränsen inte längre eftersom din Logi Kap par kan autentisera vid körning genom att använda den automatiskt aktiverade [systemtilldelade hanterade identiteten](../logic-apps/create-managed-service-identity.md). Den här hanterade identiteten skiljer sig från autentiseringsuppgifterna för autentisering eller anslutnings sträng som du använder när du skapar en anslutning. Om du inaktiverar den här systemtilldelade hanterade identiteten fungerar inte anslutningarna vid körning.

1. Om åtgärden **skicka e-post** inte visas i designern väljer du den åtgärden.

1. I åtgärdens informations fönster, på fliken **parametrar** , anger du nödvändig information för åtgärden, till exempel:

   ![Skärm bild som visar arbets flödes design med information för Office 365 Outlook "Skicka ett e-postmeddelande"-åtgärd.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Till** | Ja | <*din-e-postadress*> | E-postmottagaren, som kan vara din e-postadress i test syfte. I det här exemplet används det fiktiva e-postmeddelandet `sophiaowen@fabrikam.com` . |
   | **Ämne** | Ja | `An email from your example workflow` | E-postmeddelandets ämne |
   | **Brödtext** | Ja | `Hello from your example workflow!` | Innehållet i e-postmeddelandet |
   ||||

   > [!NOTE]
   > Om du vill göra ändringar i informations fönstret på fliken **Inställningar**, **statiskt resultat** eller **kör efter** , kontrollerar du att du väljer **genomför** ändringarna innan du växlar flikar eller byter fokus till designern. Annars behåller Visual Studio-koden inte dina ändringar. Mer information finns på sidan om [kända problem med Logic Apps offentliga för hands versioner i GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

1. I designern väljer du **Spara**.

> [!IMPORTANT]
> För att lokalt köra ett arbets flöde som använder en webhook-baserad utlösare eller åtgärder, t. ex. den [inbyggda HTTP-webhook-utlösaren eller åtgärden](../connectors/connectors-native-webhook.md), måste du aktivera den här funktionen genom att konfigurera [vidarebefordran för webhookens återanrops-URL](#webhook-setup).

<a name="webhook-setup"></a>

## <a name="enable-locally-running-webhooks"></a>Aktivera lokal körning av Webhooks

När du använder en webhook-baserad utlösare eller åtgärd, till exempel **http-webhook**, med en Logic-app som körs i Azure, prenumererar Logic Apps runtime på tjänst slut punkten genom att generera och registrera en återanrops-URL med den slut punkten. Utlösaren eller åtgärden väntar sedan på att tjänstens slut punkt ska anropa URL: en. Men när du arbetar i Visual Studio Code börjar den genererade återanrops-URL: en `http://localhost:7071/...` . Den här URL: en är för din localhost-Server, som är privat så att tjänstens slut punkt inte kan anropa denna URL.

För att lokalt köra webhook-baserade utlösare och åtgärder i Visual Studio Code, måste du konfigurera en offentlig URL som visar din localhost-Server och vidarebefordra samtal från tjänst slut punkten till webb adressen för återanrop i webhooken. Du kan använda en tjänst för vidarebefordran och ett verktyg som [**ngrok**](https://ngrok.com/), som öppnar en http-tunnel till din localhost-port, eller så kan du använda ditt eget verktyg.

#### <a name="set-up-call-forwarding-using-ngrok"></a>Konfigurera vidarekoppling av samtal med **ngrok**

1. [Registrera dig för ett **ngrok** -konto](https://dashboard.ngrok.com/signup) om du inte har något. Annars [loggar du in på ditt konto](https://dashboard.ngrok.com/login).

1. Hämta din personliga autentiseringstoken som **ngrok** -klienten behöver för att ansluta och autentisera åtkomst till ditt konto.

   1. Om du vill hitta [sidan för autentiseringstoken](https://dashboard.ngrok.com/auth/your-authtoken), expanderar du **autentisering** på menyn för instrument panelen och väljer **din authToken**.

   1. I rutan **din authToken** kopierar du token till en säker plats.

1. På [hämtnings sidan för **ngrok**](https://ngrok.com/download) eller [din konto instrument panel](https://dashboard.ngrok.com/get-started/setup), laddar du ned den **ngrok** -version som du vill ha och extraherar. zip-filen. Mer information finns i [steg 1: packa upp för att installera](https://ngrok.com/download).

1. Öppna kommando tolks verktyget på din dator. Bläddra till den plats där **ngrok.exes** filen finns.

1. Anslut **ngrok** -klienten till ditt **ngrok** -konto genom att köra följande kommando. Mer information finns i [steg 2: Anslut ditt konto](https://ngrok.com/download).

   `ngrok authtoken <your_auth_token>`

1. Öppna HTTP-tunneln till localhost port 7071 genom att köra följande kommando. Mer information finns i [steg 3: starta det](https://ngrok.com/download).

   `ngrok http 7071`

1. Leta upp följande rad från utdata:

   `http://<domain>.ngrok.io -> http://localhost:7071`

1. Kopiera och spara URL: en som har det här formatet: `http://<domain>.ngrok.io`

#### <a name="set-up-the-forwarding-url-in-your-app-settings"></a>Konfigurera vidarebefordrings-URL: en i dina app-inställningar

1. I Visual Studio Code, i designern, lägger du till **http + webhook-** utlösaren eller åtgärden.

1. När uppvarningen visas för värd slut punkten anger du den URL för vidarebefordran (omdirigering) som du skapade tidigare.

   > [!NOTE]
   > Om du ignorerar meddelandet visas en varning om att du måste ange URL: en för vidarebefordran, så välj **Konfigurera** och ange URL: en. När du har slutfört det här steget visas inte prompten igen för efterföljande webhook-utlösare eller åtgärder som du kan lägga till.
   >
   > Om du vill att prompten ska visas på projektets rotnivå öppnar du snabb menyn **local.settings.jspå** filen och väljer **Konfigurera webhook omdirigera slut punkt**. Frågan visas nu, så att du kan ange URL: en för vidarebefordran.

   Visual Studio Code lägger till vidarebefordrings-URL: en i **local.settings.js** filen i projektets rotmapp. I `Values` objektet visas egenskapen med namnet `Workflows.WebhookRedirectHostUri` nu och anges till URL för vidarebefordran, till exempel:
   
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

Första gången du startar en lokal felsökningssession eller kör arbets flödet utan fel sökning registrerar Logic Apps körningen arbets flödet med tjänstens slut punkt och prenumererar på den slut punkten för att meddela webhook-åtgärderna. Nästa gången som arbets flödet körs registreras eller omprenumereras inte körningen eftersom prenumerations registreringen redan finns i den lokala lagrings platsen.

När du stoppar felsökningssessionen för en arbets flödes körning som använder lokalt kör webhook-baserade utlösare eller åtgärder, raderas inte befintliga prenumerations registreringar. Om du vill avregistrera dig måste du manuellt ta bort eller ta bort prenumerations registreringarna.

> [!NOTE]
> När arbets flödet har börjat köras kan terminalfönstret Visa fel som det här exemplet:
>
> `message='Http request failed with unhandled exception of type 'InvalidOperationException' and message: 'System.InvalidOperationException: Synchronous operations are disallowed. Call ReadAsync or set AllowSynchronousIO to true instead.`
>
> Öppna i så fall **local.settings.js** filen i projektets rotmapp och se till att egenskapen är inställd på `true` :
>
> `"FUNCTIONS_V2_COMPATIBILITY_MODE": "true"`

<a name="manage-breakpoints"></a>

## <a name="manage-breakpoints-for-debugging"></a>Hantera Bryt punkter för fel sökning

Innan du kör och testar ditt Logic app-arbetsflöde genom att starta en felsökningssession kan du ange [Bryt punkter](https://code.visualstudio.com/docs/editor/debugging#_breakpoints) i **workflow.js** filen för varje arbets flöde. Ingen ytterligare konfiguration krävs. 

För närvarande stöds Bryt punkter endast för åtgärder, inte utlösare. Varje åtgärds definition har följande Bryt punkts platser:

* Ange start Bryt punkten på raden som visar åtgärdens namn. När den här Bryt punkten träffar under felsökningssessionen kan du granska åtgärdens indata innan de utvärderas.

* Ange slut punkten för den rad som visar åtgärdens avslutande klammerparentes (**}**). När den här Bryt punkten träffar under felsökningssessionen kan du granska åtgärdens resultat innan åtgärden slutförs.

Följ dessa steg om du vill lägga till en Bryt punkt:

1. Öppna filen **workflow.js** för det arbets flöde som du vill felsöka.

1. På den rad där du vill ange Bryt punkten går du till den vänstra kolumnen och väljer i kolumnen. Om du vill ta bort Bryt punkten väljer du den Bryt punkten.

   När du startar felsökningssessionen visas körnings läge till vänster i kod fönstret, medan verktygsfältet Felsök visas längst upp.

   > [!NOTE]
   > Om körnings vyn inte visas automatiskt trycker du på CTRL + SKIFT + D.

1. Om du vill granska tillgänglig information när en Bryt punkt träffar i körnings vyn, kontrollerar du fönstret **variabler** .

1. Fortsätt att köra arbets flödes körningen genom att välja **Fortsätt** (uppspelnings knapp) i verktygsfältet för fel sökning. 

Du kan lägga till och ta bort Bryt punkter när som helst under arbets flödes körningen. Men om du uppdaterar **workflow.jspå** filen när körningen har startat, uppdateras inte Bryt punkter automatiskt. Om du vill uppdatera Bryt punkterna startar du om Logic-appen.

Allmän information finns i [Bryt punkter – Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging#_breakpoints).

<a name="run-test-debug-locally"></a>

## <a name="run-test-and-debug-locally"></a>Kör, testa och Felsök lokalt

Testa din Logi Kap par genom att följa de här stegen för att starta en felsökningssession och hitta URL: en för den slut punkt som har skapats av begär ande utlösaren. Du behöver den här URL: en så att du senare kan skicka en begäran till den slut punkten.

1. Om du vill felsöka ett tillstånds lösa arbets flöde enklare kan du [Aktivera körnings historiken för det arbets flödet](#enable-run-history-stateless).

1. I aktivitets fältet i Visual Studio Code öppnar du menyn **Kör** och väljer **Starta fel sökning** (F5).

   **Terminalfönstret** öppnas så att du kan gå igenom felsökningssessionen.

   > [!NOTE]
   > Om du får felet "det **finns fel när du har kört preLaunchTask" generateDebugSymbols ""**, kan du läsa avsnittet fel sökning, det [går inte att starta en felsökningssession](#debugging-fails-to-start).

1. Nu hittar du återanrops-URL: en för slut punkten i utlösaren för begäran.

   1. Öppna fönstret Utforskaren igen så att du kan visa projektet.

   1. I snabb menyn **workflow.jspå** filen väljer du **Översikt**.

      ![Skärm bild som visar Explorer-fönstret och gen vägs fönstret för workflow.jspå filen med "Översikt" vald.](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. Hitta URL-värdet för **motringning** , som ser ut ungefär som den här URL: en för utlösaren exempel på begäran:

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![Skärm bild som visar din arbets flödes översikt sida med återanrops-URL](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. Om du vill testa återanrops-URL: en genom att utlösa Logic app-arbetsflödet öppnar du [Postman](https://www.postman.com/downloads/) eller önskat verktyg för att skapa och skicka förfrågningar.

   Detta exempel fortsätter med Postman. Mer information finns i [postman komma igång](https://learning.postman.com/docs/getting-started/introduction/).

   1. I verktygsfältet Postman väljer du **nytt**.

      ![Skärm bild som visar Postman med knappen Ny markerad](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. I fönstret **Skapa nytt** under **Bygg block** väljer du **begäran**.

   1. I fönstret **Spara begäran** , under **namn för begäran**, anger du ett namn för begäran, till exempel `Test workflow trigger` .

   1. Under **Välj en samling eller mapp att spara till** väljer du **skapa samling**.

   1. Under **alla samlingar** anger du ett namn för samlingen som ska skapas för att ordna dina begär Anden, trycker på RETUR och väljer **Spara till <*samlings namn* >**. I det här exemplet används `Logic Apps requests` som samlings namn.

      Postman fönster för begär ande öppnas så att du kan skicka en begäran till återanrops-URL: en för begäran utlösare.

      ![Skärm bild som visar Postman med fönstret öppen förfrågan](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Gå tillbaka till Visual Studio Code. på sidan Översikt för arbets flödet kopierar du värdet för **återanrops-URL** .

   1. Återgå till Postman. I fönstret begäran, bredvid listan metod **, som för närvarande visar som** standard metod för begäran, klistra in återanrops-URL: en som du tidigare kopierade i rutan adress och väljer **Skicka**.

      ![Skärm bild som visar Postman och återanrops-URL i rutan adress med knappen Skicka vald](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      Exempel på ett Logic app-arbetsflöde skickar ett e-postmeddelande som ser ut ungefär som i det här exemplet:

      ![Skärm bild som visar Outlook-e-post enligt beskrivningen i exemplet](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. Gå tillbaka till arbets flödets översikts sida i Visual Studio Code.

   Om du har skapat ett tillstånds känsligt arbets flöde, kommer sidan Översikt att Visa arbets flödets körnings status och historik när du har skickat begäran.

   > [!TIP]
   > Om körnings statusen inte visas kan du försöka att uppdatera översikts sidan genom att välja **Uppdatera**. Ingen körning sker för en utlösare som hoppas över på grund av ouppfyllda-villkor eller inga data hittas.

   ![Skärm bild som visar sidan för arbets flödets översikt med körnings status och historik](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   | Körnings status | Beskrivning |
   |------------|-------------|
   | **Avbruten** | Körningen stoppades eller slutfördes inte på grund av externa problem, till exempel ett system avbrott eller en upphörde Azure-prenumeration. |
   | **Avbröts** | Körningen utlöstes och startades men tog emot en begäran om annullering. |
   | **Misslyckades** | Minst en åtgärd i körningen misslyckades. Inga efterföljande åtgärder i arbets flödet har ställts in för att hantera det här problemet. |
   | **Körs** | Körningen utlöstes och pågår, men den här statusen kan också visas för en körning som är begränsad på grund av [Åtgärds gränser](logic-apps-limits-and-config.md) eller den [aktuella pris Planen](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Tips**: om du konfigurerar [diagnostikloggning](monitor-logic-apps-log-analytics.md)kan du få information om eventuella begränsnings händelser som inträffar. |
   | **Brutit** | Körningen lyckades. Om en åtgärd Miss lyckas, hanterar en efterföljande åtgärd i arbets flödet detta fel. |
   | **Tids gränsen uppnåddes** | Tids gränsen för körningen uppnåddes eftersom den aktuella varaktigheten överskred tids gränsen för körning, vilket styrs av inställningen för [ **kvarhållning av körnings historik i dagar**](logic-apps-limits-and-config.md#run-duration-retention-limits). Körningens varaktighet beräknas med hjälp av körningens start tid och tids gräns för körning vid den Start tiden. <p><p>**Obs!** om Körningens varaktighet också överskrider den aktuella *gränsen för körnings historik*, som också styrs av inställningen för [ **kvarhållning av körnings historik i dagar**](logic-apps-limits-and-config.md#run-duration-retention-limits), rensas körningen från körnings historiken med ett dagligt rensnings jobb. Oavsett om tids gränsen för körningen är slut eller slutförd beräknas alltid kvarhållningsperioden med hjälp av start tiden och den *aktuella* kvarhållningsperioden. Så om du minskar tids gränsen för en pågående körnings tid för en flygning. Körningen är dock antingen kvar eller så tas den bort från körnings historiken, baserat på om Körningens varaktighet överskred gränsen för kvarhållning. |
   | **Väntar** | Körningen har inte startat eller pausats, till exempel på grund av en tidigare arbets flödes instans som fortfarande körs. |
   |||

1. Om du vill granska status för varje steg i en speciell körning och stegets indata och utdata, väljer du knappen med tre punkter (**...**) för den här körningen och väljer **Visa kör**.

   ![Skärm bild som visar arbets flödets körnings historik rad med ellipser-knappen och "Visa körning" valt](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code öppnar vyn övervakning och visar status för varje steg i körningen.

   ![Skärm bild som visar varje steg i arbets flödets körning och deras status](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-action-status.png)

   > [!NOTE]
   > Om en körning misslyckades och ett steg i vyn övervakning visar `400 Bad Request` felet, kan det här problemet uppstå från ett längre Utlösar-namn eller åtgärds namn som gör att den underliggande Uniform Resource Identifier (URI) överskrider standard tecken gränsen. Mer information finns i ["400 Felaktig begäran"](#400-bad-request).

   Här är möjliga statusar som varje steg i arbets flödet kan ha:

   | Åtgärds status | Ikon | Beskrivning |
   |---------------|------|-------------|
   | **Avbruten** | ![Ikon för status för avbrutna åtgärder][aborted-icon] | Åtgärden stoppades eller avslutades inte på grund av externa problem, till exempel ett system avbrott eller en upphördende Azure-prenumeration. |
   | **Avbröts** | ![Ikon för status för avbrutna åtgärder][cancelled-icon] | Åtgärden kördes men tog emot en begäran att avbryta. |
   | **Misslyckades** | ![Ikon för åtgärds status misslyckades][failed-icon] | Det gick inte att utföra åtgärden. |
   | **Körs** | ![Ikon för åtgärds status som körs][running-icon] | Åtgärden körs för närvarande. |
   | **Överhoppad** | ![Ikon för åtgärds status "hoppar över"][skipped-icon] | Åtgärden hoppades över eftersom den direkt föregående åtgärden misslyckades. En åtgärd har ett `runAfter` villkor som kräver att föregående åtgärd har slutförts innan den aktuella åtgärden kan köras. |
   | **Brutit** | ![Ikon för status lyckad åtgärd][succeeded-icon] | Åtgärden har slutförts. |
   | **Lyckades med återförsök** | ![Ikon för åtgärds status slutförd med återförsök][succeeded-with-retries-icon] | Åtgärden lyckades men endast efter ett eller flera återförsök. Om du vill granska återförsöks historiken går du till vyn körnings historik och väljer den åtgärden så att du kan visa indata och utdata. |
   | **Tids gränsen uppnåddes** | ![Ikon för åtgärds status uppnåddes][timed-out-icon] | Åtgärden stoppades på grund av timeout-gränsen som anges i den åtgärdens inställningar. |
   | **Väntar** | ![Ikon för status för "väntande" åtgärd][waiting-icon] | Gäller för en webhook-åtgärd som väntar på en inkommande begäran från en anropare. |
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

   ![Skärm bild som visar status för varje steg i arbets flödet plus indata och utdata i den expanderade åtgärden "skicka e-post"](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. Om du vill granska rå data och utdata för det steget, väljer du **Visa rå data** eller **visar rå** data.

1. Om du vill stoppa felsökningssessionen väljer du **stoppa fel sökning** på menyn **Kör** (Shift + F5).

<a name="return-response"></a>

## <a name="return-a-response"></a>Returnera ett svar

Om du vill returnera ett svar till anroparen som skickade en begäran till din Logic app, kan du använda den inbyggda [svars åtgärden](../connectors/connectors-native-reqres.md) för ett arbets flöde som börjar med begär ande utlösaren.

1. Välj **nytt steg** under åtgärden **Skicka ett e-postmeddelande** i arbetsflödesdesignern.

   Frågan **Välj en åtgärd** visas i designern och **fönstret Lägg till en åtgärd** öppnas så att du kan välja nästa åtgärd.

1. I fönstret **Lägg till en åtgärd** , under sökrutan **Välj en åtgärd** , kontrollerar du att **inbyggt** är markerat. I rutan Sök anger du `response` och väljer **svars** åtgärden.

   ![Skärm bild som visar arbets flödes design med den valda svars åtgärden.](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   När **svars** åtgärden visas i designern öppnas åtgärdens informations fönster automatiskt.

   ![Skärm bild som visar arbets flödes utformaren med åtgärds informations fönstret för "Response" öppen och egenskapen "brödtext" har angetts till egenskap svärdet "Skicka ett e-postmeddelande"-åtgärd.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. På fliken **parametrar** anger du den information som krävs för den funktion som du vill anropa.

   I det här exemplet returneras **text** egenskap svärdet som matas från åtgärden **skicka e-post** .

   1. Klicka i egenskaps rutan för **brödtext** så att listan med dynamiskt innehåll visas och visar tillgängliga värden från föregående utlösare och åtgärder i arbets flödet.

      ![Skärm bild som visar åtgärds fönstret för Response-åtgärden med mus pekaren inuti egenskapen "Body" så att listan med dynamiskt innehåll visas.](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. I listan med dynamiskt innehåll under **Skicka ett e-postmeddelande** väljer du **brödtext**.

      ![Skärm bild som visar listan med öppna dynamiskt innehåll. I listan, under rubriken "Skicka ett e-postmeddelande", är utmatning svärdet "Body" markerat.](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      När du är klar har svars åtgärdens egenskap **Body** nu angetts till åtgärden **skicka en e-post-** åtgärds värde för **brödtext** .

      ![Skärm bild som visar status för varje steg i arbets flödet plus indata och utdata i den expanderade åtgärden "respons".](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. I designern väljer du **Spara**.

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>Testa om din Logic app

När du har gjort uppdateringar till din Logic app kan du köra ett annat test genom att köra fel söknings programmet i Visual Studio och skicka en annan begäran om att utlösa din uppdaterade Logic-app, på samma sätt som stegen i [köra, testa och felsöka lokalt](#run-test-debug-locally).

1. I aktivitets fältet i Visual Studio Code öppnar du menyn **Kör** och väljer **Starta fel sökning** (F5).

1. Skicka en annan begäran om att utlösa arbets flödet i Postman eller ditt verktyg för att skapa och skicka begär Anden.

1. Om du har skapat ett tillstånds känsligt arbets flöde kontrollerar du status för den senaste körningen på arbets flödets översikts sida. Om du vill visa status, indata och utdata för varje steg i den här körningen väljer du knappen med tre punkter (**...**) för den här körningen och väljer **Visa kör**.

   Här är till exempel steg för steg-status för en körning när exempel arbets flödet har uppdaterats med svars åtgärden.

   ![Skärm bild som visar status för varje steg i det uppdaterade arbets flödet plus indata och utdata i den expanderade åtgärden "respons".](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. Om du vill stoppa felsökningssessionen väljer du **stoppa fel sökning** på menyn **Kör** (Shift + F5).

<a name="deploy-azure"></a>

## <a name="deploy-to-azure"></a>Distribuera till Azure

Från Visual Studio Code kan du publicera ditt projekt direkt till Azure, som distribuerar din Logic app med hjälp av resurs typen ny **Logic app (förhands granskning)** . På samma sätt som för Function app-resursen i Azure Functions, kräver distributionen för den här nya resurs typen att du väljer en [värd plan och pris nivå](../app-service/overview-hosting-plans.md), som du kan konfigurera under distributionen. Läs följande avsnitt om du vill ha mer information om att vara värd för planer och priser:

* [Skala upp en i Azure App Service](../app-service/manage-scale-up.md)
* [Skala och var värd i Azure Functions](../azure-functions/functions-scale.md)

Du kan publicera din Logi Kap par som en ny resurs, som automatiskt skapar nödvändiga resurser, till exempel ett [Azure Storage konto, på samma sätt som funktions kraven för appen](../azure-functions/storage-considerations.md). Eller så kan du publicera din Logic app till en tidigare distribuerad **Logic app-resurs (för hands version)** , som skriver över den logiska appen.

### <a name="publish-to-a-new-logic-app-preview-resource"></a>Publicera till en ny Logic app-resurs (förhands granskning)

1. Välj Azure-ikonen i aktivitets fältet i Visual Studio Code.

1. I verktygsfältet **Azure: Logic Apps (för hands version)** väljer du **distribuera till Logic app**.

   ![Skärm bild som visar rutan "Azure: Logic Apps (för hands version)" och fönstrets verktygsfält med "distribuera till Logic app" vald.](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. Om du uppmanas väljer du den Azure-prenumeration som ska användas för din Logic app-distribution.

1. I listan som Visual Studio Code öppnas väljer du bland följande alternativ:

   * **Skapa ny Logic app (för hands version) i Azure** (snabb)
   * **Skapa ny Logic app (för hands version) i Azure Advanced**
   * En tidigare distribuerad **Logic app-resurs (för hands version)** , om sådan finns

   Det här exemplet fortsätter med **Skapa ny Logic app (för hands version) i Azure Advanced**.

   ![Skärm bild som visar fönstret "Azure: Logic Apps (för hands version)" med en lista med "Skapa ny Logic app (för hands version) i Azure" vald.](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. Följ dessa steg om du vill skapa en ny **Logic app-resurs (förhands granskning)** :

   1. Ange ett globalt unikt namn för din nya Logic app, vilket är det namn som ska användas för **Logic app (för hands version)** -resursen. I det här exemplet används `Fabrikam-Workflows-App`.

      ![Skärm bild som visar fönstret "Azure: Logic Apps (för hands version)" och en uppvarning för att ange ett namn för den nya Logic-appen att skapa.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. Välj en [värd plan](../app-service/overview-hosting-plans.md) för den nya Logic-appen, antingen [ **App Service plan** (dedikerat)](../azure-functions/dedicated-plan.md) eller [**Premium**](../azure-functions/functions-premium-plan.md).

      > [!IMPORTANT]
      > Förbruknings planer stöds inte eller är inte tillgängliga för den här resurs typen. Den valda planen påverkar de funktioner och pris nivåer som senare är tillgängliga för dig. Mer information finns i följande avsnitt: 
      >
      > * [Skala och var värd i Azure Functions](../azure-functions/functions-scale.md)
      > * [App Service pris information](https://azure.microsoft.com/pricing/details/app-service/)
      >
      > Premium-prenumerationen ger till exempel åtkomst till nätverksfunktioner, till exempel Anslut och integrera privat med virtuella Azure-nätverk, ungefär som Azure Functions när du skapar och distribuerar dina Logi Kap par. 
      > Mer information finns i följande avsnitt:
      > 
      > * [Nätverksalternativ för Azure Functions](../azure-functions/functions-networking-options.md)
      > * [Azure Logic Apps som kör nätverks möjligheter med Azure Logic Apps för hands version](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

      I det här exemplet används **App Service planen**.

      ![Skärm bild som visar fönstret "Azure: Logic Apps (för hands version)" och en prompt för att välja "App Service plan" eller "Premium".](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. Skapa en ny App Service plan eller Välj en befintlig plan. I det här exemplet väljer du **Skapa nytt App Service plan**.

      ![Skärm bild som visar fönstret "Azure: Logic Apps (för hands version)" och en uppvarning till "Skapa ny App Service plan" eller Välj en befintlig App Service plan.](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. Ange ett namn för din App Service plan och välj sedan en [pris nivå](../app-service/overview-hosting-plans.md) för planen. I det här exemplet väljs den **kostnads fria** prenumerationen F1.

      ![Skärm bild som visar fönstret "Azure: Logic Apps (för hands version)" och en uppvarning för att välja en pris nivå.](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. För optimala prestanda kan du söka efter och välja samma resurs grupp som ditt projekt för distributionen.

      > [!NOTE]
      > Även om du kan skapa eller använda en annan resurs grupp kan det påverka prestandan. Om du skapar eller väljer en annan resurs grupp, men om du avbryter när bekräftelse meddelandet visas, avbryts även distributionen.

   1. För tillstånds känsliga arbets flöden väljer du **Skapa nytt lagrings konto** eller ett befintligt lagrings konto.

      ![Skärm bild som visar fönstret "Azure: Logic Apps (för hands version)" och en uppsvars tolk för att skapa eller välja ett lagrings konto.](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. Om din Logic Apps-inställningar för skapande och distribution har stöd för att använda [Application Insights](../azure-monitor/app/app-insights-overview.md), kan du välja att aktivera diagnostikloggning och spårning för din Logic app. Du kan göra det antingen när du distribuerar din Logic app från Visual Studio Code eller efter distributionen. Du måste ha en Application Insights instans, men du kan skapa den här resursen [i förväg](../azure-monitor/app/create-workspace-resource.md)när du distribuerar din Logic app eller efter distributionen.

      Följ dessa steg om du vill aktivera loggning och spårning nu:

      1. Välj antingen en befintlig Application Insights resurs eller **skapa en ny Application Insights resurs**.

      1. I [Azure Portal](https://portal.azure.com)går du till din Application Insights-resurs.

      1. På resurs-menyn väljer du **Översikt**. Sök efter och kopiera **Instrumentation nyckel** värde.

      1. Öppna den **local.settings.js** filen i projektets rotmapp i Visual Studio Code.

      1. I `Values` objektet lägger du till `APPINSIGHTS_INSTRUMENTATIONKEY` egenskapen och anger värdet till Instrumentation-nyckeln, till exempel:

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
         > Du kan kontrol lera om Utlösar-och åtgärds namnen visas korrekt i Application Insights-instansen.
         >
         > 1. I Azure Portal går du till din Application Insights-resurs.
         >
         > 2. Välj **program karta** under **Undersök** på resurs resurs menyn.
         >
         > 3. Granska de åtgärds namn som visas i kartan.
         >
         > Vissa inkommande begär Anden från inbyggda utlösare kan visas som dubbletter i program kartan. 
         > I stället för att använda `WorkflowName.ActionName` formatet använder dessa dubbletter arbets flödes namnet som åtgärds namn och härstammar från Azure Functions-värden.

      1. Sedan kan du välja att ändra allvarlighets grad för de spårnings data som din Logic app samlar in och skickar till din Application Insights-instans.

         Varje gång en arbets flödes händelse inträffar, till exempel när ett arbets flöde utlöses eller när en åtgärd körs, avger körningen olika spår. Dessa spår omfattar arbets flödets livs längd och inkluderar, men är inte begränsat till, följande händelse typer:

         * Tjänste aktivitet, till exempel starta, stoppa och fel.
         * Jobb och dispatcher-aktivitet.
         * Arbets flödes aktivitet, till exempel utlösare, åtgärder och körning.
         * Aktivitet för lagrings förfrågan, till exempel lyckad eller misslyckad.
         * Aktivitet för HTTP-begäran, till exempel inkommande, utgående, lyckad och misslyckad.
         * Eventuella utvecklings spår, till exempel fel söknings meddelanden.

         Varje händelse typ tilldelas till en allvarlighets grad. Nivån fångar till exempel de `Trace` mest detaljerade meddelandena, medan `Information` nivån fångar in allmän aktivitet i arbets flödet, till exempel när din Logi Kap par, arbets flöde, utlösare och åtgärder startas och stoppas. I den här tabellen beskrivs allvarlighets graderna och deras spårnings typer:

         | Allvarlighetsgrad | Spårnings typ |
         |----------------|------------|
         | Kritiskt | Loggar som beskriver ett oåterkalleligt fel i din Logic app. |
         | Felsökning | Loggar som du kan använda för undersökning under utvecklingen, till exempel inkommande och utgående HTTP-anrop. |
         | Fel | Loggar som indikerar ett problem i arbets flödes körningen, men inte ett allmänt haveri i din Logic app. |
         | Information | Loggar som spårar den allmänna aktiviteten i din Logic app eller arbets flöde, till exempel: <p><p>– När en utlösare, åtgärd eller kör startar och slutar. <br>– När din Logic app startar eller slutar. |
         | Spårning | Loggar som innehåller de mest detaljerade meddelandena, till exempel lagrings begär Anden eller dispatcher-aktivitet, samt alla meddelanden som är relaterade till körnings aktivitet för arbets flöde. |
         | Varning | Loggar som markerar ett onormalt tillstånd i din Logic app, men som inte hindrar att det körs. |
         |||

         Om du vill ange allvarlighets grad på ditt projekts rotnivå öppnar du **host.jspå** filen och letar upp `logging` objektet. Det här objektet styr logg filtreringen för alla arbets flöden i din Logic app och följer [ASP.net Core layout för filtrering av loggnings typ](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&preserve-view=true#log-filtering).

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

         Om `logging` objektet inte innehåller ett `logLevel` objekt som innehåller `Host.Triggers.Workflow` egenskapen, lägger du till dessa objekt. Ange egenskapen till allvarlighets grad för den spårnings typ som du vill ha, till exempel:

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

   När du är klar med distributions stegen börjar Visual Studio Code att skapa och distribuera de resurser som krävs för att publicera din Logic app.

1. Om du vill granska och övervaka distributions processen väljer du **utdata** på **Visa** -menyn. I verktygsfältet utdata väljer du **Azure Logic Apps**.

   ![Skärm bild som visar utdatafönstret med "Azure Logic Apps" som marker ATS i listan verktygsfält tillsammans med distributionens förlopp och status.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   När Visual Studio Code har distribuerat din Logic app till Azure visas följande meddelande:

   ![Skärm bild som visar ett meddelande om att distributionen till Azure har slutförts.](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   Grattis, din Logi Kap par är nu Live i Azure och aktiverat som standard.

Sedan kan du lära dig hur du utför dessa uppgifter:

* [Lägg till ett tomt arbets flöde i projektet](#add-workflow-existing-project).

* [Hantera distribuerade Logic Apps i Visual Studio Code](#manage-deployed-apps-vs-code) eller med hjälp av [Azure Portal](#manage-deployed-apps-portal).

* [Aktivera körnings historik för tillstånds lösa arbets flöden](#enable-run-history-stateless).

* [Aktivera vyn övervakning i Azure Portal för en distribuerad Logic-app](#enable-monitoring).

<a name="add-workflow-existing-project"></a>

## <a name="add-blank-workflow-to-project"></a>Lägg till tomt arbets flöde i projektet

Du kan ha flera arbets flöden i ditt Logic Apps-projekt. Följ dessa steg om du vill lägga till ett tomt arbets flöde i projektet:

1. Välj Azure-ikonen i aktivitets fältet i Visual Studio Code.

1. I Azure-fönstret, bredvid **Azure: Logic Apps (för hands version)** väljer du **skapa arbets flöde** (ikon för Azure Logic Apps).

1. Välj den arbets flödes typ som du vill lägga till: **tillstånds känslig** eller **tillstånds lös**

1. Ange ett namn för arbets flödet.

När du är klar visas en ny arbetsflödesmall i projektet tillsammans med en **workflow.jspå** fil för arbets flödes definitionen.

<a name="manage-deployed-apps-vs-code"></a>

## <a name="manage-deployed-logic-apps-in-visual-studio-code"></a>Hantera distribuerade Logic Apps i Visual Studio Code

I Visual Studio Code kan du Visa alla distribuerade Logi Kap par i din Azure-prenumeration, oavsett om de är de ursprungliga **Logic Apps** eller om du har en resurs typ för **Logic app (för hands version)** och väljer aktiviteter som hjälper dig att hantera dessa Logic Apps. Men för att få åtkomst till båda resurs typerna behöver du både **Azure Logic Apps** och **Azure Logic Apps-tillägg (för hands version)** för Visual Studio Code.

1. Välj Azure-ikonen i det vänstra verktygsfältet. I fönstret **Azure: Logic Apps (förhands granskning)** expanderar du din prenumeration, som visar alla distribuerade Logic-appar för den prenumerationen.

1. Öppna den Logic-app som du vill hantera. Från den logiska appens snabb meny väljer du den uppgift som du vill utföra.

   Du kan till exempel välja uppgifter som att stoppa, starta, starta om eller ta bort din distribuerade Logic-app.

   ![Skärm bild som visar Visual Studio Code med anknytnings fönstret Azure Logic Apps (förhands granskning) och det distribuerade arbets flödet.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. Om du vill visa alla arbets flöden i Logic-appen expanderar du din Logic app och expanderar sedan noden **arbets flöden** .

1. Om du vill visa ett särskilt arbets flöde öppnar du arbets flödets snabb meny och väljer **Öppna i designer**, som öppnar arbets flödet i skrivskyddat läge.

   För att redigera arbets flödet har du följande alternativ:

   * I Visual Studio Code öppnar du projektets **workflow.jspå** filen i arbetsflödesdesignern, gör dina redigeringar och distribuerar om din Logic app till Azure.

   * [Leta upp och öppna din Logic app](#manage-deployed-apps-portal)i Azure Portal. Hitta, redigera och spara arbets flödet.

1. Öppna den distribuerade Logic-appen i Azure Portal genom att öppna den logiska appens snabb meny och välja **Öppna i portalen**.

   Azure Portal öppnas i webbläsaren, och du loggas in på portalen automatiskt om du är inloggad i Visual Studio Code och visar din Logic app.

   ![Skärm bild som visar Azure Portal sidan för din Logic app i Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   Du kan också logga in separat till Azure Portal, använda portalen Sök i rutan för att hitta din Logi Kap par och sedan välja din Logi Kap par i resultat listan.

   ![Skärm bild som visar Azure Portal och Sök fältet med Sök Resultat för distribuerad Logic-app, som visas markerad.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="manage-deployed-apps-portal"></a>

## <a name="manage-deployed-logic-apps-in-the-portal"></a>Hantera distribuerade Logic Apps i portalen

I Azure Portal kan du Visa alla distribuerade Logic-appar som finns i din Azure-prenumeration, oavsett om de är den ursprungliga **Logic Apps** resurs typen eller resurs typen **Logic app (för hands version)** . För närvarande organiseras och hanteras varje resurs typ som separata kategorier i Azure. Följ dessa steg om du vill hitta Logi Kap par som har resurs typen **Logic app (förhands granskning)** :

1. Skriv i rutan Azure Portal Sök `logic app preview` . När resultat listan visas väljer du **Logic app (för hands version)** under **tjänster**.

   ![Skärm bild som visar sökrutan Azure Portal med "Logic Apps Preview" Sök text.](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. I fönstret **Logic app (för hands version)** letar du reda på och väljer den Logic-app som du distribuerade från Visual Studio Code.

   ![Skärm bild som visar Azure Portal och den Logic app (för hands version) resurser som distribueras i Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   Azure Portal öppnar den enskilda resurs sidan för den valda Logic-appen.

   ![Skärm bild som visar ditt Logic app-arbetsflödes resurs sida i Azure Portal.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. Om du vill visa arbets flödena i den här Logic-appen väljer du **arbets flöden** på fliken Logic Apps.

   I fönstret **arbets flöden** visas alla arbets flöden i den aktuella Logic-appen. Det här exemplet visar det arbets flöde som du skapade i Visual Studio Code.

   ![Skärm bild som visar resurs sidan "Logic app (för hands version)" i fönstret "arbets flöden" öppen och det distribuerade arbets flödet](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. Om du vill visa ett arbets flöde väljer du det i **arbets** flödes fönstret.

   Fönstret arbets flöde öppnas och visar mer information och uppgifter som du kan utföra på det arbets flödet.

   Om du till exempel vill visa stegen i arbets flödet väljer du **Designer**.

   ![Skärm bild som visar det valda arbets flödets översikts fönster, medan arbets flödes menyn visar det valda design kommandot.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   Arbetsflödesdesignern öppnas och visar det arbets flöde som du skapade i Visual Studio Code. Nu kan du göra ändringar i det här arbets flödet i Azure Portal.

   ![Skärm bild som visar arbets flödes utformaren och arbets flödet som distribueras från Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflow-portal"></a>

## <a name="add-another-workflow-in-the-portal"></a>Lägg till ett annat arbets flöde i portalen

Du kan lägga till tomma arbets flöden till en **logisk app-resurs (för hands version)** som du har distribuerat från Visual Studio Code och skapa dessa arbets flöden i Azure Portal med hjälp av Azure Portal.

1. I [Azure Portal](https://portal.azure.com)letar du reda på och väljer din distribuerade **Logic app-resurs (för hands version)** .

1. På menyn Logic app väljer du **arbets flöden**. I fönstret **arbets flöden** väljer du **Lägg till**.

   ![Skärm bild som visar den valda Logic Apps-fönstret och verktygsfältet med kommandot Lägg till markerat.](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. Ange ett namn för arbets flödet i fönstret **nytt arbets flöde** . Välj antingen **tillstånds känslig** eller **tillstånds lös** **>** **skapa**.

   När Azure har distribuerat ditt nya arbets flöde, som visas i fönstret **arbets flöden** , väljer du det arbets flödet så att du kan hantera och utföra andra uppgifter, t. ex. öppna designern eller vyn kod.

   ![Skärm bild som visar det valda arbets flödet med alternativ för hantering och granskning.](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   Om du till exempel öppnar designern för ett nytt arbets flöde visas en tom arbets yta. Nu kan du bygga det här arbets flödet i Azure Portal.

   ![Skärm bild som visar Workflow Designer och ett tomt arbets flöde.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Aktivera körnings historik för tillstånds lösa arbets flöden

Om du vill felsöka ett tillstånds lösa arbets flöde enklare kan du aktivera körnings historiken för det arbets flödet och sedan inaktivera körnings historiken när du är klar. Följ de här stegen för Visual Studio Code, eller om du arbetar i Azure Portal, se [skapa tillstånds känsliga och tillstånds lösa arbets flöden i Azure Portal](create-stateful-stateless-workflows-azure-portal.md#enable-run-history-stateless).

1. I ditt Visual Studio Code-projekt expanderar du mappen **Workflow-designtime** och öppnar filen **local.settings.js** .

1. Lägg till `Workflows.{yourWorkflowName}.operationOptions` egenskapen och ange värdet till `WithStatelessRunHistory` , till exempel:

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

1. Om du vill inaktivera körnings historik när du är klar, anger du antingen `Workflows.{yourWorkflowName}.OperationOptions` egenskapen till `None` eller tar bort egenskapen och dess värde.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-view-in-the-azure-portal"></a>Aktivera vyn övervakning i Azure Portal

När du har distribuerat en **Logic app-resurs (förhands granskning)** från Visual Studio Code till Azure kan du granska alla tillgängliga körnings historik och information för ett arbets flöde i den resursen med hjälp av Azure Portal och **övervaknings** upplevelsen för det arbets flödet. Du måste dock först aktivera vyn **övervakare** för den logiska app-resursen.

1. I [Azure Portal](https://portal.azure.com)letar du reda på och väljer resursen distribuerad **Logic app (för hands version)** .

1. På den resurs menyn, under **API**, väljer du **CORS**.

1. Lägg till jokertecknet (*) under **tillåtna ursprung** i **CORS** -fönstret.

1. När du är klar väljer du **Spara** i verktygsfältet **CORS** .

   ![Skärm bild som visar Azure Portal med en distribuerad Logic app-resurs (förhands granskning). På resurs-menyn väljs "CORS" med en ny post för "tillåtna ursprung" på jokertecknet "*".](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Aktivera eller öppna Application Insights efter distribution

När arbets flödet körs, genererar din Logi Kap telemetri och andra händelser. Du kan använda den här Telemetrin för att få bättre insyn i hur väl ditt arbets flöde körs och hur Logic Apps runtime fungerar på olika sätt. Du kan övervaka arbets flödet med hjälp av [Application Insights](../azure-monitor/app/app-insights-overview.md), som tillhandahåller telemetri i nära real tid (Live metrics). Den här funktionen kan hjälpa dig att undersöka fel och prestanda problem enklare när du använder dessa data för att diagnostisera problem, konfigurera aviseringar och skapa diagram.

Om din Logic Apps-inställningar för skapande och distribution har stöd för att använda [Application Insights](../azure-monitor/app/app-insights-overview.md), kan du välja att aktivera diagnostikloggning och spårning för din Logic app. Du kan göra det antingen när du distribuerar din Logic app från Visual Studio Code eller efter distributionen. Du måste ha en Application Insights instans, men du kan skapa den här resursen [i förväg](../azure-monitor/app/create-workspace-resource.md)när du distribuerar din Logic app eller efter distributionen.

Följ dessa steg om du vill aktivera Application Insights i en distribuerad Logic-app eller granska Application Insights data när det redan har Aktiver ATS:

1. Leta upp din distribuerade Logic-app i Azure Portal.

1. På menyn Logic app, under **Inställningar**, väljer du **Application Insights**.

1. Om Application Insights inte är aktive rad väljer du **aktivera Application Insights** i fönstret **Application Insights** . När fönstret har uppdaterats väljer du **Använd** längst ned.

   Om Application Insights är aktive rad väljer du **visa Application Insights data** i fönstret **Application Insights** .

När Application Insights har öppnat kan du granska olika mått för din Logic app. Mer information finns i följande avsnitt:

* [Azure Logic Apps som körs överallt – övervakare med Application Insights del 1](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [Azure Logic Apps som körs överallt – övervakare med Application Insights del 2](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker"></a>Distribuera till Docker

Du kan distribuera din Logic app till en [Docker-behållare](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) som värd miljö med hjälp av [.net CLI](/dotnet/core/tools/). Med dessa kommandon kan du bygga och publicera ditt Logic Apps-projekt. Du kan sedan skapa och köra din Docker-behållare som mål för att distribuera din Logic app.

Om du inte är bekant med Docker kan du läsa följande avsnitt:

* [Vad är Docker?](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)
* [Introduktion till behållare och Docker](/dotnet/architecture/microservices/container-docker-introduction/)
* [Introduktion till .NET och Docker](/dotnet/core/docker/introduction)
* [Docker-behållare, bilder och register](/dotnet/architecture/microservices/container-docker-introduction/docker-containers-images-registries)
* [Självstudie: kom igång med Docker (Visual Studio Code)](/visualstudio/docker/tutorials/docker-tutorial)

### <a name="requirements"></a>Krav

* Det Azure Storage konto som din Logic app använder för distribution

* En Docker-fil för det arbets flöde som du använder när du skapar din Docker-behållare

  Exempel på Docker-filen distribuerar en Logic-app. Anger anslutnings strängen som innehåller åtkomst nyckeln för det Azure Storage konto som användes för att publicera Logic app till Azure Portal. Information om hur du hittar den här strängen finns i [Hämta lagrings kontots anslutnings sträng](#find-storage-account-connection-string).

   ```text
   FROM mcr.microsoft.com/azure-functions/node:3.0

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
       AzureFunctionsJobHost__Logging__Console__IsEnabled=true \
       FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY . /home/site/wwwroot

   RUN cd /home/site/wwwroot
   ```

   Mer information finns i [metod tips för att skriva Docker-filer](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

<a name="find-storage-account-connection-string"></a>

### <a name="get-storage-account-connection-string"></a>Hämta anslutnings sträng för lagrings konto

Innan du kan skapa och köra din Docker-behållar avbildning måste du hämta anslutnings strängen som innehåller åtkomst nyckeln till ditt lagrings konto. Tidigare skapade du det här lagrings kontot antingen för att använda tillägget på macOS eller Linux, eller när du distribuerade din Logic app till Azure Portal.

Följ dessa steg om du vill söka efter och kopiera den här anslutnings strängen:

1. I Azure Portal väljer du **åtkomst nycklar** under **Inställningar** på menyn lagrings konto. 

1. I fönstret **åtkomst nycklar** letar du reda på och kopierar lagrings kontots anslutnings sträng, som ser ut ungefär som i det här exemplet:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Skärm bild som visar Azure Portal med åtkomst nycklar för lagrings kontot och en anslutnings sträng som har kopierats.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   Mer information finns i [Hantera lagrings konto nycklar](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys).

1. Spara anslutnings strängen någon annan stans så att du kan lägga till den här strängen i Docker-filen som du använder för distribution. 

<a name="find-storage-account-master-key"></a>

### <a name="find-master-key-for-storage-account"></a>Sök efter huvud nyckel för lagrings konto

När arbets flödet innehåller en begär ande utlösare måste du [Hämta utlösarens återanrops-URL](#get-callback-url-request-trigger) när du har skapat och kört din Docker-behållar avbildning. För den här uppgiften måste du också ange huvud nyckel värdet för det lagrings konto som du använder för distribution.

1. Om du vill hitta huvud nyckeln öppnar du **Azure-WebJobs-hemligheter/{Deployment-Name}/host.jspå** filen i projektet.

1. Hitta `AzureWebJobsStorage` egenskapen och kopiera nyckelvärdet från det här avsnittet:

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

1. Spara det här nyckelvärdet på ett säkert sätt som du kan använda senare.

<a name="build-run-docker-container-image"></a>

### <a name="build-and-run-your-docker-container-image"></a>Skapa och kör din Docker-behållar avbildning

1. Bygg din Docker-behållar avbildning med hjälp av Docker-filen och kör följande kommando:

   `docker build --tag local/workflowcontainer .`

   Mer information finns i [Docker-build](https://docs.docker.com/engine/reference/commandline/build/).

1. Kör behållaren lokalt genom att använda det här kommandot:

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   Mer information finns i [Docker-körning](https://docs.docker.com/engine/reference/commandline/run/).

<a name="get-callback-url-request-trigger"></a>

### <a name="get-callback-url-for-request-trigger"></a>Hämta återanrops-URL för begär ande utlösare

För ett arbets flöde som använder begär ande utlösaren hämtar du utlösarens återanrops-URL genom att skicka den här begäran:

`POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2020-05-01-preview&code={master-key}`

`{trigger-name}`Värdet är namnet på den begär ande utlösare som visas i arbets flödets JSON-definition. `{master-key}`Värdet definieras i det Azure Storage konto som du har angett för `AzureWebJobsStorage` egenskapen i filen, **Azure-WebJobs-hemligheter/{Deployment-name}/host.jspå**. Mer information finns i [Sök efter lagrings kontots huvud nyckel](#find-storage-account-master-key).

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Ta bort objekt från designern

Följ dessa steg om du vill ta bort ett objekt i ditt arbets flöde från designern:

* Markera objektet, öppna objektets snabb meny (Shift + F10) och välj **ta bort**. Bekräfta genom att välja **OK**.

* Markera objektet och tryck på Delete-tangenten. Bekräfta genom att välja **OK**.

* Markera objektet så att informations fönstret öppnas för det objektet. Öppna ellipsen (**...**)-menyn i fönstrets övre högra hörn och välj **ta bort**. Bekräfta genom att välja **OK**.

  ![Skärm bild som visar ett markerat objekt i designern med det öppnade informations fönstret plus den valda ellips-knappen och kommandot "ta bort".](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

  > [!TIP]
  > Om menyn ellipser inte visas expanderar du Visual Studio Code-fönstret så att informations fönstret visar knappen med punkter (**...**) i det övre högra hörnet.

<a name="troubleshooting"></a>

## <a name="troubleshoot-errors-and-problems"></a>Felsök fel och problem

<a name="designer-fails-to-open"></a>

### <a name="designer-fails-to-open"></a>Det går inte att öppna designern

När du försöker öppna designern visas ett fel meddelande om **att det inte gick att starta arbets flödets design tid**. Om du tidigare har försökt öppna designern, sedan utgick eller tog bort ditt projekt, kanske inte tilläggs paketet laddas ned korrekt. Följ dessa steg om du vill kontrol lera om den här orsaken är problemet:

  1. Öppna fönstret utdata i Visual Studio Code. I menyn **Visa** väljer du **utdata**.

  1. Välj **Azure Logic Apps (för hands version)** i listan i utmatnings fönstrets namn List så att du kan granska utdata från tillägget, till exempel:

     ![Skärm bild som visar fönstret utdata med "Azure Logic Apps" markerat.](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

  1. Granska utdata och kontrol lera om det här fel meddelandet visas:

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

   Lös problemet genom att ta bort mappen **ExtensionBundles** på den här platsen **. ..\Users \{ your-username} \AppData\Local\Temp\Functions\ExtensionBundles** och försök **workflow.js** att öppna filen i designern igen.

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>Nya utlösare och åtgärder saknas i designer-väljaren för arbets flöden som skapats tidigare

Azure Logic Apps för hands versionen stöder inbyggda åtgärder för Azure Function Operations, flytande åtgärder och XML-åtgärder, till exempel **XML-verifiering** och **transformering av XML**. Men för tidigare skapade Logic Apps kanske dessa åtgärder inte visas i designer-väljaren för att välja om Visual Studio Code använder en inaktuell version av tilläggs paketet `Microsoft.Azure.Functions.ExtensionBundle.Workflows` .

Dessutom visas inte **Azure Functions** -anslutningsprogrammet och-åtgärder i designern om du inte har aktiverat eller valt **Använd anslutningar från Azure** när du skapade din Logic app. Om du inte har aktiverat Azure-distribuerade anslutningar när appen skapades kan du aktivera dem från projektet i Visual Studio Code. Öppna snabb menyn **workflow.jspå** och välj **Använd anslutningar från Azure**.

Åtgärda det inaktuella paketet genom att följa de här stegen för att ta bort det inaktuella paketet, som gör att Visual Studio Code automatiskt uppdaterar tilläggs paketet till den senaste versionen.

> [!NOTE]
> Den här lösningen gäller endast för Logic Apps som du skapar och distribuerar med hjälp av Visual Studio Code med tillägget Azure Logic Apps (för hands version), inte de Logic Apps som du skapade med Azure Portal Se [utlösare som stöds och åtgärder saknas i designern i Azure Portal](create-stateful-stateless-workflows-azure-portal.md#missing-triggers-actions).

1. Spara allt arbete som du inte vill förlora och Stäng Visual Studio.

1. På datorn bläddrar du till följande mapp som innehåller mappade mappar för det befintliga paketet:

   `...\Users\{your-username}\.azure-functions-core-tools\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Ta bort mappen version för det tidigare paketet, till exempel om du har en mapp för version 1.1.3 tar du bort mappen.

1. Bläddra nu till följande mapp som innehåller versions-mappar för det obligatoriska NuGet-paketet:

   `...\Users\{your-username}\.nuget\packages\microsoft.azure.workflows.webjobs.extension`

1. Ta bort mappen version för det tidigare paketet, till exempel om du har en mapp för version 1.0.0.8-Preview tar du bort mappen.

1. Öppna Visual Studio-koden igen, ditt projekt och **workflow.js** filen i designern.

De utlösare och åtgärder som saknas visas nu i designern.

<a name="400-bad-request"></a>

### <a name="400-bad-request-appears-on-a-trigger-or-action"></a>"400 Felaktig begäran visas i en utlösare eller åtgärd

När en körning Miss lyckas och du inspekterar vyn kör i övervakning, kan det här felet visas i en utlösare eller åtgärd som har ett längre namn, vilket gör att den underliggande Uniform Resource Identifier (URI) överskrider standard tecken gränsen.

För att lösa det här problemet och justera för längre URI, redigerar du- `UrlSegmentMaxCount` och- `UrlSegmentMaxLength` register nycklarna på datorn genom att följa stegen nedan. Nyckelns standardvärden beskrivs i det här avsnittet [Http.sys register inställningar för Windows](/troubleshoot/iis/httpsys-registry-windows).

> [!IMPORTANT]
> Innan du börjar ska du se till att spara ditt arbete. Den här lösningen kräver att du startar om datorn när du är klar så att ändringarna börjar gälla.

1. Öppna fönstret **Kör** på datorn och kör `regedit` kommandot som öppnar Registereditorn.

1. I rutan **User Account Control** väljer du **Ja** för att tillåta ändringarna på datorn.

1. I den vänstra rutan under **dator** expanderar du noderna längs sökvägen, **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters** och väljer sedan **parametrar**.

1. Leta upp register nycklarna i den högra `UrlSegmentMaxCount` rutan `UrlSegmentMaxLength` .

1. Öka de här nyckel värdena så att URI: erna kan hantera de namn som du vill använda. Om nycklarna inte finns lägger du till dem i mappen **parametrar** genom att följa dessa steg:

   1. Från snabb menyn **parametrar** väljer du **nytt**  >  **DWORD-värde (32-bitars)**.

   1. Ange `UrlSegmentMaxCount` som det nya nyckel namnet i redigerings rutan som visas.

   1. Öppna snabb menyn för den nya nyckeln och välj **ändra**.

   1. I rutan **Redigera sträng** som visas anger du det **värde** värdes nyckel värde som du vill ha i hexadecimalt eller decimalt format. `400`I hexadecimal till exempel motsvarar `1024` i decimal tal.

   1. Upprepa dessa steg om du vill lägga till ett `UrlSegmentMaxLength` nyckel värde.

   När du har ökat eller lagt till dessa nyckel värden ser Registereditorn ut som i det här exemplet:

   ![Skärm bild som visar Registereditorn.](media/create-stateful-stateless-workflows-visual-studio-code/edit-registry-settings-uri-length.png)

1. När du är klar startar du om datorn så att ändringarna kan börja gälla.

<a name="debugging-fails-to-start"></a>

### <a name="debugging-session-fails-to-start"></a>Det går inte att starta felsökningssessionen

När du försöker starta en felsökningssession får du ett fel meddelande om att det **redan finns ett fel när du har kört PreLaunchTask generateDebugSymbols**. Lös problemet genom att redigera **tasks.jspå** filen i projektet för att hoppa över symbol skapande.

1. I ditt projekt expanderar du **. VSCode** -mappen och öppnar filen **tasks.js** .

1. I följande uppgift tar du bort raden, `"dependsOn: "generateDebugSymbols"` tillsammans med det kommatecken som avslutar föregående rad, till exempel:

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

Vi vill gärna höra om dina upplevelser med tillägget Azure Logic Apps (förhands granskning)!

* [Skapa problem i GitHub](https://github.com/Azure/logicapps/issues)för buggar eller problem.
* [Använd det här formuläret](https://aka.ms/lafeedback)för frågor, förfrågningar, kommentarer och annan feedback.
