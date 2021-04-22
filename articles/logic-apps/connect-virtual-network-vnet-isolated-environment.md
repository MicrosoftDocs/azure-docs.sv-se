---
title: Ansluta till virtuella Azure-nätverk med en ISE
description: Skapa en integrationstjänstmiljö (ISE) som kan komma åt virtuella Azure-nätverk (VNET) från Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: bfef9f2b5420ac9377cc369d7bf9a9bdac76743b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874233"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Ansluta till virtuella Azure-nätverk från Azure Logic Apps med hjälp av Integration Service Environment (ISE)

För scenarier där dina logikappar och integrationskonton behöver åtkomst till ett virtuellt [Azure-nätverk](../virtual-network/virtual-networks-overview.md)skapar du en [ *integrationstjänstmiljö* (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) En ISE är en dedikerad miljö som använder dedikerad lagring och andra resurser som hålls åtskilda från den ”globala” Logic Apps-tjänsten med flera klienter. Den här uppdelningen minskar också eventuell påverkan som andra Azure-klienter kan ha på dina appars prestanda. Med en ISE får du även egna statiska IP-adresser. Dessa IP-adresser är separata från de statiska IP-adresser som delas av logikapparna i den offentliga tjänsten för flera innehavare.

När du skapar en ISE matar *Azure* in ISE i ditt virtuella Azure-nätverk, som sedan distribuerar Logic Apps-tjänsten till ditt virtuella nätverk. När du skapar en logikapp eller ett integrationskonto väljer du din ISE som plats. Logikappen eller integrationskontot kan sedan direkt komma åt resurser, till exempel virtuella datorer , servrar, system och tjänster i ditt virtuella nätverk.

![Välj integrationstjänstmiljö](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> För att logikappar och integrationskonton ska fungera tillsammans i en ISE måste båda använda *samma ISE* som deras plats.

En ISE har ökade gränser för körningens varaktighet, kvarhållning av lagring, dataflöde, HTTP-begäranden och tidsgränser för svar, meddelandestorlekar och begäranden om anpassade anslutningsappar. Mer information finns i [Gränser och konfiguration för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md). Mer information om ISE:er finns i [Åtkomst till Azure Virtual Network-resurser från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

Den här artikeln visar hur du utför dessa uppgifter med hjälp av Azure Portal:

* Aktivera åtkomst för din ISE.
* Skapa din ISE.
* Lägg till extra kapacitet i ise-enheten.

Du kan också skapa en ISE med hjälp [av Azure Resource Manager snabbstartsmallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) eller med hjälp av Logic Apps REST API, inklusive att konfigurera kund hanterade nycklar:

* [Skapa en Integration Service Environment (ISE) med hjälp av Logic Apps REST-API](../logic-apps/create-integration-service-environment-rest-api.md)
* [Konfigurera kund hanterade nycklar för att kryptera vilodata för ISE:er](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > Logikappar, inbyggda utlösare, inbyggda åtgärder och anslutningsappar som körs i din ISE använder en annan prisplan än den förbrukningsbaserade prisplanen. Information om hur priser och fakturering fungerar för ISE:er finns Logic Apps [prismodellen](../logic-apps/logic-apps-pricing.md#fixed-pricing). Prisinformation finns i [Logic Apps priser.](../logic-apps/logic-apps-pricing.md)

* Ett [virtuellt Azure-nätverk](../virtual-network/virtual-networks-overview.md) som har fyra tomma undernät, som krävs för att skapa och distribuera resurser i ISE och som används av dessa interna och dolda komponenter: 

  * Logic Apps Compute
  * Intern App Service-miljön (anslutningsappar)
  * Intern API Management (anslutningsappar)
  * Intern Redis för cachelagring och prestanda
  
  Du kan skapa undernäten i förväg eller när du skapar din ISE så att du kan skapa undernäten på samma gång. Innan du skapar undernäten måste du dock se till att granska [undernätskraven.](#create-subnet)

  * Se till att det virtuella [nätverket aktiverar åtkomst för din ISE](#enable-access) så att ISE kan fungera korrekt och hålla sig tillgänglig.

  * Om du använder eller vill använda [ExpressRoute](../expressroute/expressroute-introduction.md) tillsammans [](../virtual-network/manage-route-table.md) med tvingad [tunneling](../firewall/forced-tunneling.md)måste du skapa en vägtabell med följande specifika väg och länka vägtabellen till varje undernät som används av din ISE:

    **Namn:**<*vägnamn*><br>
    **Adressprefix:** 0.0.0.0/0<br>
    **Nästa hopp:** Internet
    
    Den här specifika vägtabellen krävs så Logic Apps kan kommunicera med andra beroende Azure-tjänster, till exempel Azure Storage och Azure SQL DB. Mer information om den här vägen finns i [adressprefixet 0.0.0.0/0.](../virtual-network/virtual-networks-udr-overview.md#default-route) Om du inte använder tvingad tunnel med ExpressRoute behöver du inte den här specifika vägtabellen.
    
    Med ExpressRoute kan du utöka dina lokala nätverk till Microsoft-molnet och ansluta till Microsofts molntjänster via en privat anslutning som underlättas av anslutningsleverantören. Mer specifikt är ExpressRoute ett virtuellt privat nätverk som dirigerar trafik via ett privat nätverk i stället för via det offentliga Internet. Dina logikappar kan ansluta till lokala resurser som finns i samma virtuella nätverk när de ansluter via ExpressRoute eller ett virtuellt privat nätverk.
   
  * Om du använder en [virtuell nätverksinstallation (NVA)](../virtual-network/virtual-networks-udr-overview.md#user-defined)kontrollerar du att du inte aktiverar TLS/SSL-avslutning eller ändrar den utgående TLS/SSL-trafiken. Kontrollera också att du inte aktiverar inspektion för trafik som kommer från ISE-undernätet. Mer information finns i [Trafikdirigering i virtuella nätverk.](../virtual-network/virtual-networks-udr-overview.md)

  * Om du vill använda anpassade DNS-servrar [](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) för ditt virtuella Azure-nätverk måste du konfigurera servrarna genom att följa dessa steg innan du distribuerar ISE till ditt virtuella nätverk. Mer information om hur du hanterar DNS-serverinställningar finns [i Skapa, ändra eller ta bort ett virtuellt nätverk.](../virtual-network/manage-virtual-network.md#change-dns-servers)

    > [!NOTE]
    > Om du ändrar inställningarna för DNS-servern eller DNS-servern måste du starta om ISE så att ISE kan hämta ändringarna. Mer information finns i Starta [om ISE.](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE)

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Aktivera åtkomst för ISE

När du använder en ISE med ett virtuellt Azure-nätverk är ett vanligt installationsproblem att ha en eller flera blockerade portar. De anslutningsappar som du använder för att skapa anslutningar mellan ise- och målsystemen kan också ha sina egna portkrav. Om du till exempel kommunicerar med ett FTP-system med ftp-anslutningen måste porten som du använder i FTP-systemet vara tillgänglig, till exempel port 21 för att skicka kommandon.

För att se till att ISE är tillgänglig och att logikapparna i ise kan kommunicera över varje undernät i det virtuella nätverket öppnar du portarna som beskrivs i den här tabellen för varje [undernät.](#network-ports-for-ise) Om nödvändiga portar inte är tillgängliga fungerar inte ise-enheten som den ska.

* Om du har flera ISE-instanser som behöver åtkomst till andra slutpunkter som [](../virtual-network/virtual-networks-overview.md#filter-network-traffic) har IP-begränsningar distribuerar du en [Azure Firewall](../firewall/overview.md) eller en virtuell nätverksinstallation till ditt virtuella nätverk och dirigerar utgående trafik genom brandväggen eller den virtuella nätverksinstallationen. Du kan sedan [konfigurera en enda, utgående, offentlig,](connect-virtual-network-vnet-set-up-single-ip-address.md) statisk och förutsägbar IP-adress som alla ISE-instanser i ditt virtuella nätverk kan använda för att kommunicera med målsystemen. På så sätt behöver du inte konfigurera extra brandväggsöppningar på dessa målsystem för varje ISE.

   > [!NOTE]
   > Du kan använda den här metoden för en enda ISE när ditt scenario kräver att du begränsar antalet IP-adresser som behöver åtkomst. Överväg om de extra kostnaderna för brandväggen eller den virtuella nätverksinstallationen är meningsfulla för ditt scenario. Läs mer om [Azure Firewall prissättning.](https://azure.microsoft.com/pricing/details/azure-firewall/)

* Om du har skapat ett nytt virtuellt Azure-nätverk och undernät utan begränsningar behöver du inte konfigurera nätverkssäkerhetsgrupper [(NSG:er)](../virtual-network/network-security-groups-overview.md#network-security-groups) i ditt virtuella nätverk för att styra trafiken mellan undernät.

* För ett befintligt virtuellt  nätverk kan du välja att konfigurera nätverkssäkerhetsgrupper [(NSG:er)](../virtual-network/network-security-groups-overview.md#network-security-groups) för [att filtrera nätverkstrafik mellan undernät](../virtual-network/tutorial-filter-network-traffic.md). Om du vill gå den här vägen, eller om du redan [](#network-ports-for-ise) använder NSG:er, måste du öppna portarna som beskrivs i den här tabellen för dessa NSG:er.

  När du konfigurerar [NSG-säkerhetsregler](../virtual-network/network-security-groups-overview.md#security-rules)  måste du använda både **TCP-** och  **UDP-protokollen,** eller så kan du välja Alla i stället så att du inte behöver skapa separata regler för varje protokoll. NSG-säkerhetsregler beskriver de portar som du måste öppna för de IP-adresser som behöver åtkomst till dessa portar. Se till att alla brandväggar, routrar eller andra objekt som finns mellan dessa slutpunkter också håller portarna tillgängliga för dessa IP-adresser.

* Om du ställer in tvingad tunneltrafik genom brandväggen för att omdirigera Internet-bunden trafik granskar du [kraven för tvingad tunneltrafik.](#forced-tunneling)

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Nätverksportar som används av din ISE

I den här tabellen beskrivs de portar som din ISE kräver är tillgängliga och syftet med dessa portar. För att minska komplexiteten när du ställer [](../virtual-network/service-tags-overview.md) in säkerhetsregler använder tabellen tjänsttaggar som representerar grupper av IP-adressprefix för en specifik Azure-tjänst. Om detta anges *refererar intern ISE* *och extern ISE* till den [åtkomstslutpunkt som väljs när ISE skapas.](connect-virtual-network-vnet-isolated-environment.md#create-environment) Mer information finns i [Slutpunktsåtkomst.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)

> [!IMPORTANT]
> Se till att du anger källportar till för alla regler `*` eftersom källportarna är tillfälliga.

#### <a name="inbound-security-rules"></a>Ingående säkerhetsregler

| Syfte | Källtjänsttagg eller IP-adresser | Källportar | Måltjänsttagg eller IP-adresser | Målportar | Kommentarer |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Kommunikation mellan undernät i ett virtuellt nätverk | Adressutrymme för det virtuella nätverket med ISE-undernät | * | Adressutrymme för det virtuella nätverket med ISE-undernät | * | Krävs för att trafik *ska flöda* mellan undernäten i ditt virtuella nätverk. <p><p>**Viktigt!** För att trafik ska flöda mellan *komponenterna* i varje undernät måste du öppna alla portar i varje undernät. |
| Båda: <p>Kommunikation till logikappen <p><p>Kör historik för logikapp| Intern ISE: <br>**VirtualNetwork** <p><p>Extern ISE: **Internet eller** se **anteckningar** | * | **VirtualNetwork** | 443 | I stället för att **använda Internettjänsttaggen** kan du ange källans IP-adress för följande objekt: <p><p>– Den dator eller tjänst som anropar eventuella begärandeutlösare eller webhooks i logikappen <p>– Datorn eller tjänsten som du vill komma åt logikappens körningshistorik från <p><p>**Viktigt!** Om du stänger eller blockerar den här porten förhindrar du anrop till logikappar som har begärandeutlösare eller webhooks. Du hindras också från att komma åt indata och utdata för varje steg i körningshistoriken. Du hindras dock inte från att komma åt körningshistoriken för logikappen.|
| Logic Apps designer – dynamiska egenskaper | **LogicAppsManagement** | * | **VirtualNetwork** | 454 | Begäranden kommer från Logic Apps åtkomstslutpunktens [inkommande IP-adresser](../logic-apps/logic-apps-limits-and-config.md#inbound) för den regionen. <p><p>**Viktigt!** Om du arbetar med Azure Government molnet fungerar **inte tjänsttaggen LogicAppsManagement.** I stället måste du ange Logic Apps [inkommande IP-adresser](../logic-apps/logic-apps-limits-and-config.md#azure-government-inbound) för Azure Government. |
| Hälsokontroll för nätverk | **LogicApps** | * | **VirtualNetwork** | 454 | Begäranden kommer från Logic Apps åtkomstslutpunktens [inkommande IP-adresser och](../logic-apps/logic-apps-limits-and-config.md#inbound) [utgående IP-adresser](../logic-apps/logic-apps-limits-and-config.md#outbound) för den regionen. <p><p>**Viktigt!** Om du arbetar med Azure Government molnet fungerar **inte LogicApps-tjänsttaggen.** I stället måste du ange både Logic Apps [inkommande IP-adresser och](../logic-apps/logic-apps-limits-and-config.md#azure-government-inbound) [utgående IP-adresser](../logic-apps/logic-apps-limits-and-config.md#azure-government-outbound) för Azure Government. |
| Distribution av anslutning | **AzureConnectors** | * | **VirtualNetwork** | 454 | Krävs för att distribuera och uppdatera anslutningsappar. Om du stänger eller blockerar den här porten misslyckas ISE-distributioner och uppdateringar och korrigeringar av anslutningarna förhindras. <p><p>**Viktigt!** Om du arbetar med Azure Government molnet fungerar **inte tjänsttaggen AzureConnectors.** I stället måste du ange utgående [IP-adresser för den hanterade anslutningsappen](../logic-apps/logic-apps-limits-and-config.md#azure-government-outbound) för Azure Government. |
| App Service Management-beroende | **AppServiceManagement** | * | **VirtualNetwork** | 454, 455 ||
| Kommunikation från Azure Traffic Manager | **AzureTrafficManager** | * | **VirtualNetwork** | Intern ISE: 454 <p><p>Extern ISE: 443 ||
| Båda: <p>Distribution av anslutningsprincipen <p>API Management – hanteringsslutpunkt | **APIManagement** | * | **VirtualNetwork** | 3443 | För distribution av anslutningsprincipen krävs portåtkomst för att distribuera och uppdatera anslutningsappar. Om du stänger eller blockerar den här porten misslyckas ISE-distributioner och uppdateringar och korrigeringar av anslutningarna förhindras. |
| Åtkomst Azure Cache for Redis instanser mellan rollinstanser | **VirtualNetwork** | * | **VirtualNetwork** | 6379 –6383, plus se **Anteckningar**| För att ISE ska fungera med Azure Cache for Redis måste du öppna dessa utgående och inkommande portar som beskrivs i [vanliga Azure Cache for Redis vanliga frågor och svar.](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements) |
|||||||

#### <a name="outbound-security-rules"></a>Säkerhetsregler för utgående trafik

| Syfte | Källtjänsttagg eller IP-adresser | Källportar | Måltjänsttagg eller IP-adresser | Målportar | Kommentarer |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Kommunikation mellan undernät i det virtuella nätverket | Adressutrymme för det virtuella nätverket med ISE-undernät | * | Adressutrymme för det virtuella nätverket med ISE-undernät | * | Krävs för att trafik *ska flöda* mellan undernäten i ditt virtuella nätverk. <p><p>**Viktigt!** För att trafik ska flöda mellan *komponenterna* i varje undernät måste du öppna alla portar i varje undernät. |
| Kommunikation från logikappen | **VirtualNetwork** | * | Varierar beroende på mål | Varierar beroende på mål | Målportarna varierar beroende på slutpunkterna för de externa tjänster som logikappen behöver kommunicera med. <p><p>Målporten är till exempel 443 för en webbtjänst, port 25 för en SMTP-tjänst, port 22 för en SFTP-tjänst och så vidare. |
| Azure Active Directory | **VirtualNetwork** | * | **AzureActiveDirectory** | 80, 443 ||
| Azure Storage beroende | **VirtualNetwork** | * | **Storage** | 80, 443, 445 ||
| Anslutningshantering | **VirtualNetwork** | * | **AppService** | 443 ||
| Publicera diagnostikloggar & mått | **VirtualNetwork** | * | **AzureMonitor** | 443 ||
| Azure SQL beroende | **VirtualNetwork** | * | **SQL** | 1433 ||
| Azure Resource Health | **VirtualNetwork** | * | **AzureMonitor** | 1886 | Krävs för att publicera hälsostatus till Resource Health. |
| Beroende från logga till händelsehubbprincip och övervakningsagent | **VirtualNetwork** | * | **EventHub** | 5672 ||
| Åtkomst Azure Cache for Redis instanser mellan rollinstanser | **VirtualNetwork** | * | **VirtualNetwork** | 6379–6383, plus se **Anteckningar**| För att ISE ska fungera med Azure Cache for Redis måste du öppna dessa utgående och inkommande portar som beskrivs i vanliga [Azure Cache for Redis vanliga frågor och svar.](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements) |
| DNS-namnmatchning | **VirtualNetwork** | * | IP-adresser för alla dns Domain Name System servrar (custom Domain Name System) i ditt virtuella nätverk | 53 | Krävs endast när du använder anpassade DNS-servrar i ditt virtuella nätverk |
|||||||

Dessutom måste du lägga till regler för utgående trafik [för App Service-miljön (ASE):](../app-service/environment/intro.md)

* Om du använder Azure Firewall måste du konfigurera brandväggen med taggen App Service-miljön (ASE) fullständigt kvalificerat domännamn [(FQDN),](../firewall/fqdn-tags.md#current-fqdn-tags)som tillåter utgående åtkomst till ASE-plattformstrafik.

* Om du använder en annan brandväggsinstallation än Azure Firewall måste  du konfigurera brandväggen med alla regler som anges i de brandväggsintegreringsberoenden som krävs för App Service-miljön. [](../app-service/environment/firewall-integration.md#dependencies)

<a name="forced-tunneling"></a>

#### <a name="forced-tunneling-requirements"></a>Krav på tvingad tunneling

Om du ställer in eller [använder tvingad tunneling](../firewall/forced-tunneling.md) genom brandväggen måste du tillåta extra externa beroenden för din ISE. Med tvingad tunneltrafik kan du omdirigera Internetbunden trafik till ett anvisat nästa hopp, till exempel ditt virtuella privata nätverk (VPN) eller till en virtuell installation, i stället för till Internet så att du kan inspektera och granska utgående nätverkstrafik.

Om du inte tillåter åtkomst för dessa beroenden misslyckas ISE-distributionen och din distribuerade ISE slutar fungera.

* Användardefinierade vägar

  För att förhindra asymmetrisk routning måste du definiera en väg för varje IP-adress som anges nedan med **Internet** som nästa hopp.
  
  * [App Service-miljön hanteringsadresser](../app-service/environment/management-addresses.md)
  * [Azure IP-adresser för anslutningsappar i ISE-regionen, som finns i den här nedladdningsfilen](https://www.microsoft.com/download/details.aspx?id=56519)
  * [Azure Traffic Manager hanteringsadresser](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json)
  * [Logic Apps inkommande och utgående adresser för ISE-regionen](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses-and-service-tags)
  * [Azure IP-adresser för anslutningsappar i ISE-regionen, som finns i den här nedladdningsfilen](https://www.microsoft.com/download/details.aspx?id=56519)

* Tjänstslutpunkter

  Du måste aktivera tjänstslutpunkter för Azure SQL, Storage, Service Bus, KeyVault och Event Hubs eftersom du inte kan skicka trafik via en brandvägg till dessa tjänster.

*  Andra inkommande och utgående beroenden

   Brandväggen *måste* tillåta följande inkommande och utgående beroenden:
   
   * [Azure App Service beroenden](../app-service/environment/firewall-integration.md#deploying-your-ase-behind-a-firewall)
   * [Azure Cache Service-beroenden](../azure-cache-for-redis/cache-how-to-premium-vnet.md#what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks)
   * [Azure API Management beroenden](../api-management/api-management-using-with-vnet.md#-common-network-configuration-issues)

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Skapa din ISE

1. I den [Azure Portal](https://portal.azure.com)anger du som filter i huvudsökrutan i Azure `integration service environments` och väljer Integration Service **Environments**.

   ![Leta upp och välj "Integration Service Environments" (Integreringstjänstmiljöer)](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. I fönstret **Integreringstjänstmiljöer väljer** du Lägg **till**.

   ![Välj "Lägg till" för att skapa integreringstjänstmiljön](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Ange den här informationen för din miljö och välj **sedan Granska + skapa,** till exempel:

   ![Ange miljöinformation](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Prenumeration** | Yes | <*Azure-subscription-name*> | Azure-prenumerationen som ska användas för din miljö |
   | **Resursgrupp** | Yes | <*Azure-resource-group-name*> | En ny eller befintlig Azure-resursgrupp där du vill skapa din miljö |
   | **Namn på integrationstjänstmiljö** | Yes | <*miljönamn*> | Ditt ISE-namn, som endast får innehålla bokstäver, siffror, bindestreck ( `-` ), understreck ( `_` ) och punkter ( `.` ). |
   | **Plats** | Yes | <*Azure-datacenter-region*> | Azure-datacenterregionen där du vill distribuera din miljö |
   | **SKU** | Yes | **Premium** eller **utvecklare (inget serviceavtal)** | ISE-SKU:n som ska skapas och användas. Skillnader mellan dessa SKU:er finns i [ISE-SKU:er.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) <p><p>**Viktigt!** Det här alternativet är bara tillgängligt när ISE skapas och kan inte ändras senare. |
   | **Ytterligare kapacitet** | Premium: <br>Yes <p><p>Författare: <br>Inte tillämpligt | Premium: <br>0 till 10 <p><p>Författare: <br>Inte tillämpligt | Antalet extra bearbetningsenheter som ska användas för den här ISE-resursen. Information om hur du lägger till kapacitet efter skapandet finns [i Lägga till ISE-kapacitet](../logic-apps/ise-manage-integration-service-environment.md#add-capacity). |
   | **Åtkomstslutpunkt** | Yes | **Intern** eller **extern** | Typ av åtkomstslutpunkter som ska användas för din ISE. Dessa slutpunkter avgör om begäran eller webhook-utlösare för logikappar i din ISE kan ta emot anrop utanför ditt virtuella nätverk. <p><p>Om du till exempel vill använda följande webhook-baserade utlösare kontrollerar du att du väljer **Extern:** <p><p>– Azure DevOps <br>– Azure Event Grid <br>– Common Data Service <br>– Office 365 <br>– SAP (ISE-version) <p><p>Ditt val påverkar också hur du kan visa och komma åt indata och utdata i logikappens körningshistorik. Mer information finns i [ISE-slutpunktsåtkomst.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access) <p><p>**Viktigt!** Du kan bara välja åtkomstslutpunkten när ISE skapas och kan inte ändra det här alternativet senare. |
   | **Virtuellt nätverk** | Yes | <*Azure-virtual-network-name*> | Det virtuella Azure-nätverk där du vill mata in din miljö så att logikappar i den miljön kan komma åt ditt virtuella nätverk. Om du inte har något nätverk skapar du [först ett virtuellt Azure-nätverk.](../virtual-network/quick-create-portal.md) <p><p>**Viktigt!** Du kan *bara* utföra den här injectionn när du skapar din ISE. |
   | **Undernät** | Yes | <*subnet-resource-list*> | En ISE  kräver fyra tomma undernät, som krävs för att skapa och distribuera resurser i din ISE och som används av interna Logic Apps-komponenter, till exempel anslutningsappar och cachelagring för prestanda. <p>**Viktigt!** Se till att [du granskar kraven för undernät innan du fortsätter med de här stegen för att skapa undernäten](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **Skapa undernät**

   Ise kräver fyra tomma *undernät* som behövs för att skapa och distribuera resurser i ISE och som används av interna Logic Apps-komponenter, till exempel anslutningsappar och cachelagring för prestanda. Du *kan inte ändra dessa* undernätsadresser när du har skapat din miljö. Om du skapar och distribuerar DIN ISE via Azure Portal bör du se till att du inte delegerar dessa undernät till några Azure-tjänster. Men om du skapar och distribuerar ISE via REST API, Azure PowerShell eller en Azure Resource Manager mall måste du [delegera](../virtual-network/manage-subnet-delegation.md) ett tomt undernät till `Microsoft.integrationServiceEnvironment` . Mer information finns i Lägga [till en undernätsdelegering.](../virtual-network/manage-subnet-delegation.md)

   Varje undernät måste uppfylla följande krav:

   * Använder ett namn som börjar med antingen ett alfabetiskt tecken eller ett understreck (inga siffror) och inte använder dessa tecken: `<` , , , , , , `>` `%` `&` `\\` `?` `/` .

   * Använder [formatet CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)
   
     > [!IMPORTANT]
     >
     > Använd inte följande IP-adressutrymmen för ditt virtuella nätverk eller undernät eftersom de inte kan matchas av Azure Logic Apps:<p>
     > 
     > * 0.0.0.0/8
     > * 100.64.0.0/10
     > * 127.0.0.0/8
     > * 168.63.129.16/32
     > * 169.254.169.254/32

   * Använder en `/27` i adressutrymmet eftersom varje undernät kräver 32 adresser. Har till exempel `10.0.0.0/27` 32 adresser eftersom 2<sup>(32–27)</sup> är 2<sup>5</sup> eller 32. Fler adresser ger inga extra fördelar. Mer information om hur du beräknar adresser finns [i IPv4 CIDR-block.](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks)

   * Om du [använder ExpressRoute](../expressroute/expressroute-introduction.md) [](../virtual-network/manage-route-table.md) måste du skapa en vägtabell som har följande väg och länka tabellen med varje undernät som används av din ISE:

     **Namn:**<*vägnamn*><br>
     **Adressprefix:** 0.0.0.0/0<br>
     **Nästa hopp:** Internet

   1. Under listan Undernät väljer du **Hantera undernätskonfiguration.** 

      ![Hantera undernätskonfiguration](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. I **fönstret Undernät** väljer du **Undernät.**

      ![Lägga till fyra tomma undernät](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. I fönstret **Lägg till undernät** anger du den här informationen.

      * **Namn:** Namnet på undernätet
      * **Adressintervall (CIDR-block):** Undernätets intervall i det virtuella nätverket och i CIDR-format

      ![Lägg till information om undernät](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. När du är klar väljer du **Ok**.

   1. Upprepa de här stegen för ytterligare tre undernät.

      > [!NOTE]
      > Om de undernät som du försöker skapa inte är giltiga visar Azure Portal ett meddelande, men blockerar inte förloppet.

   Mer information om hur du skapar undernät finns i [Lägga till ett undernät för virtuellt nätverk.](../virtual-network/virtual-network-manage-subnet.md)

1. När Azure har verifierat DIN ISE-information väljer **du Skapa,** till exempel:

   ![När verifieringen är lyckad väljer du "Skapa"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure börjar distribuera din miljö, vilket vanligtvis tar inom två timmar att slutföra. Ibland kan distributionen ta upp till fyra timmar. Om du vill kontrollera distributionsstatusen väljer du meddelandeikonen i Azure-verktygsfältet, vilket öppnar meddelandefönstret.

   ![Kontrollera distributionsstatus](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Om distributionen är klar visar Azure det här meddelandet:

   ![Distributionen lyckades](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   Annars följer du anvisningarna Azure Portal för felsökning av distributionen.

   > [!NOTE]
   > Om distributionen misslyckas eller om du tar bort DIN ISE kan det ta upp till en timme eller eventuellt längre tid i sällsynta fall innan undernäten frigörs. Därför kan du behöva vänta innan du kan återanvända dessa undernät i en annan ISE.
   >
   > Om du tar bort ditt virtuella nätverk tar Azure vanligtvis upp till två timmar innan undernäten släpps, men den här åtgärden kan ta längre tid. 
   > När du tar bort virtuella nätverk kontrollerar du att inga resurser fortfarande är anslutna. 
   > Se [Ta bort virtuellt nätverk.](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)

1. Om du vill visa din **miljö väljer du Gå till** resurs om Azure inte automatiskt går till din miljö när distributionen är klar.

1. För en ISE  som har extern slutpunktsåtkomst måste du skapa en nätverkssäkerhetsgrupp, om du inte redan har en, och lägga till en inkommande säkerhetsregel som tillåter trafik från utgående IP-adresser för hanterad anslutningsapp. Följ dessa steg om du vill konfigurera den här regeln:

   1. På ISE-menyn går du **till Inställningar** och väljer **Egenskaper.**

   1. Under **Utgående IP-adresser för** anslutningsappen kopierar du de offentliga IP-adressintervallen, som också visas i den här artikeln Gränser och [konfiguration – Utgående IP-adresser.](../logic-apps/logic-apps-limits-and-config.md#outbound)

   1. Skapa en nätverkssäkerhetsgrupp om du inte redan har en.
   
   1. Baserat på följande information lägger du till en inkommande säkerhetsregel för de offentliga utgående IP-adresser som du kopierade. Mer information finns i [Självstudie: Filtrera nätverkstrafik med en nätverkssäkerhetsgrupp med hjälp av Azure Portal](../virtual-network/tutorial-filter-network-traffic.md#create-a-network-security-group).

      | Syfte | Källtjänsttagg eller IP-adresser | Källportar | Måltjänsttagg eller IP-adresser | Målportar | Kommentarer |
      |---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
      | Tillåt trafik från utgående IP-adresser för anslutningsappen | <*connector-public-outbound-IP-addresses*> | * | Adressutrymme för det virtuella nätverket med ISE-undernät | * | |
      |||||||

1. Information om hur du kontrollerar nätverkshälsan för din ISE finns [i Hantera integreringstjänstmiljön.](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

   > [!CAUTION]
   > Om ise-nätverket blir felaktigt kan det interna App Service-miljön (ASE) som används av ISE också bli felaktigt. Om ASE inte är feltillstånd i mer än sju dagar pausas ASE. Lös problemet genom att kontrollera konfigurationen av det virtuella nätverket. Lös eventuella problem som du hittar och starta sedan om DIN ISE. Annars tas den inaktiverade ASE-enheten bort efter 90 dagar och ise-enheten blir oanvändbar. Se därför till att hålla ise-enheten felfri för att tillåta nödvändig trafik.
   > 
   > Mer information finns i de här ämnena:
   >
   > * [Azure App Service diagnostiköversikt](../app-service/overview-diagnostics.md)
   > * [Meddelandeloggning för Azure App Service-miljön](../app-service/environment/using-an-ase.md#logging)

1. Information om hur du börjar skapa logikappar och andra artefakter i ise-miljön finns i [Lägga till resurser i integreringstjänstmiljöer.](../logic-apps/add-artifacts-integration-service-environment-ise.md)

   > [!IMPORTANT]
   > När du har skapat ISE blir hanterade ISE-anslutningsappar tillgängliga för dig, men de visas inte automatiskt i anslutningsväljaren i Logikappdesignern. Innan du kan använda dessa ISE-anslutningsappar måste du manuellt lägga till och distribuera dessa anslutningsappar till [ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) så att de visas i Logikappdesignern.

## <a name="next-steps"></a>Nästa steg

* [Lägga till resurser i integreringstjänstmiljöer](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Hantera integreringstjänstmiljöer](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Läs mer om [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Läs mer om [integrering av virtuella nätverk för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md)
