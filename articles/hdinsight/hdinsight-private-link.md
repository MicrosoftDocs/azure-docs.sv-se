---
title: Skydda och isolera Azure HDInsight-kluster med privat länk (förhands granskning)
description: Lär dig hur du isolerar Azure HDInsight-kluster i ett virtuellt nätverk med hjälp av Azure Private Link.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: bc7834a0f8272da3f8954c7dd9f3e18163795cba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939366"
---
# <a name="secure-and-isolate-azure-hdinsight-clusters-with-private-link-preview"></a>Skydda och isolera Azure HDInsight-kluster med privat länk (förhands granskning)

I Azure HDInsights [standard arkitektur för virtuella nätverk](./hdinsight-virtual-network-architecture.md)kommunicerar HDInsight Resource Provider (RP) med klustret med hjälp av offentliga IP-adresser. Vissa scenarier kräver fullständig nätverks isolering utan användning av offentliga IP-adresser. I den här artikeln får du lära dig mer om de avancerade kontroller som du kan använda för att skapa ett privat HDInsight-kluster. Information om hur du begränsar trafik till och från klustret utan fullständig nätverks isolering finns i [kontrol lera nätverks trafik i Azure HDInsight](./control-network-traffic.md).

Du kan skapa privata HDInsight-kluster genom att konfigurera vissa nätverks egenskaper i en Azure Resource Manager-mall (ARM). Det finns två egenskaper som du använder för att skapa privata HDInsight-kluster:

* Ta bort offentliga IP-adresser genom `resourceProviderConnection` att ange till utgående.
* Aktivera den privata Azure-länken och Använd [privata slut punkter](../private-link/private-endpoint-overview.md) genom att ställa in `privateLink` på aktive rad.

## <a name="remove-public-ip-addresses"></a>Ta bort offentliga IP-adresser

Som standard använder HDInsight RP en *inkommande* anslutning till klustret med hjälp av offentliga IP-adresser. När `resourceProviderConnection` nätverks egenskapen är inställd på *utgående*, återställer den anslutningarna till HDInsight RP så att anslutningarna alltid initieras inifrån klustret i RP. Utan en inkommande anslutning behöver inte inkommande tjänst etiketter eller offentliga IP-adresser.

De grundläggande belastningsutjämnare som används i standard arkitekturen för virtuella nätverk tillhandahåller automatiskt offentliga NAT (Network Address Translation) för att få åtkomst till nödvändiga utgående beroenden, till exempel HDInsight RP. Om du vill begränsa utgående anslutning till det offentliga Internet kan du [Konfigurera en brand vägg](./hdinsight-restrict-outbound-traffic.md), men det är inget krav.

Genom `resourceProviderConnection` att konfigurera till utgående kan du också komma åt klusterbaserade resurser, till exempel Azure Data Lake Storage Gen2 eller externa metastores, med hjälp av privata slut punkter. Användning av privata slut punkter för dessa resurser är inte obligatoriskt, men om du planerar att ha privata slut punkter för dessa resurser måste du konfigurera de privata slut punkterna och DNS-posterna `before` som du skapar HDInsight-klustret. Vi rekommenderar att du skapar och tillhandahåller alla externa SQL-databaser som du behöver, till exempel Apache Ranger, Ambari, Oozie och Hive metastores, när klustret skapas. Kravet är att alla dessa resurser måste vara tillgängliga inifrån kluster under nätet, antingen via sin egen privata slut punkt eller på annat sätt.

Det finns inte stöd för att använda privata slut punkter för Azure Key Vault. Om du använder Azure Key Vault för CMK-kryptering i vila måste Azure Key Vault-slutpunkten vara tillgänglig från HDInsight-undernätet utan privat slut punkt.

Följande diagram visar hur en potentiell virtuell nätverks arkitektur för HDInsight kan se ut som när `resourceProviderConnection` har angetts till utgående:

:::image type="content" source="media/hdinsight-private-link/outbound-resource-provider-connection-only.png" alt-text="Diagram över HDInsight-arkitektur med en utgående anslutning till en resurs leverantör":::

När du har skapat klustret bör du ställa in rätt DNS-matchning. Följande kanoniska namn DNS-post (CNAME) skapas i den Azure-hanterade offentliga DNS-zonen: `azurehdinsight.net` .

```dns
<clustername>    CNAME    <clustername>-int
```

För att få åtkomst till klustret med hjälp av kluster-FQDN: er kan du antingen använda den interna belastnings utjämningens privata IP-adresser direkt eller använda din egen Privat DNS zon för att åsidosätta kluster slut punkterna efter behov. Du kan till exempel ha en Privat DNS zon, `azurehdinsight.net` . och Lägg till dina privata IP-adresser efter behov:

```dns
<clustername>        A   10.0.0.1
<clustername-ssh>    A   10.0.0.2
```

## <a name="enable-private-link"></a>Aktivera privat länk

Privat länk, som är inaktive rad som standard, kräver omfattande nätverks kunskaper för att konfigurera användardefinierade vägar (UDR) och brand Väggs regler korrekt innan du skapar ett kluster. Det är valfritt att använda den här inställningen, men den är bara tillgänglig när `resourceProviderConnection` nätverks egenskapen är inställd på *utgående* enligt beskrivningen i föregående avsnitt.

När `privateLink` är inställt på *Enable* skapas interna [standardload Balancer](../load-balancer/load-balancer-overview.md) (SLB) och en Azure Private Link-tjänst tillhandahålls för varje SLB. Med den privata länk tjänsten kan du komma åt HDInsight-klustret från privata slut punkter.

Standard belastnings utjämning ger inte automatiskt den [offentliga utgående NAT](../load-balancer/load-balancer-outbound-connections.md) som grundläggande belastnings utjämning. Du måste ange en egen NAT-lösning, till exempel [Virtual Network NAT](../virtual-network/nat-overview.md) eller en [brand vägg](./hdinsight-restrict-outbound-traffic.md), för utgående beroenden. Ditt HDInsight-kluster behöver fortfarande åtkomst till dess utgående beroenden. Om dessa utgående beroenden inte är tillåtna kan skapandet av klustret Miss lyckas.

### <a name="prepare-your-environment"></a>Förbered din miljö

För att det ska gå att skapa privata länk tjänster måste du uttryckligen [inaktivera nätverks principer för tjänsten för privata länkar](../private-link/disable-private-link-service-network-policy.md).

Följande diagram visar ett exempel på den nätverks konfiguration som krävs innan du skapar ett kluster. I det här exemplet [tvingas](../firewall/forced-tunneling.md) all utgående trafik till Azure-brandväggen med hjälp av UDR och de nödvändiga utgående beroendena ska vara "tillåtna" i brand väggen innan ett kluster skapas. För Enterprise Security Package kluster kan nätverks anslutningen till Azure Active Directory Domain Services tillhandahållas av VNet-peering.

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="Diagram över privat länk miljö innan kluster skapas":::

När du har konfigurerat nätverket kan du skapa ett kluster med anslutning till utgående resurs leverantör och privat länk aktiverat, som du ser i följande bild. I den här konfigurationen finns inga offentliga IP-adresser och privata länk tjänster etablerade för varje standard belastningsutjämnare.

:::image type="content" source="media/hdinsight-private-link/after-cluster-creation.png" alt-text="Diagram över privat länk miljö när klustret har skapats":::

### <a name="access-a-private-cluster"></a>Åtkomst till ett privat kluster

För att få åtkomst till privata kluster, kan du använda den interna belastnings Utjämnings tjänsten privata IP-adresser direkt, eller så kan du använda privata länkar DNS-tillägg och privata slut punkter. När `privateLink` inställningen är aktive rad kan du skapa egna privata slut punkter och konfigurera DNS-matchning via privata DNS-zoner.

De privata länk poster som skapats i den Azure-hanterade offentliga DNS-zonen `azurehdinsight.net` är följande:

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```

Följande bild visar ett exempel på de privata DNS-poster som krävs för att komma åt klustret från ett virtuellt nätverk som inte är peer-kopplat eller som inte har direkt insikter om belastnings utjämning för klustret. Du kan använda Azures privata zon för att åsidosätta `*.privatelink.azurehdinsight.net` FQDN: er och matcha dina egna privata slut punkter IP-adresser.

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="Diagram över arkitektur för privata länkar":::

## <a name="how-to-create-clusters"></a>Hur skapar jag kluster?
### <a name="use-arm-template-properties"></a>Använd egenskaper för ARM-mall

Följande JSON-kodfragment innehåller de två nätverks egenskaper som du måste konfigurera i ARM-mallen för att skapa ett privat HDInsight-kluster.

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound",
    "privateLink": "Enabled" | "Disabled"
}
```

En fullständig mall med många av säkerhets funktionerna för HDInsight-företag, inklusive privat länk, finns i [säkerhets mal len för HDInsight Enterprise](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template).

### <a name="use-azure-powershell"></a>Använda Azure PowerShell

Om du vill använda PowerShell, se exemplet [här](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature).

### <a name="use-azure-cli"></a>Använda Azure CLI
Information om hur du använder Azure CLI finns i exemplet [här](/cli/azure/hdinsight#az_hdinsight_create-examples).

## <a name="next-steps"></a>Nästa steg

* [Enterprise Security Package för Azure HDInsight](enterprise-security-package.md)
* [Allmän information och rikt linjer för företags säkerhet i Azure HDInsight](./domain-joined/general-guidelines.md)
