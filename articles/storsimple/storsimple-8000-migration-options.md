---
title: Alternativ för datamigrering från StorSimple 5000-7000-serie enheter
description: Innehåller en översikt över alternativen för att migrera data från StorSimple 5000-7000-serien.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: cc67b678fabeb2a7878490a4ea713acb5709cdbc
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643801"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Alternativ för att migrera data från StorSimple 5000-7000-serien 

> [!IMPORTANT]
> Den 9 juli 2019 når StorSimple 5000/7000-serien status för slut på support (EOS). Vi rekommenderar att StorSimple 5000/7000-seriens kunder migrerar till något av de alternativ som beskrivs i dokumentet.

StorSimple 5000-7000-serien når [slutet av stödet](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) i juli 2019. Kunder som kör StorSimple 5000-7000-serien har ett alternativ för att uppgradera till andra Azures hybrid tjänster från första part. I den här artikeln beskrivs de Azure Hybrid-alternativ som finns tillgängliga för att migrera data. 

## <a name="migration-options"></a>Migreringsalternativ

Kunderna som använder StorSimple 5000-7000-serien har alternativ för Azure eller tredje part.

### <a name="azure-options"></a>Azure-alternativ

#### <a name="upgrade-to-storsimple-8000-series"></a>Uppgradera till StorSimple 8000-serien

Uppgradera till StorSimple 8000-serien och fortsätt därmed till StorSimple-plattformen.  Den här uppgraderings vägen kräver att kunderna ersätter sina 5000-7000 serie enheter med en 8000-serie. Data migreras från 5000-7000-seriens enhet med hjälp av Migreringsverktyget. När migreringen har slutförts fortsätter StorSimple 8000-serie enheterna att gå med i nivå data till Azure Blob Storage. 

Mer information om hur du migrerar data med hjälp av en StorSimple 8000-serie finns i [migrera data från StorSimple 5000-7000-serien till 8000-serien het](storsimple-8000-migrate-from-5000-7000.md).

#### <a name="migrate-to-azure-file-sync"></a>Migrera till Azure File Sync

Det här nya migrerings alternativet gör att kunder kan lagra sin organisations fil resurser i Azure Files. Dessa fil resurser centraliseras sedan för lokal åtkomst med hjälp av Azure File Sync (AFS). AFS kan distribueras på en Windows Server-värd. Den faktiska datamigreringen utförs sedan som en värd kopia eller med migrations verktyget.

Mer information om hur du migrerar data till Azure File Sync finns i [migrera data från StorSimple 5000-7000-serien till Azure File Sync](../storage/files/storage-files-migration-storsimple-8000.md).

### <a name="third-party-options"></a>Alternativ från tredje part

#### <a name="migrate-to-panzura-freedom-nas"></a>Migrera till Panzura frihet NAS

StorSimple 5000-7000-kunder kan välja att migrera till Panzura frihet NAS för att skydda sina data i Azure. Panzura frihets lösning tillhandahåller en NAS-lösning som omfattar data Center, kontor, offentliga och privata moln. Lösningen möjliggör lokala, hybrid och molnbaserade data arbets flöden för NFS, SMB och mobila klienter. 

Den här migreringen stöds av Panzura och kunder kan komma igång genom att begära stöd för migrering från [Panzura-webbplatsen](https://panzura.com/storsimple-migration/).

#### <a name="migrate-to-cohesity"></a>Migrera till Cohesity

Med Cohesity kan du migrera data från din nuvarande StorSimple 5000 – 7000 till Cohesity Data Platform på Azure. Cohesity Data Platform är en programdefinierad webb skalnings lösning som konsoliderar filer, säkerhets kopior, objekt och virtuella datorer till en enda molnbaserad lösning. Efter migreringen till data plattformen kan du hantera, skydda och etablera data och appar från molnet till kärnan genom ett enda fönster i glaset. Med Cohesity börjar du med så få som tre noder. 

Läs mer om [migrering till Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### <a name="migrate-to-nasuni"></a>Migrera till Nasuni

Nasuni gör det enkelt för StorSimple 5000-7000-kunder att migrera och bevara sina data i Azure.  Nasuni är en ledande Azure-baserad lösning för NAS-lagring, vilket ger kunderna den prestanda och säkerhet som de förväntar sig från lokal lösningar, med tanke på utveckling och skalbarhet i molnet.  Förutom fil lagring med hög prestanda, Nasuni och Azure-säkerhetskopiering och DR, samtidigt som du kan dela och samar beta med dina data i hela världen med centraliserad hantering av fil lagring. 

Nasuni har erfarenhet av att göra migreringen lätt – kom igång redan idag: https://www.nasuni.com/blog-migrating-off-storsimple/

#### <a name="migrate-to-talon-fast"></a>Migrera till Talon FAST

Talon gör det enkelt för StorSimple 5000-7000-kunder att fortsätta att utnyttja de fördelar som de har för mycket på StorSimple-plattformen (små på plats som backas upp av obegränsade moln resurser) med ännu större funktion.  Med den snabba lösningen för Talon kan kunderna migrera och bevara sina data i Azure, samtidigt som du har en ännu mindre plats på plats och lägger till fördelar som global fil låsning, globalt namn område och samarbete på flera platser.  Talon är en ledande lösning för Azure eko system, som arbetar med globala kunder för att migrera sina lokala arbets belastningar för fil servrar till ett konsoliderat, Azure-baserat utrymme utan att kompromissa med användar arbets flödet eller erfarenheten.  

Lär dig mer om hur du utvecklar till ett Cloud-konsoliderat företag.

## <a name="migration---frequently-asked-questions"></a>Migrering – vanliga frågor och svar

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>F. När når StorSimple 5000-och 7000-serie enheterna tjänstens slut? 

A. StorSimple 5000-7000-serien når [tjänstens slut](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) i juli 2019. Tjänstens slut innebär att Microsoft inte längre kommer att kunna tillhandahålla stöd för både maskin-och program vara för dessa enheter efter 2019 juli. Vi rekommenderar starkt att du börjar utforma en plan för att migrera data från dina enheter nu.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>F. Vad händer med de data som jag har lagrat i Azure?  

A. Du kan fortsätta att använda data i Azure när du migrerar den till en nyare tjänst. 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>F. Vad händer med de data som jag har lagrat lokalt på min StorSimple-enhet? 

A. De data som finns på den lokala enheten kan kopieras till den nyare tjänsten enligt beskrivningen i migrerings dokumenten.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>F. Vad händer om jag vill behålla min StorSimple 5000/7000-serieprogram? 

A. Även om tjänsterna kan fortsätta att fungera kommer Microsoft inte längre att kunna tillhandahålla stöd för maskin-och program vara. Migrering rekommenderas starkt för affärs kontinuitet.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>F. Vilka alternativ finns det för att migrera data från StorSimple 5000-7000-seriens enheter? 

A. Beroende på sitt scenario har StorSimple 5000-7000-serien användare följande alternativ för migrering. 

 - **Uppgradera till 8000-serien**: Använd det här alternativet om du vill fortsätta med StorSimple-plattformen. 
 - **Migrera till Azure File Sync**: Använd det här alternativet om du vill växla till det inbyggda Azure-formatet. Du kan använda Azure File Sync för centraliserad hantering av fil resurser. 

Du kan kontakta Microsoft Support för att diskutera flyttnings alternativ som inte visas här.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>F. Stöds migrering till andra lagrings lösningar?

A. Ja. Migrering till andra lagrings lösningar med hjälp av värd kopia av data stöds.

### <a name="q-is-migration-supported-by-microsoft"></a>F. Stöds migrering av Microsoft? 

A. Migrering från 5000-eller 7000-serien är en åtgärd som stöds fullt ut. Microsoft rekommenderar faktiskt att vi når ut till support innan du påbörjar migreringen. Migreringen är för närvarande en åtgärd som kan åtgärdas. Om du planerar att migrera data från din StorSimple 5000-7000-serie enhet [öppnar du ett support ärende](storsimple-8000-contact-microsoft-support.md).

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>F. Vad är pris sättnings modellen för båda alternativen för migrering?

A. Kostnaderna för migrering varierar beroende på vilket alternativ du väljer. När själva migreringen är kostnads fri, om du bestämmer dig för att uppgradera till en StorSimple 8000-serien, blir kostnaden för maskin varu enheten. 

På samma sätt kan prenumerations avgifterna för tjänsten gälla när du använder Azure File Sync. I varje fall måste kunderna också betala kontinuerliga lagrings kostnader. Se följande för en uppskattning: 
- [StorSimple-priser](https://azure.microsoft.com/pricing/details/storsimple/)  
- [AFS-prissättning]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>F.  Hur lång tid tar det att slutföra en migrering?

A. Hur lång tid det tar att migrera data beror på mängden data och uppgraderings alternativet har valts. 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>F. Vad är slutet på support datumet för StorSimple 8000-serien?

A. Slutet på support datumet för StorSimple 8000-serien publiceras [här](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).


## <a name="next-steps"></a>Nästa steg
 - [Migrera data från en StorSimple 5000-7000-serie till en enhet med 8000-serien](storsimple-8000-migrate-from-5000-7000.md).
 - [Migrera data från en StorSimple 5000-7000-serie till Azure File Sync](../storage/files/storage-files-migration-storsimple-8000.md)
