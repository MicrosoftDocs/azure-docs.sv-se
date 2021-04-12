---
title: Översikt över hanterings åtgärder
titleSuffix: Azure SQL Managed Instance
description: Läs mer om drift tid och bästa praxis för hantering av Azure SQL-hanterade instanser.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: overview
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, MashaMSFT
ms.date: 07/10/2020
ms.openlocfilehash: bd66c10bb1d6316bbe90e7ba4092d79c6a43a75d
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285302"
---
# <a name="overview-of-azure-sql-managed-instance-management-operations"></a>Översikt över hanteringsåtgärder för Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL-hanterad instans tillhandahåller hanterings åtgärder som du kan använda för att automatiskt distribuera nya hanterade instanser, uppdatera instans egenskaper och ta bort instanser när de inte längre behövs.

## <a name="what-are-management-operations"></a>Vad är hanterings åtgärder?

Alla hanteringsåtgärder kan kategoriseras på följande sätt:

- Instansdistribution (skapande av ny instans)
- Instans uppdatering (ändring av instans egenskaper, till exempel virtuella kärnor eller reserverad lagring)
- Borttagning av instans

För att stödja [distributioner i virtuella Azure-nätverk](../../virtual-network/virtual-network-for-azure-services.md) och tillhandahålla isolering och säkerhet för kunder förlitar sig SQL-hanterade instanser på [virtuella kluster](connectivity-architecture-overview.md#high-level-connectivity-architecture). Det virtuella klustret representerar en dedikerad uppsättning isolerade virtuella datorer som distribueras i kundens virtuella nätverk under nät. Varje hanterad instans som distribueras till ett tomt undernät resulterar i grunden av en ny version av virtuella kluster.

Efterföljande hanterings åtgärder på hanterade instanser kan påverka det underliggande virtuella klustret. Ändringar som påverkar det underliggande virtuella klustret kan påverka varaktigheten för hanterings åtgärder, eftersom distribution av ytterligare virtuella datorer levereras med en kostnad som du måste tänka på när du planerar nya distributioner eller uppdateringar av befintliga hanterade instanser.


## <a name="duration"></a>Varaktighet

Varaktigheten för åtgärder i det virtuella klustret kan variera, men vanligt vis har den längsta varaktigheten. 

Följande är värden som du normalt förväntar dig, baserat på befintliga telemetridata för tjänsten:

- **Skapa virtuellt kluster**: skapandet är ett synkront steg i instans hanterings åtgärder. <br/> **90% av åtgärderna har slutförts på 4 timmar**.
- **Storleks ändring av virtuellt kluster (expandering eller krympning)**: utökning är ett synkront steg, medan krympningen utförs asynkront (utan inverkan på instans hanterings åtgärdernas varaktighet). <br/>**90% av kluster utökningar slutförs på mindre än 2,5 timmar**.
- **Borttagning av virtuellt kluster**: borttagning är ett asynkront steg, men det kan också [initieras manuellt](virtual-cluster-delete.md) på ett tomt virtuellt kluster, i vilket fall det körs synkront. <br/>**90% av de virtuella kluster borttagningarna slutförs om 1,5 timmar**.

Dessutom kan hantering av instanser också innehålla en av åtgärderna på värdbaserade databaser, vilket leder till längre varaktigheter:

- **Bifoga databasfiler från Azure Storage**: ett synkront steg, till exempel skalnings beräkning (virtuella kärnor) eller lagring upp eller ned i generell användning tjänst nivå. <br/>**90% av dessa åtgärder har slutförts på 5 minuter**.
- **Always on-tillgänglighets grupps dirigering**: ett synkront steg, till exempel Compute (virtuella kärnor) eller lagrings skalning på affärskritisk tjänst nivå samt i ändra tjänst nivån från Generell användning till affärskritisk (eller vice versa). Den här åtgärdens varaktighet är proportionerlig till den totala databas storleken samt den aktuella databas aktiviteten (antal aktiva transaktioner). Databas aktivitet vid uppdatering av en instans kan medföra betydande varianser för den totala varaktigheten. <br/>**90% av dessa åtgärder körs vid 220 GB/timme eller högre**.

Följande tabeller sammanfattar åtgärder och typiska övergripande varaktigheter, baserat på åtgärdens kategori:

**Kategori: distribution**

|Åtgärd  |Tids krävande segment  |Uppskattad varaktighet  |
|---------|---------|---------|
|Första instansen i ett tomt undernät|Skapa virtuellt kluster|90% av åtgärderna har slutförts på 4 timmar.|
|Första instansen av en annan maskin varu generation i ett undernät som inte är tomt (till exempel första generation 5-instansen i ett undernät med generation 4 instanser)|Skapa virtuellt kluster<sup>1</sup>|90% av åtgärderna har slutförts på 4 timmar.|
|Efterföljande instans skapas i det icke-tomma under nätet (andra, tredje osv.)|Storleks ändring av virtuellt kluster|90% av åtgärderna har slutförts om 2,5 timmar.|
| | | 

<sup>1</sup> virtuellt kluster skapas per maskin varu generation.

**Kategori: uppdatera**

|Åtgärd  |Tids krävande segment  |Uppskattad varaktighet  |
|---------|---------|---------|
|Ändring av instans egenskap (administratörs lösen ord, Azure AD-inloggning, Azure Hybrid-förmån flagga)|Ej tillämpligt|Upp till 1 minut.|
|Skalning av instans lagring upp/ned (Generell användning tjänst nivå)|Inget tids krävande segment<sup>1</sup>|99% av åtgärderna har slutförts på 5 minuter.|
|Skalning av instans lagring upp/ned (Affärskritisk tjänst nivå)|-Storleks ändring av virtuellt kluster<br>-Always on-tillgänglighets grupps dirigering|90% av åtgärderna har slutförts i 2,5 timmar + tid för att dirigera alla databaser (220 GB/timme).|
|Virtuella kärnor (Instance Compute) skalar upp och ned (Generell användning)|-Storleks ändring av virtuellt kluster<br>-Bifoga databasfiler|90% av åtgärderna har slutförts om 2,5 timmar.|
|Virtuella kärnor (Instance Compute) skalar upp och ned (Affärskritisk)|-Storleks ändring av virtuellt kluster<br>-Always on-tillgänglighets grupps dirigering|90% av åtgärderna har slutförts i 2,5 timmar + tid för att dirigera alla databaser (220 GB/timme).|
|Instans tjänst nivå ändring (Generell användning till Affärskritisk och vice versa)|-Storleks ändring av virtuellt kluster<br>-Always on-tillgänglighets grupps dirigering|90% av åtgärderna har slutförts i 2,5 timmar + tid för att dirigera alla databaser (220 GB/timme).|
| | | 

<sup>1</sup> skalning generell användning hanterade instans lagring orsakar ingen redundansväxling i slutet av åtgärden. I det här fallet består åtgärden av att uppdatera metadata och sprida svar för en skickad begäran.

**Kategori: ta bort**

|Åtgärd  |Tids krävande segment  |Uppskattad varaktighet  |
|---------|---------|---------|
|Borttagning av instans|Logg säkerhets kopiering för alla databaser|90% åtgärder har slutförts på upp till 1 minut.<br>OBS! om den sista instansen i under nätet tas bort, kommer den här åtgärden att schemalägga borttagning av virtuellt kluster efter 12 timmar. <sup>1</sup>|
|Borttagning av virtuellt kluster (som användarinitierad åtgärd)|Borttagning av virtuellt kluster|90% av åtgärderna har slutförts på upp till 1,5 timmar.|
| | | 

<sup>1</sup>12 timmar är den aktuella konfigurationen men det kan komma att ändras i framtiden. Om du behöver ta bort ett virtuellt kluster tidigare (till exempel för att frigöra under nätet) kan du se [ta bort ett undernät när du har tagit bort en hanterad instans](virtual-cluster-delete.md).

## <a name="instance-availability"></a>Tillgänglighet för instans

SQL-hanterad instans **är tillgänglig under uppdaterings åtgärder**, förutom ett kort stillestånd som orsakas av redundansväxlingen som inträffar i slutet av uppdateringen. Det tar vanligt vis upp till 10 sekunder, även om tids krävande transaktioner har avbrutits, tack vare [accelererad databas återställning](../accelerated-database-recovery.md).

> [!NOTE]
> Skalning Generell användning hanterade instans lagring orsakar ingen redundansväxling i slutet av uppdateringen.

SQL-hanterad instans är inte tillgänglig för klient program under distributions-och borttagnings åtgärder.

> [!IMPORTANT]
> Det rekommenderas inte att skala beräkning eller lagring av Azure SQL-hanterad instans eller ändra tjänst nivån på samma gång som tids krävande transaktioner (data import, data bearbetnings jobb, index återuppbyggnad osv.). Redundansväxlingen av databasen i slutet av åtgärden avbryter alla pågående transaktioner. 

## <a name="management-operations-steps"></a>Steg för hanterings åtgärder

Hanterings åtgärder består av flera steg. Med [Operations-API introducerade](management-operations-monitor.md) dessa steg för del åtgärder (distribution och uppdatering). Distributions åtgärden består av tre steg medan uppdaterings åtgärden utförs i sex steg. Mer information om åtgärdernas varaktighet finns i avsnittet om [hanterings åtgärdernas varaktighet](#duration) . Stegen visas efter körnings ordning.

### <a name="managed-instance-deployment-steps"></a>Distributions steg för hanterade instanser

|Steg namn  |Steg beskrivning  |
|----|---------|
|Begär verifiering |Skickade parametrar verifieras. Om det uppstår fel i en felkonfigurerad åtgärd Miss kan det uppstå ett fel. |
|Storleks ändring/skapande av virtuellt kluster |Beroende på under nätets tillstånd går det att skapa eller ändra storlek på det virtuella klustret. |
|Start av ny SQL-instans |SQL-processen har startats på det distribuerade virtuella klustret. |

### <a name="managed-instance-update-steps"></a>Uppdaterings steg för hanterade instanser

|Steg namn  |Steg beskrivning  |
|----|---------|
|Begär verifiering | Skickade parametrar verifieras. Om det uppstår fel i en felkonfigurerad åtgärd Miss kan det uppstå ett fel. |
|Storleks ändring/skapande av virtuellt kluster |Beroende på under nätets tillstånd går det att skapa eller ändra storlek på det virtuella klustret. |
|Start av ny SQL-instans | SQL-processen har startats på det distribuerade virtuella klustret. |
|Dirigera databasfiler/bifoga databasfiler |Beroende på vilken typ av uppdatering som utförts, utförs antingen databasens Dirigerings-eller kopplings databas. |
|Förbereda redundans och redundans |När data har dirigerats eller databasfiler har återanslutits förbereds systemet för redundansväxlingen. När allt är inställt utförs redundans **med en kort stillestånds** tid. |
|Gammal SQL-instans rensa |Tar bort gammal SQL-process från det virtuella klustret |

> [!NOTE]
> Till följd av skalnings instanser går det underliggande virtuella klustret igenom processen med att släppa oanvänd kapacitet och möjlig defragmentering, vilket kan påverka instanser som inte har delta i skapandet/skalnings åtgärder. 


## <a name="management-operations-cross-impact"></a>Hanterings åtgärder över-påverkan

Hanterings åtgärder på en hanterad instans kan påverka andra hanterings åtgärder för de instanser som placeras i samma virtuella kluster:

- **Långvariga återställnings åtgärder** i ett virtuellt kluster kommer att placera andra instanser av att skapa eller skala i samma undernät vid undantag.<br/>**Exempel:** Om det finns en tids krävande återställnings åtgärd och det finns en begäran om att skapa eller skala i samma undernät, tar det längre tid att slutföra den här begäran eftersom den väntar på att återställnings åtgärden ska slutföras innan den fortsätter.

- **En efterföljande instans skapas eller skalnings** åtgärd spärras av en tidigare initierad instans eller instans skala som initierade en storlek på det virtuella klustret.<br/>**Exempel:** Om det finns flera skapande-och/eller skalnings begär anden i samma undernät under samma virtuella kluster, och en av dem initierar en storlek för virtuella kluster, kommer alla begär Anden som har skickats 5 + minuter efter den första åtgärden att bli längre än förväntat, eftersom dessa begär Anden måste vänta på att storleken ska slutföras innan den kan återupptas.

- **Create/Scale-åtgärder som skickas i en 5-minuters period** kommer att grupperas och köras parallellt.<br/>**Exempel:** Det går bara att ändra storlek på ett virtuellt kluster för alla åtgärder som skickas i ett fönster med 5 minuter (mätning från tidpunkten för att köra den första åtgärden). Om en annan begäran skickas mer än 5 minuter efter att den första har skickats, väntar det att det virtuella klustrets storlek ska slutföras innan körningen påbörjas.

> [!IMPORTANT]
> Hanterings åtgärder som spärras på grund av att en annan åtgärd pågår återupptas automatiskt när villkoren för att fortsätta är uppfyllda. Ingen användar åtgärd krävs för att återuppta tillfälligt pausade hanterings åtgärder.

## <a name="monitoring-management-operations"></a>Övervaknings hanterings åtgärder

Information om hur du övervakar hanterings åtgärdens förlopp och status finns i [övervaknings hanterings åtgärder](management-operations-monitor.md).

## <a name="canceling-management-operations"></a>Avbryta hanterings åtgärder

Information om hur du avbryter hanterings åtgärden finns i [avbryta hanterings åtgärder](management-operations-cancel.md).


## <a name="next-steps"></a>Nästa steg

- Information om hur du skapar din första hanterade instans finns i [snabb starts guide](instance-create-quickstart.md).
- En funktion och en jämförelse lista finns i [vanliga SQL-funktioner](../database/features-comparison.md).
- Mer information om VNet-konfiguration finns i [konfiguration av SQL Managed instance VNet](connectivity-architecture-overview.md).
- En snabb start som skapar en hanterad instans och återställer en databas från en säkerhets kopia finns i [skapa en hanterad instans](instance-create-quickstart.md).
- En själv studie kurs om hur du använder Azure Database Migration Service för migrering finns i [migrering av SQL-hanterad instans med hjälp av Database migration service](../../dms/tutorial-sql-server-to-managed-instance.md).
