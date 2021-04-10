---
title: Vad är SQL Data Sync för Azure?
description: I den här översikten introduceras SQL Data Sync för Azure, vilket gör att du kan synkronisera data i flera molnbaserade och lokala databaser.
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
ms.openlocfilehash: c38e4681c76fb0dd52d77c7dc1438b87a9571a80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103562067"
---
# <a name="what-is-sql-data-sync-for-azure"></a>Vad är SQL Data Sync för Azure?

SQL Data Sync är en tjänst som bygger på Azure SQL Database som låter dig synkronisera data som du väljer i båda riktningarna i flera databaser, både lokalt och i molnet. 

> [!IMPORTANT]
> Azure SQL Data Sync har inte stöd för Azure SQL-hanterad instans för tillfället.


## <a name="overview"></a>Översikt 

Datasynkronisering baseras på begreppet Sync-grupp. En Sync-grupp är en grupp databaser som du vill synkronisera.

Datasynkroniseringen använder en nav-och eker-topologi för att synkronisera data. Du definierar en av databaserna i Sync-gruppen som Hub-databasen. Resten av databaserna är medlems databaser. Synkronisering sker bara mellan hubben och enskilda medlemmar.

- **Hub-databasen** måste vara en Azure SQL Database.
- **Medlems databaserna** kan vara antingen databaser i Azure SQL Database eller i instanser av SQL Server.
- **Databasen för synkronisering av metadata** innehåller metadata och logg för datasynkronisering. Databasen för synkronisering av metadata måste vara en Azure SQL Database som finns i samma region som Hub-databasen. Databasen för synkronisering av metadata är kund som har skapats och kunden äger. Du kan bara ha en databas för synkronisering av metadata per region och prenumeration. Det går inte att ta bort eller byta namn på databasen för synkronisering av metadata medan Sync-grupper eller Sync-agenter finns. Microsoft rekommenderar att du skapar en ny, tom databas för användning som databasen för synkronisering av metadata. Datasynkronisering skapar tabeller i den här databasen och kör en frekvent arbets belastning.

> [!NOTE]
> Om du använder en lokal databas som en medlems databas måste du [Installera och konfigurera en lokal Sync-agent](sql-data-sync-sql-server-configure.md#add-on-prem).

![Synkronisera data mellan databaser](./media/sql-data-sync-data-sql-server-sql-database/sync-data-overview.png)

En Sync-grupp har följande egenskaper:

- **Synkroniseringsschemat** beskriver vilka data som synkroniseras.
- **Sync-riktningen** kan vara dubbelriktad eller kan endast flöda i en riktning. Det vill säga att synkroniseringen kan vara *hubb till medlem* eller *medlem i hubben* eller både och.
- **Intervallet för synkronisering** beskriver hur ofta synkronisering sker.
- **Konflikt lösnings principen** är en princip på grup nivå som kan vara en *hubb-WINS* eller *-medlem*.

## <a name="when-to-use"></a>När du ska använda detta

Datasynkronisering är användbart i fall där data måste uppdateras i flera databaser i Azure SQL Database eller SQL Server. Här är de viktigaste användnings fallen för datasynkronisering:

- **Synkronisering av hybrid data:** Med datasynkronisering kan du hålla data synkroniserade mellan databaserna i SQL Server och Azure SQL Database för att aktivera hybrid program. Den här funktionen kan överklaga kunder som överväger att flytta till molnet och som vill placera en del av deras program i Azure.
- **Distribuerade program:** I många fall är det bra att separera olika arbets belastningar i olika databaser. Om du till exempel har en stor produktions databas, men du också behöver köra en rapporterings-eller analys arbets belastning för dessa data, är det bra att ha en andra databas för den här ytterligare arbets belastningen. Den här metoden minimerar prestanda påverkan i produktions arbets belastningen. Du kan använda datasynkronisering för att hålla dessa två databaser synkroniserade.
- **Globalt distribuerade program:** Många företag sträcker sig över flera regioner och till och med flera länder/regioner. För att minimera nätverks fördröjningen är det bäst att ha dina data i en region nära dig. Med datasynkronisering kan du enkelt lagra databaser i regioner runt om i världen.

Datasynkronisering är inte den bästa lösningen i följande scenarier:

| Scenario | Några rekommenderade lösningar |
|----------|----------------------------|
| Haveriberedskap | [Azure geo-redundanta säkerhets kopieringar](automated-backups-overview.md) |
| Läs skala | [Använd skrivskyddade repliker för att belastningsutjämna skrivskyddade arbets belastningar för frågor (för hands version)](read-scale-out.md) |
| ETL (OLTP till OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) eller [SQL Server Integration Services](/sql/integration-services/sql-server-integration-services) |
| Migrering från SQL Server till Azure SQL Database. SQL Data Sync kan dock användas efter att migreringen har slutförts, för att säkerställa att källan och målet hålls synkroniserade.  | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="how-it-works"></a>Så här fungerar det

- **Spårar data ändringar:** Datasynkronisering spårar ändringar med hjälp av INSERT-, Update-och Delete-utlösare. Ändringarna registreras i en sido tabell i användar databasen. Observera att BULK INSERT inte utlösa utlösare som standard. Om FIRE_TRIGGERS inte anges körs inga infognings utlösare. Lägg till alternativet FIRE_TRIGGERS så att datasynkronisering kan spåra dessa infogningar. 
- **Synkroniserar data:** Datasynkronisering är utformad i en nav-och eker-modell. Hubben synkroniseras med varje medlem individuellt. Ändringar från hubben laddas ned till medlemmen och ändringar från medlemmen överförs till hubben.
- **Lösa konflikter:** Datasynkronisering innehåller två alternativ för konflikt lösning, *hubb-WINS* eller *medlems-WINS*.
  - Om du väljer *hubben WINS* skrivs ändringarna i hubben alltid över ändringar i medlemmen.
  - Om du väljer *medlem WINS* skriver ändringarna i medlemmen över ändringarna i hubben. Om det finns fler än en medlem beror det sista värdet på vilka medlemmar som synkroniseras först.

## <a name="compare-with-transactional-replication"></a>Jämför med transaktionell replikering

| | Datasynkronisering | Transaktionsreplikering |
|---|---|---|
| **Fördelar** | – Stöd för aktiv-aktiv<br/>– Dubbelriktad mellan lokala och Azure SQL Database | -Nedre latens<br/>– Transaktionell konsekvens<br/>-Återanvänd befintlig topologi efter migrering <br/>– Stöd för Azure SQL Managed instance |
| **Nackdelar** | – Ingen transaktionell konsekvens<br/>-Högre prestanda påverkan | -Det går inte att publicera från Azure SQL Database <br/>– Kostnad för hög underhåll |

## <a name="private-link-for-data-sync-preview"></a>Privat länk för datasynkronisering (förhands granskning)
Med funktionen ny privat länk (förhands granskning) kan du välja en tjänst hanterad privat slut punkt för att upprätta en säker anslutning mellan synkroniseringstjänsten och dina databas-/Hubbs databaser under synkroniseringsprocessen. En tjänst hanterad privat slut punkt är en privat IP-adress inom ett särskilt virtuellt nätverk och undernät. I datasynkroniseringen skapas tjänsten hanterad privat slut punkt av Microsoft och används exklusivt av data Sync-tjänsten för en specifik synkronisering. Innan du konfigurerar den privata länken bör du läsa de [allmänna kraven](sql-data-sync-data-sql-server-sql-database.md#general-requirements) för funktionen. 

![Privat länk för datasynkronisering](./media/sql-data-sync-data-sql-server-sql-database/sync-private-link-overview.png)

> [!NOTE]
> Du måste godkänna den hanterade privata slut punkten för tjänsten manuellt på sidan **anslutningar för privata slut punkter** i Azure Portal under distributionen av synkroniseringsresursen eller med hjälp av PowerShell.

## <a name="get-started"></a>Kom igång 

### <a name="set-up-data-sync-in-the-azure-portal"></a>Konfigurera datasynkronisering i Azure Portal

- [Konfigurera Azure SQL Data Sync](sql-data-sync-sql-server-configure.md)
- Datasynkroniseringsagent – [Datasynkroniseringsagent för Azure SQL Data Sync](sql-data-sync-agent-overview.md)

### <a name="set-up-data-sync-with-powershell"></a>Konfigurera datasynkronisering med PowerShell

- [Använd PowerShell för att synkronisera mellan flera databaser i Azure SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases.md)
- [Använda PowerShell för att synkronisera mellan en databas i Azure SQL Database och en databas i en SQL Server instans](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

### <a name="set-up-data-sync-with-rest-api"></a>Konfigurera datasynkronisering med REST API
- [Använd REST API för att synkronisera mellan flera databaser i Azure SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases-rest-api.md)

### <a name="review-the-best-practices-for-data-sync"></a>Granska metod tips för datasynkronisering

- [Metodtips för Azure SQL Data Sync](sql-data-sync-best-practices.md)

### <a name="did-something-go-wrong"></a>Har något gå fel

- [Felsöka problem med Azure SQL Data Sync](./sql-data-sync-troubleshoot.md)

## <a name="consistency-and-performance"></a>Konsekvens och prestanda

### <a name="eventual-consistency"></a>Slutlig konsekvens

Eftersom datasynkroniseringen är utlöst är inte transaktionell konsekvens garanterad. Microsoft garanterar att alla ändringar görs till och med att datasynkroniseringen inte leder till data förlust.

### <a name="performance-impact"></a>Prestandapåverkan

Data Sync använder INSERT-, Update-och Delete-utlösare för att spåra ändringar. Det skapar sido tabeller i användar databasen för ändrings spårning. Dessa ändrings spårnings aktiviteter påverkar databasens arbets belastning. Utvärdera tjänst nivån och uppgradera om det behövs.

Etablering och avetablering när du skapar, uppdaterar och tar bort grupper kan också påverka databasens prestanda.

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a> Krav och begränsningar

### <a name="general-requirements"></a>Allmänna krav

- Varje tabell måste ha en primär nyckel. Ändra inte värdet för primär nyckeln i valfri rad. Om du behöver ändra ett primär nyckel värde tar du bort raden och återskapar den med det nya värdet för primär nyckel.

> [!IMPORTANT]
> Att ändra värdet för en befintlig primär nyckel leder till följande fel beteende:
> - Data mellan hubb och medlem kan gå förlorade även om synkroniseringen inte rapporterar några problem.
> - Det går inte att synkronisera eftersom spårnings tabellen inte har en befintlig rad från källan på grund av den primära nyckel ändringen.

- Ögonblicks bilds isolering måste vara aktiverat för både Sync-medlemmar och-hubb. Mer information finns i [Ögonblicksbildisolering i SQL Server](/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

- För att kunna använda privat länk med datasynkronisering måste både medlemmen och hubben vara värdbaserade i Azure (samma eller olika regioner), i samma moln typ (t. ex. både i offentligt moln eller både i myndighets moln). Om du vill använda en privat länk måste Microsoft. Network Resource providers vara registrerade för de prenumerationer som är värdar för hubben och medlems servrarna. Slutligen måste du manuellt godkänna den privata länken för datasynkronisering under konfigurationen, i avsnittet "anslutningar för privata slut punkter" i Azure Portal eller via PowerShell. Mer information om hur du godkänner den privata länken finns i [konfigurera SQL Data Sync](./sql-data-sync-sql-server-configure.md). När du godkänner den hanterade privata slut punkten för tjänsten, sker all kommunikation mellan synkroniseringstjänsten och databas/hubb-databaser över den privata länken. De befintliga grupperna kan uppdateras för att aktivera den här funktionen.

### <a name="general-limitations"></a>Allmänna begränsningar

- En tabell kan inte ha en identitets kolumn som inte är primär nyckel.
- En tabell måste ha ett grupperat index för att kunna använda datasynkronisering.
- En primär nyckel kan inte ha följande data typer: sql_variant, Binary, varbinary, image, XML.
- Var försiktig när du använder följande data typer som primär nyckel, eftersom den precision som stöds endast är för den andra: Time, DateTime, datetime2, DateTimeOffset.
- Namn på objekt (databaser, tabeller och kolumner) får inte innehålla de utskrivbara tecken perioderna (.), vänster hak paren tes ([) eller höger hak paren tes (]).
- Ett tabell namn får inte innehålla skrivbara tecken:! "# $% ' () * +-utrymme
- Azure Active Directory autentisering stöds inte.
- Om det finns tabeller med samma namn men olika schema (till exempel dbo. kunder och försäljning. kunder) kan endast en av tabellerna läggas till i Sync.
- Kolumner med User-Defined data typer stöds inte
- Det finns inte stöd för att flytta servrar mellan olika prenumerationer. 

#### <a name="unsupported-data-types"></a>Data typer som inte stöds

- -
- SQL/CLR UDT
- XMLSchemaCollection (XML stöds)
- Markör, RowVersion, tidsstämpel, hierarchyid

#### <a name="unsupported-column-types"></a>Kolumn typer som inte stöds

Datasynkronisering kan inte synkronisera skrivskyddade eller systemgenererade kolumner. Exempel:

- Beräknade kolumner.
- Systemgenererade kolumner för temporala tabeller.

#### <a name="limitations-on-service-and-database-dimensions"></a>Begränsningar för tjänst-och databas dimensioner

| **Dimensioner**                                                  | **Gränserna**              | **Lösning**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maximalt antal Sync-grupper som alla databaser kan tillhöra.       | 5                      |                             |
| Maximalt antal slut punkter i en enskild Sync-grupp              | 30                     |                             |
| Maximalt antal lokala slut punkter i en enda Sync-grupp. | 5                      | Skapa flera Sync-grupper |
| Databas-, tabell-, schema-och kolumn namn                       | 50 tecken per namn |                             |
| Tabeller i en Sync-grupp                                          | 500                    | Skapa flera Sync-grupper |
| Kolumner i en tabell i en Sync-grupp                              | 1000                   |                             |
| Data rad storlek för en tabell                                        | 24 MB                  |                             |

> [!NOTE]
> Det kan finnas upp till 30 slut punkter i en enda Sync-grupp om det bara finns en Sync-grupp. Om det finns fler än en Sync-grupp får det totala antalet slut punkter i alla Sync-grupper inte överstiga 30. Om en databas tillhör flera Sync-grupper räknas den som flera slut punkter, inte en.

### <a name="network-requirements"></a>Nätverkskrav

> [!NOTE]
> Om du använder en privat länk gäller inte dessa nätverks krav. 

När Sync-gruppen har upprättats måste data Sync-tjänsten ansluta till Hub-databasen. När du upprättar Sync-gruppen måste Azure SQL-servern ha följande konfiguration i dess `Firewalls and virtual networks` inställningar:

 * *Neka offentlig nätverks åtkomst* måste anges till *av*.
 * *Tillåt att Azure-tjänster och-resurser får åtkomst till den här servern* måste anges till *Ja*, eller så måste du skapa IP-regler för de [IP-adresser som används av tjänsten Data Sync](network-access-controls-overview.md#data-sync).

När Sync-gruppen har skapats och kon figureras kan du inaktivera dessa inställningar. Sync-agenten ansluter direkt till NAV databasen och du kan använda serverns [IP-regler för brand väggen](firewall-configure.md) eller [privata slut punkter](private-endpoint-overview.md) för att ge agenten åtkomst till nav servern.

> [!NOTE]
> Om du ändrar inställningarna för Sync-gruppen måste du tillåta att tjänsten Data Sync används för att få åtkomst till servern igen, så att Hub-databasen kan etableras på nytt.

## <a name="faq-about-sql-data-sync"></a>Vanliga frågor och svar om SQL Data Sync

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Hur mycket kostar SQL Data Sync tjänsten

Det kostar inget att själva SQL Data Sync själva tjänsten. Du kan dock fortfarande samla in data överförings avgifter för data förflyttning in och ut ur din SQL Database-instans. Mer information finns i [SQL Database prissättning](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Vilka regioner stöder datasynkronisering

SQL Data Sync är tillgängligt i alla regioner.

### <a name="is-a-sql-database-account-required"></a>Är ett SQL Database konto obligatoriskt

Ja. Du måste ha ett SQL Database-konto för att vara värd för Hub-databasen.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-databases-only"></a>Kan jag endast använda datasynkronisering för att synkronisera mellan SQL Server-databaser

Inte direkt. Du kan synkronisera mellan SQL Server-databaser indirekt, genom att skapa en Hubbs databas i Azure och sedan lägga till de lokala databaserna i gruppen Sync.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-subscriptions"></a>Kan jag använda datasynkronisering för att synkronisera mellan databaser i SQL Database som tillhör olika prenumerationer

Ja. Du kan synkronisera mellan databaser som tillhör resurs grupper som ägs av olika prenumerationer.

- Om prenumerationerna tillhör samma klient organisation och du har behörighet till alla prenumerationer kan du konfigurera Sync-gruppen i Azure Portal.
- Annars måste du använda PowerShell för att lägga till de synkroniserade medlemmar som tillhör olika prenumerationer.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Kan jag använda datasynkronisering för att synkronisera mellan databaser i SQL Database som tillhör olika moln (t. ex. Azures offentliga moln och Azure Kina 21Vianet)

Ja. Du kan synkronisera mellan databaser som tillhör olika moln. Du måste använda PowerShell för att lägga till de Sync-medlemmar som tillhör olika prenumerationer.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Kan jag använda datasynkronisering för att dirigera data från min produktions databas till en tom databas och sedan synkronisera dem

Ja. Skapa schemat manuellt i den nya databasen genom att skriva skript från originalet. När du har skapat schemat lägger du till tabellerna i en Sync-grupp för att kopiera data och hålla den synkroniserad.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Bör jag använda SQL Data Sync för att säkerhetskopiera och återställa mina databaser

Vi rekommenderar inte att du använder SQL Data Sync för att skapa en säkerhets kopia av dina data. Du kan inte säkerhetskopiera och återställa till en viss tidpunkt eftersom SQL Data Sync-synkronisering inte är versions hantering. Dessutom kan SQL Data Sync inte säkerhetskopiera andra SQL-objekt, t. ex. lagrade procedurer, och gör inte lika med en återställnings åtgärd snabbt.

En rekommenderad säkerhets kopierings teknik finns [i kopiera en databas i Azure SQL Database](database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Kan synkronisera krypterade tabeller och kolumner med datasynkronisering

- Om en databas använder Always Encrypted kan du bara synkronisera de tabeller och kolumner som *inte* är krypterade. Du kan inte synkronisera de krypterade kolumnerna eftersom datasynkroniseringen inte kan dekryptera data.
- Om en kolumn använder Column-Level kryptering (CLE) kan du synkronisera kolumnen så länge rad storleken är mindre än den maximala storleken på 24 MB. Datasynkroniseringen behandlar kolumnen som krypteras av nyckeln (CLE) som normala binära data. Om du vill dekryptera data på andra synkroniserade medlemmar måste du ha samma certifikat.

### <a name="is-collation-supported-in-sql-data-sync"></a>Stöds sortering i SQL Data Sync

Ja. SQL Data Sync stöder sortering i följande scenarier:

- Om de valda synkroniseringsschemat inte redan finns i din hubb eller medlems databaser, skapar tjänsten automatiskt motsvarande tabeller och kolumner med sorterings inställningarna markerade i de tomma mål databaserna när du distribuerar synkroniseringsresursen.
- Om de tabeller som ska synkroniseras redan finns i både din hubb och medlems databaser, kräver SQL Data Sync att primär nyckel kolumnerna har samma sortering mellan hubben och medlems databaserna för att kunna distribuera Sync-gruppen. Det finns inga sorterings begränsningar för andra kolumner än primär nyckel kolumnerna.

### <a name="is-federation-supported-in-sql-data-sync"></a>Stöds Federation i SQL Data Sync

Federations rot databasen kan användas i SQL Data Sync tjänsten utan någon begränsning. Du kan inte lägga till den federerade databas slut punkten till den aktuella versionen av SQL Data Sync.

### <a name="can-i-use-data-sync-to-sync-data-exported-from-dynamics-365-using-bring-your-own-database-byod-feature"></a>Kan jag använda datasynkronisering för att synkronisera data som exporter ATS från Dynamics 365 med en egen databas (BYOD)-funktion?

Med hjälp av en egen databas funktion i Dynamics 365 kan administratörer exportera dataentiteter från programmet till sin egen Microsoft Azure SQL-databas. Datasynkronisering kan användas för att synkronisera dessa data i andra databaser om data exporteras med hjälp av **stegvis push** (fullständig push-överföring stöds inte) och **Aktivera utlösare i mål databasen** har angetts till **Ja**.

## <a name="next-steps"></a>Nästa steg

### <a name="update-the-schema-of-a-synced-database"></a>Uppdatera schemat för en synkroniserad databas

Behöver du uppdatera schemat för en databas i en Sync-grupp? Schema ändringar replikeras inte automatiskt. Vissa lösningar finns i följande artiklar:

- [Automatisera replikeringen av schema ändringar med SQL Data Sync i Azure](./sql-data-sync-update-sync-schema.md)
- [Använd PowerShell för att uppdatera synkroniseringsschemat i en befintlig synkroniseringsgrupp](scripts/update-sync-schema-in-sync-group.md)

### <a name="monitor-and-troubleshoot"></a>Övervaka och felsök

Är SQL Data Sync att göra som det ska? Information om hur du övervakar aktiviteter och felsöker problem finns i följande artiklar:

- [Övervaka SQL Data Sync med Azure Monitor loggar](./monitor-tune-overview.md)
- [Felsöka problem med Azure SQL Data Sync](./sql-data-sync-troubleshoot.md)

### <a name="learn-more-about-azure-sql-database"></a>Läs mer om Azure SQL Database

Mer information om Azure SQL Database finns i följande artiklar:

- [Översikt över SQL Database](sql-database-paas-overview.md)
- [Livscykelhantering för databas](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
