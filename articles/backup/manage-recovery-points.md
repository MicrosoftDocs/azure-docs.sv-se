---
title: Hantera återställningspunkter
description: Lär dig hur tjänsten Azure Backup hanterar återställnings punkter för virtuella datorer
ms.topic: conceptual
ms.date: 11/08/2020
ms.openlocfilehash: 256df693aba0f799c24bcba6defe846e5c37ccaa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94428711"
---
# <a name="manage-recovery-points"></a>Hantera återställningspunkter

Den här artikeln beskriver hur kvarhållning fungerar för virtuella datorer. När säkerhets kopieringar sker skapas återställnings punkter från vilka återställnings åtgärder kan utföras.

För virtuella datorer är den första säkerhets kopieringen en fullständig säkerhets kopia och de efterföljande säkerhets kopieringarna är stegvisa säkerhets kopior.

## <a name="recovery-points-and-retention"></a>Återställnings punkter och kvarhållning

### <a name="scheduled-initial-and-incremental-backup"></a>Schemalagd inledande och stegvis säkerhets kopiering

Vi tar ett förenklat exempel på den virtuella datorn *v1* med en datadisk som består av fyra block: block 1, block 2, Block 3 och block 4. Varje block är i storlek 16 KB.

![Virtuell dator med fyra block](./media/manage-recovery-points/four-blocks.png)

**Steg 1 – första säkerhets kopiering:** Den första säkerhets kopieringen är en fullständig säkerhets kopia. Den fungerar som en bas linje där efterföljande stegvisa säkerhets kopieringar tillämpas. Anta att data skrivs till block 1 och block 2 på den virtuella käll datorn. Samma data kommer att replikeras som D1 och D2 i Recovery Services Vault-lagringen.

![Den första säkerhets kopieringen replikeras](./media/manage-recovery-points/initial-backup.png)

**Steg 2 – stegvis säkerhets kopia 1:** Tänk på att det finns nya data som har lagts till i Block 3 av den virtuella datorn. Samma data kommer att replikeras vid nästa stegvisa säkerhets kopiering och endast det block som ändras lagras som D3.  I varje steg, även om 1 KB av blocket ändras, laddas hela 16 KB-blocket upp i återställnings punkten.

![Första stegvisa säkerhets kopiering](./media/manage-recovery-points/first-incremental-backup.png)

**Steg 3 – stegvis säkerhets kopiering 2:**  Tänk nu på att det finns data ändringar på Block 3 och block 2 på den virtuella käll datorn. Dessa ändringar replikeras vid nästa stegvisa säkerhets kopiering som D3 "och D2".

![Andra stegvisa säkerhets kopieringar](./media/manage-recovery-points/second-incremental-backup.png)

### <a name="on-demand-backup"></a>Säkerhets kopiering på begäran

Du kan välja att köra en säkerhets kopiering på begäran av en virtuell dator när du har konfigurerat skyddet.

- Säkerhets kopiering på begäran kommer att vara en fullständig säkerhets kopia om den utlöses före den första schemalagda första säkerhets kopieringen.
- Om den första säkerhets kopieringen är klar och en säkerhets kopiering på begäran utlöses, är det en stegvis säkerhets kopia.
- Retentions tiden för återställnings punkter som skapats för en säkerhets kopiering på begäran är det kvarhållningsintervall som du anger när du utlöser säkerhets kopieringen.

### <a name="storage-cost"></a>Lagringskostnad

Den **återställnings punkt** som skapades för den första säkerhets kopieringen innehåller alla block som innehåller data. Efterföljande stegvisa återställnings punkter består bara av block som har ändrade data. Lagrings kostnaderna motsvarar summan av alla block som sträcker sig över alla återställnings punkter.

Vi använder exemplet ovan för att förstå lagrings kostnaden efter varje steg:

|Steg  |Typ av säkerhetskopiering  |Ändrade block  |Lagringstyp |
|------|---------|---------|---------|
|1     |     Den första säkerhetskopieringen    | Block 1, block 2        |    Motsvarande återställnings punkt 1 (D1 + D2)     |
|2     |  Stegvis säkerhets kopia 1       |  Block 3       |   Motsvarande återställnings punkt 1 (D1 + D2) + återställnings punkt 2 (D3)      |
|3     |    Stegvis säkerhets kopia 2     |    Block 2, Block 3     |   Motsvarande återställnings punkt 1 (D1 + D2) + återställnings punkt 2 (D3) + återställnings punkt 3 (D2 ' + D3 ')      |

### <a name="recovery-point-expiration"></a>Förfallo datum för återställnings punkt

Varje återställnings punkt har en kvarhållningsperiod som anges i säkerhets kopierings principen. Rensningen sker med jämna mellanrum och alla återställnings punkter som har upphört att gälla rensas.

När återställnings punkten går ut, tas den bort eller slås samman.

### <a name="case-1-initial-recovery-point-expires"></a>Fall 1: den första återställnings punkten upphör att gälla

När den första återställnings punkten går ut slås den samman med nästa stegvisa återställnings punkt. Alla data block som skrivs över i den stegvisa återställnings punkten tas bort och resten slås samman. Den stegvisa säkerhets kopieringen blir sedan den första fullständiga säkerhets kopian. Låt oss granska med ett exempel:

- *Återställnings punkt 1* som skapas under den första säkerhets kopieringen har en fullständig säkerhets kopia av den virtuella datorn.
- När *återställnings punkt 1* går ut är *återställnings punkt 2* nästa fullständiga säkerhets kopiering.
- Block D1 sammanfogas med *återställnings punkt 2* och D2 tas bort eftersom data i block 2 skrivs över i *återställnings punkt 2*. Den här ändringen samlas in som block D2.
- Block D1 behålls i de efterföljande återställnings punkterna som är, tills det finns några ändringar som gjorts före nästa säkerhets kopiering.

![Första fallet](./media/manage-recovery-points/first-case.png)

### <a name="case-2-in-between-incremental-recovery-point-expires"></a>Fall 2: den stegvisa återställnings punkten upphör att gälla

- Om *återställnings punkt 2* går ut före *återställnings punkt 1* slås data från *återställnings punkt 2* samman med nästa tillgängliga återställnings punkt: *återställnings punkt 3*. Därför slås block D3 samman med *återställnings punkt 3*.
- *Återställnings punkt 1* är fortfarande den fullständiga säkerhets kopian med block D1 och D2.

![Andra fallet](./media/manage-recovery-points/second-case.png)

### <a name="case-3-on-demand-recovery-point-expires"></a>Fall 3: återställnings punkten på begäran upphör att gälla

I det här exemplet är en schema princip (daglig säkerhets kopiering) schemalagd att köras med *n* dagars kvarhållningsperiod.  Om en säkerhets kopiering på begäran utlöses den fjärde dagen innan nästa schemalagda säkerhets kopiering och dess kvarhållningsperiod anges som 10 dagar, kommer det fortfarande att vara en stegvis säkerhets kopia. En återställnings punkt (*RP1 på begäran*) kommer att skapas efter *återställnings punkt 3* och innan *återställnings punkt 4*.  Vid slutet av dagen 14 upphör återställnings punkten på begäran (*RP1 på begäran*) och den slås samman med nästa tillgängliga återställnings punkt. De data block som fortfarande finns på servern slås samman, medan data block som har ändrats (överskrivna eller borttagna) tas bort från den utgångna återställnings punkten.

![Tredje fallet](./media/manage-recovery-points/third-case.png)

### <a name="impact-of-policy-change-on-recovery-points"></a>Effekt av princip ändringar på återställnings punkter

När en princip ändras tillämpas den på både nya och befintliga återställnings punkter. Mer information finns i [effekten av princip ändringar på återställnings punkter](backup-architecture.md#impact-of-policy-change-on-recovery-points).

### <a name="impact-of-stop-protection-on-recovery-points"></a>Påverkan av stopp skydd på återställnings punkter

Det finns två sätt att sluta skydda en virtuell dator:

- **Stoppa skyddet och ta bort säkerhetskopierade data.** Med det här alternativet stoppas alla framtida säkerhets kopierings jobb från att skydda din virtuella dator och alla återställnings punkter tas bort. Om [mjuk borttagning](backup-azure-security-feature-cloud.md) är aktiverat behålls de borttagna data i 14 dagar. Avgifterna debiteras inte för objekt i läget Soft-Deleted. Du kan ta bort data från perioden 14 dagar. Om mjuk borttagning inte är aktiverat rensas data omedelbart och du kan inte återställa den virtuella datorn eller använda alternativet **återuppta säkerhets kopiering** .
- **Stoppa skyddet och behåll säkerhets kopierings data.** Med det här alternativet stoppas alla framtida säkerhets kopierings jobb från att skydda den virtuella datorn. Azure Backups tjänsten behåller dock kontinuerligt de återställnings punkter som har säkerhetskopierats. Du måste betala för att behålla återställnings punkterna i valvet (se [Azure Backup priser](https://azure.microsoft.com/pricing/details/backup/) för mer information). Du kommer att kunna återställa den virtuella datorn om det behövs. Om du väljer att återuppta skyddet för virtuella datorer kan du använda alternativet **återuppta säkerhets kopiering** . När du har återupptagit säkerhets kopieringen tillämpas reglerna för kvarhållning för förfallo punkter. Du kan också ta bort säkerhetskopierade data med alternativet  **ta bort säkerhets kopierings data** .

## <a name="impact-of-deleting-a-vm-without-stop-protection"></a>Effekt av att ta bort en virtuell dator utan att avbryta skyddet

Att ta bort en virtuell dator utan att avbryta skyddet påverkar återställnings punkter och är ett oönskat scenario. Ideal säkerhets kopiering bör stoppas innan den virtuella datorn tas bort. Eftersom resursen inte finns kommer de schemalagda säkerhets kopieringarna att Miss lyckas med [VMNotFoundV2-felet](backup-azure-vms-troubleshoot.md#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found). Återställnings punkterna rensas regelbundet enligt bevarande principen, men den sista kopian av den virtuella datorn kommer att vara oändlig och du debiteras därefter. Beroende på ditt scenario har du följande två alternativ:

- **Alternativ 1:** Återställ den virtuella datorn med någon av återställnings punkterna. Om du vill återställa den borttagna virtuella datorn återställer du med samma namn och i samma resurs grupp. Om du skyddar den återställda virtuella datorn till samma valv blir de befintliga återställnings punkterna automatiskt anslutna.
- **Alternativ 2:** Gå till Recovery Services valvet och stoppa skyddet med ta bort data.

### <a name="impact-of-expired-recovery-points-for-items-in-soft-deleted-state"></a>Effekt av utgångna återställnings punkter för objekt i läget Soft Deleted

Om [mjuk borttagning](backup-azure-security-feature-cloud.md) har Aktiver ATS för Recovery Services-valvet förblir den utgångna återställnings punkten i läget tyst Borttagning och rensas inte. Inga avgifter uppstår när en återställnings punkt är i läget Soft-Deleted.

### <a name="impact-of-churn-on-backup-performance"></a>Effekt av omsättning på säkerhets kopierings prestanda

Anta att den totala lagringen för en virtuell dator är 8 TB och omsättningen är 5%. Sedan kommer motsvarande lagrings utrymme för säkerhets kopiorna 5% av 8 TB som är 0,4 TB. Högre omsättning motsvarar högre Server dels lagring för efterföljande stegvisa säkerhets kopieringar. Omsättningen påverkar säkerhets kopierings prestanda. Ju högre omsättning, desto långsammare säkerhets kopierings process och större förbrukning av Server dels lagring.

Ta reda på hur omsättningen påverkar säkerhets kopierings prestanda genom att titta på det här scenariot:

|Virtuella datorer  |VM1  |VM2  |VM3  |
|---------|---------|---------|---------|
|Antal data diskar    | 4 (A1, A2, A3, A4)        | 4 (B1, B2, B3, B4)        |  4 (C1, C2, C3, C4)       |
|Storlek på varje disk   |      4 TB   | 4 TB        |  4 TB       |
|Säkerhetskopiera data omsättning    |   A1 – 4 TB      | B1-1 TB; B2-1 TB <br> B3-1 TB; B4 – 1 TB  |   C1-2 TB; C4 – 2 TB      |

Prestandan för säkerhets kopiering är i ordningen VM2>VM3>VM1. Orsaken till detta är att de indelade data sprids över de olika diskarna. Eftersom säkerhets kopieringen av diskar sker parallellt, kommer VM2 att visa bästa prestanda.

## <a name="next-steps"></a>Nästa steg

- [Azure Backup arkitektur och komponenter](backup-architecture.md)
