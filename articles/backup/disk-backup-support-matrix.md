---
title: Stödmatris för säkerhetskopiering av Azure-disk
description: Innehåller en sammanfattning av support inställningar och begränsningar för säkerhets kopiering av Azure-diskar.
ms.topic: conceptual
ms.date: 01/07/2021
ms.custom: references_regions
ms.openlocfilehash: 88ec26837cc8f69c1e84c77ea6b57ce16e462e0a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612865"
---
# <a name="azure-disk-backup-support-matrix"></a>Stödmatris för säkerhetskopiering av Azure-disk

Du kan använda [Azure Backup](./backup-overview.md) för att skydda Azure-diskar. Den här artikeln sammanfattar regions tillgänglighet, scenarier som stöds och begränsningar.

## <a name="supported-regions"></a>Regioner som stöds

Azure disk Backup är tillgängligt i följande regioner: västra USA, västra USA 2, västra centrala USA, östra USA, östra 2; USA, centrala USA, södra centrala USA, norra centrala USA, centrala Kanada, södra Brasilien, norra, södra, Storbritannien, södra, Storbritannien, västra, Västeuropa, Nord Europa, Schweiz, norra, Schweiz, västra, Tyskland, västra centrala, Frankrike Central, Norge, östra, Australien, centrala 2 , Östra Australien, centrala Korea, södra Korea, Östra Japan, västra Japan, Asien, östra, Sydostasien, centrala Indien. 

Fler regioner kommer att meddelas när de blir tillgängliga.

## <a name="limitations"></a>Begränsningar

- Azure disk backup stöds för Azure Managed Disks, inklusive delade diskar (Shared Premium SSD). Ohanterade diskar stöds inte. För närvarande stöder den här lösningen extremt diskar, inklusive delade Ultra disks, på grund av brist på ögonblicks bilds kapacitet.

- Säkerhets kopiering av Azure-diskar har stöd för säkerhets kopiering av Skrivningsaccelerator disk. Men under återställningen skulle disken återställas som en normal disk. Skriv accelererat cacheminne kan aktive ras på disken när den har monterats på en virtuell dator.

- Azure Backup tillhandahåller säkerhets kopiering (ögonblicks bild) av Azure Managed disks med stöd för flera säkerhets kopior per dag. Säkerhets kopiorna kopieras inte till säkerhets kopierings valvet.

- För närvarande stöds inte alternativet Original-Location återställning (OLR) som ska återställas genom att befintliga käll diskar ersätts från den plats där säkerhets kopiorna gjordes. Du kan återställa från återställnings punkt för att skapa en ny disk antingen i samma resurs grupp som käll disken från vilken säkerhets kopian gjordes eller i någon annan resurs grupp. Detta kallas Alternate-Location återställning (återställning till).

- Azure Backup för Managed Disks använder stegvisa ögonblicks bilder, som är begränsade till 200 ögonblicks bilder per disk. För att du ska kunna ta säkerhets kopior på begäran från schemalagda säkerhets kopieringar begränsar säkerhets kopierings principen den totala säkerhets kopian till 180. Läs mer om [stegvis ögonblicks bild](../virtual-machines/disks-incremental-snapshots.md#restrictions) för hanterade diskar.

- Azure [-prenumeration och tjänst begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machine-disk-limits) gäller för det totala antalet disk ögonblicks bilder per region per prenumeration.

- Ögonblicks bilder av flera diskar som är anslutna till en virtuell dator stöds inte.

- Säkerhets kopierings valvet och diskarna som ska säkerhets kopie ras kan vara i samma eller olika prenumerationer. Men både det säkerhets kopierings valv och den disk som ska säkerhets kopie ras måste vara i samma region.

- Diskar som ska säkerhets kopie ras och resurs gruppen för ögonblicks bilder där ögonblicks bilderna lagras lokalt måste finnas i samma prenumeration.

- Det finns stöd för att återställa en disk från en säkerhets kopia till samma eller en annan prenumeration. Den återställda disken kommer dock att skapas i samma region som ögonblicks bilden.

- Det går inte att skydda ADE-krypterade diskar.

- Diskar som är krypterade med plattforms hanterade nycklar eller kund hanterade nycklar stöds. Återställningen kommer dock inte att fungera för återställnings punkterna på en disk som är krypterad med Kundhanterade nycklar (CMK) om nyckel Valvs nyckeln för disk krypterings uppsättningen är inaktive rad eller borttagen.

- För närvarande går det inte att ändra säkerhets kopierings principen och resurs gruppen för ögonblicks bilder som har tilldelats till en säkerhets kopierings instans när du konfigurerar säkerhets kopieringen av en disk kan inte ändras.

- För närvarande är Azure Portal upplevelsen för att konfigurera säkerhets kopieringen av diskar begränsad till högst 20 diskar från samma prenumeration.

- Azure disk Backup har stöd för PowerShell. Azure CLI stöds för närvarande inte.

- När du konfigurerar säkerhets kopiering måste disken som du har valt att säkerhets kopie ras och resurs gruppen för ögonblicks bilder där ögonblicks bilderna ska lagras vara en del av samma prenumeration. Du kan inte skapa en stegvis ögonblicks bild för en viss disk utanför diskens prenumeration. Läs mer om [stegvisa ögonblicks bilder](../virtual-machines/disks-incremental-snapshots.md#restrictions) för hanterad disk. Mer information om hur du väljer en resurs grupp för ögonblicks bilder finns i  [Konfigurera säkerhets kopiering](backup-managed-disks.md#configure-backup).

- För lyckade säkerhets kopierings-och återställnings åtgärder krävs roll tilldelningar av säkerhets kopierings valvets hanterade identitet. Använd endast de roll definitioner som anges i dokumentationen. Det finns inte stöd för användning av andra roller som ägare, deltagare och så vidare. Du kan få problem med behörigheten om du börjar konfigurera säkerhets kopierings-eller återställnings åtgärder efter tilldelningen av roller. Detta beror på att roll tilldelningarna tar några minuter att börja gälla.

- Managed disks tillåter att prestanda nivån ändras vid distribution eller efteråt utan att storleken på disken ändras. Lösningen för säkerhets kopiering av Azure-disk stöder prestanda nivå ändringar till käll disken som säkerhets kopie ras. Under återställningen är prestanda nivån för den återställda disken densamma som käll disken vid säkerhets kopieringen. Följ dokumentationen [här](../virtual-machines/disks-performance-tiers-portal.md) för att ändra diskens prestanda nivå efter återställnings åtgärden.

- Med stöd för [privata länkar](../virtual-machines/disks-enable-private-links-for-import-export-portal.md) för hanterade diskar kan du begränsa exporten och importen av hanterade diskar så att det bara sker i det virtuella Azure-nätverket. Säkerhets kopiering av Azure-diskar har stöd för säkerhets kopiering av diskar som har privata slut punkter aktiverade. Detta inkluderar inte säkerhets kopierings data eller ögonblicks bilder som kan nås via den privata slut punkten.

- Du kan ta bort en säkerhets kopierings instans, vilket stoppar säkerhets kopieringen och tar också bort alla säkerhetskopierade data. För närvarande kan du inte inaktivera en säkerhets kopia eftersom alternativet **stoppa säkerhets kopiering och behåll säkerhets kopierings data** inte stöds.

## <a name="next-steps"></a>Nästa steg

- [Säkerhetskopiera Azure Managed Disks](backup-managed-disks.md)