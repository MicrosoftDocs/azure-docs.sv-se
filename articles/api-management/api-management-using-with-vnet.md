---
title: Använda Azure API Management med virtuella nätverk
description: Lär dig hur du installation av en anslutning till ett virtuellt nätverk i Azure API Management få åtkomst till webbtjänster via den.
services: api-management
author: vladvino
ms.service: api-management
ms.topic: how-to
ms.date: 04/12/2021
ms.author: apimpm
ms.custom: references_regions
ms.openlocfilehash: 5612da51c1896aaa40ff2a0fb90e3343f676de43
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531620"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Använda Azure API Management med virtuella nätverk
Med virtuella Azure-nätverk (VNET) kan du placera valfria Azure-resurser i ett dirigerbart icke-Internetbaserat nätverk som du kontrollerar åtkomsten till. Dessa nätverk kan sedan anslutas till dina lokala nätverk med hjälp av olika VPN-tekniker. Om du vill veta mer om virtuella Azure-nätverk börjar du med informationen här: [Azure Virtual Network Overview](../virtual-network/virtual-networks-overview.md).

Azure API Management kan distribueras i det virtuella nätverket (VNET), så att det kan komma åt servertjänster i nätverket. Utvecklarportalen och API-gatewayen kan konfigureras för att vara tillgängliga antingen från Internet eller bara inom det virtuella nätverket.

> [!NOTE]
> URL:en för API-importdokumentet måste finnas på en offentligt tillgänglig Internet-adress.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Förutsättningar

Om du vill utföra stegen som beskrivs i den här artikeln måste du ha:

+ **En aktiv Azure-prenumeration.**

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **En API Management instans.** Mer information finns i Skapa [en Azure API Management-instans.](get-started-create-service-instance.md)

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

## <a name="enable-vnet-connection"></a><a name="enable-vpn"> </a>Aktivera VNET-anslutning

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Aktivera VNET-anslutning med hjälp av Azure Portal

1. Gå till Azure Portal [för](https://portal.azure.com) att hitta API Management-instansen. Sök efter och välj **API Management tjänster**.

1. Välj din API Management instans.

1. Välj **Virtuellt nätverk**.
1. Konfigurera API Management-instansen som ska distribueras i ett virtuellt nätverk.

    :::image type="content" source="media/api-management-using-with-vnet/api-management-menu-vnet.png" alt-text="Välj virtuellt nätverk i Azure Portal.":::
    
1. Välj önskad åtkomsttyp:

    * **Av**: Det här är standardinställningen. API Management distribueras inte till ett virtuellt nätverk.

    * **Externt:** API Management gatewayen och utvecklarportalen är tillgängliga från det offentliga Internet via en extern lastbalanserare. Gatewayen kan komma åt resurser i det virtuella nätverket.

        ![Offentlig peering][api-management-vnet-public]

    * **Internt:** API Management gatewayen och utvecklarportalen är endast tillgängliga inifrån det virtuella nätverket via en intern lastbalanserare. Gatewayen kan komma åt resurser i det virtuella nätverket.

        ![Privat peering][api-management-vnet-private]

1. Om du **har valt** Extern **eller** Intern visas en lista över alla platser (regioner) där API Management-tjänsten har etablerats. Välj en **plats** och välj sedan dess **virtuella nätverk,** **undernät** och **IP-adress.** Listan över virtuella nätverk fylls i med Resource Manager nätverk som är tillgängliga i dina Azure-prenumerationer som har konfigurerats i den region som du konfigurerar.


    :::image type="content" source="media/api-management-using-with-vnet/api-management-using-vnet-select.png" alt-text="Inställningar för virtuellt nätverk i portalen.":::

    > [!IMPORTANT]
    > * När klienten använder **API-version 2020-12-01** eller tidigare för att distribuera en Azure API Management-instans i ett Resource Manager VNET, måste tjänsten finnas i ett dedikerat undernät som inte innehåller några resurser förutom Azure API Management-instanser. Om ett försök görs att distribuera en Azure API Management-instans till ett Resource Manager VNET-undernät som innehåller andra resurser misslyckas distributionen.
    > * När klienten använder **API-version 2021-01-01-preview** eller senare för att distribuera en Azure API Management-instans i ett virtuellt nätverk stöds endast ett Resource Manager virtuellt nätverk. Dessutom kan det undernät som används innehålla andra resurser. Du behöver inte använda ett undernät som är dedikerat för API Management instanser. 

1. Välj **Använd**. Sidan **Virtuellt** nätverk för din API Management instans uppdateras med dina nya alternativ för virtuellt nätverk och undernät.

1. Fortsätt att konfigurera inställningar för virtuella nätverk för de återstående platserna API Management instansen.

7. I det övre navigeringsfältet väljer **du Spara** och sedan **Använd nätverkskonfiguration.**

    Det kan ta 15 till 45 minuter att uppdatera API Management instansen.

> [!NOTE]
> Med klienter som använder API-version 2020-12-01 och tidigare ändras VIP-adressen för API Management-instansen varje gång det virtuella nätverket aktiveras eller inaktiveras. VIP-adressen ändras också när API Management flyttas från **Externt till** **Internt** virtuellt nätverk eller tvärtom.

> [!IMPORTANT]
> Om du tar API Management från ett VNET eller ändrar det som det har distribuerats i kan det tidigare använda virtuella nätverket förbli låst i upp till sex timmar. Under den här perioden går det inte att ta bort det virtuella nätverket eller distribuera en ny resurs till det. Det här beteendet gäller för klienter som använder API-version 2018-01-01 och tidigare. Klienter som använder API-version 2019-01-01 och senare frigörs det virtuella nätverket så snart den associerade API Management-tjänsten tas bort.

### <a name="deploy-api-management-into-external-vnet"></a><a name="deploy-apim-external-vnet"> </a>Distribuera API Management till externt VNET

Du kan också aktivera virtuell nätverksanslutning med hjälp av följande metoder.

### <a name="api-version-2021-01-01-preview"></a>API-version 2021-01-01-preview

* Azure Resource Manager [mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-api-management-create-with-external-vnet-publicip)

     [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-external-vnet-publicip%2Fazuredeploy.json)

### <a name="api-version-2020-12-01"></a>API-version 2020-12-01

* Azure Resource Manager [mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-api-management-create-with-external-vnet)
    
     [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-external-vnet%2Fazuredeploy.json)

* Azure PowerShell cmdlets – [Skapa eller](/powershell/module/az.apimanagement/new-azapimanagement) [uppdatera en](/powershell/module/az.apimanagement/update-azapimanagementregion) API Management-instans i ett virtuellt nätverk

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a><a name="connect-vnet"> </a>Ansluta till en webbtjänst som finns i ett virtuellt nätverk
När din API Management är ansluten till det virtuella nätverket skiljer sig åtkomsten till backend-tjänster i den inte från åtkomsten till offentliga tjänster. Skriv bara in den lokala IP-adressen eller värdnamnet (om en DNS-server har konfigurerats för det virtuella nätverket) för webbtjänsten i fältet **Webbtjänst-URL** när du skapar ett nytt API eller redigerar ett befintligt.

![Lägg till API från VPN][api-management-setup-vpn-add-api]

## <a name="common-network-configuration-issues"></a><a name="network-configuration-issues"> </a>Vanliga problem med nätverkskonfiguration
Nedan följer en lista över vanliga felkonfigurationsproblem som kan uppstå när du distribuerar API Management-tjänsten till en Virtual Network.

* **Anpassad DNS-serverinstallation:** API Management är beroende av flera Azure-tjänster. När API Management värd i ett VNET med en anpassad DNS-server måste den matcha värdnamnen för dessa Azure-tjänster. Följ den [här vägledningen](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) om anpassad DNS-konfiguration. Se porttabellen nedan och andra nätverkskrav som referens.

> [!IMPORTANT]
> Om du planerar att använda en anpassad DNS-server för det  virtuella nätverket bör du konfigurera den innan du distribuerar en API Management-tjänst till den. Annars måste du uppdatera API Management varje gång du ändrar DNS-servrarna genom att köra [åtgärden Tillämpa nätverkskonfiguration](/rest/api/apimanagement/2019-12-01/apimanagementservice/applynetworkconfigurationupdates)

* **Portar som krävs API Management:** Inkommande och utgående trafik till undernätet där API Management distribueras kan styras med hjälp av [nätverkssäkerhetsgruppen][Network Security Group]. Om någon av dessa portar inte är API Management kanske inte fungerar korrekt och kan bli otillgänglig. Att blockera en eller flera av dessa portar är ett annat vanligt felkonfigurationsproblem när du använder API Management med ett VNET.

<a name="required-ports"></a> När en API Management-tjänstinstans finns i ett VNET används portarna i följande tabell.

| Käll-/målport(er) | Riktning          | Transportprotokoll |   [Tjänsttaggar](../virtual-network/network-security-groups-overview.md#service-tags) <br> Källa/mål   | Syfte ( \* )                                                 | Virtual Network typ |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80], 443                  | Inkommande            | TCP                | INTERNET/VIRTUAL_NETWORK            | Klientkommunikation till API Management                      | Extern             |
| * / 3443                     | Inkommande            | TCP                | ApiManagement/VIRTUAL_NETWORK       | Hanteringsslutpunkt för Azure Portal och PowerShell         | Extern & internt  |
| * / 443                  | Utgående           | TCP                | VIRTUAL_NETWORK/Lagring             | **Beroende av Azure Storage**                             | Extern & internt  |
| * / 443                  | Utgående           | TCP                | VIRTUAL_NETWORK/AzureActiveDirectory | [Azure Active Directory och](api-management-howto-aad.md) Azure KeyVault-beroende                  | Extern & internt  |
| * / 1433                     | Utgående           | TCP                | VIRTUAL_NETWORK/SQL                 | **Åtkomst till Azure SQL slutpunkter**                           | Extern & internt  |
| * / 443                     | Utgående           | TCP                | VIRTUAL_NETWORK/AzureKeyVault                 | **Åtkomst till Azure KeyVault**                           | Extern & internt  |
| * / 5671, 5672, 443          | Utgående           | TCP                | VIRTUAL_NETWORK/EventHub            | Beroende för [logga till händelsehubbprincip och](api-management-howto-log-event-hubs.md) övervakningsagent | Extern & internt  |
| * / 445                      | Utgående           | TCP                | VIRTUAL_NETWORK/Lagring             | Beroende av Azure-filresurs för [GIT](api-management-configuration-repository-git.md)                      | Extern & internt  |
| * / 443, 12000                     | Utgående           | TCP                | VIRTUAL_NETWORK/AzureCloud            | Tillägg för hälsa och övervakning         | Extern & internt  |
| * / 1886, 443                     | Utgående           | TCP                | VIRTUAL_NETWORK/AzureMonitor         | Publicera [diagnostikloggar och mått](api-management-howto-use-azure-monitor.md), [Resource Health](../service-health/resource-health-overview.md) och [Application Insights](api-management-howto-app-insights.md)                   | Extern & internt  |
| * / 25, 587, 25028                       | Utgående           | TCP                | VIRTUAL_NETWORK/INTERNET            | Ansluta till SMTP Relay för att skicka e-post                    | Extern & internt  |
| * / 6381 - 6383              | Inkommande & utgående | TCP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | Få åtkomst till Redis-tjänsten [för cacheprinciper](api-management-caching-policies.md) mellan datorer         | Extern & internt  |
| * / 4290              | Inkommande & utgående | UDP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | Synkroniseringsräknare för [principer för hastighetsbegränsning](api-management-access-restriction-policies.md#LimitCallRateByKey) mellan datorer         | Extern & internt  |
| * / *                        | Inkommande            | TCP                | AZURE_LOAD_BALANCER/VIRTUAL_NETWORK | Azure Infrastructure Load Balancer                          | Extern & internt  |

>[!IMPORTANT]
> Portarna med *fetstil* **krävs** för att API Management distribueras. Om de andra portarna blockeras **försämras** dock möjligheten att använda och övervaka den tjänst som körs **och tillhandahålla det intjänade serviceavtalet**.

+ **TLS-funktioner:** För att kunna skapa och validera TLS/SSL-certifikatkedjan behöver API Management-tjänsten utgående nätverksanslutning till ocsp.msocsp.com, mscrl.microsoft.com och crl.microsoft.com. Det här beroendet krävs inte, om något certifikat som du överför till API Management innehåller den fullständiga kedjan till CA-roten.

+ **DNS-åtkomst:** Utgående åtkomst på port 53 krävs för kommunikation med DNS-servrar. Om det finns en anpassad DNS-server i den andra änden av en VPN-gateway måste DNS-servern kunna nås från det undernät som är API Management.

+ **Mått och hälsoövervakning:** Utgående nätverksanslutning till Azure Monitoring-slutpunkter, som löses under följande domäner. Som du ser i tabellen visas dessa URL:er under tjänsttaggen AzureMonitor för användning med nätverkssäkerhetsgrupper.

    | Azure-miljö | Slutpunkter                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure, offentlig      | <ul><li>gcs.prod.monitoring.core.windows.net(**ny**)</li><li>global.prod.microsoftmetrics.com(**ny**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com(**ny**)</li><li>prod3-black.prod.microsoftmetrics.com(**ny**)</li><li>prod3-red.prod.microsoftmetrics.com(**ny**)</li><li>gcs.prod.warm.ingestion.monitoring.azure.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>global.prod.microsoftmetrics.com(**ny**)</li><li>shoebox2.prod.microsoftmetrics.com(**ny**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com(**ny**)</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.us</li></ul>                                                                                                                                                                                                                                                |
    | Azure Kina 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>global.prod.microsoftmetrics.com(**ny**)</li><li>shoebox2.prod.microsoftmetrics.com(**ny**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com(**ny**)</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.cn</li></ul>                                                                                                                                                                                                                                                |

  >[!IMPORTANT]
  > Ändringen av kluster ovan med DNS-zonen **.nsatc.net** **till .microsoftmetrics.com** är främst en DNS-ändring. IP-adressen för klustret ändras inte.

+ **Regionala tjänsttaggar:** NSG-regler som tillåter utgående anslutning till lagrings-, SQL- och Event Hubs-tjänsttaggar kan använda de regionala versionerna av dessa taggar som motsvarar den region som innehåller API Management-instansen (till exempel Storage.WestUS för en API Management-instans i regionen USA, västra). I distributioner i flera regioner ska nätverksnivånivågiggen i varje region tillåta trafik till tjänsttaggarna för den regionen och den primära regionen.

    > [!IMPORTANT]
    > Om du vill aktivera publicering [av utvecklarportalen](api-management-howto-developer-portal.md) för en API Management-instans i ett virtuellt nätverk ser du till att du även tillåter utgående anslutning till bloblagring i regionen USA, västra. Använd till exempel **tjänsttaggen Storage.WestUS** i en NSG-regel. Anslutning till bloblagring i regionen USA, västra krävs för närvarande för att publicera utvecklarportalen för alla API Management instans.

+ **SMTP-relä:** Utgående nätverksanslutning för SMTP-reläet, som matchas under värden `smtpi-co1.msn.com` , , `smtpi-ch1.msn.com` `smtpi-db3.msn.com` `smtpi-sin.msn.com` och `ies.global.microsoft.com`

+ **Utvecklarportalens CAPTCHA:** Utgående nätverksanslutning för utvecklarportalens CAPTCHA, som matchas under värdarna `client.hip.live.com` och `partner.hip.live.com` .

+ **Azure Portal Diagnostik:** Om du vill aktivera flödet av diagnostikloggar från Azure Portal när du använder API Management-tillägget inifrån en Virtual Network krävs utgående åtkomst till på `dc.services.visualstudio.com` port 443. Detta hjälper till vid felsökning av problem som kan hända när du använder tillägget.

+ **Azure Load Balancer:** Att tillåta inkommande förfrågningar från tjänsttaggen är inte ett krav för `AZURE_LOAD_BALANCER` SKU:n, eftersom vi bara distribuerar en `Developer` beräkningsenhet bakom den. Men Inkommande från [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) blir kritiskt vid skalning till högre SKU som , eftersom fel i hälsoavsökningen från Load Balancer misslyckas med `Premium` en distribution.

+ **Application Insights:** Om [Azure Application Insights-övervakning](api-management-howto-app-insights.md) är aktiverat på API Management måste vi tillåta utgående anslutning till [telemetrislutpunkten](../azure-monitor/app/ip-addresses.md#outgoing-ports) från Virtual Network. 

+ Tvinga **tunneltrafik** till lokal brandvägg med Express Route eller virtuell nätverksinstallation: En vanlig kundkonfiguration är att definiera sin egen standardväg (0.0.0.0/0) som tvingar all trafik från det API Management-delegerade undernätet att flöda genom en lokal brandvägg eller till en virtuell nätverksinstallation. Det här trafikflödet bryter alltid anslutningen till Azure API Management eftersom den utgående trafiken antingen blockeras lokalt eller nat'd till en oigenkännlig uppsättning adresser som inte längre fungerar med olika Azure-slutpunkter. Lösningen kräver att du gör några saker:

  * Aktivera tjänstslutpunkter i det undernät där API Management-tjänsten har distribuerats. [Tjänstslutpunkter][ServiceEndpoints] måste aktiveras för Azure Sql, Azure Storage, Azure EventHub och Azure ServiceBus. Genom att aktivera slutpunkter direkt API Management delegerade undernät till dessa tjänster kan de använda Microsoft Azure stamnätverk som ger optimal routning för tjänsttrafik. Om du använder tjänstslutpunkter med en tvingad tunneltrafik i API Management tvingas inte ovanstående Azure-tjänsttrafik att tvingad tunneltrafik. Den andra API Management tjänstberoende trafik tvingas tunneltrafik och kan inte gå förlorad eller så API Management tjänsten inte fungera korrekt.
    
  * All kontrollplanstrafik från Internet till hanteringsslutpunkten för din API Management-tjänst dirigeras via en specifik uppsättning inkommande IP-adresser som API Management. När trafiken tvingad tunneltrafik mappas svaren inte symmetriskt tillbaka till dessa inkommande käll-IP-adresser. För att lösa begränsningen måste vi lägga till följande användardefinierade vägar[(UDR: er)][UDRs]för att styra trafiken tillbaka till Azure genom att ange målet för dessa värdvägar till "Internet". Uppsättningen inkommande IP-adresser för kontrollplanstrafik är dokumenterade [IP-adresser för kontrollplanet](#control-plane-ips)

  * För andra API Management tjänstberoenden som är tvingad tunneltrafik, bör det finnas ett sätt att matcha värdnamnet och kontakta slutpunkten. Dessa inkluderar
      - Mått och hälsoövervakning
      - Azure Portal diagnostik
      - SMTP-relä
      - CAPTCHA för utvecklarportalen

## <a name="troubleshooting"></a><a name="troubleshooting"> </a>Felsökning
* **Inledande installation:** När den första distributionen av API Management-tjänsten till ett undernät inte lyckas, rekommenderar vi att du först distribuerar en virtuell dator till samma undernät. Nästa fjärrskrivbord till den virtuella datorn och kontrollera att det finns en anslutning till en av varje resurs nedan i din Azure-prenumeration
    * Azure Storage blob
    * Azure SQL Database
    * Azure Storage tabell

  > [!IMPORTANT]
  > När du har verifierat anslutningen måste du ta bort alla resurser som distribuerats i undernätet innan du distribuerar API Management till undernätet.

* **Kontrollera status för nätverksanslutningen:** När du API Management till undernätet använder du portalen för att kontrollera anslutningen för din instans till beroenden, till exempel Azure Storage. I portalen går du till menyn till vänster och väljer Nätverksanslutningsstatus under **Distribution och infrastruktur.**

   :::image type="content" source="media/api-management-using-with-vnet/verify-network-connectivity-status.png" alt-text="Kontrollera statusen för nätverksanslutningen i portalen":::

    * Välj **Krävs** för att granska anslutningen till de Azure-tjänster som krävs för API Management. Ett fel indikerar att instansen inte kan utföra kärnåtgärder för att hantera API:er.
    * Välj **Valfritt** för att granska anslutningen till valfria tjänster. Eventuella fel indikerar bara att den specifika funktionen inte fungerar (till exempel SMTP). Ett fel kan leda till försämring i möjligheten att använda och övervaka API Management instansen och tillhandahålla det indelat serviceavtalet.

Om du vill åtgärda anslutningsproblem kan du [läsa Vanliga problem med nätverkskonfigurationen](#network-configuration-issues) och åtgärda nödvändiga nätverksinställningar.

* **Inkrementella** uppdateringar: När du gör ändringar i nätverket, se [NetworkStatus API](/rest/api/apimanagement/2019-12-01/networkstatus)för att kontrollera att API Management-tjänsten inte har förlorat åtkomst till någon av de kritiska resurserna, som den är beroende av. Anslutningsstatusen bör uppdateras var 15:e minut.

* **Resursnavigeringslänkar:** När du distribuerar till Resource Manager vnet-undernät reserverar API Management undernätet genom att skapa en resursnavigeringslänk. Om undernätet redan innehåller en resurs från en annan provider misslyckas **distributionen.** När du flyttar en tjänst API Management ett annat undernät eller tar bort den, tar vi på samma sätt bort den resursnavigeringslänken.

## <a name="subnet-size-requirement"></a><a name="subnet-size"></a> Storlekskrav för undernät
Azure reserverar vissa IP-adresser inom varje undernät, och dessa adresser kan inte användas. De första och sista IP-adresserna för undernäten är reserverade för protokollöverensstämmelse, tillsammans med ytterligare tre adresser som används för Azure-tjänster. Mer information finns i [Finns det några begränsningar för användning av IP-adresser i dessa undernät?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Förutom de IP-adresser som används av Azure VNET-infrastrukturen använder varje Api Management-instans i undernätet två IP-adresser per premium-SKU eller en IP-adress för developer-SKU:n. Varje instans reserverar ytterligare en IP-adress för den externa lastbalanseraren. När du distribuerar till internt virtuellt nätverk krävs ytterligare en IP-adress för den interna lastbalanseraren.

Med tanke på beräkningen över den minsta storleken på undernätet, där API Management kan distribueras är /29 som ger tre användbara IP-adresser.

Varje ytterligare skalningsenhet för API Management kräver ytterligare två IP-adresser.

## <a name="routing"></a><a name="routing"></a> Routning
+ En belastningsutjämnad offentlig IP-adress (VIP) reserveras för att ge åtkomst till alla tjänstslutpunkter.
+ En IP-adress från ett undernäts IP-intervall (DIP) används för att komma åt resurser i det virtuella nätverket och en offentlig IP-adress (VIP) används för att komma åt resurser utanför det virtuella nätverket.
+ Belastningsutjämnad offentlig IP-adress finns på bladet Översikt/Essentials i Azure Portal.

## <a name="limitations"></a><a name="limitations"> </a>Begränsningar
* För klienter som använder API-version 2020-12-01 och tidigare får ett undernät som innehåller API Management-instanser inte innehålla några andra Azure-resurstyper.
* Undernätet och API Management måste finnas i samma prenumeration.
* Ett undernät som API Management instanser kan inte flyttas mellan prenumerationer.
* För distributioner API Management flera regioner som konfigurerats i internt virtuellt nätverksläge ansvarar användarna för att hantera belastningsutjämningen över flera regioner, eftersom de äger routningen.
* Anslutning från en resurs i ett globalt peer-anslutet VNET i en annan region till API Management-tjänst i internt läge fungerar inte på grund av plattformsbegränsningar. Mer information finns i Resurser [i ett virtuellt nätverk kan inte kommunicera med azures interna lastbalanserare i peer-erade virtuella nätverk](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)

## <a name="control-plane-ip-addresses"></a><a name="control-plane-ips"></a> IP-adresser för kontrollplanet

IP-adresserna divideras med **Azure Environment**. När du tillåter inkommande begäranden måste IP-adress som **är markerad** med Global tillåtas tillsammans med **den regionspecifika** IP-adressen.

| **Azure-miljö**|   **Region**|  **IP-adress**|
|-----------------|-------------------------|---------------|
| Azure, offentlig| USA, södra centrala (global)| 104.214.19.224|
| Azure, offentlig| USA, norra centrala (global)| 52.162.110.80|
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
| Azure, offentlig| USA, östra 2 (EUAP)| 52.253.229.253|
| Azure, offentlig| USA, centrala (EUAP)| 52.253.159.160|
| Azure, offentlig| USA, södra centrala| 20.188.77.119|
| Azure, offentlig| USA, östra 2| 20.44.72.3|
| Azure, offentlig| Europa, norra| 52.142.95.35|
| Azure, offentlig| Asien, östra| 52.139.152.27|
| Azure, offentlig| Frankrike, södra| 20.39.80.2|
| Azure, offentlig| Schweiz, västra| 51.107.96.8|
| Azure, offentlig| Australien, centrala 2| 20.39.99.81|
| Azure, offentlig| Förenade Arabemiraten, centrala| 20.37.81.41|
| Azure, offentlig| Schweiz, norra| 51.107.0.91|
| Azure, offentlig| Sydafrika, västra| 102.133.0.79|
| Azure, offentlig| Tyskland, västra centrala| 51.116.96.0|
| Azure, offentlig| Tyskland, norra| 51.116.0.0|
| Azure, offentlig| Mellanöstern| 51.120.2.185|
| Azure, offentlig| Tyskland, västra| 51.120.130.134|
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
| Azure Government| USDoD, östra| 52.181.32.192|

## <a name="related-content"></a><a name="related-content"> </a>Relaterat innehåll
* [Ansluta en Virtual Network till en backend med vpn-gateway](../vpn-gateway/design.md#s2smulti)
* [Ansluta en Virtual Network från olika distributionsmodeller](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Så här använder du API Inspector för att spåra anrop i Azure API Management](api-management-howto-api-inspector.md)
* [Virtual Network vanliga frågor och svar](../virtual-network/virtual-networks-faq.md)
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
