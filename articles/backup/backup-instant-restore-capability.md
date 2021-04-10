---
title: Azure Instant Restore-funktion
description: Azure Instant Restore-funktion och vanliga frågor och svar om VM backup stack, Resource Manager distributions modell
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 3448b162c17dec2ab5b7637a3527d1c470bd415c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102618584"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Få förbättrad säkerhets kopiering och återställning av prestanda med Azure Backup omedelbar återställnings funktion

> [!NOTE]
> Baserat på feedback från användarna har vi bytt namn på **den virtuella datorns säkerhets kopierings stack v2** till **omedelbar återställning** för att minska förvirringen med Azure Stack funktion.
> Alla Azure Backup-användare har nu uppgraderats till **omedelbar återställning**.

Den nya modellen för omedelbar återställning ger följande funktions förbättringar:

* Möjlighet att använda ögonblicks bilder som tagits som en del av ett säkerhets kopierings jobb som är tillgängligt för återställning utan att vänta på att data överförs till valvet att slutföras. Det minskar vänte tiden för ögonblicks bilder som ska kopieras till valvet innan återställningen utlöses.
* Minskar antalet säkerhets kopierings-och återställnings tider genom att behålla ögonblicks bilderna lokalt, under två dagar som standard. Standardvärdet för kvarhållning av ögonblicks bilder kan konfigureras till ett värde mellan 1 och 5 dagar.
* Stöder disk storlekar upp till 32 TB. Att ändra storlek på diskar rekommenderas inte av Azure Backup.
* Stöder Standard SSD diskar tillsammans med Standard HDD diskar och Premium SSD diskar.
* Möjlighet att använda en ohanterad virtuell dators ursprungliga lagrings konton (per disk) när du återställer. Den här funktionen finns även när den virtuella datorn har diskar som är distribuerade över lagrings konton. Den påskyndar återställnings åtgärder för en mängd olika VM-konfigurationer.
* För säkerhets kopiering av virtuella datorer som använder ohanterade Premium diskar i lagrings konton, rekommenderar vi att du allokerar *50%* ledigt utrymme för det totala allokerade lagrings utrymmet, vilket **endast** krävs för den första säkerhets kopieringen. Det lediga utrymmet på 50% är inte ett krav på säkerhets kopieringar när den första säkerhets kopieringen har slutförts.

## <a name="whats-new-in-this-feature"></a>Vad är nytt i den här funktionen

Säkerhetskopieringsjobbet består för närvarande av två faser:

1. Ta en VM-ögonblicksbild.
2. Överföra en VM-ögonblicksbild till Azure Recovery Services-valvet.

En återställningspunkt anses bara ha skapats när fas 1 och 2 har slutförts. Som en del av den här uppgraderingen skapas en återställningspunkt så snart ögonblicksbilden är klar och den här återställningspunkten kan användas till att utföra en återställning med hjälp av samma återställningsflöde. Du kan identifiera den här återställnings punkten i Azure Portal med "ögonblicks bild" som återställnings punkt typ, och när ögonblicks bilden har överförts till valvet ändras återställnings punktens typ till "ögonblicks bild och valv".

![Säkerhets kopierings jobb i den virtuella datorns resurs hanterare distributions modell--lagring och valv](./media/backup-azure-vms/instant-rp-flow.png)

Som standard behålls ögonblicks bilder i två dagar. Den här funktionen tillåter återställnings åtgärder från dessa ögonblicks bilder där genom att minska återställnings tiderna. Det minskar den tid som krävs för att transformera och kopiera data tillbaka från valvet.

## <a name="feature-considerations"></a>Funktions överväganden

* Ögonblicks bilder lagras tillsammans med diskarna för att förbättra skapandet av återställnings punkter och påskynda återställnings åtgärder. Därför kan du se lagrings kostnader som motsvarar ögonblicks bilder som tas under den här perioden.
* Stegvisa ögonblicks bilder lagras som Page blobbar. Alla användare som använder ohanterade diskar debiteras för ögonblicks bilderna som lagras i det lokala lagrings kontot. Eftersom de återställnings punkt samlingar som används av hanterade VM-säkerhetskopieringar använder BLOB-ögonblicksbilder på den underliggande lagrings nivån, kan du se kostnader som motsvarar priserna för BLOB-ögonblicksbilder och de är stegvisa.
* För Premium Storage-konton räknas ögonblicks bilderna för omedelbara återställnings punkter mot 10 TB-gränsen för allokerat utrymme.
* Du får möjlighet att konfigurera ögonblicks bildens kvarhållning baserat på återställnings behoven. Beroende på kravet kan du ange en kvarhållning av ögonblicks bilder till minst en dag i rutan säkerhets kopierings princip enligt beskrivningen nedan. Detta hjälper dig att spara kostnader för kvarhållning av ögonblicks bilder om du inte utför återställningar ofta.
* Det är en vägbeskrivnings uppgradering. När du har uppgraderat till omedelbar återställning kan du inte gå tillbaka.

>[!NOTE]
>Med den här uppgraderingen av återställnings perioden kommer varaktigheten för ögonblicks bilder av alla kunder (**nya och befintliga båda**) att ställas in på standardvärdet två dagar. Du kan dock ange varaktigheten enligt ditt krav på ett värde mellan 1 och 5 dagar.

## <a name="cost-impact"></a>Kostnads påverkan

De stegvisa ögonblicks bilderna lagras i den virtuella datorns lagrings konto, som används för omedelbar återställning. Stegvisa ögonblicks bilder innebär att det utrymme som används av en ögonblicks bild motsvarar det utrymme som används av sidor som skrivs efter att ögonblicks bilden skapades. Faktureringen är fortfarande för det utrymme per GB som används av ögonblicks bilden och priset per GB är detsamma som på [sidan med priser](https://azure.microsoft.com/pricing/details/managed-disks/). För virtuella datorer som använder ohanterade diskar kan du se ögonblicks bilderna på menyn för VHD-filen för varje disk. För Managed disks lagras ögonblicks bilder i en resurs för återställnings punkt i en angiven resurs grupp, och själva ögonblicks bilderna syns inte direkt.

>[!NOTE]
> Kvarhållning av ögonblicks bilder har åtgärd ATS till 5 dagar för vecko principer.

## <a name="configure-snapshot-retention"></a>Konfigurera kvarhållning av ögonblicks bilder

### <a name="using-azure-portal"></a>Använda Azure Portal

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

I Azure Portal kan du se ett fält som lagts till i fönstret **princip för säkerhets kopiering av virtuell dator** under avsnittet **omedelbar återställning** . Du kan ändra varaktigheten för ögonblicks bilder för kvarhållning från fönstret **princip för säkerhets kopiering** för alla virtuella datorer som är associerade med den aktuella säkerhets kopierings principen.

![Omedelbar återställnings funktion](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>Använda PowerShell

>[!NOTE]
> Från AZ PowerShell version 1.6.0 och senare kan du uppdatera kvarhållningsperioden för ögonblicks bilder av ögonblicks bilder i principen med hjälp av PowerShell

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

Standard kvarhållning av ögonblicks bilder för varje princip är inställd på två dagar. Du kan ändra värdet till minst 1 och högst fem dagar. För vecko Visa-principer är kvarhållning av ögonblicks bilder åtgärdat till fem dagar.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Vilka är kostnads konsekvenserna för omedelbar återställning?

Ögonblicks bilder lagras tillsammans med diskarna för att påskynda skapandet och återställnings åtgärderna för återställnings punkter. Därför kan du se de lagrings kostnader som motsvarar den kvarhållna ögonblicks bilder som valts som en del av principen för säkerhets kopiering av virtuella datorer.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Gör ögonblicks bilderna för omedelbar återställnings punkt i Premium Storage konton med gränsen på 10 TB ögonblicks bilder?

Ja, för Premium Storage-konton är ögonblicks bilderna som tas för omedelbar återställnings punkt 10 TB allokerat ögonblicks bild utrymme.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Hur fungerar kvarhållning av ögonblicks bilder under fem dagar?

Varje dag kommer en ny ögonblicks bild att tas, och det finns fem enskilda ögonblicks bilder. Storleken på ögonblicks bilden beror på data omsättningen, som är i de flesta fall cirka 2%-7%.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Är en ögonblicks bild av en ögonblicks bild eller fullständig ögonblicks bild?

Ögonblicks bilder som tas som en del av omedelbar återställnings funktion är stegvisa ögonblicks bilder.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Hur kan jag beräkna den ungefärliga kostnads ökningen på grund av omedelbar återställnings funktion?

Det beror på den virtuella datorns omsättning. I ett stabilt tillstånd kan du anta att kostnaden är = den dagliga lagrings tiden för kvarhållning per virtuell dator per GB.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Om återställnings typen för en återställnings punkt är "ögonblicks bild och valv" och jag utför en återställnings åtgärd kommer vilken återställnings typ att användas?

Om återställnings typen är "ögonblicks bild och valv" görs återställningen automatiskt från den lokala ögonblicks bilden, vilket kommer att vara mycket snabbare jämfört med återställningen som görs från valvet.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>Vad händer om jag väljer kvarhållningsperiod för återställnings punkt (nivå 2) mindre än lagrings perioden för ögonblicks bilder (1)?

Den nya modellen tillåter inte borttagning av återställnings punkten (– nivå 2) om inte ögonblicks bilden (1) tas bort. Vi rekommenderar att du schemalägger – nivå 2-kvarhållning (schema för återställnings punkt) som är större än kvarhållningsperioden för ögonblicks bilder.

### <a name="why-does-my-snapshot-still-exist-even-after-the-set-retention-period-in-backup-policy"></a>Varför finns min ögonblicks bild fortfarande kvar, även efter den angivna kvarhållningsperioden i säkerhets kopierings policyn?

Om återställnings punkten har en ögonblicks bild och det är den senaste återställnings punkten finns den kvar tills nästa lyckade säkerhets kopiering. Detta sker enligt den angivna GC-principen (skräp insamlingen). Det innebär att det alltid finns minst en senaste återställnings punkt, vilket innebär att alla efterföljande säkerhets kopieringar inte kan utföras på grund av ett problem på den virtuella datorn. I normala fall rensas återställnings punkter högst 24 timmar efter att de gått ut. I sällsynta fall kan det finnas en eller två ytterligare ögonblicks bilder utifrån den kraftigare belastningen på skräp insamlaren (GC).

### <a name="why-do-i-see-more-snapshots-than-my-retention-policy"></a>Varför visas fler ögonblicks bilder än min bevarande princip?

I ett scenario där en bevarande princip anges som "1" kan du hitta två ögonblicks bilder. Detta innebär att det alltid finns minst en senaste återställnings punkt, vilket innebär att alla efterföljande säkerhets kopieringar inte kan utföras på grund av ett problem på den virtuella datorn. Detta kan orsaka förekomst av två ögonblicks bilder.<br></br>Om principen till exempel är för ögonblicks bilder av "n" kan du hitta "n + 1" ögonblicks bilder vid en tidpunkt. Du kan även hitta "n + 1 + 2" ögonblicks bilder om det finns en fördröjning i skräp insamling. Detta kan inträffa vid sällsynta tillfällen när:
- Du rensar ögonblicks bilder, som är tidigare kvarhållning.
- Skräp insamlaren (GC) i Server delen är hårt belastad.

### <a name="i-dont-need-instant-restore-functionality-can-it-be-disabled"></a>Jag behöver inte omedelbar återställnings funktion. Kan den inaktive ras?

Funktionen omedelbar återställning är aktive rad för alla och kan inte inaktive ras. Du kan minska ögonblicks bildens kvarhållning till minst en dag.

### <a name="is-it-safe-to-restart-the-vm-during-the-transfer-process-which-can-take-many-hours-will-restarting-the-vm-interrupt-or-slow-down-the-transfer"></a>Är det säkert att starta om den virtuella datorn under överförings processen (vilket kan ta flera timmar)? Startar om den virtuella datorns avbrott eller saktar ned överföringen?

Ja, det är säkert och det finns ingen inverkan på data överförings hastigheten.

