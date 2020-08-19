---
title: Översikt över säkerhetsfunktioner
description: Lär dig mer om säkerhetsfunktioner i Azure Backup som hjälper dig att skydda dina säkerhets kopierings data och uppfylla verksamhetens säkerhets behov.
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 8a63585635f578f55b274c3257807fc731eacc47
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589392"
---
# <a name="overview-of-security-features-in-azure-backup"></a>Översikt över säkerhetsfunktioner i Azure Backup

Ett av de viktigaste stegen som du kan vidta för att skydda dina data är att ha en tillförlitlig infrastruktur för säkerhets kopiering. Men det är lika viktigt att se till att dina data säkerhets kopie ras på ett säkert sätt och att säkerhets kopiorna alltid är skyddade. Azure Backup tillhandahåller säkerhet för säkerhets kopierings miljön – både när dina data överförs och i vila. Den här artikeln innehåller säkerhets funktioner i Azure Backup som hjälper dig att skydda dina säkerhets kopierings data och uppfylla affärs behoven.

## <a name="management-and-control-of-identity-and-user-access"></a>Hantering och kontroll av identitets-och användar åtkomst

Lagrings konton som används av Recovery Services-valven är isolerade och kan inte användas av användare i något skadligt syfte. Åtkomst tillåts endast via Azure Backup hanterings åtgärder, till exempel Restore. Med Azure Backup kan du styra de hanterade åtgärderna genom detaljerad åtkomst med hjälp av [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](./backup-rbac-rs-vault.md). Med RBAC kan du åtskilja uppgifter i ditt team och bara ge åtkomst till de användare som krävs för att utföra sina jobb.

Azure Backup innehåller tre [inbyggda roller](../role-based-access-control/built-in-roles.md) för att kontrol lera säkerhets kopierings hanterings åtgärder:

* Säkerhets kopierings deltagare – för att skapa och hantera säkerhets kopior, förutom att ta bort Recovery Services valv och ge åtkomst till andra
* Ansvarig för säkerhets kopiering – allt en deltagare gör, förutom att ta bort säkerhets kopiering och hantera säkerhets kopierings principer
* Säkerhets kopierings läsare – behörigheter för att visa alla säkerhets kopierings hanterings åtgärder

Lär dig mer om [rollbaserad åtkomst kontroll för att hantera Azure Backup](./backup-rbac-rs-vault.md).

Azure Backup har flera säkerhets kontroller som är inbyggda i tjänsten för att förhindra, identifiera och reagera på säkerhets risker. Läs mer om [säkerhets kontroller för Azure Backup](./backup-security-controls.md).

## <a name="separation-between-guest-and-azure-storage"></a>Separering mellan gäst-och Azure-lagring

Med Azure Backup, som innehåller säkerhets kopiering av virtuella datorer och SQL och SAP HANA i VM-säkerhetskopiering, lagras säkerhetskopierade data i Azure Storage och gästen har ingen direkt åtkomst till säkerhets kopierings lagring eller dess innehåll.  Med säkerhets kopiering av virtuella datorer görs en ögonblicks bild och lagring av säkerhets kopiering av Azures infrastruktur resurs där gästen inte har någon annan medverkan än arbets belastningen för programkonsekventa säkerhets kopieringar.  Med SQL och SAP HANA får säkerhets kopierings tillägget tillfällig åtkomst att skriva till vissa blobbar.  På det här sättet, även i en komprometterad miljö, kan befintliga säkerhets kopieringar inte manipuleras eller tas bort av gästen.

## <a name="internet-connectivity-not-required-for-azure-vm-backup"></a>Ingen Internet anslutning krävs för säkerhets kopiering av virtuella Azure-datorer

Säkerhets kopiering av virtuella Azure-datorer måste flytta data från den virtuella datorns disk till Recovery Services-valvet. Men all nödvändig kommunikation och data överföring sker bara på Azures stamnät nätverk utan att behöva komma åt ditt virtuella nätverk. Därför kräver säkerhets kopiering av virtuella Azure-datorer i skyddade nätverk inte att du tillåter åtkomst till några IP-adresser eller FQDN.

## <a name="private-endpoints-for-azure-backup"></a>Privata slut punkter för Azure Backup

Du kan nu använda [privata slut punkter](../private-link/private-endpoint-overview.md) för att säkerhetskopiera dina data på ett säkert sätt från servrar i ett virtuellt nätverk till ditt Recovery Services-valv. Den privata slut punkten använder en IP-adress från VNET-adressutrymmet för ditt valv, så du behöver inte exponera dina virtuella nätverk för några offentliga IP-adresser. Privata slut punkter kan användas för att säkerhetskopiera och återställa SQL-och SAP HANA-databaser som körs i dina virtuella Azure-datorer. Det kan också användas för lokala servrar som använder MARS-agenten.

Läs mer om privata slut punkter för Azure Backup [här](./private-endpoints.md).

## <a name="encryption-of-data"></a>Kryptering av data

Kryptering skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden. Data kryptering sker i många steg i Azure Backup:

* I Azure skyddas data i överföring mellan Azure Storage och valvet [av https](backup-support-matrix.md#network-traffic-to-azure). Dessa data finns kvar i Azure stamnät nätverket.

* Säkerhetskopierade data krypteras automatiskt med hjälp av [plattforms hanterade nycklar](backup-encryption.md)och du behöver inte vidta några uttryckliga åtgärder för att aktivera den. Du kan också kryptera säkerhetskopierade data med hjälp av [Kundhanterade nycklar](encryption-at-rest-with-cmk.md) som lagras i Azure Key Vault. Den gäller för alla arbets belastningar som säkerhets kopie ras till Recovery Services-valvet.

* Azure Backup stöder säkerhets kopiering och återställning av virtuella Azure-datorer som har sina operativ system/data diskar krypterade med [Azure Disk Encryption (ade)](backup-azure-vms-encryption.md#encryption-support-using-ade) och [virtuella datorer med CMK krypterade diskar](backup-azure-vms-encryption.md#encryption-using-customer-managed-keys). Mer information finns i [läsa mer om krypterade virtuella Azure-datorer och Azure Backup](./backup-azure-vms-encryption.md).

* När data säkerhets kopie ras från lokala servrar med MARS-agenten krypteras data med en lösen fras innan överföring till Azure Backup och dekrypteras först efter att den har laddats ned från Azure Backup. Läs mer om [säkerhetsfunktioner som hjälper dig att skydda hybrid säkerhets kopieringar](#security-features-to-help-protect-hybrid-backups).

## <a name="protection-of-backup-data-from-unintentional-deletes"></a>Skydd av säkerhets kopierings data från oavsiktliga borttagningar

Azure Backup innehåller säkerhetsfunktioner som hjälper dig att skydda säkerhets kopierings data även efter borttagning. Om en användare tar bort en virtuell dator med alternativet mjuk borttagning behålls säkerhets kopierings data under 14 ytterligare dagar, vilket gör det möjligt att återställa säkerhets kopierings objekt utan data förlust. De ytterligare 14 dagars kvarhållning av säkerhets kopierings data i läget "mjuk borttagning" kostar ingen kostnad för kunden. [Läs mer om mjuk borttagning](backup-azure-security-feature-cloud.md).

## <a name="monitoring-and-alerts-of-suspicious-activity"></a>Övervakning och aviseringar för misstänkt aktivitet

Azure Backup innehåller [inbyggda övervaknings-och aviserings funktioner](./backup-azure-monitoring-built-in-monitor.md) för att visa och konfigurera åtgärder för händelser relaterade till Azure Backup. [Säkerhets kopierings rapporter](./configure-reports.md) fungerar som ett ett-stopp-mål för spårning, granskning av säkerhets kopiering och återställning samt för att identifiera viktiga trender på olika nivåer av granularitet. Om du använder Azure Backup övervaknings-och rapporterings verktyg kan du varna dig för obehörig, misstänkt eller skadlig aktivitet så fort de inträffar.

## <a name="security-features-to-help-protect-hybrid-backups"></a>Säkerhetsfunktioner som hjälper dig att skydda hybrid säkerhets kopieringar

Azure Backup tjänsten använder MARS-agenten (Microsoft Azure Recovery Services) för att säkerhetskopiera och återställa filer, mappar och volym eller system tillstånd från en lokal dator till Azure. MARS innehåller nu säkerhetsfunktioner som hjälper dig att skydda hybrid säkerhets kopieringar. Dessa funktioner omfattar:

* Ytterligare ett lager med autentisering läggs till när en kritisk åtgärd som att ändra en lösen fras utförs. Den här verifieringen är till för att säkerställa att sådana åtgärder bara kan utföras av användare som har giltiga autentiseringsuppgifter för Azure. [Läs mer om de funktioner som förhindrar attacker](./backup-azure-security-feature.md#prevent-attacks).

* Borttagna säkerhets kopierings data behålls under ytterligare 14 dagar från borttagnings datumet. Detta säkerställer återhämtning av data inom en viss tids period, så det finns ingen data förlust även om ett angrepp sker. Dessutom behålls ett större antal lägsta återställnings punkter för att skydda mot skadade data. [Läs mer om hur du återställer borttagna säkerhets kopierings data](./backup-azure-security-feature.md#recover-deleted-backup-data).

* För data som har säkerhetskopierats med hjälp av Microsoft Azure Recovery Services (MARS)-agenten används en lösen fras för att säkerställa att data krypteras innan de överförs till Azure Backup och dekrypteras först efter nedladdning från Azure Backup. Lösen Frass informationen är bara tillgänglig för den användare som skapade lösen frasen och agenten som har kon figurer ATS med den. Inget överförs eller delas med tjänsten. Detta garanterar fullständig säkerhet för dina data eftersom data som exponeras oavsiktligt (till exempel en man-in-the-Middle-attack i nätverket) inte kan användas utan lösen frasen och lösen frasen inte skickas i nätverket.

## <a name="compliance-with-standardized-security-requirements"></a>Kompatibilitet med standardiserade säkerhets krav

För att hjälpa organisationer att uppfylla nationella, regionala och branschspecifika krav som reglerar insamling och användning av individers data, Microsoft Azure & Azure Backup erbjuder en omfattande uppsättning certifieringar och attesteringar. [Se listan över certifieringar för regelefterlevnad](compliance-offerings.md)

## <a name="next-steps"></a>Nästa steg

* [Säkerhetsfunktioner som hjälper dig att skydda moln arbets belastningar som använder Azure Backup](backup-azure-security-feature-cloud.md)
* [Säkerhetsfunktioner som hjälper till att skydda hybrid säkerhets kopieringar som använder Azure Backup](backup-azure-security-feature.md)
