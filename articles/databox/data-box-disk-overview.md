---
title: Microsoft Azure Data Box Disk – översikt| Microsoft Docs i data
description: Beskriver Azure Data Box Disk, en molnlösning som gör det möjligt att överföra stora mängder data till Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: ca46ce3355edf2b77400011d023cd6af330a3b58
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770441"
---
# <a name="what-is-azure-data-box-disk"></a>Vad är Azure Data Box Disk?

Med Microsoft Azure Data Box Disk-lösningen kan du skicka terabyte med lokala data till Azure på ett snabbt, kostnadseffektivt och tillförlitligt sätt. Den säkra dataöverföringen görs snabbare eftersom 1 till 5 SSD-diskar (solid state-hårddisk) levereras till dig. Dessa krypterade diskar på 8 TB skickas till ditt datacenter via ett regionalt transportföretag.

Du kan snabbt konfigurera, ansluta och låsa upp diskarna via Data Box-enhet i Azure Portal. Kopiera dina data till diskar och skicka tillbaka diskarna till Azure. I Azure-datacentret laddas dina data automatiskt upp från enheter till molnet med en snabb, privat nätverksöverföringslänk.

## <a name="use-cases"></a>Användningsfall

Använd Data Box Disk för att överföra flera TB med data i scenarier med ingen eller begränsad nätverksanslutning. Dataflytten kan vara enstaka, periodisk eller en inledande massdataöverföring följt av periodiska överföringar.

- **Engångsmigrering** – när stora mängder lokala data flyttas till Azure. Till exempel flyttning av data från offlineband till arkiveringsdata i lågfrekvent Azure-lagring.
- **Inkrementell överföring** – när en inledande massöverföring utförs med hjälp av Data Box-Disk (startvärde) följt av inkrementella överföringar över nätverket. Till exempel används Commvault och Data Box Disk för att flytta säkerhetskopior till Azure. Den här migreringen följs av kopiering av inkrementella data med hjälp av nätverket till Azure Storage.
- **Periodiska uppladdningar** – när stora mängder data genereras med jämna mellanrum och behöver flyttas till Azure. Till exempel i energiutforskning, där videoinnehåll genereras på oljeplattformar och vindkraftsparker.

### <a name="ingestion-of-data-from-data-box"></a>Datainmatning från Data Box-enhet

Azure-leverantörer och icke-Azure-leverantörer kan mata in data från Azure Data Box. De Azure-tjänster som tillhandahåller datainmatning från Azure Data Box omfattar:

- **SharePoint Online** – använd Azure Data Box och SharePoint Migration Tool (SPMT) för att migrera ditt filresursinnehåll till SharePoint Online. Med Data Box-enhet tar du bort beroendet av WAN-länken för att överföra data. Mer information finns i Använda [Azure Data Box Heavy för att migrera ditt filresursinnehåll till SharePoint Online.](data-box-heavy-migrate-spo.md)

- **Azure File Sync** – replikerar filer från din Data Box-enhet till en Azure-filresurs, så att du kan centralisera dina filtjänster i Azure samtidigt som du behåller lokal åtkomst till dina data. Mer information finns i [Distribuera Azure File Sync](../storage/file-sync/file-sync-deployment-guide.md).

- **HDFS-lagring** – migrera data från ett lokalt Hadoop Distributed File System(HDFS)-lager för hadoop-klustret till Azure Storage med Data Box-enhet. Mer information finns i [Migrera från det Azure Storage HDFS-arkivet Azure Data Box](../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md).

- **Azure Backup** – gör att du kan flytta stora säkerhetskopior av viktiga företagsdata via offlinemekanismer till ett Azure Recovery Services-valv. Mer information finns i [Azure Backup översikt.](../backup/backup-overview.md)

Du kan använda dina Data Box-enhet med många icke-Azure-tjänstleverantörer. Till exempel:

- **[Commvault](http://documentation.commvault.com/commvault/v11/article?p=97276.htm)** – gör att du kan migrera stora mängder data till Microsoft Azure med hjälp av Azure Data Box.
- **[Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/osr_adding_data_box.html?ver=100)** – gör att du kan säkerhetskopiera och replikera stora mängder data från Hyper-V-datorn till din Data Box-enhet.

En lista över andra icke-Azure-tjänstleverantörer som integreras med Data Box-enhet finns i [Azure Data Box Partner](https://cloudchampions.blob.core.windows.net/db-partners/PartnersTable.pdf).

## <a name="the-workflow"></a>Arbetsflödet

Ett typiskt flöde omfattar följande steg:

1. **Order** – Skapa en beställning i Azure Portal, ange leveransinformation och måladressens Azure Storage för dina data. Om diskar är tillgängliga krypterar, förbereder och levererar Azure diskarna med ett leveransspårnings-ID.

2. **Ta emot** – när diskarna har levererats packar du upp och ansluter diskarna till den dator som har data som ska kopieras. Lås upp diskarna.

3. **Kopiera data** – dra och släpp för att kopiera data på diskarna.

4. **Returnera** – förbered och skicka tillbaka diskarna till Azure-datacentret.

5. **Ladda upp** – data kopieras automatiskt från diskarna till Azure. Diskarna raderas på ett säkert sätt enligt riktlinjerna från National Institute of Standards and Technology (NIST).

Under den här processen meddelas du via e-post om alla statusändringar. Mer information om det detaljerade flödet finns på sidan om att [distribuera Data Box Disks i Azure-portalen](data-box-disk-quickstart-portal.md).

## <a name="benefits"></a>Fördelar

Data Box Disk har utformats för att flytta stora mängder data till Azure utan att påverka nätverket. Lösningen har följande fördelar:

- **Hastighet** – Data Box Disk använder en USB 3.0-anslutning för att flytta upp till 35 TB data till Azure på mindre än en vecka.

- **Lätt att använda** – Data Box är en lättanvänd lösning.

  - Diskarna använder USB-anslutning knappt kräver någon installationstid.
  - Diskarna har en liten formfaktor som gör dem enklare att hantera.
  - Diskarna kräver ingen extern ström.
  - Diskarna kan användas med en datacenterserver, stationär dator eller bärbar dator.
  - Lösningen tillhandahåller spårning från Azure Portal.

- **Skydd** – Data Box Disk har inbyggt säkerhetsskydd för diskarna, data och tjänsten.
  - Diskarna är manipulationsresistenta och stöder säker uppdateringsfunktion.
  - Data på diskarna skyddas alltid med AES 128-bitars kryptering.
  - Diskarna kan bara låsas upp med en nyckel som tillhandahålls i Azure-portalen.
  - Tjänsten skyddas av säkerhetsfunktionerna i Azure.
  - När dina data har laddats upp till Azure rensas diskarna i enlighet med standarderna NIST 800-88r1.  

Mer information finns i [Säkerhet och dataskydd i Azure Data Box Disk](data-box-disk-security.md).

## <a name="features-and-specifications"></a>Funktioner och specifikationer

| Specifikationer                                          | Description              |
|---------------------------------------------------------|--------------------------|
| Vikt                                                  | < 1 kg per låda. Upp till 5 diskar i lådan                |
| Dimensioner                                              | Disk – 2,5-tums SSD |
| Kablar                                                  | 1 USB 3.1-kabel per disk|
| Lagringskapacitet per beställning                              | 40 TB (ca 35 TB användbart)|
| Disklagringskapacitet                                   | 8 TB (ca 7 TB användbart)|
| Datagränssnitt                                          | USB   |
| Säkerhet                                                | Förkrypterat med BitLocker och säker uppdatering <br> Nyckelskyddade diskar <br> Data hålls alltid krypterade  |
| Dataöverföringshastighet                                      | upp till 430 MB/s beroende på filstorlek      |
|Hantering                                               | Azure Portal |

## <a name="region-availability"></a>Regional tillgänglighet

Information om tillgänglighet för regioner finns i [Tillgängliga Azure-produkter per region.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) Data Box Disk kan också distribueras i Azure Government Cloud. Mer information finns i [Vad är Azure Government?](../azure-government/documentation-government-welcome.md).

## <a name="pricing"></a>Priser

Mer information om priser finns på [sidan med priser](https://azure.microsoft.com/pricing/details/databox/disk/).

## <a name="next-steps"></a>Nästa steg

- Gå igenom [kraven för Data Box Disk](data-box-disk-system-requirements.md).
- Förstå [Data Box Disk-begränsningarna](data-box-disk-limits.md).
- Distribuera snabbt [Azure Data Box Disk](data-box-disk-quickstart-portal.md) på Azure-portalen.