---
title: Automatisera uppgifter med flera Azure-tjänster
description: Självstudie – skapa automatiserade arbets flöden för att bearbeta e-postmeddelanden med Azure Logic Apps, Azure Storage och Azure Functions
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 02/27/2020
ms.openlocfilehash: bd1715dc0a3767bc5826154616bbdc97c7b61dd3
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576370"
---
# <a name="tutorial-automate-tasks-to-process-emails-by-using-azure-logic-apps-azure-functions-and-azure-storage"></a>Självstudie: automatisera uppgifter för att bearbeta e-postmeddelanden med hjälp av Azure Logic Apps, Azure Functions och Azure Storage

Med Azure Logic Apps lär du dig att automatisera arbetsflöden och integrera data i olika Azure-tjänster, Microsoft-tjänster och andra SaaS-appar (programvara som en tjänst) samt lokala system. I den här självstudien får du lära dig att skapa en [logikapp](../logic-apps/logic-apps-overview.md) som hanterar inkommande e-post och bilagor. Logikappen analyserar e-postinnehållet, sparar det i Azure Storage och skickar meddelanden för granskning av innehållet.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera [Azure Storage](../storage/common/storage-introduction.md) och Storage Explorer så att de kollar sparade e-postmeddelanden och bifogade filer.
> * Skapa en [Azure-funktion](../azure-functions/functions-overview.md) som tar bort HTML från e-postmeddelanden. Den här självstudiekursen innehåller koden som du kan använda för den här funktionen.
> * Skapa en tom logikapp.
> * Lägg till en utlösare som övervakar bifogade filer i e-postmeddelanden.
> * Lägg till ett villkor som kontrollerar om e-postmeddelanden har bifogade filer eller inte.
> * Lägg till en åtgärd som anropar Azure-funktionen när ett e-postmeddelande innehåller bifogade filer.
> * Lägg till en åtgärd som skapar lagringsblobar för e-postmeddelanden och bifogade filer.
> * Lägg till en åtgärd som skickar e-postmeddelanden.

När du är klar ser logikappen ut som det här arbetsflödet på en hög nivå:

![Logikapp på hög nivå](./media/tutorial-process-email-attachments-workflow/overview.png)

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett e-postkonto från en e-postleverantör som stöds av Logic Apps, som Office 365 Outlook, Outlook.com eller Gmail. För andra providrar [läser du listan med anslutningsappar här](/connectors/).

  Den här Logic-appen använder ett arbets-eller skol konto. Om du använder ett annat e-postkonto är stegen desamma, men användargränssnittet kan vara lite annorlunda.

  > [!IMPORTANT]
  > Om du vill använda Gmail Connector kan endast företags konton i G-Suite använda den här anslutningen utan begränsning i Logic Apps. Om du har ett Gmail-konto kan du använda den här anslutningen med endast vissa Google-godkända tjänster, eller så kan du [skapa en Google-klient som används för autentisering med din Gmail-anslutning](/connectors/gmail/#authentication-and-bring-your-own-application). Mer information finns i [principer för data säkerhet och sekretess för Google Connectors i Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Ladda ned och installera [kostnadsfria Microsoft Azure Storage Explorer](https://storageexplorer.com/). Med det här verktyget kan du kontrollera att din lagringscontainer är korrekt inställd.

* Om din Logi Kap par behöver kommunicera via en brand vägg som begränsar trafik till vissa IP-adresser, måste brand väggen tillåta åtkomst  för både [inkommande](logic-apps-limits-and-config.md#inbound) och [utgående](logic-apps-limits-and-config.md#outbound) ip-adresser som används av Logic Apps tjänst eller körning i den Azure-region där din Logic app finns. Om din Logic app även använder [hanterade anslutningar](../connectors/apis-list.md#managed-api-connectors), till exempel Office 365 Outlook Connector eller SQL-anslutning, eller använder [anpassade anslutningar](/connectors/custom-connectors/), måste brand väggen också tillåta åtkomst för *alla* [utgående IP-adresser för hanterad anslutning](logic-apps-limits-and-config.md#outbound) i din Logic app Azure-region.

## <a name="set-up-storage-to-save-attachments"></a>Konfigurera lagring för att spara bifogade filer

Du kan spara inkommande e-postmeddelanden och blobar i en [Azure-lagringscontainer](../storage/common/storage-introduction.md).

1. Logga in på [Azure Portal](https://portal.azure.com) med autentiseringsuppgifterna för ditt Azure-konto.

1. Innan du kan skapa en lagrings behållare [skapar du ett lagrings konto](../storage/common/storage-account-create.md) med de här inställningarna på fliken **grundläggande** i Azure Portal:

   | Inställning | Värde | Beskrivning |
   |---------|-------|-------------|
   | **Prenumeration** | <*Azure-prenumeration-namn*> | Azure-prenumerationens namn |  
   | **Resursgrupp** | <*Azure-resurs-grupp*> | Namnet på den [Azure-resursgrupp](../azure-resource-manager/management/overview.md) som används för att organisera och hantera relaterade resurser. I det här exemplet används "LA-självstudie-RG". <p>**Obs!** En resursgrupp finns i en viss region. Trots att objekten i den här självstudien kanske inte är tillgängliga i alla regioner ska du försöka att använda samma region när det är möjligt. |
   | **Namn på lagringskonto** | <*Azure-Storage – konto-namn*> | Ditt lagrings konto namn, som måste innehålla 3-24 tecken och får bara innehålla gemena bokstäver och siffror. I det här exemplet används "attachmentstorageacct". |
   | **Plats** | <*Azure-region*> | Den region där du vill lagra information om ditt lagrings konto. I det här exemplet används "västra USA". |
   | **Prestanda** | Standard | Den här inställningen anger datatyper som stöds och media för att lagra data. Se [Typer av lagringskonton](../storage/common/storage-introduction.md#types-of-storage-accounts). |
   | **Typ av konto** | Generellt syfte | [Lagringskontotyp](../storage/common/storage-introduction.md#types-of-storage-accounts) |
   | **Replikering** | Lokalt redundant lagring (LRS) | Den här inställningen anger hur dina data kopieras, lagras, hanteras och synkroniseras. Se [LRS (lokalt redundant lagring): låg kostnads data redundans för Azure Storage](../storage/common/storage-redundancy.md). |
   | **Åtkomstnivå (standard)** | Behåll den aktuella inställningen. |
   ||||

   Välj den här inställningen på fliken **Avancerat** :

   | Inställning | Värde | Beskrivning |
   |---------|-------|-------------|
   | **Säker överföring krävs** | Inaktiverad | Den här inställningen anger den säkerhet som krävs för begäranden från anslutningar. Se [Kräv säker överföring](../storage/common/storage-require-secure-transfer.md). |
   ||||

   När du skapar lagringskontot kan du också använda [Azure PowerShell](../storage/common/storage-account-create.md?tabs=powershell) och [Azure CLI](../storage/common/storage-account-create.md?tabs=azure-cli).

1. När du är klar väljer du **Granska + skapa**.

1. När Azure har distribuerat ditt lagrings konto kan du hitta ditt lagrings konto och hämta lagrings kontots åtkomst nyckel:

   1. I lagringskontomenyn navigerar du till **Inställningar** och väljer **Åtkomstnycklar**.

   1. Kopiera ditt lagrings konto namn och **KEY1** och spara dessa värden någon annan stans.

      ![Kopiera och spara lagringskontots namn och nyckel](./media/tutorial-process-email-attachments-workflow/copy-save-storage-name-key.png)

   När du hämtar lagringskontots åtkomstnyckel kan du också använda [Azure PowerShell](/powershell/module/az.storage/get-azstorageaccountkey) och [Azure CLI](/cli/azure/storage/account/keys).

1. Skapa en bloblagringscontainer för e-postbilagor.

   1. Navigera till lagringskontots meny. Välj **Översikt**. I översikts fönstret väljer du **behållare**.

      ![Lägg till bloblagringscontainer](./media/tutorial-process-email-attachments-workflow/create-storage-container.png)

   1. När sidan **Container** öppnas väljer du **Container**.

   1. Under **ny behållare** anger `attachments` du som behållar namn. Under **offentlig åtkomst nivå** väljer du **behållare (anonym Läs åtkomst för behållare och blobbar)**  >  **OK**.

      När du är klar kan du hitta din lagringscontainer i lagringskontot här i Azure-portalen:

      ![Klar lagringscontainer](./media/tutorial-process-email-attachments-workflow/created-storage-container.png)

   Om du vill skapa en lagrings behållare kan du också använda [Azure PowerShell](/powershell/module/az.storage/new-azstoragecontainer) eller [Azure CLI](/cli/azure/storage/container#az-storage-container-create).

Anslut sedan Storage Explorer till ditt lagringskonto.

## <a name="set-up-storage-explorer"></a>Konfigurera Storage Explorer

Anslut nu Storage Explorer till lagringskontot så du kan bekräfta att logikappen kan spara bilagor och blobar korrekt i lagringscontainern.

1. Starta Microsoft Azure Storage Explorer.

   Storage Explorer ber om en anslutning till ditt lagringskonto.

1. I fönstret **Anslut till Azure Storage** väljer du **Använd ett lagrings konto namn och nyckel**  >  **Nästa**.

   ![Storage Explorer – Anslut till lagringskonto](./media/tutorial-process-email-attachments-workflow/storage-explorer-choose-storage-account.png)

   > [!TIP]
   > Om ingen prompt visas väljer du **Lägg till ett konto** i Storage Explorer verktygsfältet.

1. Under **visnings namn** anger du ett eget namn för anslutningen. Under **Kontonamn** anger du lagringskontonamnet. Under **konto nyckel** anger du den åtkomst nyckel som du sparade tidigare och väljer **sedan nästa**.

1. Bekräfta din anslutnings information och välj sedan **Anslut**.

   Storage Explorer skapar anslutningen och visar ditt lagrings konto i Explorer-fönstret under **lokala & anslutna**  >  **lagrings konton**.

1. För att hitta din Blob Storage-behållare, under **lagrings konton**, expanderar du ditt lagrings konto, som är **attachmentstorageacct** här och expanderar **BLOB-behållare** där du hittar behållaren för **bifogade filer** , till exempel:

   ![Storage Explorer – hitta lagringscontainer](./media/tutorial-process-email-attachments-workflow/storage-explorer-check-contianer.png)

Skapa sedan en [Azure-funktion](../azure-functions/functions-overview.md) som tar bort HTML från inkommande e-post.

## <a name="create-function-to-clean-html"></a>Skapa funktion för att rensa HTML

Använd nu kodfragmentet som tillhandahålls via de här stegen för att skapa en Azure-funktion som tar bort HTML från varje inkommande e-postmeddelande. På så vis blir e-postinnehållet renare och enklare att bearbeta. Sedan kan du anropa den här funktionen från din logikapp.

1. Innan du kan skapa en funktion ska du [skapa en funktionsapp](../azure-functions/functions-create-function-app-portal.md) med dessa inställningar:

   | Inställning | Värde | Beskrivning |
   | ------- | ----- | ----------- |
   | **Appens namn** | <*funktion-App-Name*> | Appens funktions namn, som måste vara globalt unikt i Azure. I det här exemplet används redan "CleanTextFunctionApp", så ange ett annat namn, till exempel "MyCleanTextFunctionApp-<*ditt-name*>" |
   | **Prenumeration** | <*ditt-Azure-prenumerations namn*> | Samma Azure-prenumeration som du tidigare använt |
   | **Resursgrupp** | LA-Tutorial-RG | Samma Azure-resursgrupp som du tidigare använt |
   | **Operativsystem** | <*ditt operativ system*> | Välj det operativ system som stöder ditt favorit funktions programmeringsspråk. I det här exemplet väljer du **Windows**. |
   | **Värdplan** | Förbrukningsplan | Den här inställningen avgör hur resurser ska allokeras och skalas, som datorkraft, för att köra din funktionsapp. Se [jämförelse av värdplaner](../azure-functions/functions-scale.md). |
   | **Plats** | USA, västra | Samma region som du tidigare använt |
   | **Körningsstack** | Önskat språk | Välj en körning som stöder ditt favorit funktions programmeringsspråk. Välj **.net** för C# och F # functions. |
   | **Storage** | cleantextfunctionstorageacct | Skapa ett lagringskonto för din funktionsapp. Använd bara gemena bokstäver och siffror. <p>**Obs:** Det här lagrings kontot innehåller dina funktions program och skiljer sig från ditt tidigare skapade lagrings konto för e-postbilagor. |
   | **Application Insights** | Inaktivera | Aktiverar program övervakning med [Application Insights](../azure-monitor/app/app-insights-overview.md), men i den här självstudien väljer du **inaktivera**  >  **Använd**. |
   ||||

   Om din funktions app inte öppnas automatiskt efter distributionen går du till sökrutan [Azure Portal](https://portal.azure.com) , söker efter och väljer **Funktionsapp**. Under **Funktionsapp** väljer du din Function-app.

   ![Välj funktionsapp](./media/tutorial-process-email-attachments-workflow/select-function-app.png)

   I annat fall öppnar Azure automatiskt funktionsappen som visas här:

   ![Skapad funktionsapp](./media/tutorial-process-email-attachments-workflow/function-app-created.png)

   Om du vill skapa en Function-app kan du också använda [Azure CLI](../azure-functions/create-first-function-cli-csharp.md)eller [PowerShell och Resource Manager-mallar](../azure-resource-manager/templates/deploy-powershell.md).

1. I listan **Function Apps** expanderar du din Function-app om den inte redan är expanderad. Under din Function-app väljer du **Functions**. I funktionsverktygsfältet väljer du **New function** (Ny funktion).

   ![Skapa ny funktion](./media/tutorial-process-email-attachments-workflow/function-app-new-function.png)

1. Under **Välj en mall nedan eller gå till snabb** starten väljer du mallen **http-utlösare** .

   ![Välj mall för HTTP-utlösare](./media/tutorial-process-email-attachments-workflow/function-select-httptrigger-csharp-function-template.png)

   Azure skapar en funktion med hjälp av en språkspecifik mall för en HTTP-utlöst funktion.

1. I fönstret **New Function** (Ny funktion) under **Name** (Namn) skriver du `RemoveHTMLFunction`. Se till att **behörighets nivån** är inställd på **funktion** och välj **skapa**.

   ![Namnge din funktion](./media/tutorial-process-email-attachments-workflow/function-provide-name.png)

1. När redigeringsprogrammet öppnas ersätter du mallkoden med den här exempelkoden som tar bort HTML och returnerar resultatet till anroparen:

   ```csharp
   #r "Newtonsoft.Json"

   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   using Newtonsoft.Json;
   using System.Text.RegularExpressions;

   public static async Task<IActionResult> Run(HttpRequest req, ILogger log) {

      log.LogInformation("HttpWebhook triggered");

      // Parse query parameter
      string emailBodyContent = await new StreamReader(req.Body).ReadToEndAsync();

      // Replace HTML with other characters
      string updatedBody = Regex.Replace(emailBodyContent, "<.*?>", string.Empty);
      updatedBody = updatedBody.Replace("\\r\\n", " ");
      updatedBody = updatedBody.Replace(@"&nbsp;", " ");

      // Return cleaned text
      return (ActionResult)new OkObjectResult(new { updatedBody });
   }
   ```

1. När du är klar väljer du **Spara**. Om du vill testa funktionen väljer du test i redigerings programmets högra kant under pilen ( **<** )-ikonen. 

   ![Öppna testfönstret](./media/tutorial-process-email-attachments-workflow/function-choose-test.png)

1. I rutan **test** , under **brödtext i begäran**, anger du den här raden och väljer **Kör**.

   `{"name": "<p><p>Testing my function</br></p></p>"}`

   ![Testa din funktion](./media/tutorial-process-email-attachments-workflow/function-run-test.png)

   I fönstret **Utdata** visas resultatet från funktionen:

   ```json
   {"updatedBody":"{\"name\": \"Testing my function\"}"}
   ```

Skapa din logikapp när du har kontrollerat att funktionen fungerar. Trots att den här självstudien visar hur du skapar en funktion som tar bort HTML från e-post har Logic Apps även en **HTML till text**-anslutning.

## <a name="create-your-logic-app"></a>Skapa en logikapp

1. I sökrutan i Azure på översta nivån anger `logic apps` du och väljer **Logic Apps**.

   ![Sök efter och välj "Logic Apps"](./media/tutorial-process-email-attachments-workflow/find-select-logic-apps.png)

1. I fönstret **Logic Apps** väljer du **Lägg till**.

   ![Lägg till ny Logic-app](./media/tutorial-process-email-attachments-workflow/add-new-logic-app.png)

1. I fönstret **Logic app** anger du information om din Logi Kap par som visas här. När du är klar väljer du **Granska + skapa**.

   ![Ange information om din logikapp](./media/tutorial-process-email-attachments-workflow/create-logic-app-settings.png)

   | Inställning | Värde | Beskrivning |
   | ------- | ----- | ----------- |
   | **Prenumeration** | <*ditt-Azure-prenumerations namn*> | Samma Azure-prenumeration som du tidigare använt |
   | **Resursgrupp** | LA-Tutorial-RG | Samma Azure-resursgrupp som du tidigare använt |
   | **Namn på logikapp** | LA-ProcessAttachment | Logikappens namn |
   | **Välj plats** | USA, västra | Samma region som du tidigare använt |
   | **Log Analytics** | Av | I den här självstudien väljer du inställningen **av** . |
   ||||

1. När Azure har distribuerat din app väljer du ikonen meddelanden i Azure-verktygsfältet och väljer **gå till resurs**.

   ![Från listan Azure-meddelanden väljer du "gå till resurs"](./media/tutorial-process-email-attachments-workflow/go-to-new-logic-app-resource.png)

1. När Logic Apps designer öppnas och visar en sida med en introduktions video och mallar för vanliga Logic Apps-mönster. Under **Mallar** väljer du **Tom logikapp**.

   ![Välj Tom Logic app-mall](./media/tutorial-process-email-attachments-workflow/choose-logic-app-template.png)

Lägg sedan till en [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts) som lyssnar efter inkommande e-postmeddelanden som innehåller bilagor. Varje logikapp måste börja med en utlösare som utlöses när en specifik händelse sker eller när nya data uppfyller ett särskilt villkor. Mer information finns i [Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="monitor-incoming-email"></a>Övervaka inkommande e-post

1. I designern, i rutan Sök, anger `when new email arrives` du som filter. Välj den här utlösaren för e-postleverantören: **When a new email arrives - <*your-email-provider*>** (när ett nytt e-postmeddelande kommer - <din-e-postleverantör>).

   Exempel:

   ![Välj den här utlösaren för e-postleverantör: ”När ett nytt e-postmeddelande kommer”](./media/tutorial-process-email-attachments-workflow/add-trigger-when-email-arrives.png)

   * För Azure arbets- eller skolkonto väljer du Office 365 Outlook.

   * För personliga Microsoft-konton väljer du Outlook.com.

1. Om du blir tillfrågad om autentiseringsuppgifter loggar du in på e-postkontot så att Logic Apps kan ansluta till e-postkontot.

1. Nu anger du villkoret utlösaren använder för att filtrera ny e-post.

   1. Ange inställningarna som beskrivs nedan för att kontrol lera e-postmeddelanden.

      ![Ange mapp, intervall och frekvens för att kontrollera e-postmeddelanden](./media/tutorial-process-email-attachments-workflow/set-up-email-trigger.png)

      | Inställning | Värde | Beskrivning |
      | ------- | ----- | ----------- |
      | **Mapp** | Inkorgen | E-postmappen som ska kontrolleras |
      | **Has Attachment** (Innehåller bifogad fil) | Yes | Hämta endast e-postmeddelanden med bifogade filer. <p>**Obs!** Utlösaren tar inte bort e-post från ditt konto, kontrollerar endast nya meddelanden och bearbetar endast e-postmeddelanden som matchar filtrets ämne. |
      | **Inkludera bifogade filer** | Yes | Hämta bilagorna som indata i arbetsflödet istället för att bara söka efter bilagor. |
      | **Intervall** | 1 | Antalet intervaller som ska förflyta mellan kontrollerna |
      | **Frekvens** | Minut | Tidsenhet för varje intervall mellan kontroller |
      ||||

   1. I listan **Lägg till ny parameter** väljer du **ämnes filter**.

   1. När rutan **ämnes filter** visas i åtgärden anger du det ämne som visas här:

      | Inställning | Värde | Beskrivning |
      | ------- | ----- | ----------- |
      | **Ämnesfilter** | `Business Analyst 2 #423501` | Texten att söka efter i e-postämnet |
      ||||

1. Om du vill dölja utlösarinformationen för tillfället klickar du in utlösarens rubriklist.

   ![Minimera form för att dölja information](./media/tutorial-process-email-attachments-workflow/collapse-trigger-shape.png)

1. Spara logikappen. I verktygsfältet designer väljer du **Spara**.

   Logikappen har nu publicerats men gör inget annat än att kolla din e-post. Lägg sedan till ett villkor som anger kriterier för att fortsätta arbetsflödet.

## <a name="check-for-attachments"></a>Sök efter bifogade filer

Nu ska du lägga till ett villkor som väljer endast e-postmeddelanden med bifogade filer.

1. Under utlösaren väljer du **nytt steg**.

   !["Nytt steg"](./media/tutorial-process-email-attachments-workflow/add-condition-under-trigger.png)

1. Under **Välj en åtgärd** i rutan Sök anger du `condition` . Välj den här åtgärden: **villkor**

   ![Välj "villkor"](./media/tutorial-process-email-attachments-workflow/select-condition.png)

   1. Byt namn på villkoret med en bättre beskrivning. I villkorets namn List väljer du knappen med ellipser (**...**) > **Byt namn**.

      ![Byt namn på villkor](./media/tutorial-process-email-attachments-workflow/condition-rename.png)

   1. Byt namn på villkoret med den här beskrivningen: `If email has attachments and key subject phrase`

1. Skapa ett villkor som kontrollerar om e-postmeddelanden har bifogade filer.

   1. Klicka i den vänstra rutan på den första raden under **Och**. På den dynamiska innehållslistan som visas väljer du egenskapen **Has attachment** (Har bifogad fil).

      ![Skärm bild som visar egenskapen "och" för villkoret och egenskaps inställningen "har bifogade filer".](./media/tutorial-process-email-attachments-workflow/build-condition.png)

   1. I den mellersta rutan behåller du operatorn **is equal to** (är lika med).

   1. I höger ruta skriver du in **True** (Sant) som värde att jämföra med egenskapsvärdet **Has Attachment** (Har bifogad fil).

      ![Skapa villkor](./media/tutorial-process-email-attachments-workflow/finished-condition.png)

      Om båda värdena är lika innehåller e-postmeddelandet minst en bifogad fil, villkoret skickas och arbetsflödet fortsätter.

   I den underliggande logikappens definition, som du kan visa i kodredigeringsfönstret, ser villkoret ut som det här exemplet:

   ```json
   "Condition": {
      "actions": { <actions-to-run-when-condition-passes> },
      "expression": {
         "and": [ {
            "equals": [
               "@triggerBody()?['HasAttachment']",
                 "true"
            ]
         } ]
      },
      "runAfter": {},
      "type": "If"
   }
   ```

1. Spara logikappen. I verktygsfältet designer väljer du **Spara**.

### <a name="test-your-condition"></a>Testa ditt villkor

Testa nu om villkoret fungerar som det ska:

1. Om din Logic app inte körs redan, väljer du **Kör** i verktygsfältet designer.

   Det här steget startar logikappen manuellt utan att vänta tills ditt angivna intervall skickas. Men det händer ingenting förrän test-e-postmeddelandet hamnar i inkorgen.

1. Skicka ett e-postmeddelande som uppfyller kriterierna till dig själv:

   * E-postmeddelandets ämne har den text som du har angett i utlösarens **ämnes filter**: `Business Analyst 2 #423501`

   * Ditt e-postmeddelande har en bifogad fil. Skapa för tillfället en tom textfil och bifoga den i e-postmeddelandet.

   När e-postmeddelandet kommer letar logikappen efter bilagor och den angivna ämnestexten. Om villkoret uppfylls utlöser utlösaren Logic Apps-motorn och får den att skapa en logikappinstans och starta arbetsflödet.

1. Kontrol lera att utlösaren har utlösts och att Logic-appen har körts genom att välja **Översikt** på menyn Logic app.

   ![Kontrollera utlösare och körningshistorik](./media/tutorial-process-email-attachments-workflow/checkpoint-run-history.png)

   Om logikappen inte utlöstes eller kördes trots en lyckad utlösare kan du läsa [informationen om att felsöka logikappen](../logic-apps/logic-apps-diagnosing-failures.md).

Definiera därefter åtgärderna som ska vidtas för grenen **If true** (Om sant). För att spara e-postmeddelandet tillsammans med eventuella bifogade filer tar du bort alla HTML från e-postmeddelandet och skapar blobar i lagringscontainern för e-postmeddelandet och de bifogade filerna.

> [!NOTE]
> Logikappen behöver inte göra något för grenen **Om falskt** när ett e-postmeddelande inte innehåller bilagor.
> Som en extrauppgift efter självstudien kan du lägga till en lämplig åtgärd du vill vidta för grenen **Om falskt**.

## <a name="call-removehtmlfunction"></a>Anropa RemoveHTMLFunction

I det här steget lägger du till den tidigare skapade Azure-funktionen i logikappen och skickar innehållet i e-posttexten från e-postutlösaren till din funktion.

1. Välj **Logic App Designer** på logikappmenyn. I grenen **om sant** väljer du **Lägg till en åtgärd**.

   ![Lägg till åtgärd inuti "If true" (Om sant)](./media/tutorial-process-email-attachments-workflow/if-true-add-action.png)

1. Sök efter "Azure Functions" i sökrutan och välj den här åtgärden: **Välj en Azure Function-Azure Functions**

   ![Välj åtgärd för Choose an Azure function (Välj en Azure-funktion)](./media/tutorial-process-email-attachments-workflow/add-action-azure-function.png)

1. Välj din tidigare skapade Function-app, som är `CleanTextFunctionApp` i det här exemplet:

   ![Välj Azure-funktionsapp](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function-app.png)

1. Välj nu din funktion: **RemoveHTMLFunction**

   ![Välj Azure-funktion](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function.png)

1. Byt namn på funktionsformen med den här beskrivningen: `Call RemoveHTMLFunction to clean email body`

1. Ange nu indata som funktionen ska bearbeta.

   1. Under **Request Body** (Begärandetext) skriver du med ett avslutande blanksteg:

      `{ "emailBody":`

      Medan du arbetar med dessa indata i nästa steg visas ett felmeddelande om ogiltig JSON tills dina indata är rätt formaterade som JSON. När du testade funktionen tidigare använde angivna indata för funktionen JavaScript Object Notation (JSON). Så, begärandetexten måste vara i samma format.

      När markören befinner sig inom rutan **Begärandetext** visas den dynamiska innehållslistan så att du kan välja egenskapsvärden som är tillgängliga från tidigare åtgärder.

   1. Öppna den dynamiska innehållslistan. Under **När ett nytt e-postmeddelande kommer** väljer du egenskapen **Brödtext**. Kom ihåg att lägga till en avslutande klammerparentes efter den här egenskapen: `}`

      ![Ange begärandetexten som ska skickas till funktionen](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing.png)

   När du är klar ser indata i funktionen ut som i det här exemplet:

   ![Färdig begärandetext som ska skickas till din funktion](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing-2.png)

1. Spara logikappen.

Lägg sedan till en åtgärd som skapar en blob i lagringscontainern så att du kan spara e-postmeddelandets brödtext.

## <a name="create-blob-for-email-body"></a>Skapa blob för e-postmeddelandets brödtext

1. I **IF True** -blocket och under din Azure-funktion väljer du **Lägg till en åtgärd**.

1. I rutan Sök anger `create blob` du som filter och väljer den här åtgärden: **skapa BLOB**

   ![Lägga till åtgärd för att skapa blob för e-postmeddelandets brödtext](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-email-body.png)

1. Skapa en anslutning till ditt lagringskonto med inställningarna som visas och beskrivs här. När du är färdig väljer du **Skapa**.

   ![Skapa anslutning till lagringskontot](./media/tutorial-process-email-attachments-workflow/create-storage-account-connection-first.png)

   | Inställning | Värde | Beskrivning |
   | ------- | ----- | ----------- |
   | **Anslutnings namn** | AttachmentStorageConnection | Ett beskrivande namn för anslutningen |
   | **Lagringskonto** | attachmentstorageacct | Namnet på lagringskontot som du skapade tidigare för att spara bilagor |
   ||||

1. Byt namn på åtgärden **Skapa blob** med den här beskrivningen: `Create blob for email body`

1. I åtgärden **Skapa blob** anger du den här informationen och väljer fälten för att skapa bloben som visas och beskrivs:

   ![Ange blobinformation för postmeddelandets brödtext](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body.png)

   | Inställning | Värde | Beskrivning |
   | ------- | ----- | ----------- |
   | **Mappsökväg** | /attachments | Sökvägen till och namnet på containern som du skapade tidigare. I det här exemplet klickar du på mappikonen och väljer sedan containern "/attachments". |
   | **Blobnamn** | Fältet **Från** | I det här exemplet använder du avsändarens namn som blobnamn. Klicka i den här rutan så att den dynamiska innehållslistan visas. Välj sedan fältet **From** (Från) under åtgärden **When a new email arrives** (När ett nytt e-postmeddelande kommer). |
   | **Blobinnehåll** | **Innehålls** fält | I det här exemplet använder du e-postmeddelandets HTML-fria brödtext som blobinnehåll. Klicka i den här rutan så att den dynamiska innehållslistan visas. Välj sedan **Body** (Brödtext) under åtgärden **Call RemoveHTMLFunction to clean email body** (Anropa RemoveHTMLFunction för att rensa e-postbrödtext). |
   ||||

   När du är klar ser villkoret ut som i det här exemplet:

   ![Skärm bild som visar ett exempel på en färdig "skapa BLOB"-åtgärd.](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body-done.png)

1. Spara logikappen.

### <a name="check-attachment-handling"></a>Kontrollera hanteringen av bifogade filer

Testa nu om logikappen hanterar e-postmeddelanden som du angav:

1. Om din Logic app inte körs redan, väljer du **Kör** i verktygsfältet designer.

1. Skicka ett e-postmeddelande som uppfyller kriterierna till dig själv:

   * E-postmeddelandets ämne har den text som du har angett i utlösarens **ämnes filter**: `Business Analyst 2 #423501`

   * Ditt e-postmeddelande har minst en bifogad fil. För närvarande kan du bara skapa en tom textfil och bifoga filen till din e-post.

   * Ditt e-postmeddelande har lite test innehåll i bröd texten, till exempel: `Testing my logic app`

   Om logikappen inte utlöstes eller kördes trots en lyckad utlösare kan du läsa [informationen om att felsöka logikappen](../logic-apps/logic-apps-diagnosing-failures.md).

1. Kontrollera om logikappen sparade e-postmeddelandet i rätt lagringscontainer.

   1. I Storage Explorer, expanderar du **lokala & anslutna**  >  **lagrings konton**  >  **attachmentstorageacct (Key)**  >  **BLOB containers**  >  **Attachments**.

   1. Titta i containern **attachments** efter e-postmeddelandet.

      Endast e-postmeddelandet visas nu i containern eftersom logikappen inte behandlar bilagor ännu.

      ![Kontrollera Storage Explorer efter sparat e-postmeddelande](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-email.png)

   1. När du är klar tar du bort e-postmeddelandet i Storage Explorer.

1. För att testa grenen **Om falskt**, som inte gör något just nu, kan du även skicka ett e-postmeddelande som inte uppfyller villkoren.

Lägg sedan till en loop för att bearbeta alla bilagor.

## <a name="process-attachments"></a>Bearbeta bilagor

För att bearbeta varje bilaga i e-postmeddelandet lägger du till en **For each**-loop (för varje) i logikappens arbetsflöde.

1. Under formen **skapa BLOB för e-postbrödtext** väljer du **Lägg till en åtgärd**.

   ![Lägg till en "for each"-loop (för varje-loop)](./media/tutorial-process-email-attachments-workflow/add-for-each-loop.png)

1. Under **Välj en åtgärd** går du till rutan Sök och anger `for each` som filter och väljer den här åtgärden: **för varje**

   ![Välj "för varje"](./media/tutorial-process-email-attachments-workflow/select-for-each.png)

1. Byt namn på loopen med den här beskrivningen: `For each email attachment`

1. Ange nu loopens data för att bearbeta. Klicka i rutan **Select an output from previous steps** (Välj utdata från tidigare steg) så att den dynamiska innehållslistan öppnas och välj **Attachments** (Bilagor).

   ![Välj ”Bifogade filer”](./media/tutorial-process-email-attachments-workflow/select-attachments.png)

   Fältet **Bifogade filer** skickar en matris som innehåller alla bifogade filer som finns i ett e-postmeddelande. Loopen **For each** (För varje) upprepar åtgärder för varje objekt som skickats med matrisen.

1. Spara logikappen.

Lägg sedan till åtgärden som sparar varje bilaga som en blob i lagringscontainern **attachments**.

## <a name="create-blob-for-each-attachment"></a>Skapa blob för varje bifogad fil

1. I slingan **för varje e-postbilaga** väljer du **Lägg till en åtgärd** så att du kan ange vilken aktivitet som ska utföras på alla Funna bilagor.

   ![Lägga till åtgärd i loop](./media/tutorial-process-email-attachments-workflow/for-each-add-action.png)

1. I rutan Sök anger `create blob` du som filter och väljer sedan den här åtgärden: **skapa BLOB**

   ![Lägga till åtgärd för att skapa blob](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-attachments.png)

1. Byt namn på åtgärden **Skapa blob 2** med den här beskrivningen: `Create blob for each email attachment`

1. I åtgärden **Create blob for each email attachment** (Skapa blob för varje e-postbilaga) anger du den här informationen och väljer egenskaperna för varje blob du vill skapa, som visas och beskrivs:

   ![Ange blob-information](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment.png)

   | Inställning | Värde | Beskrivning |
   | ------- | ----- | ----------- |
   | **Mappsökväg** | /attachments | Sökvägen till och namnet på containern som du skapade tidigare. I det här exemplet klickar du på mappikonen och väljer sedan containern "/attachments". |
   | **Blobnamn** | Fältet **Namn** | I det här exemplet använder du den bifogade filens namn som blobnamn. Klicka i den här rutan så att den dynamiska innehållslistan visas. Välj sedan fältet **Name** (Namn) under åtgärden **When a new email arrives** (När ett nytt e-postmeddelande kommer). |
   | **Blobinnehåll** | **Innehålls** fält | I det här exemplet använder du fältet **Content** (Innehåll) som blobinnehåll. Klicka i den här rutan så att den dynamiska innehållslistan visas. Välj sedan fältet **Content** (Innehåll) under åtgärden **When a new email arrives** (När ett nytt e-postmeddelande kommer). |
   ||||

   När du är klar ser villkoret ut som i det här exemplet:

   ![Färdig skapa blob-åtgärd](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment-done.png)

1. Spara logikappen.

### <a name="check-attachment-handling"></a>Kontrollera hanteringen av bifogade filer

Testa nu om logikappen hanterar bilagor som du angav:

1. Om din Logic app inte körs redan, väljer du **Kör** i verktygsfältet designer.

1. Skicka ett e-postmeddelande som uppfyller kriterierna till dig själv:

   * E-postmeddelandets ämne har den text som du har angett i utlösarens egenskap **subject filter** : `Business Analyst 2 #423501`

   * Ditt e-postmeddelande har minst två bifogade filer. Skapa för tillfället två tomma textfiler och bifoga dem i e-postmeddelandet.

   Om logikappen inte utlöstes eller kördes trots en lyckad utlösare kan du läsa [informationen om att felsöka logikappen](../logic-apps/logic-apps-diagnosing-failures.md).

1. Kontrollera om logikappen sparade e-postmeddelandet och bilagorna i rätt lagringscontainer.

   1. I Storage Explorer, expanderar du **lokala & anslutna**  >  **lagrings konton**  >  **attachmentstorageacct (Key)**  >  **BLOB containers**  >  **Attachments**.

   1. Kontrollera både e-postmeddelandet och bilagorna finns i containern **attachments**.

      ![Leta efter sparad e-post och bilagor](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-attachments.png)

   1. När du är klar tar du bort e-postmeddelandet och bilagorna i Storage Explorer.

Lägg sedan till en åtgärd så att logikappen skickar e-post för att granska de bifogade filerna.

## <a name="send-email-notifications"></a>Skicka e-postmeddelanden

1. Välj **Lägg till en åtgärd** i listan **om sant** under **för varje e-postbilagas** slinga.

   ![Lägga till åtgärd under ”for each”-loop (för varje-loop)](./media/tutorial-process-email-attachments-workflow/add-action-send-email.png)

1. I sökrutan anger du `send email` som filter och väljer sedan åtgärden "skicka e-post" för din e-postleverantör.

   Om du vill filtrera åtgärdslistan till en specifik tjänst kan du välja anslutningsappen först.

   ![Välj ”Skicka e-post”-åtgärden för din e-postprovider](./media/tutorial-process-email-attachments-workflow/add-action-select-send-email.png)

   * För Azure arbets- eller skolkonto väljer du Office 365 Outlook.

   * För personliga Microsoft-konton väljer du Outlook.com.

1. Om du blir tillfrågad om autentiseringsuppgifter loggar du in på e-postkontot så Logic Apps skapar en anslutning till ditt e-postkonto.

1. Byt namn på åtgärden **Skicka ett e-postmeddelande** med den här beskrivningen: `Send email for review`

1. Ange informationen för åtgärden och välj fälten du vill ta med i e-postmeddelandet som det visas och beskrivs. Tryck på Skift + Enter för att lägga till tomma rader i en redigeringsruta.

   ![Skicka e-postavisering](./media/tutorial-process-email-attachments-workflow/send-email-notification.png)

   Om du inte hittar ett förväntat fält i listan med dynamiskt innehåll väljer du **Visa fler** bredvid **när ett nytt e-postmeddelande kommer**.

   | Inställning | Värde | Obs! |
   | ------- | ----- | ----- |
   | **Till** | <*mottagare-e-postadress*> | I testsyfte kan du använda din egen e-postadress. |
   | **Ämne**  | ```ASAP - Review applicant for position:```**Ämne** | E-postämnet du vill ha. Klicka i den här rutan, ange exempeltexten och välj fältet **Subject** (Ämne) under **When a new email arrives** (När ett nytt e-postmeddelande kommer). |
   | **Brödtext** | ```Please review new applicant:``` <p>```Applicant name:```**Från** <p>```Application file location:``` **Sökväg** <p>```Application email content:``` **Brödtext** | E-postmeddelandets brödtext. Klicka i den här rutan, ange exempeltext och välj de här fälten på den dynamiska innehållslistan: <p>- Fältet **Från** under **När ett nytt e-postmeddelande kommer** </br>- Fältet **Sökväg** under **Skapa blob för e-postmeddelandets brödtext** </br>- Fältet **Brödtext** under **Call RemoveHTMLFunction to clean email body** (Anropa RemoveHTMLFunction för att rensa e-postmeddelandets brödtext) |
   ||||

   > [!NOTE]
   > Om du väljer ett fält som innehåller en matris, som fältet **Innehåll** som är en matris som innehåller bifogade filer, lägger designerprogrammet automatiskt till en "For each"-loop (För varje) omkring åtgärden som refererar till fältet.
   > På så sätt kan din logikappsåtgärd utförs på varje element i matrisen.
   > Ta bort loopen genom att ta bort fältet för matrisen, flytta den refererande åtgärden till utsidan av slingan, Välj ellipserna (**...**) på loopens namn list och välj **ta bort**.

1. Spara logikappen.

Nu testar du logikappen, som nu ser ut som i det här exemplet:

![Färdig logikapp](./media/tutorial-process-email-attachments-workflow/complete.png)

## <a name="run-your-logic-app"></a>Kör logikappen

1. Skicka ett e-postmeddelande som uppfyller kriterierna till dig själv:

   * E-postmeddelandets ämne har den text som du har angett i utlösarens egenskap **subject filter** : `Business Analyst 2 #423501`

   * Ditt e-postmeddelande har en eller flera bilagor. Du kan återanvända en tom textfil från föregående test. Bifoga en återstartsfil för ett mer realistiskt scenario.

   * I e-postmeddelandet finns den här texten som du kan kopiera och klistra in:

     ```text

     Name: Jamal Hartnett

     Street address: 12345 Anywhere Road

     City: Any Town

     State or Country: Any State

     Postal code: 00000

     Email address: jamhartnett@outlook.com

     Phone number: 000-000-0000

     Position: Business Analyst 2 #423501

     Technical skills: Dynamics CRM, MySQL, Microsoft SQL Server, JavaScript, Perl, Power BI, Tableau, Microsoft Office: Excel, Visio, Word, PowerPoint, SharePoint, and Outlook

     Professional skills: Data, process, workflow, statistics, risk analysis, modeling; technical writing, expert communicator and presenter, logical and analytical thinker, team builder, mediator, negotiator, self-starter, self-managing  

     Certifications: Six Sigma Green Belt, Lean Project Management

     Language skills: English, Mandarin, Spanish

     Education: Master of Business Administration
     ```

1. Kör logikappen. Om det lyckas skickar din logikapp ett e-postmeddelande som ser ut som i det här exemplet:

   ![E-postmeddelande som skickats av logikapp](./media/tutorial-process-email-attachments-workflow/email-notification.png)

   Om du inte får e-post kan du titta i mappen Skräppost. Ditt skräppostfilter kan dirigera om dessa typer av e-post. Om du är osäker på om din logikapp kördes korrekt kan du läsa informationen om att [felsöka logikappen](../logic-apps/logic-apps-diagnosing-failures.md).

Grattis! Nu har du skapat en och kört en logikapp som automatiserar uppgifter i olika Azure-tjänster och anropar anpassad kod.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen som innehåller logikappen och alla relaterade resurser när du inte längre behöver dem.

1. I rutan Azure Search på översta nivån anger `resources groups` du och väljer **resurs grupper**.

   ![Sök efter och välj "resurs grupper"](./media/tutorial-process-email-attachments-workflow/find-azure-resource-groups.png)

1. I listan **resurs grupper** väljer du resurs gruppen för den här självstudien. 

   ![Hitta resurs gruppen för självstudier](./media/tutorial-process-email-attachments-workflow/find-select-tutorial-resource-group.png)

1. I **översikts** fönstret väljer du **ta bort resurs grupp**.

   ![Ta bort resursgrupp för logikapp](./media/tutorial-process-email-attachments-workflow/delete-resource-group.png)

1. När fönstret bekräftelse visas anger du namnet på resurs gruppen och väljer **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har du skapat en logikapp som bearbetar och lagrar e-postbilagor genom att integrera Azure-tjänster, till exempel Azure Storage och Azure Functions. Lär dig nu mer om andra anslutningsappar som du kan använda för att skapa logikappar.

> [!div class="nextstepaction"]
> [Läs mer om anslutningsappar för Logic Apps](../connectors/apis-list.md)