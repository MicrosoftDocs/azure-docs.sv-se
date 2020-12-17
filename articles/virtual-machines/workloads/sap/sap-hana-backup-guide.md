---
title: Säkerhets kopierings guide för SAP HANA på Azure Virtual Machines | Microsoft Docs
description: Säkerhets kopierings guiden för SAP HANA ger två stora säkerhets kopierings möjligheter för SAP HANA på virtuella Azure-datorer
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 340ce84ebe662b976f32048a47fd98d793083065
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617110"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Säkerhets kopierings guide för SAP HANA på Azure Virtual Machines

## <a name="getting-started"></a>Komma igång

Säkerhets kopierings guiden för SAP HANA som körs på virtuella Azure-datorer kommer bara att beskriva Azure-specifika ämnen. Information om allmänna SAP HANA säkerhets kopiering finns i SAP HANA-dokumentationen. Vi förväntar dig att du är bekant med princip för säkerhets kopiering av principer, varför och motiverar att ha en ljud-och giltig säkerhets kopierings strategi och är medveten om kraven som företaget har för säkerhets kopierings proceduren, kvarhållningsperioden för säkerhets kopiering och återställnings förfarande.

SAP HANA stöds officiellt på olika typer av virtuella Azure-datorer, t. ex. Azure M-serien. En fullständig lista över SAP HANA certifierade virtuella Azure-datorer och HANA stora instans enheter finns i [hitta certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Microsoft Azure erbjuder ett antal enheter där SAP HANA körs icke-virtualiserade på fysiska servrar. Den här tjänsten kallas [Hana-stora instanser](hana-overview-architecture.md). Den här guiden behandlar inte säkerhets kopierings processer och verktyg för HANA-stora instanser. Men kommer att begränsas till Azure Virtual Machines. Mer information om säkerhets kopierings-/återställnings processer med HANA-stora instanser finns i artikeln [HLI backup and Restore](./hana-backup-restore.md).

Den här artikeln handlar om tre säkerhets kopierings möjligheter för SAP HANA på virtuella Azure-datorer:

- HANA-säkerhetskopiering via [Azure Backup Services](../../../backup/backup-overview.md) 
- HANA-säkerhetskopiering till fil systemet på en virtuell Azure Linux-dator (se [SAP HANA Azure Backup på filnivå](sap-hana-backup-file-level.md))
- HANA-säkerhetskopiering baserat på lagrings ögonblicks bilder med hjälp av ögonblicks bilder av Azure Storage BLOB manuellt eller Azure Backup tjänsten


SAP HANA erbjuder ett säkerhets kopierings-API som gör det möjligt för säkerhets kopierings verktyg från tredje part att integrera direkt med SAP HANA. Produkter som Azure Backup-tjänsten eller [CommVault](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/) använder det här tillverkarspecifika gränssnittet för att utlösa SAP HANA databas eller göra om säkerhets kopior. 


Information om hur du hittar vilka SAP-program som stöds på Azure finns i artikeln [vilka SAP-program som stöds för Azure-distributioner](./sap-supported-product-on-azure.md).

## <a name="azure-backup-service"></a>Azure Backup tjänst

Det första scenario som visas är ett scenario där Azure Backup-tjänsten antingen använder SAP HANA- `backint` gränssnittet för att utföra en strömmande säkerhets kopiering med från en SAP HANA databas. Eller så använder du en mer allmän funktion i Azure Backup tjänst för att skapa en programkonsekvent disk ögonblicks bild och låta den överföras till tjänsten Azure Backup.

Azure Backup integreras och certifieras som säkerhets kopierings lösning för SAP HANA med hjälp av det tillverkarspecifika SAP HANA gränssnittet som kallas [backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5). Mer information om lösningen, dess funktioner och de Azure-regioner där den är tillgänglig finns i artikeln support- [matris för säkerhets kopiering av SAP HANA databaser på virtuella Azure-datorer](../../../backup/sap-hana-backup-support-matrix.md#scenario-support). Mer information och principer om Azure Backup tjänst för HANA finns [i artikeln om säkerhets kopiering SAP HANA databas i virtuella Azure-datorer](../../../backup/sap-hana-db-about.md). 

Den andra möjligheten att utnyttja Azure Backup tjänst är att skapa en programkonsekvent säkerhets kopia med disk ögonblicks bilder av Azure Premium Storage. Andra HANA-certifierade Azure-lagringar, t. ex. [Azure Ultra disk](../../disks-enable-ultra-ssd.md) och [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) , stöder inte den här typen av ögonblicks bilder via Azure backups tjänsten. Läser de här artiklarna:

- [Planera din infrastruktur för VM-säkerhetskopiering i Azure](../../../backup/backup-azure-vms-introduction.md)
- [Programkonsekventa säkerhetskopior av virtuella Linux-datorer på Azure](../../../backup/backup-azure-linux-app-consistent.md) 

den här sekvensen av aktivitet uppstår:

- Azure Backup måste köra ett skript för ögonblicks bilder som placerar programmet, i det här fallet SAP HANA, i ett konsekvent tillstånd
- I takt med att detta konsekvent tillstånd bekräftas körs Azure Backup Disk ögonblicks bilderna
- När du har slutfört ögonblicks bilderna kommer Azure Backup att återställa den aktivitet som den gjorde i skriptet för ögonblicks bilder
- Efter en lyckad körning kommer Azure Backup att strömma data till säkerhets kopierings valvet

I händelse av SAP HANA använder de flesta kunder Azure Skrivningsaccelerator för volymer som innehåller SAP HANA gör om-loggen. Azure Backup tjänsten undantar automatiskt dessa volymer från ögonblicks bilderna. Detta undantag skadar inte möjligheten för HANA att återställa. Även om det skulle blockera möjligheten att återställa med nästan alla andra SAP-stödda DBMS.

Nack delen med den här möjligheten är att du måste utveckla ditt eget skript för för-och efter ögonblicks bilder. Skriptet före ögonblicks bilden måste skapa en HANA-ögonblicksbild och hantera eventuella undantags fall. Skriptet efter ögonblicks bild måste ta bort HANA-ögonblicksbilden igen. Om du vill ha mer information om den logik som krävs börjar du med [SAP support note #2039883](https://launchpad.support.sap.com/#/notes/2039883). Överväganden för avsnittet SAP HANA data konsekvens när du tar lagrings ögonblicks bilder i den här artikeln gäller fullt ut för den här typen av säkerhets kopiering.

> [!NOTE]
> Disk ögonblicks bilds säkerhets kopieringar för SAP HANA i distributioner där flera databas behållare används, kräver en lägsta version av Hana 2,0 SP04
> 

Se information om lagrings ögonblicks bilder senare i det här dokumentet.

![Den här bilden visar två möjligheter att spara den aktuella VM-statusen](media/sap-hana-backup-guide/azure-backup-service-for-hana.png)

## <a name="other-hana-backup-methods"></a>Andra HANA-säkerhets kopierings metoder
Det finns tre andra säkerhets kopierings metoder eller sökvägar som kan övervägas:

- Säkerhetskopiera mot en NFS-resurs som baseras på Azure NetApp Files (ANF). ANF igen har möjlighet att skapa ögonblicks bilder av de volymer som du lagrar säkerhets kopior på. Med tanke på det data flöde som du slutligen kräver för att skriva säkerhets kopiorna kan den här lösningen bli en dyr metod. Även om det är lätt att upprätta eftersom HANA kan skriva säkerhets kopior direkt till Azure Native NFS-resursen
- Kör HANA-säkerhetskopiering mot VM-anslutna diskar av Standard SSD eller Azure Premium Storage. Som nästa steg kan du kopiera de säkerhetskopierade filerna mot Azure Blob Storage. Den här strategin kan vara pris vis attraktiv
- Kör HANA-säkerhetskopiering mot VM-anslutna diskar av Standard SSD eller Azure Premium Storage. Som nästa steg får disken snapshotted med jämna mellanrum. Efter den första ögonblicks bilden kan stegvisa ögonblicks bilder användas för att minska kostnaderna

![Den här bilden visar alternativ för att göra en SAP HANA säkerhets kopia av filen i den virtuella datorn](media/sap-hana-backup-guide/other-hana-backup-paths.png)

Den här bilden visar alternativ för att göra en SAP HANA säkerhets kopiering i den virtuella datorn och sedan lagra den HANA-säkerhetskopierade filer någon annan stans med hjälp av olika verktyg. Alla lösningar som inte involverar en tjänst för säkerhets kopiering från tredje part eller Azure Backup-tjänst har dock flera gemensamma hindren. En del av dem kan visas, t. ex. bevarande administration, automatisk återställnings process och automatisk återställning av tidpunkter som Azure Backup tjänst eller andra specialiserade paket för säkerhets kopiering från tredje part och tjänster. Många av dessa tjänster från tredje part kan köras på Azure. 


## <a name="sap-resources-for-hana-backup"></a>SAP-resurser för HANA-säkerhetskopiering

### <a name="sap-hana-backup-documentation"></a>Dokumentation om SAP HANA backup

- [Introduktion till SAP HANA administration](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Planera din säkerhets kopierings-och återställnings strategi](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Schemalägg HANA-säkerhetskopiering med ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Schemalägg data säkerhets kopieringar (SAP HANA cockpit)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- Vanliga frågor och svar om SAP HANA säkerhets kopiering i [SAP Note 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- Vanliga frågor om SAP HANA databas-och lagrings ögonblicks bilder i [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- Olämpliga nätverks fil system för säkerhets kopiering och återställning i [SAP Note 1820529](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Så här verifierar du att SAP HANA säkerhets kopiering är korrekt
Oberoende av din säkerhets kopierings metod är det absolut nödvändigt att köra en test återställning mot ett annat system. Den här metoden gör det möjligt att säkerställa att en säkerhets kopia är korrekt och interna processer för säkerhets kopiering och återställning fungerar som förväntat. Även om det kan vara Hurdle lokalt att återställa säkerhets kopior på grund av dess infrastruktur krav, är det mycket enklare att utföra i molnet genom att tillhandahålla nödvändiga resurser tillfälligt för detta ändamål. Det är rätt att det finns verktyg som medföljer HANA och som kan kontrol lera säkerhets kopierings filer som kan återställas. Syftet med frekventa återställnings övningar är dock att testa processen för en databas återställning och träna processen med drifts personalen.

Tänk på att det inte räcker med att göra en enkel återställning och kontrol lera om HANA är igång. Du bör köra en tabell konsekvens kontroll för att kontrol lera att den återställda databasen är felfri. SAP HANA erbjuder flera olika typer av konsekvens kontroller som beskrivs i [SAP anmärkning 1977584](https://launchpad.support.sap.com/#/notes/1977584).

Information om konsekvens kontroll av tabell finns också på SAP-webbplatsen i [konsekvens kontroller för tabeller och kataloger](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>För-och nack delar med HANA-säkerhetskopiering jämfört med lagrings ögonblicks bilder

SAP har&#39;t företräde antingen HANA-säkerhets kopiering jämfört med lagrings ögonblicks bilder. Den innehåller en lista med sina respektive-och nack delar, så att du kan avgöra vilken som ska användas beroende på situationen och tillgänglig lagrings teknik (se [Planera din strategi för säkerhets kopiering och återställning](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/ef085cd5949c40b788bba8fd3c65743e.html)).

På Azure bör du vara medveten om det faktum att funktionen för ögonblicks bilder av Azure-bloben&#39;t ger konsekvens i fil systemet på flera diskar (se [använda BLOB-ögonblicksbilder med PowerShell](/archive/blogs/cie/using-blob-snapshots-with-powershell)). 

Dessutom måste en vara medveten om fakturerings konsekvenserna när du arbetar ofta med BLOB-ögonblicksbilder enligt beskrivningen i den här artikeln: [förstå hur ögonblicks bilder debiteras](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)– det inte är&#39;t så självklart som att använda virtuella Azure-diskar.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>SAP HANA data konsekvens när du tar lagrings ögonblicks bilder

Som dokumenterades tidigare är det obligatoriskt att beskriva säkerhets kopierings funktionerna i Azure Backup, fil system och program konsekvens när du tar lagrings ögonblicks bilder. Det enklaste sättet att undvika problem är att stänga SAP HANA, eller kanske till och med hela den virtuella datorn. Något som inte är möjligt för en produktions instans.

> [!NOTE]
> Disk ögonblicks bilds säkerhets kopieringar för SAP HANA i distributioner där flera databas behållare används, kräver en lägsta version av Hana 2,0 SP04
> 

Azure Storage tillhandahåller inte konsekvens i fil systemet på flera diskar eller volymer som är kopplade till en virtuell dator under ögonblicks bild processen. Det innebär att program konsekvensen under ögonblicks bilden måste levereras av programmet, i det här fallet SAP HANA sig själv. [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883) innehåller viktig information om SAP HANA säkerhets kopior av ögonblicks bilder av lagring. Med XFS-filsystem är det till exempel nödvändigt att köra **xfs \_ Freeze** innan en lagrings ögonblicks bild startas för att tillhandahålla program konsekvens (se [xfs \_ frys (8) – Linux man-sidan](https://linux.die.net/man/8/xfs_freeze) för information om **xfs \_ frysa**).

Om det finns ett XFS fil system som sträcker sig över fyra virtuella Azure-diskar, ger följande steg en enhetlig ögonblicks bild som representerar data arean HANA:

1. Skapa HANA-förberedelse av data ögonblicks bild
1. Lås fil systemen för alla diskar/volymer (Använd till exempel **xfs \_ Freeze**)
1. Skapa alla nödvändiga BLOB-ögonblicksbilder på Azure
1. Låsa upp fil systemet
1. Bekräfta data ögonblicks bilden HANA (tar bort ögonblicks bilden)

När du använder Azure Backup funktion för att utföra programkonsekventa ögonblicks bilder av ögonblicks bilder måste steg #1 behöva kodas/följas av dig i för skriptet före ögonblicks bilden. Azure Backups tjänsten kör steg #2 och #3. Steg #4 och #5 måste anges igen av din kod i skriptet efter ögonblicks bild. Om du inte använder Azure Backup-tjänsten måste du också använda kod-och skript steg #2 och #3 på egen hand.
Mer information om hur du skapar HANA-databilder finns i följande artiklar:

- [HANA-data ögonblicks bilder] (https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/ac114d4b34d542b99bc390b34f8ef375.html
- Mer information om hur du utför steg #1 hittar du i artikeln [skapa en ögonblicks bild av data (intern SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 
- Information för att bekräfta/ta bort HANA-dataögonblicks bilder som behövs i steg #5 finns i artikeln [skapa en ögonblicks bild av data (intern SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 

Det är viktigt att bekräfta HANA-ögonblicksbilden. På grund av &quot; kopiering vid skrivning &quot; kanske SAP HANA inte kräver ytterligare disk utrymme i det här läget för ögonblicks bild förberedelse. Det&#39;s inte heller möjlighet att starta nya säkerhets kopior förrän SAP HANA ögonblicks bilden har bekräftats.


### <a name="sap-hana-backup-scheduling-strategy"></a>Strategi för SAP HANA säkerhets kopierings planering

SAP HANA artikel som [planerar säkerhets kopierings-och återställnings strategin](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) är en grundläggande plan för att göra säkerhets kopior. Förlita dig på SAP-dokumentationen runt HANA och dina upplevelser med andra DBMS i definiera strategin och processen för säkerhets kopiering/återställning för SAP HANA. Ordningsföljden mellan olika typer av säkerhets kopieringar och kvarhållningsperioden är mycket beroende av den service avtal som du behöver ange.


### <a name="sap-hana-backup-encryption"></a>SAP HANA kryptering av säkerhets kopiering

SAP HANA erbjuder kryptering av data och logg. Om SAP HANA data och loggen inte är krypterade, krypteras säkerhets kopiorna inte som standard. SAP HANA erbjuder dock en separat säkerhets kopierings kryptering som dokumenterats i [SAP HANA kryptering av säkerhets kopior](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/5f837a57ce5e468d9db21c8683bc84da.html). Om du kör äldre versioner av SAP HANA kan du behöva kontrol lera om kryptering av säkerhets kopior ingick i de funktioner som redan finns.  


## <a name="next-steps"></a>Nästa steg
* [SAP HANA Azure Backup på filnivå](sap-hana-backup-file-level.md) beskriver det filbaserade säkerhets kopierings alternativet.
* Information om hur du upprättar hög tillgänglighet och planerar för haveri beredskap för SAP HANA på Azure (stora instanser) finns i [SAP HANA (stora instanser) hög tillgänglighet och haveri beredskap på Azure](hana-overview-high-availability-disaster-recovery.md).
