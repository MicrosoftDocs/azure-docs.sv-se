---
title: Översikt över förhandsversion av Azure Logic Apps
description: Azure Logic Apps Preview är en molnlösning för att skapa automatiserade arbetsflöden med en enda klientorganisation, tillståndslösa och tillståndslösa arbetsflöden som integrerar appar, data, tjänster och system med minimal kod för scenarier på företagsnivå.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: 27889e8309c0efaf1e2869fc39d099f38f64f7c4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764827"
---
# <a name="overview-azure-logic-apps-preview"></a>Översikt: Azure Logic Apps förhandsversion

> [!IMPORTANT]
> Den här funktionen är tillgänglig som en offentlig förhandsversion utan servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med Azure Logic Apps Preview kan du skapa automatiserings- och integreringslösningar för appar, data, molntjänster och system genom att skapa och köra logikappar med en enda klient med den nya resurstypen **Logikapp (förhandsversion).** Med den här logikapptypen för en enda klient kan du skapa flera [  tillståndslösa och tillståndslösa  ](#stateful-stateless) arbetsflöden som drivs av den omdesignade Azure Logic Apps Preview-körningen, som ger portabilitet, bättre prestanda och flexibilitet för distribution och körning i olika värdmiljöer, inklusive inte bara Azure, utan även Docker-containrar.

Hur är detta möjligt? Den omdesignade körningen [använder Azure Functions utökningsbarhetsmodell](../azure-functions/functions-bindings-register.md) och finns som ett tillägg i Azure Functions-körningen. Den här arkitekturen innebär att du kan köra logikapptypen för en enskild klient var som helst Azure Functions körs. Du kan hantera den omdesignade körningen i nästan vilken nätverkstopologi som helst och välja valfri tillgänglig beräkningsstorlek för att hantera den nödvändiga arbetsbelastning som krävs av dina arbetsflöden. Mer information finns i [Introduktion till Azure Functions](../azure-functions/functions-overview.md) och Azure Functions [utlösare och bindningar.](../azure-functions/functions-triggers-bindings.md)

Du kan skapa logikappresursen **(förhandsversion)** antingen genom att starta i [Azure Portal](create-stateful-stateless-workflows-azure-portal.md) eller genom att skapa ett projekt i [Visual Studio Code med tillägget Azure Logic Apps (förhandsversion).](create-stateful-stateless-workflows-visual-studio-code.md) I Visual Studio Code kan du skapa och *köra dina* arbetsflöden lokalt i utvecklingsmiljön. Oavsett om du använder portalen eller Visual Studio Code kan du distribuera och köra logikapptypen för en enskild klient i samma typer av värdmiljöer.

Den här översikten omfattar följande områden:

* [Skillnader mellan Azure Logic Apps Preview, Azure Logic Apps för flera innehavare och integreringstjänstmiljön](#preview-differences).

* [Skillnader mellan tillståndslösa och tillståndslösa arbetsflöden,](#stateful-stateless)inklusive beteendeskillnader mellan [kapslade tillståndslösa och tillståndslösa arbetsflöden.](#nested-behavior)

* [Funktioner i den här offentliga förhandsversionen.](#public-preview-contents)

* [Så här fungerar prismodellen](#pricing-model).

* [Funktioner som har ändrats, begränsats, inte är tillgängliga eller inte stöds.](#limited-unavailable-unsupported)

* [Begränsningar i Azure Logic Apps förhandsversion.](#limits)

Mer information finns i de här andra avsnitten:

* [Azure Logic Apps var som helst – Djupdykning i körning](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)

* [Logic Apps offentliga förhandsversionen av kända problem (GitHub)](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

<a name="preview-differences"></a>

## <a name="how-does-azure-logic-apps-preview-differ"></a>Hur skiljer Azure Logic Apps förhandsversionen?

Körningen Azure Logic Apps förhandsversionen [använder Azure Functions](../azure-functions/functions-overview.md) utökningsbarhet och finns som ett tillägg i Azure Functions körningen. Den här arkitekturen innebär att du kan köra logikapptypen för en enda klient var som helst Azure Functions körs. Du kan vara värd för Azure Logic Apps Preview-körningen på nästan vilken nätverkstopologi du vill och välja valfri tillgänglig beräkningsstorlek för att hantera den nödvändiga arbetsbelastning som arbetsflödet behöver. Mer information om utökning Azure Functions finns i [WebJobs SDK: Skapa anpassade indata- och utdatabindningar.](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings)

Med den här nya metoden är Azure Logic Apps förhandsgranskningskörning och dina arbetsflöden båda en del av din app som du kan paketera tillsammans. Med den här funktionen kan du distribuera och köra dina arbetsflöden genom att helt enkelt kopiera artefakter till värdmiljön och starta appen. Den här metoden ger också en mer standardiserad upplevelse för att skapa distributionspipelines runt arbetsflödesprojekt för att köra nödvändiga tester och valideringar innan du distribuerar ändringar till produktionsmiljöer. Mer information finns i [Azure Logic Apps Running Anywhere – Runtime Deep Dive](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564).

I följande tabell sammanfattas kortfattat skillnaderna i hur arbetsflöden delar resurser, baserat på den miljö där de körs. Skillnader i gränser finns i [Begränsningar i Azure Logic Apps Preview](#limits).

| Miljö | Resursdelning och förbrukning |
|-------------|----------------------------------|
| Azure Logic Apps (flera klient) | Arbetsflöden *från kunder över flera klienter* delar samma bearbetning (beräkning), lagring, nätverk och så vidare. |
| Azure Logic Apps (förhandsversion, en klient) | Arbetsflöden *i samma logikapp och en enda klientorganisation* delar samma bearbetning (beräkning), lagring, nätverk och så vidare. |
| Integreringstjänstmiljö (inte tillgänglig i förhandsversion) | Arbetsflöden i *samma miljö* delar samma bearbetning (beräkning), lagring, nätverk och så vidare. |
|||

Under tiden kan du fortfarande skapa logikapptypen för flera innehavare i Azure Portal och i Visual Studio Code med hjälp av tillägget för Azure Logic Apps klientorganisation. Även om utvecklingsupplevelserna skiljer sig mellan logikapptyperna för flera klient och en enda klientorganisation, kan din Azure-prenumeration innehålla båda typerna. Du kan visa och komma åt alla distribuerade logikappar i din Azure-prenumeration, men apparna är ordnade i egna kategorier och avsnitt.

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>Tillståndslösa och tillståndslösa arbetsflöden

Med logikapptypen för en enda klient kan du skapa dessa arbetsflödestyper i samma logikapp:

* *Stateful*

  Skapa tillståndsful workflows när du behöver behålla, granska eller referera till data från tidigare händelser. Dessa arbetsflöden sparar indata och utdata för varje åtgärd och deras tillstånd i extern lagring, vilket gör det möjligt att granska körningsinformationen och historiken när varje körning är klar. Tillståndsful workflows ger hög återhämtning om avbrott inträffar. När tjänster och system har återställts kan du rekonstruera avbrutna körningar från det sparade tillståndet och köra om arbetsflödena tills de har slutförts. Tillståndsful workflows kan fortsätta köras i upp till ett år.

* *Tillståndslös*

  Skapa tillståndslösa arbetsflöden när du inte behöver spara, granska eller referera till data från tidigare händelser i extern lagring för senare granskning. Dessa arbetsflöden sparar indata och utdata för varje åtgärd och deras tillstånd endast i minnet *,* i stället för att överföra dessa data till extern lagring. Därför har tillståndslösa arbetsflöden kortare körningar som normalt inte är längre än 5 minuter, snabbare prestanda med snabbare svarstider, högre dataflöde och lägre driftskostnader eftersom körningsinformationen och historiken inte lagras i extern lagring. Om avbrott inträffar återställs dock inte avbrutna körningar automatiskt, så anroparen måste skicka om de avbrutna körningarna manuellt. Dessa arbetsflöden kan bara köras synkront.

  För enklare felsökning kan du aktivera körningshistorik för ett tillståndslöst arbetsflöde, vilket påverkar prestandan, och sedan inaktivera körningshistoriken när du är klar. Mer information finns i [Skapa tillståndslösa och tillståndslösa](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless) arbetsflöden i Visual Studio Code eller Skapa [tillståndslösa och tillståndslösa arbetsflöden i Azure Portal](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless).

  > [!NOTE]
  > Tillståndslösa arbetsflöden stöder för närvarande *endast* åtgärder [för hanterade anslutningsappar](../connectors/managed.md), som distribueras i Azure och inte utlösare. Om du vill starta arbetsflödet väljer du [antingen den inbyggda begäran, den Event Hubs eller den Service Bus utlösaren](../connectors/built-in.md). Dessa utlösare körs inbyggt i Azure Logic Apps förhandsgranskningskörning. Mer information om begränsade, otillgängliga eller icke-stödda utlösare, åtgärder och anslutningsappar finns i [Ändrade, begränsade,](#limited-unavailable-unsupported)otillgängliga eller funktioner som inte stöds.

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>Kapslade beteendeskillnader mellan tillståndslösa och tillståndslösa arbetsflöden

Du [kan](../logic-apps/logic-apps-http-endpoint.md) göra ett arbetsflöde anropsbart från andra arbetsflöden som finns i samma logikappresurs **(förhandsversion)** med hjälp av begärandeutlösaren, [](../connectors/connectors-native-reqres.md)HTTP [Webhook-utlösaren](../connectors/connectors-native-webhook.md)eller hanterade anslutningsutlösare som har [apiConnectionWebhook-typen](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) och kan ta emot HTTPS-begäranden.

Här följer beteendemönster som kapslade arbetsflöden kan följa när ett överordnat arbetsflöde anropar ett underordnat arbetsflöde:

* Mönster för asynkron avsökning

  Den överordnade väntar inte på svar på det första anropet, men kontrollerar kontinuerligt barnets körningshistorik tills den underordnade körningen är klar. Som standard följer tillståndsfulla arbetsflöden det här mönstret, vilket är idealiskt för långvariga underordnade arbetsflöden som kan överskrida tidsgränsgränserna [för förfrågningar.](../logic-apps/logic-apps-limits-and-config.md)

* Synkront mönster ("fire and forget")

  Den underordnade bekräftar anropet genom att omedelbart returnera ett svar, och den överordnade fortsätter till nästa åtgärd utan att `202 ACCEPTED` vänta på resultatet från den underordnade åtgärden. I stället får den överordnade användaren resultaten när det underordnade objektet har körts klart. Underordnade tillståndsfulla arbetsflöden som inte innehåller en svarsåtgärd följer alltid det synkrona mönstret. För underordnade tillståndsfulla arbetsflöden kan du granska körningshistoriken.

  Om du vill aktivera det här beteendet anger du egenskapen till i arbetsflödets `operationOptions` JSON-definition. `DisableAsyncPattern` Mer information finns i [Utlösare och åtgärdstyper – Åtgärdsalternativ.](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)

* Utlösare och vänta

  För ett underordnat tillståndslöst arbetsflöde väntar det överordnade objektet på ett svar som returnerar resultatet från det underordnade arbetsflödet. Det här mönstret fungerar ungefär som att använda den inbyggda [HTTP-utlösaren eller åtgärden för](../connectors/connectors-native-http.md) att anropa ett underligt arbetsflöde. Underordnade tillståndslösa arbetsflöden som inte innehåller en svarsåtgärd returnerar omedelbart ett svar, men den överordnade väntar på att det underordnade ska slutföras innan nästa `202 ACCEPTED` åtgärd fortsätter. Dessa beteenden gäller endast för underordnade tillståndslösa arbetsflöden.

Den här tabellen anger beteendet för det underordnade arbetsflödet baserat på om det överordnade och underordnade arbetsflödet är tillståndslösa eller är blandade arbetsflödestyper:

| Överordnat arbetsflöde | Underarbetsflöde | Underbeteende |
|-----------------|----------------|----------------|
| Tillståndskänsliga | Tillståndskänsliga | Asynkron eller synkron med `"operationOptions": "DisableAsyncPattern"` inställning |
| Tillståndskänsliga | Tillståndslös | Utlösare och vänta |
| Tillståndslös | Tillståndskänsliga | Synkront |
| Tillståndslös | Tillståndslös | Utlösa och vänta |
||||

<a name="public-preview-contents"></a>

## <a name="capabilities"></a>Funktioner

Azure Logic Apps förhandsversionen innehåller många aktuella och ytterligare funktioner, till exempel:

* Skapa logikappar och deras arbetsflöden från [över 400](/connectors/connector-reference/connector-reference-logicapps-connectors) anslutningsappar för SaaS- (Programvara som en tjänst) och PaaS-appar (plattform som en tjänst) och tjänster samt anslutningsappar för lokala system.

  * Vissa hanterade anslutningsappar är nu tillgängliga som inbyggda versioner som körs på liknande sätt som de inbyggda utlösarna och åtgärderna, till exempel begärandeutlösaren och HTTP-åtgärden, som körs inbyggt i Azure Logic Apps Preview-körningen. Dessa nya inbyggda anslutningsappar omfattar till exempel Azure Service Bus, Azure Event Hubs, SQL Server och MQ.

    > [!NOTE]
    > För den inbyggda anslutningsappen SQL Server  kan endast åtgärden Kör fråga ansluta direkt till virtuella Azure-nätverk utan att den [lokala datagatewayen krävs.](logic-apps-gateway-connection.md)

  * Skapa egna inbyggda anslutningsappar för alla tjänster du behöver med hjälp av [förhandsversionens utökningsramverk](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272). Liknar inbyggda anslutningsappar som Azure Service Bus och SQL Server, men till [](../connectors/apis-list.md#custom-apis-and-connectors) skillnad från anpassade anslutningsappar som för närvarande inte stöds för förhandsgranskning, ger dessa anslutningsappar högre dataflöde, låg latens, lokal anslutning och körs inbyggt i samma process som förhandsgranskningskörningen.

    Redigeringsfunktionerna är för närvarande endast tillgängliga i Visual Studio Code, men är inte aktiverat som standard. Om du vill skapa de här anslutningsapparna växlar du projektet från tilläggssamlingsbaserad [(Node.js) till NuGet-paketbaserad (.NET).](create-stateful-stateless-workflows-visual-studio-code.md#enable-built-in-connector-authoring) Mer information finns i [Azure Logic Apps Running Anywhere - Built-in connector extensibility](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272).

  * Du kan använda B2B-åtgärderna för Liquid Operations och XML-åtgärder utan ett integrationskonto. Om du vill använda dessa åtgärder måste du ha Liquid-kartor, XML-kartor eller XML-scheman som du kan ladda upp via respektive åtgärder i Azure Portal eller lägga till i mappen **Artifacts** i ditt Visual Studio Code-projekt med hjälp av mapparna **Maps** och **Scheman.**

  * Skapa och distribuera logikappar som kan köras var som helst eftersom Azure Logic Apps-tjänsten genererar SAS-anslutningssträngar (signatur för delad åtkomst) som dessa logikappar kan använda för att skicka begäranden till slutpunkten för molnanslutningskörning. Tjänsten Logic Apps sparar dessa anslutningssträngar med andra programinställningar så att du enkelt kan lagra dessa värden i Azure Key Vault när du distribuerar i Azure.

    > [!NOTE]
    > Som standard har en **logikappresurs (förhandsversion)** sin system tilldelade [hanterade identitet](../logic-apps/create-managed-service-identity.md) automatiskt aktiverad för att autentisera anslutningar vid körning. Den här identiteten skiljer sig från autentiseringsuppgifterna eller anslutningssträngen som du använder när du skapar en anslutning. Om du inaktiverar den här identiteten fungerar inte anslutningarna vid körning. Om du vill visa den här inställningen går du till logikappens meny och **väljer** Identitet under **Inställningar.**

* Skapa logikappar med tillståndslösa arbetsflöden som endast körs i minnet så att de slutförs snabbare, svarar snabbare, har högre dataflöde och kostar mindre att köra eftersom körningshistoriken och data mellan åtgärder inte bevaras i extern lagring. Du kan också aktivera körningshistorik för enklare felsökning. Mer information finns i [Tillståndsful versus stateless logic apps (Tillståndsful versus stateless logic apps).](#stateful-stateless)

* Kör, testa och felsöka dina logikappar lokalt och deras arbetsflöden i Visual Studio Code-utvecklingsmiljön.

  Innan du kör och testar logikappen kan du göra felsökningen enklare genom att lägga till och använda brytpunkter iworkflow.js **på** filen för ett arbetsflöde. Brytpunkter stöds dock endast för åtgärder just nu, inte utlösare. Mer information finns i [Skapa tillståndsful and stateless workflows in Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints).

* Publicera eller distribuera logikappar direkt och deras arbetsflöden från Visual Studio Code till olika värdmiljöer som Azure och [Docker-containrar.](/dotnet/core/docker/introduction)

* Aktivera funktioner för diagnostikloggning och spårning för logikappen genom att använda [Application Insights](../azure-monitor/app/app-insights-overview.md) när det stöds av din Azure-prenumeration och inställningarna för logikappen.

* Få åtkomst till nätverksfunktioner, till exempel ansluta och integrera privat med virtuella Azure-nätverk, på liknande sätt som Azure Functions när du skapar och distribuerar dina logikappar med [hjälp Azure Functions Premium-planen](../azure-functions/functions-premium-plan.md). Mer information finns i följande avsnitt:

  * [Nätverksalternativ för Azure Functions](../azure-functions/functions-networking-options.md)

  * [Azure Logic Apps var som helst – nätverksmöjligheter med Azure Logic Apps förhandsversion](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

* Återskapa åtkomstnycklar för hanterade anslutningar som används av enskilda arbetsflöden i logikappresursen **för en klient (förhandsversion).** För den här uppgiften följer du samma steg för resursen [ **för Logic Apps**](logic-apps-securing-a-logic-app.md#regenerate-access-keys)klientorganisation, men på nivån för enskilt arbetsflöde, inte resursnivån för logikappen.

* Lägg till parallella grenar i designern för en enskild klientorganisation genom att följa samma steg som designern för flera innehavare.

Mer information finns i [Funktioner som har ändrats, begränsats,](#limited-unavailable-unsupported) inte är tillgängligt och inte stöds och på sidan kända Logic Apps allmänt [tillgängliga förhandsversioner i GitHub.](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Prismodell

När du skapar logikapptypen för en klient i Azure Portal eller distribuerar från Visual Studio Code måste du välja en värdplan, antingen App Service eller [Premium,](../azure-functions/functions-scale.md)som logikappen ska använda. Den här planen avgör vilken prismodell som gäller för att köra logikappen. Om du väljer App Service plan måste du också välja [en prisnivå.](../app-service/overview-hosting-plans.md)

*Tillståndsful* workflows [använder extern](../azure-functions/storage-considerations.md#storage-account-requirements)lagring , [så Azure Storage prissättningen](https://azure.microsoft.com/pricing/details/storage/) gäller för lagringstransaktioner som Azure Logic Apps Preview-körningen utför. Köer används till exempel för schemaläggning, medan tabeller och blobar används för att lagra arbetsflödes tillstånd.

> [!NOTE]
> Under den offentliga förhandsversionen medför körning av logikappar på App Service inga *ytterligare* avgifter utöver ditt valda abonnemang.

Mer information om de prissättningsmodeller som gäller för resurstypen för en enskild klientorganisation finns i följande avsnitt:

* [Skala och var värd i Azure Functions](../azure-functions/functions-scale.md)
* [Skala upp en app i Azure App Service](../app-service/manage-scale-up.md)
* [Azure Functions prisinformation](https://azure.microsoft.com/pricing/details/functions/)
* [App Service prisinformation](https://azure.microsoft.com/pricing/details/app-service/)
* [Azure Storage prisinformation](https://azure.microsoft.com/pricing/details/storage/)

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>Ändrade, begränsade, otillgängliga eller funktioner som inte stöds

I Azure Logic Apps förhandsversionen har dessa funktioner ändrats, eller så är de för närvarande begränsade, otillgängliga eller stöds inte:

* **Os-stöd:** Designern i Visual Studio Code fungerar för närvarande inte i Linux OS, men du kan fortfarande distribuera logikappar som använder Logic Apps Preview-körningen till Linux-baserade virtuella datorer. För tillfället kan du skapa dina logikappar i Visual Studio Code på Windows eller macOS och sedan distribuera till en Linux-baserad virtuell dator.

* **Utlösare och åtgärder:** Inbyggda utlösare och åtgärder körs inbyggt i Azure Logic Apps Preview-körningen, medan hanterade anslutningsappar distribueras i Azure. Vissa inbyggda utlösare är inte tillgängliga, till exempel skjutfönster och Batch.

  Starta arbetsflödet genom att använda den [inbyggda upprepnings-, begäran-, HTTP-, HTTP-webhooken, Event Hubs eller Service Bus utlösaren](../connectors/apis-list.md). I designern visas inbyggda utlösare och åtgärder under fliken **Inbyggd,** medan utlösare och åtgärder för hanterade anslutningsappar visas under **fliken Azure.**

  > [!NOTE]
  > Om du vill köra lokalt Visual Studio Code kräver webhook-baserade utlösare och åtgärder ytterligare konfiguration. Mer information finns i [Skapa tillståndsful and stateless workflows in Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#webhook-setup).

  * För *tillståndslösa arbetsflöden* visas **inte Azure-fliken** när du väljer en utlösare eftersom du bara kan välja hanterade [ *anslutningsåtgärder*, inte utlösare](../connectors/managed.md). Även om du kan aktivera Azure-distribuerade hanterade anslutningsappar för tillståndslösa arbetsflöden visar designern inga hanterade anslutningsutlösare som du kan lägga till.

  * För *tillståndsful workflows*, förutom de utlösare och åtgärder som anges som otillgängliga nedan, är både hanterade [anslutningsutlösare](../connectors/managed.md) och åtgärder tillgängliga för dig att använda.

  * Dessa utlösare och åtgärder har antingen ändrats eller är för närvarande begränsade, stöds inte eller är inte tillgängliga:

    * [Lokala datagatewayutlösare *är*](../connectors/managed.md#on-premises-connectors) inte tillgängliga, men *gatewayåtgärder är* tillgängliga.

    * Den inbyggda åtgärden, Azure Functions [– Välj en Azure-funktion](logic-apps-azure-functions.md) är nu Azure Function Operations – Anropa **en Azure-funktion**. Den här åtgärden fungerar för närvarande endast för funktioner som skapas från **HTTP-utlösarmallen.**

      I Azure Portal kan du välja en HTTP-utlösarfunktion där du har åtkomst genom att skapa en anslutning via användarupplevelsen. Om du granskar funktionsåtgärdens JSON-definition i kodvyn eller **workflow.jspå** filen refererar åtgärden till funktionen med hjälp av en `connectionName` referens. Den här versionen abstraherar funktionens information som en anslutning, som du hittar i projektets **connections.jspå** filen, som är tillgänglig när du har skapat en anslutning.

      > [!NOTE]
      > I versionen för en enskild klientorganisation stöder funktionsåtgärden endast frågesträngsautentisering. Azure Logic Apps Preview hämtar standardnyckeln från funktionen när du upprättar anslutningen, lagrar den nyckeln i appens inställningar och använder nyckeln för autentisering när funktionen anropas.
      >
      > Precis som med versionen för flera innehavare fungerar funktionsåtgärden inte längre på grund av den ogiltiga nyckeln om du förnyar den här nyckeln, till exempel via Azure Functions-upplevelsen i portalen. För att åtgärda det här problemet måste du återskapa anslutningen till funktionen som du vill anropa eller uppdatera appens inställningar med den nya nyckeln.

    * Den inbyggda åtgärden [Inline Code – Execute JavaScript Code](logic-apps-add-run-inline-code.md) är nu **Inline Code Operations - Run in-line JavaScript**.

      * Infogade kodåtgärder kräver inte längre ett integrationskonto.

      * För macOS och Linux stöds **nu infogade** kodåtgärder när du använder tillägget Azure Logic Apps (förhandsversion) i Visual Studio Code.

      * Du behöver inte längre starta om logikappen om du gör ändringar i en **åtgärd för infogade kodåtgärder.**

      * **Infogade kodåtgärder** har [uppdaterade gränser.](logic-apps-overview-preview.md#inline-code-limits)

    * Vissa [inbyggda B2B-utlösare](../connectors/managed.md#integration-account-connectors) och åtgärder för integrationskonton  är inte tillgängliga, till exempel kodning och avkodning av flat fil.

    * Den inbyggda åtgärden, Azure Logic Apps Välj ett [logikapparbetsflöde,](logic-apps-http-endpoint.md) är nu Arbetsflödesåtgärder – Anropa **ett arbetsflöde i den här arbetsflödesappen.**

* [Anpassade anslutningsappar](../connectors/apis-list.md#custom-apis-and-connectors) stöds för närvarande inte för förhandsversionen.

* **Tillgänglighet för värdplan:** Oavsett om du skapar resurstypen **Logic App (förhandsversion)** för en enskild klient i Azure Portal eller distribuerar från Visual Studio Code kan du bara använda Premium- eller App Service-värdplanen i Azure. Förbrukningsvärdplaner är inte tillgängliga och stöds inte för distribution av den här resurstypen. Du kan distribuera från Visual Studio Code till en Docker-container, men inte till en [integrationstjänstmiljö (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)

* **Felsökning av brytpunkter i Visual Studio Code:** Även om du kan lägga till och använda brytpunkter i **workflow.jsi** filen för ett arbetsflöde, stöds brytpunkter endast för åtgärder för närvarande, inte utlösare. Mer information finns i [Skapa tillståndsful and stateless workflows in Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints).

* **Zoomkontroll:** Zoomkontrollen är för närvarande inte tillgänglig för designern.

* **Utlösarhistorik och** körningshistorik: För resurstypen Logikapp **(förhandsversion)** visas utlösarhistorik och körningshistorik i Azure Portal på arbetsflödesnivå, inte på logikappnivå. Följ dessa steg för att hitta dessa historiska data:

   * Om du vill visa körningshistoriken öppnar du arbetsflödet i logikappen. Välj Övervaka under Utvecklare på **arbetsflödesmenyn.** 

   * Om du vill granska utlösarhistoriken öppnar du arbetsflödet i logikappen. På arbetsflödesmenyn under **Utvecklare** väljer du **Trigger Histories (Utlösningshistorik).**

<a name="firewall-permissions"></a>

## <a name="permit-traffic-in-strict-network-and-firewall-scenarios"></a>Tillåt trafik i strikta nätverks- och brandväggsscenarier

Om din miljö har strikta nätverkskrav eller brandväggar som begränsar trafiken måste du tillåta åtkomst för alla utlösare eller åtgärdsanslutningar i logikappens arbetsflöden.

Om du vill hitta fullständigt kvalificerade domännamn (FQDN) för dessa anslutningar kan du läsa motsvarande avsnitt i följande avsnitt:

* [Brandväggsbehörigheter för enskilda klientlogikappar – Visual Studio Kod](create-stateful-stateless-workflows-visual-studio-code.md#firewall-setup)
* [Brandväggsbehörigheter för enskilda klientlogikappar – Azure Portal](create-stateful-stateless-workflows-azure-portal.md#firewall-setup)

<a name="limits"></a>

## <a name="updated-limits"></a>Uppdaterade gränser

Även om många gränser Azure Logic Apps förhandsversionen förblir desamma som gränserna för [Azure Logic Apps,](logic-apps-limits-and-config.md)har dessa gränser ändrats för Azure Logic Apps förhandsversionen.

<a name="http-timeout-limits"></a>

### <a name="http-timeout-limits"></a>Tidsgränsgränser för HTTP

För ett enskilt inkommande anrop eller utgående anrop är tidsgränsen 230 sekunder (3,9 minuter) för dessa utlösare och åtgärder:

* Utgående begäran: HTTP-utlösare, HTTP-åtgärd
* Inkommande begäran: Begärandeutlösare, HTTP Webhook-utlösare, HTTP Webhook-åtgärd

Som jämförelse finns tidsgränsgränserna för dessa utlösare och åtgärder i andra miljöer där logikappar och deras arbetsflöden körs:

* Flerklients-Azure Logic Apps: 120 sekunder (2 minuter)
* Integreringstjänstmiljö: 240 sekunder (4 minuter)

Mer information finns i [HTTP-gränser.](logic-apps-limits-and-config.md#http-limits)

<a name="managed-connector-limits"></a>

### <a name="managed-connectors"></a>Hanterade anslutningsappar

Hanterade anslutningsappar är begränsade till 50 begäranden per minut per anslutning. Information om hur du arbetar med problem med anslutningsbegränsning finns i Hantera begränsningsproblem [(429 – felet "För många begäranden") i Azure Logic Apps](handle-throttling-problems-429-errors.md#connector-throttling).

<a name="inline-code-limits"></a>

### <a name="inline-code-operations-execute-javascript-code"></a>Infogade kodåtgärder (kör JavaScript-kod)

För en enskild logikappdefinition har åtgärden Inline Code Operations, [**Execute JavaScript Code**](logic-apps-add-run-inline-code.md), följande uppdaterade gränser:

* Det maximala antalet kodtecken ökar från 1 024 tecken till 100 000 tecken.

* Den maximala varaktigheten för att köra kod ökar från fem sekunder till 15 sekunder.

Mer information finns i [Definitionsbegränsningar för logikapp.](logic-apps-limits-and-config.md#definition-limits)

## <a name="next-steps"></a>Nästa steg

* [Skapa tillståndslösa och tillståndslösa arbetsflöden i Azure Portal](create-stateful-stateless-workflows-azure-portal.md)
* [Skapa tillståndslösa och tillståndslösa arbetsflöden i Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md)
* [Logic Apps offentliga förhandsversionen av kända problem i GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

Vi vill också att du hör av dig om dina erfarenheter av Azure Logic Apps förhandsversion!

* För buggar eller problem skapar [du dina problem i GitHub.](https://github.com/Azure/logicapps/issues)
* Använd det här feedbackformuläret för frågor, begäranden, kommentarer [och annan feedback.](https://aka.ms/lafeedback)
