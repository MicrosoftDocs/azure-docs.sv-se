---
title: Översikt över säkerhets kopiering av Azure-diskar
description: Lär dig mer om Azure disk backup-lösningen.
ms.topic: conceptual
ms.date: 04/09/2021
ms.openlocfilehash: 42f37c1f500be719e0bd79bad41226ab3ab2d911
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285147"
---
# <a name="overview-of-azure-disk-backup"></a>Översikt över säkerhets kopiering av Azure-diskar

Azure disk Backup är en inbyggd, molnbaserad säkerhets kopierings lösning som skyddar dina data i Managed disks. Det är en enkel, säker och kostnads effektiv lösning som gör att du kan konfigurera skydd för hanterade diskar med några få steg. Det garanterar att du kan återställa dina data i ett katastrof scenario.

Azure disk Backup erbjuder en nyckel färdig lösning som tillhandahåller hantering av ögonblicks bilder för hanterade diskar genom att automatisera regelbunden generering av ögonblicks bilder och behålla den för konfigurerad varaktighet med hjälp av säkerhets kopierings principen. Du kan hantera disk ögonblicks bilderna med noll som infrastruktur kostnader och utan behov av anpassade skript eller hanterings kostnader. Detta är en krasch lösning för säkerhets kopiering som tar säkerhets kopiering av en hanterad disk vid en viss tidpunkt med hjälp av [stegvisa ögonblicks bilder](../virtual-machines/disks-incremental-snapshots.md) med stöd för flera säkerhets kopior per dag. Det är också en agent lös lösning som inte påverkar prestanda för produktions program. Den har stöd för säkerhets kopiering och återställning av både OS-och data diskar (inklusive delade diskar), oavsett om de är kopplade till en virtuell Azure-dator som körs eller inte.

Om du behöver programkonsekvent säkerhets kopiering av virtuell dator, inklusive data diskarna, eller ett alternativ för att återställa en hel virtuell dator från en säkerhets kopia, återställa en fil eller mapp, eller återställa till en sekundär region, använder du lösningen för [säkerhets kopiering](backup-azure-vms-introduction.md) av virtuella Azure-datorer. Azure Backup erbjuder sida-vid-sida-stöd för säkerhets kopiering av hanterade diskar med hjälp av disk säkerhets kopiering förutom [Azure VM backup](./backup-azure-vms-introduction.md) -lösningar. Detta är användbart när du behöver en gång per dag program som är konsekventa med säkerhets kopiering av virtuella datorer och även oftare säkerhets kopiering av OS-diskar eller en specifik datadisk som är kraschad och som inte påverkar prestandan för produktions programmet.

Säkerhets kopiering av Azure-diskar är integrerat i Backup Center, som ger en **enhetlig hanterings upplevelse** i Azure för företag för att styra, övervaka, hantera och analysera säkerhets kopior i stor skala.

## <a name="key-benefits-of-disk-backup"></a>Viktiga fördelar med disk säkerhets kopiering

Säkerhets kopiering av Azure-diskar är en lösnings löst och krasch lösning som använder [stegvisa ögonblicks bilder](../virtual-machines/disks-incremental-snapshots.md) och ger följande fördelar:

- Mer frekvent och snabb säkerhets kopiering utan att avbryta den virtuella datorn.
- Påverkar inte produktions programmets prestanda.
- Inga säkerhets problem eftersom det inte krävs att köra anpassade skript eller installera agenter.
- En kostnads effektiv lösning för att säkerhetskopiera vissa diskar jämfört med att säkerhetskopiera hela den virtuella datorn.

Lösning för säkerhets kopiering av Azure-disk är användbar i följande scenarier:

- Behovet av frekventa säkerhets kopieringar per dag utan att program vara quiescent.
- Appar som körs i ett kluster scenario: både Windows Server-redundanskluster och Linux-kluster skrivs till delade diskar.
- Specifika behov av säkerhets kopiering utan agent på grund av säkerhets-eller prestanda problem i programmet.
- Programkonsekvent säkerhets kopiering av virtuell dator är inte möjligt eftersom branschspecifika appar inte stöder tjänsten Volume Shadow Copy (VSS).

Överväg säkerhets kopiering av Azure-diskar i scenarier där:

- Ett verksamhets kritiskt program körs på en virtuell Azure-dator som kräver flera säkerhets kopieringar per dag för att uppfylla återställnings punkt målet, men utan att påverka produktions miljön eller program prestandan.
- Organisationen eller bransch förordningen begränsar installationen av agenter på grund av säkerhets problem.
- Körning av anpassade för-eller post-skript och aktivering av frysning och Tina på virtuella Linux-datorer för att få programkonsekvent säkerhets kopiering ger onödigt höga kostnader för tillgänglighet för produktions belastningen.
- Behållar program som körs på Azure Kubernetes service (AKS-kluster) använder hanterade diskar som beständiga lagrings enheter. Idag måste du säkerhetskopiera den hanterade disken via Automation-skript som är svåra att hantera.
- En hanterad disk innehåller kritiska affärs data som används som en fil resurs eller innehåller säkerhets kopior av databasen, och du vill optimera säkerhets kopierings kostnaden genom att inte investera i Azure VM-säkerhetskopiering.
- Du har många virtuella Linux-och Windows-datorer med enkel disk (det vill säga en virtuell dator med bara en OS-disk och inga data diskar anslutna) som är värd för webb servern, tillstånds lösa datorer eller fungerar som en mellanlagrings miljö med program konfigurations inställningar och du behöver en kostnads effektiv lösning för säkerhets kopiering för att skydda operativ system disken. Till exempel för att utlösa en snabb säkerhets kopiering på begäran innan du uppgraderar eller korrigerar den virtuella datorn.
- En virtuell dator kör en OS-konfiguration som inte stöds av Azure VM backup-lösningen (till exempel Windows 2008 32-bitars server).

## <a name="how-the-backup-and-restore-process-works"></a>Så här fungerar säkerhets kopierings-och återställnings processen

- Det första steget i att konfigurera säkerhets kopiering för Azure Managed disks skapar ett [säkerhets kopierings valv](backup-vault-overview.md). Valvet ger dig en samlad vy över de säkerhets kopior som kon figurer ATS för olika arbets belastningar.

- Skapa sedan en säkerhets kopierings princip som gör att du kan konfigurera säkerhets kopierings frekvens och varaktighet för kvarhållning.

- Om du vill konfigurera säkerhets kopiering går du till säkerhets kopierings valvet, tilldelar en princip för säkerhets kopiering, väljer den hanterade disk som behöver säkerhets kopie ras och anger en resurs grupp där ögonblicks bilderna ska lagras och hanteras. Azure Backup automatiskt utlöser schemalagda säkerhets kopierings jobb som skapar en stegvis ögonblicks bild av disken enligt säkerhets kopierings frekvensen. Äldre ögonblicks bilder tas bort i enlighet med den Retentions tid som anges av säkerhets kopierings principen.

- Azure Backup använder [stegvisa ögonblicks bilder](../virtual-machines/disks-incremental-snapshots.md#restrictions) av den hanterade disken. Stegvisa ögonblicks bilder är en kostnads effektiv säkerhets kopia av hanterade diskar som faktureras för förändrings ändringarna på disken sedan den senaste ögonblicks bilden. Dessa lagras alltid på den mest kostnads effektiva lagrings platsen, standard-HDD-lagring oavsett lagrings typ för de överordnade diskarna. Den första ögonblicks bilden av disken upptar diskens använda storlek och i följd stegvisa ögonblicks bilder lagrar delta ändringar på disken sedan den senaste ögonblicks bilden.

- När du har konfigurerat säkerhets kopian av en hanterad disk skapas en säkerhets kopierings instans i säkerhets kopierings valvet. Med hjälp av säkerhets kopierings instansen kan du hitta hälso tillståndet för säkerhets kopierings åtgärder, utlösa säkerhets kopior på begäran och utföra återställnings åtgärder. Du kan också Visa hälso tillståndet för säkerhets kopieringar i flera valv och säkerhets kopierings instanser med Backup Center, som ger en enda ruta i glas visning.

- Om du vill återställa väljer du bara den återställnings punkt från vilken du vill återställa disken. Ange resurs gruppen där den återställda disken ska skapas från ögonblicks bilden. Azure Backup ger en omedelbar återställnings upplevelse eftersom ögonblicks bilderna lagras lokalt i din prenumeration.

- Säkerhets kopierings valv använder hanterad identitet för att få åtkomst till andra Azure-resurser. Om du vill konfigurera säkerhets kopiering av en hanterad disk och återställa från tidigare säkerhets kopiering, kräver säkerhets kopierings valvets hanterade identitet en uppsättning behörigheter på käll disken, resurs gruppen för ögonblicks bilder där ögonblicks bilder skapas och hanteras och mål resurs gruppen där du vill återställa säkerhets kopian. Du kan bevilja behörighet till den hanterade identiteten med hjälp av rollbaserad åtkomst kontroll i Azure (Azure RBAC). Hanterad identitet är ett tjänst objekt av en särskild typ som bara kan användas med Azure-resurser. Läs mer om [hanterade identiteter](../active-directory/managed-identities-azure-resources/overview.md).

- För närvarande har Azure disk backup stöd för drift säkerhets kopiering av hanterade diskar och kopierar inte säkerhets kopior till lagring med säkerhets kopierings valv. I [support mat ris](disk-backup-support-matrix.md) finns en detaljerad lista över scenarier som stöds och som inte stöds och regions tillgänglighet.

## <a name="pricing"></a>Priser

Azure Backup erbjuder en hanterings lösning för ögonblicks bilder för att skydda Azure-diskar. Disk ögonblicks bilderna som skapats av Azure Backup lagras i resurs gruppen i din Azure-prenumeration och debiterar **lagrings avgifter för ögonblicks bilder** . Du kan gå till [priser för Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/) om du vill ha mer information om priserna för ögonblicks bilder.<br></br>Eftersom ögonblicks bilderna inte kopieras till säkerhets kopierings valvet debiteras Azure Backup inte en **skyddad instans** avgift och kostnaden för **lagring av säkerhets kopior** gäller inte. Dessutom kan stegvisa ögonblicks bilder uppta delta ändringar som den senaste ögonblicks bilden och lagras alltid i standard lagring oavsett lagrings typ för de överordnade hanterade diskarna och debiteras enligt priserna för standard lagring. Detta gör Azure disk backup till en kostnads effektiv lösning.

## <a name="next-steps"></a>Nästa steg

[Stödmatris för säkerhetskopiering av Azure-disk](disk-backup-support-matrix.md)
