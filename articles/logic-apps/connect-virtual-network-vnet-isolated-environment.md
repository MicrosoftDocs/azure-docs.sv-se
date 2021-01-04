---
title: Ansluta till virtuella Azure-nätverk med en ISE
description: Skapa en integrerings tjänst miljö (ISE) som kan komma åt Azure Virtual Networks (virtuella nätverk) från Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: 315de18539bf083515658b40fa70f3c214d7c909
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739747"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Ansluta till virtuella Azure-nätverk från Azure Logic Apps med hjälp av en integrerings tjänst miljö (ISE)

För scenarier där dina Logi Kap par och integrations konton behöver åtkomst till ett [virtuellt Azure-nätverk](../virtual-network/virtual-networks-overview.md)kan du skapa en [ *integrerings tjänst miljö* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). En ISE är en dedikerad miljö som använder dedikerad lagring och andra resurser som hålls åtskilda från den ”globala” Logic Apps-tjänsten med flera klienter. Den här separationen minskar också eventuell påverkan som andra Azure-klienter kan ha på dina appars prestanda. Med en ISE får du även egna statiska IP-adresser. De här IP-adresserna skiljer sig från de statiska IP-adresser som delas av logi Kap par i den offentliga tjänsten för flera innehavare.

När du skapar en ISE, *injicerar* Azure som ISE i ditt virtuella Azure-nätverk, som sedan distribuerar tjänsten Logic Apps till ditt virtuella nätverk. När du skapar en Logic app eller ett integrations konto väljer du din ISE som plats. Din Logic app-eller integrations konto kan sedan få direkt åtkomst till resurser, till exempel virtuella datorer, servrar, system och tjänster, i ditt virtuella nätverk.

![Välj integrerings tjänst miljö](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> För att Logic Apps och integrations konton ska fungera tillsammans i en ISE måste båda använda *samma ISE* som plats.

En ISE har ökat gränserna för körnings tid, lagrings kvarhållning, data flöde, timeout för HTTP-begäran och svar, meddelande storlekar och anpassade anslutnings begär Anden. Mer information finns i [gränser och konfiguration för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md). Mer information om ISEs finns i [åtkomst till Azure Virtual Network-resurser från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

Den här artikeln visar hur du utför dessa uppgifter med hjälp av Azure Portal:

* Aktivera åtkomst för din ISE.
* Skapa din ISE.
* Lägg till extra kapacitet i ISE.

Du kan också skapa en ISE med hjälp av [exemplet Azure Resource Manager snabb starts mal len](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) eller genom att använda Logic Apps REST API, inklusive att ställa in Kundhanterade nycklar:

* [Skapa en Integration Service Environment (ISE) med hjälp av Logic Apps REST-API](../logic-apps/create-integration-service-environment-rest-api.md)
* [Konfigurera Kundhanterade nycklar för att kryptera data i vila för ISEs](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > Logi Kap par, inbyggda utlösare, inbyggda åtgärder och anslutningar som körs i din ISE använder en pris plan som skiljer sig från den förbruknings bara pris planen. Information om hur priser och fakturering fungerar för ISEs finns i [pris modellen Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Pris nivåer finns i [Logic Apps prissättning](../logic-apps/logic-apps-pricing.md).

* Ett [virtuellt Azure-nätverk](../virtual-network/virtual-networks-overview.md) som har fyra *tomma* undernät, vilket krävs för att skapa och distribuera resurser i din ISE och används av dessa interna och dolda komponenter:

  * Logic Apps Compute
  * Interna App Service-miljön (kopplingar)
  * Interna API Management (kopplingar)
  * Internt Redis för cachelagring och prestanda
  
  Du kan skapa undernät i förväg eller när du skapar din ISE så att du kan skapa under näten på samma gång. Innan du skapar dina undernät måste du dock se till att du granskar [under nätets krav](#create-subnet).

  * Se till att ditt virtuella nätverk [ger åtkomst till din ISE](#enable-access) så att din ISE kan fungera korrekt och vara tillgänglig.

  * Om du använder eller vill använda [ExpressRoute](../expressroute/expressroute-introduction.md) tillsammans med [Tvingad tunnel trafik](../firewall/forced-tunneling.md)måste du [skapa en](../virtual-network/manage-route-table.md) routningstabell med följande angivna väg och länka routningstabellen till varje undernät som används av din ISE:

    **Namn**: <*Route-Name*><br>
    **Adressprefix**: 0.0.0.0/0<br>
    **Nästa hopp**: Internet
    
    Den här typen av väg tabell krävs så att Logic Apps-komponenter kan kommunicera med andra beroende Azure-tjänster, till exempel Azure Storage och Azure SQL DB. Mer information om den här vägen finns i adressprefixet [0.0.0.0/0](../virtual-network/virtual-networks-udr-overview.md#default-route). Om du inte använder Tvingad tunnel trafik med ExpressRoute behöver du inte den här en speciell routningstabell.
    
    Med ExpressRoute kan du utöka dina lokala nätverk till Microsoft-molnet och ansluta till Microsofts moln tjänster via en privat anslutning som under lättas av anslutnings leverantören. Mer specifikt är ExpressRoute ett virtuellt privat nätverk som dirigerar trafik över ett privat nätverk, i stället för via det offentliga Internet. Dina Logic Apps kan ansluta till lokala resurser som finns i samma virtuella nätverk när de ansluter via ExpressRoute eller ett virtuellt privat nätverk.
   
  * Om du använder en [virtuell nätverks installation (NVA)](../virtual-network/virtual-networks-udr-overview.md#user-defined)ser du till att du inte aktiverar TLS/SSL-terminering eller ändrar utgående TLS/SSL-trafik. Se också till att du inte aktiverar inspektion för trafik som kommer från din ISEs undernät. Mer information finns i [trafik dirigering för virtuella nätverk](../virtual-network/virtual-networks-udr-overview.md).

  * Om du vill använda anpassade DNS-servrar för ditt virtuella Azure-nätverk [konfigurerar du dessa servrar genom att följa de här stegen](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) innan du distribuerar din ISE till ditt virtuella nätverk. Mer information om hur du hanterar DNS-serverinställningar finns i [skapa, ändra eller ta bort ett virtuellt nätverk](../virtual-network/manage-virtual-network.md#change-dns-servers).

    > [!NOTE]
    > Om du ändrar inställningarna för DNS-servern eller DNS-servern måste du starta om ISE så att ISE kan hämta ändringarna. Mer information finns i [starta om din ISE](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE).

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Aktivera åtkomst för ISE

När du använder en ISE med ett virtuellt Azure-nätverk har ett vanligt installations problem en eller flera blockerade portar. De anslutningar som du använder för att skapa anslutningar mellan ISE och mål system kan också ha egna port krav. Om du till exempel kommunicerar med ett FTP-system med hjälp av FTP-anslutningen måste den port som du använder på ditt FTP-system vara tillgänglig, till exempel port 21 för att skicka kommandon.

För att se till att din ISE är tillgänglig och att Logi Kap par i som ISE kan kommunicera via varje undernät i det virtuella nätverket, [öppnar du portarna som beskrivs i den här tabellen för varje undernät](#network-ports-for-ise). Om några av de portar som krävs inte är tillgängliga fungerar inte din ISE som den ska.

* Om du har flera ISE-instanser som behöver åtkomst till andra slut punkter som har IP-begränsningar distribuerar du en [Azure-brandvägg](../firewall/overview.md) eller en [virtuell nätverks](../virtual-network/virtual-networks-overview.md#filter-network-traffic) installation till det virtuella nätverket och dirigerar utgående trafik via brand väggen eller den virtuella nätverks installationen. Du kan sedan [Konfigurera en enskild, utgående, offentlig, statisk och förutsägbar IP-adress](connect-virtual-network-vnet-set-up-single-ip-address.md) som alla instanser av ISE i det virtuella nätverket kan använda för att kommunicera med mål systemen. På så sätt behöver du inte konfigurera ytterligare brand Väggs öppningar på dessa mål system för varje ISE.

   > [!NOTE]
   > Du kan använda den här metoden för en enskild ISE när ditt scenario kräver att du begränsar antalet IP-adresser som behöver åtkomst. Överväg om ytterligare kostnader för brand väggen eller den virtuella nätverks enheten passar ditt scenario. Läs mer om [priser för Azure-brandvägg](https://azure.microsoft.com/pricing/details/azure-firewall/).

* Om du har skapat ett nytt virtuellt Azure-nätverk och undernät utan några begränsningar behöver du inte konfigurera [nätverks säkerhets grupper (NSG: er)](../virtual-network/network-security-groups-overview.md#network-security-groups) i det virtuella nätverket för att styra trafiken mellan undernät.

* För ett befintligt virtuellt nätverk kan du *välja* att konfigurera [nätverks säkerhets grupper (NSG: er)](../virtual-network/network-security-groups-overview.md#network-security-groups) för att [filtrera nätverks trafik över undernät](../virtual-network/tutorial-filter-network-traffic.md). Om du vill använda den här vägen, eller om du redan använder NSG: er, se till att du [öppnar portarna som beskrivs i den här tabellen](#network-ports-for-ise) för dessa NSG: er.

  När du ställer in [säkerhets regler för NSG](../virtual-network/network-security-groups-overview.md#security-rules)måste du använda *både* **TCP** -och **UDP** -protokollen, eller så kan du välja **en** i stället så att du inte behöver skapa separata regler för varje protokoll. NSG säkerhets regler beskriver de portar som du måste öppna för IP-adresserna som behöver åtkomst till dessa portar. Kontrol lera att alla brand väggar, routrar eller andra objekt som finns mellan dessa slut punkter också behåller de portarna som är tillgängliga för dessa IP-adresser.

* Om du konfigurerar Tvingad tunnel trafik genom brand väggen för att omdirigera Internet-bundit trafik, granskar du [ytterligare krav för Tvingad tunnel](#forced-tunneling)trafik.

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Nätverksportar som används av din ISE

I den här tabellen beskrivs de portar som din ISE måste ha åtkomst till och syftet med dessa portar. För att minska komplexiteten när du skapar säkerhets regler använder tabellen [tjänst koder](../virtual-network/service-tags-overview.md) som representerar grupper med IP-adressprefix för en speciell Azure-tjänst. Om inget annat anges kan *interna ISE* och *extern ISE* referera till [åtkomst slut punkten som väljs när ISE skapas](connect-virtual-network-vnet-isolated-environment.md#create-environment). Mer information finns i [slut punkts åtkomst](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access).

> [!IMPORTANT]
> För alla regler, se till att du ställer in käll portar på `*` eftersom käll portar är tillfälliga.

#### <a name="inbound-security-rules"></a>Ingående säkerhetsregler

| Syfte | Käll tjänst tag gen eller IP-adresser | Källportar | Mål service tag eller IP-adresser | Målportar | Obs! |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Kommunikation mellan undernät i det virtuella nätverket | Adress utrymme för det virtuella nätverket med ISE-undernät | * | Adress utrymme för det virtuella nätverket med ISE-undernät | * | Krävs för att trafik ska flöda *mellan* under näten i det virtuella nätverket. <p><p>**Viktigt**: för att trafik ska flöda mellan *komponenterna* i varje undernät, se till att du öppnar alla portar i varje undernät. |
| Båda: <p>Kommunikation till din Logic app <p><p>Kör historik för Logic app| Intern ISE: <br>**VirtualNetwork** <p><p>Extern ISE: **Internet** eller se **kommentarer** | * | **VirtualNetwork** | 443 | I stället för att använda taggen **Internet** service kan du ange käll-IP-adressen för följande objekt: <p><p>– Datorn eller tjänsten som anropar alla begär ande utlösare eller Webhooks i din Logic app <p>– Datorn eller tjänsten som du vill få åtkomst till kör historik för Logic app <p><p>**Viktigt**: om du stänger eller blockerar den här porten förhindras anrop till Logic Apps som har begär ande utlösare eller Webhooks. Du hindras också från att komma åt indata och utdata för varje steg i körnings historiken. Du kommer dock inte hindras från att komma åt körnings historiken för Logic app.|
| Logic Apps designer – dynamiska egenskaper | **LogicAppsManagement** | * | **VirtualNetwork** | 454 | Förfrågningar kommer från den Logic Apps åtkomst slut punktens [inkommande IP-adresser](../logic-apps/logic-apps-limits-and-config.md#inbound) för den regionen. |
| Kopplings distribution | **AzureConnectors** | * | **VirtualNetwork** | 454 | Krävs för att distribuera och uppdatera anslutningar. Om du stänger eller blockerar den här porten kan ISE-distributioner Miss Missing och förhindrar anslutnings uppdateringar och korrigeringar. |
| Nätverks hälso kontroll | **LogicApps** | * | **VirtualNetwork** | 454 | Förfrågningar kommer från Logic Apps åtkomst slut punktens [inkommande IP-adresser](../logic-apps/logic-apps-limits-and-config.md#inbound) och [utgående IP-adresser](../logic-apps/logic-apps-limits-and-config.md#outbound) för den regionen. |
| Beroende för App Service hantering | **AppServiceManagement** | * | **VirtualNetwork** | 454, 455 ||
| Kommunikation från Azure Traffic Manager | **AzureTrafficManager** | * | **VirtualNetwork** | Intern ISE: 454 <p><p>Extern ISE: 443 ||
| Båda: <p>Distribution av kopplings princip <p>API Management hanterings slut punkt | **API Management** | * | **VirtualNetwork** | 3443 | För distribution av anslutnings principer krävs port åtkomst för att distribuera och uppdatera anslutningar. Om du stänger eller blockerar den här porten kan ISE-distributioner Miss Missing och förhindrar anslutnings uppdateringar och korrigeringar. |
| Få åtkomst till Azure cache för Redis-instanser mellan roll instanser | **VirtualNetwork** | * | **VirtualNetwork** | 6379-6383, plus se **kommentarer**| För att ISE ska fungera med Azure cache för Redis måste du öppna de här [utgående och inkommande portarna som beskrivs i Azure cache för Redis vanliga frågor och svar](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
|||||||

#### <a name="outbound-security-rules"></a>Säkerhetsregler för utgående trafik

| Syfte | Käll tjänst tag gen eller IP-adresser | Källportar | Mål service tag eller IP-adresser | Målportar | Obs! |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Kommunikation mellan undernät i det virtuella nätverket | Adress utrymme för det virtuella nätverket med ISE-undernät | * | Adress utrymme för det virtuella nätverket med ISE-undernät | * | Krävs för att trafik ska flöda *mellan* under näten i det virtuella nätverket. <p><p>**Viktigt**: för att trafik ska flöda mellan *komponenterna* i varje undernät, se till att du öppnar alla portar i varje undernät. |
| Kommunikation från din Logic app | **VirtualNetwork** | * | Varierar beroende på mål | 80, 443 | Målet varierar beroende på slut punkterna för den externa tjänst som din Logic app behöver för att kommunicera med. |
| Azure Active Directory | **VirtualNetwork** | * | **AzureActiveDirectory** | 80, 443 ||
| Azure Storage beroende | **VirtualNetwork** | * | **Storage** | 80, 443, 445 ||
| Anslutnings hantering | **VirtualNetwork** | * | **AppService** | 443 ||
| Publicera diagnostikloggar & mått | **VirtualNetwork** | * | **AzureMonitor** | 443 ||
| Azure SQL-beroende | **VirtualNetwork** | * | **SQL** | 1433 ||
| Azure Resource Health | **VirtualNetwork** | * | **AzureMonitor** | 1886 | Krävs för att publicering av hälso status ska Resource Health. |
| Beroende från logg till Event Hub-princip och övervaknings agent | **VirtualNetwork** | * | **EventHub** | 5672 ||
| Få åtkomst till Azure cache för Redis-instanser mellan roll instanser | **VirtualNetwork** | * | **VirtualNetwork** | 6379-6383, plus se **kommentarer**| För att ISE ska fungera med Azure cache för Redis måste du öppna de här [utgående och inkommande portarna som beskrivs i Azure cache för Redis vanliga frågor och svar](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| DNS-namnmatchning | **VirtualNetwork** | * | IP-adresser för alla anpassade Domain Name System (DNS-servrar) i det virtuella nätverket | 53 | Krävs endast när du använder anpassade DNS-servrar i det virtuella nätverket |
|||||||

Dessutom måste du lägga till utgående regler för [App Service-miljön (ASE)](../app-service/environment/intro.md):

* Om du använder Azure-brandväggen måste du konfigurera din brand vägg med taggen App Service-miljön (ASE) [fullständigt kvalificerade domän namn (FQDN)](../firewall/fqdn-tags.md#current-fqdn-tags)som tillåter utgående åtkomst till ASE-plattforms trafik.

* Om du använder en annan brand vägg än Azure Firewall måste du konfigurera brand väggen med *alla* regler i [brand väggens integrations beroenden](../app-service/environment/firewall-integration.md#dependencies) som krävs för App Service-miljön.

<a name="forced-tunneling"></a>

#### <a name="forced-tunneling-requirements"></a>Krav för Tvingad tunnel trafik

Om du ställer in eller använder [Tvingad tunnel trafik](../firewall/forced-tunneling.md) genom brand väggen måste du tillåta ytterligare externa beroenden för din ISE. Med Tvingad tunnel trafik kan du omdirigera Internet-bundit trafik till ett utsedd nästa hopp, till exempel ditt virtuella privata nätverk (VPN) eller till en virtuell installation, i stället för till Internet så att du kan inspektera och granska utgående nätverks trafik.

Om du inte tillåter åtkomst för dessa beroenden Miss lyckas din ISE-distribution och din distribuerade ISE slutar fungera.

* Användardefinierade vägar

  För att förhindra asymmetrisk routning måste du definiera en väg för varje IP-adress som anges nedan med **Internet** som nästa hopp.
  
  * [App Service-miljön hanterings adresser](../app-service/environment/management-addresses.md)
  * [Azure IP-adresser för anslutningar i avsnittet ISE som är tillgängliga i den här nedladdnings filen](https://www.microsoft.com/download/details.aspx?id=56519)
  * [Azure Traffic Manager hanterings adresser](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json)
  * [Logic Apps inkommande och utgående adresser för ISE-regionen](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses-and-service-tags)
  * [Azure IP-adresser för anslutningar i den ISE-region som finns i den här nedladdnings filen](https://www.microsoft.com/download/details.aspx?id=56519)

* Tjänstslutpunkter

  Du måste aktivera tjänstens slut punkter för Azure SQL, Storage, Service Bus, nyckel valv och Event Hubs eftersom du inte kan skicka trafik via en brand vägg till dessa tjänster.

*  Andra inkommande och utgående beroenden

   Brand väggen *måste* tillåta följande inkommande och utgående beroenden:
   
   * [Azure App Service beroenden](../app-service/environment/firewall-integration.md#deploying-your-ase-behind-a-firewall)
   * [Azure Cache Service-beroenden](../azure-cache-for-redis/cache-how-to-premium-vnet.md#what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks)
   * [Azure API Management-beroenden](../api-management/api-management-using-with-vnet.md#-common-network-configuration-issues)

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Skapa din ISE

1. I rutan [Azure Portal](https://portal.azure.com), i den huvudsakliga Azure Search-rutan, anger `integration service environments` du som filter och väljer **integrerings tjänst miljöer**.

   ![Hitta och välj integrerings tjänst miljöer](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. I fönstret **miljöer för integrations tjänster** väljer du **Lägg till**.

   ![Välj Lägg till för att skapa integrerings tjänst miljön](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Ange den här informationen för din miljö och välj sedan **Granska + skapa**, till exempel:

   ![Ange miljö information](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Prenumeration** | Ja | <*Azure-prenumeration-namn*> | Azure-prenumerationen som ska användas för din miljö |
   | **Resursgrupp** | Ja | <*Azure-resurs-grupp-namn*> | En ny eller befintlig Azure-resurs grupp där du vill skapa din miljö |
   | **Namn på integrerings tjänst miljö** | Ja | <*miljö namn*> | Ditt ISE-namn, som endast får innehålla bokstäver, siffror, bindestreck ( `-` ), under streck ( `_` ) och punkter ( `.` ). |
   | **Plats** | Ja | <*Azure-datacenter-region*> | Azure Data Center-regionen där du distribuerar din miljö |
   | **SKU** | Ja | **Premium** eller **Developer (service avtal)** | ISE-SKU: n för att skapa och använda. För skillnader mellan dessa SKU: er, se [ISE SKU: er](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Viktigt**: det här alternativet är endast tillgängligt vid skapande av ISE och kan inte ändras senare. |
   | **Ytterligare kapacitet** | Premium: <br>Ja <p><p>Utvecklarläget <br>Inte tillämpligt | Premium: <br>0 till 10 <p><p>Utvecklarläget <br>Inte tillämpligt | Antalet ytterligare bearbetnings enheter som ska användas för denna ISE-resurs. Information om hur du lägger till kapacitet när du har skapat finns i [lägga till ISE-kapacitet](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) |
   | **Åtkomst slut punkt** | Ja | **Intern** eller **extern** | Den typ av åtkomst slut punkter som ska användas för din ISE. Dessa slut punkter avgör om begäran eller webhook-utlösare på Logic Apps i din ISE kan ta emot samtal utanför det virtuella nätverket. <p><p>Om du till exempel vill använda följande webhook-baserade utlösare, se till att du väljer **externt**: <p><p>– Azure-DevOps <br>-Azure Event Grid <br>-Common Data Service <br>– Office 365 <br>– SAP (ISE-version) <p><p>Valet påverkar också hur du kan visa och komma åt indata och utdata i din Logic app kör historik. Mer information finns i [åtkomst till ISE-slutpunkt](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Viktigt**: du kan bara välja åtkomst slut punkten under skapande av ISE och inte ändra det här alternativet senare. |
   | **Virtuellt nätverk** | Ja | <*Azure-Virtual-Network-Name*> | Det virtuella Azure-nätverket där du vill mata in din miljö så att Logic Apps i den miljön kan komma åt ditt virtuella nätverk. Om du inte har ett nätverk [skapar du först ett virtuellt Azure-nätverk](../virtual-network/quick-create-portal.md). <p><p>**Viktigt**: du kan *bara* utföra den här inmatningen när du skapar din ISE. |
   | **Undernät** | Ja | <*undernät-resurs lista*> | En ISE kräver fyra *tomma* undernät, vilket krävs för att skapa och distribuera resurser i din ISE och används av interna Logic Apps-komponenter, till exempel anslutningar och cachelagring för prestanda. <p>**Viktigt**: se till att du [granskar kraven för undernät innan du fortsätter med de här stegen för att skapa dina undernät](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **Skapa undernät**

   ISE kräver fyra *tomma* undernät, vilket krävs för att skapa och distribuera resurser i din ISE och används av interna Logic Apps-komponenter, till exempel anslutningar och cachelagring för prestanda. Du *kan inte* ändra dessa under näts adresser när du har skapat din miljö. Om du skapar och distribuerar din ISE via Azure Portal, se till att du inte delegerar dessa undernät till några Azure-tjänster. Men om du skapar och distribuerar din ISE via REST API, Azure PowerShell eller en Azure Resource Manager mall måste du [delegera](../virtual-network/manage-subnet-delegation.md) ett tomt undernät till `Microsoft.integrationServiceEnvironment` . Mer information finns i [lägga till en under näts delegering](../virtual-network/manage-subnet-delegation.md).

   Varje undernät måste uppfylla följande krav:

   * Använder ett namn som börjar med ett alfabetiskt tecken eller ett under streck (inga siffror) och inte använder dessa tecken:,,,,, `<` `>` `%` `&` `\\` `?` , `/` .

   * Använder [CIDR-formatet (classless Inter-Domain routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).
   
     > [!IMPORTANT]
     >
     > Använd inte följande IP-adressutrymme för ditt virtuella nätverk eller undernät eftersom de inte går att matcha med Azure Logic Apps:<p>
     > 
     > * 0.0.0.0/8
     > * 100.64.0.0/10
     > * 127.0.0.0/8
     > * 168.63.129.16/32
     > * 169.254.169.254/32

   * Använder en `/27` i adress utrymmet eftersom varje undernät kräver 32 adresser. Har till exempel `10.0.0.0/27` 32 adresser eftersom 2<sup>(32-27)</sup> är 2<sup>5</sup> eller 32. Fler adresser ger inga ytterligare förmåner. Mer information om hur du beräknar adresser finns i [IPv4 CIDR-block](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Om du använder [ExpressRoute](../expressroute/expressroute-introduction.md)måste du [skapa en](../virtual-network/manage-route-table.md) routningstabell med följande väg och länka tabellen till varje undernät som används av din ISE:

     **Namn**: <*Route-Name*><br>
     **Adressprefix**: 0.0.0.0/0<br>
     **Nästa hopp**: Internet

   1. I listan **undernät** väljer du **Hantera under näts konfiguration**.

      ![Hantera under näts konfiguration](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. I fönstret **undernät** väljer du **undernät**.

      ![Lägg till fyra tomma undernät](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. Ange den här informationen i fönstret **Lägg till undernät** .

      * **Namn**: namnet på under nätet
      * **Adress intervall (CIDR-block)**: under nätets intervall i det virtuella nätverket och i CIDR-format

      ![Lägg till information om undernät](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. När du är klar väljer du **Ok**.

   1. Upprepa de här stegen för tre fler undernät.

      > [!NOTE]
      > Om de undernät du försöker skapa inte är giltiga, visar Azure Portal ett meddelande, men blockerar inte ditt förlopp.

   Mer information om hur du skapar undernät finns i [lägga till ett undernät för virtuellt nätverk](../virtual-network/virtual-network-manage-subnet.md).

1. När Azure har verifierat din ISE-information väljer du **skapa**, till exempel:

   ![När verifieringen är klar väljer du "skapa"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure börjar distribuera din miljö, som vanligt vis tar inom två timmar att slutföra. Ibland kan distributionen ta upp till fyra timmar. Om du vill kontrol lera distributions statusen i Azure-verktygsfältet väljer du ikonen meddelanden, som öppnar fönstret meddelanden.

   ![Kontrol lera distributions status](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Om distributionen har slutförts visas följande meddelande i Azure:

   ![Distributionen lyckades](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   Annars följer du Azure Portal anvisningarna för fel sökning av distribution.

   > [!NOTE]
   > Om distributionen Miss lyckas eller om du tar bort din ISE kan Azure ta upp till en timme eller kanske längre i sällsynta fall innan du släpper dina undernät. Så du kan behöva vänta innan du kan återanvända dessa undernät i en annan ISE.
   >
   > Om du tar bort det virtuella nätverket tar Azure vanligt vis upp till två timmar innan du frigör dina undernät, men den här åtgärden kan ta längre tid. 
   > Se till att inga resurser fortfarande är anslutna när du tar bort virtuella nätverk. 
   > Se [ta bort virtuellt nätverk](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Om du vill visa din miljö väljer du **gå till resurs** om Azure inte automatiskt går till din miljö när distributionen är klar.

1. För en ISE som har *extern* slut punkts åtkomst måste du skapa en nätverks säkerhets grupp, om du inte redan har en, och lägga till en inkommande säkerhets regel för att tillåta trafik från hanterade anslutnings-IP-adresser. Följ dessa steg om du vill konfigurera den här regeln:

   1. I menyn ISE väljer du **Egenskaper** under **Inställningar**.

   1. Under **koppling utgående IP-adresser** kopierar du de offentliga IP-adressintervall som också visas i den här artikeln, [gränser och konfiguration-utgående IP-adresser](../logic-apps/logic-apps-limits-and-config.md#outbound).

   1. Skapa en nätverks säkerhets grupp om du inte redan har en.
   
   1. Baserat på följande information lägger du till en inkommande säkerhets regel för de offentliga utgående IP-adresser som du kopierade. Mer information finns i [Självstudier: filtrera nätverks trafik med en nätverks säkerhets grupp med hjälp av Azure Portal](../virtual-network/tutorial-filter-network-traffic.md#create-a-network-security-group).

      | Syfte | Käll tjänst tag gen eller IP-adresser | Källportar | Mål service tag eller IP-adresser | Målportar | Obs! |
      |---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
      | Tillåt trafik från utgående IP-adresser för anslutningar | <*anslutnings-offentlig-utgående IP-adresser*> | * | Adress utrymme för det virtuella nätverket med ISE-undernät | * | |
      |||||||

1. Information om hur du kontrollerar nätverks hälsan för din ISE finns i [Hantera integrerings tjänst miljön](../logic-apps/ise-manage-integration-service-environment.md#check-network-health).

   > [!CAUTION]
   > Om din ISE-nätverk blir felfria kan den interna App Service-miljön (ASE) som används av din ISE också bli felaktig. Om ASE är i fel tillstånd i mer än sju dagar pausas ASE. Kontrol lera konfigurationen av det virtuella nätverket för att lösa det här läget. Lös eventuella problem som du hittar och starta sedan om ISE. I annat fall tas den uppskjutna ASE bort, och ISE blir oanvändbara om du har 90 dagar. Därför bör du se till att du låter din ISE vara felfri för att tillåta nödvändig trafik.
   > 
   > Mer information finns i de här ämnena:
   >
   > * [Översikt över Azure App Service diagnostik](../app-service/overview-diagnostics.md)
   > * [Meddelande loggning för Azure App Service-miljön](../app-service/environment/using-an-ase.md#logging)

1. Information om hur du börjar skapa Logi Kap par och andra artefakter i din ISE finns i [lägga till resurser i integrerings tjänst miljöer](../logic-apps/add-artifacts-integration-service-environment-ise.md).

   > [!IMPORTANT]
   > När du har skapat din ISE blir Managed ISE-kopplingar tillgängliga så att du kan använda, men de visas inte automatiskt i anslutnings väljaren i Logic App Designer. Innan du kan använda dessa ISE-kopplingar måste du manuellt [lägga till och distribuera dessa anslutningar till ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) så att de visas i Logic App Designer.

## <a name="next-steps"></a>Nästa steg

* [Lägga till resurser i integreringstjänstmiljöer](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Hantera integreringstjänstmiljöer](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Läs mer om [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Lär dig mer om [Virtual Network-integrering för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md)
