---
title: Konfigurera Azure Cosmos DB konto med periodisk säkerhets kopiering
description: I den här artikeln beskrivs hur du konfigurerar Azure Cosmos DB-konton med periodisk säkerhets kopiering med säkerhets kopierings intervall. och kvarhållning. Det går också att kontakta supporten för att återställa dina data.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 69a9f0a82f5c19504564825e47f69ab8414e0909
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565843"
---
# <a name="configure-azure-cosmos-db-account-with-periodic-backup"></a>Konfigurera Azure Cosmos DB konto med periodisk säkerhets kopiering
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB gör automatiskt en säkerhetskopia av dina data med jämna mellanrum. Automatiska säkerhetskopieringar görs utan att det påverkar databasernas prestanda eller tillgänglighet. Alla säkerhets kopior lagras separat i en lagrings tjänst och de säkerhets kopiorna replikeras globalt för återhämtning mot regionala haverier. Med Azure Cosmos DB, inte bara för dina data, utan även säkerhets kopior av dina data är mycket redundanta och elastiska för regional katastrofer. Följande steg visar hur Azure Cosmos DB utför säkerhets kopiering av data:

* Azure Cosmos DB automatiskt tar en fullständig säkerhets kopia av databasen var fjärde timme och när som helst, så lagras bara de senaste två säkerhets kopiorna som standard. Om standard intervallen inte räcker för dina arbets belastningar kan du ändra säkerhets kopierings intervallet och kvarhållningsperioden från Azure Portal. Du kan ändra konfigurationen för säkerhets kopiering under eller efter att Azure Cosmos-kontot har skapats. Om behållaren eller databasen tas bort behåller Azure Cosmos DB befintliga ögonblicks bilder av en specifik behållare eller databas i 30 dagar.

* Azure Cosmos DB lagrar dessa säkerhets kopior i Azure Blob Storage, medan faktiska data finns lokalt inom Azure Cosmos DB.

* För att garantera låg latens lagras ögonblicks bilden av säkerhets kopian i Azure Blob Storage i samma region som den aktuella Skriv regionen (eller **någon** av de skrivskyddade regionerna, om du har en Skriv konfiguration i flera regioner). Som haveriberedskap replikeras alla ögonblicksbilder av säkerhetskopierade data i Azure Blob Storage till en annan region via geo-redundant lagring (GRS). Den region som säkerhetskopian replikeras till baseras på källregionen och det regionala par som källregionen är kopplad till. Mer information finns i [listan över geo-redundanta par av Azure-regioner](../best-practices-availability-paired-regions.md) . Du kan inte komma åt den här säkerhetskopian direkt. Azure Cosmos DB-teamet återställer din säkerhetskopia när du begär det via en supportbegäran.

  Följande bild visar hur en Azure Cosmos-behållare med alla de tre primära fysiska partitionerna i västra USA säkerhets kopie ras i ett fjärran slutet Azure Blob Storage-konto i USA och sedan replikeras till USA, östra:

  :::image type="content" source="./media/configure-periodic-backup-restore/automatic-backup.png" alt-text="Regelbundna fullständiga säkerhets kopieringar av alla Cosmos DB entiteter i GRS Azure Storage." lightbox="./media/configure-periodic-backup-restore/automatic-backup.png" border="false":::

* Säkerhets kopiorna tas utan att påverka programmets prestanda eller tillgänglighet. Azure Cosmos DB utför säkerhets kopiering av data i bakgrunden utan att behöva använda extra allokerat data flöde (ru: er) eller påverka databasens prestanda och tillgänglighet.

## <a name="modify-the-backup-interval-and-retention-period"></a><a id="configure-backup-interval-retention"></a>Ändra intervallet för säkerhets kopiering och kvarhållningsperiod

Azure Cosmos DB automatiskt tar en fullständig säkerhets kopia av dina data varje 4 timme och när som helst, lagras de senaste två säkerhets kopiorna. Den här konfigurationen är standard alternativet och erbjuds utan extra kostnad. Du kan ändra standardintervallet för säkerhetskopiering och kvarhållningsintervallet antingen när du skapar Azure Cosmos-kontot eller när kontot har skapats. Konfigurationen för säkerhetskopiering anges på kontonivå i Azure Cosmos och du måste ange inställningen för varje konto. När du har konfigurerat alternativ för säkerhets kopiering för ett konto tillämpas det på alla behållare i kontot. Du kan för närvarande bara ändra säkerhetskopieringsalternativ via Azure-portalen.

Om du av misstag har tagit bort eller skadat dina data, **innan du skapar en support förfrågan för att återställa data, måste du öka säkerhets kopians kvarhållning för ditt konto till minst sju dagar. Det är bäst att öka din kvarhållning inom 8 timmar efter den här händelsen.** På så sätt har Azure Cosmos DB-teamet tillräckligt med tid för att återställa ditt konto.

Använd följande steg för att ändra standard alternativ för säkerhets kopiering för ett befintligt Azure Cosmos-konto:

1. Logga in på [Azure Portal.](https://portal.azure.com/)
1. Gå till ditt Azure Cosmos-konto och öppna fönstret **säkerhetskopiera & återställning** . Uppdatera säkerhets kopierings intervallet och lagrings perioden för säkerhets kopior som krävs.

   * **Säkerhets kopierings intervall** – det är det intervall med vilket Azure Cosmos DB försöker säkerhetskopiera dina data. Säkerhets kopieringen tar en tids period som inte är noll och i vissa fall kan det potentiellt uppstå problem på grund av underordnade beroenden. Azure Cosmos DB försöker med att göra en säkerhets kopia vid det konfigurerade intervallet, men garanterar inte att säkerhets kopieringen har slutförts inom detta tidsintervall. Du kan konfigurera det här värdet i timmar eller minuter. Intervallet för säkerhets kopiering kan inte vara mindre än 1 timme och större än 24 timmar. När du ändrar intervallet börjar det nya intervallet gälla från och med den tidpunkt då den senaste säkerhets kopieringen utfördes.

   * **Kvarhållning av säkerhets kopior** – den representerar den period där varje säkerhets kopia behålls. Du kan konfigurera den i timmar eller dagar. Den minsta kvarhållningsperioden får inte vara mindre än två gånger intervallet för säkerhets kopiering (i timmar) och får inte vara större än 720 timmar.

   * **Kopior av data som behålls** – som standard erbjuds två säkerhets kopior av dina data utan kostnad. Det finns en extra avgift om du behöver fler än två kopior. Se avsnittet förbrukat lagrings utrymme på [sidan prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/) för att lära dig det exakta priset för extra kopior.

   :::image type="content" source="./media/configure-periodic-backup-restore/configure-backup-interval-retention.png" alt-text="Konfigurera intervall för säkerhets kopiering och kvarhållning för ett befintligt Azure Cosmos-konto." border="true":::

Om du konfigurerar säkerhets kopierings alternativ när kontot skapas kan du konfigurera **säkerhets kopierings principen**, som är antingen **Periodisk** eller **kontinuerlig**. Med den periodiska principen kan du konfigurera intervallet för säkerhets kopiering och kvarhållning av säkerhets kopior. Den kontinuerliga principen är för närvarande endast tillgänglig vid registrering. Azure Cosmos DBs teamet kommer att bedöma din arbets belastning och godkänna din begäran.

:::image type="content" source="./media/configure-periodic-backup-restore/configure-periodic-continuous-backup-policy.png" alt-text="Konfigurera regelbunden eller kontinuerlig säkerhets kopierings princip för nya Azure Cosmos-konton." border="true":::

## <a name="request-data-restore-from-a-backup"></a><a id="request-restore"></a>Begär data återställning från en säkerhets kopia

Om du av misstag tar bort databasen eller en behållare kan du välja [ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) eller [kontakta Azure-supporten](https://azure.microsoft.com/support/options/) för att återställa data från automatiska säkerhets kopieringar online. Support för Azure är tillgängligt för valda planer, till exempel **standard**, **utvecklare** och planer som är högre än de. Azure-supporten är inte tillgänglig med **Basic** -planen. Mer information om olika support avtal finns på sidan [support](https://azure.microsoft.com/support/plans/) avtal för Azure.

För att återställa en speciell ögonblicks bild av säkerhets kopieringen kräver Azure Cosmos DB att data är tillgängliga under säkerhets kopierings cykeln för ögonblicks bilden.
Du bör ha följande information innan du begär en återställning:

* Ha ditt prenumerations-ID klart.

* Utifrån hur dina data har tagits bort eller ändrats av misstag bör du förbereda dig för att ha ytterligare information. Vi rekommenderar att du har den information som är tillgänglig i förväg för att minimera den säkerhets kopiering som kan skadas i vissa tids känsliga fall.

* Om hela Azure Cosmos DBs kontot tas bort måste du ange namnet på det borttagna kontot. Om du skapar ett annat konto med samma namn som det borttagna kontot, kan du dela det med support teamet, eftersom det hjälper till att fastställa rätt konto för att välja. Vi rekommenderar att du skiljer olika support biljetter för varje borttaget konto eftersom det minimerar förvirringen för återställnings läget.

* Om en eller flera databaser tas bort bör du ange Azure Cosmos-kontot och databas namnen för Azure Cosmos och ange om det finns en ny databas med samma namn.

* Om en eller flera behållare tas bort bör du ange konto namnet för Azure-Cosmos, databas namn och behållar namnen. Och ange om det finns en behållare med samma namn.

* Om du av misstag har tagit bort eller skadat dina data bör du kontakta [Azure-supporten](https://azure.microsoft.com/support/options/) inom 8 timmar så att Azure Cosmos DB-teamet kan hjälpa dig att återställa data från säkerhets kopiorna. **Innan du skapar en supportbegäran för att återställa data, se till att [öka säkerhets kopians kvarhållning](#configure-backup-interval-retention) för ditt konto till minst sju dagar. Det är bäst att öka din kvarhållning inom 8 timmar efter den här händelsen.** På så sätt får Azure Cosmos DB support-teamet tillräckligt med tid för att återställa ditt konto.

Förutom namnet på Azure Cosmos-kontot, databas namn, behållar namn, bör du ange den tidpunkt som data kan återställas till. Det är viktigt att vara så exakt som möjligt för att hjälpa oss att fastställa de bästa tillgängliga säkerhets kopiorna. **Det är också viktigt att ange tiden i UTC.**

Följande skärm bild visar hur du skapar en support förfrågan för en behållare (samling/graf/tabell) för att återställa data med hjälp av Azure Portal. Ange andra uppgifter, till exempel typ av data, syftet med återställningen, tid när data togs bort för att hjälpa oss att prioritera begäran.

:::image type="content" source="./media/configure-periodic-backup-restore/backup-support-request-portal.png" alt-text="Skapa en support förfrågan för säkerhets kopiering med Azure Portal." border="true":::

## <a name="considerations-for-restoring-the-data-from-a-backup"></a>Att tänka på när du återställer data från en säkerhets kopia

Du kan oavsiktligt ta bort eller ändra dina data i något av följande scenarier:  

* Ta bort hela Azure Cosmos-kontot.

* Ta bort en eller flera Azure Cosmos-databaser.

* Ta bort en eller flera Azure Cosmos-behållare.

* Ta bort eller ändra Azure Cosmos-objekten (till exempel dokument) i en behållare. Detta specialfall kallas vanligt vis för skadade data.

* En delad erbjudande databas eller behållare i en delad erbjudande databas tas bort eller skadas.

Azure Cosmos DB kan återställa data i alla scenarier ovan. När du återställer skapas ett nytt Azure Cosmos-konto för att lagra återställda data. Namnet på det nya kontot, om det inte har angetts, kommer att ha formatet `<Azure_Cosmos_account_original_name>-restored1` . Den sista siffran ökar stegvis när flera återförsök görs. Du kan inte återställa data till ett redan skapat Azure Cosmos-konto.

När du råkar ta bort ett Azure Cosmos-konto kan vi återställa data till ett nytt konto med samma namn, om konto namnet inte används. Därför rekommenderar vi att du inte skapar kontot igen när du har tagit bort det. Eftersom den inte bara förhindrar att återställda data använder samma namn, men även identifierar rätt konto för att återställa från svårt.

När du tar bort en Azure Cosmos-databas av misstag kan vi återställa hela databasen eller en delmängd av behållarna i databasen. Det går också att välja vissa behållare i databaser och återställa dem till ett nytt Azure Cosmos-konto.

När du oavsiktligt tar bort eller ändrar ett eller flera objekt i en behållare (skadade data) måste du ange hur lång tid som ska återställas. Tiden är viktig om data skadas. Eftersom behållaren är Live körs säkerhets kopieringen fortfarande, så om du väntar bortom kvarhållningsperioden (Standardvärdet är åtta timmar) kommer säkerhets kopiorna att skrivas över. För att förhindra att säkerhets kopian skrivs över ökar du säkerhets kopians kvarhållning för ditt konto till minst sju dagar. Det är bäst att öka din kvarhållning inom 8 timmar från skadade data.

Om du av misstag har tagit bort eller skadat dina data bör du kontakta [Azure-supporten](https://azure.microsoft.com/support/options/) inom 8 timmar så att Azure Cosmos DB-teamet kan hjälpa dig att återställa data från säkerhets kopiorna. På så sätt får Azure Cosmos DB support-teamet tillräckligt med tid för att återställa ditt konto.

> [!NOTE]
> När du har återställt data överförs inte alla käll funktioner eller inställningar till det återställda kontot. Följande inställningar överförs inte till det nya kontot:
> * Åtkomst kontrol listor för VNET
> * Lagrade procedurer, utlösare och användardefinierade funktioner
> * Inställningar för flera regioner  

Om du etablerar data flöde på databas nivå sker säkerhets kopierings-och återställnings processen i det här fallet på hela databas nivån och inte på de enskilda behållar nivåerna. I sådana fall kan du inte välja en delmängd av behållare som ska återställas.

## <a name="required-permissions-to-change-retention-or-restore-from-the-portal"></a>Behörigheter som krävs för att ändra kvarhållning eller återställning från portalen
Huvud konton som ingår i rollen [CosmosdbBackupOperator](../role-based-access-control/built-in-roles.md#cosmosbackupoperator), ägare eller deltagare kan begära en återställning eller ändra kvarhållningsperioden.

## <a name="understanding-costs-of-extra-backups"></a>Förstå kostnader för extra säkerhets kopieringar
Två säkerhets kopior tillhandahålls kostnads fritt och extra säkerhets kopieringar debiteras enligt den regionbaserade prissättningen för säkerhets kopierings lagring som beskrivs i pris nivån för [säkerhets kopierings lagring](https://azure.microsoft.com/en-us/pricing/details/cosmos-db/). Till exempel om kvarhållning av säkerhets kopior har kon figurer ATS till 240 timmar, vilket är 10 dagar och intervallet för säkerhets kopiering till 24 timmar. Detta innebär 10 kopior av säkerhets kopierings data. Om vi antar 1 TB data i USA, västra 2, blir kostnaden 0,12 * 1000 * 8 för lagring av säkerhets kopior under den aktuella månaden. 


## <a name="options-to-manage-your-own-backups"></a>Alternativ för att hantera dina egna säkerhets kopior

Med Azure Cosmos DB SQL API-konton kan du även underhålla dina egna säkerhets kopior genom att använda någon av följande metoder:

* Använd [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) för att flytta data regelbundet till valfri lagrings plats.

* Använd Azure Cosmos DB [ändra feed](change-feed.md) för att läsa data regelbundet för fullständiga säkerhets kopieringar eller för stegvisa ändringar och lagra dem i ditt eget lagrings utrymme.

## <a name="post-restore-actions"></a>Åtgärder efter återställning

Det primära målet för data återställningen är att återställa de data som du av misstag har tagit bort eller ändrat. Vi rekommenderar därför att du först kontrollerar innehållet i de återställda data för att se till att det innehåller det du förväntar dig. Om allting ser bra ut kan du migrera tillbaka data till det primära kontot. Även om det är möjligt att använda det återställda kontot som ditt nya aktiva konto, är det inte ett rekommenderat alternativ om du har produktions arbets belastningar. 

När du har återställt data får du ett meddelande om namnet på det nya kontot (vanligt vis i formatet `<original-name>-restored1` ) och tiden då kontot återställdes till. Det återställda kontot kommer att ha samma allokerade data flöde, indexerings principer och finns i samma region som det ursprungliga kontot. En användare som är prenumerations administratör eller en administratör kan se det återställda kontot.

### <a name="migrate-data-to-the-original-account"></a>Migrera data till det ursprungliga kontot

Följande är olika sätt att migrera data tillbaka till det ursprungliga kontot:

* Använd [verktyget för migrering av Azure Cosmos db data](import-data.md).
* Använd [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).
* Använd [ändra feed](change-feed.md) i Azure Cosmos dB.
* Du kan skriva en egen anpassad kod.

Vi rekommenderar att du tar bort behållaren eller databasen direkt efter att ha migrerat data. Om du inte tar bort de återställda databaserna eller behållarna kommer de att debiteras för enheter för programbegäran, lagring och utgående trafik.

## <a name="next-steps"></a>Nästa steg

* Om du vill göra en återställnings förfrågan kontaktar du Azure-supporten, [File a Ticket från Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Konfigurera och hantera kontinuerlig säkerhets kopiering med [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)eller [Azure Resource Manager](continuous-backup-restore-template.md).
* [Hantera behörigheter](continuous-backup-restore-permissions.md) som krävs för att återställa data med kontinuerligt säkerhets kopierings läge.
