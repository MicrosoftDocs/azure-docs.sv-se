---
title: Använda Azure API Management med virtuella nätverk
description: Lär dig hur du konfigurerar en anslutning till ett virtuellt nätverk i Azure API Management och får åtkomst till webb tjänster via det.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/10/2020
ms.author: apimpm
ms.custom: references_regions
ms.openlocfilehash: c63b71ad00a5621babe07597720a1e9ea87f1e4a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99260256"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Använda Azure API Management med virtuella nätverk
Med virtuella Azure-nätverk (VNET) kan du placera valfria Azure-resurser i ett dirigerbart icke-Internetbaserat nätverk som du kontrollerar åtkomsten till. Dessa nätverk kan sedan anslutas till dina lokala nätverk med hjälp av olika VPN-tekniker. Om du vill veta mer om virtuella Azure-nätverk börjar du med informationen här: [Azure Virtual Network-översikt](../virtual-network/virtual-networks-overview.md).

Azure API Management kan distribueras inuti det virtuella nätverket (VNET), så att det kan komma åt Server dels tjänster i nätverket. Developer-portalen och API-gatewayen kan konfigureras att vara tillgängliga antingen från Internet eller endast inom det virtuella nätverket.

> [!NOTE]
> URL: en för API-importens dokument måste finnas på en offentligt tillgänglig Internet adress.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Förutsättningar

För att utföra stegen som beskrivs i den här artikeln måste du ha:

+ En aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ En APIM-instans. Mer information finns i [skapa en Azure API Management-instans](get-started-create-service-instance.md).

## <a name="enable-vnet-connection"></a><a name="enable-vpn"> </a>Aktivera VNET-anslutning

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Aktivera VNET-anslutning med hjälp av Azure Portal

1. Gå till [Azure Portal](https://portal.azure.com) för att hitta din API Management-instans. Sök efter och välj **API Management tjänster**.

2. Välj din API Management-instans.

3. Välj **virtuellt nätverk**.
4. Konfigurera API Management-instansen som ska distribueras i ett virtuellt nätverk.

    :::image type="content" source="media/api-management-using-with-vnet/api-management-menu-vnet.png" alt-text="Välj virtuellt nätverk i Azure Portal.":::
    
5. Välj önskad åtkomst typ:

    * **Av**: det här är standardinställningen. API Management distribueras inte till ett virtuellt nätverk.

    * **Externt**: API Management Gateway-och Developer-portalen kan nås från det offentliga Internet via en extern belastningsutjämnare. Gatewayen har åtkomst till resurser i det virtuella nätverket.

        ![Offentlig peering][api-management-vnet-public]

    * **Internt**: API Management Gateway-och Developer-portalen är bara tillgängliga i det virtuella nätverket via en intern belastningsutjämnare. Gatewayen har åtkomst till resurser i det virtuella nätverket.

        ![Privat peering][api-management-vnet-private]

6. Om du har valt **externt** eller **internt** visas en lista över alla regioner där API Managements tjänsten har tillhandahållits. Välj en **plats** och välj sedan dess **virtuella nätverk** och **undernät**. Listan med virtuella nätverk fylls i med både klassiska och virtuella Resource Manager-nätverk tillgängliga i de Azure-prenumerationer som har ställts in i den region som du konfigurerar.

    > [!IMPORTANT]
    > När du distribuerar en Azure API Management-instans till ett virtuellt Resource Manager-nätverk måste tjänsten finnas i ett dedikerat undernät som inte innehåller några andra resurser förutom Azure API Management-instanser. Om ett försök görs att distribuera en Azure API Management-instans till ett VNET-undernät i Resource Manager som innehåller andra resurser, kommer distributionen att Miss lyckas.

    Välj sedan **Använd**. Sidan **virtuellt nätverk** i API Management-instansen uppdateras med ditt nya alternativ för virtuella nätverk och undernät.

    :::image type="content" source="media/api-management-using-with-vnet/api-management-using-vnet-select.png" alt-text="Inställningar för virtuellt nätverk i portalen.":::

7. Välj **Spara** i det övre navigerings fältet och välj sedan **Använd Nätverks konfiguration**.

> [!NOTE]
> VIP-adressen för API Management-instansen ändras varje gång VNET aktive ras eller inaktive ras.
> VIP-adressen kommer också att ändras när API Management flyttas från **extern** till **intern** eller vice versa.
>

> [!IMPORTANT]
> Om du tar bort API Management från ett VNET eller ändrar den som det distribueras i, kan det tidigare använda VNET förbli låst i upp till sex timmar. Under den här perioden går det inte att ta bort det virtuella nätverket eller distribuera en ny resurs till den. Det här beteendet gäller för klienter som använder API-version 2018-01-01 och tidigare. Klienter som använder API-version 2019-01-01 och senare frigörs det virtuella nätverket så fort den associerade API Managements tjänsten tas bort.

## <a name="deploy-api-management-into-external-vnet"></a><a name="deploy-apim-external-vnet"> </a>Distribuera API Management till externt VNet

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-external-vnet%2Fazuredeploy.json)

* **Skapa en API Management tjänst inuti ett VNet**: Använd cmdleten [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) för att skapa en Azure API Management-tjänst i ett VNet.

* **Distribuera en befintlig API Management tjänst i ett VNet**: Använd cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) för att flytta en befintlig Azure API Management-tjänst i en Virtual Network.

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a><a name="connect-vnet"> </a>Ansluta till en webb tjänst som finns i ett virtuellt nätverk
När din API Management-tjänst är ansluten till det virtuella nätverket, är åtkomst till backend-tjänster i den inte något annat än att komma åt offentliga tjänster. Skriv bara in den lokala IP-adressen eller värd namnet (om en DNS-server har kon figurer ATS för VNET) för webb tjänsten i fältet **URL för webb tjänst** när du skapar ett nytt API eller redigerar en befintlig.

![Lägg till API från VPN][api-management-setup-vpn-add-api]

## <a name="common-network-configuration-issues"></a><a name="network-configuration-issues"> </a>Vanliga problem med nätverks konfiguration
Nedan följer en lista över vanliga fel konfigurations problem som kan uppstå när du distribuerar API Management tjänst till en Virtual Network.

* **Anpassad DNS-Server konfiguration**: API Management tjänsten är beroende av flera Azure-tjänster. När API Management finns i ett VNET med en anpassad DNS-server måste du matcha värd namnen för dessa Azure-tjänster. Följ [den här](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) vägledningen om anpassad DNS-konfiguration. Se tabellen portar nedan och andra nätverks krav för referens.

> [!IMPORTANT]
> Om du planerar att använda en anpassad DNS-server (er) för det virtuella nätverket bör du konfigurera den **innan** du distribuerar en API Management tjänst till den. Annars måste du uppdatera API Managements tjänsten varje gång du ändrar DNS-servrarna genom att köra [åtgärden tillämpa nätverks konfiguration](/rest/api/apimanagement/2019-12-01/apimanagementservice/applynetworkconfigurationupdates)

* **Portar som krävs för API Management**: inkommande och utgående trafik till under nätet där API Management distribueras kan kontrol leras med hjälp av [nätverks säkerhets gruppen][Network Security Group]. Om någon av dessa portar är otillgänglig kanske API Management inte fungerar korrekt och kan bli oåtkomlig. En eller flera av dessa portar blockeras är ett annat vanligt problem med fel konfiguration vid användning av API Management med ett VNET.

<a name="required-ports"></a> När en API Management tjänst instans finns i ett VNet används portarna i följande tabell.

| Käll-/mål Port (er) | Riktning          | Transport protokoll |   [Service märken](../virtual-network/network-security-groups-overview.md#service-tags) <br> Källa/mål   | Syfte ( \* )                                                 | Virtual Network typ |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| */[80], 443                  | Inkommande            | TCP                | INTERNET/VIRTUAL_NETWORK            | Klient kommunikation till API Management                      | Extern             |
| */3443                     | Inkommande            | TCP                | API Management/VIRTUAL_NETWORK       | Hanterings slut punkt för Azure Portal och PowerShell         | Externt & internt  |
| */443                  | Utgående           | TCP                | VIRTUAL_NETWORK/lagring             | **Beroende av Azure Storage**                             | Externt & internt  |
| */443                  | Utgående           | TCP                | VIRTUAL_NETWORK/AzureActiveDirectory | Beroende av [Azure Active Directory](api-management-howto-aad.md) och Azure-valv                  | Externt & internt  |
| */1433                     | Utgående           | TCP                | VIRTUAL_NETWORK/SQL                 | **Åtkomst till Azure SQL-slutpunkter**                           | Externt & internt  |
| */443                     | Utgående           | TCP                | VIRTUAL_NETWORK/AzureKeyVault                 | **Åtkomst till Azure-valv**                           | Externt & internt  |
| */5671, 5672, 443          | Utgående           | TCP                | VIRTUAL_NETWORK/EventHub            | Beroende för [logg till Event Hub-princip](api-management-howto-log-event-hubs.md) och övervaknings agent | Externt & internt  |
| */445                      | Utgående           | TCP                | VIRTUAL_NETWORK/lagring             | Beroende av Azure-filresurs för [git](api-management-configuration-repository-git.md)                      | Externt & internt  |
| */443, 12000                     | Utgående           | TCP                | VIRTUAL_NETWORK/AzureCloud            | Hälso-och övervaknings tillägg         | Externt & internt  |
| */1886, 443                     | Utgående           | TCP                | VIRTUAL_NETWORK/AzureMonitor         | Publicera diagnostikloggar [och mått](api-management-howto-use-azure-monitor.md), [Resource Health](../service-health/resource-health-overview.md) och [Application Insights](api-management-howto-app-insights.md)                   | Externt & internt  |
| */25, 587, 25028                       | Utgående           | TCP                | VIRTUAL_NETWORK/INTERNET            | Ansluta till SMTP-relä för att skicka e-post                    | Externt & internt  |
| */6381-6383              | Inkommande & utgående | TCP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | Åtkomst till Redis-tjänsten för [cache](api-management-caching-policies.md) -principer mellan datorer         | Externt & internt  |
| */4290              | Inkommande & utgående | UDP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | Synkronisering av räknare för principer för [hastighets begränsning](api-management-access-restriction-policies.md#LimitCallRateByKey) mellan datorer         | Externt & internt  |
| * / *                        | Inkommande            | TCP                | AZURE_LOAD_BALANCER/VIRTUAL_NETWORK | Azure-infrastruktur Load Balancer                          | Externt & internt  |

>[!IMPORTANT]
> Portarna för vilka *syftet* är **Bold** krävs för att API Management tjänsten ska kunna distribueras korrekt. Om du blockerar de andra portarna kan du dock orsaka **försämring** i möjligheten att använda och **övervaka den aktiva tjänsten och tillhandahålla det dedikerade service avtalet**.

+ **TLS-funktioner**: om du vill aktivera skapande och verifiering av TLS/SSL-certifikat kedja måste API Management tjänsten ha utgående nätverks anslutning till ocsp.msocsp.com, mscrl.microsoft.com och CRL.Microsoft.com. Beroendet krävs inte, om något certifikat som du överför till API Management innehåller hela kedjan till CA-roten.

+ **DNS-åtkomst**: utgående åtkomst på port 53 krävs för kommunikation med DNS-servrar. Om en anpassad DNS-Server finns i den andra änden av en VPN-gateway, måste DNS-servern gå att komma åt från under nätets värd API Management.

+ **Mått och hälso övervakning**: utgående nätverks anslutning till Azures övervaknings slut punkter, vilket löser problemet under följande domäner. Som visas i tabellen visas dessa URL: er under AzureMonitor-tjänst tag gen för användning med nätverks säkerhets grupper.

    | Azure-miljö | Slutpunkter                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure, offentlig      | <ul><li>gcs.prod.monitoring.core.windows.net (**ny**)</li><li>prod.warmpath.msftcloudes.com (**ska vara inaktuell**)</li><li>global.prod.microsoftmetrics.com (**ny**)</li><li>global.metrics.nsatc.net (**ska vara inaktuell**)</li><li>shoebox2.prod.microsoftmetrics.com (**ny**)</li><li>shoebox2.metrics.nsatc.net (**ska vara inaktuell**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com (**ny**)</li><li>prod3.metrics.nsatc.net (**ska vara inaktuell**)</li><li>prod3-black.prod.microsoftmetrics.com (**ny**)</li><li>prod3-black.prod3.metrics.nsatc.net (**ska vara inaktuell**)</li><li>prod3-red.prod.microsoftmetrics.com (**ny**)</li><li>prod3-red.prod3.metrics.nsatc.net (**ska vara inaktuell**)</li><li>gcs.prod.warm.ingestion.monitoring.azure.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>global.prod.microsoftmetrics.com (**ny**)</li><li>global.metrics.nsatc.net (**ska vara inaktuell**)</li><li>shoebox2.prod.microsoftmetrics.com (**ny**)</li><li>shoebox2.metrics.nsatc.net (**ska vara inaktuell**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com (**ny**)</li><li>prod3.metrics.nsatc.net (**ska vara inaktuell**)</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.us</li></ul>                                                                                                                                                                                                                                                |
    | Azure Kina 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>global.prod.microsoftmetrics.com (**ny**)</li><li>global.metrics.nsatc.net (**ska vara inaktuell**)</li><li>shoebox2.prod.microsoftmetrics.com (**ny**)</li><li>shoebox2.metrics.nsatc.net (**ska vara inaktuell**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com (**ny**)</li><li>prod3.metrics.nsatc.net (**ska vara inaktuell**)</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.cn</li></ul>                                                                                                                                                                                                                                                |

  >[!IMPORTANT]
  > Ändringen av kluster ovan med DNS-zon **. nsatc.net** till **. microsoftmetrics.com** är oftast en DNS-ändring. Klustrets IP-adress kommer inte att ändras.

+ **Regionala service märken**: NSG-regler som tillåter utgående anslutning till Storage-, SQL-och Event Hubs service-taggar kan använda regionala versioner av taggarna som motsvarar den region som innehåller API Management-instansen (till exempel lagring. väst för en API Management instans i regionen USA, västra). I distributioner i flera regioner bör NSG i varje region tillåta trafik till tjänst taggarna för den regionen och den primära regionen.

    > [!IMPORTANT]
    > Om du vill aktivera publicering av [Developer-portalen](api-management-howto-developer-portal.md) för en API Management-instans i ett virtuellt nätverk, se till att du även tillåter utgående anslutning till Blob Storage i regionen Västra USA. Använd till exempel taggen **Storage. västkusten** i en NSG-regel. Anslutning till Blob Storage i regionen Västra USA krävs för närvarande för att publicera Developer-portalen för API Management instans.

+ **SMTP-relä**: utgående nätverks anslutning för SMTP-reläet, som löses under värden `smtpi-co1.msn.com` ,, `smtpi-ch1.msn.com` `smtpi-db3.msn.com` `smtpi-sin.msn.com` och `ies.global.microsoft.com`

+ **Developer-portalen CAPTCHA**: utgående nätverks anslutning för Developer-portalens CAPTCHA, vilket löses under värdarna `client.hip.live.com` och `partner.hip.live.com` .

+ **Azure Portal diagnostik**: om du vill aktivera flödet av diagnostikloggar från Azure Portal när du använder API Management tillägget inifrån en Virtual Network, krävs utgående åtkomst till `dc.services.visualstudio.com` på port 443. Detta hjälper till att felsöka problem som kan uppstå när du använder tillägget.

+ **Azure Load Balancer**: Tillåt inkommande begäran från service tag `AZURE_LOAD_BALANCER` är inte ett krav för `Developer` SKU, eftersom vi bara distribuerar en beräknings enhet bakom den. Men inkommande från [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) blir kritiska när du skalar till högre SKU `Premium` , som fel vid hälso avsökning från Load Balancer, Miss lyckas en distribution.

+ **Application Insights**: om [Azure Application Insights](api-management-howto-app-insights.md) -övervakning är aktiverat på API Management måste vi tillåta utgående anslutning till [telemetri-slutpunkten](../azure-monitor/app/ip-addresses.md#outgoing-ports) från Virtual Network. 

+ **Tvinga tunnel trafik till lokal brand vägg med hjälp av Express Route eller virtuell nätverks** installation: en vanlig kund konfiguration är att definiera sin egen standard väg (0.0.0.0/0) som tvingar all trafik från det API Management delegerade under nätet att flöda genom en lokal brand vägg eller till en virtuell nätverks installation. Detta trafikflöde avbryter anslutningen till Azure API Management eftersom utgående trafik antingen blockeras lokalt eller NAT till en okänd uppsättning adresser som inte längre fungerar med olika Azure-slutpunkter. Lösningen kräver att du gör några saker:

  * Aktivera tjänst slut punkter i det undernät där API Managements tjänsten har distribuerats. [Tjänst slut punkter][ServiceEndpoints] måste aktive ras för Azure Sql, Azure Storage, Azure EventHub och Azure Service Bus. Genom att aktivera slut punkter direkt från API Management delegerade under nätet till dessa tjänster kan de använda det Microsoft Azure stamnät nätverket som tillhandahåller optimal routning för tjänst trafik. Om du använder tjänstens slut punkter med en Tvingad tunnel-API Management är ovanstående Azure-tjänstens trafik inte Tvingad tunnel trafik. Den andra API Management tjänst beroende trafik tvingas till tunnel trafik och går inte att förlora eller också fungerar inte tjänsten API Management.
    
  * All kontroll Plans trafik från Internet till hanterings slut punkten för API Managements tjänsten dirigeras via en speciell uppsättning inkommande IP-adresser som API Management. När trafiken är Tvingad tunnel trafik mappas svaren inte symmetriskt till dessa inkommande käll-IP-adresser. För att undvika begränsningen måste vi lägga till följande användardefinierade vägar ([UDR][UDRs]) för att styra trafiken tillbaka till Azure genom att ställa in målet för dessa värd vägar till "Internet". Uppsättningen inkommande IP-adresser för kontroll Plans trafik är dokumenterade [kontroll Plans-IP-adresser](#control-plane-ips)

  * För andra API Management tjänst beroenden som tvingas tunnel trafik bör det finnas ett sätt att matcha värd namnet och kontakta slut punkten. Dessa inkluderar
      - Mått och hälso övervakning
      - Azure Portal diagnostik
      - SMTP-relä
      - CAPTCHA för Developer-portalen

## <a name="troubleshooting"></a><a name="troubleshooting"> </a>Felsökning
* **Första installationen**: när den första distributionen av API Management tjänst till ett undernät inte lyckas, bör du först distribuera en virtuell dator till samma undernät. Nästa fjärr skrivbord i den virtuella datorn och kontrol lera att det finns en anslutning till en av varje resurs nedan i din Azure-prenumeration
    * Azure Storage BLOB
    * Azure SQL Database
    * Azure Storage tabell

  > [!IMPORTANT]
  > När du har verifierat anslutningen ser du till att ta bort alla resurser som har distribuerats i under nätet innan du distribuerar API Management till under nätet.

* **Verifiera status för nätverks anslutning**: när du har distribuerat API Management till under nätet använder du portalen för att kontrol lera anslutningens anslutning till beroenden som Azure Storage. I portalen, i den vänstra menyn, under **distribution och infrastruktur**, väljer du **nätverks anslutnings status**.

   :::image type="content" source="media/api-management-using-with-vnet/verify-network-connectivity-status.png" alt-text="Kontrol lera statusen för nätverks anslutningen i portalen":::

    * Välj **krävs** för att granska anslutningen till nödvändiga Azure-tjänster för API Management. Ett problem indikerar att instansen inte kan utföra kärn åtgärder för att hantera API: er.
    * Välj **valfritt** om du vill granska anslutningen till valfria tjänster. Eventuella problem indikerar bara att de speciella funktionerna inte fungerar (till exempel SMTP). Ett haveri kan leda till försämring i möjligheten att använda och övervaka API Management-instansen och tillhandahålla det dedikerade service avtalet.

Läs igenom [vanliga problem med nätverks konfigurationen](#network-configuration-issues) och korrigera nödvändiga nätverks inställningar för att lösa anslutnings problem.

* **Stegvisa uppdateringar**: när du gör ändringar i nätverket kan du se [NetworkStatus-API: et](/rest/api/apimanagement/2019-12-01/networkstatus)för att kontrol lera att tjänsten API Management inte har förlorat åtkomst till någon av de kritiska resurserna som den är beroende av. Anslutnings statusen måste uppdateras var 15: e minut.

* **Länkar till resurs navigering**: när du distribuerar till ett VNet-undernät i Resource Manager-format API Management reserverar under nätet genom att skapa en resurs navigerings länk. Om under nätet redan innehåller en resurs från en annan provider, kommer distributionen att **Miss** förfalla. När du flyttar en API Management-tjänst till ett annat undernät eller tar bort den, kommer vi att ta bort resurs navigerings länken.

## <a name="subnet-size-requirement"></a><a name="subnet-size"></a> Krav för under näts storlek
Azure reserverar vissa IP-adresser i varje undernät och de här adresserna kan inte användas. De första och sista IP-adresserna i under näten är reserverade för protokoll överensstämmelse, tillsammans med tre fler adresser som används för Azure-tjänster. Mer information finns i finns [det några begränsningar för att använda IP-adresser i dessa undernät?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Förutom de IP-adresser som används av Azure VNET-infrastrukturen använder varje API Management-instans i under nätet två IP-adresser per enhet av Premium-SKU eller en IP-adress för Developer-SKU: n. Varje instans reserverar en ytterligare IP-adress för den externa belastningsutjämnaren. När du distribuerar till ett internt virtuellt nätverk kräver det ytterligare en IP-adress för den interna belastningsutjämnaren.

Beräkningen ovanför under nätets minsta storlek, där API Management kan distribueras är/29, vilket ger tre användbara IP-adresser.

För varje extra skalnings enhet med API Management krävs ytterligare två IP-adresser.

## <a name="routing"></a><a name="routing"></a> Routning
+ En belastningsutjämnad offentlig IP-adress (VIP) är reserverad för att ge åtkomst till alla tjänst slut punkter.
+ En IP-adress från ett undernäts IP-intervall (DIP) används för att komma åt resurser i VNet och en offentlig IP-adress (VIP) kommer att användas för att få åtkomst till resurser utanför VNet.
+ Belastningsutjämnad offentlig IP-adress hittar du på bladet översikt/Essentials i Azure Portal.

## <a name="limitations"></a><a name="limitations"> </a>Begränsningar
* Ett undernät som innehåller API Management instanser får inte innehålla några andra typer av Azure-resurser.
* Under nätet och tjänsten API Management måste finnas i samma prenumeration.
* Ett undernät som innehåller API Management instanser kan inte flyttas mellan prenumerationer.
* För API Management distributioner i flera regioner som kon figurer ATS i internt virtuellt nätverks läge ansvarar användarna för att hantera belastnings utjämning över flera regioner, när de äger routningen.
* Anslutning från en resurs i ett globalt peer-nätverk i en annan region till API Management tjänsten i det interna läget fungerar inte på grund av en plattforms begränsning. Mer information finns i [resurser i ett virtuellt nätverk kan inte kommunicera med Azures interna belastningsutjämnare i peer-kopplat virtuellt nätverk](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)

## <a name="control-plane-ip-addresses"></a><a name="control-plane-ips"></a> Kontroll PLANENS IP-adresser

IP-adresserna delas av **Azure-miljön**. När du tillåter inkommande begär Anden IP-adress som marker ATS med **Global** måste tillåtas tillsammans med den **region** som är en speciell IP-adress.

| **Azure-miljö**|   **Region**|  **IP-adress**|
|-----------------|-------------------------|---------------|
| Azure, offentlig| Södra centrala USA (global)| 104.214.19.224|
| Azure, offentlig| Norra centrala USA (global)| 52.162.110.80|
| Azure, offentlig| USA, västra centrala| 52.253.135.58|
| Azure, offentlig| Sydkorea, centrala| 40.82.157.167|
| Azure, offentlig| Storbritannien, västra| 51.137.136.0|
| Azure, offentlig| Japan, västra| 40.81.185.8|
| Azure, offentlig| USA, norra centrala| 40.81.47.216|
| Azure, offentlig| Storbritannien, södra| 51.145.56.125|
| Azure, offentlig| Indien, västra| 40.81.89.24|
| Azure, offentlig| East US| 52.224.186.99|
| Azure, offentlig| Europa, västra| 51.145.179.78|
| Azure, offentlig| Japan, östra| 52.140.238.179|
| Azure, offentlig| Frankrike, centrala| 40.66.60.111|
| Azure, offentlig| Kanada, östra| 52.139.80.117|
| Azure, offentlig| Förenade Arabemiraten, norra| 20.46.144.85|
| Azure, offentlig| Brasilien, södra| 191.233.24.179|
| Azure, offentlig| Brasilien, sydöstra| 191.232.18.181|
| Azure, offentlig| Sydostasien| 40.90.185.46|
| Azure, offentlig| Sydafrika, norra| 102.133.130.197|
| Azure, offentlig| Kanada, centrala| 52.139.20.34|
| Azure, offentlig| Sydkorea, södra| 40.80.232.185|
| Azure, offentlig| Indien, centrala| 13.71.49.1|
| Azure, offentlig| USA, västra| 13.64.39.16|
| Azure, offentlig| Australien, sydöstra| 20.40.160.107|
| Azure, offentlig| Australien, centrala| 20.37.52.67|
| Azure, offentlig| Indien, södra| 20.44.33.246|
| Azure, offentlig| Central US| 13.86.102.66|
| Azure, offentlig| Australien, östra| 20.40.125.155|
| Azure, offentlig| USA, västra 2| 51.143.127.203|
| Azure, offentlig| USA, östra 2 EUAP| 52.253.229.253|
| Azure, offentlig| Centrala USA-EUAP| 52.253.159.160|
| Azure, offentlig| USA, södra centrala| 20.188.77.119|
| Azure, offentlig| USA, östra 2| 20.44.72.3|
| Azure, offentlig| Europa, norra| 52.142.95.35|
| Azure, offentlig| Asien, östra| 52.139.152.27|
| Azure, offentlig| Frankrike, södra| 20.39.80.2|
| Azure, offentlig| Schweiz, västra| 51.107.96.8|
| Azure, offentlig| Australien, centrala 2| 20.39.99.81|
| Azure, offentlig| Förenade Arabemiraten Central| 20.37.81.41|
| Azure, offentlig| Schweiz, norra| 51.107.0.91|
| Azure, offentlig| Sydafrika, västra| 102.133.0.79|
| Azure, offentlig| Tyskland, västra centrala| 51.116.96.0|
| Azure, offentlig| Tyskland, norra| 51.116.0.0|
| Azure, offentlig| Östra Norge| 51.120.2.185|
| Azure, offentlig| Norge, väst| 51.120.130.134|
| Azure Kina 21Vianet| Kina, norra (global)| 139.217.51.16|
| Azure Kina 21Vianet| Kina, östra (global)| 139.217.171.176|
| Azure Kina 21Vianet| Kina, norra| 40.125.137.220|
| Azure Kina 21Vianet| Kina, östra| 40.126.120.30|
| Azure Kina 21Vianet| Kina, norra 2| 40.73.41.178|
| Azure Kina 21Vianet| Kina, östra 2| 40.73.104.4|
| Azure Government| USGov Virginia (global)| 52.127.42.160|
| Azure Government| USGov Texas (global)| 52.127.34.192|
| Azure Government| USGov Virginia| 52.227.222.92|
| Azure Government| USGov Iowa| 13.73.72.21|
| Azure Government| USGov Arizona| 52.244.32.39|
| Azure Government| USGov Texas| 52.243.154.118|
| Azure Government| USDoD Central| 52.182.32.132|
| Azure Government| USDoD, öst| 52.181.32.192|

## <a name="related-content"></a><a name="related-content"> </a>Relaterat innehåll
* [Ansluta en Virtual Network till Server delen med VPN-gateway](../vpn-gateway/design.md#s2smulti)
* [Ansluta en Virtual Network från olika distributions modeller](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Så här använder du API-kontrollen för att spåra anrop i Azure API Management](api-management-howto-api-inspector.md)
* [Vanliga frågor och svar om Virtual Network](../virtual-network/virtual-networks-faq.md)
* [Tjänsttaggar](../virtual-network/network-security-groups-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/network-security-groups-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
