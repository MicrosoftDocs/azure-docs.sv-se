---
title: Översikt – Azure Database for MySQL – flexibel server
description: Lär dig mer Azure Database for MySQL Flexibel server, en relationsdatabastjänst i Microsoft-molnet som baseras på MySQL Community Edition.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/21/2020
ms.openlocfilehash: e3259cddc3790f92f56d7f59783cd66a2502b704
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813334"
---
# <a name="azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL – flexibel server (förhandsversion)

Azure Database for MySQL som drivs av MySQL Community Edition finns i två distributionslägen:
- Enskild server 
- Flexibel server (förhandsversion)

I den här artikeln ger vi en översikt och introduktion till grundläggande begrepp för flexibel serverdistributionsmodell. Information om hur du bestämmer vilket distributionsalternativ som är lämpligt för din arbetsbelastning finns i [välja rätt MySQL-serveralternativ i Azure](./../select-right-deployment-type.md).

## <a name="overview"></a>Översikt

Azure Database for MySQL Flexibel server är en fullständigt hanterad databastjänst som utformats för att ge mer detaljerad kontroll och flexibilitet över databashanteringsfunktioner och konfigurationsinställningar. I allmänhet ger tjänsten mer flexibilitet och anpassningar av serverkonfigurationen baserat på användarkraven. Med den flexibla serverarkitekturen kan användarna välja hög tillgänglighet i en zon med enkel tillgänglighet och över flera tillgänglighetszoner. Flexibla servrar ger också bättre kostnadsoptimeringskontroller med möjlighet att stoppa/starta servern och burst-able SKU:er, vilket är perfekt för arbetsbelastningar som inte behöver fullständig beräkningskapacitet kontinuerligt. Tjänsten stöder för närvarande community-versionen av MySQL 5.7 och 8.0. Tjänsten är för närvarande i förhandsversion och finns i dag i många olika [Azure-regioner.](https://azure.microsoft.com/global-infrastructure/services/)

Flexibla servrar passar bäst för 
- Programutveckling som kräver bättre kontroll och anpassning.
- Zonredundant hög tillgänglighet
- Hanterade underhållsfönster

![Konceptuellt diagram för flexibel server](media/overview/1-flexible-server-conceptual-diagram.png) 

## <a name="high-availability-within-and-across-availability-zones"></a>Hög tillgänglighet i och mellan tillgänglighetszoner

Den flexibla serverdistributionsmodellen har utformats för att stödja hög tillgänglighet i en zon för enkel tillgänglighet och över flera tillgänglighetszoner. Arkitekturen separerar beräkning och lagring. Databasmotorn körs på en virtuell dator medan datafiler finns i Azure Storage. Lagringen har tre lokalt redundanta synkrona kopior av databasfilerna som säkerställer datatillförlitligheten hela tiden. 

Om servern går ned på grund av planerade eller oplanerade händelser i en enda tillgänglighetszon, upprätthåller tjänsten hög tillgänglighet för servrarna med hjälp av följande automatiserade procedur:

1. En ny virtuell dator för beräkning etableras.
2. Lagringen med datafiler mappas till den nya virtuella datorn
3. MySQL-databasmotorn är online på den nya virtuella datorn.
4. Klientprogram kan återansluta när servern är redo att acceptera anslutningar.
   
:::image type="content" source="media/overview/2-flexible-server-architecture.png" alt-text="Konceptuellt diagram över hög tillgänglighet för en zon"::: 

Om zonredundant hög tillgänglighet har konfigurerats, tillhandahåller och underhåller tjänsten en server med hett vänteläge över tillgänglighetszonen i samma Azure-region. Dataändringarna på källservern replikeras synkront till standby-servern för att säkerställa noll dataförlust. Med zonredundant hög tillgänglighet, när den planerade eller oplanerade redundanshändelsen har utlösts, är standby-servern online omedelbart och kan bearbeta inkommande transaktioner. Den typiska redundanstiden sträcker sig från 60–120 sekunder. Detta gör att tjänsten kan stödja hög tillgänglighet och ge förbättrad återhämtning med tolerans för fel i en enskild tillgänglighetszon i en viss Azure-region. 

Mer [information finns i Begrepp](concepts-high-availability.md) för hög tillgänglighet.

:::image type="content" source="media/overview/3-flexible-server-overview-zone-redundant-ha.png" alt-text="Konceptuellt diagram över zonredundant hög tillgänglighet"::: 

## <a name="automated-patching-with-managed-maintenance-window"></a>Automatisk uppdatering med hanterat underhållsfönstret

Tjänsten utför automatisk uppdatering av den underliggande maskinvaran, operativsystemet och databasmotorn. Korrigeringen innehåller säkerhets- och programuppdateringar. För MySQL-motorn ingår även uppgraderingar av lägre versioner som en del av den planerade underhållsversionen. Användare kan konfigurera korrigeringsschemat så att det hanteras av systemet eller definiera sitt anpassade schema. Under underhållsschemat tillämpas korrigeringen och servern kan kräva en omstart som en del av korrigeringsprocessen för att slutföra uppdateringen. Med det anpassade schemat kan användarna göra korrigeringscykeln förutsägbar och välja en underhållsperiod med minimal påverkan på verksamheten. I allmänhet följer tjänsten månadsschemat som en del av den kontinuerliga integreringen och lanseringen.

Mer [information finns i](concepts-maintenance.md) Schemalagt underhåll. 

## <a name="automatic-backups"></a>Automatiska säkerhetskopieringar

Den flexibla servertjänsten skapar automatiskt serversäkerhetskopior och lagrar dem i användarkonfigurerad lokalt redundant eller geo-redundant lagring. Säkerhetskopior kan användas för att återställa servern till valfri tidpunkt inom kvarhållningsperioden för säkerhetskopior. Standardperioden för kvarhållning av säkerhetskopior är sju dagar. Du kan välja att konfigurera kvarhållningen i upp till 35 dagar. Alla säkerhetskopior krypteras med AES 256-bitars kryptering. 

Mer [information finns i Säkerhetskopieringsbegrepp.](concepts-backup-restore.md)

## <a name="network-isolation"></a>Nätverksisolering

Du har två nätverksalternativ för att ansluta till din Azure Database for MySQL flexibel server. Alternativen är privat **åtkomst (VNet-integrering) och** **offentlig åtkomst (tillåtna IP-adresser).** 

* **Privat åtkomst (VNet-integrering)** – Du kan distribuera din flexibla server till [azure-Virtual Network](../../virtual-network/virtual-networks-overview.md). Virtuella Azure-nätverk tillhandahåller privat och säker nätverkskommunikation. Resurser i ett virtuellt nätverk kan kommunicera via privata IP-adresser.

   Välj alternativet VNet-integrering om du vill ha följande funktioner:
   * Ansluta från Azure-resurser i samma virtuella nätverk till din flexibla server med privata IP-adresser
   * Använda VPN eller ExpressRoute för att ansluta från icke-Azure-resurser till din flexibla server
   * Ingen offentlig slutpunkt

* **Offentlig åtkomst (tillåtna IP-adresser)** – Du kan distribuera din flexibla server med en offentlig slutpunkt. Den offentliga slutpunkten är en offentligt matchad DNS-adress. Frasen "tillåtna IP-adresser" avser ett intervall av IP-adresser som du väljer att ge behörighet att komma åt servern. Dessa behörigheter kallas **brandväggsregler.**

Mer [information finns i](concepts-networking.md) Nätverksbegrepp.

## <a name="adjust-performance-and-scale-within-seconds"></a>Justera prestanda och skalning på några sekunder

Den flexibla servertjänsten är tillgänglig på tre SKU-nivåer: burstable, Generell användning och minnesoptimerad. Nivån Burstable passar bäst för utveckling till låg kostnad och arbetsbelastningar med låg samtidighet som inte behöver fullständig beräkningskapacitet kontinuerligt. Den Generell användning och minnesoptimerad passar bättre för produktionsarbetsbelastningar som kräver hög samtidighet, skalning och förutsägbara prestanda. Du kan skapa din första app på en liten databas för några dollar i månaden och sedan smidigt justera skalan för att uppfylla behoven i din lösning. Lagringsskalningen är online och stöder automatisk lagringsskalning. Dynamisk skalbarhet gör att databasen reagerar transparent på resurskrav som ändras snabbt. Du betalar bara för de resurser som du förbrukar. 

Mer [information finns i Beräknings- och](concepts-compute-storage.md) lagringsbegrepp.

## <a name="scale-out-your-read-workload-with-up-to-10-read-replicas"></a>Skala ut läsarbetsbelastningen med upp till 10 skrivskyddade repliker

MySQL är en av de populära databasmotorerna för att köra webb- och mobilprogram i Internetskala. Många av våra kunder använder den för sina onlineutbildningstjänster, videoströmningstjänster, digitala betalningslösningar, e-handelsplattformar, speltjänster, nyhetsportaler, myndigheter och sjukvårdswebbplatser. Dessa tjänster krävs för att fungera och skala när trafiken på webben eller mobilprogrammet ökar.

På programsidan utvecklas programmet vanligtvis i Java eller php och migreras för att köras på Skalningsuppsättningar för virtuella [Azure-datorer](../../virtual-machine-scale-sets/overview.md)eller Azure App Services eller är containeriserade för att köras   på Azure Kubernetes Service [](../../app-service/overview.md)    [(AKS).](../../aks/intro-kubernetes.md) Med VM-skalningsuppsättning, App Service eller AKS som underliggande infrastruktur, förenklas programskalningen genom att omedelbart etablera nya virtuella datorer och replikera de tillståndslösa komponenterna i program för att tillgodose begärandena, men ofta blir databasen en flaskhals som en centraliserad tillståndsfull komponent.

Med funktionen skrivskyddade repliker kan du replikera data från Azure Database for MySQL flexibel server till en skrivskyddat server. Du kan replikera från källservern till **upp till 10 repliker.** Repliker uppdateras asynkront med MySQL-motorns positionsbaserade replikeringsteknik baserad på intern binär loggfil [(binlog).](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) Du kan använda en proxylösning för lastbalanserare som [ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) för att sömlöst skala ut programarbetsbelastningen för att läsa repliker utan någon kostnad för programrefaktorering. 

Mer [information finns i Läsa replikbegrepp.](concepts-read-replicas.md) 


## <a name="stopstart-server-to-optimize-cost"></a>Stoppa/starta servern för att optimera kostnaderna

Med den flexibla servertjänsten kan du stoppa och starta servern på begäran för att optimera kostnaderna. Faktureringen för beräkningsnivån stoppas omedelbart när servern stoppas. Detta kan ge betydande kostnadsbesparingar under utveckling, testning och för tidsbundna förutsägbara produktionsarbetsbelastningar. Servern är i stoppat tillstånd i sju dagar om den inte startas om tidigare. 

Mer [information finns i Serverkoncept.](concept-servers.md) 

## <a name="enterprise-grade-security-and-privacy"></a>Säkerhet och sekretess i företagsklass

Den flexibla servertjänsten använder FIPS 140-2-verifierad kryptografimodul för lagringskryptering av vilodata. Data, inklusive säkerhetskopior och temporära filer som skapas när frågor körs krypteras. Tjänsten använder AES 256-bitars chiffer som ingår i Azure-lagringskryptering och nycklarna kan hanteras av systemet (standard). 

Tjänsten krypterar data i rörelse med säkerhet på transportnivå framtvingad som standard. Flexibla servrar stöder endast krypterade anslutningar med Transport Layer Security (TLS 1.2) och alla inkommande anslutningar med TLS 1.0 och TLS 1.1 nekas. 

Mer [information finns i använda krypterade anslutningar till flexibla](https://docs.mongodb.com/manual/tutorial/configure-ssl) servrar.

Flexibla servrar ger fullständig privat åtkomst till servrarna med integrering av virtuella [Azure-nätverk](../../virtual-network/virtual-networks-overview.md) (VNet). Servrar i virtuella Azure-nätverk kan bara nås och anslutas via privata IP-adresser. Med VNet-integrering nekas offentlig åtkomst och servrar kan inte nås med hjälp av offentliga slutpunkter. 

Mer [information finns i](concepts-networking.md) Nätverksbegrepp.


## <a name="monitoring-and-alerting"></a>Övervakning och avisering

Den flexibla servertjänsten är utrustad med inbyggda funktioner för prestandaövervakning och avisering. Alla Azure-mått har en frekvens på en minut och varje mått visar 30 dagars historik. Du kan konfigurera aviseringar för måtten. Tjänsten exponerar värdservermått för att övervaka resursanvändningen och gör det möjligt att konfigurera långsamma frågeloggar. Med dessa verktyg kan du snabbt optimera dina arbetsbelastningar och konfigurera servern för bästa prestanda. 

Mer [information finns i Övervakningsbegrepp.](concepts-monitoring.md)

## <a name="migration"></a>Migrering

Tjänsten kör community-versionen av MySQL. Detta ger fullständig programkompatibilitet och kräver minimala omstruktureringskostnader för att migrera befintliga program som utvecklats på MySQL-motorn till en servertjänst. Migreringen till den enskilda servern kan utföras med något av följande alternativ:

- **Dumpning och återställning** – För offlinemigrering, där användarna har råd med viss stilleståndstid, dumpar och återställning med hjälp av community-verktyg som mysqldump/mydumper kan tillhandahålla det snabbaste sättet att migrera. Mer information finns i Migrera med dumpning och återställning. 
- **Azure Database Migration Service** – För sömlös och förenklad migrering till en enskild server [med minimal avbrottstid Azure Database Migration Service](../../dms/tutorial-mysql-azure-mysql-online.md) kan utnyttjas. 

## <a name="azure-regions"></a>Azure-regioner

En av fördelarna med att köra din arbetsbelastning i Azure är att den är global räckvidd. Den flexibla servern för Azure Database for MySQL är tillgänglig i följande Azure-regioner:

| Region | Tillgänglighet | Zonredundant HA | 
| --- | --- | --- |
| Europa, västra | :heavy_check_mark: | :heavy_check_mark: |
| Europa, norra | :heavy_check_mark: | :heavy_check_mark: |
| Storbritannien, södra | :heavy_check_mark: | :heavy_check_mark: | 
| USA, östra 2 | :heavy_check_mark: | :heavy_check_mark: |
| USA, västra 2 | :heavy_check_mark: | :heavy_check_mark: |
| Central US | :heavy_check_mark: | :x: | 
| East US | :heavy_check_mark: | :heavy_check_mark: |
| Kanada, centrala | :heavy_check_mark: | :x: | 
| Sydostasien | :heavy_check_mark: | :heavy_check_mark: |
| Sydkorea, centrala | :heavy_check_mark: | :x: | 
| Japan, östra | :heavy_check_mark: | :heavy_check_mark: | 
| Australien, östra | :heavy_check_mark: | :heavy_check_mark: |

Vi arbetar på att lägga till nya regioner snart.

## <a name="contacts"></a>Kontakter
Om du har frågor eller förslag på Azure Database for MySQL flexibel server kan du skicka ett e-postmeddelande till Azure Database for MySQL Team ([ @Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Den här e-postadressen är inte ett alias för teknisk support.

Tänk dessutom på följande kontaktpunkter efter behov:

- Kontakta Azure Support genom att [skicka in ett supportärende från Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Om du vill åtgärda ett problem med ditt konto, skickar du in ett [supportärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) i Azure-portalen.
- Om du vill ge feedback eller begära nya funktioner, skapar du ett inlägg via [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Nästa steg
Nu när du har läst en introduktion Azure Database for MySQL distributionsläge för enskild server är du redo att:

- Skapa din första server. 
  - [Skapa en Azure Database for MySQL flexibel server med Azure Portal](quickstart-create-server-portal.md)
  - [Skapa en Azure Database for MySQL flexibel server med Azure CLI](quickstart-create-server-cli.md)
  - [Hantera en Azure Database for MySQL flexibel server med hjälp av Azure CLI](how-to-manage-server-portal.md)

- Skapa din första app med önskat språk:
  - [Python](connect-python.md)
  - [Php](connect-php.md)
