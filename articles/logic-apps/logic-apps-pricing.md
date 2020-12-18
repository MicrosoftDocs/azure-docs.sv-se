---
title: Pris & fakturerings modell
description: Översikt över hur pris-och fakturerings arbetet för Azure Logic Apps
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 9243d089b4a000066ec03dbeeccd046db374f558
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673118"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Pris modell för Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) hjälper dig att skapa och köra automatiserade integrerings arbets flöden som kan skalas i molnet. I den här artikeln beskrivs hur fakturering och prissättning fungerar för Azure Logic Apps. Pris nivåer finns i [Logic Apps prissättning](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Prissättnings modell för förbrukning

För nya Logi Kap par som körs i offentliga, "globala", Azure Logic Apps tjänsten för flera innehavare, betalar du bara för det du använder. Dessa Logic Apps använder en förbruknings-baserad plan och pris modell. I din Logic app är varje steg en åtgärd och Azure Logic Apps mäta alla åtgärder som körs i din Logic app.

Åtgärder är till exempel:

* [Utlösare](#triggers), som är särskilda åtgärder. Alla Logic Apps kräver en utlösare som det första steget.

* ["Inbyggda" eller inbyggda åtgärder](../connectors/apis-list.md#built-in) som http, anrop till Azure Functions och API Management, och så vidare

* Anrop till [hanterade anslutningar](../connectors/apis-list.md#managed-connectors) som Outlook 365, Dropbox och så vidare

* [Kontrol lera arbets flödes åtgärder](../connectors/apis-list.md#control-workflow) som slingor, villkorliga uttryck och så vidare

[Standard anslutningar](../connectors/apis-list.md#managed-connectors) debiteras enligt [standard anslutnings priset](https://azure.microsoft.com/pricing/details/logic-apps). Allmänt tillgängliga [företags anslutningar](../connectors/apis-list.md#managed-connectors) debiteras enligt [Enterprise Connector-priset](https://azure.microsoft.com/pricing/details/logic-apps), medan den offentliga för hands versionen av företags anslutningar debiteras enligt [standard anslutnings priset](https://azure.microsoft.com/pricing/details/logic-apps).

Lär dig mer om hur faktureringen fungerar på nivåerna [utlösare](#triggers) och [åtgärder](#actions) . Eller, för information om gränser, se [gränser och konfiguration för Azure Logic Apps](logic-apps-limits-and-config.md).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Fast pris modell

En [ *integrerings tjänst miljö* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) tillhandahåller ett isolerat sätt för dig att skapa och köra Logi Kap par som har åtkomst till resurser i ett virtuellt Azure-nätverk. Logi Kap par som körs i en ISE ådrar sig inga kostnader för datakvarhållning. När du skapar en ISE och bara när du skapar kan du välja en [ISE-nivå eller "SKU"](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), som har olika [pris nivåer](https://azure.microsoft.com/pricing/details/logic-apps):

* **Premium** ISE: den här SKU: s bas enhet har fast kapacitet, men om du behöver mer data flöde kan du [lägga till fler skalnings enheter](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) under skapande av ISE eller senare. För ISE-gränser, se [gränser och konfiguration för Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise).

* **Utvecklare** ISE: den här SKU: n har ingen möjlighet att skala upp, inget service nivå avtal (SLA) och inga publicerade gränser. Använd endast den här SKU: n för experimentering, utveckling och testning, inte produktion eller prestanda testning.

För logi Kap par som du skapar och kör i en ISE betalar du ett [fast pris](https://azure.microsoft.com/pricing/details/logic-apps) (jämfört med betalning per användning) för dessa funktioner:

* [Inbyggda](../connectors/apis-list.md#built-in) utlösare och åtgärder

  I en ISE visar inbyggda utlösare och åtgärder **kärn** etiketten och körs i samma ISE som dina Logic Apps.

* [Standard](../connectors/apis-list.md#managed-connectors) anslutningar och [företags](../connectors/apis-list.md#enterprise-connectors) anslutningar som gör att du kan ha så många företags anslutningar som du vill

   Standard-och företags anslutningar som visar **ISE** -etiketten körs i samma ISE som dina Logic Apps. Kopplingar som inte visar ISE-etiketten körs i den offentliga, globala, Logic Apps tjänsten för flera innehavare. Den fasta prissättningen gäller även för anslutningar som körs i tjänsten för flera innehavare när du använder dem med Logic Apps som körs i en ISE.

* [Integrerings kontots](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) användning utan extra kostnad, baserat på din [ISE-SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level):

  * **Premium** ISE SKU: ett enda integrations konto på [Standard nivå](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  * **Utvecklare** ISE SKU: ett enda integrations konto på den [kostnads fria nivån](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  För ytterligare kostnader kan du skapa fler integrations konton för din ISE [upp till den totala gränsen](logic-apps-limits-and-config.md#integration-account-limits). 

  * **Premium** ISE SKU: upp till 19 fler standard konton. Inga kostnads fria eller grundläggande konton är tillåtna.

  * **Utvecklare** ISE SKU: upp till 19 fler standard konton om du redan har ett kostnads fritt konto eller 20 totala standard konton om du inte har ett kostnads fritt konto. Inga grundläggande konton är tillåtna.

  Mer information om gränser för integrations konton finns i [gränser och konfiguration för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Du kan lära dig mer om [integrations konto nivåer och deras pris modell](#integration-accounts) senare i det här avsnittet.

<a name="connectors"></a>

## <a name="connectors"></a>Anslutningar

Azure Logic Apps kopplingar hjälper dig att komma åt appar, tjänster och system i molnet i molnet eller lokalt genom att tillhandahålla [utlösare](#triggers), [åtgärder](#actions)eller både och. Anslutningarna klassificeras som antingen standard eller Enterprise. En översikt över dessa anslutningar finns i [kopplingar för Azure Logic Apps](../connectors/apis-list.md). Om inga färdiga kopplingar är tillgängliga för de REST-API: er som du vill använda i dina Logi Kap par, kan du skapa [anpassade anslutningar](/connectors/custom-connectors), som bara omsluter runt dessa REST-API: er. Anpassade anslutningar faktureras som standard anslutningar. I följande avsnitt finns mer information om hur faktureringen för utlösare och åtgärder fungerar.

<a name="triggers"></a>

## <a name="triggers"></a>Utlösare

En utlösare är alltid det första steget i ett Logic app-arbetsflöde och är en särskild åtgärd som skapar och kör en Logic App-instans när särskilda villkor uppfylls eller en viss händelse inträffar. Utlösare fungerar på olika sätt, vilket påverkar hur Logic app mäts. Här är de olika typerna av utlösare som finns i Azure Logic Apps:

* **Upprepnings utlösare**: du kan använda den här generiska utlösaren, som inte är speciell för någon tjänst eller ett system, för att starta ett Logic app-arbetsflöde och skapa en Logic App-instans som körs baserat på upprepnings intervallet som du har skapat i utlösaren. Du kan till exempel ställa in en upprepnings utlösare som körs var tredje dag eller ett mer komplext schema.

* **Avsöknings utlösare**: du kan använda den här mer specialiserade återkommande utlösaren, som vanligt vis är kopplad till den hanterade anslutningen för en specifik tjänst eller ett system, för att söka efter händelser eller meddelanden som uppfyller kriterierna för att skapa och köra Logi Kap par-instansen baserat på upprepnings intervallet som du har konfigurerat i utlösaren. Även om ingen Logic App-instans skapas, till exempel när utlösare hoppas över, Logic Apps service mätarna för varje avsöknings förfrågan som en körning. Ange avsöknings intervallet genom att ställa in utlösaren via Logic App Designer.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook-utlösare**: i stället för att använda en avsöknings utlösare kan du använda en webhook-utlösare för att vänta tills klienten skickar en begäran till din Logic app på en viss slut punkts-URL. Varje begäran som skickas till webhook-slutpunkten räknas som en åtgärds körning. Till exempel är begäran och HTTP webhook-utlösaren båda allmänna webhook-utlösare. Vissa anslutningar för tjänster eller system har också webhook-utlösare.

<a name="actions"></a>

## <a name="actions"></a>Åtgärder

Azure Logic Apps mätare "inbyggda" åtgärder, till exempel HTTP, som inbyggda åtgärder. Inbyggda åtgärder omfattar till exempel HTTP-anrop, samtal från Azure Functions eller API Management och kontroll av flödes steg som villkor, slingor och växel uttryck. Varje åtgärd har sin egen åtgärds typ. Till exempel har åtgärder som anropar [kopplingar](/connectors) typen "ApiConnection". Dessa anslutningar klassificeras som standard-eller företags anslutningar, som mäts utifrån deras respektive [priser](https://azure.microsoft.com/pricing/details/logic-apps). Företags anslutningar i för *hands versionen* debiteras som standard anslutningar.

Azure Logic Apps mäta alla lyckade och misslyckade åtgärder som körningar. Logic Apps mäter dock inte följande åtgärder:

* Åtgärder som hoppas över på grund av ouppfyllda-villkor
* Åtgärder som inte körs eftersom Logic app stoppades innan den slutfördes

För åtgärder som körs inuti slingor räknar Azure Logic Apps varje åtgärd för varje cykel i slingan. Anta till exempel att du har en "for each"-loop som bearbetar en lista. Logic Apps mätare en åtgärd i den slingen genom att multiplicera antalet List objekt med antalet åtgärder i slingan och lägga till den åtgärd som startar slingan. Det innebär att beräkningen för en lista med 10 objekt är (10 * 1) + 1, vilket resulterar i 11 åtgärds körningar.

## <a name="disabled-logic-apps"></a>Inaktiverade Logic Apps

Inaktiverade Logic Apps debiteras inte eftersom de inte kan skapa nya instanser när de är inaktiverade. När du har inaktiverat en Logic app kan alla instanser som körs för närvarande ta lite tid innan de stoppas helt.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Integrationskonton

En [fast pris modell](https://azure.microsoft.com/pricing/details/logic-apps) gäller för [integrations konton](logic-apps-enterprise-integration-create-integration-account.md) där du kan utforska, utveckla och testa funktionerna [B2B och EDI](logic-apps-enterprise-integration-b2b.md) och [XML-bearbetning](logic-apps-enterprise-integration-xml.md) i Azure Logic Apps utan extra kostnad. Varje Azure-prenumeration kan ha upp till en [angiven gräns för integrations konton](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Varje integrations konto kan lagra upp till en begränsad [begränsning av artefakter](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)som omfattar handels partner, avtal, kartor, scheman, sammansättningar, certifikat, batch-konfigurationer och så vidare.

Azure Logic Apps erbjuder integrations konton kostnads fritt, Basic och standard. Nivån Basic och standard stöds av Logic Apps service avtal (SLA), medan den kostnads fria nivån inte stöds av ett service avtal och har gränser för regions tillgänglighet, data flöde och användning. Förutom för integrations konton på kostnads fri nivå kan du ha fler än ett integrations konto i varje Azure-region. Pris nivåer finns i [Logic Apps prissättning](https://azure.microsoft.com/pricing/details/logic-apps/).

Om du har en [ *integrerings tjänst miljö* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)kan din ISE använda ett enda integrations konto utan extra kostnad, även om den inkluderade konto typen varierar av [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). För [ytterligare kostnader](#fixed-pricing)kan du skapa fler integrations konton för din ISE upp till den [totala gränsen för integrations konton](logic-apps-limits-and-config.md#integration-account-limits). Information om hur den fasta pris modellen fungerar för en ISE finns i avsnittet föregående [fast pris modell](#fixed-pricing) i det här avsnittet. Pris nivåer finns i [Logic Apps prissättning](https://azure.microsoft.com/pricing/details/logic-apps).

Om du vill välja mellan ett kostnads fritt, Basic-eller standard integrations konto granskar du följande användnings Falls beskrivningar:

* **Kostnads fri**: när du vill testa exempel scenarier, inte produktions scenarier. Den här nivån är endast tillgänglig för offentliga regioner i Azure, till exempel västra USA eller Sydostasien, men inte för [Azure Kina 21Vianet](/azure/china/overview-operations) eller [Azure Government](../azure-government/documentation-government-welcome.md).

* **Basic**: när du vill att endast meddelande hantering eller ska fungera som en liten affärs partner som har en handels partner relation med en större affär senhet

* **Standard**: när du har mer komplexa B2B-relationer och ökat antal entiteter som du måste hantera

<a name="data-retention"></a>

## <a name="data-retention"></a>Datakvarhållning

Förutom för logi Kap par som körs i en integrerings tjänst miljö (ISE) debiteras alla indata och utdata som lagras i din Logic Apps körnings historik för att faktureras baserat på en Logi Kap par [körnings period](logic-apps-limits-and-config.md#run-duration-retention-limits). Logi Kap par som körs i en ISE ådrar sig inga kostnader för datakvarhållning. Pris nivåer finns i [Logic Apps prissättning](https://azure.microsoft.com/pricing/details/logic-apps).

För att hjälpa dig att övervaka din Logic Apps lagrings förbrukning kan du:

* Visa antalet lagrings enheter i GB som din Logic app använder varje månad.

* Visa storlekarna för en speciell åtgärds indata och utdata i din Logic Apps körnings historik.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Visa lagrings förbrukning i Logic app

1. Leta upp och öppna din Logic app i Azure Portal.

1. Från din Logic Apps-meny, under **övervakning**, väljer du **mått**.

1. I den högra rutan, under **diagram rubrik**, i listan **mått** väljer du **fakturerings användning för körning av lagrings förbrukning**.

   Med det här måttet får du antalet lagrings förbruknings enheter i GB per månad som debiteras.

   > [!NOTE]
   > Körningar som förbrukar mindre än 500 MB i lagringen kanske inte visas i vyn övervakning, men de faktureras fortfarande.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Visa åtgärds-och utmatnings storlekar

1. Leta upp och öppna din Logic app i Azure Portal.

1. På din Logic Apps-meny väljer du **Översikt**.

1. I den högra rutan under **körnings historik** väljer du den körning som har de indata och utdata som du vill kontrol lera.

1. Under **Logic app-körning** väljer du **Kör information**.

1. Välj den åtgärd som du vill visa i tabellen åtgärder i fönstret för att **köra information i Logic app** .

1. I fönstret **åtgärd för Logic app-åtgärd** hittar du storlekarna för åtgärdens indata och utdata. Leta upp länkarna till dessa indata och utdata under **indata-länken** och **utdata-länken**.

   > [!NOTE]
   > För slingor är det bara de toppnivå åtgärderna som visar storlekarna för indata och utdata. För åtgärder i kapslade slingor visar indata och utdata noll storlek och inga länkar.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Azure Logic Apps](logic-apps-overview.md)
* [Skapa din första logiska app](quickstart-create-first-logic-app-workflow.md)
