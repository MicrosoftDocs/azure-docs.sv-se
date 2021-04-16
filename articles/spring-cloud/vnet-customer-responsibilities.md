---
title: Kundansvar som körs Azure Spring Cloud i vnet
description: I den här artikeln beskrivs kundansvar som Azure Spring Cloud i vnet.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: devx-track-java
ms.openlocfilehash: a6b444092ec4e3588564a3f902b49c4ed3dc5fe5
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376791"
---
# <a name="customer-responsibilities-for-running-azure-spring-cloud-in-vnet"></a>Kundens ansvar för att Azure Spring Cloud i VNET
Det här dokumentet innehåller specifikationer för användning av Azure Spring Cloud i ett virtuellt nätverk.

När Azure Spring Cloud distribueras i ditt virtuella nätverk har det utgående beroenden av tjänster utanför det virtuella nätverket. För hantering och drift måste Azure Spring Cloud komma åt vissa portar och fullständigt kvalificerade domännamn (FQDN). Dessa slutpunkter krävs för att kommunicera med Azure Spring Cloud-hanteringsplanet och för att ladda ned och installera kärnkomponenter för Kubernetes-kluster och säkerhetsuppdateringar.

Som standard Azure Spring Cloud obegränsad utgående (utgående) Internetåtkomst. Med den här nivån av nätverksåtkomst kan program som du kör komma åt externa resurser efter behov. Om du vill begränsa utgående trafik måste ett begränsat antal portar och adresser vara tillgängliga för underhållsaktiviteter. Den enklaste lösningen för att skydda utgående adresser är att använda en brandväggsenhet som kan styra utgående trafik baserat på domännamn. Azure Firewall kan till exempel begränsa utgående HTTP- och HTTPS-trafik baserat på målets FQDN. Du kan också konfigurera dina önskade brandväggs- och säkerhetsregler för att tillåta dessa portar och adresser som krävs.

## <a name="azure-spring-cloud-resource-requirements"></a>Azure Spring Cloud resurskrav 

Följande är en lista över resurskrav för Azure Spring Cloud tjänster. Som ett allmänt krav bör du inte ändra resursgrupper som skapats av Azure Spring Cloud och de underliggande nätverksresurserna.
- Ändra inte resursgrupper som skapats och ägs av Azure Spring Cloud.
  - Som standard namnges dessa resursgrupper som ap-svc-rt_[SERVICE-INSTANCE-NAME]_[REGION]* och ap_[SERVICE-INSTANCE-NAME]_[REGION]*.
- Ändra inte undernät som används av Azure Spring Cloud.
- Skapa inte fler än en Azure Spring Cloud tjänstinstans i samma undernät.
- När du använder en  brandvägg för att styra trafik ska du inte blockera följande utgående trafik till Azure Spring Cloud-komponenter som använder, underhåller och stöder tjänstinstansen.

## <a name="azure-spring-cloud-network-requirements"></a>Azure Spring Cloud nätverkskrav

  | Målslutpunkt | Port | Användning | Anteckning |
  |------|------|------|------|
  | *:1194 *Eller* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – AzureCloud:1194 | UDP:1194 | Underliggande Kubernetes-klusterhantering. | |
  | *:443 *Eller* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – AzureCloud:443 | TCP:443 | Azure Spring Cloud Service Management. | Information om tjänstinstansen "requiredTraffics" kan vara känd i resursnyttolasten under avsnittet "networkProfile". |
  | *:9000 *Or* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – AzureCloud:9000 | TCP:9000 | Underliggande Kubernetes-klusterhantering. |
  | *:123 *Eller* ntp.ubuntu.com:123 | UDP:123 | NTP-tidssynkronisering på Linux-noder. | |
  | *.azure.io:443 Or  [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – AzureContainerRegistry:443 | TCP:443 | Azure Container Registry. | Kan ersättas genom att aktivera Azure Container Registry  [tjänstslutpunkt i det virtuella nätverket](../virtual-network/virtual-network-service-endpoints-overview.md). |
  | *.core.windows.net:443 och *.core.windows.net:445 *Or* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – Storage:443 och Storage:445 | TCP:443, TCP:445 | Azure File Storage | Kan ersättas genom att aktivera *Azure Storage* [tjänstslutpunkt i det virtuella nätverket](../virtual-network/virtual-network-service-endpoints-overview.md). |
  | *.servicebus.windows.net:443 Or  [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – EventHub:443 | TCP:443 | Azure Event Hub. | Kan ersättas genom att aktivera Azure Event Hubs  [tjänstslutpunkt i det virtuella nätverket](../virtual-network/virtual-network-service-endpoints-overview.md). |
  

## <a name="azure-spring-cloud-fqdn-requirementsapplication-rules"></a>Azure Spring Cloud FQDN-krav/programregler

Azure Firewall innehåller FQDN-taggen **AzureKubernetesService** för att förenkla följande konfigurationer:

  | Mål-FQDN | Port | Användning |
  |------|------|------|
  | *.azmk8s.io | HTTPS:443 | Underliggande Kubernetes-klusterhantering. |
  | <i>mcr.microsoft.com</i> | HTTPS:443 | Microsoft Container Registry (MCR). |
  | *.cdn.mscr.io | HTTPS:443 | MCR-lagring backas upp av Azure CDN. |
  | *.data.mcr.microsoft.com | HTTPS:443 | MCR-lagring backas upp av Azure CDN. |
  | <i>management.azure.com</i> | HTTPS:443 | Underliggande Kubernetes-klusterhantering. |
  | <i>*login.microsoftonline.com</i> | HTTPS:443 | Azure Active Directory autentisering. |
  | <i>*login.microsoft.com</i> | HTTPS:443 | Azure Active Directory autentisering. |
  |<i>packages.microsoft.com</i>    | HTTPS:443 | Lagringsplats för Microsoft-paket. |
  | <i>acs-mirror.azureedge.net</i> | HTTPS:443 | Lagringsplats som krävs för att installera nödvändiga binärfiler som kubenet och Azure CNI. |
  | *mscrl.microsoft.com* | HTTPS:80 | Sökvägar för Microsoft-certifikatkedjan som krävs. |
  | *crl.microsoft.com* | HTTPS:80 | Sökvägar för Microsoft-certifikatkedjan som krävs. |
  | *crl3.digicert.com* | HTTPS:80 | Sökvägar för SSL-certifikatkedjan från tredje part. |
  
## <a name="azure-spring-cloud-optional-fqdn-for-third-party-application-performance-management"></a>Azure Spring Cloud ett valfritt FQDN för prestandahantering av program från tredje part

Azure Firewall innehåller FQDN-taggen **AzureKubernetesService** för att förenkla följande konfigurationer:

  | Mål-FQDN | Port | Användning                                                          |
  | ---------------- | ---- | ------------------------------------------------------------ |
  | collector*.newrelic.com | TCP:443/80 | Obligatoriska nätverk för New Relic APM-agenter från regionen USA, se även [APM Agent Networks](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents). |
  | collector*.eu01.nr-data.net | TCP:443/80 | Obligatoriska nätverk för New Relic APM-agenter från EU-regionen, se även [APM Agent Networks](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents). |

## <a name="see-also"></a>Se även
* [Få åtkomst till ditt program i ett privat nätverk](access-app-virtual-network.md)
* [Exponera appar med Application Gateway och Azure Firewall](expose-apps-gateway-azure-firewall.md)
