---
title: Förbättra tillförlitligheten för ditt program med Advisor
description: Använd Azure Advisor för att säkerställa och förbättra tillförlitligheten i affärs kritiska Azure-distributioner.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 0d96974e53f24d5a01eeee8b08eee578177a9ad2
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258494"
---
# <a name="improve-the-reliability-of-your-application-by-using-azure-advisor"></a>Förbättra tillförlitligheten för ditt program genom att använda Azure Advisor

Azure Advisor hjälper dig att säkerställa och förbättra kontinuiteten i verksamhets kritiska program. Du kan få Tillförlitlighets rekommendationer från Advisor på fliken **tillförlitlighet** i Advisor-instrumentpanelen.

## <a name="check-the-version-of-your-check-point-network-virtual-appliance-image"></a>Kontrol lera versionen av avbildningen av den virtuella datorn för kontroll punkter

Advisor kan identifiera om den virtuella datorn kör en version av Check Point-avbildningen som har visat sig förlora nätverks anslutningen under plattforms underhålls åtgärder. Advisor-rekommendationen hjälper dig att uppgradera till en nyare version av avbildningen som åtgärdar problemet. Den här kontrollen säkerställer affärs kontinuiteten genom en bättre nätverks anslutning.

## <a name="ensure-application-gateway-fault-tolerance"></a>Säkerställ fel tolerans för Application Gateway

Den här rekommendationen säkerställer affärs kontinuiteten för verksamhets kritiska program som drivs av programgatewayer. Advisor identifierar instanser av Application Gateway som inte är konfigurerade för fel tolerans. Den föreslår sedan åtgärder som du kan vidta. Advisor identifierar medelhög eller stor Programgateway med en instans och rekommenderar att du lägger till minst en instans. Den identifierar även små programgatewayer med enkel instans och flera instanser och rekommenderar att du migrerar dem till medel stora eller stora SKU: er. Advisor rekommenderar dessa åtgärder för att se till att Application Gateway-instanserna är konfigurerade för att uppfylla de aktuella SLA-kraven för dessa resurser.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Skydda dina virtuella dator data från oavsiktlig borttagning

Att ställa in säkerhets kopiering av virtuella datorer garanterar att dina affärs kritiska data är tillgängliga och skyddar mot oavsiktlig borttagning eller skada. Advisor identifierar virtuella datorer där säkerhets kopiering inte är aktiverat och rekommenderar att du aktiverar säkerhets kopiering. 

## <a name="ensure-you-have-access-to-azure-experts-when-you-need-it"></a>Se till att du har åtkomst till Azure-experter när du behöver den

När du kör en verksamhets kritisk arbets belastning är det viktigt att ha till gång till teknisk support när du behöver den. Advisor identifierar potentiella affärs kritiska prenumerationer som inte har teknisk support som ingår i support avtalen. Vi rekommenderar att du uppgraderar till ett alternativ som omfattar teknisk support.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Skapa Azure Service Health aviseringar för att bli informerad när Azure-problem påverkar dig

Vi rekommenderar att du konfigurerar Azure Service Health aviseringar så att du meddelas när Azure-tjänstens problem påverkar dig. [Azure Service Health](https://azure.microsoft.com/features/service-health/) är en kostnads fri tjänst som ger personlig vägledning och support när du påverkas av ett problem med Azure-tjänsten. Advisor identifierar prenumerationer som inte har konfigurerade varningar och rekommenderar att du konfigurerar dem.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Konfigurera Traffic Manager slut punkter för återhämtning

Azure Traffic Manager profiler med mer än en slut punkts upplevelse med högre tillgänglighet om någon av de slut punkterna går sönder. Att placera slut punkter i olika regioner förbättrar tillförlitligheten i tjänsten. Advisor identifierar Traffic Manager-profiler där det bara finns en slut punkt och rekommenderar att du lägger till minst en slut punkt i en annan region.

Om alla slut punkter i en Traffic Manager profil som har kon figurer ATS för närhets dirigering finns i samma region kan användare från andra regioner uppleva anslutnings fördröjningar. Genom att lägga till eller flytta en slut punkt till en annan region får du bättre prestanda och ger bättre tillgänglighet om alla slut punkter i en region kraschar. Advisor identifierar Traffic Manager profiler som kon figurer ATS för närhet av routning där alla slut punkter finns i samma region. Du bör lägga till eller flytta en slut punkt till en annan Azure-region.

Om en Traffic Manager profil har kon figurer ATS för geografisk routning dirigeras trafiken till slut punkter som baseras på definierade regioner. Om en region Miss lyckas finns det ingen fördefinierad redundans. Om du har en slut punkt där regional gruppering har kon figurer ATS för **alla (värld)** kan du undvika förlorad trafik och förbättra tjänstens tillgänglighet. Advisor identifierar Traffic Manager profiler som kon figurer ATS för geografisk routning där det inte finns någon slut punkt konfigurerad för att ha regional gruppering som **alla (värld)**. Vi rekommenderar att du ändrar konfigurationen för att göra en slut punkt **alla (värld)**.

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Använd mjuk borttagning på ditt Azure Storage-konto för att spara och återställa data efter oavsiktlig överskrivning eller borttagning

Aktivera [mjuk borttagning](../storage/blobs/soft-delete-overview.md) på ditt lagrings konto så att borttagna blobar övergår till ett mjukt borttaget tillstånd i stället för att tas bort permanent. När data skrivs över skapas en mjukt borttagen ögonblicksbild för att spara läget för den överskrivna informationen. Med hjälp av mjuk borttagning kan du återställa från oavsiktliga borttagningar eller Skriv över. Advisor identifierar Azure Storage-konton som inte har mjuk borttagning aktiverat och föreslår att du aktiverar det.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Konfigurera din VPN-gateway till aktiv-aktiv för anslutnings återhämtning

I aktiv-aktiv konfiguration upprättar båda instanserna av en VPN-gateway S2S VPN-tunnlar till din lokala VPN-enhet. När en händelse för ett planerat underhåll eller en oplanerad händelse sker till en gateway-instans växlas trafiken automatiskt till den andra aktiva IPsec-tunneln. Azure Advisor identifierar VPN-gatewayer som inte är konfigurerade som aktiva – aktiva och som föreslår att du konfigurerar dem för hög tillgänglighet.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Använd VPN-gatewayer för produktion för att köra produktions arbets belastningar

Azure Advisor söker efter VPN-gatewayer som använder en grundläggande SKU och rekommenderar att du använder en produktions-SKU i stället. Bas-SKU: n är utformad för utveckling och testning. Produkt-SKU-erbjudande:
- Fler tunnlar. 
- Stöd för BGP. 
- Konfigurations alternativ för aktiv-aktiv. 
- Anpassad IPsec/IKE-princip. 
- Högre stabilitet och tillgänglighet.

## <a name="ensure-virtual-machine-fault-tolerance-temporarily-disabled"></a>Se till att fel toleransen för virtuella datorer (tillfälligt inaktive rad)

För att tillhandahålla redundans för ditt program rekommenderar vi att du grupperar två eller flera virtuella datorer i en tillgänglighets uppsättning. Advisor identifierar virtuella datorer som inte ingår i en tillgänglighets uppsättning och rekommenderar att du flyttar dem till en. Den här konfigurationen garanterar att minst en virtuell dator är tillgänglig under ett planerat eller oplanerat underhåll och uppfyller service avtalet för virtuella Azure-datorer. Du kan välja att skapa en tillgänglighets uppsättning för den virtuella datorn eller lägga till den virtuella datorn i en befintlig tillgänglighets uppsättning.

> [!NOTE]
> Om du väljer att skapa en tillgänglighets uppsättning måste du lägga till minst en virtuell dator i den. Vi rekommenderar att du grupperar två eller flera virtuella datorer i en tillgänglighets uppsättning för att säkerställa att minst en dator är tillgänglig under ett avbrott.

## <a name="ensure-availability-set-fault-tolerance-temporarily-disabled"></a>Se till att fel tolerans för tillgänglighets uppsättning (tillfälligt inaktive rad)

För att tillhandahålla redundans för ditt program rekommenderar vi att du grupperar två eller flera virtuella datorer i en tillgänglighets uppsättning. Advisor identifierar tillgänglighets uppsättningar som innehåller en enda virtuell dator och rekommenderar att du lägger till en eller flera virtuella datorer i den.Den här konfigurationen garanterar att minst en virtuell dator är tillgänglig under ett planerat eller oplanerat underhåll och uppfyller service avtalet för virtuella Azure-datorer.Du kan välja att skapa en virtuell dator eller lägga till en befintlig virtuell dator i tillgänglighets uppsättningen.  

## <a name="use-managed-disks-to-improve-data-reliability-temporarily-disabled"></a>Använd Managed disks för att förbättra data tillförlitligheten (tillfälligt inaktive rad)

Virtuella datorer som finns i en tillgänglighets uppsättning med diskar som delar lagrings konton eller lagrings skalnings enheter är inte elastiska till andra lagrings enheter för lagring vid avbrott. Advisor identifierar dessa tillgänglighets uppsättningar och rekommenderar att du migrerar till Azure Managed disks. Den här migreringen ser till att diskarna för de virtuella datorerna i tillgänglighets uppsättningen är tillräckligt isolerade för att undvika en enskild felpunkt. 

## <a name="repair-invalid-log-alert-rules"></a>Reparera ogiltiga logg aviserings regler

Azure Advisor identifierar aviserings regler som har ogiltiga frågor angivna i villkors avsnittet. Du kan skapa logg aviserings regler i Azure Monitor och använda dem för att köra Analytics-frågor vid angivna intervall. Resultatet av en fråga avgör om en avisering behöver utlösas. Analys frågor kan bli ogiltiga över tid på grund av ändringar i refererade resurser, tabeller eller kommandon. Advisor rekommenderar att du korrigerar frågan i varnings regeln för att förhindra att den automatiskt inaktive ras och säkerställer övervakningen av dina resurser i Azure. [Läs mer om fel sökning av aviserings regler.](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-azure-cosmos-db-collection"></a>Konfigurera konsekvent indexerings läge på din Azure Cosmos DB-samling

Att konfigurera Azure Cosmos DB behållare med Lazy indexerings läge kan påverka uppdatering av frågeresultat. Advisor identifierar behållare som kon figurer ATS på det här sättet och rekommenderar att du växlar till konsekvent läge. [Läs mer om att indexera principer i Azure Cosmos DB.](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Ställ in Azure Cosmos DB-containrarna med en partitionsnyckel

Azure Advisor identifierar Azure Cosmos DB icke-partitionerade samlingar som närmar sig den etablerade lagrings kvoten. Vi rekommenderar att du migrerar samlingarna till nya samlingar med en partitionsnyckel så att de kan skalas ut automatiskt av tjänsten. [Läs mer om hur du väljer en partitionsnyckel.](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Uppgradera Azure Cosmos DB .NET SDK till den senaste versionen från NuGet

Azure Advisor identifierar Azure Cosmos DB konton som använder gamla versioner av .NET SDK. Vi rekommenderar att du uppgraderar till den senaste versionen från NuGet för de senaste korrigeringarna, prestanda förbättringarna och funktionerna. [Läs mer om Azure Cosmos DB .NET SDK.](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Uppgradera Azure Cosmos DB Java-SDK till den senaste versionen från Maven

Azure Advisor identifierar Azure Cosmos DB konton som använder gamla versioner av Java SDK. Vi rekommenderar att du uppgraderar till den senaste versionen från Maven för de senaste korrigeringarna, prestanda förbättringarna och funktionerna. [Läs mer om Azure Cosmos DB Java SDK.](https://aka.ms/cosmosdb/sql-api-sdk-async-java)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Uppgradera din Azure Cosmos DB Spark-anslutning till den senaste versionen från maven

Azure Advisor identifierar Azure Cosmos DB konton som använder tidigare versioner av Azure Cosmos DB Spark-anslutningsprogrammet. Vi rekommenderar att du uppgraderar till den senaste versionen från Maven för de senaste korrigeringarna, prestanda förbättringarna och funktionerna. [Läs mer om Azure Cosmos DB Spark-anslutning.](https://aka.ms/cosmosdb/spark-connector)

## <a name="consider-moving-to-kafka-21-on-hdinsight-40"></a>Överväg att flytta till Kafka 2,1 på HDInsight 4,0

Från den 1 juli 2020 kommer du inte att kunna skapa nya Kafka-kluster med hjälp av Kafka 1,1 på Azure HDInsight 4,0. Befintliga kluster kommer att köras i befintligt skick utan stöd från Microsoft. Överväg att flytta till Kafka 2,1 på HDInsight 4,0 den 30 juni 2020, för att undvika potentiell system/support-avbrott.

## <a name="consider-upgrading-older-spark-versions-in-hdinsight-spark-clusters"></a>Överväg att uppgradera äldre Spark-versioner i HDInsight Spark-kluster

Från den 1 juli 2020 kommer du inte att kunna skapa nya Spark-kluster med Spark 2,1 eller 2,2 på HDInsight 3,6. Du kan inte skapa nya Spark-kluster med Spark 2,3 på HDInsight 4,0. Befintliga kluster kommer att köras i befintligt skick utan stöd från Microsoft. 

## <a name="enable-virtual-machine-replication"></a>Aktivera replikering av virtuell dator
Virtuella datorer som inte har någon replikering aktive rad till en annan region är inte flexibla till regionala avbrott. Att replikera virtuella datorer minskar eventuell negativ inverkan på verksamheten under drifts avbrott i Azure. Advisor identifierar virtuella datorer där replikering inte är aktiverat och rekommenderar att du aktiverar den. Om du aktiverar replikering kan du snabbt ta fram dina virtuella datorer i en fjärran sluten Azure-region om det uppstår ett avbrott. [Läs mer om replikering av virtuella datorer.](../site-recovery/azure-to-azure-quickstart.md)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Få åtkomst till rekommendationer för hög tillgänglighet i Advisor

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna [Advisor](https://aka.ms/azureadvisordashboard).

2.  På instrument panelen för Advisor väljer du fliken **hög tillgänglighet** .

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns i:
* [Introduktion till Advisor](advisor-overview.md)
* [Kom igång med Advisor](advisor-get-started.md)
* [Kostnadsrekommendationer i Advisor](advisor-cost-recommendations.md)
* [Rekommendationer för Advisor-prestanda](advisor-performance-recommendations.md)
* [Rekommendationer för Advisor-säkerhet](advisor-security-recommendations.md)
* [Rekommendationer om operativa rekommendationer](advisor-operational-excellence-recommendations.md)
