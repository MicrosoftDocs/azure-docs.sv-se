---
title: Skapa Azure Cosmos-behållare och databaser i autoskalning-läge.
description: Lär dig mer om fördelarna, användnings fall och hur du etablerar Azure Cosmos-databaser och behållare i autoskalning-läge.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/11/2020
ms.custom: seo-nov-2020
ms.openlocfilehash: ba0dd347c4ee2cb41b34c2fc34f1848a7295dc3a
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368672"
---
# <a name="create-azure-cosmos-containers-and-databases-with-autoscale-throughput"></a>Skapa Azure Cosmos-behållare och databaser med autoskalning av data flöde
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

I Azure Cosmos DB kan du konfigurera antingen standard (manuell) eller autoskalning av allokerat data flöde på dina databaser och behållare. Med autoskalning av allokerat data flöde i Azure Cosmos DB kan du **skala data flödet (ru/s) i databasen eller behållaren automatiskt och genast**. Data flödet skalas utifrån användningen, utan att det påverkar tillgänglighet, svars tid, data flöde eller prestanda för arbets belastningen.

Autoskalning av allokerat data flöde passar bra för verksamhets kritiska arbets belastningar som har varierande eller oförutsägbara trafik mönster, och kräver service avtal för hög prestanda och skalning. I den här artikeln beskrivs fördelarna med och användningsfallen för autoskalningsetablerat dataflöde.

## <a name="benefits-of-autoscale"></a>Fördelar med autoskalning

Azure Cosmos-databaser och behållare som är konfigurerade med autoskalning av allokerat data flöde har följande fördelar:

* **Enkel:** Autoskalning tar bort komplexiteten vid hantering av RU/s med anpassat skript eller manuellt skalnings kapacitet. 

* **Skalbarhet:** Databaser och behållare skalar automatiskt det etablerade data flödet vid behov. Det uppstår inget avbrott i klient anslutningar, program eller påverkan på Azure Cosmos DB service avtal.

* **Kostnads effektiv:** Med automatisk skalning kan du optimera din RU/s-användning och kostnads användning genom att skala ned när den inte används. Du betalar bara för de resurser som dina arbets belastningar behöver per timme. För alla timmar under en månad, om du ställer in autoskalning Max RU/s (Tmax) och använder hela beloppet tmax för 66% av timmarna eller mindre, sparar du med autoskalning. Mer information finns i artikeln om [att välja mellan standard (manuell) och autoskalning av allokerat data flöde](how-to-choose-offer.md) .

* **Hög tillgänglighet:** Databaser och behållare som använder autoskalning använder samma globalt distribuerade, feltoleranta Azure Cosmos DB server del för att säkerställa data hållbarhet och hög tillgänglighet.

## <a name="use-cases-of-autoscale"></a>Använda fall av autoskalning

Användnings fall för autoskalning är:

* **Varierande eller oförutsägbara arbets belastningar:** När arbets belastningarna har varierande eller oförutsägbara toppar i användning, hjälper autoskalning till att automatiskt skala upp och ned baserat på användning. Exempel omfattar detalj handels webbplatser som har olika trafik mönster beroende på säsongs beroende; IOT-arbetsbelastningar som har toppar vid olika tidpunkter under dagen. branschspecifika program som ser högsta användning några gånger i månaden eller år, med mera. Med autoskalning behöver du inte längre etablera för topp-eller genomsnitts kapacitet manuellt. 

* **Nya program:** Om du utvecklar ett nytt program och inte vet om det data flöde (RU/s) du behöver gör autoskalning det enkelt att komma igång. Du kan börja med autoskalning-punkten 400-4000 RU/s, övervaka din användning och fastställa rätt RU/s över tid.

* **Program som används sällan:** Om du har ett program som bara används under ett fåtal timmar flera gånger per dag, vecka eller månad, till exempel en program-/webb-/blogg webbplats med låg volym – autoskalning justerar kapaciteten för att hantera högsta användning och skalar ned när den är över. 

* **Arbets belastningar för utveckling och testning:** Om du eller ditt team använder Azure Cosmos-databaser och behållare under arbets tid, men inte behöver dem på nätter eller helger, hjälper autoskalning till att spara kostnader genom att skala ned till ett minimum när det inte används. 

* **Schemalagda produktions arbets belastningar/frågor:** Om du har en serie med schemalagda begär Anden, åtgärder eller frågor som du vill köra under inaktiva perioder kan du enkelt göra det med autoskalning. När du behöver köra arbets belastningen skalas data flödet automatiskt till vad som behövs och skalas ned efteråt. 

Att skapa en anpassad lösning för dessa problem kräver inte bara en enorma del av tiden, utan även en introduktion till komplexitet i programmets konfiguration eller kod. Med automatisk skalning kan du använda ovanstående scenarier i rutan och ta bort behovet av anpassad eller manuell skalning av kapaciteten. 

## <a name="how-autoscale-provisioned-throughput-works"></a>Så här fungerar autoskalning av allokerat data flöde

När du konfigurerar behållare och databaser med autoskalning anger du det maximala data flödet som `Tmax` krävs. Azure Cosmos DB skalar genom strömningen `T` `0.1*Tmax <= T <= Tmax` . Om du till exempel ställer in maximalt data flöde på 20 000 RU/s skalas data flödet mellan 2000 och 20 000 RU/s. Eftersom skalning är automatiskt och momentant, kan du, när som helst, använda upp till etableringen `Tmax` utan fördröjning. 

Varje timme debiteras du för det högsta data flödet som `T` systemet skalas till inom timmen.

Start punkten för autoskalning av maximalt data flöde `Tmax` börjar på 4000 ru/s, som skalar mellan 400-4000 ru/s. Du kan ange `Tmax` i steg om 1000 ru/s och ändra värdet när som helst.  

## <a name="enable-autoscale-on-existing-resources"></a>Aktivera autoskalning på befintliga resurser

Använd [Azure Portal](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container), [Azure Resource Manager mall](how-to-provision-autoscale-throughput.md#azure-resource-manager), [CLI](how-to-provision-autoscale-throughput.md#azure-cli) eller [PowerShell](how-to-provision-autoscale-throughput.md#azure-powershell) för att aktivera autoskalning på en befintlig databas eller behållare. Du kan växla mellan autoskalning och standard (manuellt) allokerat data flöde när som helst. Mer information finns i den här [dokumentationen](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work) .

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a> Data flödes-och lagrings gränser för autoskalning

För ett värde av `Tmax` kan databasen eller containern lagra totalt `0.01 * Tmax GB` . När den här mängden lagring nås ökar maximalt antal RU:er automatiskt baserat på det nya lagringsvärdet, utan att ditt program påverkas. 

Om du till exempel börjar med maximalt antal RU:er på 50 000 RU/s (skalar mellan 5 000–50 000 RU/s) kan du lagra upp till 500 GB data. Om du överskrider 500 GB, och lagringen till exempel ökar till 600 GB, blir det nya maximala antalet RU:er 60 000 RU/s (skalar mellan 6 000–60 000 RU/s).

När du använder dataflöde med autoskalning på databasnivå kan du låta de första 25 containrarna dela på ett maximalt antal RU:er med autoskala på 4 000 (skalar mellan 400–4 000 RU/s), så länge du inte överstiger 40 GB lagring. Mer information finns i den här [dokumentationen](autoscale-faq.md#can-i-change-the-max-rus-on-the-database-or-container) .

## <a name="comparison--containers-configured-with-manual-vs-autoscale-throughput"></a>Jämförelse – behållare som kon figurer ATS med manuella vs-genomflöde med autoskalning
Mer information finns i den här [dokumentationen](how-to-choose-offer.md) om hur du väljer mellan standard (manuell) och autoskalning av data flöde.  

|| Behållare med standard (manuell) data flöde  | Behållare med autoskalning av data flöde |
|---------|---------|---------|
| **Allokerat data flöde (RU/s)** | Manuellt etablerad. | Automatiskt och automatiskt skalas baserat på användnings mönster för arbets belastning. |
| **Hastighets begränsning av begär Anden/åtgärder (429)**  | Kan inträffa om förbrukningen överskrider den etablerade kapaciteten. | Kommer inte att ske om du använder RU/s i det data flödes intervall som du har angett för autoskalning.    |
| **Kapacitetsplanering** |  Du måste utföra kapacitets planering och tillhandahålla det exakta genomflödet du behöver. |    Systemet tar automatiskt hand om kapacitets planering och kapacitets hantering. |
| **Prissättning** | Du betalar för manuellt etablerade RU/s per timme med hjälp av [Standard (manuell) ru/s per tim pris](https://azure.microsoft.com/pricing/details/cosmos-db/). | Du betalar per timme för flest RU/s-systemet skalas upp till inom timmen. <br/><br/> För enstaka Skriv regions konton betalar du för de RU/s som används per timme med hjälp av den [automatiska skalningen ru/s per tim pris](https://azure.microsoft.com/pricing/details/cosmos-db/). <br/><br/>För konton med flera Skriv regioner finns det ingen extra kostnad för autoskalning. Du betalar för det data flöde som används per timme med samma [pris per timme som Write ru/s per timme](https://azure.microsoft.com/pricing/details/cosmos-db/). |
| **Passar bäst för arbets belastnings typer** |  Förutsägbara och stabila arbets belastningar|   Oförutsedda och varierande arbets belastningar  |

## <a name="next-steps"></a>Nästa steg

* Läs [vanliga frågor och svar om autoskalning](autoscale-faq.md).
* Lär dig hur du [väljer mellan manuell och autoskalning av data flöde](how-to-choose-offer.md).
* Lär dig hur du [etablerar autoskalning genom strömning på en Azure Cosmos-databas eller-behållare](how-to-provision-autoscale-throughput.md).
* Läs mer om [partitionering](partitioning-overview.md) i Azure Cosmos dB.


