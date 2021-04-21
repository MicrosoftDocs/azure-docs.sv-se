---
title: Åtkomst till virtuella Azure-nätverk
description: Översikt över hur integrationstjänstmiljöer (ISE) hjälper logikappar att komma åt virtuella Azure-nätverk (VNET)
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: 3070083040424b877159955dc2138f15319f05c8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766397"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Åtkomst till Azure Virtual Network resurser från Azure Logic Apps med hjälp av integrationstjänstmiljöer (ISE)

Ibland behöver dina logikappar åtkomst till skyddade resurser, till exempel virtuella datorer (VM) och andra system eller tjänster som finns i eller är anslutna till ett [virtuellt Azure-nätverk.](../virtual-network/virtual-networks-overview.md) Om du vill konfigurera den här åtkomsten kan [du skapa en Integration Service *Environment* (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) En ISE är en instans av Logic Apps-tjänsten som använder dedikerade resurser och körs separat från den "globala" Logic Apps klientorganisationstjänsten. Data i en ISE finns i samma [region där du skapar och distribuerar ISE:en.](https://azure.microsoft.com/global-infrastructure/data-residency/)

Vissa virtuella Azure-nätverk använder till exempel privata slutpunkter, som du kan konfigurera via [Azure Private Link](../private-link/private-link-overview.md), för att ge åtkomst till Azure PaaS-tjänster, till exempel Azure Storage, Azure Cosmos DB eller Azure SQL Database, partnertjänster eller kundtjänster som finns i Azure. Om dina logikappar behöver åtkomst till virtuella nätverk som använder privata slutpunkter måste du skapa, distribuera och köra dessa logikappar i en ISE.

När du skapar en ISE matar Azure *in eller* distribuerar denna ISE i ditt virtuella Azure-nätverk. Du kan sedan använda denna ISE som plats för de logikappar och integrationskonton som behöver åtkomst.

![Välj integrationstjänstmiljö](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Den här översikten innehåller mer information om varför du vill använda [en ISE,](#benefits)skillnaderna mellan den dedikerade [tjänsten och Logic Apps-tjänsten](#difference)för flera klienter samt hur du direkt kan komma åt resurser som finns i eller är anslutna till ditt virtuella Azure-nätverk.

<a name="benefits"></a>

## <a name="why-use-an-ise"></a>Varför ska jag använda en ISE?

Genom att köra logikappar i din egen separata dedikerade instans kan du minska den påverkan som andra Azure-klienter kan ha på dina appars prestanda, även kallat ["störande grannar"..](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors) En ISE ger också följande fördelar:

* Direktåtkomst till resurser som är inuti eller anslutna till ditt virtuella nätverk

  Logikappar som du skapar och kör i en ISE kan använda [särskilt utformade anslutningsappar som körs i din ISE.](../connectors/managed.md#ise-connectors) Om det finns en ISE-anslutning för ett lokalt system eller en datakälla kan du ansluta direkt utan att behöva använda [den lokala datagatewayen](../logic-apps/logic-apps-gateway-connection.md). Mer information finns i [Dedikerad jämfört med flera klienter och](#difference) Åtkomst till lokala system senare [i](#on-premises) det här avsnittet.

* Fortsatt åtkomst till resurser som är utanför eller inte är anslutna till ditt virtuella nätverk

  Logikappar som du skapar och kör i en ISE kan fortfarande använda anslutningsappar som körs i Logic Apps-tjänsten för flera innehavare när en ISE-specifik anslutning inte är tillgänglig. Mer information finns i [Dedikerad jämfört med flera klient.](#difference)

* Dina egna statiska IP-adresser, som är separata från de statiska IP-adresser som delas av logikapparna i tjänsten för flera innehavare. Du kan också konfigurera en enskild offentlig, statisk och förutsägbar utgående IP-adress för att kommunicera med målsystemen. På så sätt behöver du inte konfigurera ytterligare brandväggsöppningar på dessa målsystem för varje ISE.

* Ökade gränser för körningens varaktighet, kvarhållning av lagring, dataflöde, HTTP-begäranden och tidsgränser för svar, meddelandestorlekar och anpassade anslutningsbegäranden. Mer information finns i [Gränser och konfiguration för Azure Logic Apps](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="dedicated-versus-multi-tenant"></a>Dedikerad jämfört med flera klient

När du skapar och kör logikappar i en ISE får du samma användarupplevelser och liknande funktioner som för Logic Apps klientorganisation. Du kan använda samma inbyggda utlösare, åtgärder och hanterade anslutningsappar som är tillgängliga i tjänsten för Logic Apps klientorganisation. Vissa hanterade anslutningsappar erbjuder ytterligare ISE-versioner. Skillnaden mellan ISE-anslutningsappar och icke-ISE-anslutningsappar finns där de körs och de etiketter som de har i Logic App Designer när du arbetar i en ISE.

![Anslutningsappar med och utan etiketter i en ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Inbyggda utlösare och åtgärder, till exempel HTTP, visar **CORE-etiketten** och körs i samma ISE som logikappen.

* Hanterade anslutningsappar som visar **ISE-etiketten** är särskilt utformade för ISE:er och körs alltid i samma *ISE som din logikapp.* Här är några anslutningsappar [som erbjuder ISE-versioner:](../connectors/managed.md#ise-connectors)<p>

  * Azure Blob Storage, File Storage och Table Storage
  * Azure Service Bus, Azure Queues, Azure Event Hubs
  * Azure Automation, Azure Key Vault, Azure Event Grid och Azure Monitor Loggar
  * FTP, SFTP-SSH, filsystem och SMTP
  * SAP, IBM MQ, IBM DB2 och IBM 3270
  * SQL Server, Azure Synapse Analytics, Azure Cosmos DB
  * AS2, X12 och EDIFACT

  Om en ISE-anslutning är tillgänglig för ett lokalt system eller en datakälla med sällsynta undantag kan du ansluta direkt utan att använda den [lokala datagatewayen](../logic-apps/logic-apps-gateway-connection.md). Mer information finns i [Åtkomst till lokala system senare i](#on-premises) det här avsnittet.

* Hanterade anslutningsappar som inte visar **ISE-etiketten** fortsätter att fungera för logikappar i en ISE. Dessa anslutningsappar *körs alltid i tjänsten Logic Apps flera innehavare,* inte i ISE.

* Anpassade anslutningsappar som du skapar utanför en *ISE,* oavsett om de kräver den lokala [datagatewayen](../logic-apps/logic-apps-gateway-connection.md)eller inte, fortsätter att fungera för logikappar i en ISE. Anpassade anslutningsappar som du skapar *i en ISE* fungerar dock inte med den lokala datagatewayen. Mer information finns i [Åtkomst till lokala system.](#on-premises)

<a name="on-premises"></a>

## <a name="access-to-on-premises-systems"></a>Åtkomst till lokala system

Logikappar som körs i en ISE kan få direkt åtkomst till lokala system och datakällor som finns i eller är anslutna till ett virtuellt Azure-nätverk med hjälp av följande objekt:<p>

* HTTP-utlösaren eller åtgärden, som visar **CORE-etiketten**

* **ISE-anslutning,** om tillgängligt, för ett lokalt system eller en datakälla

  Om en ISE-anslutning är tillgänglig kan du komma åt systemet eller datakällan direkt utan [den lokala datagatewayen](../logic-apps/logic-apps-gateway-connection.md). Men om du behöver komma SQL Server från en ISE och använda Windows-autentisering måste du använda anslutningsappens icke-ISE-version och den lokala datagatewayen. Anslutningsappens ISE-version stöder inte Windows-autentisering. Mer information finns i [ISE-anslutningsappar](../connectors/managed.md#ise-connectors) och [Ansluta från en integreringstjänstmiljö.](../connectors/managed.md#integration-account-connectors)

* En anpassad anslutningsapp

  * Anpassade anslutningsappar som du skapar utanför en *ISE,* oavsett om de kräver den lokala [datagatewayen](../logic-apps/logic-apps-gateway-connection.md)eller inte, fortsätter att fungera för logikappar i en ISE.

  * Anpassade anslutningsappar som du *skapar i en ISE* fungerar inte med den lokala datagatewayen. Dessa anslutningsappar kan dock direkt komma åt lokala system och datakällor som finns i eller är anslutna till det virtuella nätverk som är värd för din ISE. Logikappar som finns i en ISE behöver därför vanligtvis inte datagatewayen vid åtkomst till dessa resurser.

För att få åtkomst till lokala system och datakällor som inte har ISE-anslutningsappar, som är utanför ditt virtuella nätverk eller som inte är anslutna till ditt virtuella nätverk, måste du fortfarande använda den lokala datagatewayen. Logikappar i en ISE kan fortsätta att använda anslutningsappar som inte har **CORE- eller** **ISE-etiketten.** Dessa anslutningsappar körs i en tjänst för Logic Apps klientorganisation i stället för i din ISE. 

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE-SKU:er

När du skapar din ISE kan du välja Developer SKU eller Premium SKU. Det här SKU-alternativet är bara tillgängligt när ISE skapas och kan inte ändras senare. Här är skillnaderna mellan dessa SKU:er:

* **Utvecklare**

  Ger en lägre kostnad för ISE som du kan använda för utforskning, experiment, utveckling och testning, men inte för produktions- eller prestandatestning. Developer-SKU:n innehåller inbyggda utlösare och åtgärder, Standard-anslutningsappar, Enterprise-anslutningsappar och ett enda konto för integrering på kostnadsfri [nivå](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) för [ett fast månadspris.](https://azure.microsoft.com/pricing/details/logic-apps) 

  > [!IMPORTANT]
  > Den här SKU:n har inget serviceavtal (SLA), uppskalningskapacitet eller redundans under återanvändning, vilket innebär att du kan uppleva fördröjningar eller driftstopp. Backend-uppdateringar kan tillfälligt avbryta tjänsten.

  Information om kapacitet och gränser finns i [ISE-gränser i Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). Information om hur faktureringen fungerar för ISE:er finns Logic Apps [prismodellen](../logic-apps/logic-apps-pricing.md#fixed-pricing).

* **Premium**

  Tillhandahåller en ISE som du kan använda för produktions- och prestandatestning. Premium-SKU:n innehåller SLA-stöd, inbyggda utlösare och åtgärder, Standard-anslutningsprogram, Enterprise-anslutningsprogram, ett enda standardnivåintegreringskonto, uppskalningskapacitet och redundans under återanvändning till ett fast [månadspris.](https://azure.microsoft.com/pricing/details/logic-apps) [](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  Information om kapacitet och gränser finns i [ISE-gränser i Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). Information om hur faktureringen fungerar för ISE:er finns i [Logic Apps prismodellen](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE-slutpunktsåtkomst

När du skapar din ISE kan du välja att använda antingen interna eller externa åtkomstslutpunkter. Ditt val avgör om begäran eller webhook-utlösare för logikappar i ISE kan ta emot anrop utanför ditt virtuella nätverk. Dessa slutpunkter påverkar också hur du kan komma åt indata och utdata från logikappens körningshistorik.

> [!IMPORTANT]
> Du kan bara välja åtkomstslutpunkten när ISE skapas och kan inte ändra det här alternativet senare.

* **Internt:** Privata slutpunkter tillåter anrop till logikappar i din ISE där du kan visa och komma åt indata och utdata från Logikappars körningshistorik endast inifrån *det virtuella nätverket*.

  > [!IMPORTANT]
  > Om du behöver använda dessa webhook-baserade utlösare  använder du externa slutpunkter, inte interna slutpunkter, när du skapar din ISE:
  > 
  > * Azure DevOps
  > * Azure Event Grid
  > * Common Data Service
  > * Office 365
  > * SAP (ISE-version)
  > 
  > Kontrollera också att du har nätverksanslutning mellan de privata slutpunkterna och den dator som du vill komma åt körningshistoriken från. Om du försöker visa logikappens körningshistorik visas annars ett felmeddelande som säger "Oväntat fel. Det gick inte att hämta".
  >
  > ![Azure Storage åtgärdsfel som beror på att det inte går att skicka trafik via brandväggen](./media/connect-virtual-network-vnet-isolated-environment-overview/integration-service-environment-error.png)
  >
  > Klientdatorn kan till exempel finnas i ISE:s virtuella nätverk eller i ett virtuellt nätverk som är anslutet till ISE:s virtuella nätverk via peering eller ett virtuellt privat nätverk. 

* **Extern:** Offentliga slutpunkter tillåter anrop till logikappar i din ISE där du kan visa och komma åt indata och utdata från logikappars körningshistorik utanför det *virtuella nätverket*. Om du använder nätverkssäkerhetsgrupper (NSG:er) kontrollerar du att de har ställts in med regler för inkommande trafik för att tillåta åtkomst till körningshistorikens indata och utdata. Mer information finns i [Aktivera åtkomst för ISE.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)

Om du vill ta reda på om ISE använder en intern eller extern åtkomstslutpunkt går du till ISE-menyn **under** Inställningar, väljer Egenskaper och hittar egenskapen **Åtkomstslutpunkt:**

![Hitta ISE-åtkomstslutpunkt](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Prismodell

Logikappar, inbyggda utlösare, inbyggda åtgärder och anslutningsappar som körs i din ISE använder en fast prisplan som skiljer sig från den förbrukningsbaserade prisplanen. Mer information finns i [Logic Apps prismodell](../logic-apps/logic-apps-pricing.md#fixed-pricing). Prisinformation finns i [Logic Apps priser.](https://azure.microsoft.com/pricing/details/logic-apps/)

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integrationskonton med ISE

Du kan använda integrationskonton med logikappar i en Integration Service Environment (ISE). Dessa integrationskonton måste dock använda samma *ISE som* de länkade logikapparna. Logikappar i en ISE kan endast referera till de integrationskonton som finns i samma ISE. När du skapar ett integrationskonto kan du välja din ISE som plats för ditt integrationskonto. Information om hur priser och fakturering fungerar för integrationskonton med en ISE finns [Logic Apps prismodellen](../logic-apps/logic-apps-pricing.md#fixed-pricing). Prisinformation finns i [Logic Apps priser.](https://azure.microsoft.com/pricing/details/logic-apps/) Information om begränsningar finns i Begränsningar [för integrationskonto.](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

## <a name="next-steps"></a>Nästa steg

* [Ansluta till virtuella Azure-nätverk från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Läs mer om [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Läs mer om [integrering av virtuella nätverk för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md)
