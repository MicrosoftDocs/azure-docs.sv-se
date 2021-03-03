---
title: Välja rätt distributions typ – Azure Database for MySQL
description: I den här artikeln beskrivs vilka faktorer du bör tänka på innan du distribuerar Azure Database for MySQL som antingen infrastruktur som en tjänst (IaaS) eller Platform as a Service (PaaS).
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: 125431e6630ccfdd9e0e5d6b2a4ec5fa9b9e58fd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736193"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Välj rätt MySQL Server-alternativ i Azure

Med Azure kan dina MySQL server-arbetsbelastningar köras i en värdbaserad virtuell dator infrastruktur som en tjänst (IaaS) eller som en tjänst som värd plattform som en tjänst (PaaS). PaaS har flera distributions alternativ och det finns tjänst nivåer inom varje distributions alternativ. När du väljer mellan IaaS och PaaS måste du bestämma om du vill hantera din databas, tillämpa korrigeringar och göra säkerhets kopior, eller om du vill delegera dessa åtgärder till Azure.

När du fattar ditt beslut bör du tänka på följande två alternativ:

- **Azure Database for MySQL**. Det här alternativet är en fullständigt hanterad MySQL-databasmotor baserat på den stabila versionen av MySQL Community Edition. Den här Relations databas som en tjänst (DBaaS), som finns på Azure-molnets plattform, hamnar i bransch kategorin för PaaS.

  Med en hanterad instans av MySQL på Azure kan du använda inbyggda funktioner som automatiserad uppdatering, hög tillgänglighet, automatisk säkerhets kopiering, elastisk skalning, säkerhet för företags klass, efterlevnad och styrning, övervakning och avisering som annars kräver omfattande konfiguration när MySQL-servern är antingen lokalt eller i en virtuell Azure-dator. När du använder MySQL som en tjänst betalar du per användning med alternativ för att skala upp eller ut för bättre kontroll utan avbrott.
  
  [Azure Database for MySQL](overview.md)som drivs av MySQL Community Edition finns tillgänglig i två distributions lägen:

  - En [enda server](single-server-overview.md) är en fullständigt hanterad databas tjänst med minimala krav på anpassningar av databasen. Plattformen för enskild server är utformad för att hantera de flesta av funktionerna för databas hantering, till exempel korrigering, säkerhets kopiering, hög tillgänglighet, säkerhet med minimal användar konfiguration och kontroll. Arkitekturen är optimerad för att ge 99,99% tillgänglighet för enskild tillgänglighets zon. Enkla servrar lämpar sig bäst för inbyggda Cloud-program som är utformade för att hantera automatiserad uppdatering utan att det krävs någon detaljerad kontroll över uppdaterings schemats och anpassade MySQL-konfigurationsinställningar.

  - [Flexibel Server (förhands granskning)](flexible-server/overview.md) är en fullständigt hanterad databas tjänst som är utformad för att ge mer detaljerad kontroll och flexibilitet för funktioner i databas hantering och konfigurations inställningar. I allmänhet tillhandahåller tjänsten mer flexibilitet och anpassningar av Server konfigurationen jämfört med distributionen av en enskild server utifrån användar kraven. Den flexibla Server arkitekturen gör det möjligt för användare att välja hög tillgänglighet i en enda tillgänglighets zon och över flera tillgänglighets zoner. Flexibla servrar ger också bättre kostnads optimerings kontroller med möjligheten att starta/stoppa din server och de enheter som går att använda och som är idealiska för arbets belastningar som inte behöver fullständig beräknings kapacitet kontinuerligt.

  Flexibla servrar passar bäst för:

  - Program utveckling kräver bättre kontroll och anpassningar av MySQL-motorn.
  - Zon redundant hög tillgänglighet
  - Hanterade underhålls fönster

- **MySQL på virtuella Azure-datorer**. Det här alternativet är inställt på IaaS för bransch kategorin. Med den här tjänsten kan du köra MySQL server i en hanterad virtuell dator på Azures moln plattform. Alla nya versioner och utgåvor av MySQL kan installeras på den virtuella datorn.

## <a name="comparing-the-mysql-deployment-options-in-azure"></a>Jämför distributions alternativen för MySQL i Azure

De huvudsakliga skillnaderna mellan dessa alternativ visas i följande tabell:

| Attribut          | Azure Database for MySQL<br/>Enskild server |Azure Database for MySQL<br/>Flexibel Server  |MySQL på virtuella Azure-datorer                      |
|:-------------------|:-------------------------------------------|:---------------------------------------------|:---------------------------------------|
| MySQL-versions stöd | 5,6, 5,7 & 8,0| 5,7 & 8,0 | Vilken version som helst|
| Beräknings skalning | Stöds (skalning från och till Basic-nivån stöds inte)| Stöds | Stöds|
| Lagrings storlek | 5 GiB till 16 TiB| 5 GiB till 16 TiB | 32 GiB till 32 767 GiB|
| Skalning av onlinelagring | Stöds| Stöds| Stöds inte|
| Automatisk skalning av lagring | Stöds| Stöds inte i för hands versionen| Stöds inte|
| Ytterligare IOPs-skalning | Stöds inte| Stöds| Stöds inte|
| Nätverks anslutning | -Offentliga slut punkter med Server brand väggen.<br/> – Privat åtkomst med stöd för privata länkar.|-Offentliga slut punkter med Server brand väggen.<br/> – Privat åtkomst med Virtual Network-integrering.| -Offentliga slut punkter med Server brand väggen.<br/> – Privat åtkomst med stöd för privata länkar.|
| Service nivå avtal (SLA) | service nivå avtal på 99,99% tillgänglighet |Inget service avtal för för hands version| 99,99% med Tillgänglighetszoner|
| Uppdatering av operativ system| Automatiskt  | Automatisk med kontroll för anpassat underhålls fönster | Hanteras av slutanvändare |
| MySQL-korrigering     | Automatiskt  | Automatisk med kontroll för anpassat underhålls fönster | Hanteras av slutanvändare |
| Hög tillgänglighet | Inbyggt HA i en zon för enskild tillgänglighet| Inbyggda HA i och över tillgänglighets zoner | Anpassad hanterad med kluster, replikering osv.|
| Zonredundans | Stöds inte | Stöds | Stöds|
| Zon placering | Stöds inte | Stöds | Stöds|
| Hybrid scenarier | Stöds med [datareplikering](./concepts-data-in-replication.md)| Inte tillgänglig i för hands version | Hanteras av slutanvändare |
| Skrivskyddade repliker | Stöds (upp till 5 repliker)| Stöds (upp till 10 repliker)| Hanteras av slutanvändare |
| Backup | Automatiserad med 7-35 dagars kvarhållning | Automatiserad med 1-35 dagars kvarhållning | Hanteras av slutanvändare |
| Övervaka databas åtgärder | Stöds | Stöds | Hanteras av slutanvändare |
| Haveriberedskap | Stöds med Geo-redundant lagring av säkerhets kopior och Läs repliker i flera regioner | Stöds inte i för hands versionen| Anpassad hantering med teknik för replikering |
| Information om frågeprestanda | Stöds | Inte tillgänglig i för hands version| Hanteras av slutanvändare |
| Prissättning för reserverad instans | Stöds | Inte tillgänglig i för hands version | Stöds |
| Azure AD-autentisering | Stöds | Inte tillgänglig i för hands version | Stöds inte|
| Data kryptering i vila | Stöds med Kundhanterade nycklar | Stöds med tjänst hanterade nycklar | Stöds inte|
| SSL/TLS | Aktive rad som standard med stöd för TLS v 1.2, 1,1 och 1,0 | Framtvingad med TLS v 1.2 | Stöds med TLS v 1.2, 1,1 och 1,0 |
| Flottans hantering | Stöds med Azure CLI, PowerShell, REST och Azure Resource Manager | Stöds med Azure CLI, PowerShell, REST och Azure Resource Manager  | Stöds för virtuella datorer med Azure CLI, PowerShell, REST och Azure Resource Manager |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Affärs motivation för att välja PaaS eller IaaS

Det finns flera faktorer som kan påverka ditt beslut att välja PaaS eller IaaS för att vara värd för MySQL-databaserna.

### <a name="cost"></a>Cost

Kostnads minskning är ofta den främsta överväganden som avgör den bästa lösningen för dina databaser. Detta gäller oavsett om du är en start med lite kontant eller ett team i ett etablerat företag som arbetar under tätt budget begränsningar. I det här avsnittet beskrivs grundläggande om fakturering och licensiering i Azure eftersom de gäller för Azure Database for MySQL och MySQL på virtuella Azure-datorer.

#### <a name="billing"></a>Fakturering

Azure Database for MySQL är för närvarande tillgängligt som en tjänst på flera nivåer med olika priser för resurser. Alla resurser debiteras per timme med ett fast pris. Den senaste informationen om de tjänst nivåer, beräknings storlekar och lagrings belopp som stöds för närvarande finns på [sidan med priser](https://azure.microsoft.com/pricing/details/mysql/). Du kan justera tjänst nivåerna dynamiskt och beräknings storlekarna så att de överensstämmer med programmets varierande data flödes behov. Du debiteras för utgående Internet trafik med regelbundna [data överförings kostnader](https://azure.microsoft.com/pricing/details/data-transfers/).

Med Azure Database for MySQL konfigurerar Microsoft automatiskt, patchar och uppgraderar databas program varan. Dessa automatiserade åtgärder minskar dina administrations kostnader. Azure Database for MySQL har också funktioner för [Automatisk säkerhets kopiering](./concepts-backup.md) . Dessa funktioner hjälper dig att uppnå avsevärda kostnads besparingar, särskilt när du har ett stort antal databaser. Däremot kan du med MySQL på virtuella Azure-datorer välja och köra en MySQL-version. Oavsett vilken MySQL-version du använder betalar du för den etablerade virtuella datorn, lagrings kostnaden som är kopplad till data, säkerhets kopiering, övervakning av data och logg lagring och kostnaderna för den speciella MySQL-licens typen som används (om det finns någon).

Azure Database for MySQL ger en inbyggd hög tillgänglighet för alla typer av avbrott på nodnivå samtidigt som service avtalet för service avtalet för service avtal fortfarande 99,99 upprätthålls. För databas hög tillgänglighet i virtuella datorer använder du dock alternativen för hög tillgänglighet som [MySQL-replikering](https://dev.mysql.com/doc/refman/8.0/en/replication.html) som är tillgängliga i en MySQL-databas. Att använda ett alternativ för hög tillgänglighet som stöds ger inte ytterligare ett service avtal. Men det gör att du kan uppnå mer än 99,99% tillgänglighet till databaser med extra kostnad och administrativa kostnader.

Mer information om priser finns i följande artiklar:

- [Azure Database for MySQL priser](https://azure.microsoft.com/pricing/details/mysql/)
- [Prissättning för virtuell dator](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [ Priskalkylator för Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administration

För många företag är beslutet att gå över till en moln tjänst lika mycket om att avlasta komplexiteten för administration eftersom det kostar mer.

Med IaaS, Microsoft:

- Administrerar den underliggande infrastrukturen.
- Tillhandahåller automatiserad uppdatering för underliggande maskin vara och operativ system.
  
Med PaaS, Microsoft:

- Administrerar den underliggande infrastrukturen.
- Tillhandahåller automatiserad uppdatering för underliggande maskin vara, operativ system och databas motor.
- Hanterar databasens hög tillgänglighet.
- Säkerhetskopierar automatiskt och replikerar alla data för att tillhandahålla haveri beredskap.
- Krypterar data i vila och i rörelse som standard.
- Övervakar servern och tillhandahåller funktioner för frågor om prestanda insikter och prestanda rekommendationer

I följande lista beskrivs administrativa överväganden för varje alternativ:

- Med Azure Database for MySQL kan du fortsätta att administrera din databas. Men du behöver inte längre hantera databas motorn, operativ systemet eller maskin varan. Exempel på objekt som du kan fortsätta att administrera är:

  - Databaser
  - Logga in
  - Index justering
  - Frågejustering
  - Granskning
  - Säkerhet

  Dessutom kräver en minimal konfiguration eller administration att konfigurera hög tillgänglighet till ett annat data Center.

- Med MySQL på virtuella Azure-datorer har du fullständig kontroll över operativ systemet och konfiguration av MySQL-serverinstansen. Med en virtuell dator bestämmer du när du vill uppdatera eller uppgradera operativ systemet och databas programmet och vilka korrigeringar som ska tillämpas. Du bestämmer också när du ska installera ytterligare program vara, till exempel ett antivirus program. Vissa automatiserade funktioner är till för att avsevärt förenkla korrigering, säkerhets kopiering och hög tillgänglighet. Du kan kontrol lera storleken på den virtuella datorn, antalet diskar och deras lagrings konfiguration. Mer information finns i [virtuella datorer och moln tjänst storlekar för Azure](../virtual-machines/sizes.md).

### <a name="time-to-move-to-azure"></a>Tid att flytta till Azure

- Azure Database for MySQL är rätt lösning för molnbaserade program när produktiviteten för utvecklare och snabb tid till marknaden för nya lösningar är viktiga. Med programmerings funktioner som fungerar som DBA är tjänsten lämplig för moln arkitekter och utvecklare, eftersom det minskar behovet av att hantera det underliggande operativ systemet och databasen.

- När du vill undvika tid och pengar på att förvärva ny lokal maskin vara är MySQL på virtuella Azure-datorer rätt lösning för program som kräver en detaljerad kontroll och anpassning av MySQL-motorn som inte stöds av tjänsten eller kräver åtkomst till det underliggande operativ systemet. Den här lösningen är också lämplig för att migrera befintliga lokala program och databaser till Azure intakt, i de fall där Azure Database for MySQL är en dålig anpassning.

Eftersom du inte behöver ändra presentations-, program-och data lager, sparar du tid och budget för att bygga om din befintliga lösning. I stället kan du fokusera på att migrera alla dina lösningar till Azure och åtgärda vissa prestanda optimeringar som Azure-plattformen kan kräva.

## <a name="next-steps"></a>Nästa steg

- Se [Azure Database for MySQL prissättning](https://azure.microsoft.com/pricing/details/MySQL/).
- Kom igång genom [att skapa din första server](./quickstart-create-mysql-server-database-using-azure-portal.md).
