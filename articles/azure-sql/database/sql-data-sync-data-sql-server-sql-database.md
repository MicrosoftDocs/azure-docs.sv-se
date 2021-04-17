---
title: Vad är SQL Data Sync för Azure?
description: Den här översikten SQL Data Sync för Azure, där du kan synkronisera data över flera molndatabaser och lokala databaser.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync, sqldbrb=1, fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/20/2019
ms.openlocfilehash: 695409740348e78ae51b263b44d9ed1cbadc1054
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531927"
---
# <a name="what-is-sql-data-sync-for-azure"></a>Vad är SQL Data Sync för Azure?

SQL Data Sync är en tjänst som bygger på Azure SQL Database som gör att du kan synkronisera de data du väljer i båda riktningarna över flera databaser, både lokalt och i molnet. 

> [!IMPORTANT]
> Azure SQL Data Sync stöder inte Azure SQL Managed Instance just nu.


## <a name="overview"></a>Översikt 

Data Sync baseras på begreppet synkroniseringsgrupp. En synkroniseringsgrupp är en grupp databaser som du vill synkronisera.

Data Sync använder en topologi med nav och ekrar för att synkronisera data. Du definierar en av databaserna i synkroniseringsgruppen som hubbdatabasen. Resten av databaserna är medlemsdatabaser. Synkronisering sker bara mellan hubben och enskilda medlemmar.

- **Hubbdatabasen** måste vara en Azure SQL Database.
- **Medlemsdatabaserna** kan antingen vara databaser i Azure SQL Database eller i instanser av SQL Server.
- Sync **Metadata Database innehåller** metadata och loggen för Data Sync. Sync Metadata Database måste vara en Azure SQL Database i samma region som hub-databasen. Synkroniseringsmetadatadatabasen är kundskapad och kundägd. Du kan bara ha en synkroniseringsmetadatadatabas per region och prenumeration. Sync Metadata Database kan inte tas bort eller byta namn medan synkroniseringsgrupper eller synkroniseringsagenter finns. Microsoft rekommenderar att du skapar en ny, tom databas som ska användas som synkroniseringsmetadatadatabas. Data Sync skapar tabeller i den här databasen och kör en frekvent arbetsbelastning.

> [!NOTE]
> Om du använder en lokal databas som en medlemsdatabas måste du installera [och konfigurera en lokal synkroniseringsagent.](sql-data-sync-sql-server-configure.md#add-on-prem)

![Synkronisera data mellan databaser](./media/sql-data-sync-data-sql-server-sql-database/sync-data-overview.png)

En synkroniseringsgrupp har följande egenskaper:

- **Synkroniseringsschemat** beskriver vilka data som synkroniseras.
- **Synkroniseringsriktningen** kan vara dubbelriktad eller bara flöda i en riktning. Synkroniseringsriktningen kan vara Hub to Member (Hubb *till medlem)* eller *Member to Hub (Medlem till hubb)* eller båda.
- **Synkroniseringsintervallet** beskriver hur ofta synkronisering sker.
- **Konfliktlösningsprincipen är** en grupprincip på gruppnivå som kan vara *Hub wins eller* Member *wins*.

## <a name="when-to-use"></a>När du ska använda detta

Data Sync är användbart i fall där data måste uppdateras över flera databaser i Azure SQL Database eller SQL Server. Här är de viktigaste användningsfallen för Data Sync:

- **Hybriddatasynkronisering:** Med Data Sync kan du hålla data synkroniserade mellan dina databaser i SQL Server och Azure SQL Database för att aktivera hybridprogram. Den här funktionen kan tilltala kunder som överväger att flytta till molnet och vill placera en del av sina program i Azure.
- **Distribuerade program:** I många fall är det bra att separera olika arbetsbelastningar mellan olika databaser. Om du till exempel har en stor produktionsdatabas, men även behöver köra en rapport- eller analysarbetsbelastning på dessa data, är det bra att ha en andra databas för den här ytterligare arbetsbelastningen. Den här metoden minimerar prestandapåverkan på din produktionsarbetsbelastning. Du kan använda Data Sync att hålla dessa två databaser synkroniserade.
- **Globalt distribuerade program:** Många företag sträcker sig över flera regioner och till och med flera länder/regioner. För att minimera nätverksfördröjningen är det bäst att ha dina data i en region nära dig. Med Data Sync kan du enkelt synkronisera databaser i regioner över hela världen.

Data Sync är inte den bästa lösningen för följande scenarier:

| Scenario | Några rekommenderade lösningar |
|----------|----------------------------|
| Haveriberedskap | [Geo-redundanta Azure-säkerhetskopieringar](automated-backups-overview.md) |
| Lässkala | [Använd skrivskyddade repliker för att belastningsutjämna skrivskyddade frågearbetsbelastningar (förhandsversion)](read-scale-out.md) |
| ETL (OLTP till OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) eller [SQL Server Integration Services](/sql/integration-services/sql-server-integration-services) |
| Migrering från SQL Server till Azure SQL Database. Men SQL Data Sync kan användas när migreringen har slutförts för att säkerställa att källan och målet är synkroniserade.  | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="how-it-works"></a>Så här fungerar det

- **Spåra dataändringar:** Data Sync spårar ändringar med hjälp av infognings-, uppdaterings- och borttagningsutlösare. Ändringarna registreras i en sidotabell i användardatabasen. Observera att BULK INSERT inte utlöser utlösare som standard. Om FIRE_TRIGGERS inte har angetts körs inga infogningsutlösare. Lägg till FIRE_TRIGGERS så Data Sync kan spåra dessa infogningar. 
- **Synkronisera data:** Data Sync är utformat i en nav- och ekermodell. Hubben synkroniseras med varje medlem individuellt. Ändringar från hubben laddas ned till medlemmen och sedan laddas ändringar från medlemmen upp till hubben.
- **Lösa konflikter:** Data Sync två alternativ för konfliktlösning, *hubb vinner eller* medlem *vinner*.
  - Om du *väljer Hubb vinner* skriver ändringarna i hubben alltid över ändringar i medlemmen.
  - Om du *väljer Medlem vinner* kommer ändringarna i medlemmen att skriva över ändringar i hubben. Om det finns fler än en medlem beror det slutliga värdet på vilken medlem som synkroniserar först.

## <a name="compare-with-transactional-replication"></a>Jämför med transaktionsreplikering

| | Datasynkronisering | Transaktionsreplikering |
|---|---|---|
| **Fördelar** | – Stöd för aktiv-aktiv<br/>– Dubbelriktad mellan lokala och Azure SQL Database | – Kortare svarstider<br/>– Transaktionskonsekvens<br/>– Återanvända befintlig topologi efter migrering <br/>-Azure SQL Managed Instance support |
| **Nackdelar** | – Ingen transaktionskonsekvens<br/>– Högre prestandapåverkan | – Det går inte att publicera från Azure SQL Database <br/>– Höga underhållskostnader |

## <a name="private-link-for-data-sync-preview"></a>Privat länk för Data Sync (förhandsversion)
Med den nya funktionen för privat länk (förhandsversion) kan du välja en tjänst hanterad privat slutpunkt för att upprätta en säker anslutning mellan synkroniseringstjänsten och dina medlems-/hubbdatabaser under datasynkroniseringsprocessen. En tjänst hanterad privat slutpunkt är en privat IP-adress inom ett specifikt virtuellt nätverk och undernät. Inom Data Sync skapas den tjänst hanterade privata slutpunkten av Microsoft och används uteslutande av Data Sync tjänsten för en viss synkroniseringsåtgärd. Läs de allmänna kraven för funktionen innan du [ställer](sql-data-sync-data-sql-server-sql-database.md#general-requirements) in den privata länken. 

![Privat länk för Data Sync](./media/sql-data-sync-data-sql-server-sql-database/sync-private-link-overview.png)

> [!NOTE]
> Du måste manuellt godkänna den tjänst  hanterade privata slutpunkten på sidan Privata slutpunktsanslutningar i Azure Portal under distributionen av synkroniseringsgruppen eller med hjälp av PowerShell.

## <a name="get-started"></a>Kom igång 

### <a name="set-up-data-sync-in-the-azure-portal"></a>Konfigurera Data Sync i Azure Portal

- [Konfigurera Azure SQL Data Sync](sql-data-sync-sql-server-configure.md)
- Datasynkroniseringsagent – [Datasynkroniseringsagent för Azure SQL Data Sync](sql-data-sync-agent-overview.md)

### <a name="set-up-data-sync-with-powershell"></a>Konfigurera Data Sync med PowerShell

- [Använd PowerShell för att synkronisera mellan flera databaser i Azure SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases.md)
- [Använda PowerShell för att synkronisera mellan en databas Azure SQL Database en databas i en SQL Server instans](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

### <a name="set-up-data-sync-with-rest-api"></a>Konfigurera Data Sync med REST API
- [Använd REST API för att synkronisera mellan flera databaser i Azure SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases-rest-api.md)

### <a name="review-the-best-practices-for-data-sync"></a>Granska metodtipsen för Data Sync

- [Metodtips för Azure SQL Data Sync](sql-data-sync-best-practices.md)

### <a name="did-something-go-wrong"></a>Har något gått fel

- [Felsöka problem med Azure SQL Data Sync](./sql-data-sync-troubleshoot.md)

## <a name="consistency-and-performance"></a>Konsekvens och prestanda

### <a name="eventual-consistency"></a>Slutlig konsekvens

Eftersom Data Sync är utlösarbaserad garanteras inte transaktionskonsekvens. Microsoft garanterar att alla ändringar görs så småningom och Data Sync inte orsakar dataförlust.

### <a name="performance-impact"></a>Prestandapåverkan

Data Sync infognings-, uppdaterings- och borttagningsutlösare för att spåra ändringar. Den skapar sidotabeller i användardatabasen för ändringsspårning. Dessa aktiviteter för ändringsspårning påverkar din databasarbetsbelastning. Utvärdera din tjänstnivå och uppgradera om det behövs.

Etablering och avetablering när synkroniseringsgruppen skapas, uppdateras och tas bort kan också påverka databasens prestanda.

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a> Krav och begränsningar

### <a name="general-requirements"></a>Allmänna krav

- Varje tabell måste ha en primärnyckel. Ändra inte värdet för primärnyckeln på någon rad. Om du måste ändra värdet för en primär nyckel tar du bort raden och återskapar den med det nya primärnyckelvärdet.

> [!IMPORTANT]
> Om du ändrar värdet för en befintlig primärnyckel resulterar det i följande felaktiga beteende:
> - Data mellan hubb och medlem kan gå förlorade även om synkroniseringen inte rapporterar några problem.
> - Synkroniseringen kan misslyckas eftersom spårningstabellen har en icke-befintlig rad från källan på grund av den primära nyckeländringen.

- Isolering av ögonblicksbilder måste aktiveras för både synkroniseringsmedlemmar och hubb. Mer information finns i [Ögonblicksbildisolering i SQL Server](/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

- För att kunna använda privat länk med Data Sync måste både medlems- och hubbdatabaserna finnas i Azure (samma eller olika regioner), i samma molntyp (t.ex. både i det offentliga molnet eller båda i myndighetsmolnet). Om du vill använda private link måste dessutom Microsoft.Network-resursproviders vara Registrerade för de prenumerationer som är värdar för hubb- och medlemsservrarna. Slutligen måste du manuellt godkänna den privata länken för Data Sync under synkroniseringskonfigurationen i avsnittet "Privata slutpunktsanslutningar" i Azure Portal eller via PowerShell. Mer information om hur du godkänner den privata länken finns [i Konfigurera SQL Data Sync](./sql-data-sync-sql-server-configure.md). När du har godkänt den tjänst hanterade privata slutpunkten sker all kommunikation mellan synkroniseringstjänsten och medlems-/hubbdatabaserna via den privata länken. Befintliga synkroniseringsgrupper kan uppdateras så att den här funktionen är aktiverad.

### <a name="general-limitations"></a>Allmänna begränsningar

- En tabell kan inte ha en identitetskolumn som inte är den primära nyckeln.
- En tabell måste ha ett grupperat index för att använda datasynkronisering.
- En primär nyckel kan inte ha följande datatyper: sql_variant, binär, varbinary, bild, xml.
- Var försiktig när du använder följande datatyper som primärnyckel, eftersom precisionen som stöds endast är den andra: tid, datetime, datetime2, datetimeoffset.
- Namnen på objekt (databaser, tabeller och kolumner) får inte innehålla de utskrivbara tecknen punkt (.), vänster hakparentes ([) eller höger hakparentes (]).
- Ett tabellnamn får inte innehålla utskrivbara tecken: ! " # $ % ' ( ) * + - blanksteg
- Azure Active Directory stöds inte.
- Om det finns tabeller med samma namn men ett annat schema (till exempel dbo.customers och sales.customers) kan bara en av tabellerna läggas till i synkroniseringen.
- Kolumner User-Defined datatyper stöds inte
- Det finns inte stöd för att flytta servrar mellan olika prenumerationer. 
- Om två primära nycklar endast är olika i fall (t.ex. Foo och foo) Data Sync inte det här scenariot.

#### <a name="unsupported-data-types"></a>Datatyper som inte stöds

- Filestream
- SQL/CLR UDT
- XMLSchemaCollection (XML stöds)
- Cursor, RowVersion, Timestamp, Hierarchyid

#### <a name="unsupported-column-types"></a>Kolumntyper som inte stöds

Data Sync kan inte synkronisera skrivskyddade eller systemgenererade kolumner. Exempel:

- Beräknade kolumner.
- Systemgenererade kolumner för temporala tabeller.

#### <a name="limitations-on-service-and-database-dimensions"></a>Begränsningar för tjänst- och databasdimensioner

| **Dimensioner**                                                  | **Gräns**              | **Lösning**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maximalt antal synkroniseringsgrupper som en databas kan tillhöra.       | 5                      |                             |
| Maximalt antal slutpunkter i en enda synkroniseringsgrupp              | 30                     |                             |
| Maximalt antal lokala slutpunkter i en enda synkroniseringsgrupp. | 5                      | Skapa flera synkroniseringsgrupper |
| Databas-, tabell-, schema- och kolumnnamn                       | 50 tecken per namn |                             |
| Tabeller i en synkroniseringsgrupp                                          | 500                    | Skapa flera synkroniseringsgrupper |
| Kolumner i en tabell i en synkroniseringsgrupp                              | 1000                   |                             |
| Dataradstorlek i en tabell                                        | 24 MB                  |                             |

> [!NOTE]
> Det kan finnas upp till 30 slutpunkter i en enda synkroniseringsgrupp om det bara finns en synkroniseringsgrupp. Om det finns fler än en synkroniseringsgrupp får det totala antalet slutpunkter för alla synkroniseringsgrupper inte överskrida 30. Om en databas tillhör flera synkroniseringsgrupper räknas den som flera slutpunkter, inte en.

### <a name="network-requirements"></a>Nätverkskrav

> [!NOTE]
> Om du använder private link gäller inte dessa nätverkskrav. 

När synkroniseringsgruppen har upprättats måste Data Sync-tjänsten ansluta till hubbdatabasen. När du upprättar synkroniseringsgruppen måste Azure SQL ha följande konfiguration i `Firewalls and virtual networks` sina inställningar:

 * *Neka offentlig nätverksåtkomst måste* vara inställt på *Av*.
 * *Tillåt Att Azure-tjänster och* -resurser får åtkomst till den här servern måste vara inställt på *Ja,* eller så måste du skapa IP-regler för de IP-adresser som [används Data Sync tjänsten](network-access-controls-overview.md#data-sync).

När synkroniseringsgruppen har skapats och etablerats kan du inaktivera de här inställningarna. Synkroniseringsagenten ansluter direkt till hubbdatabasen och du kan använda [serverns](private-endpoint-overview.md) [brandväggs-IP-regler](firewall-configure.md) eller privata slutpunkter för att ge agenten åtkomst till hubbservern.

> [!NOTE]
> Om du ändrar synkroniseringsgruppens schemainställningar måste du tillåta att Data Sync-tjänsten får åtkomst till servern igen så att hubbdatabasen kan etableras igen.

## <a name="faq-about-sql-data-sync"></a>Vanliga frågor och svar om SQL Data Sync

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Hur mycket kostar SQL Data Sync tjänsten

Det debiteras inget för SQL Data Sync tjänsten. Du samlar dock fortfarande in dataöverföringsavgifter för dataförflyttning till och från din SQL Database instans. Mer information finns i [SQL Database prissättning.](https://azure.microsoft.com/pricing/details/sql-database/)

### <a name="what-regions-support-data-sync"></a>Vilka regioner stöder Data Sync

SQL Data Sync är tillgängligt i alla regioner.

### <a name="is-a-sql-database-account-required"></a>Krävs ett SQL Database-konto

Ja. Du måste ha ett SQL Database som värd för hubbdatabasen.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-databases-only"></a>Kan jag använda Data Sync för att synkronisera mellan SQL Server databaser

Inte direkt. Du kan dock SQL Server mellan databaser indirekt genom att skapa en hubbdatabas i Azure och sedan lägga till de lokala databaserna i synkroniseringsgruppen.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-subscriptions"></a>Kan jag använda Data Sync för att synkronisera mellan databaser SQL Database som tillhör olika prenumerationer

Ja. Du kan synkronisera mellan databaser som tillhör resursgrupper som ägs av olika prenumerationer.

- Om prenumerationerna tillhör samma klientorganisation och du har behörighet till alla prenumerationer kan du konfigurera synkroniseringsgruppen i Azure Portal.
- Annars måste du använda PowerShell för att lägga till de synkroniseringsmedlemmar som tillhör olika prenumerationer.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Kan jag använda Data Sync för att synkronisera mellan databaser i SQL Database som tillhör olika moln (som Azures offentliga moln och Azure China 21Vianet)

Ja. Du kan synkronisera mellan databaser som tillhör olika moln. Du måste använda PowerShell för att lägga till de synkroniseringsmedlemmar som tillhör de olika prenumerationerna.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Kan jag använda Data Sync för att seeda data från min produktionsdatabas till en tom databas och sedan synkronisera dem

Ja. Skapa schemat manuellt i den nya databasen genom att skripta det från originalet. När du har skapat schemat lägger du till tabellerna i en synkroniseringsgrupp för att kopiera data och synkronisera dem.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Bör jag använda SQL Data Sync för att backa upp och återställa mina databaser

Vi rekommenderar inte att du använder SQL Data Sync för att skapa en säkerhetskopia av dina data. Du kan inte återställning till en viss tidpunkt eftersom SQL Data Sync inte har versionshantering. Dessutom SQL Data Sync inte andra SQL-objekt, till exempel lagrade procedurer, och gör inte motsvarigheten till en återställningsåtgärd snabbt.

En rekommenderad säkerhetskopieringsteknik finns [i Kopiera en databas i Azure SQL Database](database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Kan Data Sync synkronisera krypterade tabeller och kolumner

- Om en databas använder Always Encrypted kan du bara synkronisera de tabeller och kolumner som *inte är* krypterade. Du kan inte synkronisera krypterade kolumner eftersom Data Sync inte kan dekryptera data.
- Om en kolumn använder Column-Level Encryption (CLE) kan du synkronisera kolumnen så länge radstorleken är mindre än den maximala storleken på 24 MB. Data Sync behandlar kolumnen som krypterats med nyckel (CLE) som normala binära data. Om du vill dekryptera data på andra synkroniseringsmedlemmar måste du ha samma certifikat.

### <a name="is-collation-supported-in-sql-data-sync"></a>Stöds sortering i SQL Data Sync

Ja. SQL Data Sync har stöd för sortering i följande scenarier:

- Om de valda synkroniseringsschematabellerna inte redan finns i hubb- eller medlemsdatabaserna skapar tjänsten automatiskt motsvarande tabeller och kolumner med sorteringsinställningarna som valts i de tomma måldatabaserna när du distribuerar synkroniseringsgruppen.
- Om de tabeller som ska synkroniseras redan finns i både hubb- och medlemsdatabaserna kräver SQL Data Sync att primärnyckelkolumnerna har samma sortering mellan hubb- och medlemsdatabaser för att synkroniseringsgruppen ska kunna distribueras. Det finns inga sorteringsbegränsningar för andra kolumner än primärnyckelkolumnerna.

### <a name="is-federation-supported-in-sql-data-sync"></a>Stöds federation i SQL Data Sync

Federationsrotdatabasen kan användas i SQL Data Sync-tjänsten utan begränsning. Du kan inte lägga till slutpunkten för den federerade databasen i den aktuella versionen av SQL Data Sync.

### <a name="can-i-use-data-sync-to-sync-data-exported-from-dynamics-365-using-bring-your-own-database-byod-feature"></a>Kan jag använda Data Sync för att synkronisera data som exporteras från Dynamics 365 med hjälp av BYOD-funktionen (Bring Your Own Database)?

Med funktionen Bring Your Own Database i Dynamics 365 kan administratörer exportera dataentiteter från programmet till en egen Microsoft Azure SQL-databas. Data Sync kan användas för att synkronisera dessa data till andra databaser om data exporteras med hjälp av inkrementell **push** (fullständig push stöds inte) och aktivera utlösare i måldatabasen har **angetts** till **ja**.

## <a name="next-steps"></a>Nästa steg

### <a name="update-the-schema-of-a-synced-database"></a>Uppdatera schemat för en synkroniserad databas

Måste du uppdatera schemat för en databas i en synkroniseringsgrupp? Schemaändringar replikeras inte automatiskt. Några lösningar finns i följande artiklar:

- [Automatisera replikeringen av schemaändringar med SQL Data Sync i Azure](./sql-data-sync-update-sync-schema.md)
- [Använd PowerShell för att uppdatera synkroniseringsschemat i en befintlig synkroniseringsgrupp](scripts/update-sync-schema-in-sync-group.md)

### <a name="monitor-and-troubleshoot"></a>Övervaka och felsök

Går SQL Data Sync som förväntat? Information om hur du övervakar aktivitet och felsöker problem finns i följande artiklar:

- [Övervaka SQL Data Sync med Azure Monitor loggar](./monitor-tune-overview.md)
- [Felsöka problem med Azure SQL Data Sync](./sql-data-sync-troubleshoot.md)

### <a name="learn-more-about-azure-sql-database"></a>Läs mer om Azure SQL Database

Mer information om Azure SQL Database finns i följande artiklar:

- [Översikt över SQL Database](sql-database-paas-overview.md)
- [Livscykelhantering för databas](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
