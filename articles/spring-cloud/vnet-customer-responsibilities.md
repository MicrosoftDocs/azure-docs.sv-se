---
title: Kund ansvar som kör Azure våren Cloud i VNet
description: I den här artikeln beskrivs kund ansvar som kör Azure våren Cloud i VNet.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 91c8834b48625aac0f279f84648d374df15fbdd0
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285402"
---
# <a name="customer-responsibilities-for-running-azure-spring-cloud-in-vnet"></a>Kund ansvar för att köra Azure våren Cloud i VNET
Det här dokumentet innehåller specifikationer för användning av Azure våren Cloud i ett virtuellt nätverk.

När Azure våren Cloud distribueras i det virtuella nätverket har det utgående beroenden för tjänster utanför det virtuella nätverket. I hanterings-och drift syfte måste Azure våren-molnet komma åt vissa portar och fullständiga domän namn (FQDN). Dessa slut punkter krävs för att kommunicera med Azures moln hanterings plan och för att hämta och installera kärn Kubernetes-kluster komponenter och säkerhets uppdateringar.

Som standard har Azure våren Cloud obegränsad utgående Internet åtkomst (utgående). Den här nivån av nätverks åtkomst innebär att program som du kör för att få åtkomst till externa resurser efter behov. Om du vill begränsa utgående trafik måste ett begränsat antal portar och adresser vara tillgängliga för underhålls aktiviteter. Den enklaste lösningen för att säkra utgående adresser är användning av en brand Väggs enhet som kan styra utgående trafik baserat på domän namn. Azure-brandväggen kan till exempel begränsa utgående HTTP-och HTTPS-trafik baserat på målets FQDN. Du kan också konfigurera önskade brand Väggs-och säkerhets regler för att tillåta dessa obligatoriska portar och adresser.

## <a name="azure-spring-cloud-resource-requirements"></a>Resurs krav för Azure våren Cloud 

Följande är en lista över resurs krav för Azure våren Cloud Services. Som ett allmänt krav bör du inte ändra resurs grupper som skapats av Azure våren Cloud och de underliggande nätverks resurserna.
- Ändra inte resurs grupper som skapats och ägs av Azure våren Cloud.
  - Som standard heter dessa resurs grupper som AP-SVC-rt_ [tjänst instans-namn]_[region] * och AP_[tjänst instans-namn] _ [region] *.
- Ändra inte undernät som används av Azure våren Cloud.
- Skapa inte fler än en Azure våren Cloud Service-instans i samma undernät.
- När du använder en brand vägg för att styra trafiken ska du *inte* Blockera följande utgående trafik till moln komponenter i Azure våren som hanterar, underhåller och stöder tjänst instansen.

## <a name="azure-spring-cloud-network-requirements"></a>Nätverks krav för Azure våren Cloud

  | Destinations slut punkt | Port | Användning | Anteckning |
  |------|------|------|------|
  | *: 1194 *eller* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -AzureCloud: 1194 | UDP: 1194 | Underliggande hantering av Kubernetes-kluster. | |
  | *: 443 *eller* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -AzureCloud: 443 | TCP: 443 | Azure våren Cloud Service Management. | Information om tjänst instansen "requiredTraffics" kan vara känd i resurs nytto lasten under avsnittet "networkProfile". |
  | *: 9000 *eller* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -AzureCloud: 9000 | TCP: 9000 | Underliggande hantering av Kubernetes-kluster. |
  | *: 123 *eller* NTP.Ubuntu.com:123 | UDP: 123 | NTP-tidssynkronisering på Linux-noder. | |
  | *. azure.io:443 *eller* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -AzureContainerRegistry: 443 | TCP: 443 | Azure Container Registry. | Kan ersättas genom att aktivera *Azure Container Registry* [tjänstens slut punkt i virtuellt nätverk](../virtual-network/virtual-network-service-endpoints-overview.md). |
  | *. core.windows.net:443 och *. core.windows.net:445 *eller* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -Storage: 443 och lagring: 445 | TCP: 443, TCP: 445 | Azure File Storage | Kan ersättas genom att aktivera *Azure Storage* [tjänstens slut punkt i virtuellt nätverk](../virtual-network/virtual-network-service-endpoints-overview.md). |
  | *. servicebus.windows.net:443 *eller* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -EventHub: 443 | TCP: 443 | Azure Event Hub. | Kan ersättas genom att aktivera *Azure Event Hubs* [service-slutpunkten i det virtuella nätverket](../virtual-network/virtual-network-service-endpoints-overview.md). |
  

## <a name="azure-spring-cloud-fqdn-requirementsapplication-rules"></a>Krav för Azure våren Cloud FQDN/applikations regler

Azure-brandväggen tillhandahåller FQDN-taggen **AzureKubernetesService** för att förenkla följande konfigurationer:

  | Mål-FQDN | Port | Användning |
  |------|------|------|
  | *. azmk8s.io | HTTPS: 443 | Underliggande hantering av Kubernetes-kluster. |
  | <i>mcr.microsoft.com</i> | HTTPS: 443 | Microsoft Container Registry (MCR). |
  | *. cdn.mscr.io | HTTPS: 443 | MCR-lagring som backas upp av Azure CDN. |
  | *. data.mcr.microsoft.com | HTTPS: 443 | MCR-lagring som backas upp av Azure CDN. |
  | <i>management.azure.com</i> | HTTPS: 443 | Underliggande hantering av Kubernetes-kluster. |
  | <i>* login.microsoftonline.com</i> | HTTPS: 443 | Azure Active Directory autentisering. |
  | <i>* login.microsoft.com</i> | HTTPS: 443 | Azure Active Directory autentisering. |
  |<i>packages.microsoft.com</i>    | HTTPS: 443 | Microsoft-paketets lagrings plats. |
  | <i>acs-mirror.azureedge.net</i> | HTTPS: 443 | Lagrings plats som krävs för att installera nödvändiga binärfiler som Kubernetes och Azure CNI. |
  | *mscrl.microsoft.com* | HTTPS: 80 | Obligatoriska sökvägar för Microsoft-certifikat kedjan. |
  | *crl.microsoft.com* | HTTPS: 80 | Obligatoriska sökvägar för Microsoft-certifikat kedjan. |
  | *crl3.digicert.com* | HTTPS: 80 | Sök vägar för SSL-certifikat från tredje part. |
  
## <a name="azure-spring-cloud-optional-fqdn-for-third-party-application-performance-management"></a>Azure våren Cloud valfria FQDN för program prestanda hantering från tredje part

Azure-brandväggen tillhandahåller FQDN-taggen **AzureKubernetesService** för att förenkla följande konfigurationer:

  | Mål-FQDN | Port | Användning                                                          |
  | ---------------- | ---- | ------------------------------------------------------------ |
  | Collector *. newrelic. com | TCP: 443/80 | De nätverk som krävs för nya Relic APM-agenter från regionen USA, även i [nätverk med APM-agenter](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents). |
  | Collector *. eu01. nr-data.net | TCP: 443/80 | De nätverk som krävs för nya Relic APM-agenter från EU-region, kan också se [nätverk med APM-agenter](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents). |

## <a name="see-also"></a>Se även
* [Få åtkomst till ditt program i ett privat nätverk](access-app-virtual-network.md)
* [Exponera appar med hjälp av Application Gateway och Azure-brandväggen](expose-apps-gateway-azure-firewall.md)
