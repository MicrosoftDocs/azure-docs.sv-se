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
Customer intent: As an IT admin, I need to understand what Data Box Disk is and how it works so I can use it to import on-premises data into Azure.
ms.openlocfilehash: ea9353ed8c1938fa9b33585a0650b4507c671451
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92125038"
---
# <a name="what-is-azure-data-box-disk"></a>Vad är Azure Data Box Disk?

Med Microsoft Azure Data Box Disk-lösningen kan du skicka terabyte med lokala data till Azure på ett snabbt, kostnadseffektivt och tillförlitligt sätt. Den säkra dataöverföringen görs snabbare eftersom 1 till 5 SSD-diskar (solid state-hårddisk) levereras till dig. De här 8 TB krypterade diskarna skickas till data centret via en regional bärvåg.

Du kan snabbt konfigurera, ansluta och låsa upp diskarna via tjänsten Data Box-enhet i Azure Portal. Kopiera dina data till diskar och skicka tillbaka diskarna till Azure. I Azure-datacentret laddas dina data automatiskt upp från enheter till molnet med en snabb, privat nätverksöverföringslänk.

## <a name="use-cases"></a>Användningsfall

Använd Data Box Disk för att överföra flera TB med data i scenarier med ingen eller begränsad nätverksanslutning. Dataflytten kan vara enstaka, periodisk eller en inledande massdataöverföring följt av periodiska överföringar.

- **Engångsmigrering** – när stora mängder lokala data flyttas till Azure. Till exempel flyttning av data från offlineband till arkiveringsdata i lågfrekvent Azure-lagring.
- **Inkrementell överföring** – när en inledande massöverföring utförs med hjälp av Data Box-Disk (startvärde) följt av inkrementella överföringar över nätverket. Till exempel används Commvault och Data Box Disk för att flytta säkerhetskopior till Azure. Migreringen följs av genom att kopiera stegvisa data med hjälp av nätverk till Azure Storage.
- **Periodiska uppladdningar** – när stora mängder data genereras med jämna mellanrum och behöver flyttas till Azure. Till exempel i energiutforskning, där videoinnehåll genereras på oljeplattformar och vindkraftsparker.

### <a name="ingestion-of-data-from-data-box"></a>Inmatning av data från Data Box-enhet

Azure-leverantörer och icke-Azure-leverantörer kan mata in data från Azure Data Box. De Azure-tjänster som tillhandahåller data inmatning från Azure Data Box omfattar:

- **SharePoint Online** – Använd Azure Data Box och SPMT (SharePoint Migration Tool) för att migrera ditt fil resurs innehåll till SharePoint Online. Med hjälp av Data Box-enhet tar du bort beroendet på WAN-länken för att överföra data. Mer information finns i [använda Azure Data Box Heavy för att migrera ditt fil resurs innehåll till SharePoint Online](data-box-heavy-migrate-spo.md).

- **Azure File Sync** – replikerar filer från data Box-enhet till en Azure-filresurs, så att du kan centralisera dina fil tjänster i Azure samtidigt som du behåller lokal åtkomst till dina data. Mer information finns i [distribuera Azure File Sync](../storage/files/storage-sync-files-deployment-guide.md).

- **HDFS lagrar** -migrera data från ett lokalt HADOOP DISTRIBUTED File System (HDFS) Store för ditt Hadoop-kluster i Azure Storage med data Box-enhet. Mer information finns i [Migrera från lokal HDFS-butiken till Azure Storage med Azure Data Box](../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md).

- **Azure Backup** – låter dig flytta stora säkerhets kopieringar av kritiska företags data via offline-mekanismer till ett Azure Recovery Services-valv. Mer information finns i [Azure Backup översikt](../backup/backup-overview.md).

Du kan använda dina Data Box-enhet data med många icke-Azure-tjänstleverantörer. Till exempel:

- **[CommVault](http://documentation.commvault.com/commvault/v11/article?p=97276.htm)** – gör att du kan migrera stora mängder data till Microsoft Azure med hjälp av Azure Data box.
- **[Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/osr_adding_data_box.html?ver=100)** – gör att du kan säkerhetskopiera och replikera stora mängder data från Hyper-V-datorn till din data Box-enhet.

En lista över andra icke-Azure-tjänstleverantörer som integreras med Data Box-enhet finns [Azure Data Box partners](https://cloudchampions.blob.core.windows.net/db-partners/PartnersTable.pdf).

## <a name="the-workflow"></a>Arbetsflödet

Ett typiskt flöde omfattar följande steg:

1. **Order** – skapa en order i Azure Portal, ange leverans information och mål Azure Storage konto för dina data. Om diskar är tillgängliga krypterar, förbereder och levererar Azure diskarna med ett leveransspårnings-ID.

2. **Ta emot** – när diskarna har levererats packar du upp och ansluter diskarna till den dator som har data som ska kopieras. Lås upp diskarna.

3. **Kopiera data** – dra och släpp för att kopiera data på diskarna.

4. **Returnera** – förbered och skicka tillbaka diskarna till Azure-datacentret.

5. **Ladda upp** – data kopieras automatiskt från diskarna till Azure. Diskarna raderas på ett säkert sätt enligt riktlinjerna från National Institute of Standards and Technology (NIST).

I den här processen får du ett meddelande via e-post om alla status ändringar. Mer information om det detaljerade flödet finns på sidan om att [distribuera Data Box Disks i Azure-portalen](data-box-disk-quickstart-portal.md).

## <a name="benefits"></a>Fördelar

Data Box Disk har utformats för att flytta stora mängder data till Azure utan att påverka nätverket. Lösningen har följande fördelar:

- **Hastighet** – Data Box Disk använder en USB 3.0-anslutning för att flytta upp till 35 TB data till Azure på mindre än en vecka.

- **Lätt att använda** – Data Box är en lättanvänd lösning.

  - Diskarna använder USB-anslutning knappt kräver någon installationstid.
  - Diskarna har en liten formfaktor som gör dem enklare att hantera.
  - Diskarna kräver ingen extern ström.
  - Diskarna kan användas med en datacenterserver, stationär dator eller bärbar dator.
  - Lösningen ger slut punkt till slut punkts spårning med hjälp av Azure Portal.

- **Skydd** – Data Box Disk har inbyggt säkerhetsskydd för diskarna, data och tjänsten.
  - Diskarna är manipulationsresistenta och stöder säker uppdateringsfunktion.
  - Data på diskarna skyddas alltid med AES 128-bitars kryptering.
  - Diskarna kan bara låsas upp med en nyckel som tillhandahålls i Azure-portalen.
  - Tjänsten skyddas av säkerhetsfunktionerna i Azure.
  - När dina data har laddats upp till Azure rensas diskarna i enlighet med standarderna NIST 800-88r1.  

Mer information finns i [Säkerhet och dataskydd i Azure Data Box Disk](data-box-disk-security.md).

## <a name="features-and-specifications"></a>Funktioner och specifikationer

| Specifikationer                                          | Beskrivning              |
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

Information om regions tillgänglighet finns i [Azure-produkter tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Data Box Disk kan också distribueras i Azure Government molnet. Mer information finns i [Vad är Azure Government?](../azure-government/documentation-government-welcome.md).

## <a name="pricing"></a>Prissättning

Mer information om priser finns på [sidan med priser](https://azure.microsoft.com/pricing/details/databox/disk/).

## <a name="next-steps"></a>Nästa steg

- Gå igenom [kraven för Data Box Disk](data-box-disk-system-requirements.md).
- Förstå [Data Box Disk-begränsningarna](data-box-disk-limits.md).
- Distribuera snabbt [Azure Data Box Disk](data-box-disk-quickstart-portal.md) på Azure-portalen.