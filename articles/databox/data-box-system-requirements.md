---
title: Microsoft Azure Data Box-enhet system krav | Microsoft Docs
description: Lär dig mer om viktiga system krav för din Azure Data Box och för klienter som ansluter till Data Box-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 03/25/2021
ms.author: alkohli
ms.openlocfilehash: 7f999dbf7a4e0262e36181a98560931d32d3296b
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612508"
---
# <a name="azure-data-box-system-requirements"></a>Azure Data Box system krav

I den här artikeln beskrivs viktiga system krav för din Microsoft Azure Data Box-enhet och för klienter som ansluter till Data Box-enhet. Vi rekommenderar att du läser igenom informationen noggrant innan du distribuerar din Data Box-enhet och sedan refererar till den när du behöver under distributionen och åtgärden.

System kraven är:

* **Program varu krav:** För värdar som ansluter till Data Box-enhet, beskriver de operativ system som stöds, fil överförings protokoll, lagrings konton, lagrings typer och webbläsare för det lokala webb gränssnittet.
* **Nätverks krav:** I Data Box-enhet beskrivs kraven för nätverks anslutningar och portar för att Data Box-enhet ska fungera bäst.


## <a name="software-requirements"></a>Programvarukrav

Program varu kraven inkluderar stödda operativ system, fil överförings protokoll, lagrings konton, lagrings typer och webbläsare för det lokala webb gränssnittet.

### <a name="supported-operating-systems-for-clients"></a>Operativsystem som stöds för klienter

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-file-transfer-protocols-for-clients"></a>Fil överförings protokoll som stöds för klienter

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

> [!IMPORTANT] 
> Anslutning till Data Box-enhet resurser stöds inte via REST för export order.

### <a name="supported-storage-accounts"></a>Lagringskonton som stöds

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Lagringstyper som stöds

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Webbläsare som stöds

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Nätverkskrav

Datacentret måste ha höghastighetsnätverk. Vi rekommenderar starkt att du har minst 1 10-GbE-anslutning. Om en 10-GbE-anslutning inte är tillgänglig kan du använda en 1-GbE-datalänk för att kopiera data, men kopierings hastigheten påverkas.

### <a name="port-requirements"></a>Portkrav

I följande tabell visas de portar som måste öppnas i brand väggen för att tillåta SMB-eller NFS-trafik. I den här tabellen *avser* (*inkommande*) den riktning från vilken inkommande klient begär åtkomst till din enhet. *Ut* (eller *utgående*) avser i vilken riktning din data Box-enhet enhet skickar data externt, utöver distributionen. Data kan till exempel vara utgående till Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Nästa steg

* [Distribuera Azure Data Box](data-box-deploy-ordered.md)
