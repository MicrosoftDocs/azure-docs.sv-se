---
title: Översikt över förhandsversion av Azure Logic Apps
description: Azure Logic Apps Preview är en moln lösning för att skapa automatiserad, enskild klient, tillstånds känslig och tillstånds lösa arbets flöden som integrerar appar, data, tjänster och system med minimal kod för scenarier på företags nivå.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/10/2021
ms.openlocfilehash: 7120b6ff17657232c0e614f49b75bb24263712b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102636343"
---
# <a name="overview-azure-logic-apps-preview"></a>Översikt: Azure Logic Apps för hands version

> [!IMPORTANT]
> Den här funktionen är tillgänglig som en offentlig förhandsversion utan servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med Azure Logic Apps för hands version kan du skapa automatiserings-och integrerings lösningar mellan appar, data, moln tjänster och system genom att skapa och köra Logic Apps med en enda klient med den nya resurs typen **Logic app (för hands version)** . Med den här typen av Logic-appar för en enda klient kan du skapa flera [ *tillstånds* lösa och *tillstånds lösa* arbets flöden](#stateful-stateless) som drivs av den omdesignade Azure Logic Apps Preview runtime, som ger portabilitet, bättre prestanda och flexibilitet för att distribuera och köra i olika värd miljöer, inklusive inte bara Azure, utan även Docker-behållare.

Hur är detta möjligt? Den omdesignade körningen använder [Azure Functions utöknings modell](../azure-functions/functions-bindings-register.md) och är värdbaserad som ett tillägg på Azure Functions Runtime. Den här arkitekturen innebär att du kan köra en Logic app-typ med en enda klient som Azure Functions körs. Du kan vara värd för den omdesignade körningen på nästan vilken nätverkstopologi som helst och välja en tillgänglig beräknings storlek för att hantera den nödvändiga arbets belastningen som krävs av dina arbets flöden. Mer information finns i [Introduktion till Azure Functions](../azure-functions/functions-overview.md) och [Azure Functions utlösare och bindningar](../azure-functions/functions-triggers-bindings.md).

Du kan skapa en **Logic app-resurs (för hands version)** antingen genom [att starta i Azure Portal](create-stateful-stateless-workflows-azure-portal.md) eller genom att [skapa ett projekt i Visual Studio Code med tillägget Azure Logic Apps (förhands granskning)](create-stateful-stateless-workflows-visual-studio-code.md). I Visual Studio Code kan du också skapa *och lokalt köra* dina arbets flöden i din utvecklings miljö. Oavsett om du använder portalen eller Visual Studio Code, kan du distribuera och köra Logic app-typen med en enda klient i samma typer av värd miljöer.

Den här översikten täcker följande områden:

* [Skillnader mellan Azure Logic Apps för hands versionen, Azure Logic Apps flera klient miljö och integrerings tjänst miljön](#preview-differences).

* [Skillnader mellan tillstånds känsliga och tillstånds lösa arbets flöden](#stateful-stateless), inklusive beteende skillnader mellan [kapslade tillstånds känsliga och tillstånds lösa arbets flöden](#nested-behavior).

* [Funktioner i den här offentliga för hands versionen](#public-preview-contents).

* [Hur pris modellen fungerar](#pricing-model).

* [Ändrade, begränsade, ej tillgängliga eller funktioner som inte stöds](#limited-unavailable-unsupported).

* [Gränser i Azure Logic Apps för hands version](#limits).

Mer information finns i följande avsnitt:

* [Azure Logic Apps som körs på djupet-körning](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)

* [Logic Apps kända problem med allmänt för hands version (GitHub)](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

<a name="preview-differences"></a>

## <a name="how-does-azure-logic-apps-preview-differ"></a>Hur skiljer sig Azure Logic Apps för hands versionen?

Azure Logic Apps för hands versions körning använder [Azure Functions](../azure-functions/functions-overview.md) utöknings barhet och är värdbaserad som ett tillägg i Azure Functions Runtime. Den här arkitekturen innebär att du kan köra en Logic app-typ med en enda klient som Azure Functions körs. Du kan vara värd för Azure Logic Apps för hands versions körning på nästan vilken nätverkstopologi som helst och välja en tillgänglig beräknings storlek för att hantera den nödvändiga arbets belastningen som arbets flödet behöver. Mer information om Azure Functions utöknings barhet finns i [WebJobs SDK: skapa anpassade bindningar för indata och utdata](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings).

Med den här nya metoden är Azure Logic Apps Preview-körningsmiljön och dina arbets flöden båda delarna i din app som du kan paketera tillsammans. Med den här funktionen kan du distribuera och köra dina arbets flöden genom att helt enkelt kopiera artefakter till värd miljön och starta din app. Den här metoden ger också en mer standardiserad upplevelse för att skapa distributions pipeliner runt arbets flödes projekt för att köra nödvändiga tester och verifieringar innan du distribuerar ändringar i produktions miljöer. Mer information finns i [Azure Logic Apps som körs i djupet](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564).

I följande tabell sammanfattas skillnaderna mellan hur arbets flöden delar resurser, baserat på miljön där de körs. För skillnader i gränser, se [gränser i Azure Logic Apps för hands version](#limits).

| Miljö | Resurs delning och förbrukning |
|-------------|----------------------------------|
| Azure Logic Apps (flera innehavare) | Arbets flöden *från kunder över flera klienter* delar samma bearbetning (beräkning), lagring, nätverk och så vidare. |
| Azure Logic Apps (för hands version, en-klient) | Arbets flöden *i samma Logic-app och en enda klient* delar samma bearbetning (beräkning), lagring, nätverk och så vidare. |
| Integrerings tjänst miljö (inte tillgänglig i för hands version) | Arbets flöden i *samma miljö* delar samma bearbetning (beräkning), lagring, nätverk och så vidare. |
|||

Under tiden kan du fortfarande skapa Logic app-typen multi-apps i Azure Portal och i Visual Studio Code med hjälp av Azure Logic Apps-tillägget för flera innehavare. Även om utvecklings upplevelsen skiljer sig mellan olika typer av logiska appar för flera innehavare och en och samma klient kan din Azure-prenumeration innehålla båda typerna. Du kan visa och komma åt alla distribuerade Logic Apps i din Azure-prenumeration, men apparna organiseras i sina egna kategorier och avsnitt.

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>Tillstånds känsliga och tillstånds lösa arbets flöden

Med den här typen av Logic-appar kan du skapa de här arbets flödes typerna i samma Logic-app:

* *Tillstånds känsliga*

  Skapa tillstånds känsliga arbets flöden när du behöver behålla, granska eller referera data från tidigare händelser. Dessa arbets flöden sparar indata och utdata för varje åtgärd och deras tillstånd i extern lagring, som gör det möjligt att granska körnings informationen och historiken när varje körning har slutförts. Tillstånds känsliga arbets flöden ger hög återhämtning om avbrott inträffar. När tjänster och system har återställts kan du rekonstruera avbrutna körningar från det sparade läget och köra arbets flödena igen för att slutföras. Tillstånds känsliga arbets flöden kan fortsätta att köras i upp till ett år.

* *Tillståndslös*

  Skapa tillstånds lösa arbets flöden när du inte behöver spara, granska eller referera data från tidigare händelser i extern lagring för senare granskning. Dessa arbets flöden sparar indata och utdata för varje åtgärd och deras tillstånd *endast i minnet*, i stället för att överföra dessa data till extern lagring. Därför har tillstånds lösa arbets flöden kortare körningar som vanligt vis inte är längre än 5 minuter, snabbare prestanda med snabbare svars tider, högre data flöde och minskade drifts kostnader eftersom körnings informationen och historiken inte lagras i extern lagring. Men om avbrott uppstår, återställs inte avbrutna körningar automatiskt, så att anroparen måste skicka om avbrutna körningar manuellt. De här arbets flödena kan bara köras synkront.

  För enklare fel sökning kan du aktivera körnings historiken för ett tillstånds lösa arbets flöde, vilket påverkar prestandan och inaktiverar sedan körnings historiken när du är klar. Mer information finns i [skapa tillstånds känsliga och tillstånds lösa arbets flöden i Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless) eller [skapa tillstånds känsliga och tillstånds lösa arbets flöden i Azure Portal](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless).

  > [!NOTE]
  > Tillstånds lösa arbets flöden stöder för närvarande endast *åtgärder* för [hanterade anslutningar](../connectors/apis-list.md#managed-api-connectors), som distribueras i Azure och inte utlösare. Starta arbets flödet genom att välja antingen den [inbyggda begäran, Event Hubs eller Service Bus utlösare](../connectors/apis-list.md#built-ins). Dessa Utlösare körs internt i Azure Logic Apps Preview-körningsmiljön. Mer information om begränsade, otillgängliga eller ej stödda utlösare, åtgärder och anslutningar finns i avsnittet [ändra, begränsad, ej tillgänglig eller funktioner som inte stöds](#limited-unavailable-unsupported).

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>Kapslade beteende skillnader mellan tillstånds känsliga och tillstånds lösa arbets flöden

Du kan [skapa ett arbets flöde som kan anropas](../logic-apps/logic-apps-http-endpoint.md) från andra arbets flöden i samma **Logic app (för hands version)** -resurs med hjälp av [begär ande utlösare](../connectors/connectors-native-reqres.md), [http-webhook-utlösare](../connectors/connectors-native-webhook.md)eller hanterade anslutnings utlösare som har [typen ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) och kan ta emot HTTPS-begäranden.

Här följer beteende mönster som kapslade arbets flöden kan följa efter att ett överordnat arbets flöde anropar ett underordnat arbets flöde:

* Asynkront avsöknings mönster

  Överordnad väntar inte på svar på sitt första anrop, men kontrollerar kontinuerligt den underordnade körnings historiken tills den underordnade har körts. Tillstånds känsliga arbets flöden följer som standard det här mönstret, vilket är idealiskt för långvariga underordnade arbets flöden som kan överskrida [tids gräns gränsen för begäran](../logic-apps/logic-apps-limits-and-config.md).

* Synkront mönster ("eld och glömma")

  Underordnade bekräftar anropet genom att omedelbart returnera ett `202 ACCEPTED` svar och den överordnade åtgärden fortsätter till nästa åtgärd utan att vänta på resultatet från den underordnade. I stället får den överordnade resultatet när den underordnade har körts. Underordnade tillstånds känsliga arbets flöden som inte innehåller någon svars åtgärd följer alltid synkront mönster. För underordnade tillstånds känsliga arbets flöden finns körnings historiken som du kan granska.

  Om du vill aktivera det här beteendet anger du egenskapen till i arbets flödets JSON-definition `operationOptions` `DisableAsyncPattern` . Mer information finns i [utlösare och åtgärds typer – åtgärds alternativ](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options).

* Utlös och vänta

  För ett underordnat tillstånds löst arbets flöde väntar den överordnade svaret på ett svar som returnerar resultatet från den underordnade. Det här mönstret fungerar ungefär som att använda den inbyggda [http-utlösaren eller åtgärder](../connectors/connectors-native-http.md) för att anropa ett underordnat arbets flöde. Underordnade tillstånds lösa arbets flöden som inte innehåller någon svars åtgärd returnerar omedelbart ett `202 ACCEPTED` svar, men överordnad väntar på att barnet ska slutföras innan nästa åtgärd fortsätter. Dessa beteenden gäller endast för underordnade tillstånds lösa arbets flöden.

Den här tabellen anger beteendet för det underordnade arbets flödet baserat på om den överordnade och underordnade är tillstånds känsliga, tillstånds lösa eller är blandade arbets flödes typer:

| Överordnat arbets flöde | Underordnat arbets flöde | Underordnat beteende |
|-----------------|----------------|----------------|
| Tillståndskänsliga | Tillståndskänsliga | Asynkron eller synkron med `"operationOptions": "DisableAsyncPattern"` inställning |
| Tillståndskänsliga | Tillståndslös | Utlös och vänta |
| Tillståndslös | Tillståndskänsliga | Synkront |
| Tillståndslös | Tillståndslös | Utlös och vänta |
||||

<a name="public-preview-contents"></a>

## <a name="capabilities"></a>Funktioner

Azure Logic Apps för hands versionen innehåller många aktuella och ytterligare funktioner, till exempel:

* Skapa Logi Kap par och deras arbets flöden från [400 + kopplingar](/connectors/connector-reference/connector-reference-logicapps-connectors) för SaaS-appar (program vara som en tjänst) och PaaS (Platform-as-a-Service) och-tjänster plus anslutningar för lokala system.

  * Vissa hanterade anslutningar är nu tillgängliga som inbyggda versioner, som körs på samma sätt som de inbyggda utlösarna och åtgärderna, till exempel begär ande utlösare och HTTP-åtgärd som körs internt i Azure Logic Apps för hands versions körning. De här nya inbyggda kopplingarna inkluderar till exempel Azure Service Bus, Azure Event Hubs, SQL Server och MQ.

    > [!NOTE]
    > För den inbyggda SQL Server-anslutningen kan endast åtgärden **köra fråga** ansluta direkt till virtuella Azure-nätverk utan att den [lokala datagatewayen](logic-apps-gateway-connection.md)krävs.

  * Skapa dina egna inbyggda anslutnings program för alla tjänster du behöver med hjälp av för [hands versionens utöknings ramverk](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272). På liknande sätt som med inbyggda anslutnings program, till exempel Azure Service Bus och SQL Server, men till skillnad från [anpassade anslutningar](../connectors/apis-list.md#custom-apis-and-connectors) som inte stöds för för hands versioner, ger dessa anslutningar högre data flöde, kort svars tid, lokal anslutning och körs internt i samma process som för hands versions körningen.

    Redigerings funktionen är för närvarande endast tillgänglig i Visual Studio Code, men är inte aktive rad som standard. Om du vill skapa de här kopplingarna [byter du ditt projekt från Extensions buntbaserade (Node.js) till NuGet-paket-baserad (.net)](create-stateful-stateless-workflows-visual-studio-code.md#enable-built-in-connector-authoring). Mer information finns i [Azure Logic Apps som körs var som helst – inbyggd anslutnings barhet](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272).

  * Du kan använda B2B-åtgärder för flytande åtgärder och XML-åtgärder utan ett integrations konto. Om du vill använda dessa åtgärder måste du ha flytande kartor, XML-mappningar eller XML-scheman som du kan överföra via respektive åtgärder i Azure Portal eller lägga till i Visual Studio Code-projektets **artefakter** -mapp med hjälp av mapparna **kartor** och **scheman** .

  * Skapa och distribuera Logi Kap par som kan köras var som helst eftersom Azure Logic Apps tjänsten genererar anslutnings strängar för delad åtkomst (SAS) som dessa Logi Kap par kan använda för att skicka begär anden till moln anslutningens runtime-slutpunkt. Tjänsten Logic Apps sparar dessa anslutnings strängar med andra program inställningar så att du enkelt kan lagra dessa värden i Azure Key Vault när du distribuerar i Azure.

    > [!NOTE]
    > Som standard har en **Logic app-resurs (förhands granskning)** den [systemtilldelade hanterade identiteten](../logic-apps/create-managed-service-identity.md) aktive ras automatiskt för att autentisera anslutningar vid körning. Den här identiteten skiljer sig från autentiseringsuppgifterna för autentisering eller anslutnings sträng som du använder när du skapar en anslutning. Om du inaktiverar den här identiteten fungerar inte anslutningarna vid körning. Om du vill visa den här inställningen väljer du **identitet** på din Logic Apps-meny under **Inställningar**.

* Skapa Logi Kap par med tillstånds lösa arbets flöden som bara körs i minnet så att de avslutas snabbare, svarar snabbare, har högre genomflöde och kostar mindre att köra eftersom körnings historiken och data mellan åtgärder inte finns kvar i extern lagring. Du kan också aktivera körnings historik för enklare fel sökning. Mer information finns i [tillstånds känsliga jämfört med tillstånds lösa Logic Apps](#stateful-stateless).

* Kör, testa och Felsök dina Logi Kap par och deras arbets flöden i Visual Studio kod utvecklings miljö.

  Innan du kör och testar din Logic-app kan du göra fel sökningen enklare genom att lägga till och använda Bryt punkter i **workflow.js** filen för ett arbets flöde. Bryt punkter stöds dock bara för åtgärder för tillfället, inte utlösare. Mer information finns i [skapa tillstånds känsliga och tillstånds lösa arbets flöden i Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints).

* Publicera eller distribuera Logic Apps och deras arbets flöden direkt från Visual Studio Code till olika värd miljöer som Azure och [Docker-behållare](/dotnet/core/docker/introduction).

* Aktivera diagnostik-loggning och spårnings funktioner för din Logi Kap par genom att använda [Application Insights](../azure-monitor/app/app-insights-overview.md) när de stöds av dina Azure-prenumerationer och Logic app-inställningar.

* Få åtkomst till nätverksfunktioner, till exempel Anslut och integrera privat med virtuella Azure-nätverk, ungefär som Azure Functions när du skapar och distribuerar dina Logi Kap par med [Azure Functions Premium-planen](../azure-functions/functions-premium-plan.md). Mer information finns i följande avsnitt:

  * [Nätverksalternativ för Azure Functions](../azure-functions/functions-networking-options.md)

  * [Azure Logic Apps som kör nätverks möjligheter med Azure Logic Apps för hands version](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

* Återskapa åtkomst nycklar för hanterade anslutningar som används av enskilda arbets flöden i en resurs för **Logic app (för hands version)** med en klient. För den här uppgiften [följer du samma steg för **Logic Apps** resursen för flera innehavare, men på den enskilda arbets flödes nivån](logic-apps-securing-a-logic-app.md#regenerate-access-keys), inte på resurs nivån för Logic app.

* Lägg till parallella grenar i designern för en enda innehavare genom att följa samma steg som för flera innehavare.

Mer information finns i avsnittet om [ändrade, begränsade, otillgängliga och ej stödda](#limited-unavailable-unsupported) på sidan med [kända problem med Logic Apps offentliga för hands versioner i GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Prismodell

När du skapar en Logic app-typ med en enda klient i Azure Portal eller distribuerar från Visual Studio Code, måste du välja en värd plan, antingen [App Service eller Premium](../azure-functions/functions-scale.md), för att din Logi Kap par ska använda. Den här planen avgör vilken pris modell som används för att köra din Logic app. Om du väljer App Service plan måste du också välja en [pris nivå](../app-service/overview-hosting-plans.md).

*Tillstånds känsliga* arbets flöden använder [extern lagring](../azure-functions/storage-considerations.md#storage-account-requirements), så [Azure Storage prissättningen](https://azure.microsoft.com/pricing/details/storage/) gäller för lagrings transaktioner som Azure Logic Apps för hands versions körning utför. Köer används till exempel för schemaläggning, medan tabeller och blobbar används för att lagra arbets flödes tillstånd.

> [!NOTE]
> Under den allmänt tillgängliga för hands versionen debiteras inte *ytterligare avgifter utöver* den valda planen när du kör Logic apps på App Service.

Läs följande avsnitt om du vill ha mer information om de pris modeller som gäller för resurs typen enskild klient:

* [Skala och var värd i Azure Functions](../azure-functions/functions-scale.md)
* [Skala upp en app i Azure App Service](../app-service/manage-scale-up.md)
* [Azure Functions pris information](https://azure.microsoft.com/pricing/details/functions/)
* [App Service pris information](https://azure.microsoft.com/pricing/details/app-service/)
* [Azure Storage pris information](https://azure.microsoft.com/pricing/details/storage/)

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>Ändrade, begränsade, otillgängliga eller ej stödda funktioner

I Azure Logic Apps för hands versionen har dessa funktioner ändrats eller är begränsade, otillgängliga eller stöds inte:

* **OS-support**: för närvarande fungerar inte designern i Visual Studio Code på Linux OS, men du kan fortfarande distribuera Logi Kap par som använder Logic Apps för hands versions körning till Linux-baserade virtuella datorer. Nu kan du bygga dina Logi Kap par i Visual Studio Code på Windows eller macOS och sedan distribuera till en Linux-baserad virtuell dator.

* **Utlösare och åtgärder**: inbyggda utlösare och åtgärder körs internt i Azure Logic Apps för hands versions körning, medan hanterade anslutningar distribueras i Azure. Vissa inbyggda utlösare är inte tillgängliga, till exempel glidande fönster och batch.

  För att starta arbets flödet använder du den [inbyggda upprepningen, begäran, http, HTTP-webhook, Event Hubs eller Service Bus utlösare](../connectors/apis-list.md). I designern visas inbyggda utlösare och åtgärder under den **inbyggda** fliken, medan utlösare för hanterade anslutningar och åtgärder visas på fliken **Azure** .

  > [!NOTE]
  > För att du ska kunna köra lokalt i Visual Studio Code krävs ytterligare konfiguration för webhook-baserade utlösare och åtgärder. Mer information finns i [skapa tillstånds känsliga och tillstånds lösa arbets flöden i Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#webhook-setup).

  * För *tillstånds lösa arbets flöden* visas inte fliken **Azure** när du väljer en utlösare eftersom du bara kan välja [hanterade kopplings *åtgärder*, inte utlösare](../connectors/apis-list.md#managed-api-connectors). Även om du kan aktivera Azure-distribuerade hanterade anslutningar för tillstånds lösa arbets flöden, visar inte designern några hanterade kopplings utlösare som du kan lägga till.

  * För *tillstånds känsliga arbets flöden*, förutom utlösare och åtgärder som anges som otillgängliga nedan, är både [hanterade anslutnings utlösare och åtgärder](../connectors/apis-list.md#managed-api-connectors) tillgängliga för användning.

  * Dessa utlösare och åtgärder har antingen ändrats eller begränsas, stöds inte eller är inte tillgängliga:

    * [Lokala datagateway- *utlösare*](../connectors/apis-list.md#on-premises-connectors) är inte tillgängliga, men Gateway-åtgärder *är* tillgängliga.

    * Den inbyggda åtgärden [Azure Functions – välja en Azure-funktion](logic-apps-azure-functions.md) är nu **Azure Functions-anropa en Azure-funktion**. Den här åtgärden fungerar för närvarande endast för funktioner som skapas från mallen för **http-utlösare** .

      I Azure Portal kan du välja en funktion för HTTP-utlösare där du har åtkomst genom att skapa en anslutning via användar upplevelsen. Om du inspekterar funktions åtgärdens JSON-definition i kodvyn eller **workflow.js** filen, refererar åtgärden till funktionen med hjälp av en `connectionName` referens. Den här versionen sammanfattar funktionens information som en anslutning, som du hittar i projektets **connections.jspå** fil, som är tillgänglig när du har skapat en anslutning.

      > [!NOTE]
      > I den enda klient versionen stöder funktions åtgärden endast autentisering med frågesträng. Azure Logic Apps för hands version hämtar standard nyckeln från funktionen när du gör anslutningen, lagrar nyckeln i appens inställningar och använder nyckeln för autentisering när funktionen anropas.
      >
      > Precis som med flera klient versioner, om du förnyar den här nyckeln, till exempel genom Azure Functions upplevelsen i portalen, fungerar inte längre funktions åtgärden på grund av den ogiltiga nyckeln. För att åtgärda det här problemet måste du återskapa anslutningen till den funktion som du vill anropa eller uppdatera appens inställningar med den nya nyckeln.

    * Den inbyggda åtgärden, [Infogad kod för att köra JavaScript-kod](logic-apps-add-run-inline-code.md) , är nu **Inlined Code Operations-Run-line Java Script**.

      * Infogade kod åtgärder kräver inte längre något integrations konto.

      * För macOS och Linux stöds nu **infogade kod åtgärder** när du använder tillägget Azure Logic Apps (för hands version) i Visual Studio Code.

      * Du behöver inte längre starta om din Logic-app om du gör ändringar i en **infogad Code Operations** -åtgärd.

      * **Inlined Code Operations** -åtgärder har [uppdaterade gränser](logic-apps-overview-preview.md#inline-code-limits).

    * Några [inbyggda B2B-utlösare och åtgärder för integrations konton](../connectors/apis-list.md#integration-account-connectors) är inte tillgängliga, till exempel den **flata fil** kodningen och avkodnings åtgärder.

    * Den inbyggda åtgärden [Azure Logic Apps – Välj ett Logic app-arbetsflöde](logic-apps-http-endpoint.md) är nu arbets flödes **åtgärder – anropa ett arbets flöde i den här arbets flödes appen**.

* [Anpassade anslutningar](../connectors/apis-list.md#custom-apis-and-connectors) stöds för närvarande inte för för hands version.

* **Tillgänglighet för värd plan**: om du skapar resurs typen Single-Tenant **Logic app (för hands version)** i Azure Portal eller distribuerar från Visual Studio Code, kan du bara använda Premium-eller App Service hosting-planen i Azure. Förbruknings värd planer är inte tillgängliga och stöds inte för distribution av den här resurs typen. Du kan distribuera från Visual Studio Code till en Docker-behållare, men inte till en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

* **Bryt punkts fel sökning i Visual Studio Code**: även om du kan lägga till och använda Bryt punkter i **workflow.jsi** filen för ett arbets flöde, stöds Bryt punkter endast för åtgärder för tillfället, inte utlösare. Mer information finns i [skapa tillstånds känsliga och tillstånds lösa arbets flöden i Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints).

* **Zoomnings kontroll**: zoomnings kontrollen är för närvarande inte tillgänglig i designern.

* **Utlös ande historik och körnings historik**: för resurs typen **Logic app (förhands granskning)** , Utlös ande historik och körnings historik i Azure Portal visas på arbets flödes nivå, inte på Logic app-nivå. Följ dessa steg om du vill hitta dessa historiska data:

   * Om du vill visa körnings historiken öppnar du arbets flödet i din Logic app. Välj **övervaka** under **utvecklare** på arbets flödes menyn.

   * Om du vill granska utlösarens historik öppnar du arbets flödet i din Logic app. På arbets flödes menyn väljer du **Utlös historia** under **utvecklare**.

<a name="firewall-permissions"></a>

## <a name="permit-traffic-in-strict-network-and-firewall-scenarios"></a>Tillåt trafik i strikta scenarier för nätverk och brand väggar

Om din miljö har strikta nätverks krav eller brand väggar som begränsar trafiken, måste du tillåta åtkomst för alla utlösare eller åtgärds anslutningar i dina Logic app-arbetsflöden.

Om du vill hitta de fullständigt kvalificerade domän namnen (FQDN) för de här anslutningarna granskar du motsvarande avsnitt i följande avsnitt:

* [Brand Väggs behörigheter för en enskild klients Logic Apps – Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#firewall-setup)
* [Brand Väggs behörigheter för en enda klient organisations Logic Apps – Azure Portal](create-stateful-stateless-workflows-azure-portal.md#firewall-setup)

<a name="limits"></a>

## <a name="updated-limits"></a>Uppdaterade gränser

Även om många gränser för Azure Logic Apps för hands version hålls desamma som [gränserna för Azure Logic Apps för flera innehavare](logic-apps-limits-and-config.md)har dessa gränser ändrats för Azure Logic Apps för hands version.

<a name="http-timeout-limits"></a>

### <a name="http-timeout-limits"></a>Tids gränser för HTTP

För ett enskilt inkommande samtal eller utgående samtal är tids gränsen 230 sekunder (3,9 minuter) för dessa utlösare och åtgärder:

* Utgående begäran: HTTP-utlösare, HTTP-åtgärd
* Inkommande begäran: utlösare för begäran, HTTP-webhook-utlösare, HTTP webhook-åtgärd

I jämförelse är följande tids gränser för dessa utlösare och åtgärder i andra miljöer där Logic Apps och deras arbets flöden körs:

* Azure Logic Apps för flera innehavare: 120 sekunder (2 minuter)
* Integrerings tjänst miljö: 240 sekunder (4 minuter)

Mer information finns i [http-gränser](logic-apps-limits-and-config.md#http-limits).

<a name="managed-connector-limits"></a>

### <a name="managed-connectors"></a>Hanterade anslutningar

Hanterade anslutningar är begränsade till 50 förfrågningar per minut per anslutning. Information om hur du arbetar med anslutnings begränsnings problem finns i [Hantera begränsnings problem (429-"för många begär Anden"-fel) i Azure Logic Apps](handle-throttling-problems-429-errors.md#connector-throttling).

<a name="inline-code-limits"></a>

### <a name="inline-code-operations-execute-javascript-code"></a>Infogade kod åtgärder (kör JavaScript-kod)

För en enda Logic app-definition, kör instruktionen för infogad kod åtgärder, [**Kör JavaScript-kod**](logic-apps-add-run-inline-code.md)och har dessa uppdaterade gränser:

* Det maximala antalet kod tecken ökar med 1 024 tecken till 100 000 tecken.

* Den maximala varaktigheten för kod som körs ökar från fem sekunder till 15 sekunder.

Mer information finns i [begränsningar för Logic app definition](logic-apps-limits-and-config.md#definition-limits).

## <a name="next-steps"></a>Nästa steg

* [Skapa tillstånds lösa och tillstånds lösa arbets flöden i Azure Portal](create-stateful-stateless-workflows-azure-portal.md)
* [Skapa tillstånds lösa och tillstånds lösa arbets flöden i Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md)
* [Sidan Logic Apps allmänt kända problem i GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

Vi vill också höra om din upplevelse med Azure Logic Apps för hands version!

* [Skapa problem i GitHub](https://github.com/Azure/logicapps/issues)för buggar eller problem.
* [Använd det här formuläret](https://aka.ms/lafeedback)för frågor, förfrågningar, kommentarer och annan feedback.
