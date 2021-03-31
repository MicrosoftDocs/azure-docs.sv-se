---
title: Exempel & vanliga scenarier
description: Hitta exempel, vanliga scenarier, självstudier och genom gångar för Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 5f7afe044ab4f782f2028598c2c56e75edf95860
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92317380"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Vanliga scenarier, exempel, självstudier och genom gångar för Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) hjälper dig att samordna och integrera olika tjänster genom att tillhandahålla [hundratals färdiga kopplingar](../connectors/apis-list.md)som sträcker sig från SQL Server eller SAP till Azure Cognitive Services. Logic Appss tjänsten är "Server lös", så du behöver inte oroa dig för skalning eller instanser. Allt du behöver göra är att definiera arbets flödet med en utlösare och de åtgärder som arbets flödet utför. Den underliggande plattformen hanterar skalning, tillgänglighet och prestanda. Logic Apps är särskilt användbart för användnings fall och scenarier där du behöver koordinera åtgärder i flera system och tjänster.

I den här artikeln beskrivs vanliga start punkter, exempel och scenarier som hjälper dig att lära dig mer om de funktioner och mönster som Azure Logic Apps stöder.

## <a name="common-starting-points-for-logic-app-workflows"></a>Vanliga start punkter för Logic app-arbetsflöden

Varje Logic app börjar med en [*utlösare*](../logic-apps/logic-apps-overview.md#logic-app-concepts)och bara en utlösare som startar ditt Logic app-arbetsflöde och skickar data som en del av den utlösaren. Vissa anslutningar tillhandahåller utlösare som kommer i följande typer:

* *Avsöknings utlösare*: kontrollerar regelbundet en tjänst slut punkt för nya data. När det finns nya data skapar utlösaren och kör en ny arbets flödes instans med data som indata.

* *Push-utlösare*: lyssnar efter data på en tjänst slut punkt och väntar tills en speciell händelse inträffar. När händelsen inträffar utlöses utlösaren direkt, vilket skapar och kör en ny arbets flödes instans som använder tillgängliga data som indata.

Här är exempel som beskriver ofta använda utlösare:

* *Avsöknings* utlösare:

  * Med [ **upprepnings** utlösare](../connectors/connectors-native-recurrence.md) kan du ange start datum och-tid plus upprepningen för att starta din Logic app. Du kan till exempel välja de vecko dagar och tidpunkter då din Logic-app ska initieras. Mer information finns i de här ämnena:<p>

    * [Schemalägg och kör återkommande automatiserade uppgifter, processer och arbetsflöden med Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Självstudie: skapa automatiserade, schemabaserade återkommande arbets flöden med hjälp av Azure Logic Apps](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * Med utlösaren **när ett e-postmeddelande tas emot** kan din Logi Kap par söka efter nya e-postmeddelanden från en e-postprovider som stöds av Logic Apps, till exempel [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](/connectors/gmail/), [Outlook.com](/connectors/outlook/)och så vidare.

    > [!IMPORTANT]
    > Om du vill använda Gmail Connector kan endast företags konton i G-Suite använda den här anslutningen utan begränsning i Logic Apps. Om du har ett Gmail-konto kan du använda den här anslutningen med endast vissa Google-godkända tjänster, eller så kan du [skapa en Google-klient som används för autentisering med din Gmail-anslutning](/connectors/gmail/#authentication-and-bring-your-own-application). Mer information finns i [principer för data säkerhet och sekretess för Google Connectors i Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

    Mer information finns i de här ämnena:<p>

    * [Självstudie: skapa automatiska godkännande-baserade arbets flöden med hjälp av Azure Logic Apps](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Självstudie: automatisera uppgifter för att bearbeta e-postmeddelanden med hjälp av Azure Logic Apps, Azure Functions och Azure Storage](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * [ **Http-** utlösaren](../connectors/connectors-native-http.md) kan anropa en tjänst slut punkt via http eller https. Mer information finns i [anropa, utlösa eller kapsla arbets flöden med hjälp av HTTP-slutpunkter](../logic-apps/logic-apps-http-endpoint.md).

* *Push* -utlösare:

  * [ **Begär** ande utlösare](../connectors/connectors-native-reqres.md) kan ta emot inkommande HTTPS-begäranden.

  * [ **Http-webhook-** utlösaren](../connectors/connectors-native-webhook.md) prenumererar på en tjänst slut punkt genom att registrera en *återanrops-URL* med den tjänsten. På så sätt kan tjänsten bara meddela utlösaren när den angivna händelsen inträffar, så att utlösaren inte behöver avsöka tjänsten.

När den angivna händelsen inträffar utlöses utlösaren, som skapar en ny Logic app Workflow-instans och kör åtgärderna i arbets flödet. Du kan komma åt alla data från utlösaren i hela arbets flödet. Twitter **på en ny tweet** -utlösare skickar till exempel tweet-innehållet till Logic app-körningen. För att komma igång med Azure Logic Apps kan du försöka med följande snabb starts ämnen:

* [Snabb start: skapa ditt första automatiserade arbets flöde med hjälp av Azure Logic Apps-Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Snabb start: skapa automatiserade uppgifter, processer och arbets flöden med Azure Logic Apps-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Snabb start: skapa och hantera automatiserade Logic app-arbetsflöden med hjälp av Visual Studio Code](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="control-flow-and-error-handling-capabilities"></a>Hanterings funktioner för kontroll flöde och fel hantering

Logic Apps innehåller omfattande funktioner för avancerat kontroll flöde, till exempel villkor, växlar, slingor och omfång. För att säkerställa elastiska lösningar kan du även implementera fel och undantags hantering i dina arbets flöden.

* Utföra olika åtgärder baserat på [villkors satser](../logic-apps/logic-apps-control-flow-conditional-statement.md) och [switch-instruktioner](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Upprepa steg eller bearbeta objekt i matriser och samlingar med slingor](../logic-apps/logic-apps-control-flow-loops.md)
* [Gruppera åtgärder tillsammans med omfattningar](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Lägga till fel-och undantags hantering i ett arbets flöde](../logic-apps/logic-apps-exception-handling.md)
* [Användnings fall: hur ett hälso vårds företag använder Logic app Exception-hantering för HL7 FHIR-arbetsflöden](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="create-custom-apis-and-connectors"></a>Skapa anpassade API: er och anslutningar

För system och tjänster som inte har publicerade anslutningar kan du även utöka Logic Apps.

* [Skapa anpassade API: er för anrop från Azure Logic Apps](../logic-apps/logic-apps-create-api-app.md)
* [Sök efter nya data eller händelser regelbundet med hjälp av mönstret för avsöknings utlösare](../logic-apps/logic-apps-create-api-app.md#polling-triggers)
* [Vänta och lyssna efter nya data eller händelser med hjälp av webhook-utlösaren](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)
* [Utföra tids krävande uppgifter med hjälp av mönster för avsöknings åtgärd](../logic-apps/logic-apps-create-api-app.md#async-pattern)
* [Utföra tids krävande uppgifter med hjälp av åtgärds mönstret webhook](../logic-apps/logic-apps-create-api-app.md#webhook-actions)
* [Anpassade anslutningar i Azure Logic Apps](../logic-apps/custom-connector-overview.md)

## <a name="build-business-to-business-b2b-solutions"></a>Bygg lösningar för Business-to-Business (B2B)

För företags integrerings lösningar och sömlös kommunikation mellan organisationer kan du bygga automatiserade skalbara arbets flöden för dessa scenarier med hjälp av Enterprise-integrationspaket (EIP) med Azure Logic Apps. Även om organisationer använder olika protokoll och format kan de utbyta meddelanden elektroniskt. EIP omvandlar olika format till ett format som organisationens system kan bearbeta och stöder bransch standard protokoll, inklusive AS2, X12, EDIFACT och RosettaNet. För att skapa dessa lösningar skapar du ett integrations konto, som är en separat Azure-resurs som tillhandahåller en säker, skalbar och hanterbar behållare för de artefakter som du definierar och använder med dina Logic app-arbetsflöden. Artefakter omfattar till exempel handels partners, avtal, kartor, scheman, certifikat och batch-konfigurationer.

* [Översikt: B2B-lösningar för företags integrering med Azure Logic Apps och Enterprise-integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Skapa och hantera integrationskonton för B2B-företagsintegrationer i Azure Logic Apps](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

## <a name="access-azure-virtual-network-resources"></a>Få åtkomst till Azures virtuella nätverks resurser

Ibland behöver dina Logic Apps-och integrations konton åtkomst till skyddade resurser, till exempel virtuella datorer och andra system eller tjänster, som finns i ett virtuellt Azure-nätverk. Om du vill konfigurera den här åtkomsten kan du skapa en integrerings tjänst miljö (ISE) där du kan skapa och köra Logi Kap par. En ISE är en privat och isolerad instans av den Logic Apps tjänst som använder dedikerade resurser, till exempel lagring, och som körs separat från den offentliga, "globala", Logic Apps tjänst för flera innehavare. Genom att avgränsa den isolerade privata instansen och den offentliga globala instansen kan du också minska den påverkan som andra Azure-klienter kan ha på dina appars prestanda, vilket även kallas för "störningar på grannar"-effekten.

* [Översikt: åtkomst till Azures virtuella nätverks resurser från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Ansluta till virtuella Azure-nätverk från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="deploy-manage-and-monitor-logic-apps"></a>Distribuera, hantera och övervaka Logic Apps

Du kan helt utveckla och distribuera Logi Kap par med Visual Studio, Azure DevOps eller någon annan käll kontroll och automatiserade build-verktyg. För att stödja distribution av arbets flöden och beroende anslutningar i en resurs mall använder Logic Apps mallar för Azure-resursanvändning. Visual Studio-verktyg genererar automatiskt de här mallarna, som du kan checka in till käll kontroll för versions hantering. För meddelande-och diagnostikloggar för arbets flödets körnings status Azure Logic Apps även övervaknings-och aviserings funktioner.

### <a name="deploy"></a>Distribuera

* [Snabb start: skapa automatiserade uppgifter, processer och arbets flöden med Azure Logic Apps-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Översikt: automatisera Logic app-distribution](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Skapa Azure Resource Manager-mallar för att automatisera distributionen för Azure Logic Apps](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Distribuera Azure Resource Manager-mallar för Azure Logic Apps](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
* [Exempel: Anslut till Azure Service Bus köer från Azure Logic Apps och distribuera med Azure-pipelines i Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exempel: Anslut till Azure Storage konton från Azure Logic Apps och distribuera med Azure-pipelines i Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exempel: Konfigurera en Function-app-åtgärd för Azure Logic Apps och distribuera med Azure-pipelines i Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exempel: Anslut till ett integrations konto från Azure Logic Apps och distribuera med Azure-pipelines i Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Exempel: dirigera Azure-pipelines med hjälp av Azure Logic Apps](/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

### <a name="manage"></a>Hantera

* [Hantera Logic Apps med hjälp av Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Skapa och hantera integrations konton för B2B Enterprise-integrationer](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)
* [Hantera integrerings tjänst miljö (ISE) i Azure Logic Apps](../logic-apps/ise-manage-integration-service-environment.md)

### <a name="monitor"></a>Monitor

* [Övervaka körningsstatus, granska utlösarhistorik och konfigurera aviseringar för Azure Logic Apps](../logic-apps/monitor-logic-apps.md)
* [Konfigurera Azure Monitor loggar och samla in diagnostikdata för Azure Logic Apps](../logic-apps/monitor-logic-apps-log-analytics.md)
* [Konfigurera Azure Monitor-loggar och samla in diagnostikdata för B2B-meddelanden i Azure Logic Apps](../logic-apps/monitor-b2b-messages-log-analytics.md)
* [Visa och skapa frågor för att övervaka och spåra Azure Monitor loggar för Azure Logic Apps](../logic-apps/create-monitoring-tracking-queries.md)

## <a name="handle-content-types-conversions-and-transformations"></a>Hantera innehålls typer, konverteringar och transformeringar

Du kan komma åt, konvertera och transformera flera innehålls typer med hjälp av de många funktionerna i språket för Azure Logic Apps [Workflow definition](./logic-apps-workflow-definition-language.md). Du kan till exempel konvertera mellan en sträng, JSON och XML med-och- `@json()` `@xml()` arbets flödes uttryck. Logic Appss motorn bevarar innehålls typer för att stödja innehålls överföring på ett icke-förstörande sätt mellan tjänster.

* [Hantera innehålls typer i Azure Logic Apps](../logic-apps/logic-apps-content-type.md), till exempel,, `application/` `application/octet-stream` och `multipart/formdata`
* [Referens guide för att använda funktioner i uttryck för Azure Logic Apps och energi automatisering](../logic-apps/workflow-definition-language-functions-reference.md)
* [Språk schema för arbets flödes definition för Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md)

## <a name="other-integrations-and-capabilities"></a>Andra integreringar och funktioner

Azure Logic Apps integreras med många tjänster, till exempel Azure Functions, Azure API Management, Azure App Service och anpassade HTTP-slutpunkter, till exempel REST och SOAP.

* [Anropa Azure Functions från Azure Logic Apps](../logic-apps/logic-apps-azure-functions.md)
* [Självstudie: anropa eller utlös Logi Kap par genom att använda Azure Functions och Azure Service Bus](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Självstudie: skapa en strömmande kund insikts instrument panel med Azure Logic Apps och Azure Functions](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Självstudie: skapa en funktion som integreras med Azure Logic Apps och Azure Cognitive Services för att analysera Twitter-inlägg sentiment](../azure-functions/functions-twitter-email.md)
* [Självstudie: Bygg en AI-driven social instrument panel med hjälp av Power BI och Azure Logic Apps](https://aka.ms/logicappsdemo)
* [Självstudier: Övervaka ändringar av virtuella maskiner med Azure Event Grid och Logic Apps](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Självstudie: IoT-fjärrövervakning och-meddelanden med Azure Logic Apps du ansluter IoT Hub och post lådan](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blogg: anropa SOAP-tjänster med hjälp av Azure Logic Apps](/archive/blogs/logicapps/using-soap-services-with-logic-apps)

## <a name="end-to-end-scenarios"></a>Slutpunkt-till-slutpunkt-scenarier

* [Whitepaper: integrering av ärende hantering från slut punkt till slut punkt med Azure-tjänster, till exempel Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Kundberättelser

Lär dig hur Azure Logic Apps, tillsammans med andra Azure-tjänster och Microsoft-produkter, vilket hjälpte [dessa företag](https://aka.ms/logic-apps-customer-stories) att förbättra sin rörlighet och fokusera på sina kärn företag genom att förenkla, organisera, automatisera och dirigera komplexa processer.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [anslutningar för Logic Apps](../connectors/apis-list.md)
* Lär dig mer om [B2B Enterprise integration-scenarier med Azure Logic Apps](../logic-apps/logic-apps-enterprise-integration-overview.md)