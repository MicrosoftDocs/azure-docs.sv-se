---
title: Automatisera uppgifter och arbets flöden med Visual Studio Code
description: Skapa eller redigera arbets flödes definitioner för Logic app med hjälp av Visual Studio Code (VS Code)
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, deli, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/28/2020
ms.openlocfilehash: f46c093bffcc26b5f7975d25ccaca648bdc527da
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99054694"
---
# <a name="quickstart-create-and-manage-logic-app-workflow-definitions-by-using-visual-studio-code"></a>Snabbstart: Skapa och hantera arbetsflödesdefinitioner för logikappar med hjälp av Visual Studio Code

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och Visual Studio Code, kan du skapa och hantera Logi Kap par som hjälper dig att automatisera uppgifter, arbets flöden och processer för att integrera appar, data, system och tjänster mellan organisationer och företag. Den här snabb starten visar hur du kan skapa och redigera de underliggande arbets flödes definitionerna, som använder JavaScript Object Notation (JSON) för Logic Apps genom en kod baserad upplevelse. Du kan också arbeta med befintliga Logic Apps som redan har distribuerats till Azure.

Även om du kan utföra samma uppgifter i [Azure Portal](https://portal.azure.com) och i Visual Studio, kan du komma igång snabbare i Visual Studio Code när du redan är bekant med Logic app-definitioner och vill arbeta direkt i kod. Du kan till exempel inaktivera, aktivera, ta bort och uppdatera redan skapade Logic Apps. Du kan också arbeta med Logic Apps och integrations konton från valfri utvecklings plattform där Visual Studio Code körs, till exempel Linux, Windows och Mac.

I den här artikeln kan du skapa samma Logic-app från den här [snabb](../logic-apps/quickstart-create-first-logic-app-workflow.md)starten, vilket fokuserar på de grundläggande begreppen. Du kan också [lära dig att skapa exempel appen i Visual Studio](quickstart-create-logic-apps-with-visual-studio.md)och [lära dig att skapa och hantera appar via Azure Command-Line Interface (Azure CLI)](quickstart-logic-apps-azure-cli.md). I Visual Studio Code ser den logiska appen ut som det här exemplet:

![Exempel på logik app Workflow-definition](./media/quickstart-create-logic-apps-visual-studio-code/visual-studio-code-overview.png)

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du kontrollera att du har följande:

* Om du inte har ett Azure-konto och en prenumeration kan du [Registrera dig för ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/).

* Grundläggande kunskaper om [Logic app Workflow-definitioner](../logic-apps/logic-apps-workflow-definition-language.md) och deras struktur enligt beskrivningen med JSON

  Om du inte har använt Logic Apps kan du prova den här [snabb](../logic-apps/quickstart-create-first-logic-app-workflow.md)starten, vilket skapar dina första Logi Kap par i Azure Portal och fokuserar på de grundläggande begreppen.

* Åtkomst till webben för att logga in på Azure och din Azure-prenumeration

* Hämta och installera följande verktyg, om du inte redan har dem:

  * [Visual Studio Code version 1.25.1 eller senare](https://code.visualstudio.com/), som är kostnads fri

  * Visual Studio Code-tillägg för Azure Logic Apps

    Du kan hämta och installera det här tillägget från [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) eller direkt inifrån Visual Studio Code. Se till att du läser in Visual Studio Code igen efter installationen.

    ![Hitta Azure Logic Apps "Visual Studio Code Extension för"](./media/quickstart-create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Kontrol lera att tillägget är korrekt installerat genom att välja den Azure-ikon som visas i ditt Visual Studio Code-verktygsfält.

    ![Tillägget bekräfta är korrekt installerat](./media/quickstart-create-logic-apps-visual-studio-code/confirm-installed-visual-studio-code-extension.png)

    Mer information finns i [tillägg Marketplace](https://code.visualstudio.com/docs/editor/extension-gallery). Om du vill bidra till den här tilläggets version av öppen källkod går du till [Azure Logic Apps tillägget för Visual Studio Code på GitHub](https://github.com/Microsoft/vscode-azurelogicapps).

* Om din Logi Kap par behöver kommunicera via en brand vägg som begränsar trafik till vissa IP-adresser, måste brand väggen tillåta åtkomst  för både [inkommande](logic-apps-limits-and-config.md#inbound) och [utgående](logic-apps-limits-and-config.md#outbound) ip-adresser som används av Logic Apps tjänst eller körning i den Azure-region där din Logic app finns. Om din Logic app även använder [hanterade anslutningar](../connectors/apis-list.md#managed-api-connectors), till exempel Office 365 Outlook Connector eller SQL-anslutning, eller använder [anpassade anslutningar](/connectors/custom-connectors/), måste brand väggen också tillåta åtkomst för *alla* [utgående IP-adresser för hanterad anslutning](logic-apps-limits-and-config.md#outbound) i din Logic app Azure-region.

<a name="access-azure"></a>

## <a name="access-azure-from-visual-studio"></a>Få åtkomst till Azure från Visual Studio

1. Öppna Visual Studio Code. Välj Azure-ikonen i verktygsfältet Visual Studio Code.

   ![Välj Azure-ikon i Visual Studio Code-verktygsfältet](./media/quickstart-create-logic-apps-visual-studio-code/open-extensions-visual-studio-code.png)

1. I Azure-fönstret, under **Logic Apps**, väljer **du logga in på Azure**. När du uppmanas att logga in med ditt Azure-konto när du uppmanas att logga in på Microsoft.

   ![Välj "logga in på Azure"](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-azure-visual-studio-code.png)

   1. Om det tar längre tid än vanligt att logga in i Visual Studio Code, uppmanas du att logga in via en Microsoft-webbplats för autentisering genom att tillhandahålla en enhets kod. Om du vill logga in med koden i stället väljer du **Använd enhets kod**.

      ![Fortsätt med enhets kod i stället](./media/quickstart-create-logic-apps-visual-studio-code/use-device-code-prompt.png)

   1. Om du vill kopiera koden väljer du **kopiera & öppna**.

      ![Kopiera kod för Azure-inloggning](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-prompt-authentication.png)

   1. Öppna ett nytt webbläsarfönster och fortsätt till webbplatsen för autentisering genom att välja **Öppna länk**.

      ![Bekräfta att öppna en webbläsare och gå till webbplatsen för autentisering](./media/quickstart-create-logic-apps-visual-studio-code/confirm-open-link.png)

   1. På sidan **Logga in på ditt konto** anger du din autentiseringsnyckel och väljer **Nästa**.

      ![Ange autentiseringsnyckel för Azure-inloggning](./media/quickstart-create-logic-apps-visual-studio-code/authentication-code-azure-sign-in.png)

1. Välj ditt Azure-konto. När du har loggat in kan du stänga webbläsaren och återgå till Visual Studio Code.

   I Azure-fönstret visar avsnitten **Logic Apps** och **integrations konton** nu de Azure-prenumerationer som är associerade med ditt konto. Men om du inte ser de prenumerationer som du förväntar dig, eller om avsnitten visar för många prenumerationer, följer du dessa steg:

   1. Flytta pekaren över **Logic Apps** etiketten. När verktygsfältet visas väljer du **Välj prenumerationer** (filter ikon).

      ![Hitta eller filtrera Azure-prenumerationer](./media/quickstart-create-logic-apps-visual-studio-code/find-or-filter-subscriptions.png)

   1. I listan som visas väljer du de prenumerationer som du vill ska visas.

1. Under **Logic Apps** väljer du den prenumeration som du vill använda. Noden prenumeration expanderar och visar alla Logic Apps som finns i den prenumerationen.

   ![Välj din Azure-prenumeration](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-subscription.png)

   > [!TIP]
   > Under **integrations konton** visar val av prenumeration alla integrerings konton som finns i den prenumerationen.

<a name="create-logic-app"></a>

## <a name="create-new-logic-app"></a>Skapa ny Logic-app

1. Om du inte har loggat in på ditt Azure-konto och din prenumeration än i Visual Studio Code, följer du [stegen ovan för att logga in nu](#access-azure).

1. I Visual Studio Code, under **Logic Apps**, öppnar du din prenumerations snabb meny och väljer **skapa Logic app**.

   ![Från menyn prenumeration väljer du "skapa Logic app"](./media/quickstart-create-logic-apps-visual-studio-code/create-logic-app-visual-studio-code.png)

   En lista visas och visar alla Azure-resurs grupper i din prenumeration.

1. I listan resurs grupp väljer du antingen **skapa en ny resurs grupp** eller en befintlig resurs grupp. I det här exemplet skapar du en ny resurs grupp.

   ![Skapa en ny Azure-resursgrupp](./media/quickstart-create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Ange ett namn för Azure-resurs gruppen och tryck på RETUR.

   ![Ange ett namn för din Azure-resurs grupp](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Välj den Azure-region där du vill spara din Logic app-metadata.

   ![Välj Azure-plats för att spara metadata för Logic app](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-location-new-resources.png)

1. Ange ett namn för din Logic app och tryck på RETUR.

   ![Ange namn på din Logic app](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   I Azure-fönstret, under din Azure-prenumeration, visas din nya och tomma Logic-app. Visual Studio Code öppnar också en JSON-fil (.logicapp.jspå), som innehåller en Skeleton-arbetsflöde för din Logic app. Nu kan du börja redigera din Logic Apps arbets flödes definition i den här JSON-filen. Teknisk referens om struktur och syntax för en arbets flödes definition finns i [språk schema för arbets flödes definition för Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md).

   ![Töm JSON-filen definition för Logic app-arbetsflöde](./media/quickstart-create-logic-apps-visual-studio-code/empty-logic-app-workflow-definition.png)

   Här är till exempel en exempel arbets flödes definition för en exempel logik som börjar med en RSS-utlösare och en Outlook 365 Outlook-åtgärd. Normalt visas JSON-element i alfabetisk ordning i varje avsnitt. Det här exemplet visar dock dessa element ungefär i den ordning som Logic Apps-stegen visas i designern.

   > [!IMPORTANT]
   > Om du vill återanvända den här exempel definitionen av Logic-appen behöver du ett organisations konto, till exempel @fabrikam.com . Se till att du ersätter den fiktiva e-postadressen med din egen e-postadress. Om du vill använda en annan e-postkoppling, till exempel Outlook.com eller Gmail, ersätter du `Send_an_email_action` åtgärden med en liknande åtgärd som är tillgänglig från en [e-postanslutning som stöds av Azure Logic Apps](../connectors/apis-list.md).
   >
   > Om du vill använda Gmail Connector kan endast företags konton i G-Suite använda den här anslutningen utan begränsning i Logic Apps. 
   > Om du har ett Gmail-konto kan du använda den här anslutningen med endast vissa Google-godkända tjänster, eller så kan du [skapa en Google-klient som används för autentisering med din Gmail-anslutning](/connectors/gmail/#authentication-and-bring-your-own-application). 
   > Mer information finns i [principer för data säkerhet och sekretess för Google Connectors i Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

   ```json
   {
      "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
         "$connections": {
            "defaultValue": {},
            "type": "Object"
         }
      },
      "triggers": {
         "When_a_feed_item_is_published": {
            "recurrence": {
               "frequency": "Minute",
               "interval": 1
            },
            "splitOn": "@triggerBody()?['value']",
            "type": "ApiConnection",
            "inputs": {
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['rss']['connectionId']"
                  }
               },
               "method": "get",
               "path": "/OnNewFeed",
               "queries": {
                  "feedUrl": "http://feeds.reuters.com/reuters/topNews"
               }
            }
         }
      },
      "actions": {
         "Send_an_email_(V2)": {
            "runAfter": {},
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "<p>Title: @{triggerBody()?['title']}<br>\n<br>\nDate published: @{triggerBody()?['updatedOn']}<br>\n<br>\nLink: @{triggerBody()?['primaryLink']}</p>",
                  "Subject": "RSS item: @{triggerBody()?['title']}",
                  "To": "sophia-owen@fabrikam.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/v2/Mail"
            }
         }
      },
      "outputs": {}
   }
   ```

1. När du är klar sparar du din Logic Apps arbets flödes definition. (Arkiv-menyn > Spara eller tryck på CTRL + S)

1. När du uppmanas att ladda upp din Logic app till din Azure-prenumeration väljer du **överför**.

   Det här steget publicerar din Logic app till [Azure Portal](https://portal.azure.com), som och gör din logik Live och körs i Azure.

   ![Ladda upp ny Logic app till din Azure-prenumeration](./media/quickstart-create-logic-apps-visual-studio-code/upload-new-logic-app.png)

## <a name="view-logic-app-in-designer"></a>Visa Logic app i designern

I Visual Studio Code kan du öppna din Logic app i skrivskyddad designvy. Även om du inte kan redigera din Logic app i designern kan du visuellt kontrol lera din Logic Apps-arbetsflöde med hjälp av designvyn.

I Azure-fönstret, under **Logic Apps**, öppnar du din Logic Apps snabb meny och väljer **Öppna i designer**.

Den skrivskyddade designern öppnas i ett separat fönster och visar ditt Logic Apps-arbetsflöde, till exempel:

![Visa Logic app i skrivskyddad designer](./media/quickstart-create-logic-apps-visual-studio-code/logic-app-designer-view.png)

## <a name="view-in-azure-portal"></a>Visa i Azure-portalen

Följ dessa steg om du vill granska din Logic app i Azure Portal:

1. Logga in på [Azure Portal](https://portal.azure.com) med samma Azure-konto och prenumeration som är associerad med din Logic app.

1. Ange namnet på din Logi Kap par i sökrutan Azure Portal. I listan resultat väljer du din Logic app.

   ![Din nya Logic-app i Azure Portal](./media/quickstart-create-logic-apps-visual-studio-code/published-logic-app-in-azure.png)

<a name="disable-enable-logic-app"></a>

## <a name="disable-or-enable-logic-app"></a>Inaktivera eller aktivera logikappen

Om du redigerar en publicerad Logic-app och sparar dina ändringar i Visual Studio Code, *skriver du över* din redan distribuerade app. För att undvika att du bryter din Logic app i produktion och minimerar avbrott kan du inaktivera din Logic app först. Du kan sedan återaktivera din Logic-app när du har bekräftat att din Logic app fortfarande fungerar.

1. Om du inte har loggat in på ditt Azure-konto och din prenumeration än i Visual Studio Code, följer du [stegen ovan för att logga in nu](#access-azure).

1. I Azure-fönstret, under **Logic Apps**, expanderar du din Azure-prenumeration så att du kan visa alla Logic Apps i den prenumerationen.

   1. Om du vill inaktivera den logiska appen som du vill ha öppnar du menyn Logic Apps och väljer **inaktivera**.

      ![Inaktivera din Logic app](./media/quickstart-create-logic-apps-visual-studio-code/disable-published-logic-app.png)

   1. När du är redo att återaktivera din Logic-App öppnar du menyn för Logic-appen och väljer **Aktivera**.

      ![Aktivera din Logic app](./media/quickstart-create-logic-apps-visual-studio-code/enable-published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-deployed-logic-app"></a>Redigera distribuerad Logic-app

I Visual Studio Code kan du öppna och redigera arbets flödes definitionen för en redan distribuerad Logic-app i Azure.

> [!IMPORTANT] 
> Innan du redigerar en aktivt logisk app i produktion bör du undvika risken för att kunna dela upp den Logic appen och minimera störningar genom [att först inaktivera din Logic app](#disable-enable-logic-app).

1. Om du inte har loggat in på ditt Azure-konto och din prenumeration än i Visual Studio Code, följer du [stegen ovan för att logga in nu](#access-azure).

1. I Azure-fönstret, under **Logic Apps**, expanderar du din Azure-prenumeration och väljer den logiska app som du vill använda.

1. Öppna din Logic Apps-meny och välj **Öppna i redigeraren**. Eller Välj redigerings ikonen bredvid namnet på din Logic Apps.

   ![Öppna redigerings programmet för befintlig Logic app](./media/quickstart-create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Visual Studio Code öppnar .logicapp.jsfilen i din lokala tillfälliga mapp så att du kan visa din Logic Apps arbets flödes definition.

   ![Visa arbets flödes definition för den publicerade Logic-appen](./media/quickstart-create-logic-apps-visual-studio-code/edit-published-logic-app-workflow-definition.png)

1. Gör dina ändringar i Logic Apps arbets flödes definition.

1. När du är klar kan du spara ändringarna. (Arkiv-menyn > Spara eller tryck på CTRL + S)

1. När du uppmanas att ladda upp dina ändringar och *skriva över* din befintliga Logic-app i Azure Portal väljer du **överför**.

   Det här steget publicerar dina uppdateringar till din Logic app i [Azure Portal](https://portal.azure.com).

   ![Ladda upp redigeringar till Logic app-definition i Azure](./media/quickstart-create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="view-or-promote-other-versions"></a>Visa eller flytta upp andra versioner

I Visual Studio Code kan du öppna och granska tidigare versioner för din Logic app. Du kan också befordra en tidigare version till den aktuella versionen.

> [!IMPORTANT] 
> Innan du ändrar en aktivt igång Logic app i produktion bör du undvika risken för att kunna dela upp den logiska appen och minimera störningar genom [att först inaktivera din Logic app](#disable-enable-logic-app).

1. I Azure-fönstret, under **Logic Apps**, expanderar du din Azure-prenumeration så att du kan visa alla Logic Apps i den prenumerationen.

1. Under prenumerationen expanderar du din Logic app och expanderar **versioner**.

   I listan **versioner** visas din Logic Apps tidigare versioner, om sådana finns.

   ![Din Logic Apps tidigare versioner](./media/quickstart-create-logic-apps-visual-studio-code/view-previous-versions.png)

1. Om du vill visa en tidigare version väljer du något av stegen:

   * Om du vill visa JSON-definitionen under **versioner** väljer du versions numret för den definitionen. Eller så öppnar du den här versionens snabb meny och väljer **Öppna i redigeraren**.

     En ny fil öppnas på den lokala datorn och visar den versionens JSON-definition.

   * Om du vill visa versionen i vyn skrivskyddad designer öppnar du den här versionens snabb meny och väljer **Öppna i designer**.

1. Följ dessa steg om du vill uppgradera en tidigare version till den aktuella versionen:

   1. Under **versioner** öppnar du snabb menyn för den tidigare versionen och väljer **befordra**.

      ![Höj upp tidigare version](./media/quickstart-create-logic-apps-visual-studio-code/promote-earlier-version.png)

   1. Välj **Ja** om du vill fortsätta efter att du uppmanas att bekräfta i Visual Studio-kod.

      ![Bekräfta befordran av tidigare version](./media/quickstart-create-logic-apps-visual-studio-code/confirm-promote-version.png)

      Visual Studio Code befordrar den valda versionen till den aktuella versionen och tilldelar en ny siffra till den uppgraderade versionen. Den tidigare aktuella versionen visas nu under den uppgraderade versionen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa tillstånds känsliga och tillstånds lösa Logic Apps i Visual Studio Code (för hands version)](../logic-apps/create-stateful-stateless-workflows-visual-studio-code.md)
