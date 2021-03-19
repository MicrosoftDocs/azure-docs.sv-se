---
title: Integritet för Azure-infrastruktur
description: Lär dig mer om Azures infrastruktur integritet och de steg som Microsoft vidtar för att skydda den, till exempel virus genomsökningar på program varu komponent versioner.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: b0df14dfcf66f53e01989a27a95afa5e19fcb2fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87543647"
---
# <a name="azure-infrastructure-integrity"></a>Integritet för Azure-infrastruktur

## <a name="software-installation"></a>Program varu installation
Alla komponenter i program varu stacken som är installerade i Azure-miljön är anpassade som skapats efter processen Microsoft Security Development Lifecycle (SDL). Alla program varu komponenter, inklusive operativ system avbildningar och SQL Database, distribueras som en del av processen för hantering av ändrings hantering och versions hantering. Det operativ system som körs på alla noder är en anpassad version av Windows Server 2008 eller Windows Server 2012. Den exakta versionen väljs av Fabric Controller (FC) enligt den roll den avser för att spela upp operativ systemet. Dessutom tillåter värd-OS inte installation av obehöriga program varu komponenter.

Vissa Azure-komponenter distribueras som Azure-kunder på en gäst-VM som körs på ett gäst operativ system.

## <a name="virus-scans-on-builds"></a>Virus genomsökningar på versioner
Azures program varu komponent (inklusive OS)-versioner måste genomgå en virus genomsökning som använder Antivirus verktyget Endpoint Protection. Varje virus genomsökning skapar en logg i den associerade build-katalogen, som beskriver vad som genomsöktes och resultatet av genomsökningen. Virus genomsökningen ingår i versions käll koden för varje komponent i Azure. Koden flyttas inte till produktion utan en ren och lyckad virus genomsökning. Om det uppstår problem är build-versionen frusen och går sedan till säkerhets teamen inom Microsoft Security för att identifiera var koden "falsk" angavs i versionen.

## <a name="closed-and-locked-environment"></a>Stängd och låst miljö
Som standard har inte Azure-infrastrukturens noder och virtuella gäst datorer användar konton som skapats på dem. Dessutom inaktive ras även standard konton för Windows-administratörer. Administratörer från Azure Live-supporten kan, med korrekt autentisering, logga in på dessa datorer och administrera Azures produktions nätverk för reparation av nöd reparationer.

## <a name="azure-sql-database-authentication"></a>Azure SQL Database-autentisering
Som vid alla implementeringar av SQL Server måste hanteringen av användar konton vara tätt kontrollerad. Azure SQL Database stöder endast SQL Server autentisering. För att komplettera en kunds data säkerhets modell bör användar konton med starka lösen ord och som kon figurer ATS med specifika rättigheter också användas.

## <a name="acls-and-firewalls-between-the-microsoft-corporate-network-and-an-azure-cluster"></a>ACL: er och brand väggar mellan Microsoft företags nätverket och ett Azure-kluster
Åtkomst kontrol listor (ACL: er) och brand väggar mellan tjänst plattformen och Microsoft Corporate Network Protect SQL Database-instanser från obehörig Insider-åtkomst. Dessutom kan endast användare från IP-adressintervall från Microsofts företags nätverk komma åt den Windows Fabric plattforms hanterings slut punkten.

## <a name="acls-and-firewalls-between-nodes-in-a-sql-database-cluster"></a>ACL: er och brand väggar mellan noder i ett SQL Database kluster
Som ett ytterligare skydd, som en del av djupgående strategin, har ACL: er och en brand vägg implementerats mellan noder i ett SQL Database kluster. All kommunikation i Windows Fabric plattforms kluster samt all kod som körs är betrodd.

## <a name="custom-monitoring-agents"></a>Anpassade övervaknings agenter
SQL Database använder anpassade övervaknings agenter (MAs), även kallade övervaknings agenter, för att övervaka hälsan för SQL Database klustret.

## <a name="web-protocols"></a>Webb protokoll

### <a name="role-instance-monitoring-and-restart"></a>Övervakning och omstart av roll instans
Azure säkerställer att alla distribuerade, aktiva roller (Internet-riktad webb eller backend-arbetsrollen) är underkastade hälso övervakning för att säkerställa att de effektivt och effektivt levererar de tjänster för vilka de har etablerats. Om en roll skadas, antingen genom ett allvarligt fel i programmet som är värd för eller ett underliggande konfigurations problem inom roll instansen, identifierar FC problemet i roll instansen och initierar ett korrekt tillstånd.

### <a name="compute-connectivity"></a>Compute-anslutning
Azure säkerställer att det distribuerade programmet eller tjänsten kan uppnås via webbaserade standard protokoll. Virtuella instanser av Internet-riktade webb roller har extern Internet anslutning och kan uppnås direkt av webb användare. För att skydda känsligheten och integriteten för de åtgärder som arbets roller utför för de offentligt tillgängliga virtuella webb rollernas virtuella instanser, har virtuella instanser av backend-arbetsroller extern Internet anslutning, men kan inte nås direkt av externa webb användare.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns i:

- [Azure-anläggningar, lokal och fysisk säkerhet](physical-security.md)
- [Tillgänglighet för Azure-infrastruktur](infrastructure-availability.md)
- [Komponenter och gränser för Azure information system](infrastructure-components.md)
- [Azure nätverks arkitektur](infrastructure-network.md)
- [Azures produktions nätverk](production-network.md)
- [Azure SQL Database säkerhetsfunktioner](infrastructure-sql.md)
- [Åtgärder och hantering av Azure-produktion](infrastructure-operations.md)
- [Övervakning av Azure-infrastruktur](infrastructure-monitoring.md)
- [Data skydd för Azure-kunder](protection-customer-data.md)
