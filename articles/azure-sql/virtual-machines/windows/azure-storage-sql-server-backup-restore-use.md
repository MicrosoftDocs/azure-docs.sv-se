---
title: Använda Azure Storage för SQL Server säkerhets kopiering och återställning | Microsoft Docs
description: Lär dig hur du säkerhetskopierar SQL Server till Azure Storage. Förklarar fördelarna med att säkerhetskopiera SQL-databaser till Azure Storage.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mathoma
ms.openlocfilehash: 35fff49a53f5a0a9532fd0dff841356c5deaf3ea
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724790"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Använd Azure Storage för säkerhets kopiering och återställning av SQL Server
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Från och med SQL Server 2012 SP1-CU2 kan du nu skriva säkerhets kopierings SQL Server databaser direkt till Azure Blob Storage. Använd den här funktionen för att säkerhetskopiera till och återställa från Azure Blob Storage. Att säkerhetskopiera till molnet erbjuder fördelarna med tillgänglighet, obegränsad geo-replikerad lagring utanför platsen och enkel migrering av data till och från molnet. Du kan utfärda `BACKUP` eller `RESTORE` uttryck med hjälp av Transact-SQL eller SMO.

## <a name="overview"></a>Översikt
SQL Server 2016 introducerar nya funktioner; Du kan använda [säkerhets kopiering med fil-och ögonblicks bilder](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) för att utföra nästan momentan säkerhets kopieringar och otroligt snabba återställningar.

Det här avsnittet förklarar varför du kan välja att använda Azure Storage för SQL Server säkerhets kopieringar och beskriver de komponenter som ingår. Du kan använda de resurser som finns i slutet av artikeln för att få åtkomst till instruktioner och ytterligare information för att börja använda tjänsten med dina SQL Server säkerhets kopior.

## <a name="benefits-of-using-azure-blob-storage-for-sql-server-backups"></a>Fördelar med att använda Azure Blob Storage för SQL Server säkerhets kopieringar
Det finns flera utmaningar som du möter när du säkerhetskopierar SQL Server. Dessa utmaningar omfattar lagrings hantering, risk för lagrings problem, åtkomst till lagring utanför platsen och maskin varu konfiguration. Många av dessa utmaningar åtgärdas med hjälp av Azure Blob Storage för SQL Server säkerhets kopieringar. Tänk på följande fördelar:

* **Enkel användning**: att lagra dina säkerhets kopior i Azure-blobbar kan vara ett bekvämt, flexibelt och enkelt sätt att få åtkomst till alternativ för andra platser. Det kan vara lika enkelt att skapa lagrings platser för dina SQL Server säkerhets kopior som att ändra befintliga skript/jobb för att använda syntaxen för **säkerhets kopiering till URL** . Lagring på annan plats bör normalt vara tillräckligt långt från produktions databasens plats för att förhindra en enstaka katastrof som kan påverka både platser utanför platsen och produktions databasen. Genom att välja att [geo-replikera dina Azure-blobbar](../../../storage/common/storage-redundancy.md)har du ett extra skydds lager i händelse av en katastrof som kan påverka hela regionen.
* **Säkerhets kopierings Arkiv**: Azure Blob Storage erbjuder ett bättre alternativ till det band alternativ som ofta används för att arkivera säkerhets kopior. Band lagring kan kräva fysisk transport till en lokal plats och åtgärder för att skydda mediet. Genom att lagra säkerhets kopior i Azure Blob Storage får du ett direkt och lättillgängligt alternativ för att arkivera.
* **Hanterad maskin vara**: det finns ingen kapacitet för maskin varu hantering med Azure-tjänster. Azure-tjänster hanterar maskin varan och tillhandahåller geo-replikering för redundans och skydd mot maskin varu problem.
* **Obegränsad lagring**: genom att aktivera en direkt säkerhets kopiering till Azure-blobbar har du åtkomst till praktiskt taget obegränsad lagring. Ett annat sätt är att säkerhetskopiera till en virtuell Azure-dator disk baserat på datorns storlek. Det finns en gräns för hur många diskar du kan ansluta till en virtuell Azure-dator för säkerhets kopieringar. Den här gränsen är 16 diskar för en extra stor instans och färre för mindre instanser.
* **Tillgänglighet för säkerhets kopiering**: säkerhets kopior som lagras i Azure-blobbar är tillgängliga var som helst och kan användas för att återställas till en SQL Server instans, utan att databasen behöver kopplas/kopplas från eller hämtas och bifogas till den virtuella hård disken.
* **Kostnad**: betala endast för den tjänst som används. Kan vara kostnads effektivt som ett arkiv med säkerhets kopiering utanför platsen och. Se [pris Kalkylatorn för Azure](https://go.microsoft.com/fwlink/?LinkId=277060 "Priskalkylator")och [pris artikeln för Azure](https://go.microsoft.com/fwlink/?LinkId=277059 "Pris artikel") för mer information.
* **Ögonblicks bilder av lagring**: när databasfiler lagras i en Azure-blob och du använder SQL Server 2016 kan du använda [säkerhets kopiering för fil-och ögonblicks bilder](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) för att utföra nästan omedelbara säkerhets kopieringar och otroligt snabba återställningar.

Mer information finns i [SQL Server säkerhets kopiering och återställning med Azure Blob Storage](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service).

I följande två avsnitt introduceras Azure Blob Storage, inklusive nödvändiga SQL Server-komponenter. Det är viktigt att förstå komponenterna och deras interaktion för att kunna använda säkerhets kopiering och återställning från Azure Blob Storage.

## <a name="azure-blob-storage-components"></a>Azure Blob Storage-komponenter
Följande Azure-komponenter används när du säkerhetskopierar till Azure Blob Storage.

| Komponent | Beskrivning |
| --- | --- |
| **Lagringskonto** |Lagrings kontot är start punkten för alla lagrings tjänster. För att få åtkomst till Azure Blob Storage måste du först skapa ett Azure Storage-konto. Mer information om Azure Blob Storage finns i [så här använder du Azure Blob Storage](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/). |
| **Container** |En behållare ger gruppering av en uppsättning blobbar och kan lagra ett obegränsat antal blobbar. Om du vill skriva en SQL Server säkerhets kopia till Azure Blob Storage måste du ha minst en rot behållare skapad. |
| **Blob** |En fil av valfri typ och storlek. Blobbar är adresser bara med följande URL-format: `https://<storageaccount>.blob.core.windows.net/<container>/<blob>` . Mer information om sid-blobar finns i [förstå block-och sid-blobar](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) |

## <a name="sql-server-components"></a>SQL Server-komponenter
Följande SQL Servers komponenter används när du säkerhetskopierar till Azure Blob Storage.

| Komponent | Beskrivning |
| --- | --- |
| **URL** |En URL anger en Uniform Resource Identifier (URI) till en unik säkerhets kopierings fil. URL: en innehåller platsen och namnet på den SQL Server säkerhets kopierings filen. URL: en måste peka på en faktisk BLOB, inte bara en behållare. Om blobben inte finns skapas den av Azure. Om en befintlig BLOB anges, Miss lyckas säkerhets kopierings kommandot, om inte `WITH FORMAT` alternativet anges. Följande är ett exempel på den URL som du anger i säkerhets kopierings kommandot: `https://<storageaccount>.blob.core.windows.net/<container>/<FILENAME.bak>` .<br><br> HTTPS rekommenderas, men krävs inte. |
| **Autentiseringsuppgift** |Den information som krävs för att ansluta och autentisera till Azure Blob Storage lagras som en autentiseringsuppgift. För att SQL Server ska kunna skriva säkerhets kopior till en Azure-Blob eller återställa från den, måste du skapa en SQL Server autentiseringsuppgift. Mer information finns i [SQL Server Credential](/sql/t-sql/statements/create-credential-transact-sql). |

> [!NOTE]
> SQL Server 2016 har uppdaterats för att stödja block-blobar. Mer information finns i [Självstudier: använda Microsoft Azure Blob Storage med SQL Server 2016-databaser](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016) .
> 

## <a name="next-steps"></a>Nästa steg

1. Skapa ett Azure-konto om du inte redan har ett. Om du utvärderar Azure bör du tänka på den [kostnads fria utvärderings versionen](https://azure.microsoft.com/free/).
2. Gå sedan igenom någon av följande självstudier som vägleder dig genom att skapa ett lagrings konto och utföra en återställning.
   
   * **SQL Server 2014**: [självstudier: SQL Server 2014 säkerhets kopiering och återställning till Microsoft Azure Blob-lagring](/previous-versions/sql/2014/relational-databases/backup-restore/sql-server-backup-to-url).
   * **SQL Server 2016**: [Självstudier: använda Microsoft Azure Blob storage med SQL Server 2016-databaser](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016)
3. Granska ytterligare dokumentation som börjar med [SQL Server säkerhets kopiering och återställning med Microsoft Azure Blob Storage](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service).

Om du har problem kan du läsa avsnittet [SQL Server säkerhets kopiering till URL metod tips och fel sökning](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting).

Andra SQL Server alternativ för säkerhets kopiering och återställning finns i [säkerhets kopiering och återställning för SQL Server på Azure-Virtual Machines](backup-restore.md).