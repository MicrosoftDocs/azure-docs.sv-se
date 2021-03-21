---
title: Skapa Logic Apps för hands versioner av arbets flöden i Azure Portal
description: Skapa och kör arbets flöden för automatiserings-och integrations scenarier med Azure Logic Apps för hands version i Azure Portal.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: ff938d29d998b6fcf0b2cfae72a9a9e685a10dc5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563972"
---
# <a name="create-stateful-and-stateless-workflows-in-the-azure-portal-with-azure-logic-apps-preview"></a>Skapa tillstånds lösa och tillstånds lösa arbets flöden i Azure Portal med Azure Logic Apps för hands version

> [!IMPORTANT]
> Den här funktionen är tillgänglig som en offentlig förhandsversion utan servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med [Azure Logic Apps för hands](logic-apps-overview-preview.md)version kan du skapa automatiserings-och integrerings lösningar mellan appar, data, moln tjänster och system genom att skapa och köra Logi Kap par som innehåller [ *tillstånds* känsliga och *tillstånds lösa* arbets flöden](logic-apps-overview-preview.md#stateful-stateless) i Azure Portal genom att börja med den nya resurs typen för **Logic app (förhands granskning)** . Med den här nya typen av Logic-appar kan du skapa flera arbets flöden som drivs av omdesignad Azure Logic Apps Preview-körning, som ger portabilitet, bättre prestanda och flexibilitet för att distribuera och köra i olika värd miljöer, inte bara Azure, utan även Docker-behållare. Mer information om den nya typen av Logic-appar finns i [Översikt för Azure Logic Apps för hands version](logic-apps-overview-preview.md).

![Skärm bild som visar Azure Portal med arbetsflödesdesignern för resursen "Logic app (för hands version)".](./media/create-stateful-stateless-workflows-azure-portal/azure-portal-logic-apps-overview.png)

I Azure Portal kan du börja med att skapa en ny resurs för **Logic app (för hands version)** . Även om du kan börja med [att skapa ett projekt i Visual Studio Code med tillägget Azure Logic Apps (för hands version)](create-stateful-stateless-workflows-visual-studio-code.md)kan du använda båda metoderna för att distribuera och köra din Logi Kap par i samma typer av värd miljöer.

Under tiden kan du fortfarande skapa den ursprungliga typen av Logic-appar. Även om utvecklings upplevelsen i portalen skiljer sig mellan de ursprungliga och nya Logic app-typerna, kan din Azure-prenumeration innehålla båda typerna. Du kan visa och komma åt alla distribuerade Logi Kap par i din Azure-prenumeration, men apparna organiseras i sina egna kategorier och avsnitt.

Den här artikeln visar hur du skapar din Logi Kap par och ditt arbets flöde i Azure Portal med hjälp av resurs typen **Logic app (för hands version)** och utför följande uppgifter på hög nivå:

* Skapa den nya Logic app-resursen och Lägg till ett tomt arbets flöde.

* Lägg till en utlösare och åtgärd.

* Utlös en arbets flödes körning.

* Visa arbets flödets körnings-och utlösnings historik.

* Aktivera eller öppna Application Insights efter distribution.

* Aktivera körnings historik för tillstånds lösa arbets flöden.

> [!NOTE]
> Om du vill ha mer information om aktuella kända problem granskar du [sidan Logic Apps offentliga kända problem i GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Ett [Azure Storage konto](../storage/common/storage-account-overview.md) eftersom **Logic app (förhands granskning)** -resursen drivs av Azure Functions och har [lagrings krav som liknar Function-appar](../azure-functions/storage-considerations.md). Du kan använda ett befintligt lagrings konto eller så kan du skapa ett lagrings konto i förväg eller när du skapar en logisk app.

  > [!NOTE]
  > [Tillstånds känsliga Logic Apps](logic-apps-overview-preview.md#stateful-stateless) utför lagrings transaktioner, till exempel att använda köer för schemaläggning och lagring av arbets flödes tillstånd i tabeller och blobbar. Dessa transaktioner debiteras [Azure Storage avgifter](https://azure.microsoft.com/pricing/details/storage/). Mer information om hur tillstånds känsliga Logic Apps lagrar data i extern lagring finns i [tillstånds känsligt jämfört med tillstånds lösa](logic-apps-overview-preview.md#stateful-stateless).

* Om du vill distribuera till en Docker-behållare behöver du en befintlig Docker-behållar avbildning. Du kan till exempel skapa avbildningen via [Azure Container Registry](../container-registry/container-registry-intro.md), [App Service](../app-service/overview.md)eller [Azure Container instance](../container-instances/container-instances-overview.md). 

* Om du vill bygga samma exempel på Logic-appen i den här artikeln behöver du ett Office 365 Outlook-e-postkonto som använder ett arbets-eller skol konto från Microsoft för att logga in.

  Om du väljer att använda en annan [e-postanslutning som stöds av Azure Logic Apps](/connectors/), till exempel Outlook.com eller [Gmail](../connectors/connectors-google-data-security-privacy-policy.md), kan du fortfarande följa exemplet och de allmänna övergripande stegen är desamma, men användar gränssnittet och alternativen kan variera på vissa sätt. Om du till exempel använder Outlook.com-anslutningsprogrammet använder du din personliga Microsoft-konto i stället för att logga in.

* Om du vill testa den exempel Logic-app som du skapar i den här artikeln behöver du ett verktyg som kan skicka anrop till utlösaren för begäran, vilket är det första steget i exempel Logic app. Om du inte har ett sådant verktyg kan du ladda ned, installera och använda [Postman](https://www.postman.com/downloads/).

* Om du skapar din Logi Kap par med inställningar som stöder användning av [Application Insights](../azure-monitor/app/app-insights-overview.md), kan du välja att aktivera diagnostikloggning och spårning för din Logic app. Du kan göra det antingen när du skapar din Logic app eller efter distributionen. Du måste ha en Application Insights instans, men du kan skapa den här resursen [i förväg](../azure-monitor/app/create-workspace-resource.md)när du skapar din Logic app eller efter distributionen.

## <a name="create-the-logic-app-resource"></a>Skapa en Logic app-resurs

1. Logga in på [Azure Portal](https://portal.azure.com) med autentiseringsuppgifterna för ditt Azure-konto.

1. I sökrutan Azure Portal anger `logic app preview` du och väljer **Logic app (för hands version)**.

   ![Skärm bild som visar sökrutan Azure Portal med Sök termen "Logic Apps Preview" och "Logic app (för hands version)" vald.](./media/create-stateful-stateless-workflows-azure-portal/find-logic-app-resource-template.png)

1. På sidan **Logic app (för hands version)** väljer du **Lägg till**.

1. På sidan **skapa Logic app (förhands granskning)** , på fliken **grundläggande** , anger du den här informationen om din Logic app.

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Prenumeration** | Ja | <*Azure-prenumeration-namn*> | Den Azure-prenumeration som ska användas för din Logic app. |
   | **Resursgrupp** | Ja | <*Azure-resurs-grupp-namn*> | Den Azure-resurs grupp där du skapar din Logic app och relaterade resurser. Resurs namnet måste vara unikt i flera regioner och får bara innehålla bokstäver, siffror, bindestreck ( **-** ), under streck (**_**), parenteser (**()**) och punkter (**.**). <p><p>I det här exemplet skapas en resurs grupp med namnet `Fabrikam-Workflows-RG` . |
   | **Namn på Logic app** | Ja | <*Logic – App-Name*> | Namnet som ska användas för din Logic app. Resurs namnet måste vara unikt i flera regioner och får bara innehålla bokstäver, siffror, bindestreck ( **-** ), under streck (**_**), parenteser (**()**) och punkter (**.**). <p><p>I det här exemplet skapas en Logic-app med namnet `Fabrikam-Workflows` . <p><p>**Obs!** din Logic Apps namn hämtar automatiskt suffixet, `.azurewebsites.net` eftersom den **logiska appen (förhands granskning)** -resursen drivs av Azure Functions, som använder samma namngivnings konvention för appar. |
   | **Publicera** | Ja | <*distribution – miljö*> | Distributions målet för din Logic app. Du kan distribuera till Azure genom att välja **arbets flöde** eller **Docker-behållare**. <p><p>I det här exemplet används **arbets flöde**, som distribuerar **Logic app-resursen (förhands granskning)** till Azure Portal. <p><p>**Obs**: innan du väljer **Docker-behållaren** ser du till att skapa din Docker-behållar avbildning. Du kan till exempel skapa avbildningen via [Azure Container Registry](../container-registry/container-registry-intro.md), [App Service](../app-service/overview.md)eller [Azure Container instance](../container-instances/container-instances-overview.md). På så sätt kan du, när du har valt **Docker-behållare**, [Ange den behållare som du vill använda i din Logic Apps-inställningar](#set-docker-container). |
   | **Region** | Ja | <*Azure-region*> | Den Azure-region som ska användas när du skapar resurs gruppen och resurserna. <p><p>I det här exemplet används **västra USA**. |
   |||||

   Här är ett exempel:

   ![Skärm bild som visar sidan Azure Portal och "skapa Logic app (förhands granskning)".](./media/create-stateful-stateless-workflows-azure-portal/create-logic-app-resource-portal.png)

1. Sedan anger du den här informationen om lagrings lösningen och den värd plan som ska användas för din Logic app på fliken **värd** .

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Lagringskonto** | Ja | <*Azure-Storage – konto-namn*> | Det [Azure Storage konto](../storage/common/storage-account-overview.md) som ska användas för lagrings transaktioner. Resurs namnet måste vara unikt i flera regioner och får innehålla 3-24 tecken med endast siffror och gemena bokstäver. Välj antingen ett befintligt konto eller skapa ett nytt konto. <p><p>I det här exemplet skapas ett lagrings konto med namnet `fabrikamstorageacct` . |
   | **Plantyp** | Ja | <*Azure-värd – plan*> | [Värd planen](../app-service/overview-hosting-plans.md) som används för att distribuera din Logic app, som antingen [**fungerar som Premium**](../azure-functions/functions-premium-plan.md) eller [ **App Service-plan** (dedikerad)](../azure-functions/dedicated-plan.md). Ditt val påverkar de funktioner och pris nivåer som senare är tillgängliga för dig. <p><p>I det här exemplet används **App Service-planen**. <p><p>**Obs!** på liknande sätt som Azure Functions kräver resurs typen **Logic app (för hands version)** en värd plan och pris nivå. Förbruknings planer stöds inte eller är inte tillgängliga för den här resurs typen. Mer information finns i följande avsnitt: <p><p>- [Azure Functions skala och vara värd](../azure-functions/functions-scale.md) <br>- [App Service pris information](https://azure.microsoft.com/pricing/details/app-service/) <p><p>Till exempel ger Functions Premium-planen åtkomst till nätverksfunktioner, till exempel ansluta och integrera privat med virtuella Azure-nätverk, ungefär som Azure Functions när du skapar och distribuerar dina Logi Kap par. Mer information finns i följande avsnitt: <p><p>- [Azure Functions nätverks alternativ](../azure-functions/functions-networking-options.md) <br>- [Azure Logic Apps som kör nätverks möjligheter med Azure Logic Apps för hands version](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047) |
   | **Windows-plan** | Ja | <*plan-namn*> | Det plan namn som ska användas. Välj antingen en befintlig plan eller ange namnet på en ny plan. <p><p>I det här exemplet används namnet `Fabrikam-Service-Plan` . |
   | **SKU och storlek** | Ja | <*pris nivå*> | Den [pris nivå](../app-service/overview-hosting-plans.md) som ska användas för att vara värd för din Logic app. Dina val påverkas av den plan typ som du valde tidigare. Om du vill ändra standard nivån väljer du **ändra storlek**. Du kan sedan välja andra pris nivåer baserat på den arbets belastning du behöver. <p><p>I det här exemplet används den kostnads fria **F1-pris nivån** för arbets belastningar för **utveckling/testning** . Mer information hittar du i [App Service pris information](https://azure.microsoft.com/pricing/details/app-service/). |
   |||||

1. Sedan kan du, om du skapar och distribuerar inställningarna med hjälp av [Application Insights](../azure-monitor/app/app-insights-overview.md), aktivera diagnostikloggning och spårning för din Logic app.

   1. På fliken **övervakning** under **Application Insights** anger du **Aktivera Application Insights** till **Ja** om det inte redan är markerat.

   1. För inställningen **Application Insights** väljer du antingen en befintlig Application Insights instans, eller om du vill skapa en ny instans, Välj **Skapa ny** och ange det namn som du vill använda.

1. När Azure har verifierat dina Logic Apps-inställningar går du till fliken **Granska + skapa** och väljer **skapa**.

   Exempel:

   ![Skärm bild som visar Azure Portal och nya resurs inställningar för Logic app.](./media/create-stateful-stateless-workflows-azure-portal/check-logic-app-resource-settings.png)

   > [!TIP]
   > Om du får ett verifierings fel när du har valt **skapa**, öppna och granska fel informationen. Om din valda region till exempel når en kvot för resurser som du försöker skapa, kan du behöva testa en annan region.

   När Azure har slutfört distributionen är din Logic app automatiskt Live och körs, men det gör inget ännu eftersom det inte finns några arbets flöden.

1. På sidan distributions slut för ande väljer du **gå till resurs** så att du kan börja skapa ditt arbets flöde. Om du har valt **Docker-behållare** för att distribuera din Logic app fortsätter du med [stegen för att ange information om den Docker-behållaren](#set-docker-container).

   ![Skärm bild som visar Azure Portal och den färdiga distributionen.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-completed-deployment.png)

<a name="set-docker-container"></a>

## <a name="specify-docker-container-for-deployment"></a>Ange Docker-behållare för distribution

Innan du startar de här stegen behöver du en Docker-behållar avbildning. Du kan till exempel skapa avbildningen via [Azure Container Registry](../container-registry/container-registry-intro.md), [App Service](../app-service/overview.md)eller [Azure Container instance](../container-instances/container-instances-overview.md). Du kan sedan ange information om din Docker-behållare när du har skapat din Logic app.

1. I Azure Portal går du till din Logic app-resurs.

1. På menyn Logic app, under **Inställningar**, väljer du **distributions Center**.

1. I fönstret **distributions Center** följer du anvisningarna för att ange och hantera informationen för din Docker-behållare.

<a name="add-workflow"></a>

## <a name="add-a-blank-workflow"></a>Lägg till ett tomt arbets flöde

1. När Azure har öppnat resursen väljer du **arbets flöden** på din Logic Apps-meny. I verktygsfältet **arbets flöden** väljer du **Lägg till**.

   ![Skärm bild som visar menyn för Logic app-resursen med "arbets flöden" markerat och sedan är du klar med det i verktygsfältet, "Lägg till" markerat.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-add-blank-workflow.png)

1. När fönstret **nytt arbets flöde** öppnas anger du ett namn för arbets flödet och väljer antingen [ **tillstånds känslig** eller **tillstånds lös**](logic-apps-overview-preview.md#stateful-stateless) arbets flödes typ. När du är färdig väljer du **Skapa**.

   I det här exemplet läggs ett tomt tillstånds känsligt arbets flöde till med namnet `Fabrikam-Stateful-Workflow` . Som standard är arbets flödet aktiverat, men det gör inte något förrän du lägger till en utlösare och åtgärder.

   ![Skärm bild som visar det nyligen tillagda, tomma tillstånds känsliga arbets flödet "Fabrikam-Stateable-Workflow".](./media/create-stateful-stateless-workflows-azure-portal/logic-app-blank-workflow-created.png)

1. Öppna sedan det tomma arbets flödet i designern så att du kan lägga till en utlösare och en åtgärd.

   1. Välj det tomma arbets flödet i arbets flödes listan.

   1. Välj **Designer** under **utvecklare** på arbets flödes menyn.

      På design ytan visas alternativet **Välj en åtgärd** redan och är markerat som standard så att rutan **Lägg till utlösare** visas också öppen.

      ![Skärm bild som visar den öppna arbets flödes konstruktören med alternativet "Välj en åtgärd" som har marker ATS på design ytan.](./media/create-stateful-stateless-workflows-azure-portal/opened-logic-app-designer-blank-workflow.png)

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-an-action"></a>Lägg till en utlösare och en åtgärd

Det här exemplet skapar ett arbets flöde som har följande steg:

* Den inbyggda [begär ande utlösaren](../connectors/connectors-native-reqres.md), **när en http-begäran tas emot**, som tar emot inkommande samtal eller begär Anden och skapar en slut punkt som andra tjänster eller Logi Kap par kan anropa.

* [Office 365 Outlook-åtgärd](../connectors/connectors-create-api-office365-outlook.md), **Skicka ett e-postmeddelande**.

* Den inbyggda [svars åtgärden](../connectors/connectors-native-reqres.md)som du använder för att skicka ett svar och returnera data tillbaka till anroparen.

### <a name="add-the-request-trigger"></a>Lägg till begär ande utlösare

Innan du kan lägga till en utlösare i ett tomt arbets flöde ser du till att arbetsflödesdesignern är öppen och att rutan **Välj en åtgärd** är markerad på design ytan.

1. Bredvid design ytan, i rutan **Lägg till utlösare** , under Sök i rutan **Välj en åtgärd** , kontrollerar du att den **inbyggda** fliken är markerad. Den här fliken visar utlösare som körs internt i Azure Logic Apps.

1. I sökrutan **Välj en åtgärd** anger `when a http request` du och väljer den inbyggda begär ande utlösaren som är namngiven **när en http-begäran tas emot**.

   ![Skärm bild som visar designern och * * Lägg till ett utlösare * *-fönster med alternativet "när en HTTP-förfrågan tas emot" aktive ras.](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger.png)

   När utlösaren visas i designern öppnas utlösarens informations fönster för att Visa utlösarens egenskaper, inställningar och andra åtgärder.

   ![Skärm bild som visar design verktyget med alternativet "när en HTTP-begäran tas emot" Utlös valda och utlösnings informations fönstret öppnas.](./media/create-stateful-stateless-workflows-azure-portal/request-trigger-added-to-designer.png)

   > [!TIP]
   > Om informations fönstret inte visas ser du till att utlösaren är markerad i designern.

1. Om du behöver ta bort ett objekt från designern [följer du stegen nedan för att ta bort objekt från designern](#delete-from-designer).

1. Spara ditt arbete genom att välja **Spara** i verktygsfältet designer.

   När du sparar ett arbets flöde för första gången och det arbets flödet startar med en begär ande utlösare genererar Logic Apps tjänsten automatiskt en URL för en slut punkt som skapats av begär ande utlösaren. Senare, när du testar ditt arbets flöde, skickar du en begäran till denna URL, som utlöser utlösaren och startar arbets flödes körningen.

### <a name="add-the-office-365-outlook-action"></a>Lägg till åtgärden Office 365 Outlook

1. Välj **nytt steg** under den utlösare som du har lagt till i designern.

   Frågan **Välj en åtgärd** visas i designern och fönstret **Lägg till en åtgärd** öppnas så att du kan välja nästa åtgärd.

   > [!NOTE]
   > Om fönstret **Lägg till en åtgärd** visar fel meddelandet "det går inte att läsa egenskap" filter "för odefinierad", spara arbets flödet, läsa in sidan igen, öppna arbets flödet igen och försök igen.

1. I fönstret **Lägg till en åtgärd** går du till rutan **Välj en åtgärd** och väljer **Azure**. På den här fliken visas de hanterade anslutningar som är tillgängliga och distribuerade i Azure.

   > [!NOTE]
   > Om fönstret **Lägg till en åtgärd** visar fel meddelandet, `The access token expiry UTC time '{token-expiration-date-time}' is earlier than current UTC time '{current-date-time}'` Spara arbets flödet, Läs in sidan igen, öppna arbets flödet igen och försök att lägga till åtgärden igen.

   I det här exemplet används Office 365 Outlook-åtgärd med namnet **Skicka ett e-postmeddelande (v2)**.

   ![Skärm bild som visar designern och * * Lägg till åtgärd * * fönstret med åtgärden "skicka e-post till e-post i Office 365" vald.](./media/create-stateful-stateless-workflows-azure-portal/find-send-email-action.png)

1. I åtgärdens informations fönster väljer du **Logga** in på fliken **Skapa anslutning** så att du kan skapa en anslutning till ditt e-postkonto.

   ![Skärm bild som visar design fönstret och informations fönstret "Skicka ett e-postmeddelande (v2)" med "logga in" markerat.](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-sign-in.png)

1. När du tillfrågas om medgivande för att få åtkomst till ditt e-postkonto loggar du in med autentiseringsuppgifterna för ditt konto.

   > [!NOTE]
   > Om du får fel meddelandet kontrollerar du `Failed with error: 'The browser is closed.'. Please sign in again` om webbläsaren blockerar cookies från tredje part. Om dessa cookies är blockerade kan du försöka lägga till dem `https://portal.azure.com` i listan över platser som kan använda cookies. Om du använder Incognito läge ser du till att cookies från tredje part inte är blockerade när du arbetar i det läget.
   > 
   > Om det behövs laddar du om sidan, öppnar arbets flödet, lägger till e-poståtgärden igen och försöker skapa anslutningen.

   När Azure har skapat anslutningen visas åtgärden **skicka e-post** i designern och är markerad som standard. Om åtgärden inte är markerad väljer du åtgärden så att dess informations fönster också öppnas.

1. I åtgärdens informations fönster, på fliken **parametrar** , anger du nödvändig information för åtgärden, till exempel:

   ![Skärm bild som visar design fönstret och informations fönstret "Skicka ett e-postmeddelande" med fliken "parametrar" vald.](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-details.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Till** | Ja | <*din-e-postadress*> | E-postmottagaren, som kan vara din e-postadress i test syfte. I det här exemplet används det fiktiva e-postmeddelandet `sophiaowen@fabrikam.com` . |
   | **Ämne** | Ja | `An email from your example workflow` | E-postmeddelandets ämne |
   | **Brödtext** | Ja | `Hello from your example workflow!` | Innehållet i e-postmeddelandet |
   ||||

   > [!NOTE]
   > När du gör ändringar i informations fönstret på flikarna **Inställningar**, **statiskt resultat** eller **kör efter** tabbar, kontrollerar du att du väljer **klar** för att genomföra ändringarna innan du växlar flikar eller byter fokus till designern. Annars behåller designern dina ändringar.

1. Spara ditt arbete. I verktygsfältet designer väljer du **Spara**.

1. Om din miljö har strikta nätverks krav eller brand väggar som begränsar trafiken måste du ställa in behörigheter för alla utlösare eller åtgärds anslutningar som finns i ditt arbets flöde. För att hitta det fullständigt kvalificerade 

   Annars kan du testa arbets flödet manuellt genom att [utlösa en körning](#trigger-workflow).

<a name="firewall-setup"></a>

##  <a name="find-domain-names-for-firewall-access"></a>Hitta domän namn för brand Väggs åtkomst

Innan du distribuerar din Logic app och kör arbets flödet i Azure Portal, om din miljö har strikta nätverks krav eller brand väggar som begränsar trafiken, måste du konfigurera nätverks-eller brand Väggs behörigheter för alla utlösare eller åtgärds anslutningar i de arbets flöden som finns i din Logic app.

Följ dessa steg om du vill hitta de fullständigt kvalificerade domän namnen (FQDN) för de här anslutningarna:

1. På din Logic app-meny, under **arbets flöden**, väljer du **anslutningar**. På fliken **API-anslutningar** väljer du anslutningens resurs namn, till exempel:

   ![Skärm bild som visar menyn Azure Portal och Logic app med anslutnings resurs namnet "Connections" och "offic365" valt.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-connections.png)

1. Utöka webbläsaren tillräckligt mycket så att när **JSON-vyn** visas i webbläsarens övre högra hörn väljer du **JSON-vy**.

   ![Skärm bild som visar fönstret Azure Portal och API-anslutning med alternativet "JSON View" markerat.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-connection-view-json.png)

1. Hitta, kopiera och spara `connectionRuntimeUrl` egenskap svärdet på ett säkert sätt så att du kan konfigurera brand väggen med den här informationen.

   ![Skärm bild som visar egenskap svärdet "connectionRuntimeUrl" markerat.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-connection-runtime-url.png)

1. Upprepa de relevanta stegen för varje anslutning.

<a name="trigger-workflow"></a>

## <a name="trigger-the-workflow"></a>Utlös arbets flödet

I det här exemplet körs arbets flödet när utlösaren för förfrågningar tar emot en inkommande begäran, som skickas till URL: en för slut punkten som skapas av utlösaren. När du sparade arbets flödet för första gången genererade Logic Apps tjänsten automatiskt den här URL: en. Innan du kan skicka begäran om att utlösa arbets flödet måste du därför hitta denna URL.

1. I arbetsflödesdesignern väljer du den begär ande utlösare som är namngiven **när en HTTP-begäran tas emot**.

1. När informations fönstret öppnas går du till fliken **parametrar** och letar upp egenskapen **http post URL** . Om du vill kopiera den genererade URL: en, väljer du **kopierings-URL** : en (Kopiera fil ikon) och sparar URL: en någon annan stans. URL: en följer detta format:

   `http://<logic-app-name>.azurewebsites.net:443/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

   ![Skärm bild som visar designern med begäran utlösare och slut punkts-URL i egenskapen "HTTP POST URL".](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger-url.png)

   I det här exemplet ser URL: en ut så här:

   `https://fabrikam-workflows.azurewebsites.net:443/api/Fabrikam-Stateful-Workflow/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxxxxXXXXxxxxxXXXXxxxXXXXxxxxXXXX`

   > [!TIP]
   > Du kan också hitta slut punkts-URL: en för din Logic Apps- **Översikt** i URL-egenskapen för **arbets flödet** .
   >
   > 1. På resurs-menyn väljer du **Översikt**.
   > 1. I fönstret **Översikt** letar du reda på URL-egenskapen för **arbets flödet** .
   > 1. Om du vill kopiera slut punkts-URL: en flyttar du pekaren över slutet av slut punktens URL-text och väljer **Kopiera till Urklipp** (Kopiera fil ikon).

1. Testa URL: en genom att skicka en begäran genom att öppna [Postman](https://www.postman.com/downloads/) eller önskat verktyg för att skapa och skicka förfrågningar.

   Detta exempel fortsätter med Postman. Mer information finns i [postman komma igång](https://learning.postman.com/docs/getting-started/introduction/).

   1. I verktygsfältet Postman väljer du **nytt**.

      ![Skärm bild som visar Postman med knappen Ny markerad](./media/create-stateful-stateless-workflows-azure-portal/postman-create-request.png)

   1. I fönstret **Skapa nytt** under **Bygg block** väljer du **begäran**.

   1. I fönstret **Spara begäran** , under **namn för begäran**, anger du ett namn för begäran, till exempel `Test workflow trigger` .

   1. Under **Välj en samling eller mapp att spara till** väljer du **skapa samling**.

   1. Under **alla samlingar** anger du ett namn för samlingen som ska skapas för att ordna dina begär Anden, trycker på RETUR och väljer **Spara till <*samlings namn* >**. I det här exemplet används `Logic Apps requests` som samlings namn.

      Postman fönster för begär ande öppnas så att du kan skicka en begäran till slut punkts-URL: en för utlösaren för begäran.

      ![Skärm bild som visar Postman med fönstret öppen förfrågan](./media/create-stateful-stateless-workflows-azure-portal/postman-request-pane.png)

   1. I rutan adress i rutan adress bredvid listan metod, som för **närvarande visar som** standard metod för begäran, klistra in den URL som du tidigare kopierade och välj **Skicka**.

      ![Skärm bild som visar Postman och slut punkts-URL i rutan adress med knappen Skicka vald](./media/create-stateful-stateless-workflows-azure-portal/postman-test-endpoint-url.png)

      När utlösaren utlöses kör exempel arbets flödet och skickar ett e-postmeddelande som ser ut ungefär som i det här exemplet:

      ![Skärm bild som visar Outlook-e-post enligt beskrivningen i exemplet](./media/create-stateful-stateless-workflows-azure-portal/workflow-app-result-email.png)

<a name="view-run-history"></a>

## <a name="review-run-history"></a>Granska körningshistorik

När varje arbets flöde har körts för ett tillstånds känsligt arbets flöde kan du se körnings historiken, inklusive status för den övergripande körningen, för utlösaren och för varje åtgärd tillsammans med deras indata och utdata. I Azure Portal visas historiken och utlösnings historiken på arbets flödes nivå, inte på Logic app-nivå. Information om hur du granskar Utlös ande historik utanför körnings historiken finns i [Granska utlösare historik](#view-trigger-histories).

1. På arbets flödets meny i Azure Portal väljer du **övervaka**.

   I **övervaknings** fönstret visas körnings historiken för det arbets flödet.

   ![Skärm bild som visar arbets flödets övervaknings fönster och körnings historik.](./media/create-stateful-stateless-workflows-azure-portal/find-run-history.png)

   > [!TIP]
   > Om den senaste körnings statusen inte visas i verktygsfältet i **övervaknings** fönstret väljer du **Uppdatera**. Ingen körning sker för en utlösare som hoppas över på grund av ouppfyllda-villkor eller inga data hittas.

   | Körnings status | Beskrivning |
   |------------|-------------|
   | **Avbruten** | Körningen stoppades eller slutfördes inte på grund av externa problem, till exempel ett system avbrott eller en upphörde Azure-prenumeration. |
   | **Avbröts** | Körningen utlöstes och startades men en Cancel-begäran togs emot. |
   | **Misslyckades** | Minst en åtgärd i körningen misslyckades. Inga efterföljande åtgärder i arbets flödet har ställts in för att hantera det här problemet. |
   | **Körs** | Körningen utlöstes och pågår, men den här statusen kan också visas för en körning som är begränsad på grund av [Åtgärds gränser](logic-apps-limits-and-config.md) eller den [aktuella pris Planen](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Tips**: om du konfigurerar [diagnostikloggning](monitor-logic-apps-log-analytics.md)kan du få information om eventuella begränsnings händelser som inträffar. |
   | **Brutit** | Körningen lyckades. Om en åtgärd Miss lyckas, hanterar en efterföljande åtgärd i arbets flödet detta fel. |
   | **Tids gränsen uppnåddes** | Tids gränsen för körningen uppnåddes eftersom den aktuella varaktigheten överskred tids gränsen för körning, vilket styrs av inställningen för [ **kvarhållning av körnings historik i dagar**](logic-apps-limits-and-config.md#run-duration-retention-limits). Körningens varaktighet beräknas med hjälp av körningens start tid och tids gräns för körning vid den Start tiden. <p><p>**Obs!** om Körningens varaktighet också överskrider den aktuella *gränsen för körnings historik*, som också styrs av inställningen för [ **kvarhållning av körnings historik i dagar**](logic-apps-limits-and-config.md#run-duration-retention-limits), rensas körningen från körnings historiken med ett dagligt rensnings jobb. Oavsett om tids gränsen för körningen är slut eller slutförd beräknas alltid kvarhållningsperioden med hjälp av start tiden och den *aktuella* kvarhållningsperioden. Så om du minskar tids gränsen för en pågående körnings tid för en flygning. Körningen är dock antingen kvar eller så tas den bort från körnings historiken, baserat på om Körningens varaktighet överskred gränsen för kvarhållning. |
   | **Väntar** | Körningen har inte startat eller pausats, till exempel på grund av en tidigare arbets flödes instans som fortfarande körs. |
   |||

1. Om du vill granska statusen för varje steg i en körning väljer du den körning som du vill granska.

   Vyn körnings information öppnas och visar status för varje steg i körningen.

   ![Skärm bild som visar vyn kör information med status för varje steg i arbets flödet.](./media/create-stateful-stateless-workflows-azure-portal/review-run-details.png)

   Här är möjliga statusar som varje steg i arbets flödet kan ha:

   | Åtgärds status | Ikon | Beskrivning |
   |---------------|------|-------------|
   | **Avbruten** | ![Ikon för status för avbrutna åtgärder][aborted-icon] | Åtgärden stoppades eller avslutades inte på grund av externa problem, till exempel ett system avbrott eller en upphördende Azure-prenumeration. |
   | **Avbröts** | ![Ikon för status för avbrutna åtgärder][cancelled-icon] | Åtgärden kördes men tog emot en Cancel-begäran. |
   | **Misslyckades** | ![Ikon för åtgärds status misslyckades][failed-icon] | Det gick inte att utföra åtgärden. |
   | **Körs** | ![Ikon för åtgärds status som körs][running-icon] | Åtgärden körs för närvarande. |
   | **Överhoppad** | ![Ikon för åtgärds status "hoppar över"][skipped-icon] | Åtgärden hoppades över eftersom den direkt föregående åtgärden misslyckades. En åtgärd har ett `runAfter` villkor som kräver att föregående åtgärd har slutförts innan den aktuella åtgärden kan köras. |
   | **Brutit** | ![Ikon för status lyckad åtgärd][succeeded-icon] | Åtgärden har slutförts. |
   | **Lyckades med återförsök** | ![Ikon för åtgärds status slutförd med återförsök][succeeded-with-retries-icon] | Åtgärden lyckades men endast efter ett eller flera återförsök. Om du vill granska återförsöks historiken går du till vyn körnings historik och väljer den åtgärden så att du kan visa indata och utdata. |
   | **Tids gränsen uppnåddes** | ![Ikon för åtgärds status uppnåddes][timed-out-icon] | Åtgärden stoppades på grund av timeout-gränsen som anges i den åtgärdens inställningar. |
   | **Väntar** | ![Ikon för status för "väntande" åtgärd][waiting-icon] | Gäller för en webhook-åtgärd som väntar på en inkommande begäran från en anropare. |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-azure-portal/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-azure-portal/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-azure-portal/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-azure-portal/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-azure-portal/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-azure-portal/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-azure-portal/waiting.png

1. Om du vill granska indata och utdata för ett särskilt steg väljer du det steget.

   ![Skärm bild som visar indata och utdata i den valda åtgärden "skicka e-post".](./media/create-stateful-stateless-workflows-azure-portal/review-step-inputs-outputs.png)

1. Om du vill granska rå data och utdata för det steget, väljer du **Visa rå data** eller **visar rå** data.

<a name="view-trigger-histories"></a>

## <a name="review-trigger-histories"></a>Granska utlösare historia

För ett tillstånds känsligt arbets flöde kan du granska utlösarens historik för varje körning, inklusive utlösarens status tillsammans med indata och utdata, separat från [kontexten körnings historik](#view-run-history). I Azure Portal visas utlösarens historik och körnings historik på arbets flödes nivå, inte på Logic app-nivå. Följ dessa steg om du vill hitta dessa historiska data:

1. På arbets flödets meny i Azure Portal väljer du **Utlös ande historik** under **utvecklare**.

   I fönstret **utlösnings historik** visas utlösnings historiken för arbets flödets körningar.

1. Om du vill granska en detaljerad utlösnings Historik väljer du ID: t för den körningen.

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Aktivera eller öppna Application Insights efter distribution

När arbets flödet körs, genererar din Logi Kap telemetri och andra händelser. Du kan använda den här Telemetrin för att få bättre insyn i hur väl ditt arbets flöde körs och hur Logic Apps runtime fungerar på olika sätt. Du kan övervaka arbets flödet med hjälp av [Application Insights](../azure-monitor/app/app-insights-overview.md), som tillhandahåller telemetri i nära real tid (Live metrics). Den här funktionen kan hjälpa dig att undersöka fel och prestanda problem enklare när du använder dessa data för att diagnostisera problem, konfigurera aviseringar och skapa diagram.

Om din Logic Apps-inställningar för skapande och distribution har stöd för att använda [Application Insights](../azure-monitor/app/app-insights-overview.md), kan du välja att aktivera diagnostikloggning och spårning för din Logic app. Du kan göra det antingen när du skapar din Logi Kap par i Azure Portal eller efter distributionen. Du måste ha en Application Insights instans, men du kan skapa den här resursen [i förväg](../azure-monitor/app/create-workspace-resource.md)när du skapar din Logic app eller efter distributionen.

Följ dessa steg om du vill aktivera Application Insights i en distribuerad Logic-app eller öppna Application Insights instrument panelen om den redan är aktive rad:

1. Leta upp din distribuerade Logic-app i Azure Portal.

1. På menyn Logic app, under **Inställningar**, väljer du **Application Insights**.

1. Om Application Insights inte är aktive rad väljer du **aktivera Application Insights** i fönstret **Application Insights** . När fönstret har uppdaterats väljer du **Använd** längst ned.

   Om Application Insights är aktive rad väljer du **visa Application Insights data** i fönstret **Application Insights** .

När Application Insights har öppnat kan du granska olika mått för din Logic app. Mer information finns i följande avsnitt:

* [Azure Logic Apps som körs överallt – övervakare med Application Insights del 1](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [Azure Logic Apps som körs överallt – övervakare med Application Insights del 2](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Aktivera körnings historik för tillstånds lösa arbets flöden

Om du vill felsöka ett tillstånds lösa arbets flöde enklare kan du aktivera körnings historiken för det arbets flödet och sedan inaktivera körnings historiken när du är klar. Följ de här stegen för Azure Portal, eller om du arbetar i Visual Studio Code, se [skapa tillstånds känsliga och tillstånds lösa arbets flöden i Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless).

1. Leta upp och öppna din **Logic app-resurs (förhands granskning)** i [Azure Portal](https://portal.azure.com).

1. På menyn Logic Apps, under **Inställningar**, väljer du **konfiguration**.

1. På fliken **program inställningar** väljer du **ny program inställning**.

1. I rutan **namn** i rutan **Lägg till/redigera program inställningar** anger du namnet på åtgärds alternativet: 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. I rutan **värde** anger du följande värde: `WithStatelessRunHistory`

   Exempel:

   ![Skärm bild som visar resursen Azure Portal och Logic app (för hands version) med inställningen "konfiguration" > nya program inställningar "<" Lägg till/redigera program inställning "och" arbets flöden. {yourWorkflowName}. Alternativet OperationOptions "är inställt på" WithStatelessRunHistory ".](./media/create-stateful-stateless-workflows-azure-portal/stateless-operation-options-run-history.png)

1. Klicka på **OK** för att slutföra uppgiften. I verktygsfältet i **konfigurations** fönstret väljer du **Spara**.

1. Om du vill inaktivera körnings historik när du är klar, anger du antingen `Workflows.{yourWorkflowName}.OperationOptions` egenskapen till `None` eller tar bort egenskapen och dess värde.

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Ta bort objekt från designern

Följ dessa steg om du vill ta bort ett objekt i ditt arbets flöde från designern:

* Markera objektet, öppna objektets snabb meny (Shift + F10) och välj **ta bort**. Bekräfta genom att välja **OK**.

* Markera objektet och tryck på Delete-tangenten. Bekräfta genom att välja **OK**.

* Markera objektet så att informations fönstret öppnas för det objektet. Öppna ellipsen (**...**)-menyn i fönstrets övre högra hörn och välj **ta bort**. Bekräfta genom att välja **OK**.

  ![Skärm bild som visar ett markerat objekt i designern med det öppnade informations fönstret plus den valda ellips-knappen och kommandot "ta bort".](./media/create-stateful-stateless-workflows-azure-portal/delete-item-from-designer.png)

  > [!TIP]
  > Om menyn ellipser inte visas, expanderar du webbläsarfönstret tillräckligt mycket så att informations fönstret visar knappen med tre punkter (**...**) i det övre högra hörnet.

<a name="troubleshoot"></a>

## <a name="troubleshoot-problems-and-errors"></a>Felsök problem och fel

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>Nya utlösare och åtgärder saknas i designer-väljaren för arbets flöden som skapats tidigare

Azure Logic Apps för hands versionen stöder inbyggda åtgärder för Azure Function Operations, flytande åtgärder och XML-åtgärder, till exempel **XML-verifiering** och **transformering av XML**. Men för tidigare skapade Logic Apps kanske de här åtgärderna inte visas i designern för att du ska kunna välja om din Logic app använder en inaktuell version av tilläggs paketet `Microsoft.Azure.Functions.ExtensionBundle.Workflows` .

Åtgärda problemet genom att följa de här stegen för att ta bort den inaktuella versionen så att tilläggs paketet kan uppdateras automatiskt till den senaste versionen.

> [!NOTE]
> Den här lösningen gäller bara för de resurser för **Logic app (för hands version)** som du skapar med hjälp av Azure Portal, inte de Logic Apps som du skapar och distribuerar med hjälp av Visual Studio Code och Azure Logic Apps (för hands version). Se [utlösare som stöds och åtgärder saknas i designern i Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#missing-triggers-actions).

1. Stoppa din Logic app i Azure Portal.

   1. På din Logic app-meny väljer du **Översikt**.

   1. I verktygsfältet i **översikts** fönstret väljer du **stoppa**.

1. På din Logic app-meny, under **utvecklingsverktyg**, väljer du **Avancerade verktyg**.

1. I fönstret **Avancerade verktyg** väljer du **Go**, som öppnar kudu-miljön för din Logic app.

1. Öppna menyn för **fel söknings konsolen** i kudu-verktygsfältet och välj **cmd**. 

   Ett konsol fönster öppnas så att du kan bläddra till mappen paketera med hjälp av kommando tolken. Du kan också bläddra i katalog strukturen som visas i konsol fönstret.

1. Bläddra till följande mapp, som innehåller versions-mappar för det befintliga paketet:

   `...\home\data\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Ta bort version-mappen för det befintliga paketet. I konsol fönstret kan du köra det här kommandot där du ersätter `{bundle-version}` med den befintliga versionen:

   `rm -rf {bundle-version}`

   Exempelvis: `rm -rf 1.1.3`

   > [!TIP]
   > Om du får ett fel meddelande som "behörighet nekad" eller "fil som används", uppdaterar du sidan i webbläsaren och provar de föregående stegen igen tills mappen har tagits bort.

1. Gå tillbaka till sidan **Översikt** för din Logic apps i Azure Portal och välj **starta om**.

   Portalen hämtar och använder det senaste paketet automatiskt.

## <a name="next-steps"></a>Nästa steg

Vi vill gärna höra om dina upplevelser med den här offentliga för hands versionen!

* [Skapa problem i GitHub](https://github.com/Azure/logicapps/issues)för buggar eller problem.
* [Använd det här formuläret](https://aka.ms/lafeedback)för frågor, förfrågningar, kommentarer och annan feedback.
