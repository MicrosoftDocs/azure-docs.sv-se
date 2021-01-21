---
title: Stödmatris för säkerhetskopiering av Azure-disk
description: Innehåller en sammanfattning av support inställningar och begränsningar för säkerhets kopiering av Azure-diskar.
ms.topic: conceptual
ms.date: 01/07/2021
ms.custom: references_regions
ms.openlocfilehash: 099e83d8a2fb109da862657265dad8be8143f608
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624942"
---
# <a name="azure-disk-backup-support-matrix-in-preview"></a>Support mat ris för Azure disk Backup (för hands version)

>[!IMPORTANT]
>Azure disk Backup är i för hands version utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
>[Fyll i det här formuläret](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) om du vill registrera dig för för hands versionen.

Du kan använda [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) för att skydda Azure-diskar. Den här artikeln sammanfattar regions tillgänglighet, scenarier som stöds och begränsningar.

## <a name="supported-regions"></a>Regioner som stöds

Azure disk Backup är tillgängligt i för hands version i följande regioner: västra centrala USA. 

Fler regioner kommer att meddelas när de blir tillgängliga.

## <a name="limitations"></a>Begränsningar

- Azure disk backup stöds för Azure Managed Disks, inklusive delade diskar (Shared Premium SSD). Ohanterade diskar stöds inte. För närvarande stöder den här lösningen extremt diskar, inklusive delade Ultra disks, på grund av brist på ögonblicks bilds kapacitet.

- Säkerhets kopiering av Azure-diskar har stöd för säkerhets kopiering av Skrivningsaccelerator disk. Men under återställningen skulle disken återställas som en normal disk. Skriv accelererat cacheminne kan aktive ras på disken när den har monterats på en virtuell dator.

- Azure Backup tillhandahåller säkerhets kopiering (ögonblicks bild) av Azure Managed disks med stöd för flera säkerhets kopior per dag. Säkerhets kopiorna kopieras inte till säkerhets kopierings valvet.

- För närvarande stöds inte alternativet Original-Location återställning (OLR) som ska återställas genom att befintliga käll diskar ersätts från den plats där säkerhets kopiorna gjordes. Du kan återställa från återställnings punkt för att skapa en ny disk antingen i samma resurs grupp som käll disken från vilken säkerhets kopian gjordes eller i någon annan resurs grupp. Detta kallas Alternate-Location återställning (återställning till).

- Azure Backup för Managed Disks använder stegvisa ögonblicks bilder, som är begränsade till 200 ögonblicks bilder per disk. För att du ska kunna ta säkerhets kopior på begäran från schemalagda säkerhets kopieringar begränsar säkerhets kopierings principen den totala säkerhets kopian till 180. Läs mer om [stegvis ögonblicks bild](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal#restrictions) för hanterade diskar.

- Azure [-prenumeration och tjänst begränsningar](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#virtual-machine-disk-limits) gäller för det totala antalet disk ögonblicks bilder per region per prenumeration.

- Ögonblicks bilder av flera diskar som är anslutna till en virtuell dator stöds inte.

- Säkerhets kopierings valvet och diskarna som ska säkerhets kopie ras kan vara i samma eller olika prenumerationer. Men både det säkerhets kopierings valv och den disk som ska säkerhets kopie ras måste vara i samma region.

- Diskar som ska säkerhets kopie ras och resurs gruppen för ögonblicks bilder där ögonblicks bilderna lagras lokalt måste finnas i samma prenumeration.

- Det finns stöd för att återställa en disk från en säkerhets kopia till samma eller en annan prenumeration. Den återställda disken kommer dock att skapas i samma region som ögonblicks bilden.

- Det går inte att skydda ADE-krypterade diskar.

- Diskar som är krypterade med plattforms hanterade nycklar eller kund hanterade nycklar stöds. Återställningen kommer dock inte att fungera för återställnings punkterna på en disk som är krypterad med Kundhanterade nycklar (CMK) om nyckel Valvs nyckeln för disk krypterings uppsättningen är inaktive rad eller borttagen.

- För närvarande går det inte att ändra säkerhets kopierings principen och resurs gruppen för ögonblicks bilder som har tilldelats till en säkerhets kopierings instans när du konfigurerar säkerhets kopieringen av en disk kan inte ändras.

- För närvarande är Azure Portal upplevelsen för att konfigurera säkerhets kopieringen av diskar begränsad till högst 20 diskar från samma prenumeration.

- För närvarande (under för hands versionen) stöds inte användning av PowerShell och Azure CLI för att konfigurera säkerhets kopiering och återställning av diskar.

- När du konfigurerar säkerhets kopiering måste disken som du har valt att säkerhets kopie ras och resurs gruppen för ögonblicks bilder där ögonblicks bilderna ska lagras vara en del av samma prenumeration. Du kan inte skapa en stegvis ögonblicks bild för en viss disk utanför diskens prenumeration. Läs mer om [stegvisa ögonblicks bilder](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal#restrictions) för hanterad disk. Mer information om hur du väljer en resurs grupp för ögonblicks bilder finns i  [Konfigurera säkerhets kopiering](backup-managed-disks.md#configure-backup).

- För lyckade säkerhets kopierings-och återställnings åtgärder krävs roll tilldelningar av säkerhets kopierings valvets hanterade identitet. Använd endast de roll definitioner som anges i dokumentationen. Det finns inte stöd för användning av andra roller som ägare, deltagare och så vidare. Du kan få problem med behörigheten om du börjar konfigurera säkerhets kopierings-eller återställnings åtgärder efter tilldelningen av roller. Detta beror på att roll tilldelningarna tar några minuter att börja gälla.

- Managed disks tillåter att prestanda nivån ändras vid distribution eller efteråt utan att storleken på disken ändras. Lösningen för säkerhets kopiering av Azure-disk stöder prestanda nivå ändringar till käll disken som säkerhets kopie ras. Under återställningen är prestanda nivån för den återställda disken densamma som käll disken vid säkerhets kopieringen. Följ dokumentationen [här](https://docs.microsoft.com/azure/virtual-machines/disks-performance-tiers-portal) för att ändra diskens prestanda nivå efter återställnings åtgärden.

- Med stöd för [privata länkar](https://docs.microsoft.com/azure/virtual-machines/disks-enable-private-links-for-import-export-portal) för hanterade diskar kan du begränsa exporten och importen av hanterade diskar så att det bara sker i det virtuella Azure-nätverket. Säkerhets kopiering av Azure-diskar har stöd för säkerhets kopiering av diskar som har privata slut punkter aktiverade. Detta inkluderar inte säkerhets kopierings data eller ögonblicks bilder som kan nås via den privata slut punkten.

## <a name="next-steps"></a>Nästa steg

- [Säkerhetskopiera Azure Managed Disks](backup-managed-disks.md)
