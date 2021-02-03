---
title: Azure HDInsight-hantering IP-adresser
description: Lär dig vilka IP-adresser du måste tillåta inkommande trafik från, för att kunna konfigurera nätverks säkerhets grupper och användardefinierade vägar för virtuella nätverk med Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 08/11/2020
ms.openlocfilehash: 5f694dec6deffde9efb32fefbab91ae3b7a44a2c
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99490868"
---
# <a name="hdinsight-management-ip-addresses"></a>IP-adresser för HDInsight-hantering

Den här artikeln innehåller de IP-adresser som används av Azure HDInsight-tjänster för hälso tillstånd och hantering. Om du använder nätverks säkerhets grupper (NSG: er) eller användardefinierade vägar (UDR) kan du behöva lägga till några av de här IP-adresserna i listan över tillåtna objekt för inkommande nätverks trafik.

## <a name="introduction"></a>Introduktion
 
> [!Important]
> I de flesta fall kan du nu använda [service märken](hdinsight-service-tags.md) för nätverks säkerhets grupper, i stället för att manuellt lägga till IP-adresser. IP-adresser kommer inte att publiceras för nya Azure-regioner och de har bara publicerade tjänst etiketter. De statiska IP-adresserna för hanterings-IP-adresser kommer att bli föråldrade.

Om du använder nätverks säkerhets grupper (NSG: er) eller användardefinierade vägar (UDR) för att styra inkommande trafik till ditt HDInsight-kluster, måste du se till att klustret kan kommunicera med kritiska Azure-tjänster för hälso tillstånd och hantering.  Några av IP-adresserna för de här tjänsterna är landsspecifika och vissa av dem gäller för alla Azure-regioner. Du kan också behöva tillåta trafik från Azure DNS-tjänsten om du inte använder anpassad DNS.

Om du behöver IP-adresser för en region som inte visas här kan du använda [API: et för identifiering av service tag](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) för att hitta IP-adresser för din region. Om du inte kan använda API: t kan du hämta [JSON-filen för service tag](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) och söka efter önskad region.

HDInsight validerar de här reglerna med kluster skapande och skalning för att förhindra ytterligare fel. Om verifieringen inte är klar, går det inte att skapa och skala.

I följande avsnitt beskrivs de olika IP-adresser som måste vara tillåtna.

## <a name="azure-dns-service"></a>Azure DNS tjänst

Om du använder den Azure-angivna DNS-tjänsten ger du åtkomst till __168.63.129.16__ på port 53 för både TCP och UDP. Mer information finns i dokumentet [namn matchning för virtuella datorer och roll instanser](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) . Hoppa över det här steget om du använder anpassad DNS.

## <a name="health-and-management-services-all-regions"></a>Hälso-och hanterings tjänster: alla regioner

Tillåt trafik från följande IP-adresser för Azure HDInsight-hälso-och hanterings tjänster, som gäller för alla Azure-regioner:

| Källans IP-adress | Mål  | Riktning |
| ---- | ----- | ----- |
| 168.61.49.99 | \*: 443 | Inkommande |
| 23.99.5.239 | \*: 443 | Inkommande |
| 168.61.48.131 | \*: 443 | Inkommande |
| 138.91.141.162 | \*: 443 | Inkommande |

## <a name="health-and-management-services-specific-regions"></a>Hälso-och hanterings tjänster: vissa regioner

Tillåt trafik från IP-adresserna som anges för Azure HDInsight-hälso-och hanterings tjänsterna i den Azure-region där dina resurser finns:

> [!IMPORTANT]  
> Om den Azure-region som du använder inte visas i listan kan du använda funktionen [service tag](hdinsight-service-tags.md) för nätverks säkerhets grupper.

| Country | Region | Tillåtna käll-IP-adresser | Tillåtet mål | Riktning |
| ---- | ---- | ---- | ---- | ----- |
| Asien | Asien, östra | 23.102.235.122</br>52.175.38.134 | \*: 443 | Inkommande |
| &nbsp; | Sydostasien | 13.76.245.160</br>13.76.136.249 | \*: 443 | Inkommande |
| Australien | Australien, östra | 104.210.84.115</br>13.75.152.195 | \*: 443 | Inkommande |
| &nbsp; | Australien, sydöstra | 13.77.2.56</br>13.77.2.94 | \*: 443 | Inkommande |
| Brasilien | Brasilien, södra | 191.235.84.104</br>191.235.87.113 | \*: 443 | Inkommande |
| Kanada | Kanada, östra | 52.229.127.96</br>52.229.123.172 | \*: 443 | Inkommande |
| &nbsp; | Kanada, centrala | 52.228.37.66</br>52.228.45.222 |\*: 443 | Inkommande |
| Kina | Kina, norra | 42.159.96.170</br>139.217.2.219</br></br>42.159.198.178</br>42.159.234.157 | \*: 443 | Inkommande |
| &nbsp; | Kina, östra | 42.159.198.178</br>42.159.234.157</br></br>42.159.96.170</br>139.217.2.219 | \*: 443 | Inkommande |
| &nbsp; | Kina, norra 2 | 40.73.37.141</br>40.73.38.172 | \*: 443 | Inkommande |
| &nbsp; | Kina, östra 2 | 139.217.227.106</br>139.217.228.187 | \*: 443 | Inkommande |
| Europa | Europa, norra | 52.164.210.96</br>13.74.153.132 | \*: 443 | Inkommande |
| &nbsp; | Europa, västra| 52.166.243.90</br>52.174.36.244 | \*: 443 | Inkommande |
| Frankrike | Frankrike, centrala| 20.188.39.64</br>40.89.157.135 | \*: 443 | Inkommande |
| Tyskland | Tyskland, centrala | 51.4.146.68</br>51.4.146.80 | \*: 443 | Inkommande |
| &nbsp; | Tyskland, nordöstra | 51.5.150.132</br>51.5.144.101 | \*: 443 | Inkommande |
| Indien | Indien, centrala | 52.172.153.209</br>52.172.152.49 | \*: 443 | Inkommande |
| &nbsp; | Indien, södra | 104.211.223.67<br/>104.211.216.210 | \*: 443 | Inkommande |
| Japan | Japan, östra | 13.78.125.90</br>13.78.89.60 | \*: 443 | Inkommande |
| &nbsp; | Japan, västra | 40.74.125.69</br>138.91.29.150 | \*: 443 | Inkommande |
| Korea | Sydkorea, centrala | 52.231.39.142</br>52.231.36.209 | \*: 443 | Inkommande |
| &nbsp; | Sydkorea, södra | 52.231.203.16</br>52.231.205.214 | \*: 443 | Inkommande
| Storbritannien | Storbritannien, västra | 51.141.13.110</br>51.141.7.20 | \*: 443 | Inkommande |
| &nbsp; | Storbritannien, södra | 51.140.47.39</br>51.140.52.16 | \*: 443 | Inkommande |
| USA | Central US | 13.89.171.122</br>13.89.171.124 | \*: 443 | Inkommande |
| &nbsp; | East US | 13.82.225.233</br>40.71.175.99 | \*: 443 | Inkommande |
| &nbsp; | USA, östra 2 | 20.44.16.8/29</br>20.49.102.48/29 | \*: 443 | Inkommande |
| &nbsp; | USA, norra centrala | 157.56.8.38</br>157.55.213.99 | \*: 443 | Inkommande |
| &nbsp; | USA, västra centrala | 52.161.23.15</br>52.161.10.167 | \*: 443 | Inkommande |
| &nbsp; | USA, västra | 13.64.254.98</br>23.101.196.19 | \*: 443 | Inkommande |
| &nbsp; | USA, västra 2 | 52.175.211.210</br>52.175.222.222 | \*: 443 | Inkommande |
| &nbsp; | Förenade Arabemiraten, norra | 65.52.252.96</br>65.52.252.97 | \*: 443 | Inkommande |
| &nbsp; | Förenade Arabemiraten Central | 20.37.76.96</br>20.37.76.99 | \*: 443 | Inkommande |

Information om vilka IP-adresser som ska användas för Azure Government finns i dokumentet [Azure Government information + analys](../azure-government/compare-azure-government-global-azure.md) .

Mer information finns i [kontrol lera nätverks trafik](./control-network-traffic.md).

Om du använder användardefinierade vägar (UDR) ska du ange en väg och tillåta utgående trafik från det virtuella nätverket till ovanstående IP-adresser med nästa hopp inställt på "Internet".

## <a name="next-steps"></a>Nästa steg

* [Skapa virtuella nätverk för Azure HDInsight-kluster](hdinsight-create-virtual-network.md)
* [Service tag gen för nätverks säkerhets gruppen (NSG) för Azure HDInsight](hdinsight-service-tags.md)
