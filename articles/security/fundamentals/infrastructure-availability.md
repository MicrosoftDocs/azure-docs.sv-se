---
title: Tillgänglighet för Azure-infrastrukturen – Azure-säkerhet
description: Den här artikeln innehåller information om vad Microsoft gör för att skydda Azure-infrastrukturen och tillhandahålla maximal tillgänglighet för kundernas data.
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
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: c50c4faf47caf0a7519d61fdc8989ec9fd809d78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "68727219"
---
# <a name="azure-infrastructure-availability"></a>Tillgänglighet för Azure-infrastruktur
Den här artikeln innehåller information om vad Microsoft gör för att skydda Azure-infrastrukturen och tillhandahålla maximal tillgänglighet för kundernas data. Azure ger robust tillgänglighet, baserat på omfattande redundans som uppnås med virtualiseringsteknik.

## <a name="temporary-outages-and-natural-disaster"></a>Tillfälliga avbrott och natur katastrofer
Microsoft Cloud infrastruktur och drift team design, skapar, arbetar med och förbättrar säkerheten för moln infrastrukturen. Det här teamet garanterar att Azure-infrastrukturen ger hög tillgänglighet och tillförlitlighet, hög effektivitet och smart skalbarhet. Teamet tillhandahåller ett säkrare, privat och betrott moln.

Avbrotts fria strömförsörjningar och stora banker av batterier ser till att elektriciteten förblir kontinuerlig om ett strömavbrott inträffar på kort sikt. Nöd generatorer ger säkerhets kopierings kraft för utökade avbrott och planerat underhåll. Om en naturlig haveri inträffar kan data centret använda bränsle reserver på plats.

Höghastighets och robusta fiber optiska nätverk ansluter Data Center med andra större hubbar och Internet användare. Compute Nodes är värd för arbets belastningar närmare användare för att minska svars tiden, tillhandahålla GEO-redundans och öka den totala tjänste återhämtningen. Ett team med tekniker arbetar dygnet runt för att säkerställa att tjänsterna är tillgängliga permanent.

Microsoft säkerställer hög tillgänglighet genom avancerad övervakning och incident hantering, service support och funktioner för säkerhets kopiering av redundans. Geografiskt distribuerade Microsoft Operations Center driver 24/7/365. Azure-nätverket är ett av världens största i världen. Det fiber optiska och innehålls distributions nätverket ansluter Data Center och Edge-noder för att säkerställa hög prestanda och tillförlitlighet.

## <a name="disaster-recovery"></a>Haveriberedskap
Azure håller dina data varaktiga på två platser. Du kan välja platsen för säkerhets kopierings platsen. På båda platserna behåller Azure ständigt tre felfria repliker av dina data.

## <a name="database-availability"></a>Tillgänglighet för databaser
Azure säkerställer att en databas är tillgänglig via Internet via en Internet-Gateway med varaktig databas tillgänglighet. Övervakningen bedömer hälso tillståndet och tillståndet för de aktiva databaserna med tidsintervaller på fem minuter.

## <a name="storage-availability"></a>Lagringstillgänglighet
Azure levererar lagring via en mycket skalbar och varaktig lagrings tjänst som tillhandahåller anslutnings slut punkter. Det innebär att ett program kan komma åt lagrings tjänsten direkt. Lagrings tjänsten bearbetar inkommande lagrings begär Anden effektivt, med transaktions integritet.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns i:

- [Azure-anläggningar, lokal och fysisk säkerhet](physical-security.md)
- [Komponenter och gränser för Azure information system](infrastructure-components.md)
- [Azure nätverks arkitektur](infrastructure-network.md)
- [Azures produktions nätverk](production-network.md)
- [Azure SQL Database säkerhetsfunktioner](infrastructure-sql.md)
- [Åtgärder och hantering av Azure-produktion](infrastructure-operations.md)
- [Övervakning av Azure-infrastruktur](infrastructure-monitoring.md)
- [Integritet för Azure-infrastruktur](infrastructure-integrity.md)
- [Data skydd för Azure-kunder](protection-customer-data.md)
