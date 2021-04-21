---
title: Kund hanterad transparent datakryptering (TDE)
description: Bring Your Own Key (BYOK) för transparent datakryptering (TDE) med Azure Key Vault för SQL Database och Azure Synapse Analytics. TDE med BYOK-översikt, fördelar, hur det fungerar, överväganden och rekommendationer.
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: seo-lt-2019, azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 02/01/2021
ms.openlocfilehash: b3403558cbc07d152bbae7e901464a8aa4a8e4d2
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812776"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Transparent datakryptering i Azure SQL med kundhanterad nyckel
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL transparent datakryptering [(TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) med kundhanteringsnyckel möjliggör Bring Your Own Key-scenario (BYOK) för dataskydd i vila och gör att organisationer kan implementera uppdelning av uppgifter i hanteringen av nycklar och data. Med kundstyrd transparent datakryptering ansvarar kunden för och har fullständig kontroll över en nyckellivscykelhantering (nyckelskapande, uppladdning, rotation, borttagning), behörigheter för nyckelanvändning och granskning av åtgärder på nycklar.

I det här scenariot är nyckeln som används för kryptering av databaskrypteringsnyckeln (DEK), som kallas TDE-skydd, en kundbaserad asymmetrisk nyckel som lagras i ett kundägt och kundägt [Azure Key Vault (AKV),](../../key-vault/general/security-features.md)ett molnbaserat externt nyckelhanteringssystem. Key Vault har hög tillgänglig och skalbar säker lagring för RSA-kryptografiska nycklar, eventuellt backat av FIPS 140-2 Level 2-verifierade maskinvarusäkerhetsmoduler (HSM). Den tillåter inte direkt åtkomst till en lagrad nyckel, men tillhandahåller tjänster för kryptering/dekryptering med nyckeln till behöriga entiteter. Nyckeln kan genereras av nyckelvalvet, importeras eller överföras till nyckelvalvet från en [on-prem HSM-enhet](../../key-vault/keys/hsm-protected-keys.md).

För Azure SQL Database och Azure Synapse Analytics anges TDE-skyddet på servernivå och ärvs av alla krypterade databaser som är associerade med den servern. Till Azure SQL Managed Instance TDE-skyddet anges på instansnivå och ärvs av alla krypterade databaser på den instansen. Termen server *refererar* både till en server i SQL Database och Azure Synapse till en hanterad instans i SQL Managed Instance det här dokumentet, om inget annat anges.

> [!IMPORTANT]
> För dem som använder tjänst-hanterad TDE som vill börja använda kund-hanterad TDE förblir data krypterade under växlingsprocessen och det finns ingen avbrottstid eller omkryptering av databasfilerna. Växling från en tjänst hanterad nyckel till en kund hanterad nyckel kräver bara omkryptering av DEK, vilket är en snabb och online-åtgärd.

> [!NOTE]
> <a id="doubleencryption"></a> För att Azure SQL kunder två lager med kryptering av vilodata distribueras infrastrukturkryptering (med AES-256-krypteringsalgoritmen) med plattforms hanterade nycklar. Detta ger ett extra lager av kryptering i vila tillsammans med TDE med kund-hanterade nycklar, som redan är tillgängliga. För Azure SQL Database och hanterad instans krypteras alla databaser, inklusive huvuddatabasen och andra systemdatabaser, när infrastrukturkryptering är aktiverat. För stunden måste kunderna begära åtkomst till den här funktionen. Om du är intresserad av den här funktionen kontaktar du AzureSQLDoubleEncryptionAtRest@service.microsoft.com .

## <a name="benefits-of-the-customer-managed-tde"></a>Fördelar med kund-hanterad TDE

Kund-hanterad TDE ger kunden följande fördelar:

- Fullständig och detaljerad kontroll över användning och hantering av TDE-skyddet;

- Transparens för användning av TDE-skydd;

- Möjlighet att implementera uppdelning av uppgifter i hanteringen av nycklar och data i organisationen;

- Key Vault kan återkalla behörigheter för nyckelåtkomst för att göra den krypterade databasen otillgänglig.

- Central hantering av nycklar i AKV;

- Större förtroende från dina slutanvändare, eftersom AKV är utformat så att Microsoft inte kan se eller extrahera krypteringsnycklar;

## <a name="how-customer-managed-tde-works"></a>Så här fungerar kund hanterad TDE

![Konfiguration och drift av den kund hanterade TDE:en](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-roles.PNG)

För att servern ska kunna använda TDE-skydd som lagras i AKV för kryptering av DEK måste nyckelvalvsadministratören ge följande åtkomsträttigheter till servern med dess unika Azure Active Directory-identitet (Azure AD):

- **get** – för att hämta den offentliga delen och egenskaperna för nyckeln i Key Vault

- **wrapKey** – för att kunna skydda (kryptera) DEK

- **unwrapKey** – för att kunna ta bort skyddet (dekryptera) DEK

Key Vault-administratören kan [också aktivera loggning av key vault-granskningshändelser,](../../azure-monitor/insights/key-vault-insights-overview.md)så att de kan granskas senare.

När servern är konfigurerad för att använda ett TDE-skydd från AKV skickar servern DEK för varje TDE-aktiverad databas till nyckelvalvet för kryptering. Key Vault returnerar den krypterade DEK som sedan lagras i användardatabasen.

Vid behov skickar servern skyddad DEK till nyckelvalvet för dekryptering.

Granskare kan använda Azure Monitor för att granska Key Vault AuditEvent-loggar om loggning är aktiverat.

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>Krav för att konfigurera kund-hanterad TDE

### <a name="requirements-for-configuring-akv"></a>Krav för att konfigurera AKV

- Nyckelvalv och SQL Database/hanterad instans måste tillhöra samma Azure Active Directory klientorganisation. Interaktioner mellan klientnyckelvalv och server stöds inte. Om du vill flytta resurser efteråt måste TDE med AKV konfigureras om. Läs mer om [att flytta resurser](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

- [Funktionen för mjuk borttagning](../../key-vault/general/soft-delete-overview.md) måste vara aktiverad på nyckelvalvet för att skydda mot oavsiktlig borttagning av dataförlust (eller nyckelvalv). Mjukt borttagna resurser bevaras i 90 dagar, såvida de inte återställs eller tas bort av kunden under tiden. *Återställnings-* *och rensningsåtgärderna* har sina egna behörigheter som är associerade med en åtkomstprincip för nyckelvalvet. Funktionen för mjuk borttagning är inaktiverad som standard och kan aktiveras via [PowerShell](../../key-vault/general/key-vault-recovery.md?tabs=azure-powershell) [eller CLI.](../../key-vault/general/key-vault-recovery.md?tabs=azure-cli) Det kan inte aktiveras via Azure Portal.  

- Ge servern eller den hanterade instansen åtkomst till nyckelvalvet (get, wrapKey, unwrapKey) med hjälp av dess Azure Active Directory identitet. När du använder Azure Portal skapas Azure AD-identiteten automatiskt. När du använder PowerShell eller CLI måste Azure AD-identiteten skapas explicit och slutförande ska verifieras. Se [Konfigurera TDE med BYOK](transparent-data-encryption-byok-configure.md) och Konfigurera TDE med [BYOK](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md) SQL Managed Instance detaljerade stegvisa instruktioner när du använder PowerShell.

- När du använder brandväggen med AKV måste du aktivera alternativet *Tillåt betrodda Microsoft-tjänster att kringgå brandväggen.*

### <a name="requirements-for-configuring-tde-protector"></a>Krav för att konfigurera TDE-skydd

- TDE-skydd kan bara vara asymmetriskt, RSA- eller RSA HSM-nyckel. Nyckellängderna som stöds är 2 048 byte och 3 072 byte.

- Nyckelaktiveringsdatumet (om det har angetts) måste vara ett tidigare datum och en tid. Förfallodatumet (om det har angetts) måste vara ett framtida datum och tid.

- Nyckeln måste vara i tillståndet *Aktiverad.*

- Om du importerar en befintlig nyckel till nyckelvalvet måste du ange den i filformat som stöds ( `.pfx` , `.byok` eller `.backup` ).

> [!NOTE]
> Azure SQL nu stöd för användning av en RSA-nyckel som lagras i en hanterad HSM som TDE-skydd. Den här funktionen är i **offentlig förhandsversion.** Azure Key Vault Managed HSM är en fullständigt hanterad molntjänst som uppfyller standardkrav för en enskild klientorganisation och som gör att du kan skydda kryptografiska nycklar för dina molnprogram med FIPS 140-2 Level 3-verifierade HSM:er. Läs mer om [Managed HSM:er.](../../key-vault/managed-hsm/index.yml)


## <a name="recommendations-when-configuring-customer-managed-tde"></a>Rekommendationer när du konfigurerar kund hanterad TDE

### <a name="recommendations-when-configuring-akv"></a>Rekommendationer när du konfigurerar AKV

- Associera högst 500 Generell användning eller 200 Affärskritisk-databaser totalt med ett nyckelvalv i en enda prenumeration för att säkerställa hög tillgänglighet när servern kommer åt TDE-skyddet i nyckelvalvet. Dessa siffror baseras på upplevelsen och dokumenteras i [key vault-tjänstens gränser.](../../key-vault/general/service-limits.md) Avsikten här är att förhindra problem efter server-redundans, eftersom det utlöser lika många nyckelåtgärder mot valvet som det finns databaser på den servern.

- Ange ett resurslås för nyckelvalvet för att styra vem som kan ta bort den här kritiska resursen och förhindra oavsiktlig eller obehörig borttagning. Läs mer om [resurslås.](../../azure-resource-manager/management/lock-resources.md)

- Aktivera granskning och rapportering för alla krypteringsnycklar: Key Vault innehåller loggar som är enkla att mata in i andra säkerhetsinformations- och händelsehanteringsverktyg. Operations Management Suite [Log Analytics](../../azure-monitor/insights/key-vault-insights-overview.md) är ett exempel på en tjänst som redan är integrerad.

- Länka varje server med två nyckelvalv som finns i olika regioner och innehåller samma nyckelmaterial för att säkerställa hög tillgänglighet för krypterade databaser. Markera endast nyckeln från nyckelvalvet i samma region som ett TDE-skydd. Systemet växlar automatiskt till nyckelvalvet i fjärrregionen om ett avbrott påverkar nyckelvalvet i samma region.

### <a name="recommendations-when-configuring-tde-protector"></a>Rekommendationer när du konfigurerar TDE-skydd

- Förvara en kopia av TDE-skyddet på en säker plats eller depositionsplatsen till depositionstjänsten.

- Om nyckeln genereras i nyckelvalvet skapar du en nyckelsäkerhetskopia innan du använder nyckeln i AKV för första gången. Säkerhetskopieringen kan bara återställas till en Azure Key Vault. Läs mer om [kommandot Backup-AzKeyVaultKey.](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- Skapa en ny säkerhetskopia när ändringar görs i nyckeln (till exempel nyckelattribut, taggar, ACL:er).

- **Behåll tidigare versioner** av nyckeln i nyckelvalvet när du roterar nycklar, så att äldre databassäkerhetskopior kan återställas. När TDE-skyddet ändras för en databas uppdateras  inte gamla säkerhetskopior av databasen för att använda det senaste TDE-skyddet. Vid återställningen behöver varje säkerhetskopiering det TDE-skydd som den krypterades med när den skapades. Nyckelrotationerna kan utföras enligt anvisningarna i [Rotera transparent datakryptering-skydd med Hjälp av PowerShell.](transparent-data-encryption-byok-key-rotation.md)

- Behåll alla tidigare använda nycklar i AKV även efter byte till tjänst hanterade nycklar. Det säkerställer att databassäkerhetskopior kan återställas med TDE-skydd som lagras i AKV.  TDE-skydd som skapats med Azure Key Vault måste underhållas tills alla återstående lagrade säkerhetskopior har skapats med tjänst hanterade nycklar. Gör återställningsbara säkerhetskopior av dessa nycklar med [backup-AzKeyVaultKey.](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- Om du vill ta bort en potentiellt komprometterad nyckel under en säkerhetsincident utan risk för dataförlust följer du stegen i Ta bort [en potentiellt komprometterad nyckel.](transparent-data-encryption-byok-remove-tde-protector.md)

## <a name="inaccessible-tde-protector"></a>Otillgängligt TDE-skydd

När transparent datakryptering har konfigurerats för att använda en kund hanterad nyckel krävs kontinuerlig åtkomst till TDE-skyddet för att databasen ska vara online. Om servern förlorar åtkomst till det kund hanterade TDE-skyddet i AKV kommer en databas inom 10 minuter att börja neka alla anslutningar med motsvarande felmeddelande och ändra dess tillstånd till *Otillgänglig*. Den enda åtgärden som tillåts för en databas i otillgängligt tillstånd är att ta bort den.

> [!NOTE]
> Om databasen inte är tillgänglig på grund av ett tillfälligt nätverksavbrott krävs ingen åtgärd och databaserna kommer att online igen automatiskt.

När åtkomsten till nyckeln har återställts krävs extra tid och steg för att ta databasen online igen, vilket kan variera beroende på hur lång tid som förflutit utan åtkomst till nyckeln och storleken på data i databasen:

- Om nyckelåtkomsten återställs inom 8 timmar, kommer databasen att autohealeras inom nästa timme.

- Om nyckelåtkomsten återställs efter mer än 8 timmar går det inte att göra en automatiskheal utan att återställa databasen kräver extra steg på portalen och det kan ta lång tid beroende på databasens storlek. När databasen är online igen går tidigare konfigurerade [](auto-failover-group-overview.md) inställningar på servernivå, till exempel konfiguration av redundansgrupp, historik för återställning till tidpunkt och taggar **förlorade.** Därför rekommenderar vi att du implementerar ett meddelandesystem som gör att du kan identifiera och åtgärda underliggande problem med nyckelåtkomst inom 8 timmar.

Nedan visas en vy över de extra steg som krävs på portalen för att ta en otillgänglig databas online igen.

![Otillgänglig TDE BYOK-databas](./media/transparent-data-encryption-byok-overview/customer-managed-tde-inaccessible-database.jpg)


### <a name="accidental-tde-protector-access-revocation"></a>Oavsiktligt återkallande av åtkomst till TDE-skydd

Det kan hända att någon med tillräcklig åtkomstbehörighet till nyckelvalvet av misstag inaktiverar serveråtkomsten till nyckeln genom att:

- återkalla nyckelvalvsbehörigheterna *get*, *wrapKey*, *unwrapKey* från servern

- ta bort nyckeln

- ta bort nyckelvalvet

- ändra nyckelvalvs brandväggsregler

- ta bort den hanterade identiteten för servern i Azure Active Directory

Läs mer om [vanliga orsaker till att databasen blir otillgänglig.](/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current&preserve-view=true#common-errors-causing-databases-to-become-inaccessible)

## <a name="monitoring-of-the-customer-managed-tde"></a>Övervakning av kund-hanterad TDE

Om du vill övervaka databastillståndet och aktivera aviseringar om förlust av åtkomst till TDE-skydd konfigurerar du följande Azure-funktioner:

- [Azure Resource Health](../../service-health/resource-health-overview.md). En otillgänglig databas som har förlorat åtkomst till TDE-skyddet visas som "Ej tillgänglig" när den första anslutningen till databasen har nekats.
- [Aktivitetslogg](../../service-health/alerts-activity-log-service-notifications-portal.md) när åtkomsten till TDE-skyddet i det kund hanterade nyckelvalvet misslyckas, och poster läggs till i aktivitetsloggen.  Genom att skapa aviseringar för dessa händelser kan du återställa åtkomsten så snart som möjligt.
- [Åtgärdsgrupper](../../azure-monitor/alerts/action-groups.md) kan definieras för att skicka meddelanden och aviseringar till dig baserat på dina önskemål, till exempel e-post/SMS/push/röst, logikapp, webhook, ITSM eller Automation Runbook.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Säkerhetskopiering och återställning av databas med kund-hanterad TDE

När en databas har krypterats med TDE med hjälp av en nyckel från Key Vault, krypteras alla nyligen genererade säkerhetskopior också med samma TDE-skydd. När TDE-skyddet ändras uppdateras inte gamla säkerhetskopior av databasen **för att använda** det senaste TDE-skyddet.

Om du vill återställa en säkerhetskopia som krypterats med ett TDE-skydd från Key Vault, kontrollerar du att nyckelmaterial är tillgängligt för målservern. Vi rekommenderar därför att du behåller alla gamla versioner av TDE-skyddet i nyckelvalvet, så att säkerhetskopior av databasen kan återställas.

> [!IMPORTANT]
> Det kan när som helst inte finnas fler än ett TDE-skydd för en server. Det är nyckeln som markerats med "Make the key the default TDE protector" (Gör nyckeln till standard TDE-skydd) Azure Portal bladet. Flera ytterligare nycklar kan dock länkas till en server utan att markera dem som ett TDE-skydd. Dessa nycklar används inte för att skydda DEK, men kan användas under återställning från en säkerhetskopia, om säkerhetskopian krypteras med nyckeln med motsvarande tumavtryck.

Om nyckeln som behövs för att återställa en säkerhetskopia inte längre är tillgänglig för målservern returneras följande felmeddelande vid återställningsfelet: "Målservern har inte åtkomst till alla AKV-URI:er som skapats mellan `<Servername>` \<Timestamp #1> och \<Timestamp #2> . Försök igen när du har återställt alla AKV-URI:er."

Du kan åtgärda det genom att köra cmdleten [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) för målservern eller [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) för den hanterade målinstansen för att returnera listan över tillgängliga nycklar och identifiera de nycklar som saknas. Kontrollera att alla säkerhetskopior kan återställas genom att se till att målservern för återställningen har åtkomst till alla nycklar som behövs. De här nycklarna behöver inte markeras som TDE-skydd.

Mer information om säkerhetskopieringsåterställning för SQL Database finns i [Återställa en databas i SQL Database](recovery-using-backups.md). Mer information om säkerhetskopieringsåterställning för dedikerad SQL-pool i Azure Synapse Analytics finns i [Återställa en dedikerad SQL-pool.](../../synapse-analytics/sql-data-warehouse/backup-and-restore.md) Information SQL Server interna säkerhetskopiering/återställning med SQL Managed Instance finns i [Snabbstart: Återställa en databas till SQL Managed Instance](../managed-instance/restore-sample-database-quickstart.md)

Ytterligare överväganden för loggfiler: Säkerhetskopierade loggfiler förblir krypterade med det ursprungliga TDE-skyddet, även om det roterades och databasen nu använder ett nytt TDE-skydd.  Vid återställningen krävs båda nycklarna för att återställa databasen.  Om loggfilen använder ett TDE-skydd som lagras i Azure Key Vault krävs den här nyckeln vid återställningen, även om databasen har ändrats för att använda tjänst-hanterad TDE under tiden.

## <a name="high-availability-with-customer-managed-tde"></a>Hög tillgänglighet med kund hanterad TDE

Även om det inte finns någon konfigurerad georedundans för servern rekommenderar vi starkt att du konfigurerar servern så att den använder två olika nyckelvalv i två olika regioner med samma nyckelmaterial. Nyckeln i det sekundära nyckelvalvet i den andra regionen ska inte markeras som TDE-skydd och den tillåts inte ens. Om ett avbrott påverkar det primära nyckelvalvet, och först då, växlar systemet automatiskt till den andra länkade nyckeln med samma tumavtryck i det sekundära nyckelvalvet, om det finns. Observera att växlingen inte sker om TDE-skyddet inte är åtkomligt på grund av återkallade åtkomsträttigheter, eller om nyckeln eller nyckelvalvet tas bort, eftersom det kan tyda på att kunden avsiktligt vill hindra servern från att komma åt nyckeln. Du kan tillhandahålla samma nyckelmaterial till två nyckelvalv i olika regioner genom att skapa nyckeln utanför nyckelvalvet och importera dem till båda nyckelvalven. 

Du kan också göra detta genom att generera nyckeln med hjälp av det primära nyckelvalvet som är samplacerat i samma region som servern och klona nyckeln till ett nyckelvalv i en annan Azure-region. Använd cmdleten [Backup-AzKeyVaultKey](/powershell/module/az.keyvault/Backup-AzKeyVaultKey) för att hämta nyckeln i krypterat format från det primära nyckelvalvet och använd sedan cmdleten [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) och ange ett nyckelvalv i den andra regionen för att klona nyckeln. Du kan också använda Azure Portal för att backa upp och återställa nyckeln. Säkerhetskopiering/återställning av nycklar tillåts endast mellan nyckelvalv inom samma Azure-prenumeration och [Azure-geografi.](https://azure.microsoft.com/global-infrastructure/geographies/)  

![Single-Server HA](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>Geo-DR och kund-hanterad TDE

I scenarier med både [](auto-failover-group-overview.md) [aktiv geo-replikering](active-geo-replication-overview.md) och redundansgrupper kräver varje server som ingår ett separat nyckelvalv som måste samplaceras med servern i samma Azure-region. Kunden ansvarar för att hålla nyckelmaterial i nyckelvalven konsekventa, så att geo-sekundärt är synkroniserat och kan ta över med samma nyckel från det lokala nyckelvalvet om den primära blir otillgänglig på grund av ett avbrott i regionen och en redundans utlöses. Upp till fyra secondaries kan konfigureras och länkning (secondaries av secondaries) stöds inte.

För att undvika problem när du upprättar eller under geo-replikering på grund av ofullständigt nyckelmaterial, är det viktigt att du följer dessa regler när du konfigurerar kund-hanterad TDE:

- Alla nyckelvalv som ingår måste ha samma egenskaper och samma åtkomstbehörigheter för respektive servrar.

- Alla berörda nyckelvalv måste innehålla identiskt nyckelmaterial. Det gäller inte bara för det aktuella TDE-skyddet, utan även för alla tidigare TDE-skydd som kan användas i säkerhetskopieringsfilerna.

- Både den inledande installationen och rotationen av TDE-skyddet måste göras på den sekundära först och sedan på den primära.

![Redundansgrupper och geo-dr](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-bcdr.png)

Om du vill testa en redundans följer du stegen i [Översikt över aktiv geo-replikering.](active-geo-replication-overview.md) Du bör regelbundet testa redundans för att verifiera SQL Database har bibehållen åtkomstbehörighet till båda nyckelvalven.

## <a name="next-steps"></a>Nästa steg

Du kanske också vill kontrollera följande PowerShell-exempelskript för vanliga åtgärder med kund-hanterad TDE:

- [Rotera transparent datakryptering skydd för SQL Database PowerShell](transparent-data-encryption-byok-key-rotation.md)

- [Ta bort ett transparent datakryptering(TDE)-skydd för SQL Database powershell](transparent-data-encryption-byok-remove-tde-protector.md)

- [Hantera transparent datakryptering i SQL Managed Instance med din egen nyckel med hjälp av PowerShell](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)