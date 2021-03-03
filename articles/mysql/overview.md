---
title: Översikt – Azure Database for MySQL
description: Lär dig mer om tjänsten Azure Database for MySQL, en Relations databas tjänst i Microsoft-molnet baserat på MySQL Community Edition.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: 20b54afdfbb5641def921ffc17b3c38f6650acab
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736227"
---
# <a name="what-is-azure-database-for-mysql"></a>Vad är Azure Database for MySQL?

Azure Database for MySQL är en Relations databas tjänst i Microsoft-molnet baserat på [MySQL Community Edition](https://www.mysql.com/products/community/) (tillgänglig i GPLv2-licensen) databas motor, version 5,6, 5,7 och 8,0. Azure Database for MySQL ger:

- Inbyggd hög tillgänglighet.
- Data skydd med automatiska säkerhets kopieringar och tidpunkts återställning i upp till 35 dagar.
- Automatiserat underhåll av underliggande maskin vara, operativ system och databas motor för att hålla tjänsten säker och uppdaterad.
- Förutsägbar prestanda med inkluderande Betala per användning-prissättning.
- Elastisk skalning inom några sekunder.
- Kontroller för kostnads optimering med möjlighet att stoppa/starta servern. 
- Säkerhet på företags nivå och branschledande efterlevnad för att skydda känsliga data i vila och i rörelse.
- Övervakning och automatisering för att förenkla hanteringen och övervakningen av storskaliga distributioner.
- Branschledande support upplevelser.

Dessa funktioner kräver nästan ingen administration och de tillhandahålls utan extra kostnad. De gör att du kan fokusera på snabb apputveckling och att accelerera din tid till marknaden, istället för att ägna värdefull tid och resurser åt att hantera virtuella datorer och infrastruktur. Du kan dessutom fortsätta att utveckla programmet med verktygen med öppen källkod och på valfri plattform, och leverera med den hastighet och effektivitet som verksamheten kräver utan att du behöver lära dig nya färdigheter.

:::image type="content" source="media/overview/1-azure-db-for-mysql-conceptual-diagram.png" alt-text="Azure Database for MySQL konceptuellt diagram":::

## <a name="deployment-models"></a>Distributionsmodeller

Azure Database for MySQL som drivs av MySQL Community Edition finns i två distributions lägen:
- Enskild server 
- Flexibel server (förhandsversion)
  
### <a name="azure-database-for-mysql---single-server"></a>Azure Database for MySQL-enskild server

Azure Database for MySQL enskild server är en fullständigt hanterad databas tjänst med minimala krav för anpassning av databasen. Plattformen för enskild server är utformad för att hantera de flesta av funktionerna för databas hantering, till exempel korrigering, säkerhets kopiering, hög tillgänglighet, säkerhet med minimal användar konfiguration och kontroll. Arkitekturen är optimerad för inbyggd hög tillgänglighet med 99,99% tillgänglighet för enskild tillgänglighets zon. Den stöder community-versionen av MySQL 5,6, 5,7 och 8,0. Tjänsten är allmänt tillgänglig idag i många olika Azure- [regioner](https://azure.microsoft.com/global-infrastructure/services/).

Distributionsalternativet för enkel server har tre prisnivåer: Basic, Generell användning och Minnesoptimerad. Varje nivå erbjuder olika resursfunktioner som har stöd för arbetsbelastningar för databaser. Du kan skapa din första app i en liten databas för några kronor i månaden och sedan justera skalan för att bemöta lösningens behov. Dynamisk skalbarhet gör att databasen reagerar transparent på resurskrav som ändras snabbt. Du betalar bara för de resurser du behöver och endast när du behöver dem. Mer information finns i [Prisnivåer](concepts-pricing-tiers.md).

Enkla servrar lämpar sig bäst för inbyggda Cloud-program som är utformade för att hantera automatiserad uppdatering utan att det krävs någon detaljerad kontroll över uppdaterings schemats och anpassade MySQL-konfigurationsinställningar. 

Detaljerad översikt över distributions läget för en enskild server finns i [Översikt över en enskild server](single-server-overview.md).

### <a name="azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL-flexibel Server (för hands version)

Azure Database for MySQL flexibel Server är en fullständigt hanterad databas tjänst som är utformad för att ge mer detaljerad kontroll och flexibilitet för funktioner i databas hantering och konfigurations inställningar. I allmänhet tillhandahåller tjänsten mer flexibilitet och anpassningar baserat på användar kraven. Den flexibla Server arkitekturen gör det möjligt för användare att välja hög tillgänglighet i en zon för enskild tillgänglighet och över flera tillgänglighets zoner. Flexibla servrar ger bättre kostnads optimerings kontroller med möjligheten att stoppa/starta Server-och data bearbetnings nivåer, idealiska för arbets belastningar som inte behöver fullständig beräknings kapacitet kontinuerligt. Tjänsten stöder community-versionen av MySQL 5,7 och 8,0. Tjänsten är för närvarande en offentlig för hands version som är tillgänglig idag i många olika [Azure-regioner](https://azure.microsoft.com/global-infrastructure/services/).

Flexibla servrar lämpar sig bäst för 
- Program utveckling som kräver bättre kontroll och anpassningar.
- Zon redundant hög tillgänglighet
- Hanterade underhålls fönster

Detaljerad översikt över flexibelt Server distributions läge finns i [Översikt över flexibla](flexible-server/overview.md)servrar.

## <a name="contacts"></a>Kontakter
Om du har frågor eller förslag som du kan behöva för att arbeta med Azure Database for MySQL skickar du ett e-postmeddelande till Azure Database for MySQL-teamet ([ @Ask Azure dB för MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Den här e-postadressen är inte ett alias för teknisk support.

Tänk dessutom på följande kontaktpunkter efter behov:

- Kontakta Azure Support genom att [skicka in ett supportärende från Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Om du vill åtgärda ett problem med ditt konto, skickar du in ett [supportärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) i Azure-portalen.
- Om du vill ge feedback eller begära nya funktioner, skapar du ett inlägg via [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Nästa steg

Läs mer om de två distributions lägena för Azure Database for MySQL och välj rätt alternativ baserat på dina behov.

- [Enskild server](single-server/index.yml)
- [Flexibel Server](flexible-server/index.yml)
- [Välj rätt MySQL-distributions alternativ för din arbets belastning](select-right-deployment-type.md)
