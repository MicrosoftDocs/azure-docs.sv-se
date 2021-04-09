---
title: Begränsa utgående trafik i Azure Kubernetes service (AKS)
description: Ta reda på vilka portar och adresser som krävs för att styra utgående trafik i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.author: jpalma
ms.date: 01/12/2021
author: palma21
ms.openlocfilehash: 9e65e2736578ce04dfa79d5a7827e190d47fb312
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103573837"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Styra utgående trafik för klusternoder i Azure Kubernetes service (AKS)

Den här artikeln innehåller nödvändig information som gör att du kan skydda utgående trafik från Azure Kubernetes service (AKS). Den innehåller kluster kraven för en bas-AKS-distribution och ytterligare krav för valfria tillägg och funktioner. [Ett exempel kommer att anges i slutet av hur du konfigurerar dessa krav med Azure-brandväggen](#restrict-egress-traffic-using-azure-firewall). Du kan dock använda den här informationen för all utgående begränsnings metod eller-utrustning.

## <a name="background"></a>Bakgrund

AKS-kluster distribueras i ett virtuellt nätverk. Det här nätverket kan hanteras (skapas av AKS) eller anpassas (förkonfigureras av användaren i förväg). I båda fallen har klustret **utgående** beroenden för tjänster utanför det virtuella nätverket (tjänsten har inga inkommande beroenden).

I hanterings-och drift syfte måste noder i ett AKS-kluster ha åtkomst till vissa portar och fullständiga domän namn (FQDN). De här slut punkterna krävs för att noderna ska kunna kommunicera med API-servern, eller för att ladda ned och installera kärn Kubernetes-kluster komponenter och nodens säkerhets uppdateringar. Till exempel måste klustret Hämta behållare avbildningar för bas system från Microsoft Container Registry (MCR).

AKS utgående beroenden är nästan helt definierade med FQDN, som inte har statiska adresser bakom dem. Bristen på statiska adresser innebär att nätverks säkerhets grupper inte kan användas för att låsa utgående trafik från ett AKS-kluster. 

Som standard har AKS-kluster obegränsad utgående Internet åtkomst. Den här nivån av nätverks åtkomst tillåter noder och tjänster som du kör för att få åtkomst till externa resurser efter behov. Om du vill begränsa utgångs trafik måste ett begränsat antal portar och adresser vara tillgängliga för att upprätthålla felfria kluster underhålls uppgifter. Den enklaste lösningen för att skydda utgående adresser är att använda en brand Väggs enhet som kan styra utgående trafik baserat på domän namn. Azure-brandväggen kan till exempel begränsa utgående HTTP-och HTTPS-trafik baserat på målets FQDN. Du kan också konfigurera önskade brand Väggs-och säkerhets regler för att tillåta dessa obligatoriska portar och adresser.

> [!IMPORTANT]
> Det här dokumentet beskriver bara hur du låser trafiken som lämnar AKS-undernätet. AKS har som standard inga ingress-krav.  Det går inte att blockera **intern under näts trafik** med hjälp av nätverks säkerhets grupper (NSG: er) och brand väggar. Använd [**_nätverks principer_**][network-policy]för att styra och blockera trafiken i klustret.

## <a name="required-outbound-network-rules-and-fqdns-for-aks-clusters"></a>Nödvändiga utgående nätverks regler och FQDN för AKS-kluster

Följande nätverks-och FQDN/applikations regler krävs för ett AKS-kluster. du kan använda dem om du vill konfigurera en annan lösning än Azure-brandväggen.

* IP-adress beroenden är för trafik som inte är HTTP/S (både TCP-och UDP-trafik)
* FQDN HTTP/HTTPS-slutpunkter kan placeras i brand Väggs enheten.
* HTTP/HTTPS-slutpunkter med jokertecken är beroenden som kan variera med ditt AKS-kluster baserat på ett antal kvalificerare.
* AKS använder en åtkomst kontroll för att mata in FQDN som en miljö variabel för alla distributioner under Kube-system och Gatekeeper-system, som säkerställer all system kommunikation mellan noder och API-servern använder API-serverns FQDN och inte IP-adressen för API-servern. 
* Om du har en app eller lösning som behöver kommunicera med API-servern måste du lägga till **ytterligare** en nätverks regel för att tillåta *TCP-kommunikation till port 443 i din API-servers IP*.
* Vid sällsynta tillfällen, om det finns en underhålls åtgärd kan din API-servers IP-adress ändras. Planerade underhålls åtgärder som kan ändra API-serverns IP-adress förmedlas alltid i förväg.


### <a name="azure-global-required-network-rules"></a>Azures globala nätverks regler krävs

De nödvändiga nätverks reglerna och IP-adress beroendena är:

| Destinations slut punkt                                                             | Protokoll | Port    | Användning  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Eller* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Eller* <br/> [Regionala CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Eller* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | För tunnel säker kommunikation mellan noderna och kontroll planet. Detta krävs inte för [privata kluster](private-clusters.md)|
| **`*:9000`** <br/> *Eller* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Eller* <br/> [Regionala CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Eller* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | För tunnel säker kommunikation mellan noderna och kontroll planet. Detta krävs inte för [privata kluster](private-clusters.md) |
| **`*:123`** eller **`ntp.ubuntu.com:123`** (om du använder Azure Firewall Network rules)  | UDP      | 123     | Krävs för Time-synkronisering för Network Time Protocol (NTP) på Linux-noder.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Om du använder anpassade DNS-servrar måste du se till att de är tillgängliga för klusternoderna. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Krävs om du kör poddar/distributioner som ansluter till API-servern, använder dessa poddar/distributioner API-IP. Detta krävs inte för [privata kluster](private-clusters.md)  |

### <a name="azure-global-required-fqdn--application-rules"></a>Globalt obligatoriska FQDN/applikations regler för Azure 

Följande FQDN/program-regler krävs:

| Mål-FQDN                 | Port            | Användning      |
|----------------------------------|-----------------|----------|
| **`*.hcp.<location>.azmk8s.io`** | **`HTTPS:443`** | Krävs för noden <-> API-server kommunikation. Ersätt *\<location\>* med den region där ditt AKS-kluster har distribuerats. |
| **`mcr.microsoft.com`**          | **`HTTPS:443`** | Krävs för att få åtkomst till avbildningar i Microsoft Container Registry (MCR). Det här registret innehåller bilder/diagram från första part (till exempel coreDNS osv.). De här avbildningarna krävs för att skapa och arbeta med klustret korrekt, inklusive skalnings-och uppgraderings åtgärder.  |
| **`*.data.mcr.microsoft.com`**   | **`HTTPS:443`** | Krävs för MCR-lagring som backas upp av Azure Content Delivery Network (CDN). |
| **`management.azure.com`**       | **`HTTPS:443`** | Krävs för Kubernetes-åtgärder mot Azure-API: et. |
| **`login.microsoftonline.com`**  | **`HTTPS:443`** | Krävs för Azure Active Directory autentisering. |
| **`packages.microsoft.com`**     | **`HTTPS:443`** | Den här adressen är Microsoft packages-lagringsplatsen som används för cachelagrat *apt-get-* åtgärder.  Exempel paket är Moby, PowerShell och Azure CLI. |
| **`acs-mirror.azureedge.net`**   | **`HTTPS:443`** | Den här adressen är för den lagrings plats som krävs för att ladda ned och installera nödvändiga binärfiler som Kubernetes och Azure CNI. |

### <a name="azure-china-21vianet-required-network-rules"></a>Azure Kina 21Vianet krävde nätverks regler

De nödvändiga nätverks reglerna och IP-adress beroendena är:

| Destinations slut punkt                                                             | Protokoll | Port    | Användning  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Eller* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.Region:1194`** <br/> *Eller* <br/> [Regionala CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Eller* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | För tunnel säker kommunikation mellan noderna och kontroll planet. |
| **`*:9000`** <br/> *Eller* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Eller* <br/> [Regionala CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Eller* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | För tunnel säker kommunikation mellan noderna och kontroll planet. |
| **`*:22`** <br/> *Eller* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:22`** <br/> *Eller* <br/> [Regionala CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:22`** <br/> *Eller* <br/> **`APIServerPublicIP:22`** `(only known after cluster creation)`  | TCP           | 22      | För tunnel säker kommunikation mellan noderna och kontroll planet. |
| **`*:123`** eller **`ntp.ubuntu.com:123`** (om du använder Azure Firewall Network rules)  | UDP      | 123     | Krävs för Time-synkronisering för Network Time Protocol (NTP) på Linux-noder.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Om du använder anpassade DNS-servrar måste du se till att de är tillgängliga för klusternoderna. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Krävs om du kör poddar/distributioner som ansluter till API-servern, använder dessa Pod/distributioner API-IP.  |

### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure Kina 21Vianet krävde FQDN/program regler

Följande FQDN/program-regler krävs:

| Mål-FQDN                               | Port            | Användning      |
|------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Krävs för noden <-> API-server kommunikation. Ersätt *\<location\>* med den region där ditt AKS-kluster har distribuerats. |
| **`*.tun.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Krävs för noden <-> API-server kommunikation. Ersätt *\<location\>* med den region där ditt AKS-kluster har distribuerats. |
| **`mcr.microsoft.com`**                        | **`HTTPS:443`** | Krävs för att få åtkomst till avbildningar i Microsoft Container Registry (MCR). Det här registret innehåller bilder/diagram från första part (till exempel coreDNS osv.). De här avbildningarna krävs för att skapa och arbeta med klustret korrekt, inklusive skalnings-och uppgraderings åtgärder. |
| **`.data.mcr.microsoft.com`**                  | **`HTTPS:443`** | Krävs för MCR-lagring som backas upp av Azure-Content Delivery Network (CDN). |
| **`management.chinacloudapi.cn`**              | **`HTTPS:443`** | Krävs för Kubernetes-åtgärder mot Azure-API: et. |
| **`login.chinacloudapi.cn`**                   | **`HTTPS:443`** | Krävs för Azure Active Directory autentisering. |
| **`packages.microsoft.com`**                   | **`HTTPS:443`** | Den här adressen är Microsoft packages-lagringsplatsen som används för cachelagrat *apt-get-* åtgärder.  Exempel paket är Moby, PowerShell och Azure CLI. |
| **`*.azk8s.cn`**                               | **`HTTPS:443`** | Den här adressen är för den lagrings plats som krävs för att ladda ned och installera nödvändiga binärfiler som Kubernetes och Azure CNI. |

### <a name="azure-us-government-required-network-rules"></a>Azure amerikanska myndigheter, nödvändiga nätverks regler

De nödvändiga nätverks reglerna och IP-adress beroendena är:

| Destinations slut punkt                                                             | Protokoll | Port    | Användning  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Eller* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Eller* <br/> [Regionala CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Eller* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | För tunnel säker kommunikation mellan noderna och kontroll planet. |
| **`*:9000`** <br/> *Eller* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Eller* <br/> [Regionala CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Eller* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | För tunnel säker kommunikation mellan noderna och kontroll planet. |
| **`*:123`** eller **`ntp.ubuntu.com:123`** (om du använder Azure Firewall Network rules)  | UDP      | 123     | Krävs för Time-synkronisering för Network Time Protocol (NTP) på Linux-noder.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Om du använder anpassade DNS-servrar måste du se till att de är tillgängliga för klusternoderna. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Krävs om du kör poddar/distributioner som ansluter till API-servern, använder dessa poddar/distributioner API-IP.  |

### <a name="azure-us-government-required-fqdn--application-rules"></a>Azure amerikanska myndigheter, nödvändiga FQDN/applikations regler 

Följande FQDN/program-regler krävs:

| Mål-FQDN                                        | Port            | Användning      |
|---------------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.aks.containerservice.azure.us`** | **`HTTPS:443`** | Krävs för noden <-> API-server kommunikation. Ersätt *\<location\>* med den region där ditt AKS-kluster har distribuerats.|
| **`mcr.microsoft.com`**                                 | **`HTTPS:443`** | Krävs för att få åtkomst till avbildningar i Microsoft Container Registry (MCR). Det här registret innehåller bilder/diagram från första part (till exempel coreDNS osv.). De här avbildningarna krävs för att skapa och arbeta med klustret korrekt, inklusive skalnings-och uppgraderings åtgärder. |
| **`*.data.mcr.microsoft.com`**                          | **`HTTPS:443`** | Krävs för MCR-lagring som backas upp av Azure Content Delivery Network (CDN). |
| **`management.usgovcloudapi.net`**                      | **`HTTPS:443`** | Krävs för Kubernetes-åtgärder mot Azure-API: et. |
| **`login.microsoftonline.us`**                          | **`HTTPS:443`** | Krävs för Azure Active Directory autentisering. |
| **`packages.microsoft.com`**                            | **`HTTPS:443`** | Den här adressen är Microsoft packages-lagringsplatsen som används för cachelagrat *apt-get-* åtgärder.  Exempel paket är Moby, PowerShell och Azure CLI. |
| **`acs-mirror.azureedge.net`**                          | **`HTTPS:443`** | Den här adressen är för den lagrings plats som krävs för att installera nödvändiga binärfiler som Kubernetes och Azure CNI. |

## <a name="optional-recommended-fqdn--application-rules-for-aks-clusters"></a>Valfria rekommenderade FQDN/program regler för AKS-kluster

Följande FQDN/program-regler är valfria men rekommenderas för AKS-kluster:

| Mål-FQDN                                                               | Port          | Användning      |
|--------------------------------------------------------------------------------|---------------|----------|
| **`security.ubuntu.com`, `azure.archive.ubuntu.com`, `changelogs.ubuntu.com`** | **`HTTP:80`** | Den här adressen låter Linux-klusternoderna hämta de nödvändiga säkerhets korrigeringarna och uppdateringarna. |

Om du väljer att blockera/inte tillåta dessa FQDN-namn får noderna bara uppdateringar av operativ systemet när du gör en [uppgradering av uppgradering](node-image-upgrade.md) eller [kluster uppgradering](upgrade-cluster.md).

## <a name="gpu-enabled-aks-clusters"></a>GPU-aktiverade AKS-kluster

### <a name="required-fqdn--application-rules"></a>Obligatoriska FQDN/applikations regler

Följande FQDN/program-regler krävs för AKS-kluster med aktiverat GPU: er:

| Mål-FQDN                        | Port      | Användning      |
|-----------------------------------------|-----------|----------|
| **`nvidia.github.io`**                  | **`HTTPS:443`** | Den här adressen används för korrekt driv rutins installation och åtgärd på GPU-baserade noder. |
| **`us.download.nvidia.com`**            | **`HTTPS:443`** | Den här adressen används för korrekt driv rutins installation och åtgärd på GPU-baserade noder. |
| **`apt.dockerproject.org`**             | **`HTTPS:443`** | Den här adressen används för korrekt driv rutins installation och åtgärd på GPU-baserade noder. |

## <a name="windows-server-based-node-pools"></a>Windows Server-baserade Node-pooler 

### <a name="required-fqdn--application-rules"></a>Obligatoriska FQDN/applikations regler

Följande FQDN/applikations regler krävs för att använda Windows Server-baserade Node-pooler:

| Mål-FQDN                                                           | Port      | Användning      |
|----------------------------------------------------------------------------|-----------|----------|
| **`onegetcdn.azureedge.net, go.microsoft.com`**                            | **`HTTPS:443`** | Installera Windows-relaterade binärfiler |
| **`*.mp.microsoft.com, www.msftconnecttest.com, ctldl.windowsupdate.com`** | **`HTTP:80`**   | Installera Windows-relaterade binärfiler |

## <a name="aks-addons-and-integrations"></a>AKS-tillägg och integreringar

### <a name="azure-monitor-for-containers"></a>Azure Monitor för containrar

Det finns två alternativ för att ge åtkomst till Azure Monitor för behållare. du kan tillåta Azure Monitor [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) **eller** ge åtkomst till nödvändiga FQDN/applikations regler.

#### <a name="required-network-rules"></a>Nödvändiga nätverks regler

Följande FQDN/program-regler krävs:

| Destinations slut punkt                                                             | Protokoll | Port    | Användning  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureMonitor:443`**  | TCP           | 443      | Den här slut punkten används för att skicka Mät data och loggar till Azure Monitor och Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Obligatoriska FQDN/applikations regler

Följande FQDN/program-regler krävs för AKS-kluster som har Azure Monitor för behållare aktiverade:

| FQDN                                    | Port      | Användning      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | **`HTTPS:443`**    | Den här slut punkten används för mått och övervakning av telemetri med hjälp av Azure Monitor. |
| *.ods.opinsights.azure.com    | **`HTTPS:443`**    | Den här slut punkten används av Azure Monitor för inmatning av Log Analytics-data. |
| *.oms.opinsights.azure.com | **`HTTPS:443`** | Den här slut punkten används av omsagent, som används för att autentisera Log Analytics-tjänsten. |
| *. monitoring.azure.com | **`HTTPS:443`** | Den här slut punkten används för att skicka Mät data till Azure Monitor. |

### <a name="azure-dev-spaces"></a>Azure Dev Spaces

Uppdatera brand väggen eller säkerhets konfigurationen för att tillåta nätverks trafik till och från alla nedanstående FQDN och [infrastruktur tjänster i Azure dev Spaces][dev-spaces-service-tags].

#### <a name="required-network-rules"></a>Nödvändiga nätverks regler

| Destinations slut punkt                                                             | Protokoll | Port    | Användning  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureDevSpaces`**  | TCP           | 443      | Den här slut punkten används för att skicka Mät data och loggar till Azure Monitor och Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Obligatoriska FQDN/applikations regler 

Följande FQDN/program-regler krävs för AKS-kluster som har Azure dev-utrymmen aktiverade:

| FQDN                                    | Port      | Användning      |
|-----------------------------------------|-----------|----------|
| `cloudflare.docker.com` | **`HTTPS:443`** | Den här adressen används för att hämta Linux Alpine och andra Azure dev Spaces-bilder |
| `gcr.io` | **`HTTPS:443`** | Den här adressen används för att hämta Helm/till-avbildningar |
| `storage.googleapis.com` | **`HTTPS:443`** | Den här adressen används för att hämta Helm/till-avbildningar |


### <a name="azure-policy"></a>Azure Policy

#### <a name="required-fqdn--application-rules"></a>Obligatoriska FQDN/applikations regler 

Följande FQDN/program-regler krävs för AKS-kluster som har Azure Policy aktiverat.

| FQDN                                          | Port      | Användning      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.core.windows.net`** | **`HTTPS:443`** | Den här adressen används för att hämta Kubernetes-principerna och rapportera klustrets kompatibilitetsstatus till princip tjänsten. |
| **`store.policy.core.windows.net`** | **`HTTPS:443`** | Den här adressen används för att hämta Gatekeeper-artefakter för inbyggda principer. |
| **`gov-prod-policy-data.trafficmanager.net`** | **`HTTPS:443`** | Adressen används för att Azure Policy ska fungera korrekt.  |
| **`raw.githubusercontent.com`**               | **`HTTPS:443`** | Den här adressen används för att hämta de inbyggda principerna från GitHub för att säkerställa korrekt drift av Azure Policy. |
| **`dc.services.visualstudio.com`**            | **`HTTPS:443`** | Azure Policy tillägg som skickar telemetridata till program insikter-slutpunkten. |

#### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure Kina 21Vianet krävde FQDN/program regler 

Följande FQDN/program-regler krävs för AKS-kluster som har Azure Policy aktiverat.

| FQDN                                          | Port      | Användning      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.cn`** | **`HTTPS:443`** | Den här adressen används för att hämta Kubernetes-principerna och rapportera klustrets kompatibilitetsstatus till princip tjänsten. |
| **`store.policy.azure.cn`** | **`HTTPS:443`** | Den här adressen används för att hämta Gatekeeper-artefakter för inbyggda principer. |

#### <a name="azure-us-government-required-fqdn--application-rules"></a>Azure amerikanska myndigheter, nödvändiga FQDN/applikations regler

Följande FQDN/program-regler krävs för AKS-kluster som har Azure Policy aktiverat.

| FQDN                                          | Port      | Användning      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.us`** | **`HTTPS:443`** | Den här adressen används för att hämta Kubernetes-principerna och rapportera klustrets kompatibilitetsstatus till princip tjänsten. |
| **`store.policy.azure.us`** | **`HTTPS:443`** | Den här adressen används för att hämta Gatekeeper-artefakter för inbyggda principer. |

## <a name="restrict-egress-traffic-using-azure-firewall"></a>Begränsa utgående trafik med hjälp av Azure-brandvägg

Azure-brandväggen tillhandahåller en FQDN-tagg (Azure Kubernetes service `AzureKubernetesService` ) för att förenkla den här konfigurationen. 

> [!NOTE]
> FQDN-taggen innehåller alla FQDN-namn som anges ovan och uppdateras automatiskt.
>
> Vi rekommenderar att du har minst 20 frontend-IP: er i Azure-brandväggen för produktions scenarier för att undvika att det uppstår problem med SNAT-port överbelastningar.

Nedan visas en exempel arkitektur för distributionen:

![Låst topologi](media/limit-egress-traffic/aks-azure-firewall-egress.png)

* Offentliga ingress tvingas att flöda genom brand Väggs filter
  * AKS agent-noder isoleras i ett dedikerat undernät.
  * [Azure-brandväggen](../firewall/overview.md) distribueras i sitt eget undernät.
  * Med en DNAT-regel översätts den offentliga IP-adressen till LB-klientens IP-adress.
* Utgående begär Anden börjar från agent-noder till den interna Azure-brandväggen med en [användardefinierad väg](egress-outboundtype.md)
  * Begär Anden från AKS agent-noder följer en UDR som har placerats i under nätet som AKS-klustret distribuerades till.
  * Azure-brandväggen går ut från det virtuella nätverket från en offentlig IP-klient
  * Åtkomst till offentliga Internet eller andra Azure-tjänster flödar till och från brand Väggs klient delens IP-adress
  * Åtkomst till AKS-kontroll planet skyddas av [API-serverns auktoriserade IP-intervall](./api-server-authorized-ip-ranges.md), vilket omfattar IP-adress för brand Väggs offentlig klient.
* Intern trafik
  * Du kan också, i stället för en [offentlig Load Balancer](load-balancer-standard.md) använda en [intern Load Balancer](internal-lb.md) för intern trafik, som du kan isolera i sitt eget undernät också.


I stegen nedan används Azure firewalls FQDN- `AzureKubernetesService` tagg för att begränsa utgående trafik från AKS-klustret och ger ett exempel på hur du konfigurerar offentlig inkommande trafik via brand väggen.


### <a name="set-configuration-via-environment-variables"></a>Konfigurera konfiguration via miljövariabler

Definiera en uppsättning miljövariabler som ska användas för att skapa resurser.

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

Etablera ett virtuellt nätverk med två separata undernät, ett för klustret, ett för brand väggen. Du kan också skapa ett för interna tjänst ingångar.

![Tom nätverkstopologi](media/limit-egress-traffic/empty-network.png)

Skapa en resurs grupp för att lagra alla resurser.

```azurecli
# Create Resource Group

az group create --name $RG --location $LOC
```

Skapa ett virtuellt nätverk med två undernät som ska vara värd för AKS-klustret och Azure-brandväggen. Var och en får sitt eget undernät. Vi börjar med AKS-nätverket.

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

### <a name="create-and-set-up-an-azure-firewall-with-a-udr"></a>Skapa och konfigurera en Azure-brandvägg med en UDR

Regler för inkommande och utgående Azure-brandvägg måste konfigureras. Huvud syftet med brand väggen är att göra det möjligt för organisationer att konfigurera detaljerade ingångs-och utgående trafik regler till och från AKS-klustret.

![Brand vägg och UDR](media/limit-egress-traffic/firewall-udr.png)


> [!IMPORTANT]
> Om klustret eller programmet skapar ett stort antal utgående anslutningar riktade mot samma eller mindre delmängd av destinationer, kan du behöva fler brand Väggs-IP-adresser för att undvika maxing av portarna per klient dels-IP.
> Mer information om hur du skapar en Azure-brandvägg med flera IP-adresser finns [ **här**](../firewall/quick-create-multiple-ip-template.md)

Skapa en offentlig IP-resurs med standard-SKU som ska användas som Azure Firewall-frontend-adress.

```azurecli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Registrera Preview CLI-tillägget för att skapa en Azure-brandvägg.
```azurecli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC --enable-dns-proxy true
```
Den IP-adress som skapades tidigare kan nu tilldelas brand Väggs klient delen.

> [!NOTE]
> Det kan ta några minuter att konfigurera den offentliga IP-adressen till Azure-brandväggen.
> Om du vill utnyttja FQDN på nätverks regler måste DNS-proxyn vara aktive rad, när brand väggen ska lyssna på port 53 och vidarebefordra DNS-begäranden till den DNS-server som anges ovan. Detta gör att brand väggen kan översätta detta FQDN automatiskt.

```azurecli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

När det föregående kommandot har slutförts, sparar du IP-adressen för brand Väggs klient delen för konfiguration senare.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

> [!NOTE]
> Om du använder säker åtkomst till AKS-API-servern med [auktoriserade IP-adressintervall](./api-server-authorized-ip-ranges.md)måste du lägga till den offentliga brand väggen i det AUKTORISERAde IP-intervallet.

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Skapa en UDR med hopp till Azure-brandväggen

Azure dirigerar automatiskt trafik mellan Azure-undernät, virtuella nätverk och lokala nätverk. Om du vill ändra någon av Azures standardroutning gör du det genom att skapa en routningstabell.

Skapa en tom routningstabell som ska associeras med ett angivet undernät. Routningstabellen definierar nästa hopp som den Azure-brandvägg som skapats ovan. Varje undernät kan ha noll eller en associerad routningstabell.

```azurecli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG -l $LOC --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Mer information om hur du kan åsidosätta Azures standard system vägar eller lägga till ytterligare vägar till ett undernäts routningstabell finns i [dokumentationen för virtuella nätverks vägar](../virtual-network/virtual-networks-udr-overview.md#user-defined) .

### <a name="adding-firewall-rules"></a>Lägga till brand Väggs regler

Nedan finns tre nätverks regler som du kan använda för att konfigurera i brand väggen. du kan behöva anpassa reglerna baserat på din distribution. Den första regeln tillåter åtkomst till port 9000 via TCP. Den andra regeln tillåter åtkomst till port 1194 och 123 via UDP (om du distribuerar till Azure Kina 21Vianet kan du behöva [mer](#azure-china-21vianet-required-network-rules)). Båda dessa regler tillåter bara trafik till Azure-regionen CIDR som vi använder, i det här fallet i USA, östra. Slutligen ska vi lägga till en tredje nätverks regel som öppnar port 123 i `ntp.ubuntu.com` FQDN via UDP (att lägga till ett fullständigt domän namn som en nätverks regel är en av de specifika funktionerna i Azure Firewall och du måste anpassa den när du använder dina egna alternativ).

När du har angett nätverks reglerna lägger vi också till en program regel med hjälp av `AzureKubernetesService` som omfattar alla nödvändiga FQDN: er som är tillgängliga via TCP-port 443 och port 80.

```
# Add FW Network Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apiudp' --protocols 'UDP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 1194 --action allow --priority 100
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apitcp' --protocols 'TCP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 9000
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'time' --protocols 'UDP' --source-addresses '*' --destination-fqdns 'ntp.ubuntu.com' --destination-ports 123

# Add FW Application Rules

az network firewall application-rule create -g $RG -f $FWNAME --collection-name 'aksfwar' -n 'fqdn' --source-addresses '*' --protocols 'http=80' 'https=443' --fqdn-tags "AzureKubernetesService" --action allow --priority 100
```

Mer information om Azure Firewall-tjänsten finns i [dokumentationen för Azure-brandväggen](../firewall/overview.md) .

### <a name="associate-the-route-table-to-aks"></a>Koppla routningstabellen till AKS

För att associera klustret med brand väggen måste det dedikerade under nätet för klustrets undernät referera till routningstabellen som skapats ovan. Kopplingen kan göras genom att ett kommando utfärdas till det virtuella nätverket som innehåller både klustret och brand väggen för att uppdatera routningstabellen för klustrets undernät.

```azurecli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

### <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Distribuera AKS med utgående typ av UDR till det befintliga nätverket

Nu kan ett AKS-kluster distribueras till det befintliga virtuella nätverket. Vi använder även [utgående typ `userDefinedRouting` ](egress-outboundtype.md), men den här funktionen ser till att utgående trafik tvingas genom brand väggen och inga andra utgående sökvägar kommer att finnas (som standard kan Load Balancer utgående typ användas).

![AKS – distribuera](media/limit-egress-traffic/aks-udr-fw.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Skapa ett huvud namn för tjänsten med åtkomst till etablering i det befintliga virtuella nätverket

En kluster identitet (hanterad identitet eller tjänstens huvud namn) används av AKS för att skapa kluster resurser. Ett huvud namn för tjänsten som skickas vid skapande tid används för att skapa underliggande AKS-resurser, till exempel lagrings resurser, IP-adresser och belastningsutjämnare som används av AKS (du kan också använda en [hanterad identitet](use-managed-identity.md) i stället). Om du inte har beviljats de behörigheter som krävs nedan kan du inte etablera AKS-klustret.

```azurecli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Ersätt nu `APPID` och `PASSWORD` nedan med tjänstens huvud namn och lösen ord för tjänstens huvud namn som genererats av föregående kommandoutdata. Vi refererar till resurs-ID: t för VNET för att bevilja behörighet till tjänstens huvud namn så att AKS kan distribuera resurser till den.

```azurecli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role "Network Contributor"
```

Du kan kontrol lera de detaljerade behörigheter som krävs [här](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).

> [!NOTE]
> Om du använder Kubernetes Network-pluginprogrammet måste du ge AKS tjänstens huvud namn eller hanterade identitets behörigheter till den förgenererade routningstabellen eftersom Kubernetes kräver en routningstabell för att lägga till krävs-routningsregler. 
> ```azurecli-interactive
> RTID=$(az network route-table show -g $RG -n $FWROUTE_TABLE_NAME --query id -o tsv)
> az role assignment create --assignee $APPID --scope $RTID --role "Network Contributor"
> ```

### <a name="deploy-aks"></a>Distribuera AKS

Slutligen kan AKS-klustret distribueras till det befintliga under nätet som vi har dedikerat för klustret. Det mål under nät som ska distribueras till definieras med miljövariabeln `$SUBNETID` . Vi har inte definierat `$SUBNETID` variabeln i föregående steg. Om du vill ange värdet för under nätets ID kan du använda följande kommando:

```azurecli
SUBNETID=$(az network vnet subnet show -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --query id -o tsv)
```

Du definierar den utgående typen för att använda UDR som redan finns i under nätet. Med den här konfigurationen kan AKS hoppa över installationen och IP-etableringen för belastningsutjämnaren.

> [!IMPORTANT]
> Mer information om utgående typ UDR inklusive begränsningar finns i [**utgående utgående typ UDR**](egress-outboundtype.md#limitations).


> [!TIP]
> Ytterligare funktioner kan läggas till i kluster distributionen, till exempel ett [**privat kluster**](private-clusters.md). 
>
> AKS-funktionen för [**tillåtna IP-intervall för API-servrar**](api-server-authorized-ip-ranges.md) kan läggas till för att begränsa åtkomsten till API-servern till endast brand väggens offentliga slut punkt. Funktionen auktoriserade IP-adressintervall anges i diagrammet som valfri. När du aktiverar funktionen auktoriserat IP-intervall för att begränsa åtkomsten till API-servern, måste utvecklarverktyg använda en hoppsida från brand väggens virtuella nätverk, eller så måste du lägga till alla utvecklares slut punkter i det auktoriserade IP-intervallet.

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

### <a name="enable-developer-access-to-the-api-server"></a>Aktivera åtkomst till utvecklare till API-servern

Om du har använt auktoriserade IP-intervall för klustret i föregående steg måste du lägga till verktygen för utvecklarverktyg i AKS-kluster listan över godkända IP-intervall för att få åtkomst till API-servern därifrån. Ett annat alternativ är att konfigurera en hoppsida med nödvändiga verktyg i ett separat undernät i brand väggens virtuella nätverk.

Lägg till en annan IP-adress till de godkända intervallen med följande kommando

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 Använd kommandot [AZ AKS get-credentials] [AZ-AKS-get-credentials] för att konfigurera `kubectl` för att ansluta till ditt nya Kubernetes-kluster. 

 ```azurecli
 az aks get-credentials -g $RG -n $AKSNAME
 ```

### <a name="deploy-a-public-service"></a>Distribuera en offentlig tjänst
Nu kan du börja exponera tjänster och distribuera program till det här klustret. I det här exemplet ska vi exponera en offentlig tjänst, men du kan också välja att exponera en intern tjänst via en [intern belastningsutjämnare](internal-lb.md).

![DNAT för offentlig tjänst](media/limit-egress-traffic/aks-create-svc.png)

Distribuera programmet Azure röstning app genom att kopiera yaml nedan till en fil med namnet `example.yaml` .

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

### <a name="add-a-dnat-rule-to-azure-firewall"></a>Lägg till en DNAT-regel i Azure-brandväggen

> [!IMPORTANT]
> När du använder Azure-brandväggen för att begränsa utgående trafik och skapar en användardefinierad väg (UDR) för att tvinga all utgående trafik, se till att du skapar en lämplig DNAT-regel i brand väggen för att tillåta inträngande trafik korrekt. Genom att använda Azure-brandväggen med en UDR avbryter du ingångs inställningen på grund av asymmetrisk routning. (Problemet uppstår om AKS-undernätet har en standard väg som går till brand väggens privata IP-adress, men du använder en offentlig belastningsutjämnare – ingress eller Kubernetes-tjänst av typen: LoadBalancer). I det här fallet tas den inkommande belastnings Utjämnings trafiken emot via dess offentliga IP-adress, men retur vägen går genom brand väggens privata IP-adress. Eftersom brand väggen är tillstånds känslig, släpps det returnerade paketet eftersom brand väggen inte är medveten om en etablerad session. Information om hur du integrerar Azure-brandväggen med ingångs-eller tjänst belastnings utjämning finns i [integrera Azure-brandväggen med azure standard Load Balancer](../firewall/integrate-lb.md).


Om du vill konfigurera inkommande anslutning måste en DNAT-regel skrivas till Azure-brandväggen. För att testa anslutningen till klustret definieras en regel för den offentliga IP-adressen för brand Väggs-frontend att dirigera till den interna IP-adressen som exponeras av den interna tjänsten.

Mål adressen kan anpassas eftersom det är porten i brand väggen som ska nås. Den översatta adressen måste vara IP-adressen för den interna belastningsutjämnaren. Den översatta porten måste vara den exponerade porten för din Kubernetes-tjänst.

Du måste ange den interna IP-adress som har tilldelats till belastningsutjämnaren som skapats av Kubernetes-tjänsten. Hämta adressen genom att köra:

```bash
kubectl get services
```

Den IP-adress som behövs visas i kolumnen extern IP-adress, som ser ut ungefär så här.

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

### <a name="validate-connectivity"></a>Verifiera anslutning

Gå till Azure Firewall-klientens IP-adress i en webbläsare för att verifiera anslutningen.

Du bör se AKS röstning-appen. I det här exemplet var brand väggens offentliga IP `52.253.228.132` .


![Skärm bild som visar en K S röstnings app med knappar för katter, hundar och återställning samt summor.](media/limit-egress-traffic/aks-vote.png)


### <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurs gruppen AKS om du vill rensa Azure-resurser.

```azurecli
az group delete -g $RG
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig vilka portar och adresser som du kan använda om du vill begränsa utgående trafik för klustret. Du såg också hur du skyddar utgående trafik med hjälp av Azure-brandväggen. 

Om det behövs kan du generalisera stegen ovan för att vidarebefordra trafiken till den önskade utgångs lösningen enligt dokumentationen för [utgående typ `userDefinedRoute` ](egress-outboundtype.md).

Om du vill begränsa hur poddar kommunicerar sinsemellan och East-West trafik begränsningar inom klustret, se [säker trafik mellan poddar med hjälp av nätverks principer i AKS][network-policy].

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[dev-spaces-service-tags]: ../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations
