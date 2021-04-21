---
title: Automatisera uppgifter och arbetsflöden med Visual Studio Code
description: Skapa eller redigera arbetsflödesdefinitioner för logikappen med Visual Studio Code (VS Code)
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, deli, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2021
ms.openlocfilehash: 03cfb49dabd5039453f84ef0e636d3948af70d8d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764845"
---
# <a name="quickstart-create-and-manage-logic-app-workflow-definitions-by-using-visual-studio-code"></a>Snabbstart: Skapa och hantera arbetsflödesdefinitioner för logikappar med hjälp av Visual Studio Code

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och Visual Studio Code kan du skapa och hantera logikappar som hjälper dig att automatisera uppgifter, arbetsflöden och processer för att integrera appar, data, system och tjänster i organisationer och företag. Den här snabbstarten visar hur du kan skapa och redigera de underliggande arbetsflödesdefinitionerna, som använder JavaScript Object Notation (JSON) för logikappar via en kodbaserad upplevelse. Du kan också arbeta med befintliga logikappar som redan har distribuerats till Azure.

Även om du kan utföra samma uppgifter i [Azure Portal](https://portal.azure.com) och i Visual Studio kan du komma igång snabbare i Visual Studio Code när du redan är bekant med logikappdefinitioner och vill arbeta direkt i kod. Du kan till exempel inaktivera, aktivera, ta bort och uppdatera redan skapade logikappar. Du kan också arbeta med logikappar och integrationskonton från valfri utvecklingsplattform där Visual Studio Code körs, till exempel Linux, Windows och Mac.

I den här artikeln kan du skapa samma logikapp från den här [snabbstarten,](../logic-apps/quickstart-create-first-logic-app-workflow.md)som fokuserar mer på grundläggande begrepp. Du kan också [lära dig](quickstart-create-logic-apps-with-visual-studio.md)att skapa exempelappen i Visual Studio och lära dig att skapa och hantera appar via Azure Command-Line Interface [(Azure CLI).](quickstart-logic-apps-azure-cli.md) I Visual Studio Code ser logikappen ut som i det här exemplet:

![Exempel på arbetsflödesdefinition för logikapp](./media/quickstart-create-logic-apps-visual-studio-code/visual-studio-code-overview.png)

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du kontrollera att du har följande:

* Om du inte har ett Azure-konto och -prenumeration kan [du registrera dig för ett kostnadsfritt Azure-konto.](https://azure.microsoft.com/free/)

* Grundläggande kunskaper om [arbetsflödesdefinitioner för logikapp](../logic-apps/logic-apps-workflow-definition-language.md) och deras struktur enligt beskrivningen i JSON

  Om du inte har börjat Logic Apps den här [snabbstarten](../logic-apps/quickstart-create-first-logic-app-workflow.md), som skapar dina första logikappar i Azure Portal fokuserar mer på de grundläggande begreppen.

* Åtkomst till webben för att logga in på Azure och din Azure-prenumeration

* Hämta och installera följande verktyg, om du inte redan har dem:

  * [Visual Studio Code version 1.25.1 eller senare](https://code.visualstudio.com/), som är kostnadsfri

  * Visual Studio Code-tillägg för Azure Logic Apps

    Du kan ladda ned och installera det här [tillägget från Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) eller direkt från Visual Studio Code. Se till att du läser Visual Studio kod igen efter installationen.

    ![Hitta "Visual Studio Code-tillägg för Azure Logic Apps"](./media/quickstart-create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Kontrollera att tillägget har installerats korrekt genom att välja Azure-ikonen som visas i verktygsfältet Visual Studio Code.

    ![Bekräfta att tillägget är korrekt installerat](./media/quickstart-create-logic-apps-visual-studio-code/confirm-installed-visual-studio-code-extension.png)

    Mer information finns i [Marketplace-tillägg.](https://code.visualstudio.com/docs/editor/extension-gallery) Om du vill bidra till tilläggets version med öppen källkod går du [till Azure Logic Apps för att Visual Studio Code på GitHub.](https://github.com/Microsoft/vscode-azurelogicapps)

* Om logikappen behöver kommunicera via en brandvägg som begränsar trafiken till specifika  IP-adresser måste brandväggen tillåta åtkomst för både [inkommande](logic-apps-limits-and-config.md#inbound) och utgående [IP-adresser](logic-apps-limits-and-config.md#outbound) som används av Logic Apps-tjänsten eller körningen i Azure-regionen där logikappen finns. Om logikappen även använder hanterade anslutningsappar, till exempel Office 365 Outlook-anslutningsappen eller SQL-anslutningsappen, eller använder anpassade anslutningsappar, måste brandväggen också tillåta åtkomst för alla utgående [IP-adresser](logic-apps-limits-and-config.md#outbound) för hanterade anslutningsappar i logikappens Azure-region. [](../connectors/managed.md) [](/connectors/custom-connectors/) 

<a name="access-azure"></a>

## <a name="access-azure-from-visual-studio"></a>Få åtkomst till Azure från Visual Studio

1. Öppna Visual Studio Code. I verktygsfältet Visual Studio Code väljer du Azure-ikonen.

   ![Välj Azure-ikonen Visual Studio kodverktygsfältet](./media/quickstart-create-logic-apps-visual-studio-code/open-extensions-visual-studio-code.png)

1. I Azure-fönstret, under **Logic Apps** väljer du **Logga in på Azure**. Logga in med ditt Azure-konto när du uppmanas att logga in på Microsofts inloggningssida.

   ![Välj "Logga in på Azure"](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-azure-visual-studio-code.png)

   1. Om inloggningen tar längre tid än vanligt Visual Studio Code dig att logga in via en webbplats för Microsoft-autentisering genom att ange en enhetskod. Om du vill logga in med koden i stället väljer **du Använd enhetskod.**

      ![Fortsätt med enhetskod i stället](./media/quickstart-create-logic-apps-visual-studio-code/use-device-code-prompt.png)

   1. Om du vill kopiera koden väljer du **Kopiera & Öppna**.

      ![Kopiera kod för Azure-inloggning](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-prompt-authentication.png)

   1. Om du vill öppna ett nytt webbläsarfönster och fortsätta till autentiseringswebbplatsen väljer du **Öppna länk.**

      ![Bekräfta att du öppnar en webbläsare och går till autentiseringswebbplatsen](./media/quickstart-create-logic-apps-visual-studio-code/confirm-open-link.png)

   1. På sidan **Logga in på ditt konto** anger du din autentiseringskod och väljer **Nästa.**

      ![Ange autentiseringskod för Azure-inloggning](./media/quickstart-create-logic-apps-visual-studio-code/authentication-code-azure-sign-in.png)

1. Välj ditt Azure-konto. När du har loggat in kan du stänga webbläsaren och gå tillbaka till Visual Studio Code.

   I azure-fönstret visar **avsnitten Logic Apps** och **integrationskonton** nu de Azure-prenumerationer som är associerade med ditt konto. Men om du inte ser de prenumerationer som du förväntar dig, eller om avsnitten visar för många prenumerationer, följer du dessa steg:

   1. Flytta pekaren över **Logic Apps** etikett. När verktygsfältet visas väljer du **Välj prenumerationer** (filterikon).

      ![Hitta eller filtrera Azure-prenumerationer](./media/quickstart-create-logic-apps-visual-studio-code/find-or-filter-subscriptions.png)

   1. I listan som visas väljer du de prenumerationer som du vill ska visas.

1. Under **Logic Apps** väljer du den prenumeration som du vill använda. Prenumerationsnoden expanderar och visar eventuella logikappar som finns i prenumerationen.

   ![Välj din Azure-prenumeration](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-subscription.png)

   > [!TIP]
   > Under **Integrationskonton** visar val av din prenumeration eventuella integrationskonton som finns i prenumerationen.

<a name="create-logic-app"></a>

## <a name="create-new-logic-app"></a>Skapa ny logikapp

1. Om du inte har loggat in på ditt Azure-konto och din prenumeration än inifrån Visual Studio Code följer du de föregående stegen för [att logga in nu.](#access-azure)

1. I Visual Studio Code, under **Logic Apps**, öppnar du snabbmenyn för prenumerationen och väljer **Skapa logikapp**.

   ![Från prenumerationsmenyn väljer du "Skapa logikapp"](./media/quickstart-create-logic-apps-visual-studio-code/create-logic-app-visual-studio-code.png)

   En lista visas och visar alla Azure-resursgrupper i din prenumeration.

1. I listan över resursgrupper väljer du **antingen Skapa en ny resursgrupp** eller en befintlig resursgrupp. I det här exemplet skapar du en ny resursgrupp.

   ![Skapa en ny Azure-resursgrupp](./media/quickstart-create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Ange ett namn för din Azure-resursgrupp och tryck på RETUR.

   ![Ange ett namn för din Azure-resursgrupp](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Välj den Azure-region där du vill spara logikappens metadata.

   ![Välj Azure-plats för att spara logikappmetadata](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-location-new-resources.png)

1. Ange ett namn för logikappen och tryck på Retur.

   ![Ange ett namn för logikappen](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   I Azure-fönstret under din Azure-prenumeration visas din nya och tomma logikapp. Visual Studio Code öppnar också en JSON-fil (.logicapp.json) som innehåller en definition av stommen för arbetsflödet för logikappen. Nu kan du börja redigera logikappens arbetsflödesdefinition manuellt i den här JSON-filen. Teknisk referens om strukturen och syntaxen för en arbetsflödesdefinition finns i [schema för arbetsflödesdefinitionsspråk för Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md).

   ![Tom JSON-fil för arbetsflödesdefinition för logikapp](./media/quickstart-create-logic-apps-visual-studio-code/empty-logic-app-workflow-definition.png)

   Här är till exempel ett exempel på en arbetsflödesdefinition för logikappen, som börjar med en RSS-utlösare och en Office 365 Outlook-åtgärd. Vanligtvis visas JSON-element alfabetiskt i varje avsnitt. Det här exemplet visar dock de här elementen ungefär i den ordning som logikappens steg visas i designern.

   > [!IMPORTANT]
   > Om du vill återanvända den här exempeldefinitionen för logikappen behöver du ett organisationskonto, till exempel @fabrikam.com . Se till att du ersätter den fiktiva e-postadressen med din egen e-postadress. Om du vill använda en annan e-postanslutning, till exempel Outlook.com eller Gmail, ersätter du åtgärden med en liknande åtgärd som är tillgänglig från en e-postanslutning som stöds `Send_an_email_action` [av Azure Logic Apps](../connectors/apis-list.md).
   >
   > Om du vill använda Gmail-anslutningsappen kan endast G Suite-företagskonton använda den här anslutningsappen utan begränsning i logikappar. 
   > Om du har ett Gmail-konsumentkonto kan du använda den här anslutningsappen med endast specifika Google-godkända tjänster, eller så kan du skapa en Google-klientapp som ska användas för autentisering med [Gmail-anslutningsappen.](/connectors/gmail/#authentication-and-bring-your-own-application) 
   > Mer information finns i [Datasäkerhets- och sekretesspolicyer för Google-anslutningsappar i Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

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

1. När du är klar sparar du logikappens arbetsflödesdefinition. (Arkiv-> Spara eller tryck på Ctrl +S)

1. När du uppmanas att ladda upp logikappen till din Azure-prenumeration väljer du **Ladda upp.**

   I det här steget publiceras [logikappen till Azure Portal](https://portal.azure.com), som gör logiken live och körs i Azure.

   ![Ladda upp ny logikapp till din Azure-prenumeration](./media/quickstart-create-logic-apps-visual-studio-code/upload-new-logic-app.png)

## <a name="view-logic-app-in-designer"></a>Visa logikapp i designern

I Visual Studio Code kan du öppna logikappen i skrivskyddad designvy. Även om du inte kan redigera logikappen i designern kan du visuellt kontrollera logikappens arbetsflöde med hjälp av designervyn.

I Azure-fönstret går **Logic Apps**, öppnar du logikappens snabbmeny och väljer **Öppna i Designer.**

Den skrivskyddade designern öppnas i ett separat fönster och visar logikappens arbetsflöde, till exempel:

![Visa logikapp i skrivskyddade designer](./media/quickstart-create-logic-apps-visual-studio-code/logic-app-designer-view.png)

## <a name="view-in-azure-portal"></a>Visa i Azure-portalen

Följ dessa steg om du Azure Portal logikappen i den här appen:

1. Logga in på [Azure Portal](https://portal.azure.com) med samma Azure-konto och prenumeration som är associerat med logikappen.

1. I Azure Portal sökrutan anger du logikappens namn. Välj din logikapp i resultatlistan.

   ![Din nya logikapp i Azure Portal](./media/quickstart-create-logic-apps-visual-studio-code/published-logic-app-in-azure.png)

<a name="disable-enable-logic-app"></a>

## <a name="disable-or-enable-logic-app"></a>Inaktivera eller aktivera logikappen

I Visual Studio Code skriver du över din redan distribuerade app  om du redigerar en publicerad logikapp och sparar ändringarna. Inaktivera logikappen först för att undvika avbrott i logikappen i produktion och minimera störningar. Du kan sedan återaktiveras din logikapp när du har bekräftat att logikappen fortfarande fungerar.

1. Om du inte har loggat in på ditt Azure-konto och din prenumeration än från Visual Studio Code följer du föregående steg för [att logga in nu.](#access-azure)

1. I Azure-fönstret under **Logic Apps** expanderar du din Azure-prenumeration så att du kan visa alla logikappar i prenumerationen.

   1. Om du vill inaktivera den logikapp som du vill använda öppnar du logikappens meny och väljer **Inaktivera**.

      ![Inaktivera logikappen](./media/quickstart-create-logic-apps-visual-studio-code/disable-published-logic-app.png)

   1. När du är redo att återaktivera logikappen öppnar du logikappens meny och väljer **Aktivera.**

      ![Aktivera logikappen](./media/quickstart-create-logic-apps-visual-studio-code/enable-published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-deployed-logic-app"></a>Redigera distribuerad logikapp

I Visual Studio Code kan du öppna och redigera arbetsflödesdefinitionen för en redan distribuerad logikapp i Azure.

> [!IMPORTANT] 
> Innan du redigerar en aktiv logikapp som körs i produktion bör du undvika risken att bryta logikappen och minimera störningar genom [att inaktivera logikappen först.](#disable-enable-logic-app)

1. Om du inte har loggat in på ditt Azure-konto och din prenumeration än från Visual Studio Code följer du föregående steg för [att logga in nu.](#access-azure)

1. I Azure-fönstret, under **Logic Apps** expanderar du din Azure-prenumeration och väljer den logikapp som du vill använda.

1. Öppna logikappens meny och välj Öppna **i redigeraren.** Bredvid logikappens namn kan du också välja redigeringsikonen.

   ![Öppna redigeringsprogram för befintlig logikapp](./media/quickstart-create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Visual Studio Code öppnar .logicapp.jspå filen i din lokala tillfälliga mapp så att du kan visa logikappens arbetsflödesdefinition.

   ![Visa arbetsflödesdefinition för publicerad logikapp](./media/quickstart-create-logic-apps-visual-studio-code/edit-published-logic-app-workflow-definition.png)

1. Gör dina ändringar i logikappens arbetsflödesdefinition.

1. När du är klar kan du spara ändringarna. (Arkiv-> Spara eller tryck på Ctrl + S)

1. När du uppmanas att ladda  upp dina ändringar och skriva över din befintliga logikapp i Azure Portal väljer du **Ladda upp.**

   Det här steget publicerar dina uppdateringar till logikappen i [Azure Portal](https://portal.azure.com).

   ![Ladda upp redigeringar till logikappdefinitionen i Azure](./media/quickstart-create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="view-or-promote-other-versions"></a>Visa eller höja upp andra versioner

I Visual Studio Code kan du öppna och granska de tidigare versionerna för din logikapp. Du kan också höja upp en tidigare version till den aktuella versionen.

> [!IMPORTANT] 
> Innan du ändrar en aktiv logikapp som körs i produktion bör du undvika risken att bryta logikappen och minimera störningar genom [att inaktivera logikappen först.](#disable-enable-logic-app)

1. I Azure-fönstret, under **Logic Apps**, expanderar du din Azure-prenumeration så att du kan visa alla logikappar i prenumerationen.

1. Under din prenumeration expanderar du logikappen och **versionerna**.

   Listan **Versioner** visar logikappens tidigare versioner, om det finns några.

   ![Logikappens tidigare versioner](./media/quickstart-create-logic-apps-visual-studio-code/view-previous-versions.png)

1. Om du vill visa en tidigare version väljer du något av följande steg:

   * Om du vill visa JSON-definitionen **går du till** Versioner och väljer versionsnumret för den definitionen. Eller öppna den versionens snabbmeny och välj **Öppna i redigeraren.**

     En ny fil öppnas på den lokala datorn och visar versionens JSON-definition.

   * Om du vill visa versionen i den skrivskyddade designervyn öppnar du den versionens snabbmeny och väljer **Öppna i Designer.**

1. Gör så här om du vill höja upp en tidigare version till den aktuella versionen:

   1. Under **Versioner** öppnar du den tidigare versionens snabbmeny och väljer **Befordra**.

      ![Höja upp en tidigare version](./media/quickstart-create-logic-apps-visual-studio-code/promote-earlier-version.png)

   1. Om du vill fortsätta Visual Studio Code uppmanar dig att bekräfta väljer du **Ja.**

      ![Bekräfta att du befordrar en tidigare version](./media/quickstart-create-logic-apps-visual-studio-code/confirm-promote-version.png)

      Visual Studio Code befordrar den valda versionen till den aktuella versionen och tilldelar ett nytt nummer till den uppflyttade versionen. Den tidigare aktuella versionen visas nu under den uppflyttade versionen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa tillståndsful och tillståndslös logikapp i Visual Studio Code (förhandsversion)](../logic-apps/create-stateful-stateless-workflows-visual-studio-code.md)
