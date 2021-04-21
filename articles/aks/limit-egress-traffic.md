---
title: Begränsa utgående trafik i Azure Kubernetes Service (AKS)
description: Lär dig vilka portar och adresser som krävs för att styra utgående trafik i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.author: jpalma
ms.date: 01/12/2021
author: palma21
ms.openlocfilehash: 39c0877b96a3e8c6c716c1ab9ae7ba11575990a0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765601"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Kontrollera utgående trafik för klusternoder i Azure Kubernetes Service (AKS)

Den här artikeln innehåller nödvändig information som gör att du kan skydda utgående trafik från din Azure Kubernetes Service (AKS). Den innehåller klusterkraven för en grundläggande AKS-distribution och ytterligare krav för valfria tillägg och funktioner. [I slutet ges ett exempel på hur du konfigurerar dessa krav med Azure Firewall](#restrict-egress-traffic-using-azure-firewall). Du kan dock använda den här informationen för alla metoder eller apparat för utgående begränsning.

## <a name="background"></a>Bakgrund

AKS-kluster distribueras i ett virtuellt nätverk. Det här nätverket kan hanteras (skapas av AKS) eller anpassat (förkonfigurerat av användaren i förväg). I båda fallen har klustret **utgående** beroenden av tjänster utanför det virtuella nätverket (tjänsten har inga inkommande beroenden).

För hantering och drift behöver noder i ett AKS-kluster komma åt vissa portar och fullständigt kvalificerade domännamn (FQDN). Dessa slutpunkter krävs för att noderna ska kunna kommunicera med API-servern eller för att ladda ned och installera kärnkomponenter för Kubernetes-kluster och nodsäkerhetsuppdateringar. Klustret måste till exempel hämta containeravbildningar för bassystem från Microsoft Container Registry (MCR).

De utgående AKS-beroendena är nästan helt definierade med FQDN, som inte har statiska adresser bakom sig. Bristen på statiska adresser innebär att nätverkssäkerhetsgrupper inte kan användas för att låsa utgående trafik från ett AKS-kluster. 

Som standard har AKS-kluster obegränsad utgående (utgående) Internetåtkomst. Med den här nivån av nätverksåtkomst kan noder och tjänster som du kör komma åt externa resurser efter behov. Om du vill begränsa utgående trafik måste ett begränsat antal portar och adresser vara tillgängliga för att underhålla felfria klusterunderhållsuppgifter. Den enklaste lösningen för att skydda utgående adresser är att använda en brandväggsenhet som kan styra utgående trafik baserat på domännamn. Azure Firewall kan till exempel begränsa utgående HTTP- och HTTPS-trafik baserat på målets FQDN. Du kan också konfigurera dina önskade brandväggs- och säkerhetsregler för att tillåta dessa portar och adresser som krävs.

> [!IMPORTANT]
> Det här dokumentet beskriver bara hur du låser den trafik som lämnar AKS-undernätet. AKS har inga krav på ingress som standard.  Det **finns inte stöd för att** blockera intern undernätstrafik med nätverkssäkerhetsgrupper (NSG:er) och brandväggar. Om du vill styra och blockera trafiken i klustret använder du [**_Nätverksprinciper_**][network-policy].

## <a name="required-outbound-network-rules-and-fqdns-for-aks-clusters"></a>Nödvändiga regler för utgående nätverk och FQDN för AKS-kluster

Följande nätverk och FQDN/programregler krävs för ett AKS-kluster. Du kan använda dem om du vill konfigurera en annan lösning än Azure Firewall.

* IP-adressberoenden är för icke-HTTP/S-trafik (både TCP- och UDP-trafik)
* FQDN HTTP/HTTPS-slutpunkter kan placeras i brandväggsenheten.
* JOKERTECKEN HTTP/HTTPS-slutpunkter är beroenden som kan variera beroende på ditt AKS-kluster baserat på ett antal kvalificerare.
* AKS använder en antagningskontrollant för att mata in det FQDN som en miljövariabel till alla distributioner under kube-system och gatekeeper-system, som säkerställer att all systemkommunikation mellan noder och API-servern använder API-serverns FQDN och inte API-serverns IP-adress. 
* Om du har en app eller lösning som behöver kommunicera  med API-servern måste du lägga till ytterligare en nätverksregel för att tillåta TCP-kommunikation till *port 443 på API-serverns IP-adress.*
* I sällsynta fall kan API-serverns IP-adress ändras om det finns en underhållsåtgärd. Planerade underhållsåtgärder som kan ändra API-serverns IP-adress förmedlas alltid i förväg.


### <a name="azure-global-required-network-rules"></a>Globala nätverksregler som krävs för Azure

De nödvändiga nätverksreglerna och IP-adressberoendena är:

| Målslutpunkt                                                             | Protokoll | Port    | Användning  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Eller* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Eller* <br/> [Regionala CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Eller* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | För säker tunnelkommunikation mellan noderna och kontrollplanet. Detta krävs inte för [privata kluster](private-clusters.md)|
| **`*:9000`** <br/> *Eller* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Eller* <br/> [Regionala CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Eller* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | För säker tunnelkommunikation mellan noderna och kontrollplanet. Detta krävs inte för [privata kluster](private-clusters.md) |
| **`*:123`** eller **`ntp.ubuntu.com:123`** (om du Azure Firewall nätverksregler)  | UDP      | 123     | Krävs för tidssynkronisering med NETWORK Time Protocol (NTP) på Linux-noder.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Om du använder anpassade DNS-servrar måste du se till att de är tillgängliga för klusternoderna. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Krävs om du kör poddar/distributioner som har åtkomst till API-servern. Dessa poddar/distributioner använder API-IP-adressen. Detta krävs inte för [privata kluster](private-clusters.md)  |

### <a name="azure-global-required-fqdn--application-rules"></a>Azure Globala obligatoriska FQDN/programregler 

Följande FQDN/programregler krävs:

| Mål-FQDN                 | Port            | Användning      |
|----------------------------------|-----------------|----------|
| **`*.hcp.<location>.azmk8s.io`** | **`HTTPS:443`** | Krävs för Node <-> API-serverkommunikation. Ersätt *\<location\>* med den region där ditt AKS-kluster distribueras. |
| **`mcr.microsoft.com`**          | **`HTTPS:443`** | Krävs för att komma åt avbildningar Microsoft Container Registry (MCR). Det här registret innehåller bilder/diagram från första part (till exempel coreDNS osv.). Dessa avbildningar krävs för att klustret ska kunna skapas och fungera korrekt, inklusive skalning och uppgradering.  |
| **`*.data.mcr.microsoft.com`**   | **`HTTPS:443`** | Krävs för MCR-lagring som backas upp av Azures nätverk för innehållsleverans (CDN). |
| **`management.azure.com`**       | **`HTTPS:443`** | Krävs för Kubernetes-åtgärder mot Azure-API:et. |
| **`login.microsoftonline.com`**  | **`HTTPS:443`** | Krävs för Azure Active Directory autentisering. |
| **`packages.microsoft.com`**     | **`HTTPS:443`** | Den här adressen är lagringsplatsen för Microsoft-paket som används för *cachelagrade apt-get-åtgärder.*  Exempelpaket är Moby, PowerShell och Azure CLI. |
| **`acs-mirror.azureedge.net`**   | **`HTTPS:443`** | Den här adressen är till för den lagringsplats som krävs för att ladda ned och installera nödvändiga binärfiler som kubenet och Azure CNI. |

### <a name="azure-china-21vianet-required-network-rules"></a>Azure China 21Vianet nödvändiga nätverksregler

De nödvändiga nätverksreglerna och IP-adressberoendena är:

| Målslutpunkt                                                             | Protokoll | Port    | Användning  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Eller* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.Region:1194`** <br/> *Eller* <br/> [Regionala CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Eller* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | För säker tunnelkommunikation mellan noderna och kontrollplanet. |
| **`*:9000`** <br/> *Eller* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Eller* <br/> [Regionala CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Eller* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | För säker tunnelkommunikation mellan noderna och kontrollplanet. |
| **`*:22`** <br/> *Eller* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:22`** <br/> *Eller* <br/> [Regionala CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:22`** <br/> *Eller* <br/> **`APIServerPublicIP:22`** `(only known after cluster creation)`  | TCP           | 22      | För säker tunnelkommunikation mellan noderna och kontrollplanet. |
| **`*:123`** eller **`ntp.ubuntu.com:123`** (om du Azure Firewall nätverksregler)  | UDP      | 123     | Krävs för tidssynkronisering med NETWORK Time Protocol (NTP) på Linux-noder.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Om du använder anpassade DNS-servrar måste du se till att de är tillgängliga för klusternoderna. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Krävs om du kör poddar/distributioner som har åtkomst till API-servern. Dessa poddar/distributioner använder API-IP-adressen.  |

### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure China 21Vianet krävs FQDN/programregler

Följande FQDN/programregler krävs:

| Mål-FQDN                               | Port            | Användning      |
|------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Krävs för Node <-> API-serverkommunikation. Ersätt *\<location\>* med den region där ditt AKS-kluster distribueras. |
| **`*.tun.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Krävs för Node <-> API-serverkommunikation. Ersätt *\<location\>* med den region där ditt AKS-kluster distribueras. |
| **`mcr.microsoft.com`**                        | **`HTTPS:443`** | Krävs för att komma åt avbildningar Microsoft Container Registry (MCR). Det här registret innehåller bilder/diagram från första part (till exempel coreDNS osv.). Dessa avbildningar krävs för att klustret ska kunna skapas och fungera korrekt, inklusive åtgärder för skalning och uppgradering. |
| **`.data.mcr.microsoft.com`**                  | **`HTTPS:443`** | Krävs för MCR-lagring som backas upp av Azure Content Delivery Network (CDN). |
| **`management.chinacloudapi.cn`**              | **`HTTPS:443`** | Krävs för Kubernetes-åtgärder mot Azure-API:et. |
| **`login.chinacloudapi.cn`**                   | **`HTTPS:443`** | Krävs för Azure Active Directory autentisering. |
| **`packages.microsoft.com`**                   | **`HTTPS:443`** | Den här adressen är lagringsplatsen för Microsoft-paket som används för *cachelagrade apt-get-åtgärder.*  Exempelpaket är Moby, PowerShell och Azure CLI. |
| **`*.azk8s.cn`**                               | **`HTTPS:443`** | Den här adressen är till för den lagringsplats som krävs för att ladda ned och installera nödvändiga binärfiler som kubenet och Azure CNI. |

### <a name="azure-us-government-required-network-rules"></a>Nätverksregler som krävs för Azure US Government

De nödvändiga nätverksreglerna och IP-adressberoendena är:

| Målslutpunkt                                                             | Protokoll | Port    | Användning  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Eller* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Eller* <br/> [Regionala CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Eller* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | För säker tunnelkommunikation mellan noderna och kontrollplanet. |
| **`*:9000`** <br/> *Eller* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Eller* <br/> [Regionala CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Eller* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | För säker tunnelkommunikation mellan noderna och kontrollplanet. |
| **`*:123`** eller **`ntp.ubuntu.com:123`** (om du Azure Firewall nätverksregler)  | UDP      | 123     | Krävs för tidssynkronisering med NETWORK Time Protocol (NTP) på Linux-noder.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Om du använder anpassade DNS-servrar måste du se till att de är tillgängliga för klusternoderna. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Krävs om du kör poddar/distributioner som har åtkomst till API-servern. Dessa poddar/distributioner använder API-IP-adressen.  |

### <a name="azure-us-government-required-fqdn--application-rules"></a>Azure US Government kräver FQDN/programregler 

Följande FQDN/programregler krävs:

| Mål-FQDN                                        | Port            | Användning      |
|---------------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.aks.containerservice.azure.us`** | **`HTTPS:443`** | Krävs för Node <-> API-serverkommunikation. Ersätt *\<location\>* med den region där ditt AKS-kluster distribueras.|
| **`mcr.microsoft.com`**                                 | **`HTTPS:443`** | Krävs för att komma åt avbildningar Microsoft Container Registry (MCR). Det här registret innehåller bilder/diagram från första part (till exempel coreDNS osv.). Dessa avbildningar krävs för att klustret ska kunna skapas och fungera korrekt, inklusive skalning och uppgradering. |
| **`*.data.mcr.microsoft.com`**                          | **`HTTPS:443`** | Krävs för MCR-lagring som backas upp av Azures nätverk för innehållsleverans (CDN). |
| **`management.usgovcloudapi.net`**                      | **`HTTPS:443`** | Krävs för Kubernetes-åtgärder mot Azure-API:et. |
| **`login.microsoftonline.us`**                          | **`HTTPS:443`** | Krävs för Azure Active Directory autentisering. |
| **`packages.microsoft.com`**                            | **`HTTPS:443`** | Den här adressen är lagringsplatsen för Microsoft-paket som används för *cachelagrade apt-get-åtgärder.*  Exempelpaket är Moby, PowerShell och Azure CLI. |
| **`acs-mirror.azureedge.net`**                          | **`HTTPS:443`** | Den här adressen är till för den lagringsplats som krävs för att installera nödvändiga binärfiler som kubenet och Azure CNI. |

## <a name="optional-recommended-fqdn--application-rules-for-aks-clusters"></a>Valfria rekommenderade FQDN/programregler för AKS-kluster

Följande FQDN/programregler är valfria men rekommenderas för AKS-kluster:

| Mål-FQDN                                                               | Port          | Användning      |
|--------------------------------------------------------------------------------|---------------|----------|
| **`security.ubuntu.com`, `azure.archive.ubuntu.com`, `changelogs.ubuntu.com`** | **`HTTP:80`** | Med den här adressen kan Linux-klusternoderna ladda ned nödvändiga säkerhetskorrigeringar och uppdateringar. |

Om du väljer att blockera/inte tillåta dessa FQDN får noderna [](node-image-upgrade.md) endast os-uppdateringar när du uppgraderar en nodavbildning eller [uppgraderar klustret.](upgrade-cluster.md)

## <a name="gpu-enabled-aks-clusters"></a>GPU-aktiverade AKS-kluster

### <a name="required-fqdn--application-rules"></a>Obligatoriskt FQDN/programregler

Följande FQDN/programregler krävs för AKS-kluster som har GPU aktiverat:

| Mål-FQDN                        | Port      | Användning      |
|-----------------------------------------|-----------|----------|
| **`nvidia.github.io`**                  | **`HTTPS:443`** | Den här adressen används för korrekt drivrutinsinstallation och -åtgärd på GPU-baserade noder. |
| **`us.download.nvidia.com`**            | **`HTTPS:443`** | Den här adressen används för korrekt drivrutinsinstallation och -åtgärd på GPU-baserade noder. |
| **`apt.dockerproject.org`**             | **`HTTPS:443`** | Den här adressen används för korrekt drivrutinsinstallation och -åtgärd på GPU-baserade noder. |

## <a name="windows-server-based-node-pools"></a>Windows Server-baserade nodpooler 

### <a name="required-fqdn--application-rules"></a>Obligatoriskt FQDN/programregler

Följande FQDN/programregler krävs för att använda Windows Server-baserade nodpooler:

| Mål-FQDN                                                           | Port      | Användning      |
|----------------------------------------------------------------------------|-----------|----------|
| **`onegetcdn.azureedge.net, go.microsoft.com`**                            | **`HTTPS:443`** | Installera Windows-relaterade binärfiler |
| **`*.mp.microsoft.com, www.msftconnecttest.com, ctldl.windowsupdate.com`** | **`HTTP:80`**   | Installera Windows-relaterade binärfiler |

## <a name="aks-addons-and-integrations"></a>AKS-tillägg och integreringar

### <a name="azure-monitor-for-containers"></a>Azure Monitor för containrar

Det finns två alternativ för att ge åtkomst till Azure Monitor för containrar. Du kan tillåta Azure Monitor [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags)  eller ge åtkomst till de FQDN/programregler som krävs.

#### <a name="required-network-rules"></a>Nätverksregler som krävs

Följande FQDN/programregler krävs:

| Målslutpunkt                                                             | Protokoll | Port    | Användning  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureMonitor:443`**  | TCP           | 443      | Den här slutpunkten används för att skicka måttdata och loggar till Azure Monitor och Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Obligatoriskt FQDN/programregler

Följande FQDN/programregler krävs för AKS-kluster som har Azure Monitor för containrar aktiverade:

| FQDN                                    | Port      | Användning      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | **`HTTPS:443`**    | Den här slutpunkten används för mått och övervakning av telemetri med hjälp av Azure Monitor. |
| *.ods.opinsights.azure.com    | **`HTTPS:443`**    | Den här slutpunkten används av Azure Monitor för att mata in Log Analytics-data. |
| *.oms.opinsights.azure.com | **`HTTPS:443`** | Den här slutpunkten används av omsagent, som används för att autentisera Log Analytics-tjänsten. |
| *.monitoring.azure.com | **`HTTPS:443`** | Den här slutpunkten används för att skicka måttdata till Azure Monitor. |

### <a name="azure-dev-spaces"></a>Azure Dev Spaces

Uppdatera brandväggen eller säkerhetskonfigurationen för att tillåta nätverkstrafik till och från alla FQDN nedan och [Infrastrukturtjänster för Azure Dev Spaces.][dev-spaces-service-tags]

#### <a name="required-network-rules"></a>Nätverksregler som krävs

| Målslutpunkt                                                             | Protokoll | Port    | Användning  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureDevSpaces`**  | TCP           | 443      | Den här slutpunkten används för att skicka måttdata och loggar till Azure Monitor och Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Obligatoriskt FQDN/programregler 

Följande FQDN/programregler krävs för AKS-kluster som har Azure Dev Spaces aktiverat:

| FQDN                                    | Port      | Användning      |
|-----------------------------------------|-----------|----------|
| `cloudflare.docker.com` | **`HTTPS:443`** | Den här adressen används för att hämta Linux Alpine och andra Azure Dev Spaces-avbildningar |
| `gcr.io` | **`HTTPS:443`** | Den här adressen används för att hämta helm/tiller-avbildningar |
| `storage.googleapis.com` | **`HTTPS:443`** | Den här adressen används för att hämta helm/tiller-avbildningar |


### <a name="azure-policy"></a>Azure Policy

#### <a name="required-fqdn--application-rules"></a>Obligatoriskt FQDN/programregler 

Följande FQDN/programregler krävs för AKS-kluster som har Azure Policy aktiverat.

| FQDN                                          | Port      | Användning      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.core.windows.net`** | **`HTTPS:443`** | Den här adressen används för att hämta Kubernetes-principer och rapportera klusterefterlevnadsstatus till principtjänsten. |
| **`store.policy.core.windows.net`** | **`HTTPS:443`** | Den här adressen används för att hämta Gatekeeper-artefakter från inbyggda principer. |
| **`gov-prod-policy-data.trafficmanager.net`** | **`HTTPS:443`** | Den här adressen används för korrekt Azure Policy.  |
| **`raw.githubusercontent.com`**               | **`HTTPS:443`** | Den här adressen används för att hämta inbyggda principer från GitHub för att säkerställa korrekt Azure Policy. |
| **`dc.services.visualstudio.com`**            | **`HTTPS:443`** | Azure Policy tillägg som skickar telemetridata till slutpunkten för programinsikter. |

#### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure China 21Vianet FQDN/programregler 

Följande FQDN/programregler krävs för AKS-kluster som har Azure Policy aktiverat.

| FQDN                                          | Port      | Användning      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.cn`** | **`HTTPS:443`** | Den här adressen används för att hämta Kubernetes-principerna och rapportera klusterefterlevnadsstatus till principtjänsten. |
| **`store.policy.azure.cn`** | **`HTTPS:443`** | Den här adressen används för att hämta Gatekeeper-artefakter för inbyggda principer. |

#### <a name="azure-us-government-required-fqdn--application-rules"></a>FQDN/programregler som krävs för Azure US Government

Följande FQDN/programregler krävs för AKS-kluster som har Azure Policy aktiverat.

| FQDN                                          | Port      | Användning      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.us`** | **`HTTPS:443`** | Den här adressen används för att hämta Kubernetes-principer och rapportera klusterefterlevnadsstatus till principtjänsten. |
| **`store.policy.azure.us`** | **`HTTPS:443`** | Den här adressen används för att hämta Gatekeeper-artefakter för inbyggda principer. |

## <a name="restrict-egress-traffic-using-azure-firewall"></a>Begränsa utgående trafik med Hjälp av Azure Firewall

Azure Firewall innehåller en FQDN-tagg () Azure Kubernetes Service ( ) för `AzureKubernetesService` att förenkla konfigurationen. 

> [!NOTE]
> FQDN-taggen innehåller alla FQDN som anges ovan och hålls automatiskt uppdaterad.
>
> Vi rekommenderar att du har minst 20 IP-adresser för Azure Firewall för produktionsscenarier för att undvika problem med SNAT-portutmattning.

Nedan visas ett exempel på en arkitektur för distributionen:

![Låst topologi](media/limit-egress-traffic/aks-azure-firewall-egress.png)

* Offentlig ingress tvingas att flöda genom brandväggsfilter
  * AKS-agentnoder är isolerade i ett dedikerat undernät.
  * [Azure Firewall](../firewall/overview.md) distribueras i ett eget undernät.
  * En DNAT-regel översätter den offentliga IP-adressen för den privata datorn till LB-ip-adressen för frontend.
* Utgående begäranden startar från agentnoder till Azure Firewall interna IP-adressen med hjälp av [en användardefinierad väg](egress-outboundtype.md)
  * Begäranden från AKS-agentnoder följer en UDR som har placerats i undernätet som AKS-klustret distribuerades till.
  * Azure Firewall ut från det virtuella nätverket från en offentlig IP-adress på en server
  * Åtkomst till det offentliga Internet eller andra Azure-tjänster flödar till och från brandväggens IP-adress på frontend
  * Alternativt kan åtkomsten till AKS-kontrollplanet skyddas av [API-serverns](./api-server-authorized-ip-ranges.md)auktoriserade IP-intervall , som innehåller brandväggens offentliga IP-adress på serversidan.
* Intern trafik
  * Alternativt kan du i stället eller förutom en offentlig [Load Balancer](load-balancer-standard.md) använda en intern [Load Balancer](internal-lb.md) för intern trafik, som du även kan isolera i sitt eget undernät.


Stegen nedan använder FQDN Azure Firewall taggen för att begränsa utgående trafik från AKS-klustret och ger ett exempel på hur du konfigurerar offentlig inkommande trafik `AzureKubernetesService` via brandväggen.


### <a name="set-configuration-via-environment-variables"></a>Ange konfiguration via miljövariabler

Definiera en uppsättning miljövariabler som ska användas i resursskapande.

```bash
PREFIX="aks-egress"
RG="${PREFIX}-rg"
LOC="eastus"
PLUGIN=azure
AKSNAME="${PREFIX}"
VNET_NAME="${PREFIX}-vnet"
AKSSUBNET_NAME="aks-subnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}-fw"
FWPUBLICIP_NAME="${PREFIX}-fwpublicip"
FWIPCONFIG_NAME="${PREFIX}-fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}-fwrt"
FWROUTE_NAME="${PREFIX}-fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}-fwinternet"
```

### <a name="create-a-virtual-network-with-multiple-subnets"></a>Skapa ett virtuellt nätverk med flera undernät

Etablera ett virtuellt nätverk med två separata undernät, ett för klustret, ett för brandväggen. Du kan också skapa en för intern tjänstingress.

![Tom nätverkstopologi](media/limit-egress-traffic/empty-network.png)

Skapa en resursgrupp som ska innehålla alla resurser.

```azurecli
# Create Resource Group

az group create --name $RG --location $LOC
```

Skapa ett virtuellt nätverk med två undernät som värd för AKS-klustret och Azure Firewall. Var och en har sitt eget undernät. Vi börjar med AKS-nätverket.

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --location $LOC \
    --address-prefixes 10.42.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 10.42.1.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 10.42.2.0/24
```

### <a name="create-and-set-up-an-azure-firewall-with-a-udr"></a>Skapa och konfigurera en Azure Firewall med en UDR

Azure Firewall regler för inkommande och utgående trafik måste konfigureras. Brandväggens huvudsakliga syfte är att göra det möjligt för organisationer att konfigurera detaljerade regler för in- och utgående trafik till och från AKS-klustret.

![Brandvägg och UDR](media/limit-egress-traffic/firewall-udr.png)


> [!IMPORTANT]
> Om ditt kluster eller program skapar ett stort antal utgående anslutningar som dirigeras till samma eller en liten delmängd av mål, kan du behöva fler IP-adresser för brandväggen för att undvika att portarna per ip-adress på serversidan maxas.
> Mer information om hur du skapar en Azure-brandvägg med flera IP-adresser finns [ **här**](../firewall/quick-create-multiple-ip-template.md)

Skapa en offentlig IP-standardresurs för SKU:n som ska användas Azure Firewall för frontend-adressen.

```azurecli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Registrera cli-tillägget preview för att skapa en Azure Firewall.
```azurecli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC --enable-dns-proxy true
```
IP-adressen som skapades tidigare kan nu tilldelas till brandväggens frontend.

> [!NOTE]
> Det kan ta några minuter att konfigurera den offentliga IP Azure Firewall adressen.
> Om du vill använda FQDN i nätverksregler måste DNS-proxyn vara aktiverad. När brandväggen är aktiverad lyssnar den på port 53 och vidarebefordrar DNS-begäranden till DNS-servern som anges ovan. Detta gör att brandväggen kan översätta detta FQDN automatiskt.

```azurecli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

När föregående kommando har lyckats sparar du brandväggens IP-adress på serversidan för konfiguration senare.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

> [!NOTE]
> Om du använder säker åtkomst till [](./api-server-authorized-ip-ranges.md)AKS API-servern med auktoriserade IP-adressintervall måste du lägga till brandväggens offentliga IP-adress i det auktoriserade IP-intervallet.

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Skapa en UDR med ett hopp till Azure Firewall

Azure dirigerar automatiskt trafik mellan Azure-undernät, virtuella nätverk och lokala nätverk. Om du vill ändra någon av Azures standardroutning gör du det genom att skapa en routningstabell.

Skapa en tom vägtabell som ska associeras med ett visst undernät. Vägtabellen definierar nästa hopp som den Azure Firewall skapade ovan. Varje undernät kan ha noll eller en associerad routningstabell.

```azurecli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG -l $LOC --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Läs [dokumentationen om virtuell nätverksvägtabell](../virtual-network/virtual-networks-udr-overview.md#user-defined) om hur du kan åsidosätta Azures standardsystemvägar eller lägga till ytterligare vägar i ett undernäts vägtabell.

### <a name="adding-firewall-rules"></a>Lägga till brandväggsregler

Nedan visas tre nätverksregler som du kan använda för att konfigurera i brandväggen. Du kan behöva anpassa dessa regler baserat på din distribution. Den första regeln tillåter åtkomst till port 9000 via TCP. Den andra regeln tillåter åtkomst till port 1194 och 123 via UDP (om du distribuerar till Azure China 21Vianet kan du behöva [mer](#azure-china-21vianet-required-network-rules)). Båda dessa regler tillåter endast trafik till den Azure-region-CIDR som vi använder, i det här fallet USA, östra. Slutligen lägger vi till en tredje nätverksregel som öppnar port 123 till FQDN via UDP (att lägga till ett FQDN som en nätverksregel är en av de specifika funktionerna i Azure Firewall och du måste anpassa det när du använder dina `ntp.ubuntu.com` egna alternativ).

När du har lagt till nätverksregler lägger vi även till en programregel med som omfattar alla nödvändiga FQDN som är tillgängliga via `AzureKubernetesService` TCP-port 443 och port 80.

```
# Add FW Network Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apiudp' --protocols 'UDP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 1194 --action allow --priority 100
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apitcp' --protocols 'TCP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 9000
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'time' --protocols 'UDP' --source-addresses '*' --destination-fqdns 'ntp.ubuntu.com' --destination-ports 123

# Add FW Application Rules

az network firewall application-rule create -g $RG -f $FWNAME --collection-name 'aksfwar' -n 'fqdn' --source-addresses '*' --protocols 'http=80' 'https=443' --fqdn-tags "AzureKubernetesService" --action allow --priority 100
```

I [Azure Firewall dokumentationen](../firewall/overview.md) finns mer information om Azure Firewall tjänsten.

### <a name="associate-the-route-table-to-aks"></a>Associera vägtabellen med AKS

För att associera klustret med brandväggen måste det dedikerade undernätet för klustrets undernät referera till den vägtabell som skapades ovan. Association kan göras genom att utfärda ett kommando till det virtuella nätverket som innehåller både klustret och brandväggen för att uppdatera vägtabellen för klustrets undernät.

```azurecli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

### <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Distribuera AKS med utgående typ av UDR till det befintliga nätverket

Nu kan ett AKS-kluster distribueras till det befintliga virtuella nätverket. Vi använder även [ `userDefinedRouting` ](egress-outboundtype.md)utgående typ . Den här funktionen säkerställer att all utgående trafik tvingas genom brandväggen och att inga andra utgående sökvägar finns (som standard kan Load Balancer utgående typ användas).

![aks-deploy](media/limit-egress-traffic/aks-udr-fw.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Skapa ett huvudnamn för tjänsten med åtkomst till etablering i det befintliga virtuella nätverket

En klusteridentitet (hanterad identitet eller tjänstens huvudnamn) används av AKS för att skapa klusterresurser. Ett huvudnamn för tjänsten som skickas vid tidpunkten för att skapa används för att skapa underliggande AKS-resurser som Storage-resurser, IP-adresser och lastbalanserare som används av AKS (du kan också använda en [hanterad identitet](use-managed-identity.md) i stället). Om du inte beviljas lämpliga behörigheter nedan kan du inte etablera AKS-klustret.

```azurecli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Ersätt nu och `APPID` nedan `PASSWORD` med appid för tjänstens huvudnamn och lösenordet för tjänstens huvudnamn som genererades automatiskt av föregående kommandoutdata. Vi refererar till VNET-resurs-ID:t för att bevilja behörigheter till tjänstens huvudnamn så att AKS kan distribuera resurser till det.

```azurecli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role "Network Contributor"
```

Du kan kontrollera de detaljerade behörigheter som krävs [här.](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)

> [!NOTE]
> Om du använder plugin-programmet kubenet måste du ge AKS-tjänstens huvudnamn eller hanterade identitet behörigheter till den förskapade routningstabellen, eftersom kubenet kräver en routningstabell för att lägga till neccesary-routningsregler. 
> ```azurecli-interactive
> RTID=$(az network route-table show -g $RG -n $FWROUTE_TABLE_NAME --query id -o tsv)
> az role assignment create --assignee $APPID --scope $RTID --role "Network Contributor"
> ```

### <a name="deploy-aks"></a>Distribuera AKS

Slutligen kan AKS-klustret distribueras till det befintliga undernät som vi har dedikerat för klustret. Målundernätet som ska distribueras till definieras med miljövariabeln `$SUBNETID` . Vi har inte definierat `$SUBNETID` variabeln i föregående steg. Om du vill ange värdet för undernäts-ID:t kan du använda följande kommando:

```azurecli
SUBNETID=$(az network vnet subnet show -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --query id -o tsv)
```

Du definierar den utgående typen för att använda den UDR som redan finns i undernätet. Den här konfigurationen gör att AKS kan hoppa över installationen och IP-etableringen för lastbalanseraren.

> [!IMPORTANT]
> Mer information om utgående typ av UDR, inklusive begränsningar, finns i [**utgående utgående typ UDR**](egress-outboundtype.md#limitations).


> [!TIP]
> Ytterligare funktioner kan läggas till i klusterdistributionen, till exempel [**privat kluster.**](private-clusters.md) 
>
> AKS-funktionen för [**API-server-auktoriserade IP-intervall**](api-server-authorized-ip-ranges.md) kan läggas till för att begränsa API-serveråtkomst till endast brandväggens offentliga slutpunkt. Funktionen för auktoriserade IP-intervall betecknas i diagrammet som valfri. När du aktiverar funktionen för auktoriserat IP-intervall för att begränsa API-serveråtkomsten måste utvecklarverktygen använda en jumpbox från brandväggens virtuella nätverk, eller så måste du lägga till alla slutpunkter för utvecklare i det auktoriserade IP-intervallet.

```azurecli
az aks create -g $RG -n $AKSNAME -l $LOC \
  --node-count 3 --generate-ssh-keys \
  --network-plugin $PLUGIN \
  --outbound-type userDefinedRouting \
  --service-cidr 10.41.0.0/16 \
  --dns-service-ip 10.41.0.10 \
  --docker-bridge-address 172.17.0.1/16 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
```

### <a name="enable-developer-access-to-the-api-server"></a>Aktivera utvecklaråtkomst till API-servern

Om du använde auktoriserade IP-intervall för klustret i föregående steg måste du lägga till dina IP-adresser för utvecklingsverktyg i AKS-klusterlistan över godkända IP-intervall för att få åtkomst till API-servern därifrån. Ett annat alternativ är att konfigurera en jumpbox med nödvändiga verktyg i ett separat undernät i brandväggens virtuella nätverk.

Lägg till ytterligare en IP-adress i godkända intervall med följande kommando

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 Använd kommandot [az aks get-credentials][az-aks-get-credentials] för att konfigurera för att ansluta till ditt nyligen skapade `kubectl` Kubernetes-kluster. 

 ```azurecli
 az aks get-credentials -g $RG -n $AKSNAME
 ```

### <a name="deploy-a-public-service"></a>Distribuera en offentlig tjänst
Nu kan du börja exponera tjänster och distribuera program till det här klustret. I det här exemplet exponerar vi en offentlig tjänst, men du kan också välja att exponera en intern tjänst via en [intern lastbalanserare](internal-lb.md).

![Public Service DNAT](media/limit-egress-traffic/aks-create-svc.png)

Distribuera appen Azure Voting genom att kopiera yaml nedan till en fil med namnet `example.yaml` .

```yaml
# voting-storage-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-storage
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-storage
  template:
    metadata:
      labels:
        app: voting-storage
    spec:
      containers:
      - name: voting-storage
        image: mcr.microsoft.com/aks/samples/voting/storage:2.0
        args: ["--ignore-db-dir=lost+found"]
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_ROOT_PASSWORD
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
---
# voting-storage-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: voting-storage-secret
type: Opaque
data:
  MYSQL_USER: ZGJ1c2Vy
  MYSQL_PASSWORD: UGFzc3dvcmQxMg==
  MYSQL_DATABASE: YXp1cmV2b3Rl
  MYSQL_ROOT_PASSWORD: UGFzc3dvcmQxMg==
---
# voting-storage-pv-claim.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
# voting-storage-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-storage
  labels: 
    app: voting-storage
spec:
  ports:
  - port: 3306
    name: mysql
  selector:
    app: voting-storage
---
# voting-app-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-app
  template:
    metadata:
      labels:
        app: voting-app
    spec:
      containers:
      - name: voting-app
        image: mcr.microsoft.com/aks/samples/voting/app:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
        - name: ANALYTICS_HOST
          value: "voting-analytics"
---
# voting-app-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-app
  labels: 
    app: voting-app
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 8080
    name: http
  selector:
    app: voting-app
---
# voting-analytics-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-analytics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-analytics
      version: "2.0"
  template:
    metadata:
      labels:
        app: voting-analytics
        version: "2.0"
    spec:
      containers:
      - name: voting-analytics
        image: mcr.microsoft.com/aks/samples/voting/analytics:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
---
# voting-analytics-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-analytics
  labels: 
    app: voting-analytics
spec:
  ports:
  - port: 8080
    name: http
  selector:
    app: voting-analytics
```

Distribuera tjänsten genom att köra:

```bash
kubectl apply -f example.yaml
```

### <a name="add-a-dnat-rule-to-azure-firewall"></a>Lägga till en DNAT-regel för att Azure Firewall

> [!IMPORTANT]
> När du använder Azure Firewall för att begränsa utgående trafik och skapa en användardefinierad väg (UDR) för att tvinga all utgående trafik, se till att du skapar en lämplig DNAT-regel i brandväggen för att tillåta ingresstrafik korrekt. Om Azure Firewall med en UDR bryts ingresskonfigurationen på grund av asymmetrisk routning. (Problemet uppstår om AKS-undernätet har en standardväg som går till brandväggens privata IP-adress, men du använder en offentlig lastbalanserare – ingress- eller Kubernetes-tjänst av typen: LoadBalancer). I det här fallet tas den inkommande lastbalanseringstrafiken emot via dess offentliga IP-adress, men returvägen går genom brandväggens privata IP-adress. Eftersom brandväggen är tillståndsfull ignorerar den det returnerade paketet eftersom brandväggen inte känner till en upprättad session. Information om hur du integrerar Azure Firewall med ingress- eller tjänstlastbalanserare finns i [Integrera Azure Firewall med Azure Standard Load Balancer](../firewall/integrate-lb.md).


För att konfigurera inkommande anslutningar måste en DNAT-regel skrivas till Azure Firewall. För att testa anslutningen till klustret definieras en regel för brandväggens offentliga IP-adress på serversidan som dirigeras till den interna IP-adress som exponeras av den interna tjänsten.

Måladressen kan anpassas eftersom det är porten i brandväggen som ska nås. Den översatta adressen måste vara IP-adressen för den interna lastbalanseraren. Den översatta porten måste vara den exponerade porten för Kubernetes-tjänsten.

Du måste ange den interna IP-adressen som tilldelats till lastbalanseraren som skapas av Kubernetes-tjänsten. Hämta adressen genom att köra:

```bash
kubectl get services
```

IP-adressen som behövs visas i kolumnen EXTERNAL-IP, ungefär så här.

```bash
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP      10.41.0.1       <none>        443/TCP        10h
voting-analytics   ClusterIP      10.41.88.129    <none>        8080/TCP       9m
voting-app         LoadBalancer   10.41.185.82    20.39.18.6    80:32718/TCP   9m
voting-storage     ClusterIP      10.41.221.201   <none>        3306/TCP       9m
```

Hämta tjänstens IP-adress genom att köra:
```bash
SERVICE_IP=$(kubectl get svc voting-app -o jsonpath='{.status.loadBalancer.ingress[*].ip}')
```

Lägg till NAT-regeln genom att köra:
```azurecli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address $SERVICE_IP
```

### <a name="validate-connectivity"></a>Verifiera anslutningen

Gå till den Azure Firewall IP-adressen i en webbläsare för att verifiera anslutningen.

Du bör se AKS-röstningsappen. I det här exemplet var brandväggens offentliga IP-adress `52.253.228.132` .


![Skärmbild som visar A K S-röstningsappen med knappar för katter, hundar och återställning och summor.](media/limit-egress-traffic/aks-vote.png)


### <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa Azure-resurser tar du bort AKS-resursgruppen.

```azurecli
az group delete -g $RG
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig vilka portar och adresser som ska tillåtas om du vill begränsa utgående trafik för klustret. Du har också sett hur du skyddar din utgående trafik med Azure Firewall. 

Om det behövs kan du generalisera stegen ovan för att vidarebefordra trafiken till din önskade utgående lösning genom att följa dokumentationen [för `userDefinedRoute` utgående typ.](egress-outboundtype.md)

Om du vill begränsa hur poddar kommunicerar sinsemellan och East-West trafikbegränsningar i klustret kan du se Skydda trafik mellan poddar med [nätverksprinciper i AKS][network-policy].

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[dev-spaces-service-tags]: ../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations
