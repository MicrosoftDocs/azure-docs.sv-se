---
title: Övervakning av Azure-infrastruktur
description: Lär dig mer om infrastruktur övervaknings aspekter av Azures produktions nätverk, till exempel sårbarhets sökning.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 7b75c9dc874a41d4221c55a8b00dd12d943e80fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87542950"
---
# <a name="azure-infrastructure-monitoring"></a>Övervakning av Azure-infrastruktur   

## <a name="configuration-and-change-management"></a>Konfiguration och ändrings hantering
Azure granskar och uppdaterar konfigurations inställningar och grundläggande konfigurations inställningar för maskin vara, program vara och nätverks enheter varje år. Ändringar utvecklas, testas och godkänns innan du går in i produktions miljön från en utvecklings-och/eller test miljö.

De grundläggande konfigurationerna som krävs för Azure-baserade tjänster granskas av Azure-teamet för säkerhet och efterlevnad och av service team. En granskning av service team är en del av testningen som inträffar innan du distribuerar produktions tjänsten.

## <a name="vulnerability-management"></a>Sårbarhetshantering
Hantering av säkerhets uppdateringar hjälper till att skydda system från kända sårbarheter. Azure använder integrerade distributions system för att hantera distribution och installation av säkerhets uppdateringar för Microsoft-programvara. Azure kan även rita på resurserna i Microsoft Security Response Center (MSRC). MSRC identifierar, övervakar, svarar på och löser säkerhets incidenter och moln sårbarheter dygnet runt, varje dag på året.

## <a name="vulnerability-scanning"></a>Sårbarhetsgenomsökning
Sårbarhets sökning utförs på serveroperativ system, databaser och nätverks enheter. Sårbarhets genomsökningarna utförs varje kvartal som minst en gång. Azure-kontrakt med oberoende utvärderare för att utföra inträngande tester av Azure-gränser. Röda-team-övningar utförs också rutinmässigt och resultaten används för att förbättra säkerheten.

## <a name="protective-monitoring"></a>Skydds övervakning
Azure-säkerhet har definierat krav för aktiv övervakning. Service Teams konfigurera aktiva övervaknings verktyg i enlighet med dessa krav. I aktiva övervaknings verktyg ingår MMA (Microsoft Monitoring Agent) och System Center Operations Manager. Dessa verktyg är konfigurerade för att tillhandahålla tids aviseringar till Azures säkerhets personal i situationer som kräver omedelbar åtgärd.

## <a name="incident-management"></a>Incidenthantering
Microsoft implementerar en hanterings process för säkerhets incidenter för att under lätta ett samordnat svar till incidenter, om ett sådant inträffar.

Om Microsoft är medveten om obehörig åtkomst till kund information som lagras på sin utrustning eller i dess lokaler, eller om den blir medveten om obehörig åtkomst till sådan utrustning eller anläggningar som leder till förlust, avslöjande eller ändring av kund information, vidtar Microsoft följande åtgärder:

- Meddela kunden om säkerhets incidenten.
- Undersöker säkerhets incidenten och ger kunderna detaljerad information om säkerhets incidenten.
- Vidtar rimliga och snabba steg för att minimera effekterna och minimera eventuella skador som orsakas av säkerhets incidenten.

Ett ramverk för incident hantering har upprättats som definierar roller och tilldelar ansvars områden. Hanterings teamet för Azure-Säkerhetsincidenten ansvarar för att hantera säkerhets incidenter, inklusive eskalering och för att säkerställa medverkan av specialiserade team vid behov. Azure Operations Manager ansvarar för att övervaka undersökningen och lösningarna för säkerhets-och sekretess incidenter.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns i:

- [Azure-anläggningar, lokal och fysisk säkerhet](physical-security.md)
- [Tillgänglighet för Azure-infrastruktur](infrastructure-availability.md)
- [Komponenter och gränser för Azure information system](infrastructure-components.md)
- [Azure nätverks arkitektur](infrastructure-network.md)
- [Azures produktions nätverk](production-network.md)
- [Azure SQL Database säkerhetsfunktioner](infrastructure-sql.md)
- [Åtgärder och hantering av Azure-produktion](infrastructure-operations.md)
- [Integritet för Azure-infrastruktur](infrastructure-integrity.md)
- [Data skydd för Azure-kunder](protection-customer-data.md)
