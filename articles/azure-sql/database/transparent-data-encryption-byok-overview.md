---
title: Kundhanterad transparent data kryptering (TDE)
description: Bring Your Own Key (BYOK) stöd för transparent datakryptering (TDE) med Azure Key Vault för SQL Database och Azure Synapse Analytics. TDE med BYOK-översikt, fördelar, hur det fungerar, överväganden och rekommendationer.
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: seo-lt-2019, azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/01/2021
ms.openlocfilehash: e096e21e7d20c992e18634d684f663f149cc3c55
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691254"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Transparent datakryptering i Azure SQL med kundhanterad nyckel
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL [Transparent datakryptering (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) med kundhanterad nyckel möjliggör Bring Your Own Key-scenario (BYOK) för data skydd i vila och gör det möjligt för organisationer att implementera separering av uppgifter i hanteringen av nycklar och data. Med kundhanterad transparent data kryptering är kunden ansvarig för och i en fullständig kontroll över en nyckel livs cykel hantering (nyckel skapande, uppladdning, rotation, borttagning), nyckel användnings behörigheter och granskning av åtgärder på nycklar.

I det här scenariot är den nyckel som används för kryptering av databas krypterings nyckeln (DEK), som kallas TDE-skydd, en kundhanterad asymmetrisk nyckel som lagras i ett kundägda och Kundhanterade [Azure Key Vault (AKV)](../../key-vault/general/secure-your-key-vault.md), ett molnbaserad hanterings system för externa nycklar. Key Vault har hög tillgänglighet och skalbart säkert lagrings utrymme för kryptografiska RSA-nycklar, eventuellt backas upp av FIPS 140-2 nivå 2-validerade maskinvarubaserade säkerhetsmoduler (HSM: er). Den tillåter inte direkt åtkomst till en lagrad nyckel, men tillhandahåller tjänster för kryptering/dekryptering med hjälp av nyckeln till auktoriserade entiteter. Nyckeln kan genereras av nyckel valvet, importeras eller [överföras till nyckel valvet från en lokal HSM-enhet](../../key-vault/keys/hsm-protected-keys.md).

För Azure SQL Database och Azure Synapse Analytics anges TDE-skyddet på server nivå och ärvs av alla krypterade databaser som är kopplade till den servern. För Azure SQL-hanterad instans anges TDE-skydd på instans nivå och ärvs av alla krypterade databaser på den instansen. Termen *Server* refererar både till en server i SQL Database-och Azure-Synapse och till en hanterad instans i SQL-hanterad instans i det här dokumentet, om inget annat anges.

> [!IMPORTANT]
> För de som använder tjänstehanterade TDE som vill börja använda Kundhanterade TDE-data förblir data krypterade under växlings processen och det finns ingen nedtid eller Omkryptering av databasfilerna. Om du växlar från en tjänst-hanterad nyckel till en kundhanterad nyckel krävs bara Omkryptering av DEK, vilket är en snabb och online-åtgärd.

> [!NOTE]
> <a id="doubleencryption"></a> För att ge Azure SQL-kunder två lager av kryptering av data i vila, kommer infrastruktur kryptering (med AES-256 krypteringsalgoritm) med plattforms hanterade nycklar att distribueras. Detta ger en tilläggs nivå av kryptering i vila tillsammans med TDE med Kundhanterade nycklar som redan är tillgänglig. För Azure SQL Database och hanterad instans kommer alla databaser, inklusive huvud databasen och andra system databaser, att krypteras när infrastruktur kryptering aktive ras. För närvarande måste kunderna begära åtkomst till den här funktionen. Om du är intresse rad av den här funktionen kan du kontakta AzureSQLDoubleEncryptionAtRest@service.microsoft.com .

## <a name="benefits-of-the-customer-managed-tde"></a>Fördelar med kund hanterade TDE

Kundhanterade TDE ger kunden följande fördelar:

- Fullständig och detaljerad kontroll över användning och hantering av TDE-skyddskomponenten;

- Transparens för användningen av TDE-skydd;

- Möjlighet att införa separering av uppgifter i hanteringen av nycklar och data inom organisationen.

- Key Vault administratör kan återkalla nyckel åtkomst behörigheter för att göra den krypterade databasen oåtkomlig.

- Central hantering av nycklar i AKV;

- Bättre förtroende från dina slut kunder, eftersom AKV är utformat så att Microsoft inte kan se eller extrahera krypterings nycklar.

## <a name="how-customer-managed-tde-works"></a>Så här fungerar Kundhanterade TDE

![Installation och funktion av Kundhanterade TDE](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-roles.PNG)

För att servern ska kunna använda TDE-skyddskomponenten som lagras i AKV för kryptering av DEK måste nyckel valv administratören ge följande åtkomst behörighet till servern med hjälp av dess unika Azure Active Directory (Azure AD)-identitet:

- **Hämta** för att hämta den offentliga delen och egenskaperna för nyckeln i Key Vault

- **wrapKey** -för att kunna skydda (kryptera) DEK

- **unwrapKey** -för att kunna ta bort skyddet (DEKRYPTERA) DEK

Key Vault-administratören kan också [Aktivera loggning av nyckel valv gransknings händelser](../../azure-monitor/insights/key-vault-insights-overview.md), så att de kan granskas senare.

När servern har kon figurer ATS för att använda ett TDE-skydd från AKV skickar servern DEK för varje TDE-aktiverad databas till nyckel valvet för kryptering. Key Vault returnerar den krypterade DEK, som sedan lagras i användar databasen.

Vid behov skickar servern skyddade DEK till nyckel valvet för dekryptering.

Granskare kan använda Azure Monitor för att granska Key Vault-AuditEvent loggar om loggning är aktiverat.

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>Krav för att konfigurera Kundhanterade TDE

### <a name="requirements-for-configuring-akv"></a>Krav för att konfigurera AKV

- Key Vault och SQL Database/Managed instance måste tillhöra samma Azure Active Directory-klient. Nyckel valv för flera klienter och Server interaktioner stöds inte. Om du vill flytta resurserna efteråt måste TDE med AKV konfigureras om. Lär dig mer om att [Flytta resurser](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

- Funktionen för [mjuk borttagning](../../key-vault/general/soft-delete-overview.md) måste vara aktive rad i nyckel valvet för att det ska gå att ta bort data förlust, oavsiktlig nyckel (eller Key Vault). Mjuka, borttagna resurser behålls i 90 dagar, såvida de inte återställs eller rensas av kunden under tiden. Åtgärder för att *återställa* och *Rensa* har sina egna behörigheter som är kopplade till en åtkomst princip för nyckel valv. Funktionen mjuk borttagning är inaktive rad som standard och kan aktive ras via [PowerShell](../../key-vault/general/key-vault-recovery.md?tabs=azure-powershell) eller [CLI](../../key-vault/general/key-vault-recovery.md?tabs=azure-cli). Den kan inte aktive ras via Azure Portal.  

- Bevilja servern eller hanterad instans åtkomst till nyckel valvet (get, wrapKey, unwrapKey) med sin Azure Active Directory identitet. När du använder Azure Portal skapas Azure AD-identiteten automatiskt. När du använder PowerShell eller CLI måste Azure AD-identiteten skapas och slutföras måste verifieras. Se [Konfigurera TDE med BYOK](transparent-data-encryption-byok-configure.md) och [Konfigurera TDE med BYOK för SQL-hanterad instans](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md) för detaljerade steg-för-steg-instruktioner när du använder PowerShell.

- När du använder en brand vägg med AKV måste du aktivera alternativet *Tillåt att betrodda Microsoft-tjänster kringgår brand väggen*.

### <a name="requirements-for-configuring-tde-protector"></a>Krav för att konfigurera TDE-skydd

- TDE-skydd kan bara vara asymmetrisk, RSA eller RSA HSM-nyckel. De nyckel längder som stöds är 2048 och 3072 byte.

- Aktiverings datumet (om det är inställt) måste vara datum och tid tidigare. Utgångs datum (om det anges) måste vara ett framtida datum och en framtida tidpunkt.

- Nyckeln måste vara i *aktiverat* läge.

- Om du importerar en befintlig nyckel till nyckel valvet ska du se till att tillhandahålla den i de fil format som stöds (. pfx,. BYOK eller. säkerhets kopiering).

> [!NOTE]
> Azure SQL stöder nu användning av en RSA-nyckel som lagras i en hanterad HSM som TDE-skydd. Den här funktionen finns i **offentlig för hands version**. Azure Key Vault hanterad HSM är en fullständigt hanterad moln tjänst med hög tillgänglighet, en standard som är kompatibel med en enda klient, som gör att du kan skydda kryptografiska nycklar för dina moln program med hjälp av FIPS 140-2 nivå 3-verifierade HSM: er. Läs mer om [hanterade HSM: er](../../key-vault/managed-hsm/index.yml).


## <a name="recommendations-when-configuring-customer-managed-tde"></a>Rekommendationer vid konfigurering av Kundhanterade TDE

### <a name="recommendations-when-configuring-akv"></a>Rekommendationer när du konfigurerar AKV

- Associera högst 500 Generell användning eller 200 Affärskritisk-databaser totalt med ett nyckel valv i en enda prenumeration för att säkerställa hög tillgänglighet när servern använder TDE-skydd i nyckel valvet. Dessa siffror baseras på upplevelsen och dokumenterat i [nyckel valvs tjänstens gränser](../../key-vault/general/service-limits.md). Avsikten med detta är att förhindra problem efter serverns redundans, eftersom det utlöses som många viktiga åtgärder mot valvet eftersom det finns databaser på den servern.

- Ange ett resurs lås i nyckel valvet för att kontrol lera vem som kan ta bort den här kritiska resursen och förhindra oavsiktlig eller obehörig borttagning. Läs mer om [resurs lås](../../azure-resource-manager/management/lock-resources.md).

- Aktivera granskning och rapportering på alla krypterings nycklar: Key Vault innehåller loggar som är lätta att mata in i andra säkerhets informations-och händelse hanterings verktyg. Operations Management Suite [Log Analytics](../../azure-monitor/insights/key-vault-insights-overview.md) är ett exempel på en tjänst som redan är integrerad.

- Länka varje server med två nyckel valv som finns i olika regioner och håll samma viktiga material för att säkerställa hög tillgänglighet för krypterade databaser. Markera bara nyckeln från nyckel valvet i samma region som ett TDE-skydd. Systemet växlar automatiskt till nyckel valvet i den fjärranslutna regionen om det uppstår ett avbrott som påverkar nyckel valvet i samma region.

### <a name="recommendations-when-configuring-tde-protector"></a>Rekommendationer när du konfigurerar TDE-skydd

- Behåll en kopia av TDE-skydd på en säker plats eller depositions det till depositions-tjänsten.

- Om nyckeln genereras i nyckel valvet skapar du en nyckel säkerhets kopia innan du använder nyckeln i AKV för första gången. Säkerhets kopieringen kan bara återställas till en Azure Key Vault. Läs mer om kommandot [Backup-AzKeyVaultKey](/powershell/module/az.keyvault/backup-azkeyvaultkey) .

- Skapa en ny säkerhets kopia när ändringar görs i nyckeln (t. ex. nyckelattribut, taggar, ACL: er).

- **Behåll tidigare versioner** av nyckeln i nyckel valvet när du roterar nycklar, så att äldre databas säkerhets kopior kan återställas. När TDE-skyddet ändras för en databas, uppdateras gamla säkerhets kopior av databasen **inte** för att använda det senaste TDE-skyddet. Vid återställnings tillfället behöver varje säkerhets kopiering TDE-skyddskomponenten som den krypterades med när den skapades. Nyckel rotationer kan utföras genom att följa anvisningarna i [rotera Transparent datakryptering-skydd med hjälp av PowerShell](transparent-data-encryption-byok-key-rotation.md).

- Behåll alla tidigare använda nycklar i AKV även efter att ha växlat till tjänstens hanterade nycklar. Det garanterar att säkerhets kopior av databaser kan återställas med TDE-skydd som lagras i AKV.  TDE-skydd som skapats med Azure Key Vault måste underhållas tills alla återstående lagrade säkerhets kopior har skapats med tjänst hanterade nycklar. Gör återställnings bara säkerhets kopior av dessa nycklar med hjälp av [Backup-AzKeyVaultKey](/powershell/module/az.keyvault/backup-azkeyvaultkey).

- Om du vill ta bort en potentiellt komprometterad nyckel under en säkerhets incident utan risken för data förlust följer du stegen i [ta bort en potentiellt komprometterad nyckel](transparent-data-encryption-byok-remove-tde-protector.md).

## <a name="inaccessible-tde-protector"></a>Oåtkomligt TDE-skydd

När transparent data kryptering har kon figurer ATS för att använda en kundhanterad nyckel, krävs kontinuerlig åtkomst till TDE-skydd för att databasen ska vara online. Om servern förlorar åtkomsten till det Kundhanterade TDE-skyddskomponenten i AKV, på upp till 10 minuter kommer en databas att starta neka alla anslutningar med motsvarande fel meddelande och ändra dess tillstånd till *otillgängligt*. Den enda åtgärden som tillåts för en databas i det otillgängliga läget tar bort den.

> [!NOTE]
> Om databasen inte är tillgänglig på grund av ett tillfälligt nätverks avbrott, krävs ingen åtgärd och databaserna kommer att anslutas igen automatiskt.

När åtkomst till nyckeln har återställts krävs ytterligare tid och steg, vilket kan variera beroende på hur lång tid som förflutit utan åtkomst till nyckeln och storleken på data i databasen:

- Om nyckel åtkomsten återställs inom 8 timmar kommer databasen automatiskt att korrigeras inom nästa timma.

- Om nyckelåtkomsten återställs efter mer än åtta timmar går det inte att reparera automatiskt. Det kan då krävas ytterligare steg i portalen för att få databasen online igen, vilket kan ta lång tid för stora databaser. När databasen är online igen, har tidigare konfigurerade inställningar på server nivå, till exempel konfiguration av [redundanskonfiguration](auto-failover-group-overview.md) , punkt-i-tid-återställnings historik och taggar går **förlorade**. Därför rekommenderar vi att du implementerar ett meddelande system som gör det möjligt att identifiera och åtgärda de underliggande åtkomst problemen för nycklar inom 8 timmar.

Nedan visas de ytterligare steg som krävs på portalen för att den oåtkomliga databasen ska bli online igen.

![TDE BYOK-databas](./media/transparent-data-encryption-byok-overview/customer-managed-tde-inaccessible-database.jpg)


### <a name="accidental-tde-protector-access-revocation"></a>Oavsiktlig åtkomst till återkallning av TDE-skydd

Det kan hända att någon med tillräckliga åtkomst rättigheter till Key Vault oavsiktligt inaktiverar Server åtkomst till nyckeln av:

- återkalla nyckel valvets *Get*-, *wrapKey*-, *unwrapKey* -behörigheter från servern

- tar bort nyckeln

- tar bort nyckel valvet

- ändra nyckel valvets brand Väggs regler

- tar bort den hanterade identiteten för servern i Azure Active Directory

Lär dig mer om [de vanligaste orsakerna till att databasen blir oåtkomlig](/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current&preserve-view=true#common-errors-causing-databases-to-become-inaccessible).

## <a name="monitoring-of-the-customer-managed-tde"></a>Övervakning av Kundhanterade TDE

Konfigurera följande Azure-funktioner för att övervaka databas tillstånd och aktivera avisering för förlust av TDE-skydds åtkomst:

- [Azure Resource Health](../../service-health/resource-health-overview.md). En oåtkomlig databas som har förlorat åtkomst till TDE-skyddet visas som "ej tillgänglig" efter att den första anslutningen till databasen har nekats.
- [Aktivitets logg](../../service-health/alerts-activity-log-service-notifications-portal.md) när åtkomst till TDE-skydd i det Kundhanterade nyckel valvet Miss lyckas, läggs poster till i aktivitets loggen.  Genom att skapa aviseringar för dessa händelser kan du återställa åtkomst så snart som möjligt.
- [Åtgärds grupper](../../azure-monitor/alerts/action-groups.md) kan definieras för att skicka aviseringar och aviseringar baserat på dina inställningar, t. ex. e-post/SMS/push/röst, Logic app, webhook, ITSM eller Automation Runbook.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Säkerhets kopiering och återställning av databasen med Kundhanterade TDE

När en databas har krypterats med TDE med hjälp av en nyckel från Key Vault krypteras även eventuella nyligen genererade säkerhets kopior med samma TDE-skydd. När TDE-skyddet ändras, **uppdateras inte** gamla säkerhets kopior av databasen för att använda det senaste TDE-skyddet.

För att återställa en säkerhets kopia krypterad med ett TDE-skydd från Key Vault, se till att nyckel materialet är tillgängligt för mål servern. Därför rekommenderar vi att du behåller alla gamla versioner av TDE-skyddskomponenten i Key Vault, så att databas säkerhets kopior kan återställas.

> [!IMPORTANT]
> Det kan aldrig finnas fler än en TDE-skydds uppsättning för en server. Det är nyckeln markerat med "gör nyckeln till standard TDE skydd" på bladet Azure Portal. Flera ytterligare nycklar kan dock länkas till en server utan att markera dem som TDE skydd. Dessa nycklar används inte för att skydda DEK, men kan användas vid återställning från en säkerhets kopia, om säkerhets kopierings filen krypteras med nyckeln med motsvarande tumavtryck.

Om nyckeln som behövs för att återställa en säkerhets kopia inte längre är tillgänglig för mål servern, returneras följande fel meddelande på återställnings försöket: mål servern `<Servername>` har inte åtkomst till alla AKV-URI: er som skapats mellan \<Timestamp #1> och \<Timestamp #2> . Försök igen när du har återställt alla AKV-URI: er. "

För att minimera den, kör cmdleten [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) för mål servern eller [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) för den hanterade mål instansen för att returnera listan över tillgängliga nycklar och identifiera de som saknas. Se till att mål servern för återställningen har åtkomst till alla nycklar som behövs för att säkerställa att alla säkerhets kopior kan återställas. Dessa nycklar behöver inte markeras som TDE-skydd.

Mer information om säkerhets kopierings återställning för SQL Database finns [i återställa en databas i SQL Database](recovery-using-backups.md). Mer information om säkerhets kopierings återställning för dedikerad SQL-pool i Azure Synapse Analytics finns i [återställa en dedikerad SQL-pool](../../synapse-analytics/sql-data-warehouse/backup-and-restore.md). För SQL Server interna säkerhets kopiering/återställning med SQL-hanterad instans, se [snabb start: återställa en databas till SQL-hanterad instans](../managed-instance/restore-sample-database-quickstart.md)

Ytterligare överväganden för loggfiler: säkerhetskopierade loggfiler förblir krypterade med det ursprungliga TDE-skyddet, även om det roterats och databasen nu använder ett nytt TDE-skydd.  Vid återställnings tiden krävs båda nycklarna för att återställa databasen.  Om logg filen använder ett TDE-skydd som lagras i Azure Key Vault, krävs den här nyckeln vid återställnings tiden, även om databasen har ändrats för att använda tjänstehanterade TDE under tiden.

## <a name="high-availability-with-customer-managed-tde"></a>Hög tillgänglighet med Kundhanterade TDE

Även om det inte finns någon konfigurerad GEO-redundans för Server, rekommenderar vi starkt att du konfigurerar servern att använda två olika nyckel valv i två olika regioner med samma nyckel material. Nyckeln i det sekundära nyckel valvet i den andra regionen ska inte markeras som TDE-skydd och det är inte ens tillåtet. Om det uppstår ett avbrott som påverkar primär nyckel valvet kommer systemet automatiskt att växla till den andra länkade nyckeln med samma tumavtryck i det sekundära nyckel valvet, om det finns. Observera att växeln inte sker om TDE-skyddskomponenten inte kan nås på grund av återkallade åtkomst rättigheter, eller att nyckel-eller nyckel valvet har tagits bort, eftersom det kan indikera att kunden avsiktligt ville begränsa servern från att komma åt nyckeln. Att tillhandahålla samma nyckel material till två nyckel valv i olika regioner kan göras genom att skapa nyckeln utanför nyckel valvet och importera dem till båda nyckel valven. 

Du kan också åstadkomma detta genom att generera nyckeln med hjälp av primär nyckel valvet som finns i samma region som servern och klona nyckeln till ett nyckel valv i en annan Azure-region. Använd cmdleten [Backup-AzKeyVaultKey](/powershell/module/az.keyvault/Backup-AzKeyVaultKey) för att hämta nyckeln i krypterat format från primär nyckel valvet och sedan använda cmdleten [restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) och ange ett nyckel valv i den andra regionen för att klona nyckeln. Du kan också använda Azure Portal för att säkerhetskopiera och återställa nyckeln. Nyckel säkerhets kopiering/återställning tillåts bara mellan nyckel valv i samma Azure-prenumeration och [Azure geografi](https://azure.microsoft.com/global-infrastructure/geographies/).  

![Single-Server HA](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>Geo-DR och Kundhanterade TDE

Vid både [aktiva scenarier för geo-replikering](active-geo-replication-overview.md) och [failover-grupper](auto-failover-group-overview.md) kräver varje server som krävs ett separat nyckel valv, som måste samplaceras med servern i samma Azure-region. Kunden ansvarar för att hålla nyckel materialet över nyckel valven konsekvent, så att geo-Secondary är synkroniserat och kan ta över samma nyckel från det lokala nyckel valvet om primärt blir otillgängligt på grund av ett avbrott i regionen och redundansväxlingen utlöses. Upp till fyra sekundära servrar kan konfigureras och länkning (sekundär nyckel zoner) stöds inte.

För att undvika problem vid etablering eller under geo-replikering på grund av ofullständigt nyckel material, är det viktigt att följa dessa regler när du konfigurerar Kundhanterade TDE:

- Alla nyckel valv som ingår måste ha samma egenskaper och samma åtkomst rättigheter för respektive server.

- Alla nyckel valv som ingår måste innehålla identiska nyckel material. Den gäller inte bara för det aktuella TDE-skyddet, utan till alla tidigare TDE-skydd som kan användas i säkerhetskopieringsfilerna.

- Både inledande installation och rotation av TDE-skyddskomponenten måste utföras på den sekundära först och sedan på primär.

![Redundansväxla grupper och geo-Dr](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-bcdr.png)

Om du vill testa en redundansväxling följer du stegen i [Översikt över aktiv geo-replikering](active-geo-replication-overview.md). Testning av redundans bör göras regelbundet för att verifiera att SQL Database har bevarat åtkomst behörighet till båda nyckel valven.

## <a name="next-steps"></a>Nästa steg

Du kanske också vill kontrol lera följande PowerShell-exempel skript för vanliga åtgärder med Kundhanterade TDE:

- [Rotera transparent datakryptering-skyddet för SQL Database med hjälp av PowerShell](transparent-data-encryption-byok-key-rotation.md)

- [Ta bort ett transparent datakryptering (TDE) skydd för SQL Database med hjälp av PowerShell](transparent-data-encryption-byok-remove-tde-protector.md)

- [Hantera transparent datakryptering i SQL-hanterad instans med din egen nyckel med hjälp av PowerShell](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)