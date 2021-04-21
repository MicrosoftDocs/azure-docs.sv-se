---
title: Prissättningsmodeller & fakturering
description: Översikt över hur pris- och faktureringsmodeller fungerar i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: a3c20dd85c94c359259cf69e25bb9083d56857fc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777157"
---
# <a name="pricing-and-billing-models-for-azure-logic-apps"></a>Prissättnings- och faktureringsmodeller för Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) hjälper dig att skapa och köra automatiserade integreringsarbetsflöden som kan skalas i molnet. Den här artikeln beskriver hur fakturerings- och prismodeller fungerar för Logic Apps tjänsten och relaterade resurser. Specifika priser finns i [Logic Apps Priser.](https://azure.microsoft.com/pricing/details/logic-apps) Information om hur du kan planera, hantera och övervaka kostnader finns i [Planera och hantera kostnader för Azure Logic Apps](plan-manage-costs.md).

<a name="consumption-pricing"></a>

## <a name="multi-tenant-pricing"></a>Priser för flera innehavare

En prismodell där du betalar för användningsförbrukning gäller för logikappar som körs i den offentliga, "globala" tjänsten för Logic Apps klientorganisation. Alla lyckade och misslyckade körningar mäts och faktureras.

Till exempel mäts en begäran som en avsökningsutlösare gör fortfarande som en körning även om utlösaren hoppas över och ingen instans av logikapparbetsflödet skapas.

| Poster | Description |
|-------|-------------|
| [Inbyggda utlösare](../connectors/built-in.md) och åtgärder | Körs inbyggt i Logic Apps-tjänsten och mäts med hjälp av [ **åtgärdspriset**](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>HTTP-utlösaren och begärandeutlösaren är till exempel inbyggda utlösare, medan HTTP-åtgärden och svarsåtgärden är inbyggda åtgärder. Dataåtgärder, batchåtgärder, variabelåtgärder [](../connectors/built-in.md)och arbetsflödeskontrollåtgärder, till exempel loopar, villkor, switch, parallella grenar och så vidare, är också inbyggda åtgärder. |
| [Utlösare och åtgärder](../connectors/managed.md) för standardanslutningsappen <p><p>[Utlösare och åtgärder](../connectors/apis-list.md#custom-apis-and-connectors) för anpassad anslutningsapp | Mäts med [standardanslutningspriset](https://azure.microsoft.com/pricing/details/logic-apps/). |
| [Utlösare och åtgärder](../connectors/managed.md) för enterprise-anslutningsappen | Mäts med [enterprise-anslutningspriset](https://azure.microsoft.com/pricing/details/logic-apps/). I den offentliga förhandsversionen mäts dock Enterprise-anslutningsappar med [ *standardanslutningspriset*](https://azure.microsoft.com/pricing/details/logic-apps/). |
| Åtgärder inuti [loopar](logic-apps-control-flow-loops.md) | Varje åtgärd som körs i en loop mäts för varje loopcykel som körs. <p><p>Anta till exempel att du har en "för varje"-loop som innehåller åtgärder som bearbetar en lista. Tjänsten Logic Apps mäter varje åtgärd som körs i loopen genom att multiplicera antalet listobjekt med antalet åtgärder i loopen och lägger till åtgärden som startar loopen. Beräkningen för en lista med 10 objekt är alltså (10 * 1) + 1, vilket resulterar i 11 åtgärdskörningar. |
| Antal återförsök | Om du vill hantera de mest grundläggande undantagen och felen kan du konfigurera en [återförsöksprincip](logic-apps-exception-handling.md#retry-policies) för utlösare och åtgärder där det stöds. Dessa återförsök tillsammans med den ursprungliga begäran debiteras enligt priset baserat på om utlösaren eller åtgärden har inbyggd typ, Standard eller Enterprise. Till exempel debiteras en åtgärd som körs med 2 återförsök för 3 åtgärdskörningar. |
| [Datalagring och lagringsförbrukning](#data-retention) | Mäts med datalagringspriset, som du hittar på [Logic Apps,](https://azure.microsoft.com/pricing/details/logic-apps/)under tabellen **Prisinformation.** |
|||

Mer information finns i följande:

* [Visa mått för körningar och lagringsförbrukning](plan-manage-costs.md#monitor-billing-metrics)
* [Begränsningar i Azure Logic Apps](logic-apps-limits-and-config.md)

### <a name="not-metered"></a>Ej uppmätt

* Utlösare som hoppas över på grund av ouppfyllda villkor
* Åtgärder som inte har körts eftersom logikappen stoppades innan slutförande
* [Inaktiverade logikappar](#disabled-apps)

### <a name="other-related-resources"></a>Andra relaterade resurser

Logikappar fungerar med andra relaterade resurser, till exempel integrationskonton, lokala datagatewayer och integrationstjänstmiljöer (ISE). Mer information om priser för dessa resurser finns i de här avsnitten senare i det här avsnittet:

* [On-premises data gateway (Lokal datagateway)](#data-gateway)
* [Prissättningsmodell för integrationskonto](#integration-accounts)
* [ISE-prismodell](#fixed-pricing)

### <a name="tips-for-estimating-consumption-costs"></a>Tips för att uppskatta förbrukningskostnader

Läs följande tips för att hjälpa dig att beräkna mer exakta förbrukningskostnader:

* Fundera över det möjliga antalet meddelanden eller händelser som kan komma under en viss dag, i stället för att endast basera dina beräkningar på avsökningsintervallet.

* När en händelse eller ett meddelande uppfyller utlösarkriterierna försöker många utlösare omedelbart läsa alla väntande händelser eller meddelanden som uppfyller villkoren. Det här beteendet innebär att utlösaren utlöses baserat på antalet väntande händelser eller meddelanden som är kvalificerade för att starta arbetsflöden även när du väljer ett längre avsökningsintervall. Utlösare som följer det här beteendet omfattar Azure Service Bus och Azure Event Hub.

  Anta till exempel att du ställer in en utlösare som kontrollerar en slutpunkt varje dag. När utlösaren kontrollerar slutpunkten och hittar 15 händelser som uppfyller kriterierna utlöses utlösaren och kör motsvarande arbetsflöde 15 gånger. Tjänsten Logic Apps alla åtgärder som dessa 15 arbetsflöden utför, inklusive utlösarbegäranden.

<a name="fixed-pricing"></a>

## <a name="ise-pricing"></a>ISE-priser

En fast prismodell gäller för logikappar som körs i en [ *integrationstjänstmiljö* (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) En ISE debiteras med hjälp [Integration Service Environment priset](https://azure.microsoft.com/pricing/details/logic-apps), vilket beror på [ISE-nivån eller *SKU:n*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) som du skapar. Den här prissättningen skiljer sig från priser för flera innehavare eftersom du betalar för reserverad kapacitet och dedikerade resurser oavsett om du använder dem eller inte.

| ISE SKU | Description |
|---------|-------------|
| **Premium** | Basenheten har [fast kapacitet och](logic-apps-limits-and-config.md#integration-service-environment-ise) [debiteras per timme för Premium-SKU:n](https://azure.microsoft.com/pricing/details/logic-apps). Om du behöver mer dataflöde kan du lägga [till fler skalningsenheter](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) när du skapar ISE eller efteråt. Varje skalningsenhet debiteras till ett [timpris som är ungefär hälften av basenhetspriset.](https://azure.microsoft.com/pricing/details/logic-apps) <p><p>Information om kapacitet och gränser finns i [ISE-gränser i Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
| **Utvecklare** | Basenheten har [fast kapacitet](logic-apps-limits-and-config.md#integration-service-environment-ise) och [debiteras till ett timpris för Developer-SKU:n](https://azure.microsoft.com/pricing/details/logic-apps). Denna SKU har dock inget serviceavtal (SLA), uppskalningskapacitet eller redundans under återanvändning, vilket innebär att det kan uppstå fördröjningar eller driftstopp. Backend-uppdateringar kan tillfälligt avbryta tjänsten. <p><p>**Viktigt!** Se till att du endast använder den här SKU:n för utforskning, experiment, utveckling och testning – inte för produktions- eller prestandatestning. <p><p>Information om kapacitet och gränser finns i [ISE-gränser i Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). |
|||

### <a name="included-at-no-extra-cost"></a>Ingår utan extra kostnad

| Poster | Description |
|-------|-------------|
| [Inbyggda utlösare](../connectors/built-in.md) och åtgärder | Visa **Core-etiketten** och kör i samma ISE som dina logikappar. |
| [Anslutningsappar av standardtyp](../connectors/managed.md) <p><p>[Enterprise-anslutningsappar](../connectors/managed.md#enterprise-connectors) | – Hanterade anslutningsappar som visar **ISE-etiketten** är särskilt utformade för att fungera utan den lokala datagatewayen och köras i samma ISE som dina logikappar. ISE-prissättningen omfattar så många Enterprise-anslutningar som du vill. <p><p>– Anslutningsappar som inte visar ISE-etiketten körs i Logic Apps klientorganisation. Ise-priser inkluderar dock dessa körningar för logikappar som körs i en ISE. |
| Åtgärder inuti [loopar](logic-apps-control-flow-loops.md) | ISE-prissättningen omfattar varje åtgärd som körs i en loop för varje loopcykel som körs. <p><p>Anta till exempel att du har en "för varje"-loop som innehåller åtgärder som bearbetar en lista. Om du vill hämta det totala antalet åtgärdskörningar multiplicerar du antalet listobjekt med antalet åtgärder i loopen och lägger till åtgärden som startar loopen. Beräkningen för en lista med 10 objekt är alltså (10 * 1) + 1, vilket resulterar i 11 åtgärdskörningar. |
| Antal återförsök | Om du vill hantera de mest grundläggande undantagen och felen kan du konfigurera en [återförsöksprincip](logic-apps-exception-handling.md#retry-policies) för utlösare och åtgärder där det stöds. I PRISSÄTTNINGen för ISE ingår återförsök tillsammans med den ursprungliga begäran. |
| [Datalagring och lagringsförbrukning](#data-retention) | Logikappar i en ISE medför inte kostnader för kvarhållning och lagring. |
| [Integrationskonton](#integration-accounts) | Inkluderar användning för en enda integrationskontonivå, baserat på ISE SKU, utan extra kostnad. |
|||

Information om begränsningar finns i [ISE-gränser i Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise).

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Integrationskonton

Ett [integrationskonto](../logic-apps/logic-apps-pricing.md#integration-accounts) är en separat resurs som du skapar och länkar till logikappar så att du kan utforska, skapa och testa B2B-integreringslösningar som använder [EDI-](logic-apps-enterprise-integration-b2b.md) och XML-bearbetningsfunktioner. [](logic-apps-enterprise-integration-xml.md)

Azure Logic Apps erbjuder dessa nivåer eller nivåer för [](https://azure.microsoft.com/pricing/details/logic-apps/) integrationskonto som varierar i prissättning och faktureringsmodell, [](logic-apps-pricing.md#integration-accounts)beroende på om dina logikappar är förbrukningsbaserade eller ISE-baserade:

| Nivå | Description |
|------|-------------|
| **Basic** | För scenarier där du bara vill ha meddelandehantering eller som en liten affärspartner som har en handelspartnerrelation med en större affärsenhet. <p><p>Stöds av Logic Apps serviceavtal. |
| **Standard** | För scenarier där du har mer komplexa B2B-relationer och ett ökat antal entiteter som du måste hantera. <p><p>Stöds av Logic Apps serviceavtal. |
| **Kostnadsfri** | För undersökande scenarier, inte produktionsscenarier. Den här nivån har gränser för regionstillgänglighet, dataflöde och användning. Till exempel är den kostnadsfria nivån endast tillgänglig för offentliga regioner i Azure, till exempel USA, västra eller Sydostasien, men inte [för Azure China 21Vianet](/azure/china/overview-operations) [eller Azure Government](../azure-government/documentation-government-welcome.md). <p><p>**Obs!** Stöds inte av Logic Apps serviceavtal. |
|||

Information om begränsningar för integrationskonto finns [i Gränser och konfiguration för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits), till exempel:

* [Begränsningar för integrationskonton per Azure-prenumeration](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

* [Begränsningar för olika artefakter per integrationskonto](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). Artefakter omfattar handelspartner, avtal, kartor, scheman, sammansättningar, certifikat, batchkonfigurationer och så vidare.

### <a name="integration-accounts-for-consumption-based-logic-apps"></a>Integrationskonton för förbrukningsbaserade logikappar

Integrationskonton debiteras med ett fast [pris för integrationskontot](https://azure.microsoft.com/pricing/details/logic-apps/) som baseras på den kontonivå som du använder.

### <a name="ise-based-logic-apps"></a>ISE-baserade logikappar

Ise innehåller ett enda integrationskonto baserat på DIN ISE-SKU utan extra kostnad. För en extra kostnad kan du skapa fler integrationskonton för din ISE för att använda upp till den [totala ISE-gränsen.](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) Läs mer om [ISE-prismodellen tidigare](#fixed-pricing) i det här avsnittet.

| ISE SKU | Integrationskonto ingår | Ytterligare kostnad |
|---------|------------------------------|-----------------|
| **Premium** | Ett enda [Standard-integrationskonto](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) | Upp till 19 fler Standard-konton. Inga kostnadsfria eller grundläggande konton tillåts. |
| **Utvecklare** | Ett enda [kostnadsfritt](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integrationskonto | Upp till 19 fler Standard-konton om du redan har ett kostnadsfritt konto eller 20 totala Standard-konton om du inte har ett kostnadsfritt konto. Inga Grundläggande konton tillåts. |
||||

<a name="data-retention"></a>

## <a name="data-retention-and-storage-consumption"></a>Databevarande och lagringsförbrukning

Alla indata och utdata i logikappens körningshistorik lagras och mäts baserat på appens körningstid och kvarhållningsperiod [för historik.](logic-apps-limits-and-config.md#run-duration-retention-limits)

* För logikappar i Logic Apps-tjänsten för flera innehavare debiteras lagringsförbrukningen till ett fast pris, som du hittar [på sidan med Logic Apps-priser](https://azure.microsoft.com/pricing/details/logic-apps)under tabellen **Prisinformation.**

* För logikappar i ISE:er medför lagringsförbrukning inte kostnader för datalagring.

Information om hur du övervakar användningen av [lagringsanvändning finns i Visa mått för körningar och lagringsförbrukning.](plan-manage-costs.md#monitor-billing-metrics)

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>Lokal datagateway

En [lokal datagateway](../logic-apps/logic-apps-gateway-install.md) är en separat resurs som du skapar så att dina logikappar kan komma åt lokala data med hjälp av specifika anslutningsappar som stöds av gatewayen. Anslutningsappar som körs via gatewayen medför avgifter, men själva gatewayen medför inga avgifter.

<a name="disabled-apps"></a>

## <a name="disabled-logic-apps"></a>Inaktiverade logikappar

Inaktiverade logikappar debiteras inte eftersom de inte kan skapa nya instanser när de är inaktiverade. När du har inaktiverat en logikapp kan alla instanser som körs ta lite tid innan de stoppas helt.

## <a name="next-steps"></a>Nästa steg

* [Planera och hantera kostnader för Azure Logic Apps](plan-manage-costs.md)
