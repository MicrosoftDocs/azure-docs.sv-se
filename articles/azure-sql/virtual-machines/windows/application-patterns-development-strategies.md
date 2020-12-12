---
title: SQL Server program mönster på virtuella datorer | Microsoft Docs
description: Den här artikeln beskriver program mönster för SQL Server på Azure Virtual Machines. Den ger lösnings arkitekter och utvecklare en grund för lämplig program arkitektur och design.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 41863c8d-f3a3-4584-ad86-b95094365e05
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mathoma
ms.openlocfilehash: e877f775c6a7bc83ce6c4a559c9f74b606c952fc
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359497"
---
# <a name="application-patterns-and-development-strategies-for-sql-server-on-azure-virtual-machines"></a>Program mönster och utvecklings strategier för SQL Server på Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>Sammanfattning:
Att avgöra vilka program mönster eller mönster som ska användas för dina SQL Server-baserade program i en Azure-miljö är ett viktigt design beslut och det kräver en heltäckande förståelse för hur SQL Server och varje infrastruktur komponent i Azure fungerar tillsammans. Med SQL Server i Azures infrastruktur tjänster kan du enkelt migrera, underhålla och övervaka dina befintliga SQL Server program som bygger på Windows Server till virtuella datorer i Azure.

Målet med den här artikeln är att tillhandahålla lösnings arkitekter och utvecklare en grund för bra program arkitektur och design, som de kan följa när du migrerar befintliga program till Azure och utvecklar nya program i Azure.

För varje program mönster hittar du ett lokalt scenario, dess respektive molnbaserade lösning och de relaterade tekniska rekommendationerna. Dessutom diskuterar artikeln Azure-/regionsspecifika utvecklings strategier så att du kan utforma dina program på rätt sätt. På grund av de många möjliga program mönstren rekommenderar vi att arkitekter och utvecklare bör välja det mest lämpliga mönstret för sina program och användare.

**Tekniska deltagare:** Luis Carlos Vargas sill, Madhan Arumugam Ramakrishnan

**Teknisk granskare:** Corey Sanders, ritade McDaniel, Narayan Annamalai, NIR Mashkowski, Sanjay Mishra, Silvano Coriani, Stefan Schackow, Tim Hickey, Tim Wieman, Xin Jin

## <a name="introduction"></a>Introduktion
Du kan utveckla många typer av program på n-nivån genom att åtskilja komponenterna i olika program lager på olika datorer samt i separata komponenter. Du kan till exempel placera komponenter för klient program-och affärs regler i en dator, frontend-Webbnivå och data åtkomst nivå komponenter på en annan dator och en server dels databas nivå på en annan dator. Den här typen av strukturering hjälper till att isolera varje nivå från varandra. Om du ändrar var data kommer från behöver du inte ändra klient-eller webbappen, utan endast data åtkomst nivå komponenterna.

Ett typiskt *n-Nivåprogram* innehåller presentations nivån, affärs nivån och data nivån:

| Nivå | Beskrivning |
| --- | --- |
| **Presentation** |*Presentations nivån* (webb nivå, front-end-nivå) är det lager där användarna interagerar med ett program. |
| **Företag** |*Affärs nivån* (mellan nivån) är det skikt som presentations nivån och data nivån använder för att kommunicera med varandra och som innehåller systemets kärn funktioner. |
| **Data** |*Data nivån* är i princip den server som lagrar data för ett program (till exempel en server som kör SQL Server). |

Program lager beskriver de logiska grupperna av funktioner och komponenter i ett program. nivåer beskriver den fysiska distributionen av funktionerna och komponenterna på separata fysiska servrar, datorer, nätverk eller fjärranslutna platser. Lagren i ett program kan finnas på samma fysiska dator (samma nivå) eller kan distribueras över olika datorer (n-nivå) och komponenterna i varje lager kommunicerar med komponenter i andra lager via väldefinierade gränssnitt. Du kan tänka på term nivån som refererar till fysiska distributions mönster, till exempel två nivåer, tre lager och n-nivå. Ett **program mönster på 2 nivåer** innehåller två program nivåer: program Server och databas server. Direkt kommunikationen sker mellan program servern och databas servern. Program servern innehåller både webb-och affärs skikts komponenter. I ett **program mönster** på tre nivåer finns det tre program nivåer: webb server, program server som innehåller affärs logik nivån och/eller data åtkomst komponenter för affärs nivå och databas servern. Kommunikationen mellan webb servern och databas servern sker över program servern. Detaljerad information om program lager och-nivåer finns i [Microsoft Application Architecture guide](/previous-versions/msp-n-p/ff650706(v=pandp.10)).

Innan du börjar läsa den här artikeln bör du ha kunskap om de grundläggande begreppen i SQL Server och Azure. Mer information finns i [SQL Server Books Online](/sql/sql-server/) [SQL Server på Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md) och [Azure.com](https://azure.microsoft.com/).

I den här artikeln beskrivs flera program mönster som kan vara lämpliga för dina enkla program samt de mycket komplexa företags programmen. Innan du anger varje mönster rekommenderar vi att du bekantar dig med tillgängliga data lagrings tjänster i Azure, till exempel [Azure Storage](../../../storage/common/storage-introduction.md), [Azure SQL Database](../../database/sql-database-paas-overview.md)och [SQL Server i en virtuell Azure-dator](sql-server-on-azure-vm-iaas-what-is-overview.md). För att fatta de bästa design besluten för dina program, förstå när du ska använda vilken data lagrings tjänst som är tydlig.

### <a name="choose-sql-server-on-azure-virtual-machines-when"></a>Välj SQL Server på Azure Virtual Machines när:

* Du måste ha kontroll över SQL Server och Windows. Detta kan till exempel omfatta SQL Server version, särskilda snabb korrigeringar, prestanda konfiguration osv.
* Du behöver en fullständig kompatibilitet med SQL Server och vill flytta befintliga program till Azure i befintligt skick.
* Du vill utnyttja funktionerna i Azure-miljön men Azure SQL Database inte stöder alla funktioner som programmet kräver. Detta kan omfatta följande områden:
  
  * **Databas storlek**: när den här artikeln uppdaterades SQL Database stöd för en databas på upp till 1 TB data. Om programmet kräver mer än 1 TB data och du inte vill implementera anpassade horisontell partitionering-lösningar, rekommenderar vi att du använder SQL Server på en virtuell Azure-dator. Den senaste informationen finns i [skala ut Azure SQL Database](/previous-versions/azure/dn495641(v=azure.100)), [DTU-baserad inköps modell](../../database/service-tiers-dtu.md)och [VCore-baserad inköps modell](../../database/service-tiers-vcore.md)(för hands version).
  * **HIPAA-efterlevnad**: hälso vårds kunder och oberoende program varu leverantörer (ISV) kan välja [SQL Server på Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md) i stället för [Azure SQL Database](../../database/sql-database-paas-overview.md) eftersom SQL Server på Azure Virtual Machines täcks av HIPAA Business associerings avtal (Baa). Information om kompatibilitet finns i [Microsoft Azure säkerhets Center: efterlevnad](https://azure.microsoft.com/support/trust-center/compliance/).
  * **Funktioner på instans nivå**: vid det här tillfället stöder SQL Database inte funktioner som är aktiva utanför databasen (till exempel länkade servrar, Agent jobb, FileStream, Service Broker osv.). Mer information finns i [rikt linjer och begränsningar för Azure SQL Database](/previous-versions/azure/ff394102(v=azure.100)).

## <a name="1-tier-simple-single-virtual-machine"></a>1-nivå (enkel): enskild virtuell dator
I det här program mönstret distribuerar du ditt SQL Server-program och-databas till en fristående virtuell dator i Azure. Samma virtuella dator innehåller klient-/webb program, affärs komponenter, data åtkomst skikt och databas servern. Presentations-, affärs-och data åtkomst koden separeras logiskt men är fysiskt placerad på en dator med en enda server. De flesta kunder börjar med det här program mönstret och skalar sedan ut genom att lägga till fler webb roller eller virtuella datorer i systemet.

Det här program mönstret är användbart när:

* Du vill utföra en enkel migrering till Azure-plattformen för att utvärdera om plattformen svarar på programmets krav eller inte.
* Du vill behålla alla program nivåer som finns på samma virtuella dator i samma Azure-datacenter för att minska svars tiden mellan nivåerna.
* Du vill snabbt kunna etablera utvecklings-och test miljöer under korta tids perioder.
* Du vill utföra stress testning för varierande arbets belastnings nivåer men samtidigt vill du inte äga och underhålla många fysiska datorer hela tiden.

Följande diagram visar ett enkelt lokalt scenario och hur du kan distribuera sin moln aktive rad lösning på en enda virtuell dator i Azure.

![program mönster på 1 nivå](./media/application-patterns-development-strategies/IC728008.png)

Distribution av affärs skiktet (affärs logik och data åtkomst komponenter) på samma fysiska nivå som presentations lagret kan maximera programmets prestanda, om du inte måste använda en separat nivå på grund av skalbarhet eller säkerhets problem.

Eftersom det här är ett mycket vanligt mönster att börja med kan du hitta följande artikel vid migrering som är användbar för att flytta data till din SQL Server VM: [Migrera en databas till SQL Server på en virtuell Azure-dator](migrate-to-vm-from-sql-server.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>3-nivå (enkel): flera virtuella datorer
I det här program mönstret distribuerar du ett program på tre nivåer i Azure genom att placera varje program nivå i en annan virtuell dator. Detta ger en flexibel miljö för enkel skalnings-och skalnings scenarier. När en virtuell dator innehåller klient-/webb programmet, är den andra som är värd för dina affärs komponenter och den andra som är värd för databas servern.

Det här program mönstret är användbart när:

* Du vill utföra en migrering av komplexa databas program till Azure Virtual Machines.
* Du vill att olika program nivåer ska ligga i olika regioner. Du kan till exempel ha delade databaser som distribueras till flera regioner i rapporterings syfte.
* Du vill flytta företags program från lokala virtualiserade plattformar till Azure Virtual Machines. En detaljerad diskussion om företags program finns i [Vad är ett företags program](/previous-versions/visualstudio/aa267045(v=vs.60)).
* Du vill snabbt kunna etablera utvecklings-och test miljöer under korta tids perioder.
* Du vill utföra stress testning för varierande arbets belastnings nivåer men samtidigt vill du inte äga och underhålla många fysiska datorer hela tiden.

Följande diagram visar hur du kan placera ett enkelt 3-Nivåprogram i Azure genom att placera varje program nivå i en annan virtuell dator.

![program mönster på 3 nivåer](./media/application-patterns-development-strategies/IC728009.png)

I det här program mönstret finns det bara en virtuell dator på varje nivå. Om du har flera virtuella datorer i Azure rekommenderar vi att du konfigurerar ett virtuellt nätverk. [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) skapar en betrodd säkerhets gränser och gör det också möjligt för virtuella datorer att kommunicera sinsemellan över den privata IP-adressen. Dessutom måste du alltid kontrol lera att alla Internet anslutningar går till presentations nivån. När du följer det här programmets mönster hanterar du regler för nätverks säkerhets grupper för att kontrol lera åtkomst. Mer information finns i [Tillåt extern åtkomst till den virtuella datorn med hjälp av Azure Portal](../../../virtual-machines/windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

I diagrammet kan Internet protokoll vara TCP, UDP, HTTP eller HTTPS.

> [!NOTE]
> Det kostar inget att konfigurera ett virtuellt nätverk i Azure. Men du debiteras för den VPN-gateway som ansluter till den lokala platsen. Den här kostnaden baseras på hur lång tid anslutningen är etablerad och tillgänglig.
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>2-nivå och 3-nivå med presentations nivå utskalning
I det här program mönstret distribuerar du två-eller 3-skikts databas program till Azure Virtual Machines genom att placera varje program nivå i en annan virtuell dator. Dessutom kan du skala ut presentations nivån på grund av ökad volym av inkommande klient begär Anden.

Det här program mönstret är användbart när:

* Du vill flytta företags program från lokala virtualiserade plattformar till Azure Virtual Machines.
* Du vill skala ut presentations nivån på grund av ökad volym av inkommande klient begär Anden.
* Du vill snabbt kunna etablera utvecklings-och test miljöer under korta tids perioder.
* Du vill utföra stress testning för varierande arbets belastnings nivåer men samtidigt vill du inte äga och underhålla många fysiska datorer hela tiden.
* Du vill äga en infrastruktur miljö som kan skala upp och ned på begäran.

Följande diagram visar hur du kan placera program nivåer i flera virtuella datorer i Azure genom att skala ut presentations nivån på grund av ökad volym av inkommande klient begär Anden. Som det visas i diagrammet är Azure Load Balancer ansvarig för att distribuera trafik över flera virtuella datorer och även bestämma vilken webb server som ska anslutas till. Om du har flera instanser av webb servrarna bakom en belastningsutjämnare säkerställer du att presentations nivån blir hög tillgänglig.

![Program mönster – skala ut presentations nivå](./media/application-patterns-development-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Metod tips för mönster med 2 skikt, 3 skikt eller n-nivå som har flera virtuella datorer på en nivå
Vi rekommenderar att du placerar de virtuella datorerna som tillhör samma nivå i samma moln tjänst och i samma tillgänglighets uppsättning. Du kan till exempel placera en uppsättning webb servrar i **CloudService1** och **AvailabilitySet1** och en uppsättning databas servrar i **CloudService2** och **AvailabilitySet2**. Med en tillgänglighets uppsättning i Azure kan du placera noderna med hög tillgänglighet i olika fel domäner och uppgraderings domäner.

Om du vill utnyttja flera virtuella dator instanser av en nivå måste du konfigurera Azure Load Balancer mellan program nivåer. Om du vill konfigurera Load Balancer på varje nivå skapar du en belastningsutjämnad slut punkt på varje nivås virtuella datorer separat. För en speciell nivå måste du först skapa virtuella datorer i samma moln tjänst. Detta säkerställer att de har samma offentliga virtuella IP-adress. Skapa sedan en slut punkt på en av de virtuella datorerna på den nivån. Tilldela sedan samma slut punkt till de andra virtuella datorerna på den nivån för belastnings utjämning. Genom att skapa en belastningsutjämnad uppsättning distribuerar du trafiken över flera virtuella datorer och tillåter också Load Balancer att avgöra vilken nod som ska anslutas när en VM-nod Miss lyckas. Om du till exempel har flera instanser av webb servrarna bakom en belastningsutjämnare säkerställer du att presentations nivån blir hög tillgänglig.

Bästa praxis är att alltid se till att alla Internet anslutningar går till presentations nivån. Presentations lagret får åtkomst till affärs nivån och sedan får affärs nivån åtkomst till data nivån. Mer information om hur du tillåter åtkomst till presentations lagret finns i [Tillåt extern åtkomst till den virtuella datorn med hjälp av Azure Portal](../../../virtual-machines/windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Observera att Load Balancer i Azure fungerar på liknande sätt som belastningsutjämnare i en lokal miljö. Mer information finns i [belastnings utjämning för Azures infrastruktur tjänster](../../../virtual-machines/windows/tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Dessutom rekommenderar vi att du konfigurerar ett privat nätverk för dina virtuella datorer med hjälp av Azure Virtual Network. Detta gör det möjligt för dem att kommunicera sinsemellan över den privata IP-adressen. Mer information finns i [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>2-nivå och 3-nivå med skala på affärs nivå
I det här program mönstret distribuerar du ett databas program på 2 eller 3 nivåer till Azure Virtual Machines genom att placera varje program nivå i en annan virtuell dator. Dessutom kanske du vill distribuera program Server komponenter till flera virtuella datorer på grund av programmets komplexitet.

Det här program mönstret är användbart när:

* Du vill flytta företags program från lokala virtualiserade plattformar till Azure Virtual Machines.
* Du vill distribuera program Server komponenter till flera virtuella datorer på grund av programmets komplexitet.
* Du vill flytta affärs logiken kraftigt lokala LOB-program (Line-of-Business) till Azure Virtual Machines. LOB-program är en uppsättning kritiska dator program som är viktiga för att köra ett företag, till exempel redovisning, personal (HR), lön, hantering av leverans kedjan och resurs planerings program.
* Du vill snabbt kunna etablera utvecklings-och test miljöer under korta tids perioder.
* Du vill utföra stress testning för varierande arbets belastnings nivåer men samtidigt vill du inte äga och underhålla många fysiska datorer hela tiden.
* Du vill äga en infrastruktur miljö som kan skala upp och ned på begäran.

Följande diagram visar ett lokalt scenario och dess moln aktiverade lösning. I det här scenariot placerar du program nivåerna i flera virtuella datorer i Azure genom att skala ut affärs nivån, som innehåller affärs logik nivån och data åtkomst komponenterna. Som det visas i diagrammet är Azure Load Balancer ansvarig för att distribuera trafik över flera virtuella datorer och även bestämma vilken webb server som ska anslutas till. Om du har flera instanser av program servrarna bakom en belastningsutjämnare säkerställs hög tillgänglighet för affärs nivån. Mer information finns i [metod tips för program mönster på 2-nivå, 3-nivå eller n-nivå som har flera virtuella datorer på en nivå](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Program mönster med skala på affärs nivå](./media/application-patterns-development-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>2-nivå och 3-nivå med presentations-och affärs nivåer, skalbara och HADR
I det här program mönstret distribuerar du ett databas program på 2-eller 3-nivå till Azure Virtual Machines genom att distribuera presentations nivån (webb servern) och affärs nivå komponenterna (program Server) till flera virtuella datorer. Dessutom implementerar du lösningar för hög tillgänglighet och haveri beredskap (HADR) för dina databaser i Azure Virtual Machines.

Det här program mönstret är användbart när:

* Du vill flytta företags program från virtualiserade plattformar lokalt till Azure genom att implementera SQL Server funktioner för hög tillgänglighet och katastrof återställning.
* Du vill skala upp presentations nivån och affärs nivån på grund av en ökad volym av inkommande klient begär Anden och programmets komplexitet.
* Du vill snabbt kunna etablera utvecklings-och test miljöer under korta tids perioder.
* Du vill utföra stress testning för varierande arbets belastnings nivåer men samtidigt vill du inte äga och underhålla många fysiska datorer hela tiden.
* Du vill äga en infrastruktur miljö som kan skala upp och ned på begäran.

Följande diagram visar ett lokalt scenario och dess moln aktiverade lösning. I det här scenariot skalar du presentations nivån och affärs nivå komponenterna på flera virtuella datorer i Azure. Dessutom implementerar du teknikerna hög tillgänglighet och haveri beredskap (HADR) för SQL Server databaser i Azure.

Om du kör flera kopior av ett program på olika virtuella datorer kontrollerar du att du har belastnings Utjämnings begär anden över dem. Om du har flera virtuella datorer måste du kontrol lera att alla virtuella datorer är tillgängliga och körs vid en viss tidpunkt. Om du konfigurerar belastnings utjämning spårar Azure Load Balancer hälsan för virtuella datorer och dirigerar inkommande anrop till de felfria fungerande VM-noderna på rätt sätt. Information om hur du konfigurerar belastnings utjämning för de virtuella datorerna finns i [belastnings utjämning för Azures infrastruktur tjänster](../../../virtual-machines/windows/tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Om du har flera instanser av webb-och program servrar bakom en belastningsutjämnare säkerställs hög tillgänglighet för presentations-och affärs nivåerna.

![Skala ut och hög tillgänglighet](./media/application-patterns-development-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Metod tips för program mönster som kräver SQL-HADR
När du konfigurerar SQL Server lösningar för hög tillgänglighet och haveri beredskap i Azure Virtual Machines är det obligatoriskt att konfigurera ett virtuellt nätverk för dina virtuella datorer med [azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) . Virtuella datorer inom en Virtual Network har en stabil privat IP-adress även efter drift avbrott i tjänsten, vilket innebär att du kan undvika uppdaterings tiden som krävs för DNS-namnmatchning. Dessutom kan du med det virtuella nätverket utöka ditt lokala nätverk till Azure och skapa en betrodd säkerhets gränser. Om ditt program till exempel har begränsningar för företagets domän (t. ex. Windows-autentisering, Active Directory), är det nödvändigt att konfigurera [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) .

De flesta kunder, som kör produktions kod på Azure, behåller både primära och sekundära repliker i Azure.

Omfattande information och självstudier om teknik för hög tillgänglighet och haveri beredskap finns i [hög tillgänglighet och haveri beredskap för SQL Server på Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="2-tier-and-3-tier-using-azure-virtual-machines-and-cloud-services"></a>2-nivå och 3-nivå med Azure Virtual Machines och Cloud Services
I det här program mönstret distribuerar du 2-eller 3-Nivåprogram till Azure med hjälp av både [Azure Cloud Services](../../../cloud-services/cloud-services-choose-me.md) (Web och Worker roles-Platform as a Service (PaaS)) och [Azure Virtual Machines](../../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (infrastruktur som en tjänst (IaaS)). Att använda [azure Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) för presentations nivå/affärs nivå och SQL Server i [Azure Virtual Machines](../../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för data nivån är fördelaktigt för de flesta program som körs på Azure. Orsaken är att en beräknings instans som körs på Cloud Services ger en enkel hantering, distribution, övervakning och skalbarhet.

Med Cloud Services upprätthåller Azure infrastrukturen åt dig, utför rutin underhåll, korrigerar operativ systemen och försöker återställa från fel i tjänsten och maskin varan. När ditt program behöver skalnings-, automatisk-och manuella skalnings alternativ är tillgängliga för ditt moln tjänst projekt genom att öka eller minska antalet instanser eller virtuella datorer som används av ditt program. Dessutom kan du använda den lokala Visual Studio för att distribuera programmet till ett moln tjänst projekt i Azure.

I sammanfattning, om du inte vill äga omfattande administrativa uppgifter för presentationen/affärs nivån och programmet inte kräver någon komplex konfiguration av program vara eller operativ system, använder du Azure Cloud Services. Om Azure SQL Database inte har stöd för alla funktioner som du söker efter, använder du SQL Server på en virtuell Azure-dator för data nivån. Att köra ett program på Azure Cloud Services och lagra data i Azure Virtual Machines kombinerar fördelarna med båda tjänsterna. En detaljerad jämförelse finns i avsnittet i det här avsnittet om hur du [Jämför utvecklings strategier i Azure](#comparing-web-development-strategies-in-azure).

I det här program mönstret innehåller presentations nivån en webbroll, som är en Cloud Services-komponent som körs i Azures körnings miljö och den är anpassad för webb programs programmering som stöds av IIS och ASP.NET. Verksamhets-eller backend-nivån innehåller en arbets roll, som är en Cloud Services komponent som körs i Azures körnings miljö och som är användbar för generaliserad utveckling och som kan utföra bakgrunds bearbetning för en webb roll. Databas nivån finns i en SQL Server virtuell dator i Azure. Kommunikationen mellan presentations nivån och databas nivån sker direkt eller över affärs nivån – Worker Role-komponenter.

Det här program mönstret är användbart när:

* Du vill flytta företags program från virtualiserade plattformar lokalt till Azure genom att implementera SQL Server funktioner för hög tillgänglighet och katastrof återställning.
* Du vill äga en infrastruktur miljö som kan skala upp och ned på begäran.
* Azure SQL Database stöder inte alla funktioner som ditt program eller din databas behöver.
* Du vill utföra stress testning för varierande arbets belastnings nivåer men samtidigt vill du inte äga och underhålla många fysiska datorer hela tiden.

Följande diagram visar ett lokalt scenario och dess moln aktiverade lösning. I det här scenariot placerar du presentations nivån i webb roller, affärs nivån i arbets roller, men data nivån i virtuella datorer i Azure. Om du kör flera kopior av presentations nivån i olika webb roller säkerställs belastnings Utjämnings begär anden över dem. När du kombinerar Azure Cloud Services med Azure Virtual Machines rekommenderar vi att du även konfigurerar [Azure-Virtual Network](../../../virtual-network/virtual-networks-overview.md) . Med [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)kan du ha stabila och beständiga privata IP-adresser inom samma moln tjänst i molnet. När du har definierat ett virtuellt nätverk för dina virtuella datorer och moln tjänster kan de börja kommunicera sinsemellan över den privata IP-adressen. Dessutom ger Virtual Machines och Azure Web/Worker-roller i samma [Azure-Virtual Network](../../../virtual-network/virtual-networks-overview.md) låg latens och säkrare anslutning. Mer information finns i [Vad är en moln tjänst](../../../cloud-services/cloud-services-choose-me.md).

Som det visas i diagrammet, Azure Load Balancer distribuerar trafik över flera virtuella datorer och avgör även vilken webb server eller program server som ska anslutas till. Om du har flera instanser av webb-och program servrarna bakom en belastningsutjämnare säkerställs hög tillgänglighet för presentations nivån och affärs nivån. Mer information finns i [metod tips för program mönster som kräver SQL-hadr](#best-practices-for-application-patterns-requiring-sql-hadr).

![Diagrammet visar lokala fysiska eller virtuella datorer som är anslutna till webb roll instanser i ett virtuellt Azure-nätverk via en Azure Load Balancer.](./media/application-patterns-development-strategies/IC728013.png)

En annan metod för att implementera det här program mönstret är att använda en konsol IDE rad webb roll som innehåller både presentations nivå och affärs nivå komponenter som visas i följande diagram. Det här program mönstret är användbart för program som kräver tillstånds känslig design. Eftersom Azure tillhandahåller tillstånds lösa Compute-noder i Web-och Worker-roller rekommenderar vi att du implementerar en logik för att lagra sessionstillstånd med någon av följande tekniker: [Azure caching](https://azure.microsoft.com/documentation/services/azure-cache-for-redis/), [azure Table Storage](../../../cosmos-db/tutorial-develop-table-dotnet.md) eller [Azure SQL Database](../../database/sql-database-paas-overview.md).

![Diagrammet visar lokala fysiska eller virtuella datorer som är anslutna till de konsoliderade webb-och arbets roll instanserna i ett virtuellt Azure-nätverk.](./media/application-patterns-development-strategies/IC728014.png)

## <a name="pattern-with-azure-virtual-machines-azure-sql-database-and-azure-app-service-web-apps"></a>Mönster med Azure Virtual Machines, Azure SQL Database och Azure App Service (Web Apps)
Det främsta målet med det här program mönstret är att visa hur du kombinerar IaaS-komponenter (Azure Infrastructure as a Service) med Azures PaaS (Platform-as-a-service Components) i din lösning. Det här mönstret fokuserar på Azure SQL Database för Relations data lagring. Den omfattar inte SQL Server på en virtuell Azure-dator, som är en del av Azure-infrastrukturen som ett tjänst erbjudande.

I det här program mönstret distribuerar du ett databas program till Azure genom att placera presentations-och affärs nivåerna i samma virtuella dator och komma åt en databas i Azure SQL Database-servrar (SQL Database). Du kan implementera presentations nivån med hjälp av traditionella IIS-baserade webb lösningar. Eller så kan du implementera en kombinerad presentations-och affärs nivå med hjälp av [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/web/).

Det här program mönstret är användbart när:

* Du har redan en befintlig SQL Database Server som kon figurer ATS i Azure och du vill testa programmet snabbt.
* Du vill testa funktionerna i Azure-miljön.
* Du vill snabbt kunna etablera utvecklings-och test miljöer under korta tids perioder.
* Din affärs logik och dina data åtkomst komponenter kan vara fristående i ett webb program.

Följande diagram visar ett lokalt scenario och dess moln aktiverade lösning. I det här scenariot placerar du program nivåer på en enda virtuell dator i Azure och får åtkomst till data i Azure SQL Database.

![Mönster för blandat program](./media/application-patterns-development-strategies/IC728015.png)

Om du väljer att implementera en kombinerad webb-och program nivå med hjälp av Azure Web Apps, rekommenderar vi att du behåller den mittersta nivån eller program nivån som dll: er (Dynamic-Link Libraries) i kontexten för ett webb program.

Läs dessutom igenom rekommendationerna i avsnittet [jämföra webb utvecklings strategier i Azure i](#comparing-web-development-strategies-in-azure) slutet av den här artikeln för att lära dig mer om programmerings tekniker.

## <a name="n-tier-hybrid-application-pattern"></a>Mönster för hybrid program i N-nivå
På n-nivå hybrid program mönster implementerar du ditt program på flera nivåer som distribueras mellan lokalt och Azure. Därför kan du skapa ett flexibelt och återanvändbart hybrid system, som du kan ändra eller lägga till en nivå utan att ändra andra nivåer. För att utöka företagets nätverk till molnet använder du [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) -tjänsten.

Det här hybrid program mönstret är användbart när:

* Du vill skapa program som körs delvis i molnet och delvis lokalt.
* Du vill migrera vissa eller alla element i ett befintligt lokalt program till molnet.
* Du vill flytta företags program från lokala virtualiserade plattformar till Azure.
* Du vill äga en infrastruktur miljö som kan skala upp och ned på begäran.
* Du vill snabbt kunna etablera utvecklings-och test miljöer under korta tids perioder.
* Du vill ha ett kostnads effektivt sätt att göra säkerhets kopior för företags databas program.

Följande diagram visar ett hybrid program mönster på n-nivå som sträcker sig över både lokalt och Azure. Som du ser i diagrammet innehåller den lokala infrastrukturen [Active Directory Domain Services](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831484(v=ws.11)) domänkontrollant som stöder användarautentisering och auktorisering. Observera att diagrammet visar ett scenario, där vissa delar av data nivån är aktiva i ett lokalt Data Center, medan vissa delar av data nivån är Live i Azure. Beroende på programmets behov kan du implementera flera andra hybrid scenarier. Du kan till exempel behålla presentations nivån och affärs nivån i en lokal miljö, men data nivån i Azure.

![Program mönster på N-nivå](./media/application-patterns-development-strategies/IC728016.png)

I Azure kan du använda Active Directory som en fristående moln katalog för din organisation, eller också kan du integrera befintliga lokala Active Directory med [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Som det visas i diagrammet kan affärs nivå komponenterna komma åt flera data källor, till exempel för att [SQL Server i Azure](sql-server-on-azure-vm-iaas-what-is-overview.md) via en privat intern IP-adress, till lokala SQL Server via [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)eller [SQL Database](../../database/sql-database-paas-overview.md) med hjälp av .NET Framework Data Provider Technologies. I det här diagrammet är Azure SQL Database en valfri data lagrings tjänst.

På n-nivå hybrid program mönster kan du implementera följande arbets flöde i den ordning som anges:

1. Identifiera företags databas program som behöver flyttas upp till molnet med hjälp av [Microsoft Assessment and Planning Toolkit (mappa) Toolkit](https://microsoft.com/map). Kart verktyget samlar in inventerings-och prestanda data från datorer som du överväger för virtualisering och ger rekommendationer om kapacitets-och utvärderings planeringen.
2. Planera de resurser och den konfiguration som krävs i Azure-plattformen, till exempel lagrings konton och virtuella datorer.
3. Konfigurera nätverks anslutning mellan företags nätverket lokalt och [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md). Använd någon av följande två metoder för att konfigurera anslutningen mellan företags nätverket lokalt och en virtuell dator i Azure:
   
   1. Upprätta en anslutning mellan lokalt och Azure via offentliga slut punkter på en virtuell dator i Azure. Den här metoden ger enkel konfiguration och gör att du kan använda SQL Server autentisering på den virtuella datorn. Dessutom kan du konfigurera regler för nätverks säkerhets grupper för att styra offentlig trafik till den virtuella datorn. Mer information finns i [Tillåt extern åtkomst till den virtuella datorn med hjälp av Azure Portal](../../../virtual-machines/windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   2. Upprätta en anslutning mellan lokal och Azure via Azures tunnel för virtuella privata nätverk (VPN). Med den här metoden kan du utöka domän principer till en virtuell dator i Azure. Dessutom kan du konfigurera brand Väggs regler och använda Windows-autentisering på den virtuella datorn. För närvarande stöder Azure säkra VPN-anslutningar för plats-till-plats-VPN och punkt-till-plats:
      
      * Med säker plats-till-plats-anslutning kan du upprätta en nätverks anslutning mellan ditt lokala nätverk och ditt virtuella nätverk i Azure. Vi rekommenderar att du ansluter din lokala data Center miljö till Azure.
      * Med säker punkt-till-plats-anslutning kan du upprätta en nätverks anslutning mellan ditt virtuella nätverk i Azure och dina enskilda datorer som kör var som helst. Det rekommenderas främst för utvecklings-och test syfte.
      
      Information om hur du ansluter till SQL Server i Azure finns i [ansluta till en SQL Server virtuell dator på Azure](ways-to-connect-to-sql.md).
4. Konfigurera schemalagda jobb och aviseringar som säkerhetskopierar lokala data på en virtuell dator disk i Azure. Mer information finns i [SQL Server säkerhets kopiering och återställning med Azure Blob Storage-tjänsten](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) och [säkerhets kopiering och återställning för SQL Server på Azure-Virtual Machines](../../../azure-sql/virtual-machines/windows/backup-restore.md).
5. Beroende på programmets behov kan du implementera ett av följande tre vanliga scenarier:
   
   1. Du kan behålla din webb server, program Server och inkänsliga data på en databas server i Azure, medan du behåller känsliga data lokalt.
   2. Du kan hålla din webb server och program server lokalt medan du är databas servern i en virtuell dator i Azure.
   3. Du kan behålla databas servern, webb servern och program servern lokalt, medan du behåller databas replikerna i virtuella datorer i Azure. Med den här inställningen kan lokala webb servrar eller rapporterings program få åtkomst till databas repliker i Azure. Därför kan du uppnå att minska arbets belastningen i en lokal databas. Vi rekommenderar att du implementerar det här scenariot för tunga Läs arbets belastningar och utvecklings syfte. Information om hur du skapar databas repliker i Azure finns AlwaysOn-tillgänglighetsgrupper vid [hög tillgänglighet och haveri beredskap för SQL Server på Azure-Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="comparing-web-development-strategies-in-azure"></a>Jämföra webb utvecklings strategier i Azure
Om du vill implementera och distribuera ett SQL Server-baserat program med flera nivåer i Azure kan du använda någon av följande två programmerings metoder:

* Konfigurera en traditionell webb server (IIS-Internet Information Services) i Azure och få åtkomst till databaser i SQL Server på Azure Virtual Machines.
* Implementera och distribuera en moln tjänst till Azure. Kontrol lera sedan att den här moln tjänsten har åtkomst till databaser i SQL Server på Azure Virtual Machines. En moln tjänst kan innehålla flera webb-och arbets roller.

Följande tabell innehåller en jämförelse av traditionell webb utveckling med Azure Cloud Services och Azure Web Apps med avseende på SQL Server på Azure Virtual Machines. Tabellen innehåller Azure Web Apps eftersom det är möjligt att använda SQL Server i en virtuell Azure-dator som data källa för Azure Web Apps via dess offentliga virtuella IP-adress eller DNS-namn.

|  | Traditionell webb utveckling i Azure Virtual Machines | Moln tjänster i Azure | Webbhotell med Azure Web Apps |
| --- | --- | --- | --- |
| **Program-migrering från lokal plats** |Befintliga program i befintligt skick. |Program behöver webb-och arbets roller. |Befintliga program som är anpassade för fristående webb program och webb tjänster som kräver snabb skalbarhet. |
| **Utveckling och distribution** |Visual Studio, WebMatrix, Visual Web Developer, WebDeploy, FTP, TFS, IIS Manager, PowerShell. |Visual Studio, Azure SDK, TFS, PowerShell. Varje moln tjänst har två miljöer som du kan använda för att distribuera tjänst paketet och konfigurationen: mellanlagring och produktion. Du kan distribuera en moln tjänst till mellanlagrings miljön för att testa den innan du befordrar den till produktion. |Visual Studio, WebMatrix, Visual Web Developer, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, mercurial, TFS, Web Deploy, PowerShell. |
| **Administration och installation** |Du ansvarar för administrativa uppgifter om program, data, brand Väggs regler, virtuellt nätverk och operativ system. |Du ansvarar för administrativa uppgifter om program, data, brand Väggs regler och virtuella nätverk. |Du ansvarar enbart för administrativa uppgifter för programmet och data. |
| **Hög tillgänglighet och haveri beredskap (HADR)** |Vi rekommenderar att du placerar virtuella datorer i samma tillgänglighets uppsättning och i samma moln tjänst. Att behålla de virtuella datorerna i samma tillgänglighets uppsättning kan Azure placera noderna med hög tillgänglighet i olika fel domäner och uppgraderings domäner. Om du behåller de virtuella datorerna i samma moln tjänst kan belastnings utjämning och virtuella datorer kommunicera direkt med varandra via det lokala nätverket i ett Azure-datacenter.<br/><br/>Du ansvarar för att implementera en lösning för hög tillgänglighet och haveri beredskap för SQL Server på Azure Virtual Machines för att undvika avbrott. För HADR tekniker som stöds, se [hög tillgänglighet och haveri beredskap för SQL Server på Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md).<br/><br/>Du ansvarar för att säkerhetskopiera dina egna data och program.<br/><br/>Azure kan flytta dina virtuella datorer om värd datorn i data centret Miss lyckas på grund av maskin varu problem. Dessutom kan det finnas ett planerat avbrott i den virtuella datorn när värddatorn uppdateras för säkerhets-eller program uppdateringar. Därför rekommenderar vi att du behåller minst två virtuella datorer på varje program nivå för att säkerställa kontinuerlig tillgänglighet. Azure tillhandahåller inte service avtal för en enda virtuell dator. |Azure hanterar felen som orsakas av den underliggande maskin varan eller operativ systemets program vara. Vi rekommenderar att du implementerar flera instanser av en webb-eller arbets roll för att säkerställa hög tillgänglighet för ditt program. Mer information finns i [Cloud Services, Virtual Machines och Virtual Network serviceavtal](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/).<br/><br/>Du ansvarar för att säkerhetskopiera dina egna data och program.<br/><br/>För databaser som finns i en SQL Server-databas på en virtuell Azure-dator ansvarar du för att implementera en lösning för hög tillgänglighet och haveri beredskap för att undvika eventuella drift stopp. För HDAR tekniker som stöds, se hög tillgänglighet och haveri beredskap för SQL Server på Azure Virtual Machines.<br/><br/>**SQL Server databas spegling**: Använd med Azure Cloud Services (webb-/arbets roller). SQL Server virtuella datorer och ett moln tjänst projekt kan finnas i samma Azure-Virtual Network. Om SQL Server VM inte finns i samma Virtual Network måste du skapa ett SQL Server-alias för att dirigera kommunikationen till instansen av SQL Server. Dessutom måste aliasnamnet matcha SQL Server namn. |Hög tillgänglighet ärvs från Azure Worker-roller, Azure Blob Storage och Azure SQL Database. Azure Storage behåller till exempel tre repliker av alla BLOB-, tabell-och Queue-data. Vid ett och samma tillfälle behåller Azure SQL Database tre repliker av data som körs, en primär replik och två sekundära repliker. Mer information finns i [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) och [Azure SQL Database](../../database/sql-database-paas-overview.md).<br/><br/>När du använder SQL Server i en virtuell Azure-dator som en data källa för Azure Web Apps bör du tänka på att Azure-Web Apps inte stöder Azure Virtual Network. Med andra ord måste alla anslutningar från Azure Web Apps för att SQL Server virtuella datorer i Azure gå igenom offentliga slut punkter för virtuella datorer. Detta kan orsaka vissa begränsningar för scenarier med hög tillgänglighet och katastrof återställning. Klient programmet på Azure Web Apps som ansluter till SQL Server VM med databas spegling skulle till exempel inte kunna ansluta till den nya primära servern som databas spegling kräver att du konfigurerar Azure Virtual Network mellan SQL Server virtuella datorer i Azure. Därför stöds inte användning av **SQL Server databas spegling** med Azure Web Apps för närvarande.<br/><br/>**SQL Server AlwaysOn-tillgänglighetsgrupper**: du kan konfigurera AlwaysOn-tillgänglighetsgrupper när du använder Azure Web Apps med SQL Server virtuella datorer i Azure. Men du måste konfigurera lyssnaren för AlwaysOn-tillgänglighetsgrupper för att dirigera kommunikationen till den primära repliken via offentliga belastningsutjämnade slut punkter. |
| **Anslutning på flera platser** |Du kan använda Azure Virtual Network för att ansluta till en lokal plats. |Du kan använda Azure Virtual Network för att ansluta till en lokal plats. |Azure-Virtual Network stöds. Mer information finns i [Web Apps Virtual Network-integration](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/). |
| **Skalbarhet** |Skala upp är tillgängligt genom att öka storleken på virtuella datorer eller lägga till fler diskar. Mer information om storlekar för virtuella datorer finns i [storlekar för virtuella datorer för Azure](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).<br/><br/>**För databas server**: utskalning är tillgängligt via databas partitionerings teknik och SQL Server AlwaysOn-tillgänglighetsgrupper.<br/><br/>För tung Read-arbetsbelastningar kan du använda [AlwaysOn-tillgänglighetsgrupper](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) på flera sekundära noder och SQL Server replikering.<br/><br/>För stora Skriv arbets belastningar kan du implementera horisontella partitionerings data över flera fysiska servrar för att ge programskalning.<br/><br/>Dessutom kan du implementera en skalbarhet med hjälp av [SQL Server med data beroende routning](/previous-versions/sql/sql-server-2005/administrator/cc966448(v=technet.10)). Med data Dependent routing (DDR) måste du implementera mekanismen för partitionering i klient programmet, vanligt vis på nivån affärs nivå, för att dirigera databas förfrågningar till flera SQL Server noder. Affärs nivån innehåller mappningar till hur data partitioneras och vilka noder som innehåller data.<br/><br/>Du kan skala program som kör virtuella datorer. Mer information finns i [skala ett program](../../../cloud-services/cloud-services-how-to-scale-portal.md).<br/><br/>**Viktigt meddelande**: funktionen för automatisk **skalning** i Azure gör att du automatiskt kan öka eller minska de virtuella datorer som används av ditt program. Den här funktionen garanterar att slut användar upplevelsen inte påverkas negativt under perioder med hög belastning och att virtuella datorer stängs av när efter frågan är låg. Vi rekommenderar att du inte anger alternativet för autoskalning för moln tjänsten om det innehåller SQL Server virtuella datorer. Orsaken är att funktionen för autoskalning gör att Azure kan aktivera en virtuell dator när processor användningen i den virtuella datorn är högre än tröskelvärdet och stänga av en virtuell dator när CPU-användningen blir lägre än den. Funktionen för autoskalning är användbar för tillstånds lösa program, till exempel webb servrar, där en virtuell dator kan hantera arbets belastningen utan referenser till något tidigare tillstånd. Funktionen för autoskalning är dock inte användbar för tillstånds känsliga program, till exempel SQL Server, där endast en instans kan skriva till databasen. |Skala upp är tillgängligt genom att använda flera webb-och arbets roller. Mer information om storlekar för virtuella datorer för webb roller och arbets roller finns i [Konfigurera storlekar för Cloud Services](../../../cloud-services/cloud-services-sizes-specs.md).<br/><br/>När du använder **Cloud Services** kan du definiera flera roller för att distribuera bearbetning och även uppnå flexibel skalning av ditt program. Varje moln tjänst innehåller en eller flera webb roller och/eller arbets roller, var och en med sina egna programfiler och-konfigurationer. Du kan skala upp en moln tjänst genom att öka antalet roll instanser (virtuella datorer) som har distribuerats för en roll och skala ned en moln tjänst genom att minska antalet roll instanser. Detaljerad information finns i [modeller för Azure-körning](../../../cloud-services/cloud-services-choose-me.md).<br/><br/>Skalbarhet är tillgängligt via inbyggt stöd för hög tillgänglighet i Azure via [Cloud Services, Virtual Machines och Virtual Network serviceavtal](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) och Load Balancer.<br/><br/>För ett program med flera nivåer rekommenderar vi att du ansluter webb-/arbets Rolls program till virtuella databas Server-datorer via Azure Virtual Network. Dessutom tillhandahåller Azure belastnings utjämning för virtuella datorer i samma moln tjänst och sprider användar förfrågningar över dem. Virtuella datorer som är anslutna på det här sättet kan kommunicera direkt med varandra via det lokala nätverket i ett Azure-datacenter.<br/><br/>Du kan ställa in **autoskalning** på Azure Portal samt schema tiderna. Mer information finns i [så här konfigurerar du automatisk skalning för en moln tjänst i portalen](../../../cloud-services/cloud-services-how-to-scale-portal.md). |**Skala upp och ned**: du kan öka/minska storleken på instansen (VM) som reserver ATS för webbplatsen.<br/><br/>Skala ut: du kan lägga till fler reserverade instanser (VM) för webbplatsen.<br/><br/>Du kan ställa in **autoskalning** på portalen samt schema tiderna. Mer information finns i [skala Web Apps](../../../app-service/manage-scale-up.md). |

Mer information om hur du väljer mellan dessa programmerings metoder finns i [Azure Web Apps, Cloud Services och virtuella datorer: när du ska använda dem](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="next-steps"></a>Nästa steg
Mer information om hur du kör SQL Server på Azure Virtual Machines finns i [SQL Server på azure Virtual Machines översikt](sql-server-on-azure-vm-iaas-what-is-overview.md).