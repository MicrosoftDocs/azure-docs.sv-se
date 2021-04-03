---
title: Databas säkerhet – Azure Cosmos DB
description: Lär dig hur Azure Cosmos DB tillhandahåller databas skydd och data säkerhet för dina data.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: mjbrown
ms.openlocfilehash: 19b4c8466e88159839ce1f43a5ba282b1bb3ec9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94636936"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Säkerhet i Azure Cosmos DB – översikt
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

I den här artikeln beskrivs metodtips för databassäkerhet och viktiga funktioner som erbjuds av Azure Cosmos DB som hjälper dig att förhindra, identifiera och reagera på databasintrång.

## <a name="whats-new-in-azure-cosmos-db-security"></a>Vad är nytt i Azure Cosmos DB säkerhet

Kryptering i vila är nu tillgängligt för dokument och säkerhets kopior som lagras i Azure Cosmos DB i alla Azure-regioner. Kryptering i vila tillämpas automatiskt för både nya och befintliga kunder i dessa regioner. Du behöver inte konfigurera något. och du får samma svars tid, data flöde, tillgänglighet och funktionalitet som innan fördelarna med att veta hur dina data är säkra och är säkra med kryptering i vila.

## <a name="how-do-i-secure-my-database"></a>Hur gör jag för att skydda min databas

Data säkerhet är ett delat ansvar mellan dig, kunden och din databas leverantör. Beroende på vilken databas leverantör du väljer kan du variera mängden ansvar som du bär. Om du väljer en lokal lösning måste du ge allt från slut punkts skydd till fysisk säkerhet för din maskin vara, vilket inte är enkelt. Om du väljer en moln databas leverantör för PaaS, till exempel Azure Cosmos DB, minskar ditt eget intresse avsevärt. Följande bild, som lånas från Microsofts [delade ansvar för molnbaserad data behandling](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91) White Paper, visar hur ditt ansvar minskar med en PaaS-provider som Azure Cosmos dB.

:::image type="content" source="./media/database-security/nosql-database-security-responsibilities.png" alt-text="Kund-och databas leverantörs ansvar":::

Föregående diagram visar moln säkerhets komponenter på hög nivå, men vilka objekt behöver du bekymra dig om specifikt för din databas lösning? Och hur kan du jämföra lösningar med varandra?

Vi rekommenderar följande check lista över krav för att jämföra databas system:

- Inställningar för nätverks säkerhet och brand vägg
- Användarautentisering och detaljerade användar kontroller
- Möjlighet att replikera data globalt för regionala haverier
- Möjlighet att redundansväxla ett Data Center till ett annat
- Lokal datareplikering i ett Data Center
- Automatisk data säkerhets kopiering
- Återställning av borttagna data från säkerhets kopior
- Skydda och isolera känsliga data
- Övervakning av attacker
- Svara på attacker
- Möjlighet att använda geo-stängsel för att följa data styrnings begränsningar
- Fysiskt skydd av servrar i skyddade data Center
- Certifieringar

Och även om det kan verka självklart kan den senaste [storskaliga databasen](https://thehackernews.com/2017/01/mongodb-database-security.html) påminna oss om den enkla men viktiga betydelsen av följande krav:

- Korrigerade servrar som hålls aktuella
- HTTPS som standard/TLS-kryptering
- Administrativa konton med starka lösen ord

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Hur skyddar Azure Cosmos DB databasen

Nu ska vi gå tillbaka i föregående lista – hur många av dessa säkerhets krav Azure Cosmos DB tillhandahålla? Var och en.

Låt oss titta närmare på var och en.

|Säkerhets krav|Azure Cosmos DB säkerhets metod|
|---|---|
|Nätverkssäkerhet|Att använda en IP-brandvägg är det första skydds lagret som skyddar din databas. Azure Cosmos DB stöder princip drivna IP-baserade åtkomst kontroller för inkommande brand Väggs stöd. IP-baserade åtkomst kontroller liknar de brand Väggs regler som används av traditionella databas system, men de expanderas så att ett Azure Cosmos Database-konto endast är tillgängligt från en godkänd uppsättning datorer eller moln tjänster. Läs mer i artikeln om [stöd för Azure Cosmos DB-brandvägg](how-to-configure-firewall.md) .<br><br>Med Azure Cosmos DB kan du aktivera en speciell IP-adress (168.61.48.0), ett IP-intervall (168.61.48.0/8) och kombinationer av IP-adresser och intervall. <br><br>Alla begär Anden som kommer från datorer utanför den här tillåtna listan blockeras av Azure Cosmos DB. Begär Anden från godkända datorer och moln tjänster måste sedan slutföra autentiseringsprocessen för att få åtkomst kontroll till resurserna.<br><br> Du kan använda [taggar för virtuella nätverks tjänster](../virtual-network/service-tags-overview.md) för att uppnå nätverks isolering och skydda dina Azure Cosmos DB resurser från det allmänna Internet. Använd tjänst Taggar i stället för vissa IP-adresser när du skapar säkerhets regler. Genom att ange service tag-namnet (till exempel AzureCosmosDB) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst.|
|Auktorisering|Azure Cosmos DB använder hash-baserad meddelande authentication code (HMAC) för auktorisering. <br><br>Varje begäran hashas med den hemliga konto nyckeln och den efterföljande bas-64-kodade hashen skickas med varje anrop till Azure Cosmos DB. För att verifiera begäran använder tjänsten Azure Cosmos DB rätt hemliga nyckel och egenskaper för att generera en hash. Därefter jämförs värdet med det som finns i begäran. Om de två värdena matchar, godkänns åtgärden korrekt och begäran bearbetas, annars uppstår ett auktoriseringsfel och begäran avvisas.<br><br>Du kan antingen använda en [primär nyckel](#primary-keys)eller en [resurs-token](secure-access-to-data.md#resource-tokens) som ger detaljerad åtkomst till en resurs, till exempel ett dokument.<br><br>Läs mer om hur [du skyddar åtkomsten till Azure Cosmos DB resurser](secure-access-to-data.md).|
|Användare och behörigheter|Med hjälp av den primära nyckeln för kontot kan du skapa användar resurser och behörighets resurser per databas. En Resource-token är associerad med en behörighet i en databas och avgör om användaren har åtkomst (Read-Write, skrivskyddad eller ingen åtkomst) till en program resurs i databasen. Program resurser omfattar behållare, dokument, bilagor, lagrade procedurer, utlösare och UDF: er. Resurs-token används sedan vid autentisering för att ge eller neka åtkomst till resursen.<br><br>Läs mer om hur [du skyddar åtkomsten till Azure Cosmos DB resurser](secure-access-to-data.md).|
|Active Directory-integrering (Azure RBAC)| Du kan också ange eller begränsa åtkomsten till Cosmos-kontot, databasen, behållaren och erbjudandena (genom strömning) med åtkomst kontroll (IAM) i Azure Portal. IAM tillhandahåller rollbaserad åtkomst kontroll och kan integreras med Active Directory. Du kan använda inbyggda roller eller anpassade roller för enskilda användare och grupper. Mer information finns i [Active Directory integrations](role-based-access-control.md) artikeln.|
|Global replikering|Azure Cosmos DB erbjuder nyckel färdig global distribution, vilket gör att du kan replikera dina data till någon av Azures världs omfattande data Center med ett klick på en knapp. Med global replikering kan du skala globalt och ge låg latens åtkomst till dina data runtom i världen.<br><br>I säkerhets kontexten säkerställer global replikering data skydd mot regionala haverier.<br><br>Läs mer i [Distribuera data globalt](distribute-data-globally.md).|
|Regionala redundanstestningar|Om du har replikerat dina data i mer än ett Data Center, kan Azure Cosmos DB automatiskt gå över dina åtgärder om ett regionalt Data Center är offline. Du kan skapa en prioriterad lista över växlings regioner med hjälp av de regioner där dina data replikeras. <br><br>Läs mer i [regional redundans i Azure Cosmos DB](high-availability.md).|
|Lokal replikering|I ett enda data Center replikerar Azure Cosmos DB automatiskt data för hög tillgänglighet och ger dig möjlighet att välja [konsekvens nivåer](consistency-levels.md). Den här replikeringen garanterar ett [service avtal](https://azure.microsoft.com/support/legal/sla/cosmos-db) på 99,99% tillgänglighet för alla enkla region konton och alla konton med flera regioner med avslappnad konsekvens och 99,999% Läs tillgänglighet för alla databas konton i flera regioner.|
|Automatiserade säkerhets kopieringar online|Azure Cosmos-databaser säkerhets kopie ras regelbundet och lagras i en Geo-redundant lagring. <br><br>Läs mer i [Automatisk säkerhets kopiering och återställning online med Azure Cosmos DB](online-backup-and-restore.md).|
|Återställ borttagna data|De automatiserade säkerhets kopieringarna kan användas för att återställa data som du kan ha tagit bort av misstag, upp till ~ 30 dagar efter händelsen. <br><br>Läs mer i [Automatisk säkerhets kopiering och återställning online med Azure Cosmos DB](online-backup-and-restore.md)|
|Skydda och isolera känsliga data|Alla data i de regioner som anges i vad är nytt? är nu krypterad i vila.<br><br>Personliga data och andra konfidentiella data kan isoleras till en viss behållare och skriv-eller Läs behörighet kan begränsas till vissa användare.|
|Övervaka för attacker|Genom att använda [gransknings loggning och aktivitets loggar](./monitor-cosmos-db.md)kan du övervaka ditt konto för normal och onormal aktivitet. Du kan se vilka åtgärder som utfördes på resurserna, som initierade åtgärden, när åtgärden utfördes, status för åtgärden och mycket mer som visas på skärm bilden efter den här tabellen.|
|Svara på attacker|När du har kontaktat Azure-supporten för att rapportera en potentiell attack, startas en process med 5 stegs incident svar. Målet med en 5-stegs process är att återställa normala tjänst säkerhet och-åtgärder så snabbt som möjligt när ett problem har upptäckts och en undersökning har startats.<br><br>Läs mer i [Microsoft Azure säkerhets svar i molnet](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).|
|Geo-staket|Azure Cosmos DB garanterar data styrning för suveräna regioner (till exempel Tyskland, Kina, US Gov).|
|Skyddade anläggningar|Data i Azure Cosmos DB lagras på SSD i Azures skyddade data Center.<br><br>Läs mer i [Microsofts globala data Center](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|HTTPS/SSL/TLS-kryptering|Alla anslutningar till Azure Cosmos DB stöd för HTTPS. Azure Cosmos DB stöder även TLS 1,2.<br>Det är möjligt att framtvinga en lägsta TLS-version på Server sidan. Om du vill göra det kontaktar du [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com) .|
|Kryptering i vila|Alla data som lagras i Azure Cosmos DB krypteras i vila. Läs mer i [Azure Cosmos DB kryptering i vila](./database-encryption-at-rest.md)|
|Korrigerade servrar|Som en hanterad databas eliminerar Azure Cosmos DB behovet av att hantera och korrigera servrar, som du gör automatiskt.|
|Administrativa konton med starka lösen ord|Det är svårt att tro att vi ens behöver nämna detta krav, men till skillnad från några av våra konkurrenter är det omöjligt att ha ett administrativt konto utan lösen ord i Azure Cosmos DB.<br><br> Säkerhet via TLS och HMAC-baserad autentisering är bakade i som standard.|
|Säkerhets-och data skydds certifieringar| Den senaste listan över certifieringar finns i den övergripande [Azure Compliance-webbplatsen](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) samt det senaste [Azure Compliance-dokumentet](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) med alla certifieringar (Sök efter Cosmos). För en mer fokuserad läsning kolla den 25 april 2018 post [Azure #CosmosDB: säker, privat, kompatibel med SOCS 1/2 typ 2, HITRUST, PCI DSS nivå 1, ISO 27001, HIPAA, FedRAMP hög och många andra.

Följande skärm bild visar hur du kan använda gransknings loggning och aktivitets loggar för att övervaka ditt konto: :::image type="content" source="./media/database-security/nosql-database-security-application-logging.png" alt-text="aktivitets loggar för Azure Cosmos DB":::

<a id="primary-keys"></a>

## <a name="primary-keys"></a>Primära nycklar

Primära nycklar ger åtkomst till alla administrativa resurser för databas kontot. Primär nycklar:

- Ge åtkomst till konton, databaser, användare och behörigheter. 
- Kan inte användas för att ge detaljerad åtkomst till behållare och dokument.
- Skapas när ett konto skapas.
- Kan återskapas när som helst.

Varje konto består av två primär nycklar: en primär nyckel och en sekundär nyckel. Syftet med dubbla nycklar är att du kan skapa om eller registrera nycklar, vilket ger kontinuerlig åtkomst till ditt konto och dina data.

Förutom de två primära nycklarna för det Cosmos DB kontot finns det två skrivskyddade nycklar. Dessa skrivskyddade nycklar tillåter bara Läs åtgärder på kontot. Skrivskyddade nycklar ger inte åtkomst till Läs behörighets resurser.

Primära, sekundära, skrivskyddade och Läs-och skriv-och skrivbara primär nycklar kan hämtas och återskapas med hjälp av Azure Portal. Instruktioner finns i [Visa, kopiera och återskapa åtkomst nycklar](manage-with-cli.md#regenerate-account-key).

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Åtkomst kontroll (IAM) i Azure Portal – demonstrera NoSQL Database-säkerhet":::

## <a name="next-steps"></a>Nästa steg

Mer information om primära nycklar och resurs-token finns i [Skydda åtkomsten till Azure Cosmos db data](secure-access-to-data.md).

Mer information om gransknings loggning finns i [Azure Cosmos DB diagnostisk loggning](./monitor-cosmos-db.md).

Mer information om Microsoft-certifieringar finns [Azure Säkerhetscenter](https://azure.microsoft.com/support/trust-center/).