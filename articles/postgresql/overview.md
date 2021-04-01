---
title: Vad är Azure Database for PostgreSQL
description: Innehåller en översikt över Azure Database for PostgreSQL Relations databas tjänst i samband med flexibel Server.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: c3ea7930f41fe89538a817da032e993e534db9cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92491333"
---
# <a name="what-is-azure-database-for-postgresql"></a>Vad är Azure Database for PostgreSQL?

Azure Database for PostgreSQL är en Relations databas tjänst i Microsoft-molnet baserat på [postgresql community-versionen](https://www.postgresql.org/) (finns i GPLv2-licensen). Azure Database for PostgreSQL ger:

- Inbyggd hög tillgänglighet.
- Data skydd med automatiska säkerhets kopieringar och tidpunkts återställning i upp till 35 dagar.
- Automatiserat underhåll av underliggande maskin vara, operativ system och databas motor för att hålla tjänsten säker och uppdaterad.
- Förutsägbar prestanda med inkluderande Betala per användning-prissättning.
- Elastisk skalning inom några sekunder.
- Säkerhet på företags nivå och branschledande efterlevnad för att skydda känsliga data i vila och i rörelse.
- Övervakning och automatisering för att förenkla hanteringen och övervakningen av storskaliga distributioner.
- Branschledande support upplevelser.

 :::image type="content" source="./media/overview/overview-what-is-azure-postgres.png" alt-text="Azure Database for PostgreSQL":::

Dessa funktioner kräver nästan ingen administration och tillhandahålls utan extra kostnad. De gör att du kan fokusera på snabb program utveckling och påskynda tiden till marknaden snarare än att allokera dyrbar tid och resurser för att hantera virtuella datorer och infrastruktur. Du kan dessutom fortsätta att utveckla programmet med verktygen med öppen källkod och på valfri plattform, och leverera med den hastighet och effektivitet som verksamheten kräver utan att du behöver lära dig nya färdigheter.

## <a name="deployment-models"></a>Distributionsmodeller

Azure Database for PostgreSQL som drivs av PostgreSQL Community Edition finns i tre distributions lägen:

- Enskild server
- Flexibel server (förhandsversion)
- Hyperskala (Citus)

### <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL – enskild server

Azure Database for PostgreSQL enskild server är en fullständigt hanterad databas tjänst med minimala krav för anpassning av databasen. Plattformen för enskild server är utformad för att hantera de flesta av funktionerna för databas hantering, till exempel korrigering, säkerhets kopiering, hög tillgänglighet, säkerhet med minimal användar konfiguration och kontroll. Arkitekturen är optimerad för inbyggd hög tillgänglighet med 99,99% tillgänglighet för enskild tillgänglighets zon. Den stöder community-versionen av PostgreSQL 9,5, 9, 6, 10 och 11. Tjänsten är allmänt tillgänglig idag i många olika Azure- [regioner](https://azure.microsoft.com/global-infrastructure/services/).

Distributionsalternativet för enkel server har tre prisnivåer: Basic, Generell användning och Minnesoptimerad. Varje nivå erbjuder olika resursfunktioner som har stöd för arbetsbelastningar för databaser. Du kan skapa din första app i en liten databas för några kronor i månaden och sedan justera skalan för att bemöta lösningens behov. Dynamisk skalbarhet gör att databasen reagerar transparent på resurskrav som ändras snabbt. Du betalar bara för de resurser du behöver och endast när du behöver dem. Mer information finns i [Prisnivåer](./concepts-pricing-tiers.md).

Enkla servrar lämpar sig bäst för molnbaserade program som är utformade för att hantera automatiserad uppdatering utan att behöva detaljerad kontroll över uppdaterings schema och anpassade konfigurations inställningar för PostgreSQL.

Detaljerad översikt över distributions läget för en enskild server finns i [Översikt över en enskild server](./overview-single-server.md).

### <a name="azure-database-for-postgresql---flexible-server-preview"></a>Azure Database for PostgreSQL – flexibel server (förhandsversion)

Azure Database for PostgreSQL flexibel Server är en fullständigt hanterad databas tjänst som är utformad för att ge mer detaljerad kontroll och flexibilitet för funktioner i databas hantering och konfigurations inställningar. I allmänhet tillhandahåller tjänsten mer flexibilitet och anpassningar baserat på användar kraven. Den flexibla Server arkitekturen gör det möjligt för användare att välja hög tillgänglighet i en zon för enskild tillgänglighet och över flera tillgänglighets zoner. Flexibel Server ger bättre kostnads optimerings kontroller med möjligheten att stoppa/starta Server-och data bearbetnings nivåer, perfekt för arbets belastningar som inte behöver fullständig beräknings kapacitet kontinuerligt. Tjänsten stöder för närvarande community-versionen av PostgreSQL 11 och 12 med planer på att lägga till nyare versioner snart. Tjänsten är för närvarande en offentlig för hands version som är tillgänglig idag i många olika Azure-regioner.

Flexibla servrar lämpar sig bäst för

- Program utveckling som kräver bättre kontroll och anpassningar.
- Kontroller för kostnads optimering med möjlighet att stoppa/starta servern.
- Zon redundant hög tillgänglighet
- Hanterade underhålls fönster
  
En detaljerad översikt över läget för flexibel Server distribution finns i [Översikt över flexibel Server](./flexible-server/overview.md).

### <a name="azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL – storskalig (citus)

Alternativet Hyperskala (Citus) skalar frågor horisontellt över flera datorer med hjälp av horisontell partitionering. Dess frågemotor parallelliserar inkommande SQL-frågor över dessa servrar för snabbare svar på stora datamängder. Det hanterar program som kräver större skalning och prestanda, vanligtvis arbetsbelastningar som närmar sig – eller som redan är större än – 100 GB data.

Distributionsalternativet Hyperskala (Citus) ger:

- Horisontell skalning mellan flera datorer med hjälp av horisontell partitionering
- Frågeparallellisering över dessa servrar för snabbare svar på stora datamängder
- Utmärkt stöd för tillämpningar med flera klienter, driftanalys i realtid och högt genomflöde transaktionsarbetsbelastningar
  
Program som skapats för PostgreSQL kan köra distribuerade frågor på citus (storskalig) med standard [anslutnings bibliotek](./concepts-connection-libraries.md) och minimala ändringar.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om de tre distributions lägena för Azure Database for PostgreSQL och välj rätt alternativ baserat på dina behov.

- [Enskild server](./overview-single-server.md)
- [Flexibel Server](./flexible-server/overview.md)
- Hyperskala (Citus)