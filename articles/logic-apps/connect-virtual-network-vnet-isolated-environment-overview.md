---
title: Åtkomst till virtuella Azure-nätverk
description: Översikt över hur integrerings tjänst miljöer (ISEs) hjälper Logic Apps åtkomst till Azure Virtual Networks (virtuella nätverk)
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: 4d83609eea57c2350881360ef757b1a291627c23
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374736"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Åtkomst till Azure Virtual Network-resurser från Azure Logic Apps med hjälp av integrerings tjänst miljöer (ISEs)

Ibland behöver dina Logi Kap par åtkomst till skyddade resurser, till exempel virtuella datorer och andra system eller tjänster, som finns i eller är anslutna till ett [virtuellt Azure-nätverk](../virtual-network/virtual-networks-overview.md). Om du vill konfigurera den här åtkomsten kan du [skapa en *integrerings tjänst miljö* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). En ISE är en instans av den Logic Apps tjänst som använder dedikerade resurser och körs separat från den "globala" Multi-Tenant Logic Apps-tjänsten. Data i en ISE förblir i [samma region där du skapar och distribuerar denna ISE](https://azure.microsoft.com/global-infrastructure/data-residency/).

Till exempel använder vissa virtuella Azure-nätverk privata slut punkter, som du kan konfigurera via en [privat Azure-länk](../private-link/private-link-overview.md), för att ge åtkomst till Azure PaaS-tjänster, till exempel Azure Storage, Azure Cosmos DB eller Azure SQL Database, partner tjänster eller kund tjänster som finns i Azure. Om dina Logi Kap par behöver åtkomst till virtuella nätverk som använder privata slut punkter, måste du skapa, distribuera och köra dessa Logic Apps i en ISE.

När du skapar en ISE *injicerar* Azure eller distribuerar detta ISE till ditt virtuella Azure-nätverk. Du kan sedan använda denna ISE som plats för de Logic Apps och integrations konton som behöver åtkomst.

![Välj integrerings tjänst miljö](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Den här översikten innehåller mer information om [varför du vill använda en ISE](#benefits), [skillnaderna mellan dedikerad och Logic Apps tjänst för flera innehavare](#difference)och hur du kan få direkt åtkomst till resurser som finns i eller är anslutna till ditt virtuella Azure-nätverk.

<a name="benefits"></a>

## <a name="why-use-an-ise"></a>Varför ska jag använda en ISE?

Genom att köra Logi Kap par i en egen separat dedikerad instans kan du minska den påverkan som andra Azure-klienter kan ha på dina appars prestanda, även kallade ["störningar på grannar"-effekten](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). En ISE tillhandahåller även följande fördelar:

* Direkt åtkomst till resurser som finns i eller är anslutna till ditt virtuella nätverk

  Logic Apps som du skapar och kör i en ISE kan använda [särskilt utformade anslutningar som körs i din ISE](../connectors/apis-list.md#ise-connectors). Om det finns en ISE-anslutning för ett lokalt system eller en data källa kan du ansluta direkt utan att behöva använda den [lokala datagatewayen](../logic-apps/logic-apps-gateway-connection.md). Mer information finns i [dedikerade kontra flera klienter](#difference) och [åtkomst till lokala system](#on-premises) senare i det här avsnittet.

* Fortsatt åtkomst till resurser som är utanför eller inte anslutna till ditt virtuella nätverk

  Logic Apps som du skapar och kör i en ISE kan fortfarande använda anslutningar som körs i Logic Apps tjänsten för flera innehavare när en ISE-Specific Connector inte är tillgänglig. Mer information finns i [dedikerade jämfört med flera klient organisationer](#difference).

* Dina egna statiska IP-adresser, som är åtskilda från de statiska IP-adresser som delas av logi Kap par i tjänsten för flera innehavare. Du kan också konfigurera en enskild offentlig, statisk och förutsägbar utgående IP-adress för att kommunicera med mål systemen. På så sätt behöver du inte konfigurera ytterligare brand Väggs öppningar på dessa mål system för varje ISE.

* Ökade gränser för körnings tid, lagrings kvarhållning, data flöde, timeout för HTTP-begäran och svar, meddelande storlekar och anpassade anslutnings begär Anden. Mer information finns i [gränser och konfiguration för Azure Logic Apps](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="dedicated-versus-multi-tenant"></a>Dedikerat kontra flera innehavare

När du skapar och kör Logi Kap par i en ISE får du samma användar upplevelse och liknande funktioner som Logic Apps tjänsten för flera innehavare. Du kan använda alla inbyggda utlösare, åtgärder och hanterade anslutningar som är tillgängliga i Logic Apps tjänsten för flera innehavare. Vissa hanterade anslutningar erbjuder ytterligare ISE-versioner. Skillnaden mellan ISE-kopplingar och icke-ISE-anslutningar finns i där de körs och de etiketter som de har i Logic Apps designer när du arbetar i en ISE.

![Kopplingar med och utan etiketter i en ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Inbyggda utlösare och åtgärder, till exempel HTTP, visar **kärn** etiketten och körs i samma ISE som din Logic app.

* Hanterade kopplingar som visar **ISE** -etiketten är särskilt utformade för ISEs och *körs alltid i samma ISE som din Logic app*. Här är till exempel några [kopplingar som erbjuder ISE-versioner](../connectors/apis-list.md#ise-connectors):<p>

  * Azure Blob Storage, File Storage och Table Storage
  * Azure Service Bus, Azure-köer, Azure Event Hubs
  * Azure Automation, Azure Key Vault, Azure Event Grid och Azure Monitor loggar
  * FTP, SFTP-SSH, fil system och SMTP
  * SAP, IBM MQ, IBM DB2 och IBM 3270
  * SQL Server, Azure Synapse Analytics Azure Cosmos DB
  * AS2, X12 och EDIFACT

  I sällsynta fall, om en ISE-anslutning är tillgänglig för ett lokalt system eller en data källa, kan du ansluta direkt utan att använda den [lokala datagatewayen](../logic-apps/logic-apps-gateway-connection.md). Mer information finns i [åtkomst till lokala system](#on-premises) senare i det här avsnittet.

* Hanterade kopplingar som inte visar **ISE** -etiketten fungerar fortfarande för logi Kap par i en ISE. Dessa anslutningar *körs alltid i Logic Apps-tjänsten för flera innehavare*, inte i ISE.

* Anpassade anslutningar som du skapar *utanför en ISE*, oavsett om de kräver den [lokala datagatewayen](../logic-apps/logic-apps-gateway-connection.md), fortsätter att fungera för logi Kap par i en ISE. Anpassade anslutningar som du skapar *i en ISE* fungerar dock inte med den lokala datagatewayen. Mer information finns i [åtkomst till lokala system](#on-premises).

<a name="on-premises"></a>

## <a name="access-to-on-premises-systems"></a>Åtkomst till lokala system

Logic Apps som körs i en ISE kan direkt komma åt lokala system och data källor som finns i eller anslutna till ett virtuellt Azure-nätverk med hjälp av följande objekt:<p>

* HTTP-utlösaren eller åtgärden som visar **Core** -etiketten

* **ISE** -anslutningen, om den är tillgänglig, för ett lokalt system eller data Källa

  Om det finns en ISE-anslutning kan du direkt komma åt systemet eller data källan utan den [lokala datagatewayen](../logic-apps/logic-apps-gateway-connection.md). Men om du behöver komma åt SQL Server från en ISE och använda Windows-autentisering måste du använda anslutningens icke-ISE-version och den lokala datagatewayen. Anslutningens ISE-version stöder inte Windows-autentisering. Mer information finns i [ISE-anslutningar](../connectors/apis-list.md#ise-connectors) och [ansluta från en integrerings tjänst miljö](../connectors/apis-list.md#integration-service-environment).

* En anpassad anslutningsapp

  * Anpassade anslutningar som du skapar *utanför en ISE*, oavsett om de kräver den [lokala datagatewayen](../logic-apps/logic-apps-gateway-connection.md), fortsätter att fungera för logi Kap par i en ISE.

  * Anpassade anslutningar som du skapar *i en ISE* fungerar inte med den lokala datagatewayen. Dessa anslutningar kan dock direkt komma åt lokala system och data källor som finns i eller anslutna till det virtuella nätverk som är värd för din ISE. Därför behöver Logic Apps som ingår i en ISE vanligt vis inte datagatewayen vid åtkomst till dessa resurser.

För att komma åt lokala system och data källor som inte har ISE-kopplingar, är utanför det virtuella nätverket eller inte är anslutna till ditt virtuella nätverk, måste du fortfarande använda den lokala datagatewayen. Logi Kap par i en ISE kan fortsätta använda kopplingar som saknar etiketten **Core** eller **ISE** . Dessa anslutningar körs i Logic Apps tjänsten för flera innehavare i stället för i din ISE. 

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE SKU: er

När du skapar din ISE kan du välja Developer SKU eller Premium SKU. Detta SKU-alternativ är endast tillgängligt vid skapande av ISE och kan inte ändras senare. Här följer skillnaderna mellan dessa SKU: er:

* **Utvecklare**

  Tillhandahåller en miljö med lägre kostnad som du kan använda för utforskning, experiment, utveckling och testning, men inte för produktion eller prestanda testning. Developer SKU: n innehåller inbyggda utlösare och åtgärder, standard anslutningar, företags anslutningar och ett enda [kostnads fritt nivå](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integrations konto för ett [fast månads pris](https://azure.microsoft.com/pricing/details/logic-apps). 

  > [!IMPORTANT]
  > Denna SKU har inget service avtal (SLA), skalnings kapacitet eller redundans under återvinning, vilket innebär att du kan uppleva fördröjningar eller drift stopp. Server dels uppdateringar kan tillfälligt avbryta tjänsten.

  Information om kapacitet och begränsningar finns [i avsnittet ISE-gränser i Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). Information om hur faktureringen fungerar för ISEs finns i [prissättnings modellen för Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing).

* **Premium**

  Tillhandahåller en ISE som du kan använda för produktions-och prestanda testning. Premium-SKU: n innehåller SLA-support, inbyggda utlösare och åtgärder, standard anslutningar, företags anslutningar, ett enda integrations konto på [Standard nivå](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) , skalnings möjligheter och redundans vid återvinning för ett [fast månads pris](https://azure.microsoft.com/pricing/details/logic-apps).

  Information om kapacitet och begränsningar finns [i avsnittet ISE-gränser i Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). Information om hur faktureringen fungerar för ISEs finns i [prissättnings modellen för Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE slut punkts åtkomst

När du skapar din ISE kan du välja att använda antingen interna eller externa slut punkter för åtkomst. Ditt val bestämmer om begäran eller webhook-utlösare på Logic Apps i din ISE kan ta emot samtal utanför det virtuella nätverket. Dessa slut punkter påverkar också hur du kan komma åt indata och utdata från dina Logic Apps körnings historik.

> [!IMPORTANT]
> Du kan bara välja åtkomst slut punkten under skapande av ISE och inte ändra det här alternativet senare.

* **Internt**: privata slut punkter tillåter anrop till logi Kap par i din ISE där du kan visa och komma åt indata och utdata från Logic Apps körnings historiken endast inifrån *det virtuella nätverket*.

  > [!IMPORTANT]
  > Om du behöver använda dessa webhook-baserade utlösare använder du externa slut punkter, *inte* interna slut punkter, när du skapar din ISE:
  > 
  > * Azure DevOps
  > * Azure Event Grid
  > * Common Data Service
  > * Office 365
  > * SAP (ISE-version)
  > 
  > Kontrol lera också att du har nätverks anslutning mellan privata slut punkter och den dator där du vill komma åt körnings historiken. Annars visas ett fel meddelande som säger "oväntat fel" när du försöker visa körnings historiken för din Logic Apps. Det gick inte att hämta ".
  >
  > ![Azure Storage åtgärds fel som orsakas av oförmåga att skicka trafik genom brand väggen](./media/connect-virtual-network-vnet-isolated-environment-overview/integration-service-environment-error.png)
  >
  > Klient datorn kan till exempel finnas i ISE: s virtuella nätverk eller i ett virtuellt nätverk som är anslutet till ISE: s virtuella nätverk via peering eller ett virtuellt privat nätverk. 

* **Externt**: offentliga slut punkter tillåter anrop till logi Kap par i din ISE där du kan visa och komma åt indata och utdata från Logic Apps kör historik *från utanför det virtuella nätverket*. Om du använder nätverks säkerhets grupper (NSG: er) kontrollerar du att de har kon figurer ATS med regler för inkommande trafik för att tillåta åtkomst till körnings historikens indata och utdata. Mer information finns i [Aktivera åtkomst för ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

Du kan ta reda på om din ISE använder en intern eller extern åtkomst slut punkt genom att välja **Egenskaper** under **Inställningar** på din ISE-meny, och sedan hitta **åtkomst slut punkts** egenskapen:

![Hitta åtkomst punkten för ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Prismodell

Logi Kap par, inbyggda utlösare, inbyggda åtgärder och anslutningar som körs i din ISE använder en fast pris plan som skiljer sig från den förbruknings pris planen. Mer information finns i [Logic Apps pris modell](../logic-apps/logic-apps-pricing.md#fixed-pricing). Pris nivåer finns i [Logic Apps prissättning](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integrations konton med ISE

Du kan använda integrations konton med Logic Apps i en integrerings tjänst miljö (ISE). Dessa integrations konton måste dock använda *samma ISE* som de länkade Logic Apps. Logi Kap par i en ISE kan endast referera till de integrerings konton som finns i samma ISE. När du skapar ett integrations konto kan du välja din ISE som plats för ditt integrations konto. Information om hur priser och fakturering fungerar för integrations konton med en ISE finns i [Logic Apps prissättnings modell](../logic-apps/logic-apps-pricing.md#fixed-pricing). Pris nivåer finns i [Logic Apps prissättning](https://azure.microsoft.com/pricing/details/logic-apps/). Information om begränsningar finns i [gränser för integrations konton](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="next-steps"></a>Nästa steg

* [Ansluta till virtuella Azure-nätverk från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Läs mer om [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Lär dig mer om [Virtual Network-integrering för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md)
