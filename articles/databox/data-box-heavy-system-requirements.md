---
title: Microsoft Azure Data Box Heavy system krav | Microsoft Docs
description: Läs om program-och nätverks kraven för din Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 03/25/2021
ms.author: alkohli
ms.openlocfilehash: d4fed8a79b9d74f44a511bd019531c7843965d0e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105611942"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Azure Data Box Heavy system krav

I den här artikeln beskrivs viktiga system krav för din Azure Data Box Heavy-enhet och för klienter som ansluter till enheten. Vi rekommenderar att du läser informationen noggrant innan du distribuerar din Data Box Heavy och sedan går tillbaka till den vid behov under distributionen och efterföljande åtgärder.

System kraven är:

* **Program varu krav för värdar som ansluter till data Box Heavy** – beskriver de plattformar som stöds, webbläsare för det lokala webb gränssnittet, SMB-klienter och eventuella ytterligare krav för värdar som kan ansluta till data Box-enhet.
* **Nätverks krav för data Box Heavy** – ger information om nätverks kraven för den optimala driften av data Box Heavys enheten.

## <a name="software-requirements"></a>Programvarukrav

Program varu kraven innehåller information om de operativ system som stöds, vilka webbläsare som stöds för det lokala webb gränssnittet och SMB-klienter.

### <a name="supported-operating-systems-for-clients"></a>Operativsystem som stöds för klienter

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Fil system som stöds för Linux-klienter

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Lagringskonton som stöds

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Lagringstyper som stöds

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Webbläsare som stöds

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Nätverkskrav

Datacentret måste ha höghastighetsnätverk. För snabbaste kopierings hastigheter kan 2 40-GbE-anslutningar användas parallellt (en per nod). Om du inte har 40-GbE tillgängligt rekommenderar vi att du har minst 2 10-GbE-anslutningar (en per nod).

### <a name="port-requirements"></a>Portkrav

I följande tabell visas de portar som måste öppnas i brand väggen för att tillåta SMB-eller NFS-trafik. I den här tabellen avser *i* eller *inkommande* den riktning från vilken inkommande klient begär åtkomst till din enhet. *Out* eller *utgående* avser i vilken riktning din data Box Heavy enhet skickar data externt, utöver distributionen: till exempel utgående till Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>Nästa steg

* [Distribuera Azure Data Box](data-box-deploy-ordered.md)
