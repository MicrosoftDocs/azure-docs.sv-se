---
title: Systemkrav för Microsoft Azure Data Box Disk | Microsoft Docs
description: Lär dig mer om programvaran och nätverkskraven för din Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 02/22/2021
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 750ed8f65db04199ea284e69693bced65a1dc8d9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703141"
---
::: zone target="docs"

# <a name="azure-data-box-disk-system-requirements"></a>Systemkrav för Azure Data Box Disk

I den här artikeln beskrivs viktiga systemkrav för din Microsoft Azure Data Box Disk-lösning och för klienter som ansluter till Data Box Disk. Vi rekommenderar att du läser informationen noggrant innan du distribuerar din Data Box Disk och går tillbaka till den vid behov under distributionen och efterföljande åtgärder.

Systemkraven anger vilka plattformar som stöds för klienter som ansluter till diskar, vilka lagringskonton som stöds samt lagringstyper.

::: zone-end

::: zone target="chromeless"

## <a name="review-prerequisites"></a>Gå igenom förhandskraven

1. Du måste ha beställt din Data Box Disk med hjälp [självstudien: Beställa en Azure Data Box Disk](data-box-disk-deploy-ordered.md). Du har fått diskarna och en anslutningskabel per disk.
2. Du har en tillgänglig klientdator som du kan kopiera data från. Klientdatorn måste:

    - Köra ett operativsystem som stöds.
    - Ha annan nödvändig programvara installerad.

::: zone-end

## <a name="supported-operating-systems-for-clients"></a>Operativsystem som stöds för klienter

Här är en lista över de operativsystem som stöds för diskupplåsningen och datakopieringen via klienterna som är anslutna till Data Box Disk.

| **Operativsystem** | **Testade versioner** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows (64-bitars) |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04, 16.04, 18.04 <br> 8.11, 9 <br> 7.0 <br> 6.5, 6.9, 7.0, 7.5 |  

## <a name="other-required-software-for-windows-clients"></a>Annan nödvändig programvara för Windows-klienter

För Windows-klienten bör även följande vara installerat.

| **Programvara**| **Version** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET Framework |4.5.1 |
| Windows Management Framework |5.1|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Annan nödvändig programvara för Linux-klienter

För Linux-klienten installerar Data Box Disk-verktygen följande nödvändiga programvara:

- dislocker
- OpenSSL

## <a name="supported-connection"></a>Anslutning som stöds

Klientdatorn som innehåller data måste ha en USB 3.0-port eller senare. Diskarna ansluter till klienten med hjälp av den medföljande kabeln.

## <a name="supported-storage-accounts"></a>Lagringskonton som stöds

Här är en lista över de lagringstyper som stöds för Data Box Disk.

| **Lagringskonto** | **Anteckningar** |
| --- | --- |
| Klassisk | Standard |
| Generell användning  |Standard; både v1 och v2 stöds. Både frekventa och lågfrekventa nivåer stöds. |
| Bloblagringskonto | |

> [!IMPORTANT]
> NFS (Network File System) 3,0 protokoll stöd i Azure Blob Storage stöds inte med Data Box Disk.

## <a name="supported-storage-types-for-upload"></a>Lagringstyper som stöds för uppladdning

Här är en lista över de lagringstyper som stöds för uppladdning till Azure med hjälp av Data Box Disk.

| **Filformat** | **Anteckningar** |
| --- | --- |
| Azure-blockblob | |
| Azure-sidblob  | |
| Azure Files  | |
| Managed Disks | |

::: zone target="docs"

## <a name="next-step"></a>Nästa steg

* [Distribuera Azure Data Box Disk](data-box-disk-deploy-ordered.md)

::: zone-end

