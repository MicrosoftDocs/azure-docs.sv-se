---
title: Alternativ för datamigrering från StorSimple 8000-serie enheter
description: Innehåller en översikt över alternativen för att migrera data från StorSimple 8000-serien.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 03/25/2020
ms.author: alkohli
ms.openlocfilehash: 4839f8211e678f5fc2fb3572c7eaa545fbee6c6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94961200"
---
# <a name="options-to-migrate-data-from-storsimple-8000-series"></a>Alternativ för att migrera data från StorSimple 8000-serien

> [!IMPORTANT]
> Den 31 december 2022 kommer StorSimple 8000-serien att uppnå status för slut på support (EOS). Vi rekommenderar att StorSimple 8000-seriens kunder migrerar till något av de alternativ som beskrivs i dokumentet.

StorSimple 8000-serien når [slutet av stödet](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) i december 2022. Kunder som kör StorSimple 8000-serien har ett alternativ för att uppgradera till andra Azures hybrid tjänster från första part. I den här artikeln beskrivs de Azure Hybrid-alternativ som finns tillgängliga för att migrera data.

## <a name="migration-options"></a>Migreringsalternativ

Kunderna som använder StorSimple 8000-serien har alternativ för Azure eller tredje part.

### <a name="azure-options"></a>Azure-alternativ

#### <a name="migrate-to-azure-file-sync"></a>Migrera till Azure File Sync

Det här nya migrerings alternativet gör att kunder kan lagra sin organisations fil resurser i Azure Files. Dessa fil resurser centraliseras sedan för lokal åtkomst med hjälp av Azure File Sync (AFS). AFS kan distribueras på en Windows Server-värd. Den faktiska datamigreringen utförs sedan som en värd kopia eller med migrations verktyget.

Mer information om hur du migrerar data till Azure File Sync finns i [StorSimple 8100 och 8600 migrering till Azure File Sync](../storage/files/storage-files-migration-storsimple-8000.md).

### <a name="third-party-options"></a>Alternativ från tredje part

#### <a name="migrate-to-panzura-freedom-nas"></a>Migrera till Panzura frihet NAS

Kunder med StorSimple 5000-7000-serien och StorSimple 8000-serien kan välja att migrera till Panzura frihet som NAS ska behålla sina data i Azure. Lösningen Panzura frihet är en NAS-lösning som omfattar data Center, kontor, offentliga och privata moln. Lösningen möjliggör lokala, hybrid och molnbaserade data arbets flöden för NFS, SMB och mobila klienter.

Den här migreringen stöds av Panzura och kunder kan komma igång genom att begära stöd för migrering från [Panzura-webbplatsen](https://panzura.com/migrate-storsimple-panzura/).

#### <a name="migrate-to-nasuni"></a>Migrera till Nasuni

Att flytta hela StorSimple-miljön till en stabil och säker plattform för fil tjänster med höga prestanda är enkelt med Nasuni. Nasuni erbjuder säkerhet och prestanda för lokala fil lagrings enheter samtidigt som de kombineras med skalbarhet och tålighet i Azure.  Som en ledande Azure-oberoende program varu leverantör (ISV), tar Nasuni alla verktyg som behövs för att flytta dina StorSimple-data till en modern plattform som gör det möjligt att dela och samar beta med dina filer på flera platser.

Kom igång idag: [Nasuni webbplats](https://info.nasuni.com/storsimple8000-webinar).

<!-- 04/09/2020 v-grpr (priestlg) - As per request, commenting out this section because the information that will go into this section is forthcoming
#### Migrate to Cohesity

Cohesity enables you to migrate data from your current StorSimple 5000–7000 to the Cohesity Data Platform on Azure. The Cohesity Data Platform is a software-defined web-scale solution that consolidates files, backups, objects, and VMs onto a single cloud-native solution. After migration to the Data Platform, you can manage, protect, and provision data and apps from cloud to core through a single pane of glass. With Cohesity, start with as few as three nodes. 

Learn more on [migration to the Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### Migrate to Nasuni

Nasuni makes it easy for StorSimple 5000-7000 customers to migrate and keep their data in Azure.  Nasuni is a leading Azure-based NAS storage solution, giving customers the performance and security they expect from on-prem solutions, with cloud economics and scale.  In addition to high performance file storage, Nasuni and Azure handle backup and DR, while allowing you to share and collaborate on your data around the globe with centralized file storage management. 

Nasuni has the experience to make your migration easy – get started today: https://info.nasuni.com/nasuni-storsimple-migration

#### Migrate to Talon FAST

Talon makes it easy for StorSimple 5000-7000 customers to continue to leverage the benefits they valued so much in the StorSimple platform (small on-site footprint backed by unlimited cloud resources) with even greater function.  With the Talon FAST solution, customers can migrate and keep their data in Azure, while now having an even smaller software-only onsite footprint and adding benefits such as global file locking, global namespace, and multi-site collaboration.  Talon is a leading Azure ecosystem solution, working with global customers to migrate their on-premises file server workloads into a consolidated, Azure-based footprint without compromising user workflow or experience.  

Learn more about how to evolve to a cloud-consolidated enterprise at https://www.talonstorage.com/alliances/microsoft-storsimple.
-->

## <a name="migration---frequently-asked-questions"></a>Migrering – vanliga frågor och svar

### <a name="q-when-do-the-storsimple-8000-series-devices-reach-end-of-service"></a>F. När når StorSimple 8000-serie enheterna tjänstens slut?

A. StorSimple 8000-serien når [Supportens slut](https://support.microsoft.com/[lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) i december 2022. Slutet av supporten innebär att Microsoft inte längre kommer att kunna tillhandahålla stöd för både maskin-och program vara för dessa enheter efter 2022 december. Vi rekommenderar starkt att du börjar utforma en plan för att migrera data från dina enheter nu.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>F. Vad händer med de data som jag har lagrat i Azure?  

A. Du kan fortsätta att använda data i Azure när du migrerar den till en nyare tjänst.

### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>F. Vad händer med de data som jag har lagrat lokalt på min StorSimple-enhet?

A. De data som finns på den lokala enheten kan kopieras till den nyare tjänsten enligt beskrivningen i migrerings dokumenten.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-8000-series-appliance"></a>F. Vad händer om jag vill behålla min StorSimple 8000-serieprogram?

A. Även om tjänsterna kan fortsätta att fungera kommer Microsoft inte längre att kunna tillhandahålla stöd för maskin-och program vara. Migrering rekommenderas starkt för affärs kontinuitet.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-8000-series-devices"></a>F. Vilka alternativ finns det för att migrera data från StorSimple 8000-seriens enheter?

A. Beroende på sitt scenario har StorSimple 8000-serien användare följande alternativ för migrering:

* **Migrera till Azure File Sync**: Använd det här alternativet om du vill växla till det inbyggda Azure-formatet. Du kan använda Azure File Sync för centraliserad hantering av fil resurser.

* **Andra alternativ**: du kan kontakta Microsoft Support för att diskutera de migreringsåtgärder som inte listas här.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>F. Stöds migrering till andra lagrings lösningar?

A. Ja. Migrering till andra lagrings lösningar med hjälp av värd kopia av data stöds.

### <a name="q-is-migration-supported-by-microsoft"></a>F. Stöds migrering av Microsoft?

A. Migrering från 8000-serien är en åtgärd som stöds fullt ut. Microsoft rekommenderar faktiskt att vi når ut till support innan du påbörjar migreringen. Migreringen är för närvarande en åtgärd som kan åtgärdas. [Kontakta StorSimple-supporten](mailto:storsimp@microsoft.com)om du vill migrera data från din enhet för StorSimple 8000-serien.

### <a name="q-what-is-the-pricing-model-for-migration-to-azure-file-sync"></a>F. Vad är pris sättnings modellen för migrering till Azure File Sync?

A. När du använder Azure File Sync kan prenumerations avgifterna för tjänsten tillkomma. Kunderna måste också betala kontinuerliga lagrings kostnader. Se pris information för [AFS]( https://azure.microsoft.com/pricing/details/storage/files/) för en uppskattning.

### <a name="q-how-long-does-it-take-to-complete-a-migration"></a>F. Hur lång tid tar det att slutföra en migrering?

A. Hur lång tid det tar att migrera data beror på mängden data och uppgraderings alternativet har valts.

## <a name="next-steps"></a>Nästa steg

* [Migrera data från en StorSimple 8000-serie till Azure File Sync](../storage/files/storage-files-migration-storsimple-8000.md)