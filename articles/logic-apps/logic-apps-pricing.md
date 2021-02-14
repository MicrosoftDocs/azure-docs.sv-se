---
title: Pris modeller för fakturerings &
description: Översikt över hur prissättnings-och fakturerings modeller fungerar i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 103855748c4b5d998dfc81eeb4044f5f53dae9e5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372016"
---
# <a name="pricing-and-billing-models-for-azure-logic-apps"></a>Pris-och fakturerings modeller för Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) hjälper dig att skapa och köra automatiserade integrerings arbets flöden som kan skalas i molnet. Den här artikeln beskriver hur fakturerings-och pris modeller fungerar för den Logic Apps tjänsten och relaterade resurser. För vissa pris nivåer, se [Logic Apps priser](https://azure.microsoft.com/pricing/details/logic-apps). Information om hur du kan planera, hantera och övervaka kostnader finns i [planera och hantera kostnader för Azure Logic Apps](plan-manage-costs.md).

<a name="consumption-pricing"></a>

## <a name="multi-tenant-pricing"></a>Prissättning för flera klienter

En prissättnings modell för användnings-och användnings priser gäller för logi Kap par som körs i den offentliga, globala, Logic Apps tjänsten för flera innehavare. Alla lyckade och misslyckade körningar mäts och faktureras.

En begäran om avsöknings utlösare är till exempel fortfarande avgiftsbelagd som en körning även om denna utlösare hoppas över och ingen Logic app-arbetsflöde skapas.

| Poster | Beskrivning |
|-------|-------------|
| [Inbyggda](../connectors/apis-list.md#built-in) utlösare och åtgärder | Körs internt i Logic Appss tjänsten och mäts med hjälp av [ **Åtgärds** priset](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>Till exempel är HTTP-utlösaren och begär ande utlösare inbyggda utlösare, medan åtgärden HTTP-åtgärd och svar är inbyggda åtgärder. Åtgärder för data åtgärder, batch-åtgärder, varierande åtgärder och [arbets flödes kontroll](../connectors/apis-list.md#control-workflow), till exempel slingor, villkor, växlar, parallella grenar och så vidare, är också inbyggda åtgärder. |
| [Standard anslutnings](../connectors/apis-list.md#managed-connectors) utlösare och åtgärder <p><p>[Anpassade anslutnings](../connectors/apis-list.md#custom) program utlösare och åtgärder | Mäts med [standard anslutnings priset](https://azure.microsoft.com/pricing/details/logic-apps/). |
| [Enterprise Connector](../connectors/apis-list.md#managed-connectors) -utlösare och åtgärder | Mäts med [Enterprise Connector-priset](https://azure.microsoft.com/pricing/details/logic-apps/). Men under den offentliga för hands versionen mäts företags anslutningar med hjälp av [ *standard* anslutnings priset](https://azure.microsoft.com/pricing/details/logic-apps/). |
| Åtgärder inuti [slingor](logic-apps-control-flow-loops.md) | Varje åtgärd som körs i en slinga mäts för varje loop som körs. <p><p>Anta till exempel att du har en "för varje"-loop som innehåller åtgärder som bearbetar en lista. Logic Apps service mätar varje åtgärd som körs i slingan genom att multiplicera antalet List objekt med antalet åtgärder i slingan och lägger till den åtgärd som startar slingan. Det innebär att beräkningen för en lista med 10 objekt är (10 * 1) + 1, vilket resulterar i 11 åtgärds körningar. |
| Antal återförsök | För att hantera de mest grundläggande undantagen och felen kan du konfigurera en [princip för återförsök](logic-apps-exception-handling.md#retry-policies) för utlösare och åtgärder där det stöds. Dessa återförsök tillsammans med den ursprungliga begäran debiteras enligt priserna baserat på om utlösaren eller åtgärden har inbyggd typ, standard eller företags typ. Till exempel debiteras en åtgärd som körs med 2 återförsök för tre åtgärds körningar. |
| [Data lagring och lagrings förbrukning](#data-retention) | Mäts med hjälp av data lagrings priset, som du hittar på [sidan Logic Apps priser](https://azure.microsoft.com/pricing/details/logic-apps/), under **pris informations** tabellen. |
|||

Mer information finns i följande:

* [Visa mått för körningar och lagrings förbrukning](plan-manage-costs.md#monitor-billing-metrics)
* [Gränser i Azure Logic Apps](logic-apps-limits-and-config.md)

### <a name="not-metered"></a>Inte uppmätt

* Utlösare som hoppas över på grund av ouppfyllda-villkor
* Åtgärder som inte kördes på grund av att Logic app stoppades innan den slutfördes
* [Inaktiverade Logic Apps](#disabled-apps)

### <a name="other-related-resources"></a>Andra relaterade resurser

Logic Apps fungerar med andra relaterade resurser, till exempel integrations konton, lokala datagatewayer och integrerings tjänst miljöer (ISEs). Om du vill veta mer om priser för dessa resurser går du igenom dessa avsnitt senare i det här avsnittet:

* [On-premises data gateway (Lokal datagateway)](#data-gateway)
* [Pris modell för integrerings konto](#integration-accounts)
* [Pris modell för ISE](#fixed-pricing)

### <a name="tips-for-estimating-consumption-costs"></a>Tips för att uppskatta förbruknings kostnader

Läs följande tips för att få hjälp att beräkna mer exakta förbruknings kostnader:

* Ta hänsyn till det möjliga antalet meddelanden eller händelser som kan komma att komma efter en bestämd dag, i stället för att basera beräkningarna enbart på avsöknings intervallet.

* När en händelse eller ett meddelande uppfyller utlösarens villkor försöker många utlösare omedelbart läsa eventuella och alla andra väntande händelser eller meddelanden som uppfyller villkoren. Det innebär att även om du väljer ett längre avsöknings intervall utlöses utlösaren baserat på antalet väntande händelser eller meddelanden som är kvalificerade för start av arbets flöden. Utlösare som följer detta beteende är Azure Service Bus och Azure Event Hub.

  Anta till exempel att du konfigurerar utlösare som kontrollerar en slut punkt varje dag. När utlösaren kontrollerar slut punkten och hittar 15 händelser som uppfyller villkoren, utlöses utlösaren och kör motsvarande arbets flöde 15 gånger. Logic Apps service mätar alla åtgärder som dessa 15 arbets flöden utför, inklusive utlösare begär Anden.

<a name="fixed-pricing"></a>

## <a name="ise-pricing"></a>ISE-prissättning

En fast pris modell gäller för Logic Apps som körs i en [ *integrerings tjänst miljö* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). En ISE faktureras med hjälp av [integration service Environment priset](https://azure.microsoft.com/pricing/details/logic-apps), som är beroende av den [ISE-nivå eller *SKU*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) som du skapar. Den här prissättningen skiljer sig från priserna för flera klienter när du betalar för reserverad kapacitet och dedikerade resurser oavsett om du använder dem eller inte.

| ISE SKU | Beskrivning |
|---------|-------------|
| **Premium** | Bas enheten har [fast kapacitet](logic-apps-limits-and-config.md#integration-service-environment-ise) och [debiteras enligt Tim priset för Premium-SKU: n](https://azure.microsoft.com/pricing/details/logic-apps). Om du behöver mer data flöde kan du [lägga till fler skalnings enheter](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) när du skapar din ISE eller senare. Varje skalnings enhet debiteras enligt [Tim pris som är ungefär hälften av bas enhets](https://azure.microsoft.com/pricing/details/logic-apps)priset. <p><p>Information om kapacitet och begränsningar finns [i avsnittet ISE-gränser i Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
| **Utvecklare** | Bas enheten har [fast kapacitet](logic-apps-limits-and-config.md#integration-service-environment-ise) och [debiteras enligt Tim priset för Developer-SKU: n](https://azure.microsoft.com/pricing/details/logic-apps). Denna SKU har dock inget service avtal (SLA), skalnings kapacitet eller redundans under återvinning, vilket innebär att du kan uppleva fördröjningar eller stillestånds tid. Server dels uppdateringar kan tillfälligt avbryta tjänsten. <p><p>**Viktigt**: se till att du endast använder den här SKU: n för utforskning, experiment, utveckling och testning – inte för produktion eller prestanda testning. <p><p>Information om kapacitet och begränsningar finns [i avsnittet ISE-gränser i Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
|||

### <a name="included-at-no-extra-cost"></a>Ingår utan extra kostnad

| Poster | Beskrivning |
|-------|-------------|
| [Inbyggda](../connectors/apis-list.md#built-in) utlösare och åtgärder | Visa **kärn** etiketten och kör i samma ISE som dina Logic Apps. |
| [Anslutningsappar av standardtyp](../connectors/apis-list.md#managed-connectors) <p><p>[Enterprise-anslutningsappar](../connectors/apis-list.md#enterprise-connectors) | – Hanterade anslutningar som visar **ISE** -etiketten är särskilt utformade för att fungera utan den lokala datagatewayen och köras i samma ISE som dina Logic Apps. ISE-prissättningen innehåller så många företags anslutningar som du vill. <p><p>– Kopplingar som inte visar ISE-etiketten körs i Logic Appss tjänsten för flera innehavare. ISE-prissättningen omfattar dock dessa körningar för logi Kap par som körs i en ISE. |
| Åtgärder inuti [slingor](logic-apps-control-flow-loops.md) | I priserna för ISE ingår varje åtgärd som körs i en slinga för varje loop-cykel som körs. <p><p>Anta till exempel att du har en "för varje"-loop som innehåller åtgärder som bearbetar en lista. Om du vill hämta det totala antalet åtgärds körningar multiplicerar du antalet List objekt med antalet åtgärder i slingan och lägger till den åtgärd som startar slingan. Det innebär att beräkningen för en lista med 10 objekt är (10 * 1) + 1, vilket resulterar i 11 åtgärds körningar. |
| Antal återförsök | För att hantera de mest grundläggande undantagen och felen kan du konfigurera en [princip för återförsök](logic-apps-exception-handling.md#retry-policies) för utlösare och åtgärder där det stöds. I priser för ISE ingår återförsök tillsammans med den ursprungliga begäran. |
| [Data lagring och lagrings förbrukning](#data-retention) | Logic Apps i en ISE ådrar sig inte lagrings-och lagrings kostnader. |
| [Integrations konton](#integration-accounts) | Inkluderar användning för en enskild integrations konto nivå, baserat på ISE SKU, utan extra kostnad. |
|||

Information om begränsningar finns [i avsnittet ISE-gränser i Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise).

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Integrationskonton

Ett [integrations konto](../logic-apps/logic-apps-pricing.md#integration-accounts) är en separat resurs som du skapar och länkar till logi Kap par så att du kan utforska, bygga och testa B2B-integrerings lösningar som använder funktioner för [EDI](logic-apps-enterprise-integration-b2b.md) och [XML-bearbetning](logic-apps-enterprise-integration-xml.md) . Azure Logic Apps erbjuder dessa nivåer eller nivåer för integrerings kontona:

| Nivå | Beskrivning |
|------|-------------|
| **Basic** | För scenarier där du bara vill ha meddelande hantering eller som fungerar som en liten affärs partner som har en partner relation med en större affär senhet. <p><p>Stöds av Logic Apps service avtal. |
| **Standard** | För scenarier där du har mer komplexa B2B-relationer och ökat antal entiteter som du måste hantera. <p><p>Stöds av Logic Apps service avtal. |
| **Kostnadsfri** | I exempel scenarier, inte produktions scenarier. Den här nivån har begränsningar för regions tillgänglighet, data flöde och användning. Den kostnads fria nivån är till exempel bara tillgänglig för offentliga regioner i Azure, till exempel västra USA eller Sydostasien, men inte för [Azure Kina 21Vianet](/azure/china/overview-operations) eller [Azure Government](../azure-government/documentation-government-welcome.md). <p><p>**Obs!**: stöds inte av Logic Apps service avtal. |
|||

Information om gränser för integrations konton finns i [gränser och konfiguration för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits), till exempel:

* [Gränser för integrations konton per Azure-prenumeration](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

* [Begränsningar för olika artefakter per integrations konto](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). Artefakter omfattar handels partner, avtal, kartor, scheman, sammansättningar, certifikat, batch-konfigurationer och så vidare.

### <a name="integration-accounts-for-consumption-based-logic-apps"></a>Integrations konton för användnings Logic Apps

Integrations konton faktureras med hjälp av ett fast [integrerings konto pris](https://azure.microsoft.com/pricing/details/logic-apps/) som baseras på den konto nivå som du använder.

### <a name="ise-based-logic-apps"></a>ISE-baserade Logic Apps

Som en extra kostnad innehåller din ISE ett enda integrations konto, baserat på din ISE SKU. För en extra kostnad kan du skapa fler integrations konton för att din ISE ska använda upp till den [totala ISE-gränsen](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Läs mer om versions [pris modellen för ISE](#fixed-pricing) tidigare i det här avsnittet.

| ISE SKU | Inkluderat integrations konto | Ytterligare kostnad |
|---------|------------------------------|-----------------|
| **Premium** | Ett enda [standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integrations konto | Upp till 19 fler standard konton. Inga kostnads fria eller grundläggande konton är tillåtna. |
| **Utvecklare** | Ett enda [kostnads fritt](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integrations konto | Upp till 19 fler standard konton om du redan har ett kostnads fritt konto eller 20 totala standard konton om du inte har ett kostnads fritt konto. Inga grundläggande konton är tillåtna. |
||||

<a name="data-retention"></a>

## <a name="data-retention-and-storage-consumption"></a>Data lagring och lagrings förbrukning

Alla indata och utdata i din Logic Apps körnings historik lagras och mäts baserat på appens [körnings tid och historik kvarhållningsperiod](logic-apps-limits-and-config.md#run-duration-retention-limits).

* För logi Kap par i Logic Apps tjänsten för flera innehavare debiteras lagrings förbrukningen med ett fast pris, som du hittar [Logic Apps på sidan](https://azure.microsoft.com/pricing/details/logic-apps)med pris **information** .

* För logi Kap par i ISEs medför inte lagrings förbrukning kostnader för datakvarhållning.

Information om hur du övervakar användning av lagrings förbrukning finns i [Visa mått för körningar och lagrings förbrukning](plan-manage-costs.md#monitor-billing-metrics).

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>Lokal datagateway

En [lokal datagateway](../logic-apps/logic-apps-gateway-install.md) är en separat resurs som du skapar så att dina Logi Kap par kan komma åt lokala data med hjälp av vissa anslutningar som stöds av gateway. Anslutnings åtgärder som körs via gatewayen debiteras, men själva gatewayen debiteras inte.

<a name="disabled-apps"></a>

## <a name="disabled-logic-apps"></a>Inaktiverade Logic Apps

Inaktiverade Logic Apps debiteras inte eftersom de inte kan skapa nya instanser när de är inaktiverade. När du har inaktiverat en Logic app kan alla instanser som körs för närvarande ta lite tid innan de stoppas helt.

## <a name="next-steps"></a>Nästa steg

* [Planera och hantera kostnader för Azure Logic Apps](plan-manage-costs.md)
