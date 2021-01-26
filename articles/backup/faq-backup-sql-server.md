---
title: Vanliga frågor och svar – säkerhetskopiera SQL Server databaser på virtuella Azure-datorer
description: Få svar på vanliga frågor om hur du säkerhetskopierar SQL Server databaser på virtuella Azure-datorer med Azure Backup.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: ca785e217da4355a44ffbb26b813d55d942c5c14
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787628"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Vanliga frågor om SQL Server databaser som körs på en virtuell Azure-säkerhetskopiering

Den här artikeln besvarar vanliga frågor om säkerhets kopiering av SQL Server databaser som körs på virtuella Azure-datorer (VM) och använder tjänsten [Azure Backup](backup-overview.md) .

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>Kan jag använda Azure Backup för IaaS VM och SQL Server på samma dator?

Ja, du kan ha både VM-säkerhetskopiering och SQL-säkerhetskopiering på samma virtuella dator. I det här fallet utlöser vi internt endast kopiera fullständig säkerhets kopiering på den virtuella datorn för att inte trunkera loggarna.

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>Försöker lösningen igen eller automatiskt korrigera säkerhets kopiorna?

Under vissa omständigheter utlöser tjänsten Azure Backup tjänsten återställnings säkerhets kopior. Auto-läka kan ske under de sex villkor som anges nedan:

- Om loggen eller differentiell säkerhets kopieringen Miss lyckas på grund av LSN-valideringen, konverteras nästa logg eller differentiell säkerhets kopia i stället till en fullständig säkerhets kopia.
- Om ingen fullständig säkerhets kopiering har skett före en logg eller differentiell säkerhets kopiering, konverteras den loggen eller differentiella säkerhets kopian i stället till en fullständig säkerhets kopia.
- Om den senaste fullständiga säkerhets kopian är äldre än 15 dagar, konverteras nästa logg eller differentiell säkerhets kopia i stället till en fullständig säkerhets kopia.
- Alla säkerhets kopierings jobb som avbryts på grund av en tilläggs uppgradering utlöses igen när uppgraderingen har slutförts och tillägget har startats.
- Om du väljer att skriva över databasen under återställningen Miss lyckas nästa logg/differentiella säkerhets kopiering och en fullständig säkerhets kopiering utlöses i stället.
- I de fall där en fullständig säkerhets kopiering krävs för att återställa logg kedjorna på grund av ändringar i databas återställnings modellen utlöses en fullständig säkerhets kopiering automatiskt enligt nästa schema.

Auto-läka som en funktion aktive ras för alla användare som standard. Men om du väljer att inte inaktivera den, utför du följande steg:

- På SQL Server-instansen, i mappen *C:\Program Files\Azure arbets belastning Backup\bin* , skapar eller redigerar du **ExtensionSettingsOverrides.jspå** filen.
- I **ExtensionSettingsOverrides.jspå** anger du *{"EnableAutoHealer": false}*.
- Spara ändringarna och Stäng filen.
- Öppna **uppgiften hantera** på SQL Server-instansen och starta sedan om **AzureWLBackupCoordinatorSvc** -tjänsten.

## <a name="can-i-control-how-many-concurrent-backups-run-on-the-sql-server"></a>Kan jag styra hur många säkerhetskopieringar som körs på SQL-servern samtidigt?

Ja. Du kan begränsa den hastighet som säkerhets kopierings principen körs för att minimera påverkan på en SQL Server instans. Så här ändrar du inställningen:

1. På SQL Server-instansen i mappen *C:\Program Files\Azure arbets belastning Backup\bin* skapar du *ExtensionSettingsOverrides.jspå* filen.
2. Ändra inställningen **DefaultBackupTasksThreshold** till ett lägre värde (till exempel 5) i filen *ExtensionSettingsOverrides.js* . <br>
  `{"DefaultBackupTasksThreshold": 5}`
<br>
Standardvärdet för DefaultBackupTasksThreshold är **20**.

3. Spara ändringarna och Stäng filen.
4. I SQL Server-instansen öppnar du **Aktivitetshanteraren**. Starta om tjänsten **AzureWLBackupCoordinatorSvc**.<br/> <br/>
 Även om den här metoden hjälper om säkerhets kopierings programmet använder en stor mängd resurser, är SQL Server [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) ett mer allmänt sätt att ange gränser för mängden processor, fysisk IO och minne som inkommande program begär Anden kan använda.

> [!NOTE]
> I UX-UX kan du fortfarande gå vidare och schemalägga så många säkerhets kopieringar vid en angiven tidpunkt. De kommer dock att bearbetas i ett glidande fönster, t. ex. 5, i enlighet med exemplet ovan.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Kan jag köra en fullständig säkerhetskopia från en sekundär replik?

Enligt SQL-begränsningar kan du bara köra fullständig kopiering på den sekundära repliken. Fullständig säkerhets kopiering är dock inte tillåten.

## <a name="can-i-protect-availability-groups-on-premises"></a>Kan jag skydda tillgänglighets grupper lokalt?

Nej. Azure Backup skyddar SQL Server databaser som körs i Azure. Om en tillgänglighets grupp (AG) sprids mellan Azure och lokala datorer kan AG endast skyddas om den primära repliken körs i Azure. Azure Backup skyddar också bara de noder som körs i samma Azure-region som Recovery Servicess valvet.

## <a name="can-i-protect-availability-groups-across-regions"></a>Kan jag skydda tillgänglighets grupper i flera regioner?

Azure Backup Recovery Services-valvet kan identifiera och skydda alla noder i samma region som valvet. Om din SQL Server Always on-tillgänglighetsgrupper sträcker sig över flera Azure-regioner ställer du in säkerhets kopieringen från den region som har den primära noden. Azure Backup kan identifiera och skydda alla databaser i tillgänglighets gruppen enligt din säkerhets kopierings inställning. När säkerhets kopierings inställningen inte uppfylls, Miss lyckas säkerhets kopieringarna och du får aviseringen om felet.

## <a name="do-successful-backup-jobs-create-alerts"></a>Skapar lyckade säkerhetskopieringsjobb aviseringar?

Nej. Lyckade säkerhetskopieringsjobb genererar inte aviseringar. Aviseringar skickas bara för säkerhetskopieringsjobb som misslyckas. Detaljerade funktioner för Portal varningar dokumenteras [här](backup-azure-monitoring-built-in-monitor.md). Men om du är intresse rad av aviseringar även för lyckade jobb kan du använda [övervakning med hjälp av Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Kan jag se schemalagda säkerhets kopierings jobb på menyn säkerhets kopierings jobb?

På menyn **säkerhets kopierings jobb** visas alla schemalagda och på begäran-åtgärder, förutom de schemalagda logg säkerhets kopiorna eftersom de ofta kan vara frekventa. För schemalagda logg jobb använder du [övervakning med hjälp av Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Läggs framtida databaser automatiskt till för säkerhetskopiering?

Ja, du kan få den här funktionen med [automatiskt skydd](backup-sql-server-database-azure-vms.md#enable-auto-protection).  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Vad händer med säkerhetskopiorna om jag tar bort en databas från en automatiskt skyddad instans?

Om en databas bryts från en skyddad instans, görs fortfarande databas säkerhets kopiorna. Detta innebär att den borttagna databasen börjar visas som ohälsosam under **säkerhets kopierings objekt** och fortfarande är skyddad.

Det korrekta sättet att sluta skydda den här databasen är att **stoppa säkerhets kopieringen** med **ta bort data** på den här databasen.  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Vad händer om jag avbryter säkerhets kopieringen av en automatiskt skyddad databas?

Om du **stoppar säkerhets kopiering med Behåll data**, kommer inga framtida säkerhets kopieringar att äga rum och de befintliga återställnings punkterna förblir intakta. Databasen kommer fortfarande att anses vara skyddad och visas under **säkerhets kopierings objekt**.

Om du **stoppar säkerhets kopiering med ta bort data** kommer inga framtida säkerhets kopieringar att äga rum och befintliga återställnings punkter tas också bort. Databasen betraktas som icke-skyddad och visas under instansen i Konfigurera säkerhets kopiering. Men till skillnad från andra skyddade databaser som kan väljas manuellt eller som kan skyddas automatiskt, visas den här databasen som nedtonad och kan inte väljas. Det enda sättet att skydda den här databasen är att inaktivera automatiskt skydd på instansen. Nu kan du välja den här databasen och konfigurera skydd på den eller återaktivera det automatiska skyddet på instansen igen.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Vad händer om jag ändrar namnet på databasen efter att det har skyddats?

En döpt databas behandlas som en ny databas. Tjänsten kommer därför att behandla den här situationen som om databasen inte hittades och att säkerhets kopieringen Miss lyckas.

Du kan välja databasen, som nu har bytt namn och konfigurerat skydd på den. Om det automatiska skyddet har Aktiver ATS på instansen identifieras och skyddas den omdöpta databasen automatiskt.

## <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Varför kan jag inte se en tillagd databas för en skyddad instans?

En databas som du [lägger till i en skyddad instans](backup-sql-server-database-azure-vms.md#enable-auto-protection) kanske inte omedelbart visas under skyddade objekt. Det beror på att identifieringen vanligtvis körs var 8:e timme. Du kan dock identifiera och skydda nya databaser omedelbart om du kör en identifiering manuellt genom att välja **identifiera databaser**, som du ser i följande bild:

  ![Identifiera en nyligen tillagd databas manuellt](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="can-i-protect-databases-on-virtual-machines-that-have-azure-disk-encryption-ade-enabled"></a>Kan jag skydda databaser på virtuella datorer som har Azure Disk Encryption (ADE) aktiverat?
Ja, du kan skydda databaser på virtuella datorer som har Azure Disk Encryption (ADE) aktiverat.

## <a name="can-i-protect-databases-that-have-tde-transparent-data-encryption-turned-on-and-will-the-database-stay-encrypted-through-the-entire-backup-process"></a>Kan jag skydda databaser som har TDE (transparent datakryptering) aktiverade och kommer databasen att förbli krypterad genom hela säkerhets kopierings processen?

Ja, Azure Backup har stöd för säkerhets kopiering av SQL Server databaser eller servrar med TDE aktiverat. Säkerhets kopiering stöder [TDE](/sql/relational-databases/security/encryption/transparent-data-encryption) med nycklar som hanteras av Azure eller med Kundhanterade nycklar (BYOK).  Säkerhets kopieringen utför inte någon SQL-kryptering som en del av säkerhets kopierings processen, så databasen förblir krypterad vid säkerhets kopiering.

## <a name="does-azure-backup-perform-a-checksum-operation-on-the-data-stream"></a>Utför Azure Backup en åtgärd för kontroll summa i data strömmen?

Vi utför en åtgärd för kontroll summa för data strömmen. Detta kan dock inte förväxlas med [SQL-kontrollsumma](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server).
Säkerhets kopiering av Azure-arbetsbelastning beräknar kontroll summan på data strömmen och lagrar den explicit under säkerhets kopieringen. Den här data Ströms data strömmen tas sedan som en referens och mellan verifieras med data strömmens kontroll Summa under återställnings åtgärden för att säkerställa att data är konsekventa.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [säkerhetskopierar en SQL Server databas](backup-azure-sql-database.md) som körs på en virtuell Azure-dator.