---
title: Välj rätt PostgreSQL Server-alternativ i Azure
description: Innehåller rikt linjer för att välja rätt PostgreSQL-Server alternativ för dina distributioner.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: db80ae66761e49ce90f25a22f0e74cf8a1300796
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98797234"
---
# <a name="choose-the-right-postgresql-server-option-in-azure"></a>Välj rätt PostgreSQL Server-alternativ i Azure

Med Azure kan dina PostgreSQL Server-arbetsbelastningar köras i en värdbaserad virtuell dator infrastruktur som en tjänst (IaaS) eller som en tjänst som är värd plattform som en tjänst (PaaS). PaaS har flera distributions alternativ, var och en med flera tjänst nivåer. När du väljer mellan IaaS och PaaS måste du bestämma om du vill hantera din databas, tillämpa korrigeringar och göra säkerhets kopior, eller om du vill delegera dessa åtgärder till Azure.

När du fattar ditt beslut bör du överväga följande tre alternativ i PaaS eller köra på virtuella Azure-datorer (IaaS)
- [Azure Database för PostgreSQL-enskild server](./overview-single-server.md)
- [Azure Database för PostgreSQL-flexibel Server](./flexible-server/overview.md)
- [Azure Database for PostgreSQL-storskalig (citus)](hyperscale/index.yml)

Alternativet **postgresql på virtuella Azure-datorer** hamnar i kategorin IaaS. Med den här tjänsten kan du köra PostgreSQL-server i en helt hanterad virtuell dator på Azures moln plattform. Alla nya versioner och utgåvor av PostgreSQL kan installeras på en IaaS virtuell dator. PostgreSQL på virtuella Azure-datorer har en kontroll över databas motorn i den viktigaste skillnaden från Azure Database for PostgreSQL. Den här kontrollen kommer dock att ansvara för att hantera de virtuella datorerna och många databas administrations uppgifter (DBA). Dessa uppgifter omfattar att underhålla och korrigera databas servrar, databas återställning och design med hög tillgänglighet.

De huvudsakliga skillnaderna mellan dessa alternativ visas i följande tabell:

| **Attribut** | **Postgres på virtuella Azure-datorer** | **PostgreSQL som PaaS** |
| ----- | ----- | ----- |
| <B> Tillgänglighets-SLA |– 99,99% med tillgänglighets uppsättningar <br> – 99,95% med enskilda virtuella datorer | – En server – 99,99% <br> – Flexibel Server – inte tillgänglig under för hands versionen <br> -Scale (citus)-99,95% (när hög tillgänglighet har Aktiver ATS)|
| <B> Operativ system och PostgreSQL korrigering | – Kund Managed | -Enskild server – automatisk <br> – Flexibel Server – automatiskt med alternativ för Kundhanterade kunder <br> -Scale (citus) – automatisk |
| <B> Hög tillgänglighet | – Kunders arkitekt, implementera, testa och underhålla hög tillgänglighet. Funktioner kan vara klustring, replikering osv. | – Enskild server: inbyggd <br> – Flexibel Server: inbyggd <br> -Scale (citus): byggd med standby |
| <B> Zon redundans | – Virtuella Azure-datorer kan konfigureras så att de körs i olika tillgänglighets zoner. För en lokal lösning måste kunderna skapa, hantera och underhålla ett eget sekundärt Data Center. | – Enskild server: Nej <br> – Flexibel Server: Ja <br> -Scale (citus): Nej |
| <B> Hybrid scenario | – Kund Managed |– Enskild server: Read-Replica <br> – Flexibel Server: inte tillgänglig under för hands versionen <br> -Scale (citus): Nej |
| <B> Säkerhets kopiering och återställning | – Kund Managed | – Enskild server: inbyggd med användar konfiguration för lokal och geo <br> – Flexibel Server: inbyggd med användar konfiguration i zoner – redundant lagring <br> -Scale (citus): inbyggd |
| <B> Övervaka databas åtgärder | – Kund Managed | – Enskild server, flexibel Server och storskalig (citus): alla erbjudanden kunder kan ange aviseringar för databas åtgärden och vidta åtgärder för att uppnå tröskelvärden. |
| <B> Avancerat skydd | – Kunderna måste bygga det här skyddet för sig själva. |– Enskild server: Ja <br> – Flexibel Server: inte tillgänglig under för hands versionen <br> -Scale (citus): Nej |
| <B> Haveri beredskap | – Kund Managed | – Enskild server: Geo-redundant säkerhets kopiering och geo Read-Replica <br> – Flexibel Server: inte tillgänglig under för hands versionen <br> -Scale (citus): Nej |
| <B> Intelligent prestanda | – Kund Managed | – Enskild server: Ja <br> – Flexibel Server: inte tillgänglig under för hands versionen <br> -Scale (citus): Nej |

## <a name="total-cost-of-ownership-tco"></a>Total ägande kostnad (TCO)

TCO är ofta den främsta överväganden som avgör den bästa lösningen för dina databaser. Detta gäller oavsett om du är en start med lite kontant eller ett team i ett etablerat företag som arbetar under tätt budget begränsningar. I det här avsnittet beskrivs grundläggande information om fakturering och licensiering i Azure eftersom de gäller för Azure Database for PostgreSQL och PostgreSQL på virtuella Azure-datorer.

## <a name="billing"></a>Fakturering

Azure Database for PostgreSQL är för närvarande tillgängligt som en tjänst på flera nivåer med olika priser för resurser. Alla resurser debiteras per timme med ett fast pris. För den senaste informationen om tjänster som stöds för närvarande, beräknings storlek och lagrings belopp, se [sidan prissättning](https://azure.microsoft.com/pricing/details/postgresql/server/) kan du dynamiskt justera tjänst nivåer och beräknings storlekar för att matcha ditt programs varierande data flödes behov. Du debiteras för utgående Internet trafik med regelbundna [data överförings kostnader](https://azure.microsoft.com/pricing/details/data-transfers/).

Med Azure Database for PostgreSQL konfigurerar Microsoft automatiskt, patchar och uppgraderar databas program varan. Dessa automatiserade åtgärder minskar dina administrations kostnader. Azure Database for PostgreSQL har också funktioner för [Automatisk säkerhets kopierings länk]() . Dessa funktioner hjälper dig att uppnå avsevärda kostnads besparingar, särskilt när du har ett stort antal databaser. Med PostgreSQL på virtuella Azure-datorer kan du däremot välja och köra valfri PostgreSQL-version. Du måste dock betala för den etablerade virtuella datorn, lagrings kostnaden som är kopplad till data, säkerhets kopiering, övervakning av data och logg lagring och kostnaderna för den aktuella PostgreSQL-licens typen (om sådana finns).

Azure Database for PostgreSQL ger en inbyggd hög tillgänglighet för alla typer av avbrott på nodnivå samtidigt som service avtalet för service avtalet för service avtal fortfarande 99,99 upprätthålls. För databas hög tillgänglighet i virtuella datorer kan du dock använda alternativen för hög tillgänglighet, t. ex. [streaming-replikering](https://www.postgresql.org/docs/12/warm-standby.html#STREAMING-REPLICATION) som är tillgängliga i en PostgreSQL-databas. Att använda ett alternativ för hög tillgänglighet som stöds ger inte ytterligare ett service avtal. Men det gör att du kan uppnå mer än 99,99% tillgänglighet till databaser med extra kostnad och administrativa kostnader.

Mer information om priser finns i följande artiklar:
- [Azure Database for PostgreSQL priser](https://azure.microsoft.com/pricing/details/postgresql/server/)
- [Prissättning för virtuell dator](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [ Priskalkylator för Azure](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>Administration

För många företag är beslutet att gå över till en moln tjänst lika mycket om att avlasta komplexiteten för administration eftersom det kostar mer.

Med IaaS, Microsoft:

- Administrerar den underliggande infrastrukturen.
- Tillhandahåller automatiserad uppdatering för underliggande maskin vara och operativ system

Med PaaS, Microsoft:

- Administrerar den underliggande infrastrukturen.
- Tillhandahåller automatiserad uppdatering för underliggande maskin vara, operativ system och databas motor.
- Hanterar databasens hög tillgänglighet.
- Säkerhetskopierar automatiskt och replikerar alla data för att tillhandahålla haveri beredskap.
- Krypterar data i vila och i rörelse som standard.
- Övervakar servern och tillhandahåller funktioner för att ställa frågor till prestanda insikter och prestanda rekommendationer.

Med Azure Database for PostgreSQL kan du fortsätta att administrera din databas. Men du behöver inte längre hantera databas motorn, operativ systemet eller maskin varan. Exempel på objekt som du kan fortsätta att administrera är:

- Databaser
- Logga in
- Index justering
- Frågejustering
- Granskning
- Säkerhet

Dessutom kräver en minimal konfiguration eller administration att konfigurera hög tillgänglighet till ett annat data Center.

- Med PostgreSQL på virtuella Azure-datorer har du fullständig kontroll över operativ systemet och konfigurationen av PostgreSQL-serverinstansen. Med en virtuell dator bestämmer du när du vill uppdatera eller uppgradera operativ systemet och databas programmet och vilka korrigeringar som ska tillämpas. Du bestämmer också när du ska installera ytterligare program vara, till exempel ett antivirus program. Vissa automatiserade funktioner är till för att avsevärt förenkla korrigering, säkerhets kopiering och hög tillgänglighet. Du kan kontrol lera storleken på den virtuella datorn, antalet diskar och deras lagrings konfiguration. Mer information finns i [virtuella datorer och moln tjänst storlekar för Azure](../virtual-machines/sizes.md).

## <a name="time-to-move-to-azure-postgresql-service-paas"></a>Tid för att flytta till Azure PostgreSQL service (PaaS)

- Azure Database for PostgreSQL är rätt lösning för molnbaserade program när produktiviteten för utvecklare och snabb tid till marknaden för nya lösningar är viktiga. Med programmerings funktioner som fungerar som DBA är tjänsten lämplig för moln arkitekter och utvecklare, eftersom det minskar behovet av att hantera det underliggande operativ systemet och databasen.

- När du vill undvika tid och kostnader för att förvärva ny lokal maskin vara är PostgreSQL på virtuella Azure-datorer den rätta lösningen för program som kräver en detaljerad kontroll och anpassning av PostgreSQL-motorn som inte stöds av tjänsten eller kräver åtkomst till det underliggande operativ systemet.

## <a name="next-steps"></a>Nästa steg

- Se Azure Database for [postgresql-priser](https://azure.microsoft.com/pricing/details/postgresql/server/).
- Kom igång genom att skapa din första server.