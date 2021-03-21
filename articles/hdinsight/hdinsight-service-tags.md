---
title: Service tag gen för nätverks säkerhets gruppen (NSG) för Azure HDInsight
description: 'Använd HDInsight-tjänstens taggar för att tillåta inkommande trafik till ditt kluster från noden hälso-och hanterings tjänster, utan att lägga till IP-adresser till din NSG: er.'
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 08/11/2020
ms.openlocfilehash: d17d067b88add3006bc5c7fb10caa6b80a80a827
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98931772"
---
# <a name="nsg-service-tags-for-azure-hdinsight"></a>NSG service-taggar för Azure HDInsight

Azure HDInsight Service-taggar för nätverks säkerhets grupper (NSG: er) är grupper med IP-adresser för hälso-och hanterings tjänster. Dessa grupper bidrar till att minimera komplexiteten vid skapande av säkerhets regler. [Service Taggar](../virtual-network/network-security-groups-overview.md#service-tags) tillåter inkommande trafik från vissa IP-adresser utan att ange var och en av [hanterings-IP-adresserna](hdinsight-management-ip-addresses.md) i din NSG: er.

HDInsight-tjänsten hanterar dessa service märken. Du kan inte skapa en egen service tag eller ändra en befintlig tagg. Microsoft hanterar de adressprefix som matchar tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

Om du vill använda en viss region och service tag-numret inte har dokumenterats på den här sidan kan du använda [API: et för identifiering av service tag](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) för att hitta din service tag. Du kan också hämta [JSON-filen för service tag](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) och söka efter önskad region.

## <a name="get-started-with-service-tags"></a>Kom igång med service märken

Du har två alternativ för att använda tjänst Taggar i dina nätverks säkerhets grupper:

- **Använd en enda global HDInsight-Service tag**: det här alternativet öppnar det virtuella nätverket för alla IP-adresser som HDInsight-tjänsten använder för att övervaka kluster i alla regioner. Det här alternativet är den enklaste metoden, men det kanske inte är lämpligt om du har begränsade säkerhets krav.

- **Använd flera regionala service Taggar**: med det här alternativet öppnas det virtuella nätverket till de IP-adresser som HDInsight använder i den aktuella regionen. Om du använder flera regioner måste du dock lägga till flera service märken i det virtuella nätverket.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Använd en enda global HDInsight Service-tagg

Det enklaste sättet att börja använda service märken med ditt HDInsight-kluster är att lägga till den globala taggen `HDInsight` i en NSG-regel.

1. Välj din nätverks säkerhets grupp från [Azure Portal](https://portal.azure.com/).

1. Under **Inställningar** väljer du **inkommande säkerhets regler** och väljer sedan **+ Lägg till**.

1. I list rutan **källa** väljer du **service tag**.

1. I list rutan **käll tjänst tag** väljer du **HDInsight**.

    ![Lägg till en service tag från Azure Portal](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

Den här taggen innehåller IP-adresserna för hälso-och hanterings tjänster för alla regioner där HDInsight är tillgängligt. Taggen ser till att klustret kan kommunicera med nödvändiga hälso-och hanterings tjänster oavsett var de skapas.

## <a name="use-regional-hdinsight-service-tags"></a>Använda regionala HDInsight Service-Taggar

Om alternativet för den globala taggen inte fungerar eftersom du behöver mer restriktiva behörigheter kan du bara tillåta de service märken som gäller för din region. Det kan finnas flera tjänst etiketter, beroende på den region där klustret skapas.

Om du vill ta reda på vilka service märken som ska läggas till för din region läser du följande avsnitt i artikeln.

### <a name="use-a-single-regional-service-tag"></a>Använd en enda regional service tag

Om klustret finns i en region som anges i den här tabellen, behöver du bara lägga till en enda regional service-tagg till din NSG.

| Country | Region | Tjänsttagg |
| ---- | ---- | ---- |
| Australien | Australien, östra | HDInsight. AustraliaEast |
| &nbsp; | Australien, sydöstra | HDInsight. AustraliaSoutheast |
| &nbsp; | Australien, centrala | HDInsight. AustraliaCentral |
| Kina | Kina, östra 2 | HDInsight. ChinaEast2 |
| &nbsp; | Kina, norra 2 | HDInsight. ChinaNorth2 |
| USA | USA, norra centrala | HDInsight. Usanorracentrala |
| &nbsp; | USA, västra 2 | HDInsight. WestUS2 |
| &nbsp; | USA, västra centrala | HDInsight. WestCentralUS |
| Kanada | Kanada, östra | HDInsight. Indien |
| Brasilien | Brasilien, södra | HDInsight. centrala |
| Korea | Sydkorea, centrala | HDInsight. centrala |
| &nbsp; | Sydkorea, södra | HDInsight. Koreasödra |
| Indien | Indien, centrala | HDInsight. Kanada |
| &nbsp; | Indien, södra | HDInsight. Australienöstra |
| Japan | Japan, västra | HDInsight. Japanvästra |
| Frankrike | Frankrike, centrala| HDInsight. FranceCentral |
| Storbritannien | Storbritannien, södra | HDInsight. UKSouth |
| Azure Government | USDoD Central | HDInsight. USDoDCentral |
| &nbsp; | USGov Texas | HDInsight. USGovTexas |
| &nbsp; | UsDoD, öst | HDInsight. USDoDEast |
| &nbsp; | USGov Arizona | HDInsight. USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>Använda flera regionala tjänst etiketter

Om den region där klustret skapades inte visas i tabellen ovan måste du tillåta flera regionala service märken. Behovet av att använda mer än ett beror på olikheter i placeringen av resurs leverantörer i de olika regionerna.

De återstående regionerna är indelade i grupper baserat på vilka regionala tjänst Taggar de använder.

#### <a name="group-1"></a>Grupp 1

Om klustret har skapats i någon av regionerna i följande tabell kan du tillåta service-taggarna `HDInsight.WestUS` och `HDInsight.EastUS` . Dessutom anges den regionala service tag gen. Regioner i det här avsnittet kräver tre tjänst etiketter.

Om klustret till exempel har skapats i `East US 2` regionen måste du lägga till följande service Taggar i din nätverks säkerhets grupp:

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| Country | Region | Tjänsttagg |
| ---- | ---- | ---- |
| USA | USA, östra 2 | HDInsight. EastUS2 |
| &nbsp; | Central US | HDInsight. centrala |
| &nbsp; | NorthCentral oss | HDInsight. Usanorracentrala |
| &nbsp; | USA, södra centrala | HDInsight. Usasödracentrala |
| &nbsp; | East US | HDInsight. öst |
| &nbsp; | USA, västra | HDInsight. väst |
| Japan | Japan, östra | HDInsight. Japanöstra |
| Europa | Europa, norra | HDInsight. Europanorra |
| &nbsp; | Europa, västra| HDInsight. WestEurope |
| Asien | Asien, östra | HDInsight. Asienöstra |
| &nbsp; | Sydostasien | HDInsight. Sydostasien |
| Australien | Australien, östra | HDInsight. AustraliaEast |

#### <a name="group-2"></a>Grupp 2

Kluster i regionerna *Kina, norra* och *Kina, östra* behöver tillåta två service märken: `HDInsight.ChinaNorth` och `HDInsight.ChinaEast` .

#### <a name="group-3"></a>Grupp 3

Kluster i regionerna *US gov, Iowa* och *US gov, Virginia* behöver tillåta två service märken: `HDInsight.USGovIowa` och `HDInsight.USGovVirginia` .

#### <a name="group-4"></a>Grupp 4

Kluster i regionerna i Tyskland, *centrala* och *Tyskland nordöstra* måste tillåta två service märken: `HDInsight.GermanyCentral` och `HDInsight.GermanyNortheast` .

## <a name="next-steps"></a>Nästa steg

- [Nätverks säkerhets grupper: service Taggar](../virtual-network/network-security-groups-overview.md#security-rules)
- [Skapa virtuella nätverk för Azure HDInsight-kluster](hdinsight-create-virtual-network.md)