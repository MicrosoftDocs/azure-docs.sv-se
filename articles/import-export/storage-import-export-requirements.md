---
title: Krav för tjänsten Azure import/export | Microsoft Docs
description: Förstå program-och maskin varu kraven för Azure import/export-tjänsten.
author: alkohli
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 0bfc09a372584a25c23060cef33d1f698e6d5ff3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101712614"
---
# <a name="azure-importexport-system-requirements"></a>Systemkrav för Azure Import/Export

I den här artikeln beskrivs viktiga krav för Azure import/export-tjänsten. Vi rekommenderar att du läser informationen noggrant innan du använder import/export-tjänsten och sedan refererar tillbaka till den vid behov under åtgärden.

## <a name="supported-operating-systems"></a>Operativsystem som stöds

För att förbereda hård diskarna med WAImportExport-verktyget stöds följande **64-bitars operativ system som stöder BitLocker-diskkryptering** .


|Plattform |Version |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8,1 Pro, Windows 8,1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Annan nödvändig program vara för Windows-klient

|Plattform |Version |
|---------|---------|
|.NET Framework    | 4.5.1       |
| BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>Lagringskonton som stöds

Tjänsten Azure import/export stöder följande typer av lagrings konton:

- Standard Generell användning v2-lagrings konton (rekommenderas för de flesta scenarier)
- Blob Storage-konton
- Generell användning v1-lagrings konton (både klassiska eller Azure Resource Manager distributioner)

> [!IMPORTANT]
> NFS (Network File System) 3,0 protokoll stöd i Azure Blob Storage stöds inte med Azure import/export.

Mer information om lagrings konton finns i [Översikt över Azure Storage-konton](../storage/common/storage-account-overview.md).

Varje jobb kan användas för att överföra data till eller från ett enda lagrings konto. Med andra ord kan ett enda import/export-jobb inte omfatta över flera lagrings konton. Information om hur du skapar ett nytt lagrings konto finns i [så här skapar du ett lagrings konto](../storage/common/storage-account-create.md).

> [!IMPORTANT]
> För lagrings konton där funktionen [Virtual Network tjänst slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md) har Aktiver ATS använder du inställningen **Tillåt betrodda Microsoft-tjänster...** om du vill [Aktivera import](../storage/common/storage-network-security.md) /export-tjänsten för att importera/exportera data till/från Azure.

## <a name="supported-storage-types"></a>Lagringstyper som stöds

Följande lista över lagrings typer stöds med Azure import/export-tjänsten.


|Jobb  |Lagrings tjänst |Stöds  |Stöds inte  |
|---------|---------|---------|---------|
|Importera     |  Azure Blob Storage <br><br> Azure File Storage       | Blockera blobbar och sid-blobar som stöds <br><br> Filer som stöds          |
|Exportera     |   Azure Blob Storage       | Block-blobar, Page blobbar och bifogade blobbar stöds         | Azure Files stöds inte


## <a name="supported-hardware"></a>Maskin vara som stöds

För tjänsten Azure import/export behöver du diskar som stöds för att kopiera data.

### <a name="supported-disks"></a>Diskar som stöds

Följande lista över diskar stöds för användning med import/export-tjänsten.


|Disktyp  |Storlek  |Stöds |
|---------|---------|---------|
|SSD    |   2,5 "      |SATA III          |
|HDD     |  2,5 "<br>3,5 "       |SATA II, SATA III         |

Följande disk typer stöds inte:

- USBs.
- Extern hård disk med inbyggd USB-adapter.
- Diskar som är i höljet för en extern hård disk.

Ett enda import/export-jobb kan ha:

- Högst 10 hård disk-/SSD.
- En blandning av hård diskar/SSD i valfri storlek.

Ett stort antal enheter kan spridas över flera jobb och det finns ingen gräns för antalet jobb som kan skapas. För import jobb bearbetas bara den första data volymen på enheten. Data volymen måste vara formaterad med NTFS.

När du förbereder hård diskar och kopierar data med hjälp av WAImportExport-verktyget kan du använda externa USB-anpassningar. De flesta USB 3,0-och senare-anpassningarna i rack bör fungera.

## <a name="next-steps"></a>Nästa steg

* [Överföra data med kommando rads verktyget AzCopy](../storage/common/storage-use-azcopy-v10.md)